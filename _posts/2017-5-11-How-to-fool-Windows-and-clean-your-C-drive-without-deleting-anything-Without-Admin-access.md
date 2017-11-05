# How to fool Windows and clean your C drive without deleting anything. Without Admin access.
If you are a Windows user, changes are you have had ran out of space in C drive, at one point or another.

This tutorial will help you clean-up C drive using directory links aka [NTFS junction point](https://en.wikipedia.org/wiki/NTFS_junction_point). This lets Windows an all other programs believe that the specified directory exists but actually it's just a pointer to the new path which can be on another drive. 

It's like a shortcut but better.

## Steps
Without further ado, let's see the code.
1. Cut the folder which is taking up space. e.g.
````
C:\Satish\Some Folder\Another Folder
````
2. Paste it in another drive. e.g.
````
D:\Satish\Not the Some Folder\Not Another Folder
````
3. Run following command in Command Prompt
````
mklink /J "C:\Satish\Some Folder\Another Folder" "D:\Satish\Not the Some Folder\Not Another Folder"
````
That's it. Now when you navigate to ````C:\Satish\Some Folder\Another Folder````, it will actually show contents of "D:\Satish\Not the Some Folder\Not Another Folder", but Windows and other programs will be oblivious about that.

## Limitations
Neither the Windows NT startup process nor the Windows Vista startup process support Junction points, so it's impossible to redirect certain system folders:

* folder containing hiberfil.sys (if it's configured to be outside root directory)
* \Windows
* \Windows\System32
* \Windows\Config

However it is possible to redirect non-critical folders:

  * \Users
  * \Documents and Settings
  * \Program Files
  * \Program Files (x86)

## Warning

* Creating junctions for \Users and \ProgramData pointing to another drive is not recommended as it breaks updates and Windows Store Apps.[[1]]

* Creating junctions for \Users, \ProgramData, "\Program Files" or "\Program Files (x86)" pointing to other locations breaks installation resp. upgrade of Windows.[[2]]

* Creating junctions for "\Program Files" or "\Program Files (x86)" pointing to another drive breaks Windows' Component Based Servicing which hardlinks files from its repository \Windows\SxS to their installation directory.

[1]: https://support.microsoft.com/en-us/help/949977/relocation-of-the-users-directory-and-the-programdata-directory-to-a-d
[2]: https://support.microsoft.com/en-us/help/2876597/error-installing-windows-because-users-or-program-files-folder-redirec
