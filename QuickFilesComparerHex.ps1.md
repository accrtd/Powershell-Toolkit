Quick compare :loop: two files on bytes level.

Example run:
```
##########################
QuickFilesComparerHex ver 1.0
##########################
[WARNING] Files have different size!
[WARNING] 13 bytes vs 14 bytes
[WARNING] Biger file: .\testB.txt
Difference (aganist .\testB.txt):
        C2

File A:
        [1] 74 65 73 74 69 6E 67 A0 66 69 6C 65
        [2] 41

File B:
        [1] 74 65 73 74 69 6E 67 C2 A0 66 69 6C
        [2] 65 41
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
Write-Host "##########################" -ForegroundColor yellow -BackgroundColor red
Write-Host "QuickFilesComparerHex ver 1.0" -ForegroundColor yellow -BackgroundColor red
Write-Host "##########################" -ForegroundColor yellow -BackgroundColor red

function PrintFile($byteArray){
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

$fileABytes = [System.IO.File]::ReadAllBytes($FileA)
$fileBBytes = [System.IO.File]::ReadAllBytes($FileB)

$bigerFileName = ""
if($fileABytes.Count -ne $fileBBytes.Count){
    $bigerFileName = if($fileABytes.Count -gt $fileBBytes.Count) { $FileA } else { $FileB }
    Write-Host "[WARNING] Files have different size!" -ForegroundColor white -BackgroundColor red
    Write-Host "[WARNING] $($fileABytes.Count) bytes vs $($fileBBytes.Count) bytes" -ForegroundColor white -BackgroundColor red
    Write-Host "[WARNING] Biger file: $($bigerFileName)" -ForegroundColor white -BackgroundColor red
}

$lineCounter = 0
$loopCounter = if($fileABytes.Count -gt $fileBBytes.Count) { $fileABytes.Count } else { $fileBBytes.Count }

if($bigerFileName -ne ""){
    Write-Host "Difference (aganist $($bigerFileName)):"
} else {
    Write-Host "Difference:"
}
$diff = if($fileABytes.Count -gt $fileBBytes.Count) { [System.Linq.Enumerable]::Except($fileABytes, $fileBBytes) } else { $fileBBytes | Where {$fileABytes -NotContains $_} }
$lineCounter = 0
Write-Host "`t" -NoNewline
foreach($elem in $diff) {
    Write-Host ("$([System.BitConverter]::ToString($elem)) ") -NoNewline

    #Break line
    $lineCounter++
    if($lineCounter -eq $ByteLineLength){
        $lineCounter = 0
        $lineIndex ++
        Write-Host "`n`t[$($lineIndex)] " -NoNewline
    }
}

if($PrintFiles) {
    Write-Host "`n`nFile A:"
    PrintFile($fileABytes)

    Write-Host "`n`nFile B:"
    PrintFile($fileBBytes)
}
```
