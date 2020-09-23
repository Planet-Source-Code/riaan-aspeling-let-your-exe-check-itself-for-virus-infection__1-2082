<div align="center">

## Let your EXE check itself for VIRUS infection


</div>

### Description

As far as I know viruses modify the header of your complied EXE to jump to a different location to execute the virus. This means the header will change and your EXE file will (with some viruses) have the virus attached to the end of the EXE file. I've included a module you should use in your EXE file with a function it should call to check itself. The rest of the code as well as the module you have to include in your project will compile as an inoculate program to write the footer to your EXE file so the EXE file can check itself. (THIS SOFTWARE IS NOT FOR PROGRAMMERS THAT WRITE VIRUSES ;) PS: This program will not make your program protect itself but will let the user know when it's infected.
 
### More Info
 
This is not a guarantee against viruses ... there probably will be viruses that works differently. Please let us all work together and try and make this program as full proof as possible. Any suggestions you might have will be greatly appreciated and will make this code allot more effective.

The program you want to inoculate should not be in use.


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Riaan Aspeling](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/riaan-aspeling.md)
**Level**          |Unknown
**User Rating**    |3.7 (11 globes from 3 users)
**Compatibility**  |VB 5\.0, VB 6\.0
**Category**       |[Complete Applications](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/complete-applications__1-27.md)
**World**          |[Visual Basic](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/visual-basic.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/riaan-aspeling-let-your-exe-check-itself-for-virus-infection__1-2082/archive/master.zip)

### API Declarations

```
'******************************************
' CODENAME : VIRUS WARRIOR
' DATE : 14 June 1999
' DESIGNED AND CODED BY : Riaan Aspeling
' E-MAIL : riaana@hotmail.com
'******************************************
' PASTE THE NEXT SECTIONS INTO NOTEPAD
' AND SAVE IT WITH THE SPECIFIED EXTENSIONS
'******************************************
'
' Copy and paste the next section into
' Notepad and save the file as
' Enock.vbp
'
' SECTION Enock.vbp START ************************
Type=Exe
Form=frmDoIt.frm
Module=ENOCK; ENOCK.bas
Module=mFile; mFile.bas
Startup="frmDoIt"
HelpFile=""
ExeName32="Enock.exe"
Command32=""
Name="Project1"
HelpContextID="0"
CompatibleMode="0"
MajorVer=1
MinorVer=0
RevisionVer=0
AutoIncrementVer=0
ServerSupportFiles=0
VersionCompanyName="Altered Reality Corporation"
CompilationType=0
OptimizationType=0
FavorPentiumPro(tm)=0
CodeViewDebugInfo=0
NoAliasing=0
BoundsCheck=0
OverflowCheck=0
FlPointCheck=0
FDIVCheck=0
UnroundedFP=0
StartMode=0
Unattended=0
ThreadPerObject=0
MaxNumberOfThreads=1
' SECTION Enock.vbp END ************************
'
' Copy and paste the next section into
' Notepad and save the file as
' ENOCK.bas
'
' SECTION ENOCK.bas START ************************
Attribute VB_Name = "ENOCK"
Option Explicit
Const CheckSumSize = 1024  'Size of EXE header to use
Const CheckSumChrLen = 5  'Size of string to contain checksum value
Function CheckENOCK(ByVal Filename As String, Optional lChkSm As Long = -1) As Integer
'Return :
' 2 = File is ENOCKED and CheckSum was Checked
' 1 = File is ENOCKED and didn't check CheckSum
' 0 = Some Error occured
'-1 = File is NOT ENOCKED and didn't check Checksum
'-2 = File is ENOCKED and CheckSum doesn't match
On Error GoTo handelerror
  CheckENOCK = False
  Dim ReadLast As String
  If Filename = "" Then
    Filename = IIf(Right$(App.Path, 1) = "\", App.Path & App.EXEName & ".EXE", App.Path & "\" & App.EXEName & ".EXE")
  End If
  Open Filename For Binary Access Read As #1
  ReadLast = Space(8 + CheckSumChrLen)
  Get #1, LOF(1) - (7 + CheckSumChrLen), ReadLast
  Close #1
  If Mid$(ReadLast, 1, 7) = "|ENOCK|" Then
    If lChkSm >= 0 Then
      If lChkSm = CreateLong(Mid$(ReadLast, 8, CheckSumChrLen)) Then
        CheckENOCK = 2
        Else
        CheckENOCK = -2
      End If
      Else
      CheckENOCK = 1
    End If
    Else
    CheckENOCK = -1
  End If
  Exit Function
handelerror:
  MsgBox Err.Description, 16, "ERROR #" & Err.Number
  CheckENOCK = 0
  Exit Function
End Function
Function CalcCheckSum(ByVal Filename As String) As String
'Return :
' 0 > Successfully calculated the CheckSum
'-1 = There was a error in the CalcCheckSum process somewhere
On Error GoTo handelxorerror
  Dim SBuffer As String
  Dim CalcIt As Long, i As Integer
  If Filename = "" Then
    Filename = IIf(Right$(App.Path, 1) = "\", App.Path & App.EXEName & ".EXE", App.Path & "\" & App.EXEName & ".EXE")
  End If
  Open Filename For Binary Access Read As #1
  SBuffer = Input(CheckSumSize, 1)
  For i = 1 To Len(SBuffer)
    CalcIt = CalcIt + Asc(Mid$(SBuffer, i, 1))
  Next i
  Close #1
  CalcCheckSum = CreateChrs(CalcIt, CheckSumChrLen)
  Exit Function
handelxorerror:
  CalcCheckSum = ""
  MsgBox Err.Description, 16, "ERROR #" & Err.Number
  Exit Function
End Function
Function MakeENOCK(ByVal Filename As String) As Integer
'Return :
' 1 = ENOCKED the file successfully
' 0 = Couldn't ENOCK the file because :
'    a) the CalcCheckSum return -1 or
'    b) the file is already ENOCKED
'-1 = There was a error in the ENOCK process somewhere
On Error GoTo handelmakeenock
  Dim sCalc As String
  Dim ReadLast As String * 1, Writestr As String
  If Filename = "" Then
    Filename = IIf(Right$(App.Path, 1) = "\", App.Path & App.EXEName & ".EXE", App.Path & "\" & App.EXEName & ".EXE")
  End If
  MakeENOCK = 0
  Select Case CheckENOCK(Filename) 'Check file without checking CheckSum
    Case 1 'File is ENOCKED and didn't check CheckSum
      MsgBox "File already ENOCKED", 16
      Exit Function
    Case 0 'Some Error occured
      Exit Function
    Case -1 'File is NOT ENOCKED and didn't check Checksum
```


### Source Code

```
'Save the files as described above
'and compile your ENOCK.EXE program.
'All you have to do now is to include
'the ENOCK.BAS file in your project
'and add this code to the startup of
'your program .. :)
  Dim lCalc As Long
  'Get the current CheckSum value
  lCalc = CreateLong(CalcCheckSum(""))
  If lCalc > 0 Then
    Select Case CheckENOCK("", lCalc)
      Case 2 'File is ENOCKED and CheckSum was Checked
        MsgBox "File is Authentic ....", 32
      Case 0 'Some Error occured
        'Some error occured and will be displayed by the function
      Case -1 'File is NOT ENOCKED and didn't check Checksum
        MsgBox "File is NOT ENOCKED and didn't check Checksum", 32
      Case -2 'File is ENOCKED and CheckSum doesn't match
        MsgBox "File is NOT Authentic, posible virus infection ", 16
    End Select
  End If
```

