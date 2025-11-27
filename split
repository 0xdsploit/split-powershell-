param(
    [Parameter(Position=0)]
    [string]$InputFile,

    [Parameter()]
    [int]$p,                     # number of parts

    [Parameter()]
    [string]$o = "part_",        # output prefix

    [switch]$h,                  # short help
    [switch]$help                # long help
)

function Show-Help {
    Write-Host @"
Usage:
  ./split.ps1 <inputfile> -p <parts> -o <output_prefix>

Example:
  ./split.ps1 myfile.txt -p 5 -o part_

Options:
  -p <int>      Number of parts to split into (required)
  -o <string>   Output file prefix (default: part_)
  -h, --help    Show this help message

Description:
  Splits a file into N equal-sized binary parts.
"@
}

# If user requested help
if ($h -or $help) {
    Show-Help
    exit 0
}

# Validate parameters
if (-not $InputFile -or -not $p) {
    Write-Host "Missing required arguments." -ForegroundColor Red
    Show-Help
    exit 1
}

if (-not (Test-Path $InputFile)) {
    Write-Host "Error: File not found: $InputFile" -ForegroundColor Red
    exit 1
}

# Get file size
$inInfo = Get-Item $InputFile
$fileSize = $inInfo.Length
$partSize = [Math]::Ceiling($fileSize / $p)

Write-Host "Splitting '$InputFile' into $p parts..."
Write-Host "Each part â‰ˆ $partSize bytes"
Write-Host ""

$in = [IO.File]::OpenRead($InputFile)

for ($i = 0; $i -lt $p; $i++) {

    $outName = "$o$i"
    $out = [IO.File]::OpenWrite($outName)

    $remaining = $partSize
    $buffer = New-Object byte[] 65536  # 64 KB chunks

    while ($remaining -gt 0) {
        $toRead = [Math]::Min($buffer.Length, $remaining)
        $read = $in.Read($buffer, 0, $toRead)

        if ($read -le 0) { break }

        $out.Write($buffer, 0, $read)
        $remaining -= $read
    }

    $out.Close()
    Write-Host "Created $outName"
}

$in.Close()

Write-Host ""
Write-Host "Done!"
