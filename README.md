# Muushig
path: C:\builtfiles\dnSpy-net472.zip
      - uses: actions/upload-artifact@v1
        if: true
        with:
          name: dnSpy-netcore-win32
          path: C:\builtfiles\dnSpy-netcore-win32.zip
      - uses: actions/upload-artifact@v1
        if: true
        with:
          name: dnSpy-netcore-win64
          path: C:\builtfiles\dnSpy-netcore-win64.zip
      # Create a new release: create a tag, then 'git push origin <tagname>', wait for it to build
      - name: Create a new GitHub release if a new tag is pushed
        uses: softprops/action-gh-release@b28d815 #@v1
        with:
          files: |
            C:\builtfiles\dnSpy-net472.zip
            C:\builtfiles\dnSpy-netcore-win32.zip
            C:\builtfiles\dnSpy-netcore-win64.zip
          body: |
            ### Consider [❤️ donating](https://github.com/sponsors/0xd4d) to support this project if dnSpy helped you!
            
            
            Downloads below or [build](https://github.com/0xd4d/dnSpy/blob/master/README.md#building) it yourself
            
            If your antivirus software complains, it's a false positive and you should contact them. I can't fix their buggy software.
            
            `dnSpy-net472.zip` requires .NET Framework 4.7.2 or later.
            
            `dnSpy-netcore-win32.zip` and `dnSpy-netcore-win64.zip` don't depend on .NET Framework, but use .NET Core instead which is included in the zip files. If `dnSpy.exe` fails to start, try:
 13  DnSpyCommon.props 
@@ -25,9 +25,9 @@
    <SatelliteResourceLanguages>cs;de;es;es-ES;fa;fr;hu;it;pt-BR;pt-PT;ru;tr;uk;zh-CN</SatelliteResourceLanguages>

    <!-- Update app.config whenever this value changes -->
    <DnSpyAssemblyVersion>6.1.5.0</DnSpyAssemblyVersion>
    <DnSpyAssemblyVersion>6.1.7.0</DnSpyAssemblyVersion>
    <!-- This is shown in the title bar -->
    <DnSpyAssemblyInformationalVersion>v</DnSpyAssemblyInformationalVersion>
    <DnSpyAssemblyInformationalVersion>v6.1.7</DnSpyAssemblyInformationalVersion>
    <DnSpyAssemblyCopyright>Copyright (C) 2014-2020 de4dot@gmail.com</DnSpyAssemblyCopyright>

    <!-- AD0001: buggy Roslyn analyzer(s) crash, disable that warning -->
@@ -37,7 +37,7 @@
    <!-- Update app.config whenever some of these versions change (eg. dnlib version) -->
    <DiaSymReaderVersion>1.7.0</DiaSymReaderVersion>
    <DnlibVersion>3.3.2</DnlibVersion>
    <IcedVersion>1.7.0</IcedVersion>
    <IcedVersion>1.8.0</IcedVersion>
    <MSBuildNuGetVersion>16.5.0</MSBuildNuGetVersion>
    <MSDiagRuntimeVersion>1.1.126102</MSDiagRuntimeVersion>
    <MSVSCompositionVersion>16.4.11</MSVSCompositionVersion>
@@ -51,13 +51,6 @@

  <Import Project="$(MSBuildThisFileDirectory)Build\ConvertToNetstandardReferences\ConvertToNetstandardReferences.tasks" Condition=" '$(IsDotNetCore)' == 'true' " />

  <PropertyGroup Condition=" '$(Configuration)' == 'Debug' ">
    <DefineConstants>$(DefineConstants);DEBUG;TRACE</DefineConstants>
  </PropertyGroup>
  <PropertyGroup Condition=" '$(Configuration)' == 'Release' ">
    <DefineConstants>$(DefineConstants);TRACE</DefineConstants>
  </PropertyGroup>

  <!-- .NET Core 3.0 SDK doesn't currently support COMReference: https://github.com/0xd4d/dnSpy/issues/1053 -->
  <PropertyGroup>
    <HasCOMReference>false</HasCOMReference>
 2  ...sions/dnSpy.Debugger/dnSpy.Debugger.DotNet.CorDebug/Impl/Evaluation/DbgDotNetValueImpl.cs 
@@ -377,7 +377,9 @@ enum ValueFlags : byte {
					offsetToStringData = IntPtr.Size == 4 ? OffsetToStringData32_CLR2 : OffsetToStringData64_CLR2;
				else {
					offsetToStringData = IntPtr.Size == 4 ? OffsetToStringData32 : OffsetToStringData64;
#pragma warning disable CS0618
					Debug.Assert(offsetToStringData == RuntimeHelpers.OffsetToStringData);
#pragma warning restore CS0618
				}
				uint stringLength = v.StringLength;
				Debug.Assert((ulong)offsetToStringData + stringLength * 2 <= size);
 2  Extensions/dnSpy.Debugger/dnSpy.Debugger.DotNet.Mono/Impl/ObjectConstants.cs 
@@ -65,7 +65,9 @@ namespace dnSpy.Debugger.DotNet.Mono.Impl {
			var type = thread.Domain.Corlib.GetType("System.Runtime.CompilerServices.RuntimeHelpers");
			if (type is null)
				return null;
#pragma warning disable CS0618
			var method = type.GetMethod("get_" + nameof(System.Runtime.CompilerServices.RuntimeHelpers.OffsetToStringData));
#pragma warning restore CS0618
			if (method is null)
				return null;
			var res = Call(method, Array.Empty<Value>());
