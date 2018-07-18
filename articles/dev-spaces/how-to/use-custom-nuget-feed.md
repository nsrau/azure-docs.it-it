---
title: Come usare un feed NuGet personalizzato in Azure Dev Spaces | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: article
description: Usare un feed NuGet personalizzato per accedere ai pacchetti NuGet e usarli in uno spazio Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, AKS, servizio contenitore di Azure, contenitori
manager: ghogen
ms.openlocfilehash: 3badd15bcfd09c97b43744a20c5df05f4ff57e84
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199110"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Usare un feed NuGet personalizzato in uno spazio Azure Dev Spaces

Un feed NuGet costituisce un modo semplice per includere le origini dei pacchetti in un progetto. Azure Dev Spaces dovrà essere in grado di accedere a questo feed affinché le dipendenze siano installate correttamente nel contenitore Docker.

## <a name="set-up-a-nuget-feed"></a>Configurare un feed NuGet

Per configurare un feed NuGet:
1. Aggiungere un [riferimento al pacchetto](https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files) nel file `*.csproj` nel nodo `PackageReference`.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. Creare un file [NuGet.Config](https://docs.microsoft.com/en-us/nuget/reference/nuget-config-file) nella cartella di progetto.
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

