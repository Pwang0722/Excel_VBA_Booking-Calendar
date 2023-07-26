<h2 align="center">Excel: Scheduled Booking Management with VBA Macros and Formulas</h1>
</div>

### Spreadsheet Example
- [Booking Sheet.xlsm (password:1234)](https://github.com/Pwang0722/Excel_PerpetualCalendar_OutlookCalendar/raw/main/Booking%20Sheet%20Template.xlsm)

---

### Outline
A spreadsheet with a customized perpetual calendar that allows team members to make bookings for their duties each month and update specific bookings to the Outlook Calendar. It involves utilizing multiple Functions, Conditional Formatting, and VBA Macros to efficiently achieve the desired objectives. And created Formulas and VBA Macros with the help of ChatGPT


---

### Sheet Protection 
- To avoid accidentally modifying the template worksheets, they will be protected with a password every time the workbook is opened or closed.
  
Macro example for protecting sheets:
  ```bash
Private Sub Workbook_Open()
    ProtectSheets
End Sub

Private Sub Workbook_BeforeClose(Cancel As Boolean)
    ProtectSheets
End Sub

Private Sub Workbook_SheetSelectionChange(ByVal Sh As Object, ByVal Target As Range)
    Dim protectedSheets As Variant
    Dim ws As Worksheet
    Dim password As String
    
    ' List of sheets to protect
    protectedSheets = Array("TEMPLATE_ALL", "Audio Out-House", "Summary", "HOLIDAYS")
    
    ' Check if the changed sheet is one of the protected sheets
    On Error Resume Next
    Set ws = ThisWorkbook.Sheets(Target.Worksheet.Name)
    On Error GoTo 0
    
    If Not ws Is Nothing And IsInArray(ws.Name, protectedSheets) Then
        ' Check if the sheet is protected
        If ws.ProtectContents Then
            ' Prompt the user to enter the password to unprotect the sheet
            password = InputBox("Enter the password to unprotect the sheet:", "Password")
            
            ' Check if the entered password matches the preset password
            If password = "1234" Then
                ' Unprotect the sheet to allow editing
                ws.Unprotect password:="1234"
            Else
                MsgBox "Incorrect password. The sheet will remain protected.", vbExclamation
                Application.EnableEvents = False
                Target.Offset(1, 0).Select ' Move to the next cell to avoid an infinite loop
                Application.EnableEvents = True
            End If
        End If
    End If
End Sub

Private Sub ProtectSheets()
    Dim protectedSheets As Variant
    Dim ws As Worksheet
    
    ' List of sheets to protect
    protectedSheets = Array("TEMPLATE_ALL", "Audio Out-House", "Summary", "HOLIDAYS")
    
    ' Loop through each protected sheet and protect with the preset password
    For Each sheetName In protectedSheets
        On Error Resume Next
        Set ws = ThisWorkbook.Sheets(sheetName)
        On Error GoTo 0
        
        If Not ws Is Nothing Then
            ws.Protect password:="1234", UserInterfaceOnly:=True
        End If
    Next sheetName
End Sub

Function IsInArray(val As Variant, arr As Variant) As Boolean
    IsInArray = (UBound(Filter(arr, val)) > -1)
End Function
  ```
---

### Perpetual Calendar
- Sheet 'TEMPLATE_ALL' contains a perpetual calendar which could generate a clean monthly calendar for team members to make bookings for their projects.
- Generate a clean monthly calendar by select desired Year and Month in cells AN2 & AN5.

Macro example for generating calendar:
  ```bash
Sub GenerateBookingSheet()
    NewSheet = Range("B1").Text & " " & Range("AM1")
    Sheets("TEMPLATE_ALL").Copy Before:=Sheets(2)
    ActiveSheet.Name = NewSheet
    ActiveSheet.Select
    Range("B1:AJ79").Select
    Selection.Copy
    Range("B1:AJ79").Select
    Selection.PasteSpecial Paste:=xlPasteValues, Operation:=xlNone, SkipBlanks _
        :=False, Transpose:=False
    Columns("AL:AV").Hidden = True
    Range("B1").Select
    MsgBox "Done"
End Sub
  ```
- Design with 6 weeks from Monday to Friday by using DATE and WEEKDAY functions to generate the dates based on Year and Month input in cells AN2 & AN5.
  
Formula example in cell H8:
  ```bash
=DATE(AN2,AN5,1)-WEEKDAY(DATE(AN2,AN5,1),2)+1
  ```
- Use Conditional Formatting with a formula to format cells' color in grey for those dates that don't belong to the selected month.

Formula example for Conditional Formatting wtih MONTH function:
  ```bash
=MONTH($H$8)<>$AN$5
 ```
---
