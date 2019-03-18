News
===========

 [This project](https://github.com/codejq/EPPlus.Core/) is a branch of  [This project](https://github.com/VahidN/EPPlus.Core/issues/37) helped EPPlus to add .NET Core support. due the fact that the performance  of System.Drawing.Common  non-Windows operating systems is very poor, so i mantian this brance for  for project still using CoreCompat.System.Drawing which targets  non-Windows operating systems

---------------------------------

EPPlus.Core.CoreCompat
===========

`EPPlus.Core.CoreCompat` is an **unofficial** port of the [EPPlus library](http://epplus.codeplex.com) to .NET Core.
It's based on the [5/24/2017, change set#fcded570d92e](http://epplus.codeplex.com/SourceControl/list/changesets).


Install via NuGet
-----------------

To install EPPlus.Core.CoreCompat, run the following command in the Package Manager Console:

```
PM> Install-Package EPPlus.Core.CoreCompat
```

You can also view the [package page](http://www.nuget.org/packages/EPPlus.Core.CoreCompat/) on NuGet.


Usage
------

- [A sample ASP.NET Core App](/src/EPPlus.Core.SampleWebApp)


Notes:
-----------------

To run this project on non-Windows-based operating systems, you will need to install `libgdiplus` too:
- Ubuntu 16.04 and above:
	- apt-get install libgdiplus
	- cd /usr/lib
	- ln -s libgdiplus.so gdiplus.dll
- Fedora 23 and above:
	- dnf install libgdiplus
	- cd /usr/lib64/
	- ln -s libgdiplus.so.0 gdiplus.dll
- CentOS 7 and above:
	- yum install autoconf automake libtool
	- yum install freetype-devel fontconfig libXft-devel
	- yum install libjpeg-turbo-devel libpng-devel giflib-devel libtiff-devel libexif-devel
	- yum install glib2-devel cairo-devel
	- git clone https://github.com/mono/libgdiplus
	- cd libgdiplus
	- ./autogen.sh
	- make
	- make install
	- cd /usr/lib64/
	- ln -s /usr/local/lib/libgdiplus.so gdiplus.dll
- Docker
	- RUN apt-get update \\

      && apt-get install -y libgdiplus
- MacOS
	- brew install mono-libgdiplus

      After installing the [Mono MDK](http://www.mono-project.com/download/#download-mac), Copy Mono MDK Files:
	   - /Library/Frameworks/Mono.framework/Versions/4.6.2/lib/libgdiplus.0.dylib
	   - /Library/Frameworks/Mono.framework/Versions/4.6.2/lib/libgdiplus.0.dylib.dSYM
	   - /Library/Frameworks/Mono.framework/Versions/4.6.2/lib/libgdiplus.dylib
	   - /Library/Frameworks/Mono.framework/Versions/4.6.2/lib/libgdiplus.la

      And paste them to: /usr/local/lib


The original EPPlus project has been [moved to Github](https://github.com/JanKallman/EPPlus/). Please post its development related issues/pull requests at there. The main purpose of the current project is just providing an existing EPPlus for .NET Standard. There will be no further developments here.
