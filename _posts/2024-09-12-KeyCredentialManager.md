---
title: Implementing Windows Hello - a surprising obstacle
date: 2024-09-21 14:00:00 +/-0000
categories: [C++]
tags: [coding, cli, devops]     # TAG names should always be lowercase
---
As the next step on my journey to add missing features to the **Cryptomator** app, I decided to implement **Windows Hello** support.

Recently, I added a similar feature for the Mac variant of **Cryptomator**: Touch ID. You can decide in the preferences for every vault, that a user needs to authenticate with Touch ID to access the vault. This prevents access on an unlocked system by people not owning the system. Windows Hello provides the same functionality on Windows.

Browsing the Microsoft documentation leads to a `KeyCredentialManager` object, that can be used to implement the **Windows Hello** functionality. This object is part of the `Windows.Security.Credentials` namespace, which itself is part of the Windows Runtime. As the native code within the `integrations-win` part of **Cryptomator** is written in C++, the C++/WinRT library is the way to go. And that’s what the 
Microsoft documentation also tells you.

That’s where my problems started.

Writing the code, you’ll have to include the required headers.
```cpp
#include <winrt/Windows.Security.Credentials.h>

using namespace winrt;
using namespace winrt::Windows::Security::Credentials;
```

It's natural, that you install the Windows SDK to have the required headers. But compiling fails, due to a missing `vcruntime.h`. What the heck? A header that comes only with a Visual Studio installation?

That was not, what I wanted. I want to compile the **Cryptomator** source code on the command line and it must be possible to compile the code on CI too.

This took me a while, as all the notes and hints I found on the web lead to: you need to install Visual Studio. Besides, I never used Visual Studio and I am not willing to learn another IDE.

Putting the missing header aside, there wasn't any documentation available on the web on how to compile C++/WinRT on the command line. The best choice seemed to be `MinGW-w64`, a project that provides the GCC compiler and related tools for Windows systems.

Well, until I found the solution: the Buildtools for Visual Studio 2022. These contain the required compiler and linker for the command line and support C++/WinRT desktop development as well.
