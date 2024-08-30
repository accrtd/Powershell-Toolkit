Quick compare :loop: two files on bytes level.

Example run:
```
#############################
QuickFilesComparerHex ver 1.1
#############################
[INFO] Processing ....
[WARNING] Files have different size!
[INFO] 13 bytes vs 15 bytes
[INFO] Biger file: .\testB.txt
[INFO] Difference (aganist .\testB.txt):
        C2:7 66:10

File A:
        [1] 74 65 73 74 69 6E 67 A0 66 69 6C 65
        [2] 41

File B:
        [1] 74 65 73 74 69 6E 67 C2 A0 66 66 69
        [2] 6C 65 41

[INFO] Done :)
```

Code:

```powershell
param (
    [string]
    $FileA = $(throw "-FileA is required."),

    [string]
    $FileB = $(throw "-FileA is required."),

    [int]
    $ByteLineLength = 12,

    [bool]
    $PrintFiles = $true
)

$QFCVer = "1.1"

function PrintArray($byteArray){
    $lineCounter = 0
    $lineIndex = 1
    Write-Host "`t[$($lineIndex)] " -NoNewline
    foreach($elem in $byteArray) {
        Write-Host ("$([System.BitConverter]::ToString($elem)) ") -NoNewline

        #Break line
        $lineCounter++
        if($lineCounter -eq $ByteLineLength){
            $lineCounter = 0
            $lineIndex ++
            Write-Host "`n`t[$($lineIndex)] " -NoNewline
        }
    }
}

function FindDiff($arrayA, $arrayB){
    $lineCounter = 0
    $lineIndex = 1
    $indexOfArrayB = 0
    Write-Host "`t"  -NoNewline
    for($indexOfArrayA=0; $indexOfArrayA -le $arrayA.Count - 1; $indexOfArrayA++){
        if($indexOfArrayB -lt $arrayB.Count){
            if($arrayA[$indexOfArrayA] -ne $arrayB[$indexOfArrayB]){
                Write-Host "$([System.BitConverter]::ToString($arrayA[$indexOfArrayA])):$($indexOfArrayA) " -NoNewline
            }else{
                $indexOfArrayB++
            }
        }else{
            Write-Host "$([System.BitConverter]::ToString($arrayA[$indexOfArrayA])):$($indexOfArrayA) " -NoNewline
        }
    }
}

Write-Host "#############################" -ForegroundColor yellow -BackgroundColor red
Write-Host "QuickFilesComparerHex ver $($QFCVer)" -ForegroundColor yellow -BackgroundColor red
Write-Host "#############################" -ForegroundColor yellow -BackgroundColor red
Write-Host "[INFO] Processing ...."

$fileABytes = [System.IO.File]::ReadAllBytes($FileA)
$fileBBytes = [System.IO.File]::ReadAllBytes($FileB)

$bigerFileName = ""
if($fileABytes.Count -ne $fileBBytes.Count){
    $bigerFileName = if($fileABytes.Count -gt $fileBBytes.Count) { $FileA } else { $FileB }
    Write-Host "[WARNING] Files have different size!" -ForegroundColor white -BackgroundColor red
    Write-Host "[INFO] $($fileABytes.Count) bytes vs $($fileBBytes.Count) bytes"
    Write-Host "[INFO] Biger file: $($bigerFileName)"
}

if($bigerFileName -ne ""){
    Write-Host "[INFO] Difference (aganist $($bigerFileName)):"
} else {
    Write-Host "[INFO] Difference:"
}
if($fileABytes.Count -gt $fileBBytes.Count){ 
    FindDiff -arrayA $fileABytes -arrayB $fileBBytes
} else{ 
    FindDiff -arrayA $fileBBytes -arrayB $fileABytes
}

if($PrintFiles){
    Write-Host "`n`nFile A:"
    PrintArray($fileABytes)

    Write-Host "`n`nFile B:"
    PrintArray($fileBBytes)
}

Write-Host "`n`n[INFO] Done :)"
```
