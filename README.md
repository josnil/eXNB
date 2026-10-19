# eXNB

eXNB is a small command-line tool that loads XNB texture assets (XNA/FNA .xnb files) and exports them to PNG. It' intended for converting game content packed as .xnb into editable image files.

Stack
- 语言(): C# (primary)
- Framework / runtime: .NET Framework 4.8 (classic .csproj / Visual Studio project)
- Notable libraries: FNA (submodule under deps/FNA), Microsoft.Xna.Framework, System.Drawing

What this repo contains
```
.e.g.
.eXNB.sln            - Visual Studio solution
update.bat           - small helper script
deps/                - git submodule pointing to FNA (FNA runtime/source)
src/                 - application project
  eXNB.csproj        - project file (targets .NET Framework v4.8)
  Program.cs         - program entry (creates Game1 and runs it)
  Game1.cs           - main logic: finds .xnb files, converts to PNG
  App.config
  Properties/
    AssemblyInfo.cs
.gitmodules
.gitattributes
.gitignore
```

How it works (runtime shape)
- The app is a minimal XNA/FNA-based program (Game1) that boots a GraphicsDevice, uses Content.Load<Texture2D> to load each .xnb it finds, converts the loaded Texture2D into a System.Drawing.Bitmap, applies a color-fix pass, then writes a .png next to the original file (same path, .png extension). File discovery is recursive starting at the input directory.

Requirements
- .NET Framework 4.8 developer/targeting pack (to build the project)
- Visual Studio (recommended) or MSBuild capable of building .NET Framework projects
- Git with submodule support (deps/FNA must be initialized)
- The FNA submodule must be present and buildable (the project references deps/FNA/FNA.csproj)

Build and run (shortest path)
1. 克隆 and init submodules:
   git clone https://github.com/BttrDrgn/eXNB.git
   cd eXNB
   git submodule update --init --recursive

2. Build
   - Recommended: open eXNB.sln in Visual Studio (2019/2022), set Configuration to Release, build solution.
   - CLI with MSBuild:
     msbuild eXNB.sln /p:Configuration=Release

   After a successful build the executable will be under src\bin\<Configuration>\ (e.g. src\bin\Release\eXNB.exe or platform-specific bin folders)

3. Run
   - Basic usage:
     src\bin\Release\eXNB.exe -i "Path\To\Content" -o "Path\To\Output"

   - Notes:
     - If -i is not provided, the default input directory is "Content".
     - If -o is not provided, the default output directory is "Content".
     - The tool will recursively search the input directory for *.xnb files and write corresponding .png files next to each .xnb (replacing the .xnb extension with .png).
     - The program logs progress and errors to the console and exits when finished.

Examples
- Convert a folder called MyGameContent and output PNGs to MyPngs:
  src\bin\Release\eXNB.exe -i "C:\Projects\MyGame\Content" -o "C:\Projects\MyGame\PngOutput"

Behavior and limitations
- The tool uses the Content.Load<Texture2D> path for loading .xnb files — it expects XNB assets that FNA/XNA can load as Texture2D.
- Colors are adjusted by reading pixels into a Bitmap and re-creating a Texture2D; this may be slow for very large texture sets.
- The project references the FNA submodule; ensure that submodule is present and builds correctly for your platform.
- No explicit license is included in the repository — add a LICENSE file if you want to clarify reuse terms.

Contributing
- If you want to improve or extend the tool (e.g., add command-line flags for overwrite behavior, parallel processing, different output formats), fork and open a PR. Make sure to keep deps/FNA submodule handling consistent.

Try asking
- Can eXNB safely handle non-texture XNBs (models or other content), or should it only be used for texture assets? — see src/Game1.cs where Content.Load<Texture2D> is used.
- How should I modify the project to write output PNGs to a separate folder tree instead of next to the .xnb files? — check Game1.cs file paths and the GetArgument defaults.
- I see deps/FNA in .gitmodules — what build steps are needed for FNA on my OS (Windows/Linux/macOS) so the project links and runs correctly?

If you want, I can produce a ready-to-commit README.md file (with this content) or expand the Usage/Build sections with platform-specific troubleshooting steps.
