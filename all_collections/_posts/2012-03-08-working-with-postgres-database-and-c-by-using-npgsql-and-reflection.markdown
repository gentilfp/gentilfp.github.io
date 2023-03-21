---
title:  "Working with Postgres and C# using Npgsql"
date:   2012-03-08 20:00:00
layout: post
categories: [c#]
---

I’m working on a project that had a must-be-Postgres moment and before that I had worked with this database and C# once, in 2009. So, lots of thing changed since then.
Well, today I’m working with [Npgsql][npgsql]...
<!--more-->

“Npgsql is a .Net Data Provider for Postgresql. It allows any program developed for .Net framework to access database server. It is implemented in 100% C# code. Works with Postgresql 7.x and above.”

All right, there’s a great and complete [documentation][doc] at Npgsql’s website and I hardly recommend that you take a look at them. Cause I’m here just to make it quickly, a full tutorial that will approach you to the main 4 actions that you use while working with database

* Insert
* Delete
* Update
* Query


Before everything, make sure you have downloaded Npgsql’s dll (which you can find [here][dll]). Don’t forget to add it as a [Reference][ref].

I’ll try to write a generic database class, very simple one with the main methods using Reflection (which, in case you don’t know what’s that, take a look at MSDN, it’s just awesome!). This way, you will be able to use the same class in different projects, sometimes you will need to ajust only a couple of lines of code.

Here we go...

We will need a variable which will connect direct to the database and call us the methods we want, remember to configure it with your database server, port, user and password.

{% highlight c# %}
using Npgsql;

NpgsqlConnection conn = new NpgsqlConnection("Server=127.0.0.1;Port=5432;User Id=postgres;Password=admin;Database=XXXXXX;");        
{% endhighlight %}

Before we start calling our methods, it is important to between every operation follow this instruction:

* Open connection
* Do your stuff
* Close connection

This will prevent you to get in trouble. So, here it is our Open and Close connection.

{% highlight c# %}
public void OpenConn()
{
    try
    {               
        conn.Open();
    }
    catch (Exception exp)
    {                
        MessageBox.Show("Error :S");
    }
}

public void CloseConn()
{
    try
    {
        conn.Close();
    }
    catch (Exception)
    {
        MessageBox.Show("Error :S");
    }
}
{% endhighlight %}

Ok so, let’s get it started by Insert operations. All you are going to need as parameter is the table name.

{% highlight c# %}
public void InsertOnTable(Object objGen)
{
    try
    {
        this.OpenConn();

        // Get type and properties (vector)
        Type typeObj = objGen.GetType();
        PropertyInfo[] properties = typeObj.GetProperties();

        // Get table
        string[] type = typeObj.ToString().Split('.');
        string table = type[2].ToLower();

        // Start mounting string to insert
        string SQL = "INSERT INTO " + table + " VALUES (";

        // It goes from second until second to last
        for (int i = 1; i < properties.Length - 1; i++)
        {
            object propValue = properties[i].GetValue(objGen, null);
            string[] typeValue = propValue.GetType().ToString().Split('.');                    
            if (typeValue[1].Equals("String"))
            {
                SQL += "'" + propValue.ToString() + "',";
            }
            else if (typeValue[1].Equals("DateTime"))
            {
                SQL += "'" + Convert.ToDateTime(propValue.ToString()).ToShortDateString() + "',";
            }
            else
            {
                SQL += propValue.ToString() + ",";
            }                    
        }

        // get last attribute here
        object lastValue = properties[properties.Length - 1].GetValue(objGen, null);
        string[] lastType = lastValue.GetType().ToString().Split('.');
        if (lastType[1].Equals("String"))
        {
            SQL += "'" + lastValue.ToString() + "'";
        }
        else if (lastType[1].Equals("DateTime"))
        {
            SQL += "'" + Convert.ToDateTime(lastValue.ToString()).ToShortDateString() + "'";
        }
        else
        {
            SQL += lastValue.ToString();
        }

        // Ends string builder
        SQL += ");";

        // Execute command
        NpgsqlCommand command = new NpgsqlCommand(SQL, conn);
        Int32 rowsaffected = command.ExecuteNonQuery();

        this.CloseConn();
    }
    catch (Exception)
    {
        MessageBox.Show("Errr on insert!");
    }
}
{% endhighlight %}

The update method goes below.

{% highlight c# %}
public void UpdateOnTable(Object objGen, int idValue)
{
    try
    {
        this.OpenConn();                               

        // Get table
        string[] type = objGen.GetType().ToString().Split('.');
        string table = type[2].ToLower();

        // Start building
        string SQL =  "UPDATE " + table + " SET ";

        // Get types and properties
        Type type2 = objGen.GetType();
        PropertyInfo[] properties = type2.GetProperties();

        // Goes until second to last
        for (int i = 0; i < properties.Length - 1; i++)
        {
            object propValue = properties[i].GetValue(objGen, null);
            string[] nameAttribute = properties[i].ToString().Split(' ');
            string[] typeAttribute = propValue.GetType().ToString().Split('.');

            if (typeAttribute[1].Equals("String"))
            {
                SQL += nameAttribute[1] + " = '" + propValue.ToString() + "',";
            }
            else if (typeAttribute[1].Equals("DateTime"))
            {
                SQL += nameAttribute[1] + "= '" + Convert.ToDateTime(propValue.ToString()).ToShortDateString() + "',";
            }
            else
            {
                SQL += nameAttribute[1] + " = " + propValue.ToString() + ",";
            }
        }

        // Process last attribute
        object lastValue = properties[properties.Length - 1].GetValue(objGen, null);
        string[] lastType = lastValue.GetType().ToString().Split('.');
        string[] ultimoCampo = properties[properties.Length - 1].ToString().Split(' ');
        if (lastType[1].Equals("String"))
        {
            SQL += ultimoCampo[1] + " = '" + lastValue.ToString() + "'";
        }
        else if (lastType[1].Equals("DateTime"))
        {
            SQL += ultimoCampo[1] + "= '" + Convert.ToDateTime(lastValue.ToString()).ToShortDateString() + "'";
        }
        else
        {
            SQL += ultimoCampo[1] + " = " + lastValue.ToString();
        }

        // Ends string builder
        SQL += " WHERE id = " + idValue + ";";

        // Execute query
        NpgsqlCommand command = new NpgsqlCommand(SQL, conn);
        Int32 rowsaffected = command.ExecuteNonQuery();

        this.CloseConn();
    }
    catch (Exception)
    {
        MessageBox.Show("Errr on update!");
    }
}
{% endhighlight %}

Pay attention to this code, I will assume that my table has an integer id as primary key. (Remember that I told you about few adjustments? Here it is!)

The delete method follows the same path as the update part. An integer id as primary key.

{% highlight c# %}
public void DeleteOnTable(Object objGen, int idValue)
{
    try
    {
        this.OpenConn();

        string[] type = objGen.GetType().ToString().Split('.');
        string table = type[2];

        string SQL = "DELETE FROM " + table + " WHERE id = " + idValue + ";";

        NpgsqlCommand command = new NpgsqlCommand(SQL, conn);
        Int32 rowsaffected = command.ExecuteNonQuery();               

        this.CloseConn();
    }
    catch (Exception)
    {
        MessageBox.Show("Errr on update!");             
    }
}
{% endhighlight %}

To make it simpler and (bad looking), I have splitted the Query method in two, the first one will get all data from your table and the second allows you to use parameters in your SQL query string.

Querying everything...

{% highlight c# %}
public List<Object> QueryAllOnTable(string table)
{
    try
    {
        this.OpenConn();

        List<Object> lstSelect = new List<Object>();
        string SQL = "SELECT * FROM " + table + ";";

        NpgsqlCommand command = new NpgsqlCommand(SQL, conn);
        NpgsqlDataReader dr = command.ExecuteReader();

        while (dr.Read())
        {
            for (int i = 0; i < dr.FieldCount; i++)
            {
                lstSelect.Add(dr[i]);                        
            }                    
        }

        this.CloseConn();

        return lstSelect;                            
    }
    catch (Exception)
    {
        MessageBox.Show("Errr on query!");
        return null;
    }            
}
{% endhighlight %}

Querying only what you really want...

{% highlight c# %}
public List<Object> QueryOnTableWithParams(string table, string[] paramName, string[] paramValue)
{
    try
    {
        if (paramName.Count != paramValue.Count)
        {
            MessageBox.Show("Wrong number of params");
            return null;
        }

        this.OpenConn();

        List<Object> lstSelect = new List<Object>();
        string SQL = "SELECT * FROM " + table + " WHERE ";

        // get params
        for (int i = 0; i < paramName.Count - 1; i++)
        {
            SQL += paramName[i] + " = " + paramValue[i] + " AND ";
        }

        // get last param
        SQL += paramName[paramName.Count - 1] + " = " + paramValue[paramValue.Count - 1] + ";";

        NpgsqlCommand command = new NpgsqlCommand(SQL, conn);
        NpgsqlDataReader dr = command.ExecuteReader();

        while (dr.Read())
        {
            for (int i = 0; i < dr.FieldCount; i++)
            {
                lstSelect.Add(dr[i]);
            }
        }

        this.CloseConn();

        return lstSelect;
    }
    catch (Exception)
    {
        MessageBox.Show("Errr on query!");
        return null;
    }
}
{% endhighlight %}

Thats pretty much it! Before I forget, I want to focus on two things.

This is working for only one table. It’s just a simple example. If you want to do your nested queries, it’s all about the SQL string builder.
When you query into your database and get a List<Object> your data comes like this (if you have a table with 2 columns):

* list[0] = first column of the first element
* list[1] = second column of the first element
* list[2] = first column of the second element
* list[3] = second column of the second element
* ... etc

[npgsql]: http://www.npgsql.org/
[doc]: http://www.npgsql.org/doc/
[dll]: http://dl.dropbox.com/u/5986982/Npgsql.rar
[ref]: http://msdn.microsoft.com/pt-br/library/618ayhy6.aspx
