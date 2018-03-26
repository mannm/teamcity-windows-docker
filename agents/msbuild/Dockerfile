FROM teamcity-minimal-agent:latest-windowsservercore-1709 AS tools

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

# Install Git
RUN Invoke-WebRequest https://github.com/git-for-windows/git/releases/download/v2.15.1.windows.2/MinGit-2.15.1.2-64-bit.zip -OutFile git.zip; \
    Expand-Archive git.zip -DestinationPath $Env:ProgramFiles\Git ; \
    Remove-Item -Force git.zip

# Install Open JDK
RUN Invoke-WebRequest https://github.com/ojdkbuild/ojdkbuild/releases/download/1.8.0.151-1/java-1.8.0-openjdk-1.8.0.151-1.b12.ojdkbuild.windows.x86_64.zip -OutFile openjdk.zip; \
    Expand-Archive openjdk.zip -DestinationPath $Env:ProgramFiles\Java; \
    Get-ChildItem -Path $Env:ProgramFiles\Java -Filter "java-*-openjdk*" | ForEach-Object {$_ | Rename-Item -NewName "OpenJDK" }; \
	Remove-Item -Force $Env:ProgramFiles\Java\OpenJDK\src.zip; \
    Remove-Item -Force openjdk.zip

FROM microsoft/dotnet-framework-build:4.7.1-windowsservercore-1709

ENV CONFIG_FILE="C:/BuildAgent/conf/buildAgent.properties" \
    JRE_HOME="C:\Program Files\Java\Oracle\jre" \
    NUGET_XMLDOC_MODE=skip \
    DOTNET_CLI_TELEMETRY_OPTOUT=true \
    DOTNET_SKIP_FIRST_TIME_EXPERIENCE=true

# Note: Add NuGet
RUN Invoke-WebRequest "https://dist.nuget.org/win-x86-commandline/latest/nuget.exe" -OutFile "C:\windows\nuget.exe" -UseBasicParsing

EXPOSE 9090

VOLUME C:/BuildAgent/conf

COPY --from=tools /BuildAgent /BuildAgent
COPY --from=tools ["C:/Program Files/Git", "C:/Program Files/Git"]
COPY --from=tools ["C:/Program Files/Java/Oracle", "C:/Program Files/Java/Oracle"]
COPY --from=tools ["C:/Program Files/Java/OpenJDK", "C:/Program Files/Java/OpenJDK"]

RUN setx /M PATH ('{0};{1}\bin;C:\Program Files\Git\cmd' -f $env:PATH, $env:JRE_HOME)

CMD ./BuildAgent/run-agent.ps1