---
title: "Move Method Example (VB)"
 
 
manager: soliver
ms.date: 11/16/2014
ms.audience: Developer
ms.topic: reference
  
localization_priority: Normal
ms.assetid: e3e837a4-f46d-f8ec-11ba-9b8d66105b13
description: "This example uses the Move method to position the record pointer based on user input."
---

# Move Method Example (VB)

This example uses the [Move](move-method-ado.md) method to position the record pointer based on user input. 
  
```
 
'BeginMoveVB 
 
 'To integrate this code 
 'replace the data source and initial catalog values 
 'in the connection string 
 
Public Sub Main() 
 On Error GoTo ErrorHandler 
 
 ' connection and recordset variables 
 Dim rstAuthors As ADODB.Recordset 
 Dim Cnxn As ADODB.Connection 
 Dim strCnxn As String 
 Dim strSQLAuthors As String 
 ' record variables 
 Dim varBookmark As Variant 
 Dim strCommand As String 
 Dim lngMove As Long 
 
 ' Open connection 
 Set Cnxn = New ADODB.Connection 
 strCnxn = "Provider='sqloledb';Data Source='MySqlServer';" &amp; _ 
 "Initial Catalog='Pubs';Integrated Security='SSPI';" 
 Cnxn.Open strCnxn 
 
 ' Open recordset from Authors table 
 Set rstAuthors = New ADODB.Recordset 
 rstAuthors.CursorLocation = adUseClient 
 ' Use client cursor to allow use of AbsolutePosition property 
 strSQLAuthors = "SELECT au_id, au_fname, au_lname, city, state FROM Authors ORDER BY au_lname" 
 rstAuthors.Open strSQLAuthors, strCnxn, adOpenStatic, adLockOptimistic, adCmdText 
 
 rstAuthors.MoveFirst 
 
 Do 
 ' Display information about current record and 
 ' ask how many records to move 
 
 strCommand = InputBox( _ 
 "Record " &amp; rstAuthors.AbsolutePosition &amp; _ 
 " of " &amp; rstAuthors.RecordCount &amp; vbCr &amp; _ 
 "Author: " &amp; rstAuthors!au_fname &amp; _ 
 " " &amp; rstAuthors!au_lname &amp; vbCr &amp; _ 
 "Location: " &amp; rstAuthors!city &amp; _ 
 ", " &amp; rstAuthors!State &amp; vbCr &amp; vbCr &amp; _ 
 "Enter number of records to Move " &amp; _ 
 "(positive or negative).") 
 
 ' this is for exiting the loop 
 'lngMove = CLng(strCommand) 
 
 lngMove = CLng(Val(strCommand)) 
 If lngMove = 0 Then 
 MsgBox "You either entered a non-number or canceled the input box. Exit the application." 
 Exit Do 
 End If 
 
 ' Store bookmark in case the Move goes too far 
 ' forward or backward 
 varBookmark = rstAuthors.Bookmark 
 
 ' Move method requires parameter of data type Long 
 rstAuthors.Move lngMove 
 
 ' Trap for BOF or EOF 
 If rstAuthors.BOF Then 
 MsgBox "Too far backward! Returning to current record." 
 rstAuthors.Bookmark = varBookmark 
 End If 
 If rstAuthors.EOF Then 
 MsgBox "Too far forward! Returning to current record." 
 rstAuthors.Bookmark = varBookmark 
 End If 
 Loop 
 
 ' clean up 
 rstAuthors.Close 
 Cnxn.Close 
 Set rstAuthors = Nothing 
 Set Cnxn = Nothing 
 Exit Sub 
 
ErrorHandler: 
 ' clean up 
 If Not rstAuthors Is Nothing Then 
 If rstAuthors.State = adStateOpen Then rstAuthors.Close 
 End If 
 Set rstAuthors = Nothing 
 
 If Not Cnxn Is Nothing Then 
 If Cnxn.State = adStateOpen Then Cnxn.Close 
 End If 
 Set Cnxn = Nothing 
 
 If Err <> 0 Then 
 MsgBox Err.Source &amp; "-->" &amp; Err.Description, , "Error" 
 End If 
End Sub 
'EndMoveVB 

```

