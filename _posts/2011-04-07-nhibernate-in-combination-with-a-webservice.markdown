---
layout: post
title: "NHibernate in combination with a webservice"
date: 2011-04-07 06:38:00 +0200
comments: true
published: true
categories: ["blog", "archives"]
tags: ["NHibernate"]
alias: ["/post/NHibernate-in-combination-with-a-webservice", "/post/nhibernate-in-combination-with-a-webservice"]
---

In this post i'ill try to explain how you can make NHibernate interact with a webservice to fill in a certain object.
##Current situation##

This is the current situation that we have.

![Current situation](http://blog.kennytordeur.be/images/2011-04-07-nhibernate-in-combination-with-a-webservice/situation.gif)

We have a webservice called "Marial State service". This service is responsible for all marial states available. This service has only 2 methods:

* GetAll()
  * Gives all the available marial states
* GetById(Int32 id)
  * Gives a specific marial state by it's id


The client is an application that has it's own database and uses NHibernate as ORM. The client is responsible for creating/updating/deleting persons in his database. So in the database we find a table called "Person" with contains all the data.

![database schema](http://blog.kennytordeur.be/images/2011-04-07-nhibernate-in-combination-with-a-webservice/database.gif)

As you can see, the person, has a marial state which comes from the MarialState service.
For those who noticed, yes i am using an Access as database. [Here](https://nhcontrib.svn.sourceforge.net/svnroot/nhcontrib/trunk/src/NHibernate.JetDriver/) you can find the needed DLL.

##The MarialState service##
I created 2 new projects in visual studio. One which contains the model off the service.

![webservice model](http://blog.kennytordeur.be/images/2011-04-07-nhibernate-in-combination-with-a-webservice/webservicemodel.gif)

```csharp
namespace NHibernateWebservices.WebServiceProxy.Model
{
    public class MarialState
    {
        public Int32 Id { get; set; }
        public String Description { get; set; }
    }
}
```

And another project for the service logic. As you can see this isn't a real service. I am mocking it.

![webservice model](http://blog.kennytordeur.be/images/2011-04-07-nhibernate-in-combination-with-a-webservice/webserviceproxy.gif)

```csharp
namespace NHibernateWebservices.WebServiceProxy
{
    public static class MarialState
    {
        public static List<Model.MarialState> GetAll()
        {
            return new List<Model.MarialState>
                                {
                                    new Model.MarialState { Id = 1, Description = "Single"}
                                    ,new Model.MarialState { Id = 2, Description = "Married"}
                                    ,new Model.MarialState { Id = 3, Description = "widower"}
                                };
        }
        public static Model.MarialState GetById(Int32 id)
        {
            return GetAll().SingleOrDefault(m => m.Id == id);
        }
    }
}
```

##The client##

At client side the we created a business object called "Person" which will map at the Person table in it's database.

```csharp
public class Person
{
    public virtual Int32 Id { get; set; }
    public virtual String FullName { get; set; }
    public virtual Int32 MarialStateId { get; set; }
}
```

This is the mapping file for the "Person" entity.

```xml
<?xmlversion="1.0" ?>
<hibernate-mappingxmlns="urn:nhibernate-mapping-2.2">
    <classname="NHibernateWebservices.Model.Person, NHibernateWebservices.Model"
         table="Person">
    <idname="Id"
        column="Id"
              unsaved-value="0">
      <generatorclass="increment" />
    </id>
   
    <propertycolumn="FullName"
              name="FullName"
              type="String" />

    <propertyname="MarialStateId"
             column="MarialStateId"
             type="int"/>
   
  </class>
</hibernate-mapping>
```


##Now How to get the data?##


###First try###


The most easy way to get the MarialState entity would be to write something like this.


```csharp
public class Person
{
    public virtual Int32 Id { get; set; }
    public virtual String FullName { get; set; }      

    private Int32 marialStateId;
    public virtual Int32 MarialStateId
    {
        get
        {
            return marialStateId;
        }
        set
        {
            marialStateId = value;
        }
    }

    public virtual WebServiceProxy.Model.MarialState MarialState
    {
        get
        {
            return WebServiceProxy.MarialState.GetById(marialStateId);
        }
        set
        {
            marialStateId = (null == value) ? (0) : (value.Id);
        }
    }
}
```

This solution will work, but I can see several things that I am not happy with. First of all it's a lot off extra code. In this case the "Person" entity is very simple, but what if it was complex and you needed to get other data from a service, you would be writing a lot of code looking like this.


Second, in this approach you need to create a reference between the WebServiceProxy and the business Model of the client. At this point the business Model of the client is also responsible to fill it's own data instead of only validating against business rules.


###Using NHibernate IUserType###

The ideal situation would that we ask NHibernate to give use a "Person" and that everything, including MarialState object, would be filled automatically. You can do this if you create a new NHibernate IUserType object. IUserType is an interface which allows you to plug in into the NHibernate mapping process and handle it your self.

```csharp
public class MarialStateUserType : IUserType
{
    public object Assemble(object cached, object owner)
    {
        return DeepCopy(cached);
    }

    public object DeepCopy(object value)
    {
        return value;
    }

    public object Disassemble(object value)
    {
        return DeepCopy(value);
    }

    public int GetHashCode(object x)
    {
        return x.GetHashCode();
    }

    public bool IsMutable
    {
        get { return true; }
    }

    public object NullSafeGet(System.Data.IDataReader rs, string[] names, object owner)
    {
        object obj = NHibernateUtil.Int32.NullSafeGet(rs, names[0]);
        WebServiceProxy.Model.MarialState marialState = null;

        if (null != obj)
        {
            marialState = WebServiceProxy.MarialState.GetById(Convert.ToInt32(obj));
        }

        return marialState;
    }

    public void NullSafeSet(System.Data.IDbCommand cmd, object value, int index)
    {
        WebServiceProxy.Model.MarialState marialState = value as WebServiceProxy.Model.MarialState;

        var parameter = (IDataParameter)cmd.Parameters[index];
        if (null == marialState)
            parameter.Value = DBNull.Value;
        else
            parameter.Value = marialState.Id;
    }

    public Type ReturnedType
    {
        get { return typeof(WebServiceProxy.Model.MarialState); }
    }

    public SqlType[] SqlTypes
    {
        get { return new SqlType[] { NHibernateUtil.Int32.SqlType }; }
    }

    public object Replace(object original, object target, object owner)
    {
        return original;
    }

    public new bool Equals(object x, object y)
    {
        if (object.ReferenceEquals(x, y)) return true;
        if (x == null || y == null) return false;
        return x.Equals(y);
    }
}

```

This it the custom IUsertype that is created for the MarialState entity. In this example, these are the most interesting parts of code:

* ReturnedType
* SqlTypes
* NullSafeGet
* NullSafeSet

####ReturnedType/SqlTypes properties####


The ReturnedType and SqlTypes properties tells NHibernate which type to expect. The ReturnedType property contains the type of object that this IUsertype has to return. In our case it is a MarialState object.

```csharp
public Type ReturnedType
{
    get { return typeof(WebServiceProxy.Model.MarialState); }
}
```

The SqlTypes property tells how the object is mapped in the database. Here the MarialState object is mapped to a numerical column.

```csharp
public SqlType[] SqlTypes
{
    get { return new SqlType[] { NHibernateUtil.Int32.SqlType }; }
}
```

####NullSafeGet/NullSafeSet methods####

The NullSafeSet method is called when we want to save/update our object. In our case, the parameter "value" will contain our MarialState entity. The parameter "cmd" will contain the command that is used to update/insert the "Person" entity in the database.


What we now have to do, is to insert the Id of the MarialState entity in the command at the index place. We know the index because it is passed to the NullSafeSet method. If the MarialState entity is empty, we insert the DBNull value.

```csharp
public void NullSafeSet(System.Data.IDbCommand cmd, object value, int index)
{
    WebServiceProxy.Model.MarialState marialState = value as WebServiceProxy.Model.MarialState;

    var parameter = (IDataParameter)cmd.Parameters[index];
    if (null == marialState)
        parameter.Value = DBNull.Value;
    else
        parameter.Value = marialState.Id;
}
```

The NullSafeSet method is called when we want to extract the data from the database. This method will return a MarialState entity. As you can see, this method has a IDataReader object as parameter. From this IDataReader we have to extract the MarialStateId column and use it's value to get the MarialState entity from the MarialState service. We also do a check to see if the value isn't null because it could be *NULL* in the database.

```csharp
public object NullSafeGet(System.Data.IDataReader rs, string[] names, object owner)
{
    object obj = NHibernateUtil.Int32.NullSafeGet(rs, names[0]);
    WebServiceProxy.Model.MarialState marialState = null;

    if (null != obj)
    {
        marialState = WebServiceProxy.MarialState.GetById(Convert.ToInt32(obj));
    }

    return marialState;
}
```

####Putting the pieces together####
Now that everything is in place, we can start to glue it together. First of all, we can change the "Person" entity at the client. The property MarialStateId can now be replaced by the MarialState property. The class looks a lot cleaner this way.

```csharp
public class Person
{
    public virtual Int32 Id { get; set; }
    public virtual String FullName { get; set; }

    public virtual WebServiceProxy.Model.MarialState MarialState { get; set; }
}
```

Next in line is to change the mapping file for the "Person" entity. In this file we are going to make use of the MarialStateUserType that we created.

```xml
<?xmlversion="1.0" ?>
<hibernate-mappingxmlns="urn:nhibernate-mapping-2.2">
    <classname="NHibernateWebservices.Model.Person, NHibernateWebservices.Model"
         table="Person">
    <idname="Id"
        column="Id"
              unsaved-value="0">
      <generatorclass="increment" />
    </id>
   
    <propertycolumn="FullName"
              name="FullName"
              type="String" />

    <propertyname="MarialState"
              column="MarialStateId"
              type="NHibernateWebservices.NHibernateUserType.MarialStateUserType, NHibernateWebservices"/>
   
  </class>
</hibernate-mapping>
```

If we run the client and get a "Person" from the database, the MarialState will be filled automatically be NHibernate.

##What about the performance?##
If you have some performance problems, you can always but lazy loading on the MarialState property. This way we will only go to the service if we use the MarialState property.

```xml
<propertyname="MarialState"
          column="MarialStateId"
          lazy="true"
          type="NHibernateWebservices.NHibernateUserType.MarialStateUserType, NHibernateWebservices"/>
```
