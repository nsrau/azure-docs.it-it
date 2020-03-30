---
title: Funzionamento della preparazione di un progetto per Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descrive il funzionamento della preparazione del progetto con Azure Dev Spaces
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 24a54fffdc8e94493d2a4a9aeb1c5f02dcd192b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241634"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Funzionamento della preparazione di un progetto per Azure Dev Spaces

Azure Dev Spaces offre diversi modi per scorrere rapidamente ed eseguire il debug delle applicazioni Kubernetes e collaborare con il team in un cluster del servizio Azure Kubernetes (AKS). Gli spazi dev possono generare dockerfile e grafici Helm per il progetto. Dev Spaces crea e utilizza inoltre un file di configurazione per la distribuzione, l'esecuzione e il debug delle applicazioni Kubernetes in AKS. Tutti questi file risiedono con il codice dell'applicazione e possono essere aggiunti al sistema di controllo della versione.

Questo articolo descrive cosa accade preparare il progetto per l'esecuzione in AKS con Dev Spaces.

## <a name="prepare-your-code"></a>Preparare il codice

Per eseguire l'applicazione in uno spazio di sviluppo, è necessario essere in contenitori ed è necessario definire come deve essere distribuito a Kubernetes. Per serimare l'applicazione, è necessario un Dockerfile.To containerize your application, you need a Dockerfile. Per definire la modalità di distribuzione dell'applicazione in Kubernetes, è necessario un [grafico Helm](https://docs.helm.sh/). Per facilitare la creazione del grafico Dockerfile e Helm per `prep` l'applicazione, gli strumenti lato client forniscono il comando seguente:

```cmd
azds prep --enable-ingress
```

Il `prep` comando esaminerà i file nel progetto e tenterà di creare il Dockerfile e Helm chart per l'esecuzione dell'applicazione in Kubernetes. Attualmente, `prep` il comando genererà un dockerfile e Helm grafico con le seguenti lingue:

* Java
* Node.js
* .NET Core

È *necessario* `prep` eseguire il comando da una directory che contiene codice sorgente. L'esecuzione del `prep` comando dalla directory corretta consente agli strumenti sul lato client di identificare il linguaggio e creare un Dockerfile appropriato per il contenitore dell'applicazione. È inoltre possibile `prep` eseguire il comando da una directory che contiene un file *pom.xml* per i progetti Java.

Se si `prep` esegue il comando dalla directory che non contiene codice sorgente, gli strumenti sul lato client non genereranno un Dockerfile. Verrà inoltre visualizzato un errore che indica che *non è stato possibile generare Dockerfile a causa di una lingua non supportata.* Questo errore si verifica anche se gli strumenti sul lato client non riconoscono il tipo di progetto.

Quando si `prep` esegue il comando, è possibile `--enable-ingress` specificare il flag. Questo flag indica al controller di creare un endpoint accessibile da Internet per questo servizio. Se non si specifica questo flag, il servizio è accessibile solo dall'interno del cluster o tramite il tunnel localhost creato dagli strumenti lato client. È possibile abilitare o disabilitare questo comportamento dopo l'esecuzione del `prep` comando aggiornando il grafico Helm generato.

Il `prep` comando non sostituirà i grafici Dockerfiles o Helm esistenti nel progetto. Se un grafico Dockerfile o Helm esistente utilizza la `prep` stessa convenzione di denominazione dei file generati dal comando, il `prep` comando ignorerà la generazione di tali file. In caso `prep` contrario, il comando genererà il proprio Dockerfile o Helm chart lungo il lato dei file esistenti.

> [!IMPORTANT]
> Azure Dev Spaces usa il dockerfile e il grafico Helm per il progetto per compilare ed eseguire il codice, ma è possibile modificare questi file se si vuole modificare la modalità di compilazione ed esecuzione del progetto.

Il `prep` comando genererà `azds.yaml` anche un file alla radice del progetto. Azure Dev Spaces usa questo file per compilare, installare, configurare ed eseguire l'applicazione. Questo file di configurazione elenca la posizione del dockerfile e del grafico Helm e fornisce anche una configurazione aggiuntiva in aggiunta a tali elementi.

Di seguito è riportato un file azds.yaml di esempio creato con [l'applicazione di esempio .NET Core:](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

Il `azds.yaml` file generato `prep` dal comando deve funzionare per uno scenario di sviluppo di un singolo progetto semplice. Se il progetto specifico ha una maggiore complessità, potrebbe `prep` essere necessario aggiornare questo file dopo l'esecuzione del comando. Ad esempio, il progetto potrebbe richiedere alcune modifiche al processo di compilazione o di avvio in base alle esigenze di sviluppo o debug. È inoltre possibile avere più applicazioni nel progetto, che richiedono più processi di compilazione o un contenuto di compilazione diverso.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'esecuzione del codice nello spazio di sviluppo, vedere Funzionamento dell'esecuzione del codice con Azure Dev Spaces.To learn more about running your code in your dev space, see [How running your code with Azure Dev Spaces works.][how-it-works-up]

Per iniziare a usare Azure Dev Spaces per preparare il progetto per lo spazio di sviluppo di Azure, vedere le guide introduttive seguenti:To get started using Azure Dev Spaces to prepare your project for Azure Dev Space, see the following quickstarts:

* [Iterazione ed eseguire rapidamente il debug con Visual Studio Code e Java][quickstart-java]
* [Iterazione ed eseguire rapidamente il debug con Visual Studio Code e .NET][quickstart-netcore]
* [Eseguire rapidamente l'iterazione e il debug con Visual Studio Code e Node.js][quickstart-node]
* [Iterazione ed eseguire rapidamente il debug con Visual Studio e .NET Core][quickstart-vs]
* [Utilizzo della CLI per sviluppare un'applicazione su Kubernetes][quickstart-cli]

[how-it-works-up]: how-dev-spaces-works-up.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md