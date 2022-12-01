
---
title: A.1 - 静态库和动态库
alias: A.1 - 静态库和动态库
origin: /a1-static-and-dynamic-libraries/
origin_title: "A.1 — Static and dynamic libraries"
time: 2022-9-13
type: translation
---


??? note "Key Takeaway"
	

**库**是一个代码包，它可以被许多程序重用。通常，C++库分为两部分：

1. 定义公开接口(提供功能)的头文件。
2. 一种预编译的二进制文件，其中包含预编译成机器语言的功能的实现。

有些库可能被拆分为多个文件和/或具有多个头文件。

库被预编译有几个原因。首先，由于库很少更改，所以不需要经常重新编译。每次编写使用库的程序时重新编译库是浪费时间的。其次，因为预编译对象是用机器语言编写的，所以它阻止了人们访问或更改源代码，这对于企业或因为知识产权原因不想让源代码可用的人来说很重要。

库可以分为两类：静态库和动态库

**静态库**(也称为**存档 archive**)由编译后直接链接到程序中程序组成。当编译使用静态库的程序时，程序使用的静态库的所有功能都成为可执行文件的一部分。在Windows上，静态库通常有一个`.lib`扩展名，而在Linux上，静态库通常有一个`.a`(存档)扩展名。静态库的一个优点是，让用户运行你的程序，只需分发可执行文件。因为库成为了程序的一部分，这就确保了程序总是使用正确版本的库。另外，由于静态库成为程序的一部分，你可以像为自己的程序编写的功能一样使用它们。缺点是，由于库的副本成为每个使用它的可执行文件的一部分，这可能导致大量的空间浪费。静态库也不容易升级——要更新库，需要替换整个可执行文件。

动态库(也称为**共享库 shared library**)由在运行时加载到应用程序中的程序组成。当编译一个使用动态库的程序时，库不会成为可执行程序的一部分——它仍然是一个独立的单元。在Windows上，动态库通常具有`.dll`(动态链接库)扩展名，而在Linux上，动态库通常具有`.so`(共享对象)扩展名。动态库的一个优点是许多程序可以共享一个副本，这节省了空间。也许更大的优点是动态库可以升级到新版本，而不需要替换使用它的所有可执行程序。

因为动态库没有链接到程序中，所以使用动态库的程序必须显式加载动态库并与之连接。这种机制可能令人困惑，并使与动态库的接口变得尴尬。为了使动态库更容易使用，可以使用导入库。

**导入库**是一个自动加载和使用**动态库**的库。在Windows上，这通常是通过与动态库(`.dll`)同名的小型静态库(`.lib`)来完成的。在编译时将静态库链接到程序中，然后就可以像使用静态库一样有效地使用动态库的功能。在Linux上，共享对象(`.so`)文件既是动态库又是导入库。==大多数链接器可以在创建动态库时为动态库构建导入库==。

## 安装和使用库

现在你已经了解了不同类型的库，让我们讨论一下如何在程序中实际使用库。在C++中安装库通常包括4个步骤：

1. 获得库最好的方式是为你的操作系统下载预编译的包(如果它存在的话)，这样我们就不必自己编译库。如果没有，那么我们将不得不下载一个源代码包并自己编译它(这超出了本课的范围)。在Windows上，库通常以`.zip`文件的形式分发。在Linux上，库通常以包的形式分发(例如`. rpm`)。包管理器可能已经列出了一些更流行的库(例如SDL)，以便于安装，所以请先检查这些库。
2. 安装库。在Linux上，通常使用包管理器完成所有工作。在Windows上，这通常涉及将库解压缩到你所选择的目录。我们建议将所有的库放在一个位置以便于访问。例如，使用一个名为`C:\Libs`的目录，并将每个库放在它自己的子目录中。
3. 确保编译器知道在哪里查找库的头文件。在Windows上，通常这是你安装库文件的目录的include子目录(例如，如果你把库安装到`C:\libs\SDL-1.2.11`，头文件可能在`C:\libs\SDL-1.2.11\include`)。在Linux上，头文件通常安装到`/usr/include`，它应该已经是包含文件搜索路径的一部分。但是，如果文件安装在其他地方，则必须告诉编译器在哪里找到它们。
5.  Tell the linker where to look for the library file(s). As with step 3, this typically involves adding a directory to the list of places the linker looks for libraries. On Windows, this is typically the /lib subdirectory of the directory you installed the library files to. On Linux, libraries are typically installed to /usr/lib, which should already be a part of your library search path.

Once the library is installed and the IDE knows where to look for it, the following 3 steps typically need to be performed for each project that wants to use the library:

5.  If using static libraries or import libraries, tell the linker which library files to link.
6.  #include the library’s header file(s) in your program. This tells the compiler about all of the functionality the library is offering so that your program will compile properly.
7.  If using dynamic libraries, make sure the program knows where to find them. Under Linux, libraries are typically installed to /usr/lib, which is in the default search path after the paths in the `LD_LIBRARY_PATH` environment variable. On Windows, the default search path includes the directory the program is run from, directories set by calling SetDllDirectory(), the Windows, System, and System32 directories, and directories in the PATH environment variable. The easiest way to use a .dll is to copy the .dll to the location of the executable. Since you’ll typically distribute the .dll with your executable, it makes sense to keep them together anyway.

Steps 3-5 involve configuring your IDE -- fortunately, almost all IDEs work the same way when it comes to doing these things. Unfortunately, because each IDE has a different interface, the most difficult part of this process is simply locating _where_ the proper place to perform each of these steps is. Consequently, in the next few lessons in this section, we’ll cover how to do all of these steps for both Visual Studio Express 2005 and Code::Blocks. If you are using another IDE, read both -- by the time you’re done, you should have enough information to do the same with your own IDE with a little searching.