CamelCrusher 1.0.1 (Mac) - Patched for macOS 10.14 Mojave and later
===================================================================

WHAT THIS FIXES
On macOS 10.14+ CamelCrusher loads and processes audio, and the knobs respond
to the mouse, but the editor window is blank. The plug-in's GUI library
(VSTGUI 3.6) calls [self lockFocus] inside its NSView drawRect:. On the
layer-backed views every modern host uses, that redirects all drawing to an
off-screen context that never reaches the screen. The patch removes those two
calls so the GUI draws normally again. Nothing else is changed.

  CamelCrusher.component  Audio Unit, 64-bit + 32-bit (both patched)
  CamelCrusher.vst        VST 2, 64-bit (patched) + 32-bit (unchanged:
                          it uses the older Carbon UI, which isn't affected)
  CamelCrusherData        Presets, skin graphics and manual (required)


OPTION 1 - INSTALLER
Double-click "Install CamelCrusher.pkg".
The package isn't signed by a registered Apple developer, so macOS will
block it the first time. Go to System Settings > Privacy & Security, scroll
down, click "Open Anyway", then run it again.


OPTION 2 - MANUAL INSTALL
1. Copy CamelCrusher.component to   /Library/Audio/Plug-Ins/Components/
2. Copy CamelCrusher.vst to         /Library/Audio/Plug-Ins/VST/
3. Copy the CamelCrusherData folder to
                                    /Library/Application Support/Camel Audio/
   (create the "Camel Audio" folder if it doesn't exist; the path must be
    exactly /Library/Application Support/Camel Audio/CamelCrusherData)
   Use the main /Library folder, NOT ~/Library in your home folder.
   Without this folder the GUI is blank even with the patch.
4. Open Terminal and run these lines so macOS lets the plug-ins load:

   sudo xattr -cr /Library/Audio/Plug-Ins/Components/CamelCrusher.component /Library/Audio/Plug-Ins/VST/CamelCrusher.vst
   sudo codesign --force --deep -s - /Library/Audio/Plug-Ins/Components/CamelCrusher.component
   sudo codesign --force --deep -s - /Library/Audio/Plug-Ins/VST/CamelCrusher.vst
   killall -9 AudioComponentRegistrar

5. Restart your DAW and rescan plug-ins.


APPLE SILICON (M1/M2/M3/M4) MACS
CamelCrusher is Intel-only. Logic Pro and GarageBand load it automatically
(through Rosetta). For other DAWs, select the app in Finder, choose
File > Get Info, and tick "Open using Rosetta". Rosetta must be installed.


CHECKING THE AUDIO UNIT
   auval -v aumf CaCr CamA
(on Apple Silicon:  arch -x86_64 auval -v aumf CaCr CamA)


PATCH DETAILS
AU  x86_64  slice offsets 0x6900d, 0x690fb   ff15xxxxxxxx -> 660f1f440000
AU  i386    slice offsets 0x7798a, 0x77aa3   e8xxxxxxxx   -> 0f1f440000
VST x86_64  slice offsets 0x5486d, 0x5495b   ff15xxxxxxxx -> 660f1f440000
The PowerPC slices were removed.
CamelCrusher was freeware by Camel Audio. This is an unofficial
compatibility patch and is not affiliated with Camel Audio or Apple.
