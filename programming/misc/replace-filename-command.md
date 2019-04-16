# Replace Filename Command

This is an example to replace "[天堂www.*.com]" in the video file name. 

Create a `rename.bat` file with command below:

```bat
@echo off
Powershell.exe -executionpolicy remotesigned -File ./rename.ps1
# pause
```

Create a `rename.ps1` file with command below:

```ps1
# Replace [天堂www.*.com] from video file name
$stringToReplace = -join([regex]::Escape("[天堂"), "www.*.com", [regex]::Escape("]"))
ls *.avi,*.mp4,*.mkv | ren -NewName {$_.name -replace $stringToReplace,""}
```

Put both `rename.bat` and `rename.ps1` in the video folder, click on the `rename.bat` and it will execute powershell command `rename.ps1`

## Expected Result

- [天堂www.abc.com]video_001.avi --> video_001.avi
- [天堂www.abc.com]video_002.mp4 --> video_002.mp4
- [天堂www.abc.com]video_003.mkv --> video_003.mkv