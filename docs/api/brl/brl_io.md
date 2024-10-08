---
id: brl.io
title: BRL.IO
sidebar_label: Introduction
---


## Introduction
MaxIO is a powerful feature of BlitzMax that enables developers to work with archives 
(such as ZIP files) as if they were regular file systems. This is achieved through an 
underlying mechanism provided by PhysFS. The integration of PhysFS with BlitzMax allows 
the standard FileSystem functions to seamlessly work with mounted archives.

## Writing Directory and Search Path
MaxIO uses a single writing directory and multiple reading directories called the "search path".
The writing directory is the root of the writable filesystem, and the search path is a list 
of directories and archives that MaxIO can read from.

### Writing Directory
The writing directory is a secure location where your application can store files. By
setting the writing directory, you prevent the application from accessing any directories
above it, enhancing security. For example, if you were to set the writing directory to
"C:\MyGame\MyWritingDirectory" (on Windows), then no MaxIO calls could touch anything
above this directory, including the "C:\MyGame" and "C:" directories.

To give MaxIO full access to the system's real file system, you can set the writing
directory to the root directory, but this is generally not recommended as it may expose
your application to potential security risks.

### Search Path
The search path is a list of directories and archives that MaxIO can read from. When
opening a file for reading, MaxIO goes through the search path, checking each location
for the requested file. The search path creates a single, hierarchical directory structure
that lends itself well to general abstraction with archives and provides better support
for various operating systems.

Drive letters are hidden in MaxIO once you set up your initial paths. This means that you
should not hardcode a drive letter when specifying file paths. Instead, use
platform-independent notation, which ensures compatibility across different operating
systems.

For example, if you want to write to "C:\MyGame\MyConfigFiles\game.cfg" (on Windows), you
might set the writing directory to "C:\MyGame" and then open the file with the
platform-independent notation "MyConfigFiles/game.cfg". This abstraction allows your
application to work seamlessly across multiple platforms.

## Platform-Independent Notation
Platform-independent notation is a standardized way of specifying file paths that works 
across different operating systems. When using MaxIO, you should always specify file paths
using platform-independent notation. This notation uses forward slashes ("/") as directory
separators and omits drive letters.

## Supported Archive Types
MaxIO supports the following archive types:

- .ZIP (pkZip/WinZip/Info-ZIP compatible)
- .7Z (7zip archives)
- .ISO (ISO9660 files, CD-ROM images)
- .GRP (Build Engine groupfile archives)
- .PAK (Quake I/II archive format)
- .HOG (Descent I/II HOG file archives)
- .MVL (Descent II movielib archives)
- .WAD (DOOM engine archives)
- .VDF (Gothic I/II engine archives)
- .SLB (Independence War archives)

## Initializing MaxIO
Before you can use any MaxIO features, you need to initialise it.
Initialization sets up the necessary internal structures and prepares MaxIO for use.
To initialise MaxIO, call the `MaxIO.Init()` function. This function returns an
Int value indicating the success or failure of the initialization process.

Here's an example of how to initialise MaxIO:
```blitzmax
If MaxIO.Init() Then
    ' MaxIO is initialised and ready to use
Else
    Print "Failed to initialise MaxIO"
    End
End If
```
It's important to check the return value of `MaxIO.Init()` to ensure that MaxIO has been
properly initialised. If the initialization fails, you should not attempt to use MaxIO
functions, as they will not behave correctly and may cause unexpected issues.

Once you've finished using the MaxIO module, you should deinitialise it to release any
resources it has acquired. To deinitialise MaxIO, call the `MaxIO.DeInit()` function:
```blitzmax
MaxIO.DeInit()
```

Keep in mind that deinitializing MaxIO while it's still in use can lead to unexpected
behaviour. Always make sure that you have finished working with the module before
deinitializing it.

## Mounting Archives and Directories
Mounting archives and directories in MaxIO refers to the process of attaching an archive
(such as a ZIP file) or a directory to the search path. This allows you to access the
files and directories within the mounted archive or directory as if they were part of the
native file system.

### Mounting Archives
To mount an archive, use the `MaxIO.Mount()` function. This function takes two parameters:
the path to the archive file and the mount point within the search path. The mount point
is a virtual directory where the archive will be attached in the search path.

Here's an example of how to mount a ZIP archive:
```blitzmax
Local archivePath:String = "path/to/your/archive.zip"
Local mountPoint:String = "myArchive"

If MaxIO.Mount(archivePath, mountPoint) Then
    ' Archive is mounted and ready for use
Else
    Print "Failed to mount the archive"
End If
```

Once the archive is mounted, you can access its contents using platform-independent
notation and the standard BlitzMax file functions. For example, if the archive contains
a file named `data.txt`, you can access it using the file path `myArchive/data.txt`.

### Mounting Directories
Mounting directories is similar to mounting archives. You can use the `MaxIO.Mount()` 
function to attach a directory to the search path, just like you would with an archive.

Here's an example of how to mount a directory:
```blitzmax
Local dirPath:String = "path/to/your/directory"
Local mountPoint:String = "myDir"

If MaxIO.Mount(dirPath, mountPoint) Then
    ' Directory is mounted and ready for use
Else
    Print "Failed to mount the directory"
End If
```

Once the directory is mounted, you can access its contents using platform-independent
notation and the standard BlitzMax file functions.

There are several optional parameters you can pass to the `MaxIO.Mount()`` function to
control the behaviour of the mounted archive or directory:

 - `append:Bool = True` : By default, the mounted archive or directory is appended to the
 end of the search path. If you set append to False, the archive or directory will be
 inserted at the beginning of the search path instead.

 - `createVirtualDir:Bool = False`: If set to True, MaxIO will create a virtual directory
 for the mount point if it doesn't already exist.


## Accessing Files Within Archives
After an archive is mounted, you can use the standard BlitzMax file functions to access
files within the archive. MaxIO treats the mounted archive as if it were a regular file
system, allowing you to read and write files with ease.

Here are some examples of how you can read and write files within a mounted archive using
BlitzMax file functions:

### Reading a File from an Archive

To read a file from a mounted archive, you can use the [ReadFile](../../brl/brl.filesystem/#function-readfiletstream-urlobject-) function, just as you
would when working with regular files:

```blitzmax
Local file:TStream = ReadFile("path/to/file/in/archive.txt")
If file
    Local content:String = ReadString(file)
    file.Close()
    Print "Content of the file: " + content
Else
    Print "Failed to open the file in the archive"
End If
```

### Writing a File to an Archive

Writing to files within an archive is generally not supported. Instead, you should extract
the file, modify it, and then add it back to the archive if necessary. However, if the
archive is mounted to a writable directory, you can write a new file to the directory that
will be accessible alongside the archive's contents:

```blitzmax
Local file:TStream = WriteFile("path/to/writable/directory/newfile.txt")
If file
    WriteString(file, "This is a new file.")
    CloseStream(file)
    Print "New file created successfully"
Else
    Print "Failed to create a new file"
End If
```

Remember that the writing directory should be set up to allow writing to the desired
location in the virtual file system created by MaxIO.

## Listing Files and Directories
You can use the standard BlitzMax directory listing functions to obtain a list of files
and directories within the mounted archive. This allows you to navigate and explore
the contents of an archive as if it were a regular directory on your file system.

Here's an example of how to list files and directories within a mounted archive:


``` blitzmax
Function ListDirContents(dirPath:String)
    Local dir:Byte Ptr = ReadDir(dirPath)

    If Not dir
        Print "Failed to open directory: " + dirPath
        Return
    End If

    Local entry:String = NextFile(dir)
    While entry
        Print "Entry: " + entry

        ' Check if the entry is a directory
        If FileType(dirPath + "/" + entry) = FILETYPE_DIR Then
            Print "This is a directory"
            ' Recursively list the contents of the subdirectory
            ListDirContents(dirPath + "/" + entry)
        End If

        entry = NextFile(dir)
    Wend

    CloseDir(dir)
End Function

' Call the function with the path to the mounted archive or a directory within the archive
ListDirContents("path/to/mounted/archive")
```

In this example, the `ListDirContents` function takes a directory path as a parameter and
lists all files and directories within the specified directory. If it encounters a
subdirectory, it calls itself recursively to list the contents of the subdirectory as well.


## Combining Multiple Archives
By mounting multiple archives to the same search path, you can treat them as a single,
unified file system. This means that when you access files using MaxIO, the system will
automatically search through all mounted archives in the search path to find the
requested file. This can be particularly useful for managing game assets, mods, or updates,
where you might want to overlay newer files on top of older ones without altering the
original archive.

Here's an example of how to mount multiple archives to the same search path:


```blitzmax
' Mount the base archive
If Not MaxIO.Mount("base_archive.zip", "/") Then
    Print "Failed to mount base_archive.zip"
    End
End If

' Mount the mod archive
If Not MaxIO.Mount("mod_archive.zip", "/") Then
    Print "Failed to mount mod_archive.zip"
    End
End If

' Mount the update archive
If Not MaxIO.Mount("update_archive.zip", "/") Then
    Print "Failed to mount update_archive.zip"
    End
End If
```

In this example, three archives (`base_archive.zip`, `mod_archive.zip`, and `update_archive.zip`)
are mounted to the root of the search path. When accessing files, MaxIO will search through
these archives, effectively combining their contents into a single file system.

If a file exists in more than one archive, the file from the most recently mounted archive
will take precedence. This allows you to easily overlay new files on top of older ones, making
it easy to manage updates or modifications to your application's assets.

## Thread Safety
MaxIO is mostly thread-safe, with errors returned by `MaxIO.GetLastErrorCode()` being
unique to each thread. However, individual file accesses are not locked, so you cannot
safely read, write, seek, or close the same file from two threads simultaneously.


## Types
| Type | Description |
|---|---|
| [MaxIO](../../brl/brl.io/maxio) | IO abstraction implementation. |

