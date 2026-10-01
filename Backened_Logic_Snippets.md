# Backend Engineering & Logic Snippets (ASP.NET / VB)

Rather than uploading the complete legacy Visual Studio Web Forms solution, this document highlights the core backend logic, ADO.NET database integrations, and session management protocols engineered from scratch for the Estashirna platform.

## 1. Dynamic Content Rendering & User Interactions
This script handles the dynamic retrieval of specific item details based on the active session ID, binds the results directly to UI elements, and processes user interactions (Likes, Dislikes, and Comments).

```vb
Imports System.Data
Imports System.Data.SqlClient

Public Class itemdetails
    Inherits System.Web.UI.Page
    
    Dim con As String = " Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Database1.mdf;Integrated Security=True"
    Dim dbcon As New SqlConnection(con)
    Dim dataset1 As New DataSet
    Dim dataset2 As New DataSet
    Dim cm As New SqlCommand
    Dim sql As String
    Dim sql2 As String

    Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
        ' Verify active user session
        If Session("username") = "" Then
            Button1.Visible = False
        End If
        Label7.Text = Session("username")
        
        ' Dynamically load item details based on clicked Item ID
        sql = "Select * from Items where IT_id =" & Session("itemid")
        dbcon.Open()
        Dim dataadapter1 As New SqlDataAdapter(sql, dbcon)
        dataadapter1.Fill(dataset1, "Items")
        dbcon.Close()

        ' Bind database records to frontend UI elements
        GridView1.DataSource = dataset1.Tables(0)
        GridView1.DataBind()
        Label2.Text = dataset1.Tables(0).Rows(0)(1).ToString()
        Label4.Text = dataset1.Tables(0).Rows(0)(2).ToString()
        Label6.Text = dataset1.Tables(0).Rows(0)(3).ToString()
        Label9.Text = dataset1.Tables(0).Rows(0)(5).ToString()
        Label10.Text = dataset1.Tables(0).Rows(0)(6).ToString()
        Image1.ImageUrl = dataset1.Tables(0).Rows(0)(4).ToString()
        
        ' Load associated comments on initial page load
        If Not Page.IsPostBack Then
            sql2 = "Select usname,Comments from Co_item where items_name='" & Label2.Text & "'"
            dbcon.Open()
            Dim dataadapter2 As New SqlDataAdapter(sql2, dbcon)
            dataadapter2.Fill(dataset2, "Co_item")
            dbcon.Close()
            
            Session("comments") = (2 + dataset2.Tables(0).Rows.Count)
            GridView2.DataSource = dataset2.Tables(0)
            GridView2.DataBind()
        End If
    End Sub

    ' Process User Comment Insertion
    Protected Sub Button1_Click(sender As Object, e As EventArgs) Handles Button1.Click
        sql = "insert into Co_item (CT_id,items_name,Comments,usname)" & " values('" & Session("comments") & "','" & Label2.Text & "','" & TextBox1.Text & "','" & Session("username") & "')"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("itemdetails.aspx")
    End Sub

    ' Process User "Like" Interaction and Database Update
    Protected Sub Button2_Click(sender As Object, e As EventArgs) Handles Button2.Click
        Button2.Visible = False
        Button3.Visible = False
        
        sql2 = "select I_Like from Items where IT_id='" & Session("itemid") & "'"
        dbcon.Open()
        Dim dataadapter2 As New SqlDataAdapter(sql2, dbcon)
        dataadapter2.Fill(dataset2, "Like")
        dbcon.Close()
        
        Session("iLike") = dataset2.Tables(0).Rows(0)(0)
        Session("iLike") = Session("iLike") + 1
        sql = "update Items set I_Like ='" & Session("iLike") & "'where IT_id='" & Session("itemid") & "'"

        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()

        Response.Redirect("itemdetails.aspx")
    End Sub
End Class



```
## 2. Administrative Control Panel & Multi-Entity CRUD Operations
This script manages the administrative dashboard, handling the initialization of multiple data grids (Items, Careers, Travel, Users, Notifications) 
and executing direct CRUD operations across all platform modules.

```vb
Imports System.Data
Imports System.Data.SqlClient

Public Class admin
    Inherits System.Web.UI.Page
    
    Dim con As String = " Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Database1.mdf;Integrated Security=True"
    Dim dbcon As New SqlConnection(con)
    Dim dataset1 As New DataSet
    Dim dataset2 As New DataSet
    Dim dataset3 As New DataSet
    Dim dataset4 As New DataSet
    Dim cm As New SqlCommand
    Dim sql As String

    Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
        ' Initialize and bind multiple Data Grids on initial page load
        If Not Page.IsPostBack Then
            ' Load E-Commerce Items
            sql = "select * from Items"
            dbcon.Open()
            Dim dataadapter1 As New SqlDataAdapter(sql, dbcon)
            dataadapter1.Fill(dataset1, "Items")
            dbcon.Close()
            GridView1.DataSource = dataset1.Tables(0)
            GridView1.DataBind()

            ' Load Career/Jobs Data
            sql = "select * from CAREER"
            dbcon.Open()
            Dim dataadapter2 As New SqlDataAdapter(sql, dbcon)
            dataadapter2.Fill(dataset3, "CAREER")
            dbcon.Close()
            GridView3.DataSource = dataset3.Tables(0)
            GridView3.DataBind()

            ' Load Tourism/Travel Data
            sql = "select * from Travel"
            dbcon.Open()
            Dim dataadapter3 As New SqlDataAdapter(sql, dbcon)
            dataadapter3.Fill(dataset2, "Travel")
            dbcon.Close()
            GridView2.DataSource = dataset2.Tables(0)
            GridView2.DataBind()

            ' Load User Login Credentials
            sql = "select * from login"
            dbcon.Open()
            Dim dataadapter4 As New SqlDataAdapter(sql, dbcon)
            dataadapter4.Fill(dataset4, "login")
            dbcon.Close()
            GridView4.DataSource = dataset4.Tables(0)
            GridView4.DataBind()
        End If
    End Sub

    ' ==========================================
    ' MODULE 1: ITEMS MANAGEMENT (CRUD)
    ' ==========================================
    Protected Sub Button1_Click(sender As Object, e As EventArgs) Handles Button1.Click
        sql = "insert into Items (IT_id,Items_name,Description,price,imageurl,I_Like,I_DLike)" & " values('" & TextBox1.Text & "','" & TextBox2.Text & "','" & TextBox3.Text & "','" & TextBox4.Text & "','" & TextBox17.Text & ",0,0')"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("admin.aspx")
    End Sub

    Protected Sub Button2_Click(sender As Object, e As EventArgs) Handles Button2.Click
        sql = "update Items set Items_name ='" & TextBox2.Text & "', Description='" & TextBox3.Text & "', price='" & TextBox4.Text & "', imageurl='" & TextBox17.Text & "'where IT_id='" & TextBox1.Text & "'"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("admin.aspx")
    End Sub

    Protected Sub Button3_Click(sender As Object, e As EventArgs) Handles Button3.Click
        sql = "delete from Items where IT_id='" & TextBox1.Text & "'"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("admin.aspx")
    End Sub

    ' ==========================================
    ' MODULE 2: TRAVEL MANAGEMENT (CRUD)
    ' ==========================================
    Protected Sub Button6_Click(sender As Object, e As EventArgs) Handles Button6.Click
        sql = "insert into Travel (t_id,city_name,Description,price,imageurl,T_Like,T_DLike)" & " values('" & TextBox5.Text & "','" & TextBox6.Text & "','" & TextBox7.Text & "','" & TextBox8.Text & "','" & TextBox18.Text & ",0,0')"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("admin.aspx")
    End Sub

    Protected Sub Button7_Click(sender As Object, e As EventArgs) Handles Button7.Click
        sql = "update Travel set city_name ='" & TextBox6.Text & "', Description='" & TextBox7.Text & "', price='" & TextBox8.Text & "', imageurl='" & TextBox18.Text & "'where t_id='" & TextBox5.Text & "'"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("admin.aspx")
    End Sub

    Protected Sub Button8_Click(sender As Object, e As EventArgs) Handles Button8.Click
        sql = "delete from Travel where t_id='" & TextBox5.Text & "'"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("admin.aspx")
    End Sub

    ' ==========================================
    ' MODULE 3: CAREER MANAGEMENT (CRUD)
    ' ==========================================
    Protected Sub Button10_Click(sender As Object, e As EventArgs) Handles Button10.Click
        sql = "insert into CAREER (c_id,Career_name,Description,Salary,imageurl)" & " values('" & TextBox9.Text & "','" & TextBox10.Text & "','" & TextBox11.Text & "','" & TextBox19.Text & "','" & TextBox20.Text & "')"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("admin.aspx")
    End Sub

    Protected Sub Button11_Click(sender As Object, e As EventArgs) Handles Button11.Click
        sql = "update CAREER set Career_name ='" & TextBox10.Text & "', Description='" & TextBox11.Text & "', Salary='" & TextBox19.Text & "', imageurl='" & TextBox20.Text & "'where c_id='" & TextBox9.Text & "'"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("admin.aspx")
    End Sub

    Protected Sub Button12_Click(sender As Object, e As EventArgs) Handles Button12.Click
        sql = "delete from CAREER where c_id='" & TextBox9.Text & "'"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("admin.aspx")
    End Sub

    ' ==========================================
    ' MODULE 4: USER & NOTIFICATION MANAGEMENT 
    ' ==========================================
    Protected Sub Button15_Click(sender As Object, e As EventArgs) Handles Button15.Click
        sql = "update login set email ='" & TextBox15.Text & "', pss='" & TextBox16.Text & "'where usname='" & TextBox14.Text & "'"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("admin.aspx")
    End Sub

    Protected Sub Button16_Click(sender As Object, e As EventArgs) Handles Button16.Click
        sql = "delete from login where usname='" & TextBox14.Text & "'"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("admin.aspx")
    End Sub

    Protected Sub Button18_Click(sender As Object, e As EventArgs) Handles Button18.Click
        sql = "insert into Notification (N_Name,Notif)" & " values('" & TextBox21.Text & "','" & TextBox22.Text & "')"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("admin.aspx")
    End Sub

    Protected Sub Button19_Click(sender As Object, e As EventArgs) Handles Button19.Click
        sql = "update Notification set Notif ='" & TextBox22.Text & "'where N_Name='" & TextBox21.Text & "'"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("admin.aspx")
    End Sub

    Protected Sub Button20_Click(sender As Object, e As EventArgs) Handles Button20.Click
        sql = "delete from Notification where N_Name='" & TextBox21.Text & "'"
        dbcon.Open()
        cm = New SqlCommand(sql, dbcon)
        cm.ExecuteNonQuery()
        dbcon.Close()
        Response.Redirect("admin.aspx")
    End Sub
End Class
