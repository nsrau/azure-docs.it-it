---
title: Come preparare un progetto per Azure Dev Spaces funziona
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Viene descritto come preparare il progetto con Azure Dev Spaces funziona
keywords: azds. YAML, Azure Dev Spaces, spazi di sviluppo, Docker, Kubernetes, Azure, AKS, servizio Kubernetes di Azure, contenitori
ms.openlocfilehash: 61351072494b51d02a1d6c31399208b9e9b54fce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213420"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Come preparare un progetto per Azure Dev Spaces funziona

Azure Dev Spaces offre diversi modi per eseguire rapidamente l'iterazione e il debug di applicazioni Kubernetes e collaborare con il team in un cluster del servizio Azure Kubernetes. Gli spazi di sviluppo possono generare grafici Dockerfile e Helm per il progetto. Spazi di sviluppo crea e usa anche un file di configurazione per la distribuzione, l'esecuzione e il debug delle applicazioni Kubernetes in AKS. Tutti questi file si trovano nel codice dell'applicazione e possono essere aggiunti al sistema di controllo della versione.

Questo articolo descrive cosa accade per preparare il progetto per l'esecuzione in AKS con gli spazi di sviluppo.

## <a name="prepare-your-code"></a>Preparare il codice

Per eseguire l'applicazione in uno spazio di sviluppo, è necessario che venga inserito in un contenitore ed è necessario definire il modo in cui deve essere distribuita in Kubernetes. Per distribuire l'applicazione, è necessario un Dockerfile. Per definire il modo in cui l'applicazione viene distribuita in Kubernetes, è necessario un [grafico Helm](https://docs.helm.sh/). Per semplificare la creazione del grafico Dockerfile e Helm per l'applicazione, gli strumenti lato client forniscono il `prep` comando:

```cmd
azds prep --enable-ingress
```

Il comando esaminerà `prep` i file del progetto e tenterà di creare il grafico Dockerfile e Helm per l'esecuzione dell'applicazione in Kubernetes. Attualmente, il `prep` comando genererà un grafico Dockerfile e Helm con le seguenti lingue:

* Java
* Node.js
* .NET Core

È *necessario* eseguire il `prep` comando da una directory che contiene il codice sorgente. L'esecuzione del `prep` comando dalla directory corretta consente agli strumenti lato client di identificare la lingua e creare un Dockerfile appropriato per distribuire l'applicazione. È anche possibile eseguire il `prep` comando da una directory che contiene un file di *pom.xml* per i progetti Java.

Se si esegue il `prep` comando dalla directory che non contiene codice sorgente, gli strumenti lato client non genereranno un Dockerfile. Verrà inoltre visualizzato un errore che informa che *non è stato possibile generare Dockerfile a causa della lingua non supportata*. Questo errore si verifica anche se gli strumenti lato client non riconoscono il tipo di progetto.

Quando si esegue il `prep` comando, è possibile specificare il `--enable-ingress` flag. Questo flag indica al controller di creare un endpoint accessibile da Internet per questo servizio. Se non si specifica questo flag, il servizio è accessibile solo dall'interno del cluster o usando il tunnel localhost creato dagli strumenti lato client. È possibile abilitare o disabilitare questo comportamento dopo l'esecuzione del `prep` comando aggiornando il grafico Helm generato.

Il `prep` comando non sostituisce i grafici dockerfile o Helm esistenti presenti nel progetto. Se un grafico Dockerfile o Helm esistente utilizza la stessa convenzione di denominazione dei file generati dal `prep` comando, il comando ignorerà la `prep` generazione di tali file. In caso contrario, il `prep` comando genererà il proprio Dockerfile o il proprio grafico Helm lungo i file esistenti.

> [!IMPORTANT]
> Azure Dev Spaces usa il grafico Dockerfile e Helm per il progetto per compilare ed eseguire il codice, ma è possibile modificare questi file se si vuole modificare il modo in cui il progetto viene compilato ed eseguito.

Il `prep` comando genererà anche un `azds.yaml` file nella radice del progetto. Azure Dev Spaces usa questo file per compilare, installare, configurare ed eseguire l'applicazione. Questo file di configurazione elenca il percorso del grafico Dockerfile e Helm e fornisce anche una configurazione aggiuntiva su tali elementi.

Di seguito è riportato un esempio di file azds. YAML creato con l' [applicazione di esempio .NET Core](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

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

Il `azds.yaml` file generato dal `prep` comando è destinato a funzionare per uno scenario di sviluppo semplice e singolo del progetto. Se il progetto specifico presenta una maggiore complessità, potrebbe essere necessario aggiornare il file dopo l'esecuzione del `prep` comando. Ad esempio, il progetto potrebbe richiedere alcune modifiche al processo di compilazione o avvio in base alle esigenze di sviluppo e debug. È anche possibile che nel progetto siano presenti più applicazioni, che richiedono più processi di compilazione o un contenuto di compilazione diverso.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'esecuzione del codice nello spazio di sviluppo, vedere la pagina relativa all' [esecuzione del codice con Azure Dev Spaces funziona][how-it-works-up].

[how-it-works-up]: how-dev-spaces-works-up.md