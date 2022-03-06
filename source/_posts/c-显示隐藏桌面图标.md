---
title: c++显示隐藏桌面图标
date: 2022-03-06 13:49:57
tags:
    - C/C++
toc: true
categories: C/C++
---

使用SHGetSetSettings死活没用，后来在StackOverflow发现一份隐藏桌面图标的代码。

```c++
// Copy this code from: https://stackoverflow.com/questions/53310374/how-to-hide-desktop-icons-with-windows-api-in-c

#include <ShlObj.h>     // Shell API
#include <atlcomcli.h>  // CComPtr & Co.
#include <string> 
#include <iostream> 
#include <system_error>
#include<Windows.h>


// Throw a std::system_error if the HRESULT indicates failure.
template< typename T >
void ThrowIfFailed(HRESULT hr, T&& msg)
{
    if (FAILED(hr))
        throw std::system_error{ hr, std::system_category(), std::forward<T>(msg) };
}

// RAII wrapper to initialize/uninitialize COM
struct CComInit
{
    CComInit() { ThrowIfFailed(::CoInitialize(nullptr), "CoInitialize failed"); }
    ~CComInit() { ::CoUninitialize(); }
    CComInit(CComInit const&) = delete;
    CComInit& operator=(CComInit const&) = delete;
};

// Query an interface from the desktop shell view.
void FindDesktopFolderView(REFIID riid, void** ppv, std::string const& interfaceName)
{
    CComPtr<IShellWindows> spShellWindows;
    ThrowIfFailed(
        spShellWindows.CoCreateInstance(CLSID_ShellWindows),
        "Failed to create IShellWindows instance");

    CComVariant vtLoc(CSIDL_DESKTOP);
    CComVariant vtEmpty;
    long lhwnd;
    CComPtr<IDispatch> spdisp;
    ThrowIfFailed(
        spShellWindows->FindWindowSW(
            &vtLoc, &vtEmpty, SWC_DESKTOP, &lhwnd, SWFO_NEEDDISPATCH, &spdisp),
        "Failed to find desktop window");

    CComQIPtr<IServiceProvider> spProv(spdisp);
    if (!spProv)
        ThrowIfFailed(E_NOINTERFACE, "Failed to get IServiceProvider interface for desktop");

    CComPtr<IShellBrowser> spBrowser;
    ThrowIfFailed(
        spProv->QueryService(SID_STopLevelBrowser, IID_PPV_ARGS(&spBrowser)),
        "Failed to get IShellBrowser for desktop");

    CComPtr<IShellView> spView;
    ThrowIfFailed(
        spBrowser->QueryActiveShellView(&spView),
        "Failed to query IShellView for desktop");

    ThrowIfFailed(
        spView->QueryInterface(riid, ppv),
        "Could not query desktop IShellView for interface " + interfaceName);
}

void ToggleDesktopIcons()
{
    CComPtr<IFolderView2> spView;
    FindDesktopFolderView(IID_PPV_ARGS(&spView), "IFolderView2");

    DWORD flags = 0;
    ThrowIfFailed(
        spView->GetCurrentFolderFlags(&flags),
        "GetCurrentFolderFlags failed");
    ThrowIfFailed(
        spView->SetCurrentFolderFlags(FWF_NOICONS, flags ^ FWF_NOICONS),
        "SetCurrentFolderFlags failed");
}

int wmain(int argc, wchar_t** argv)
{
    try
    {
        CComInit init;
            
        ToggleDesktopIcons();

        std::cout << "Desktop icons have been toggled.\n";
    }
    catch (std::system_error const& e)
    {
        std::cout << "ERROR: " << e.what() << ", error code: " << e.code() << "\n";
        return 1;
    }

    return 0;
}
```