# Sitefinity 15.2.8432 – .NET Framework Backend + ASP.NET Core Renderer Setup

This guide walks you through creating a **Sitefinity CMS 15.2.8432** project using **.NET Framework 4.8** backend and **ASP.NET Core .NET 8 LTS** frontend renderer.

📚 Based on: [Progress Docs - Install Sitefinity in .NET Core Mode](https://www.progress.com/documentation/sitefinity-cms/152/install-sitefinity-in-.net-core-mode-dp)

---

## Step 1: Create the Sitefinity Backend (.NET Framework 4.8)
### 1.1 Create Project
- Open **Visual Studio 2022** → **Create a new project**  
- Select **C# ASP.NET Web Application (.NET Framework)**  
- Configure project as:  
  - **Project name:** `SingleBuyer.Sitefinity`  
  - **Solution name:** `SingleBuyer-DP`  
  - **Framework:** `.NET Framework 4.8` (must be 4.8)  
  - **Template:** Empty

### 1.2 Configure NuGet Source
- Go to: `Tools > NuGet Package Manager > Package Manager Settings > Package Sources`  
- Make sure the source https://nuget.sitefinity.com/nuget is **installed and enabled**

### 1.3 Install Sitefinity Package
- Right-click `SingleBuyer.Sitefinity` project → **Manage NuGet Packages**  
- Search and install: Telerik.Sitefinity.All (version 15.2.8432)
  - Accept license terms  
  - If a popup appears, select **Ignore**  
- Note: `Progress.Sitefinity.Headless` package will be installed automatically  
---

## Step 2: Create the Renderer (.NET Core 8.0)
### 2.1 Create Project
- In Visual Studio → **File > Add > New Project**  
- Select **ASP.NET Core Empty**  
- Configure project as:  
  - **Project name:** `SingleBuyer.Sitefinity.Renderer`  
  - **Framework:** `.NET 8.0 (Long Term Support)`  
  - **Add to solution:** `SingleBuyer-DP`

### 2.2 Install Required NuGet Packages
- In `SingleBuyer.Sitefinity.Renderer`, install the following packages:
  - **Progress.Sitefinity.AspNetCore**
  - **Progress.Sitefinity.AspNetCore.FormWidgets**
  - **Progress.Sitefinity.AspNetCore.Widgets**
- Note: `Progress.Sitefinity.Renderer` and `Progress.Sitefinity.RestSdk` are installed automatically as dependencies
---

## Step 3: Configure the ASP.NET Core Renderer
### 3.1 Update `appsettings.json`
- Replace your `appsettings.json` content with:
  ```json
  {
    "Logging": {
      "LogLevel": {
        "Default": "Information",
        "Microsoft": "Warning",
        "Microsoft.Hosting.Lifetime": "Information"
      }
    },
    "AllowedHosts": "*",
    "Sitefinity": {
      "Url": "https://localhost:44354",
      "WebServicePath": "api/default"
    }
  }
  ```

### 3.2 Update `Program.cs`
- Replace `Program.cs` content with:
  ```csharp
  using Microsoft.AspNetCore.Builder;
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.Hosting;
  using Progress.Sitefinity.AspNetCore;
  using Progress.Sitefinity.AspNetCore.FormWidgets;
  
  var builder = WebApplication.CreateBuilder(args);
  
  builder.Services.AddSitefinity();
  builder.Services.AddViewComponentModels();
  builder.Services.AddFormViewComponentModels();
  
  var app = builder.Build();
  
  if (app.Environment.IsDevelopment())
  {
      app.UseDeveloperExceptionPage();
  }
  else
  {
      app.UseExceptionHandler("/Error");
      app.UseHsts();
  }
  
  app.UseStaticFiles();
  app.UseRouting();
  app.UseSitefinity();
  
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapSitefinityEndpoints();
  });
  
  app.Run();
  ```

### 3.3 Update `launchSettings.json`
- Update the `launchSettings.json` profiles with:
  ```json
  {
    "http": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": true,
      "applicationUrl": "http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "https": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": true,
      "applicationUrl": "https://localhost:5001;http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
  ```

### 3.4 Build Solution
- Build the entire solution in Visual Studio 
---

## 4. Additional Setup and Running
### 4.1 Run Sitefinity Project
  - Start the `SingleBuyer.Sitefinity` project with IIS Express.  
  - A browser should open pointing to something like: `https://localhost:44354/`  
  - You should see the Sitefinity licensing page.

### 4.2 Update Renderer URL
 - Update the `"Url"` value in `appsettings.json` of the Renderer project (`SingleBuyer.Sitefinity.Renderer`) to match the URL where Sitefinity is running.  
 - Example:  
     ```json
     "Sitefinity": {
       "Url": "https://localhost:44354",
       "WebServicePath": "api/default"
     }
     ```
### 4.3 Configure Multiple Startup Projects
 - In Visual Studio, right-click the solution and select **Set Startup Projects**.  
 - Choose **Multiple startup projects**, set the **Action** for both projects to **Start**, then click **Apply** and **OK**.

### 4.4 Run Both Projects
 - Start debugging (F5).  
 - You should get:
   - Sitefinity running on `https://localhost:44354`
   - Renderer running on `https://localhost:5001`
---

## 5. Notes
- Always rebuild the solution and restore NuGet packages after any code or dependency changes.  
- Ensure the URLs in `appsettings.json` match the running instances.  
- The renderer requires Sitefinity to be accessible at the configured URL.
