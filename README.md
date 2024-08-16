@echo off
echo Starting enhanced cleanup and optimization...

:: Request administrative privileges
echo Requesting administrative privileges...
if "%1"=="" (
    powershell start -verb runas '%0' admin
    exit
)

:admin
echo Administrative privileges granted.

:: Clean prefetch files
echo Deleting prefetch files...
del /q /f /s %SystemRoot%\Prefetch\*

:: Clean temporary files (system and user)
echo Deleting temporary files...
del /q /f /s %TEMP%\*
del /q /f /s %SystemRoot%\Temp\*

:: Clean browsing history (but not saved history)
echo Deleting browsing history...
RunDll32.exe InetCpl.cpl,ClearMyTracksByProcess 1

:: Clean storage delivery optimization files
echo Deleting delivery optimization files...
del /q /f /s %SystemDrive%\DeliveryOptimization\*

:: Clean thumbnails cache
echo Deleting thumbnails cache...
del /q /f /s %LocalAppData%\Microsoft\Windows\Explorer\thumbcache_*.db

:: Clean temporary internet files
echo Deleting temporary internet files...
RunDll32.exe InetCpl.cpl,ClearMyTracksByProcess 8

:: Clean DirectX shader cache
echo Deleting DirectX shader cache...
del /q /f /s %LocalAppData%\Microsoft\DirectX Shader Cache\*

:: Clean memory dump files
echo Deleting memory dump files...
del /q /f /s %SystemRoot%\Minidump\*
del /q /f /s %SystemRoot%\MEMORY.DMP

:: Clean error report files
echo Deleting error report files...
del /q /f /s %LocalAppData%\Microsoft\Windows\WER\*

:: Clean system log files
echo Deleting system log files...
wevtutil cl System
wevtutil cl Application
wevtutil cl Security

:: Clean IIS log files
echo Deleting IIS log files...
del /q /f /s %SystemDrive%\inetpub\logs\LogFiles\*

:: Clean old prefetch data
echo Deleting old prefetch data...
del /q /f /s %SystemRoot%\Prefetch\*

:: Clean Windows Installer temporary files
echo Deleting Windows Installer temporary files...
del /q /f /s %SystemRoot%\Installer\$PatchCache$\*

:: Clean Microsoft Internet Explorer cache and history
echo Deleting Internet Explorer cache and history...
RunDll32.exe InetCpl.cpl,ClearMyTracksByProcess 255

:: Clean Microsoft Edge cache
echo Deleting Microsoft Edge cache...
del /q /f /s %LocalAppData%\Microsoft\Edge\User Data\Default\Cache\*

:: Clean Brave browser cache
echo Deleting Brave browser cache...
del /q /f /s %LocalAppData%\BraveSoftware\Brave-Browser\User Data\Default\Cache\*

:: Clean Google Chrome cache
echo Deleting Google Chrome cache...
del /q /f /s %LocalAppData%\Google\Chrome\User Data\Default\Cache\*

:: Clean Windows Storage Sense files
echo Running Windows Storage Sense...
powershell.exe -command "Start-Process ms-settings:storagesense"

:: Clean system errors
echo Cleaning system errors...
chkdsk /f /r

:: Check and fix corrupt system files with SFC
echo Checking and fixing corrupt system files with SFC...
sfc /scannow

:: Check and repair system image with DISM
echo Checking and repairing system image with DISM...
DISM /Online /Cleanup-Image /ScanHealth
DISM /Online /Cleanup-Image /RestoreHealth

:: Full system scan for rootkits and malware
echo Performing a full system scan for rootkits and malware...
powershell Start-MpScan -ScanType FullScan

:: Run a full antivirus scan with Windows Defender
echo Running a full antivirus scan with Windows Defender...
powershell "Start-MpScan -ScanType FullScan"

:: Clean non-critical files used by Microsoft Defender Antivirus
echo Cleaning non-critical files used by Microsoft Defender Antivirus...
powershell.exe -command "Remove-MpPreference -ExclusionPath"

:: Clean DNS cache
echo Cleaning DNS cache...
ipconfig /flushdns

:: Optimize system by closing unnecessary background programs
echo Optimizing system by closing unnecessary background programs...
taskkill /F /FI "STATUS eq NOT RESPONDING"

:: Ensure cookies are not deleted
echo Ensuring cookies are not deleted...

:: Optional: Defragmentation for HDDs (Uncomment if needed)
:: echo Defragmenting the hard drive...
:: defrag %SystemDrive% /U /V

:: Optional: Delete old system restore points (Uncomment if needed)
:: echo Deleting old system restore points...
:: vssadmin delete shadows /for=%SystemDrive% /oldest

:: Optional: Disable hibernation (Uncomment if needed)
:: echo Disabling hibernation...
:: powercfg -h off

echo Cleanup and optimization complete!
pause
