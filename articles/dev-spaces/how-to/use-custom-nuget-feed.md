---
title: Come usare un NuGet personalizzato feed in spazi di sviluppo di Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: conceptual
description: Usare un feed NuGet personalizzato per accedere ai pacchetti NuGet e usarli in uno spazio Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, servizio Azure Container, contenitori
manager: ghogen
ms.openlocfilehash: 1a000e378a9b8ecfb09d778fd6444e3f24b3df7b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686458"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Usare un feed NuGet personalizzato in uno spazio Azure Dev Spaces

Un feed NuGet costituisce un modo semplice per includere le origini dei pacchetti in un progetto. Azure Dev Spaces dovrà essere in grado di accedere a questo feed affinché le dipendenze siano installate correttamente nel contenitore Docker.

## <a name="set-up-a-nuget-feed"></a>Configurare un feed NuGet

Per configurare un feed NuGet:
1. Aggiungere un [riferimento al pacchetto](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) nel file `*.csproj` nel nodo `PackageReference`.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. Creare un file [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) nella cartella di progetto.
     * Usare la sezione `packageSources` per fare riferimento al percorso del feed NuGet. Importante: il feed NuGet deve essere accessibile pubblicamente.
     * Usare la sezione `packageSourceCredentials` per configurare le credenziali di nome utente e password. 

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

3. Se si usa il controllo del codice sorgente:
    - Fare riferimento a `NuGet.Config` nel file `.gitignore` in modo da non eseguire accidentalmente il commit delle credenziali nel repository del codice sorgente.
    - Aprire il file `azds.yaml` nel progetto e individuare la sezione `build`, quindi inserire il frammento di codice seguente per assicurarsi che il file `NuGet.Config` venga sincronizzato con Azure e usato durante il processo di compilazione dell'immagine del contenitore. Per impostazione predefinita, Azure Dev Spaces non sincronizza i file che corrispondono alle regole `.gitignore` e `.dockerignore`.

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>Passaggi successivi

Dopo avere completato i passaggi precedenti, alla successiva esecuzione di `azds up` (o quando si preme `F5` in VSCode o Visual Studio), Azure Dev Spaces sincronizzerà il file `NuGet.Config` in Azure, che verrà quindi utilizzato da `dotnet restore` per installare le dipendenze del pacchetto nel contenitore.

