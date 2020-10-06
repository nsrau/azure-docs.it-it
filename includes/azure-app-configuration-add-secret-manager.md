---
ms.openlocfilehash: c3c5b8ef94b507cad433e587c9ebfc2ec16c0ff9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440396"
---
## <a name="add-secret-manager"></a>Aggiungere Secret Manager

Uno strumento denominato Secret Manager archivia i dati sensibili per operazioni di sviluppo al di fuori dell'albero del progetto. Questo approccio contribuisce a impedire la condivisione accidentale dei segreti dell'app all'interno del codice sorgente. Completare i passaggi seguenti per abilitare l'uso di Secret Manager nel progetto ASP.NET Core:

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

Passare alla directory radice del progetto ed eseguire il comando seguente per abilitare l'archiviazione dei segreti nel progetto:

```dotnetcli
dotnet user-secrets init
```

Un elemento `UserSecretsId` contenente un GUID viene aggiunto al file con estensione *csproj*:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

1. Aprire il file con estensione *csproj*.

1. Aggiungere un elemento `UserSecretsId` al file con estensione *csproj*, come illustrato di seguito. È possibile usare lo stesso GUID oppure sostituire questo valore con uno proprio.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
1. Salvare il file con estensione *csproj*.

---

> [!TIP]
> Per altre informazioni su Secret Manager, vedere [Archiviazione sicura dei segreti delle app in fase di sviluppo in ASP.NET Core](/aspnet/core/security/app-secrets).
