# C# (.NET Core 2.2)

## Summary

*Develop C# and .NET Core 2.2 based applications. Includes all needed SDKs, extensions, and dependencies.*

| Metadata | Value |  
|----------|-------|
| *Contributors* | The VS Code Team |
| *Definition type* | Dockerfile |
| *Languages, platforms* | .NET Core, C# |

## Using this definition with an existing folder

While the definition itself works unmodified, there are some tips that can help you deal with some of the defaults .NET Core uses.

### Using appPort with ASP.NET Core

By default, ASP.NET Core only listens to localhost. If you use the `appPort` property in `.devcontainer/devcontainer.json`, the port is [published](https://docs.docker.com/config/containers/container-networking/#published-ports) rather than forwarded. Unfortunately, means that ASP.NET Core only listens to localhost is inside the container itself. It needs to listen to `*` or `0.0.0.0` for the application to be accessible externally.

This container solves that problem by setting the environment variable `ASPNETCORE_Kestrel__Endpoints__Http__Url` to `http://*:5000` in `.devcontainer/devcontainer.json`. Using an environment variable to override this setting in the container only, which allows you to leave your actual application config as-is for use when running locally.

```json
"appPort": [5000, 5001],
"runArgs": [
    "-e", "ASPNETCORE_Kestrel__Endpoints__Http__Url=http://*:5000"
]
```

If you've already opened your folder in a container, rebuild the container using the **Remote-Containers: Rebuild Container** command from the Command Palette (<kbd>F1</kbd>) so the settings take effect.

### Enabling HTTPS in ASP.NET Core

To enable HTTPS in ASP.NET, you can mount an exported copy of your local dev certificate. First, export it using the following command:

**Windows PowerShell**

```powershell
dotnet dev-certs https --trust; dotnet dev-certs https -ep "$env:USERPROFILE/.aspnet/https/aspnetapp.pfx" -p "SecurePwdGoesHere"
```

**macOS/Linux terminal**

```powershell
dotnet dev-certs https --trust; dotnet dev-certs https -ep "${HOME}/.aspnet/https/aspnetapp.pfx" -p "SecurePwdGoesHere"
```

Next, add the following in the `runArgs` array in `.devcontainer/devcontainer.json` (assuming port 5000 and 5001 are the correct ports):

```json
"appPort": [5000, 5001],
"runArgs": [
    "-e", "ASPNETCORE_Kestrel__Endpoints__Http__Url=http://*:5000",
    "-e", "ASPNETCORE_Kestrel__Endpoints__Https__Url=https://*:5001",
    "-v", "${env:HOME}${env:USERPROFILE}/.aspnet/https:/home/vscode/.aspnet/https",
    "-e", "ASPNETCORE_Kestrel__Certificates__Default__Password=SecurePwdGoesHere",
    "-e", "ASPNETCORE_Kestrel__Certificates__Default__Path=/home/vscode/.aspnet/https/aspnetapp.pfx"
]
```

If you've already opened your folder in a container, rebuild the container using the **Remote-Containers: Rebuild Container** command from the Command Palette (<kbd>F1</kbd>) so the settings take effect.

### Debug Configuration

Only the integrated terminal is supported by the Remote - Containers extension. You may need to modify `launch.json` configurations to include the following value if an external console is used.

```json
"console": "integratedTerminal"
```

### Installing Node.js or the Azure CLI

Given how frequently ASP.NET applications use Node.js for front end code, this container also includes Node.js. You can change the version of Node.js installed or disable its installation by updating these lines in `.devcontainer/Dockerfile`.

```Dockerfile
ARG INSTALL_NODE="true"
ARG NODE_VERSION="10"
```

If you would like to install the Azure CLI update this line in `.devcontainer/Dockerfile`:

```Dockerfile
ARG INSTALL_AZURE_CLI="true"
```

If you've already opened your folder in a container, rebuild the container using the **Remote-Containers: Rebuild Container** command from the Command Palette (<kbd>F1</kbd>) so the settings take effect.

### Adding the definition to your folder

1. If this is your first time using a development container, please follow the [getting started steps](https://aka.ms/vscode-remote/containers/getting-started) to set up your machine.

2. To use VS Code's copy of this definition:
   1. Start VS Code and open your project folder.
   2. Press <kbd>F1</kbd> select and **Remote-Containers: Add Development Container Configuration Files...** from the command palette.
   3. Select the C# (.NET Core 2.2) definition.

3. To use latest-and-greatest copy of this definition from the repository:
   1. Clone this repository.
   2. Copy the contents of `containers/dotnetcore-2.2/.devcontainer` to the root of your project folder.
   3. Start VS Code and open your project folder.

4. After following step 2 or 3, the contents of the `.devcontainer` folder in your project can be adapted to meet your needs.

5. Finally, press <kbd>F1</kbd> and run **Remote-Containers: Reopen Folder in Container** to start using the definition.

## Testing the definition

This definition includes some test code that will help you verify it is working as expected on your system. Follow these steps:

1. If this is your first time using a development container, please follow the [getting started steps](https://aka.ms/vscode-remote/containers/getting-started) to set up your machine.
2. Clone this repository.
3. Start VS Code, press <kbd>F1</kbd>, and select **Remote-Containers: Open Folder in Container...**
4. Select the `containers/dotnetcore-2.2` folder.
5. After the folder has opened in the container, if prompted to restore packages in a notification, click "Restore".
6. After packages are restored, press <kbd>F5</kbd> to start the project.
7. Once the project is running, press <kbd>F1</kbd> and select **Remote-Containers: Forward Port from Container...**
8. Select port 8090 and click the "Open Browser" button in the notification that appears.
9. You should see "Hello remote world from ASP.NET Core!" after the page loads.
10. From here, you can add breakpoints or edit the contents of the `test-project` folder to do further testing.

## License

Copyright (c) Microsoft Corporation. All rights reserved.

Licensed under the MIT License. See [LICENSE](https://github.com/Microsoft/vscode-dev-containers/blob/master/LICENSE).