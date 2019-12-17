---
layout: post
title: c# sqladapter 与sqldataReader
category: 技术
tags: [SQL]
keywords: SqlDataReader,SqlDataAdapter,原理
description:
---

#ADO.NET提供了丰富的数据库操作，在这些操作中SqlConnection和SqlCommand类是必须使用的，但接下来可以分为两类操作：

第一类是用SqlDataReader直接一行一行的读取数据库。
第二类是SqlDataAdapter联合DataSet来读取数据。
下面通过两个子程序，来看看它们的用法：

SqlDataReader
```
private void button1_Click(object sender, EventArgs e)
        {
            string cnn_char = @"Data Source=.\SQLEXPRESS;AttachDbFilename=|DataDirectory|\MyDB.mdf;Integrated Security=True;User Instance=True";
            using (SqlConnection conn = new SqlConnection(cnn_char))
            {
                conn.Open();
                using (SqlCommand cmd = conn.CreateCommand())
                {
                    cmd.CommandText = "Select * from T_User where UserName=@User";
                    cmd.Parameters.Add(new SqlParameter("User",textUserName.Text));
                    using(SqlDataReader reader=cmd.ExecuteReader())
                    {
                        if (reader.Read())
                        {
                            int errorTimes = reader.GetInt32(reader.GetOrdinal("ErrorTimes"));
                                                                     
                        }                                           
                    }
                }
            }
        }
```
接下来是SqlDataAdapter：

```
private void button3_Click(object sender, EventArgs e)
        {
             string cnn_char = @"Data Source=.\SQLEXPRESS;AttachDbFilename=|DataDirectory|\MyDB.mdf;Integrated Security=True;User Instance=True";
             using (SqlConnection conn = new SqlConnection(cnn_char))
             {
                 conn.Open();
                 using (SqlCommand cmd = conn.CreateCommand())
                 {
                     cmd.CommandText = "Select * from T_User";
                     DataSet dataset = new DataSet();
                     SqlDataAdapter adapter = new SqlDataAdapter(cmd);//将SqlCommand与SqlDataAdapter绑定
                     adapter.Fill(dataset);
                     DataTable table = dataset.Tables[0];
                     foreach(DataRow row in table.Rows)
                     {
                         string name=Convert.ToString(row["UserName"]);
                         MessageBox.Show(name);
                     }              
                 }
             }
        }
```

      通过这两个事件的代码，我们可以看出，也符合前面说的规则：这两种读取数据的方法都需要SqlConnection和SqlCommand类的使用。它们的最大的区别是数据源不同：

      SqlDataReader的数据源在数据库服务器上，对于程序而言，它在数据库服务器上设置了一个游标，指向一行数据，用Read()方法来对游标进行判断，当它返回false时，表示查询的数据已取完。因此，它适合数据量比较大的时候的读取，因为它不占内存，数据在数据库服务器中。它的缺点在于，当数据库服务连接断开时，不能再进行数据的读取了。

      SqlDataAdapter的方式，数据源在内存中，用一个数据集DataSet类的实例进行存储。SqlDataAdapter相当于是一个桥梁，将数据库服务器中的数据读取到内存中，它的Fill( )方法完成了这个过程。因此，对于小量的数据，它的一个优点还在于，即使当服务器连接断开时，也能继续读取数据。

       继续说一说SqlDataAdapter。由于SqlDataAdapter是桥梁，因此需要与一个SQL命令绑定，所SqlDataAdapter对象有一个SelectCommand属性。可以在创建SqlDataAdapter时作为参数写入构造函数中，也可以在创建实例后，赋给SelectCommand属性。dataset有集合Tables，对于每一个DataTable，有集合Rows。对于每一行row，可以使用row["列名"]来获得数据，注意row[]索引出来的是object对象，因此需要显式的转换。

出处：http://www.cnblogs.com/wxhpy7722/archive/2011/08/27/2155503.html