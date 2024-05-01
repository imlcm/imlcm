
imlcm@conda笔记
---
- [关心的问题](#关心的问题)
  - [conda activate  执行后，会执行哪些操作](#conda-activate--执行后会执行哪些操作)
  - [conda deactivate命令会执行哪些操作](#conda-deactivate命令会执行哪些操作)
  - [小知识：Linux中的PATH是个全局变量吗](#小知识linux中的path是个全局变量吗)
  - [conda是如何隔离不同环境的](#conda是如何隔离不同环境的)
  - [不同环境会重复安装相同的包吗](#不同环境会重复安装相同的包吗)

---


# 关心的问题
## conda activate  执行后，会执行哪些操作
执行`conda activate`命令后，会执行以下操作：

1. **激活环境**：这个命令用于激活一个指定的Conda环境。当环境被激活后，你在该环境下安装的软件包对当前终端会话生效。
2. **修改环境变量**：激活环境会修改`PATH`环境变量，以便终端能够找到当前激活环境中安装的可执行文件。这通常包括将当前环境的`bin`目录添加到`PATH`环境变量的前面。
3. **更新提示符**：在某些情况下，激活环境还会更新终端的提示符，以显示当前激活的环境名称。这有助于用户识别他们当前在哪个Conda环境中工作。

这些操作确保了用户在激活的环境中有正确的软件和工具可用，并且能够在该环境中执行命令和操作。请注意，具体的行为可能会因Conda的版本和配置而有所不同。

---
执行`conda activate`命令后，会激活指定的Conda环境。激活环境主要是指以下两个功能：

1. 将环境的条目添加到PATH中：当环境被激活时，Conda会将该环境的路径添加到系统的PATH环境变量中。这意味着在该环境被激活的状态下，你可以在命令行中直接运行该环境中安装的可执行文件，而无需指定完整的路径。
2. 运行环境可能包含的任何激活脚本：Conda环境可以包含自定义的激活脚本，这些脚本在环境被激活时会自动执行。这些脚本可以执行各种操作，例如设置额外的环境变量、加载特定的库或工具等。通过运行这些脚本，你可以在激活环境时自动配置你的工作环境，以满足你的特定需求。

具体执行的命令和脚本取决于你所激活的Conda环境以及该环境中定义的设置。如果你创建了一个Conda环境，并在其中安装了特定的软件包或设置了自定义的激活脚本，那么在激活该环境时，这些软件包和脚本将会生效。

需要注意的是，激活环境是一个临时的操作，它只影响当前终端会话。当你关闭终端或退出当前会话时，激活的环境将不再生效。如果你需要在其他终端会话或计算机上使用相同的环境配置，你需要重新激活相应的Conda环境。

> 执行`conda activate`命令后，会执行以下操作：

1. **修改环境变量**：`conda activate`命令会修改系统的`PATH`环境变量，将当前激活的Conda环境的`bin`目录添加到`PATH`的前面。这样，当你在命令行中输入命令时，系统会优先在当前激活的环境中查找该命令的可执行文件。
2. **加载环境配置**：除了修改`PATH`环境变量外，`conda activate`还会加载当前激活环境的配置文件，这些文件通常包含一些环境特定的设置，如环境变量、别名、函数等。这些设置可以帮助你定制当前环境的行为和外观。
3. **更新提示符**：在某些情况下，`conda activate`还会更新终端的提示符，以显示当前激活的环境名称。这有助于你识别当前在哪个Conda环境中工作，从而避免在错误的环境中执行命令或安装软件包。

通过执行这些操作，`conda activate`命令确保了你在当前激活的环境中具有正确的软件和工具可用，并且能够在该环境中执行命令和操作。请注意，具体的行为可能会因Conda的版本和配置而有所不同。

> 看下面这个例子，退出conda的lcmConda这个环境后，PATH值立马发生变化。
```
(lcmConda) imlcm@landuse:~/miniconda3/envs$ echo $PATH

/home/imlcm/miniconda3/envs/**lcmConda**/bin:/home/imlcm/miniconda3/condabin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/usr/lib/wsl/lib:/mnt/c/Windows/System32/HWAudioDriverLibs:/mnt/c/WINDOWS/system32:/mnt/c/WINDOWS:/mnt/c/WINDOWS/System32/Wbem:/mnt/c/WINDOWS/System32/WindowsPowerShell/v1.0:/mnt/c/WINDOWS/System32/OpenSSH:/mnt/c/Users/iliving/anaconda3:/mnt/c/Users/iliving/anaconda3/Scripts:/mnt/c/Users/iliving/anaconda3/Library/bin:/mnt/c/Users/iliving/anaconda3/Library/mingw-w64/bin:/mnt/c/Program Files/PuTTY:/mnt/c/WINDOWS/system32/config/systemprofile/AppData/Local/Microsoft/WindowsApps:/mnt/c/WINDOWS/System32/Wbem:/mnt/c/WINDOWS/system32:/mnt/c/WINDOWS:/mnt/c/WINDOWS/System32/WindowsPowerShell/v1.0:/mnt/c/WINDOWS/System32/OpenSSH:/mnt/c/Program Files/Docker/Docker/resources/bin:/mnt/c/Users/imlcm/AppData/Local/Microsoft/WindowsApps:/mnt/c/Users/imlcm/AppData/Local/Programs/Ollama:/mnt/c/Windows/System32:/mnt/c/Users/imlcm:/mnt/c/Users/imlcm/AppData/Local/Programs/Microsoft VS Code/bin:/snap/bin

(lcmConda) imlcm@landuse:~/miniconda3/envs$ exit
logout
There are stopped jobs.

(lcmConda) imlcm@landuse:~/miniconda3/envs$ conda deactivate

(base) imlcm@landuse:~/miniconda3/envs$ echo $PATH

/home/imlcm/**miniconda3**/bin:/home/imlcm/miniconda3/condabin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/usr/lib/wsl/lib:/mnt/c/Windows/System32/HWAudioDriverLibs:/mnt/c/WINDOWS/system32:/mnt/c/WINDOWS:/mnt/c/WINDOWS/System32/Wbem:/mnt/c/WINDOWS/System32/WindowsPowerShell/v1.0:/mnt/c/WINDOWS/System32/OpenSSH:/mnt/c/Users/iliving/anaconda3:/mnt/c/Users/iliving/anaconda3/Scripts:/mnt/c/Users/iliving/anaconda3/Library/bin:/mnt/c/Users/iliving/anaconda3/Library/mingw-w64/bin:/mnt/c/Program Files/PuTTY:/mnt/c/WINDOWS/system32/config/systemprofile/AppData/Local/Microsoft/WindowsApps:/mnt/c/WINDOWS/System32/Wbem:/mnt/c/WINDOWS/system32:/mnt/c/WINDOWS:/mnt/c/WINDOWS/System32/WindowsPowerShell/v1.0:/mnt/c/WINDOWS/System32/OpenSSH:/mnt/c/Program Files/Docker/Docker/resources/bin:/mnt/c/Users/imlcm/AppData/Local/Microsoft/WindowsApps:/mnt/c/Users/imlcm/AppData/Local/Programs/Ollama:/mnt/c/Windows/System32:/mnt/c/Users/imlcm:/mnt/c/Users/imlcm/AppData/Local/Programs/Microsoft VS Code/bin:/snap/bin
```

## conda deactivate命令会执行哪些操作
执行`conda deactivate`命令后，会执行以下操作：

1. **恢复环境变量**：该命令会恢复之前的`PATH`环境变量设置，将当前Conda环境的`bin`目录从`PATH`中移除。这意味着系统将不再在当前环境中查找可执行文件，而是回到之前的环境或系统默认的Python环境中。
2. **卸载环境配置**：除了恢复`PATH`环境变量外，`conda deactivate`还会卸载当前环境的配置，包括任何环境特定的设置、别名、函数等。这些设置将不再对当前终端会话生效。
3. **更新提示符**：在某些情况下，`conda deactivate`还会更新终端的提示符，以显示当前已退出Conda环境。这有助于用户识别当前不在任何Conda环境中工作。

通过执行这些操作，`conda deactivate`命令确保了用户在退出当前Conda环境后，系统环境变量和配置将恢复到之前的状态，从而避免在错误的环境中执行命令或安装软件包。

## 小知识：Linux中的PATH是个全局变量吗

**是的，Linux中的PATH是一个全局环境变量**。它定义了shell在查找可执行文件时应该搜索的目录列表。当你在命令行中输入一个命令时，shell会按照PATH环境变量中定义的目录顺序去查找这个命令的可执行文件。

PATH环境变量通常包含一些标准的目录，如`/usr/bin`、`/bin`等，这些目录包含了系统级别的可执行文件。用户也可以在自己的shell配置文件中（如`.bashrc`、`.bash_profile`等）修改或扩展PATH环境变量，以便包含用户自定义的目录或特定的可执行文件路径。

由于PATH是一个全局环境变量，因此它会影响到所有使用shell的进程。这意味着无论你在哪个目录下执行命令，shell都会按照PATH中定义的目录顺序去查找可执行文件。这使得用户能够方便地在系统的任何位置执行命令，而无需指定完整的路径。

**是的，在shell中不同的程序可以对$PATH环境变量进行修改变动**。PATH环境变量定义了shell在查找可执行文件时应该搜索的目录列表，而任何程序都可以通过修改这个变量来改变搜索路径。

常见的修改PATH的方式包括：

1. **在shell配置文件中修改**：用户可以在自己的shell配置文件中（如`.bashrc`、`.bash_profile`、`.zshrc`等）添加或修改PATH变量的值。这样，每次启动新的shell会话时，都会加载这些配置文件，从而应用对PATH的修改。
2. **在脚本中修改**：脚本文件（如bash脚本）中也可以包含修改PATH的命令。当脚本执行时，它会临时修改当前的PATH环境变量。这种修改只在脚本执行期间有效，脚本执行完毕后，PATH会恢复到之前的状态。
3. **在程序中修改**：某些程序在运行过程中可能需要修改PATH环境变量，以便能够找到特定的可执行文件或库。这些程序可以通过调用系统API或shell命令来修改PATH的值。

需要注意的是，对PATH的修改可能会影响到其他程序或脚本的运行。因此，在修改PATH之前，最好了解清楚修改的影响范围，并确保不会对其他程序造成不必要的干扰。另外，修改系统级别的配置文件（如`/etc/profile`）需要谨慎操作，因为这会影响到所有用户的环境设置。

---

## conda是如何隔离不同环境的

Conda通过创建一个隔离的Python环境来实现不同环境的隔离。以下是conda如何隔离不同环境的关键步骤：

1. **环境创建**：当你使用conda创建一个新的环境时，conda会在你的文件系统中创建一个新的目录来存储该环境的所有文件和配置。这个目录通常位于你的Anaconda或Miniconda安装目录下的`envs`文件夹中。
2. **环境变量修改**：当你激活一个conda环境时，conda会修改你的PATH环境变量，将新环境的bin目录添加到PATH的前面。这意味着，当你运行一个命令时，系统会在新环境的bin目录中查找该命令的可执行文件，而不是在全局Python环境中查找。
3. **软件包安装**：在新激活的环境中，你可以使用conda或pip安装软件包。这些软件包将被安装在新环境的目录中，而不是全局Python环境中。因此，每个环境可以有自己独立的软件包集合和版本。
4. **环境隔离**：由于每个环境都有自己的目录和PATH设置，因此它们之间是相互隔离的。在一个环境中安装的软件包不会影响其他环境，反之亦然。这使得你可以在不同的环境中使用不同的Python版本、软件包版本和配置，而不会相互干扰。
5. **环境退出**：当你退出一个conda环境时，conda会恢复你之前的PATH设置，使你的系统回到全局Python环境或其他之前激活的环境。

通过这些步骤，conda能够实现不同Python环境的隔离，使你能够轻松地在不同的项目中使用不同的Python版本和软件包配置。

## 不同环境会重复安装相同的包吗

在Conda环境中，**不同环境可能会重复安装相同的包**。每个Conda环境都是独立的，因此在一个环境中安装的包不会自动出现在其他环境中。这意味着，如果你在不同的环境中都需要使用某个特定的包，你需要在每个环境中都单独安装它。

然而，值得注意的是，虽然每个环境都有自己的包集合，但Conda会尝试在多个环境之间共享包文件，以减少磁盘空间的占用。这**通过Conda的硬链接功能实现，允许多个环境引用同一个包文件**，而不是为每个环境复制一份完整的包文件。

因此，虽然从逻辑上看，每个环境都有自己的包集合，但实际上在磁盘上，相同的包文件可能会被多个环境共享。这既节省了磁盘空间，又提高了安装和卸载包的速度。
