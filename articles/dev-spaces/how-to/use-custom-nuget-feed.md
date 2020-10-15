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
ms.openlocfilehash: d60d7142d9b9979be76eebb3d324a448bd76638f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960219"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Usare un feed NuGet personalizzato con Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Un feed NuGet costituisce un modo semplice per includere le origini dei pacchetti in un progetto. Azure Dev Spaces necessario accedere a questo feed per poter installare correttamente le dipendenze nel contenitore docker.

## <a name="set-up-a-nuget-feed"></a>Configurare un feed NuGet

Aggiungere un [riferimento al pacchetto](/nuget/consume-packages/package-references-in-project-files) per la dipendenza nel `*.csproj` file nel `PackageReference` nodo. Ad esempio:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Creare un file di [NuGet.Config](/nuget/reference/nuget-config-file) nella cartella del progetto e impostare `packageSources` le `packageSourceCredentials` sezioni e per il feed NuGet. La `packageSources` sezione contiene l'URL del feed, che deve essere accessibile dal cluster AKS. `packageSourceCredentials`Sono le credenziali per l'accesso al feed. Ad esempio:

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

Aggiornare il Dockerfile per copiare il `NuGet.Config` file nell'immagine. Ad esempio:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> In Windows, `NuGet.Config` , `Nuget.Config` e `nuget.config` funziona come nomi di file validi. In Linux è solo `NuGet.Config` un nome file valido per il file. Poiché Azure Dev Spaces USA Docker e Linux, questo file deve essere denominato `NuGet.Config` . È possibile correggere la denominazione manualmente o eseguendo `dotnet restore --configfile nuget.config` .


Se si usa Git, non è necessario avere le credenziali per il feed NuGet nel controllo della versione. Aggiungere `NuGet.Config` a `.gitignore` per il progetto in modo che il `NuGet.Config` file non venga aggiunto al controllo della versione. Azure Dev Spaces sarà necessario questo file durante il processo di compilazione dell'immagine del contenitore, ma per impostazione predefinita rispetta le regole definite in `.gitignore` e `.dockerignore` durante la sincronizzazione. Per modificare il valore predefinito e consentire Azure Dev Spaces di sincronizzare il `NuGet.Config` file, aggiornare il `azds.yaml` file:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Se non si usa Git, è possibile ignorare questo passaggio.

La volta successiva che si esegue `azds up` o si raggiunge il `F5` Visual Studio Code o Visual Studio, Azure Dev Spaces sincronizza il `NuGet.Config` file e lo usa per installare le dipendenze del pacchetto.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su [NuGet e sul suo funzionamento](/nuget/what-is-nuget).
