# Windows z-order bands

### Subjects
- About bands
- Getting started
- Usage examples
- External links

# About bands
Windows 11 and 10 have multiple bands for z-order, the system is using these bands to create windows with higher z-orders . ZBID_DESKTOP is the band for regular user-created window (CreateWindow) is pushed into but there are many more bands that system tools use. Task manager in Windows 11 has a top most option which uses ZBID_SYSTEM_TOOLS band and as we can see in ZBID enum, it has a higher index so it can easily draw above desktop windows. 
Gaining access to bands gives us permission to use the any z-order we need including the highest one possible (ZBID_UIACCESS), this already sounds nice.

# Getting started
To use a higher z-order band other than ZBID_DESKTOP, your process requires a valid TokenUIAccess token attribute (check CreateProcessAsUserA). 
Processes with a valid token can call successfully the private functions SetWindowBand and CreateWindowInBand from user32.dll (their behavior might change in future Windows updates). 

```
enum ZBID
{
     ZBID_DEFAULT = 0,
     ZBID_DESKTOP = 1,
     ZBID_UIACCESS = 2,
     ZBID_IMMERSIVE_IHM = 3,
     ZBID_IMMERSIVE_NOTIFICATION = 4,
     ZBID_IMMERSIVE_APPCHROME = 5,
     ZBID_IMMERSIVE_MOGO = 6,
     ZBID_IMMERSIVE_EDGY = 7,
     ZBID_IMMERSIVE_INACTIVEMOBODY = 8,
     ZBID_IMMERSIVE_INACTIVEDOCK = 9,
     ZBID_IMMERSIVE_ACTIVEMOBODY = 10,
     ZBID_IMMERSIVE_ACTIVEDOCK = 11,
     ZBID_IMMERSIVE_BACKGROUND = 12,
     ZBID_IMMERSIVE_SEARCH = 13,
     ZBID_GENUINE_WINDOWS = 14,
     ZBID_IMMERSIVE_RESTRICTED = 15,
     ZBID_SYSTEM_TOOLS = 16,
 
     //Windows 10+
     ZBID_LOCK = 17,
     ZBID_ABOVELOCK_UX = 18
};
```
```
BOOL WINAPI SetWindowBand(
     HWND hWnd, 
     HWND hwndInsertAfter, 
     DWORD dwBand);

HWND WINAPI CreateWindowInBand(
     DWORD dwExStyle,
     LPCWSTR lpClassName,
     LPCWSTR lpWindowName,
     DWORD dwStyle,
     int x,
     int y,
     int nWidth,
     int nHeight,
     HWND hWndParent,
     HMENU hMenu,
     HINSTANCE hInstance,
     LPVOID lpParam,
     DWORD dwBand);
```
How can you get a TokenUIAccess token? I found two methods:
1. Requires injecting a dll into an immersive process and using CreateWindowInBand (https://gist.github.com/ADeltaX/a0b5366f91df26c5fa2aeadf439346c9)
2. (better) Uses the token from winlogon.exe to create a new token, and from here you are free to use it. (https://github.com/killtimer0/uiaccess)

# Usage examples

### Overlays
A fullscreen window is in most of the cases the only window visible but that's not mandatory. We can draw as many windows as we want above it using a higher z-order band without interacting with the fullscreen program as many overlays do (DLL injection).
### Monitoring
Monitoring data can have improved focus since it's possible to chose what windows (including system windows of course) can block the vision of your program.
### Notification systems
High priority notifications can be visible over lock screen.
### ATMs
Restaurant self-service kiosks using ATMs are vulnerable to accessing system interface such as Start menu, using a higher z-order can avoid any unwanted access.


# External links:
Thank you a lot!

https://blog.adeltax.com/window-z-order-in-windows-10/

https://github.com/killtimer0/uiaccess


