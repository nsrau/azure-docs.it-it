---
title: Usare un feed NuGet personalizzato
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Usare un feed NuGet personalizzato per accedere ai pacchetti NuGet e usarli in uno spazio Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, servizio Azure Container, contenitori
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325734"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Usare un feed NuGet personalizzato con Azure Dev SpacesUse a custom NuGet feed with Azure Dev Spaces

Un feed NuGet costituisce un modo semplice per includere le origini dei pacchetti in un progetto. Azure Dev Spaces deve accedere a questo feed affinché le dipendenze siano installate correttamente nel contenitore Docker.Azure Dev Spaces needs to access this feed in order for dependencies to be properly installed in the Docker container.

## <a name="set-up-a-nuget-feed"></a>Configurare un feed NuGet

Aggiungere un [riferimento al pacchetto](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) `*.csproj` per la `PackageReference` dipendenza nel file nel nodo. Ad esempio:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Creare un file [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) nella cartella `packageSources` `packageSourceCredentials` del progetto e impostare le sezioni e per il feed NuGet. La `packageSources` sezione contiene l'URL del feed, che deve essere accessibile dal cluster AKS. Le `packageSourceCredentials` sono le credenziali per l'accesso al feed. Ad esempio:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Aggiornare i file Docker per copiare il `NuGet.Config` file nell'immagine. Ad esempio:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> In `NuGet.Config`Windows, `Nuget.Config`, `nuget.config` e tutti funzionano come nomi di file validi. In Linux, `NuGet.Config` solo è un nome di file valido per questo file. Poiché Azure Dev Spaces usa Docker e `NuGet.Config`Linux, questo file deve essere denominato . È possibile correggere il nome `dotnet restore --configfile nuget.config`manualmente o eseguendo .


Se si utilizza Git, non è necessario disporre delle credenziali per il feed NuGet nel controllo della versione. Aggiungere `NuGet.Config` al `.gitignore` per il progetto `NuGet.Config` in modo che il file non viene aggiunto al controllo della versione. Azure Dev Spaces richiede questo file durante il processo di compilazione dell'immagine contenitore, ma per impostazione predefinita rispetta le regole definite in `.gitignore` e `.dockerignore` durante la sincronizzazione. Per modificare l'impostazione predefinita e `NuGet.Config` consentire ad `azds.yaml` Azure Dev Spaces di sincronizzare il file, aggiornare il file:To change the default and allow Azure Dev Spaces to synchronize the file, update the file:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Se non si utilizza Git, è possibile ignorare questo passaggio.

Alla successiva esecuzione `azds up` o `F5` hit in Visual Studio Code o Visual `NuGet.Config` Studio, Azure Dev Spaces sincronizza il file usarlo per installare le dipendenze del pacchetto.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su [NuGet e su come funziona.](https://docs.microsoft.com/nuget/what-is-nuget)