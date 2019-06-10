---
title: Creare uno spazio di sviluppo Kubernetes nel cloud usando Java e VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: stepro
ms.author: stephpr
ms.date: 09/26/2018
ms.topic: tutorial
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
manager: mmontwil
ms.openlocfilehash: 0677eb4c65da242f8cfcb20754ec88ffb02c5929
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393154"
---
# <a name="get-started-on-azure-dev-spaces-with-java"></a>Guida introduttiva ad Azure Dev Spaces con Java

In questa guida si apprenderà come:

- Creare un ambiente basato su Kubernetes in Azure ottimizzato per lo sviluppo, ovvero uno _spazio di sviluppo_.
- Sviluppare codice in modo iterativo nei contenitori con VS Code e la riga di comando.
- Sviluppare e testare il codice in modo produttivo in un ambiente di team.

> [!Note]
> **In caso di problemi** in qualsiasi momento, vedere la sezione [Risoluzione dei problemi](troubleshooting.md).

## <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure
Azure Dev Spaces richiede un'installazione minima nel computer locale. La maggior parte della configurazione dello spazio di sviluppo viene archiviata nel cloud ed è condivisibile con altri utenti. Per iniziare, scaricare ed eseguire l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sign-in-to-azure-cli"></a>Accedere all'interfaccia della riga di comando di Azure
Accedere ad Azure. Digitare il comando seguente in una finestra del terminale:

```cmd
az login
```

> [!Note]
> Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Se sono disponibili più sottoscrizioni di Azure...
È possibile visualizzare le sottoscrizioni eseguendo: 

```cmd
az account list
```
Individuare la sottoscrizione con `isDefault: true` nell'output JSON.
Se non si tratta della sottoscrizione che si vuole usare, è possibile modificare la sottoscrizione predefinita:

```cmd
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Creare un cluster Kubernetes abilitato per Azure Dev Spaces

Al prompt dei comandi creare il gruppo di risorse in un'[area che supporta Azure Dev Spaces][supported-regions].

```cmd
az group create --name MyResourceGroup --location <region>
```

Usare il comando seguente per creare un cluster Kubernetes:

```cmd
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

La creazione del cluster richiede alcuni minuti.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Configurare il cluster servizio Azure Kubernetes per l'uso di Azure Dev Spaces

Immettere il comando seguente dell'interfaccia della riga di comando di Azure, usando il gruppo di risorse contenente il cluster servizio Azure Kubernetes e il nome del cluster servizio Azure Kubernetes. Il comando configura il cluster con il supporto per gli spazi Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

> [!IMPORTANT]
> Il processo di configurazione di Azure Dev Spaces rimuoverà lo spazio dei nomi `azds` nel cluster, se presente.

## <a name="get-kubernetes-debugging-for-vs-code"></a>Ottenere funzionalità di debug Kubernetes per VS Code
Per gli sviluppatori di .NET Core e Node.js che usano VS Code sono disponibili funzionalità avanzate, come il debug Kubernetes.

1. Se [VS Code](https://code.visualstudio.com/Download) non è disponibile, installarlo.
1. Scaricare e installare l'[estensione Azure Dev Spaces di Visual Studio](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Fare clic su Installa nella pagina del Marketplace dell'estensione e di nuovo in VS Code. 

Per eseguire il debug di applicazioni Java con Azure Dev Spaces, scaricare e installare l'estensione [Java Debugger for Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) per VS Code. Fare clic su Installa nella pagina del Marketplace dell'estensione e di nuovo in VS Code.

## <a name="create-a-web-app-running-in-a-container"></a>Creare un'app Web in esecuzione in un contenitore

In questa sezione si creerà un'applicazione Web Java e si eseguirà tale app in un contenitore in Kubernetes.

### <a name="create-a-java-web-app"></a>Creare un'app Web Java
Scaricare il codice da GitHub passando a https://github.com/Azure/dev-spaces e selezionando **Clone or Download** (Clona o scarica) per scaricare il repository GitHub nell'ambiente locale. Il codice per questa guida è disponibile in `samples/java/getting-started/webfrontend`.

## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Preparazione del codice per lo sviluppo Docker e Kubernetes
Fino a questo punto, si ha un'app Web di base che è possibile eseguire in locale. Ora si tratta di racchiuderla in un contenitore mediante la creazione di asset che definiscono il contenitore dell'applicazione e la modalità con cui verrà distribuita a Kubernetes. Questa operazione è semplice da eseguire con Azure Dev Spaces: 

1. Avviare VS Code e aprire la cartella `webfrontend`. Le richieste, visualizzate per impostazione predefinita, di aggiungere risorse di debug o di ripristinare il progetto possono essere ignorate.
1. Aprire il terminale integrato di VS Code usando il menu **Visualizza > Terminale integrato**.
1. Eseguire questo comando assicurandosi che **webfrontend** sia la cartella corrente:

    ```cmd
    azds prep --public
    ```

Il comando `azds prep` dell'interfaccia della riga di comando di Azure genera gli asset Docker e Kubernetes con le impostazioni predefinite:
* `./Dockerfile` descrive l'immagine del contenitore dell'app e come il codice sorgente viene compilato ed eseguito all'interno del contenitore.
* Un [grafico Helm](https://docs.helm.sh) in `./charts/webfrontend` descrive come distribuire il contenitore a Kubernetes.

Per il momento non è necessario comprendere l'intero contenuto di questi file. Vale la pena sottolineare, tuttavia, che **gli stessi asset configuration-as-code Kubernetes e Docker possono essere utilizzati dallo sviluppo alla produzione, in modo da garantire una maggiore coerenza tra ambienti diversi.**
 
Dal comando `prep` viene generato anche un file denominato `./azds.yaml` ed è il file di configurazione per Azure Dev Spaces. Completa gli elementi Docker e Kubernetes con elementi di configurazione aggiuntivi che consentono di creare un'esperienza di sviluppo iterativo in Azure.

## <a name="build-and-run-code-in-kubernetes"></a>Compilare ed eseguire codice in Kubernetes
Eseguire il codice. Nella finestra del terminale, eseguire questo comando dalla **cartella radice del codice**, webfrontend:

```cmd
azds up
```

Monitorare l'output del comando; si noteranno varie cose importanti man mano che avanza:
- Il codice sorgente viene sincronizzato con lo spazio di sviluppo in Azure.
- Un'immagine del contenitore viene compilata in Azure, come specificato dagli asset di Docker nella cartella del codice.
- Vengono creati oggetti Kubernetes che usano l'immagine del contenitore come specificato dal grafico Helm nella cartella del codice.
- Vengono visualizzate informazioni sull'endpoint del contenitore. In questo caso, è previsto un URL HTTP pubblico.
- Presupponendo che le fasi precedenti siano state completate correttamente, si dovrebbe iniziare a visualizzare l'output `stdout` (e `stderr`) all'avvio del contenitore.

> [!Note]
> Questi passaggi richiederanno più tempo la prima volta che viene eseguito il comando `up`, ma le esecuzioni successive dovrebbero essere più rapide.

### <a name="test-the-web-app"></a>Testare l'app Web
Analizzare l'output della console per informazioni sull'URL pubblico che è stato creato con il comando `up`. Sarà nel formato: 

```
(pending registration) Service 'webfrontend' port 'http' will be available at <url>
Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
```

Aprire l'URL in una finestra del browser; si dovrebbe visualizzare il caricamento dell'applicazione Web. Durante l'esecuzione del contenitore, l'output `stdout` e `stderr` vengono trasmessi nella finestra del terminale.

> [!Note]
> Alla prima esecuzione, possono essere necessari alcuni minuti prima che il DNS pubblico sia pronto. Se l'URL pubblico non viene risolto, è possibile usare l'URL alternativo `http://localhost:<portnumber>` visualizzato nell'output della console. Se si usa l'URL localhost, potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito in servizio Azure Kubernetes. Per motivi di praticità e per semplificare l'interazione con il servizio nel computer locale, Azure Dev Spaces crea un tunnel SSH temporaneo al contenitore in esecuzione in Azure. È possibile tornare in seguito per tentare di usare l'URL pubblico quando il record DNS sarà pronto.
> ### <a name="update-a-content-file"></a>Aggiornare un file di contenuto
> Azure Dev Spaces consente non solo di eseguire codice in Kubernetes, ma anche di visualizzare in modo rapido e iterativo l'applicazione delle modifiche apportate al codice in un ambiente Kubernetes nel cloud.

1. Nella finestra del terminale premere `Ctrl+C` per arrestare `azds up`.
1. Aprire il file di codice denominato `src/main/java/com/ms/sample/webfrontend/Application.java` e modificare il messaggio di saluto: `return "Hello from webfrontend in Azure!";`
1. Salvare il file.
1. Eseguire `azds up` nella finestra del terminale.

Questo comando ricompila l'immagine del contenitore e ridistribuisce il grafico Helm. Per visualizzare l'effetto delle modifiche apportate al codice nell'app in esecuzione, è sufficiente aggiornare il browser.

Esiste tuttavia un *metodo ancora più rapido* per lo sviluppo di codice, che verrà esaminato nella sezione successiva. 

## <a name="debug-a-container-in-kubernetes"></a>Eseguire il debug di un contenitore in Kubernetes

In questa sezione si userà VS Code per eseguire direttamente il debug del contenitore in esecuzione in Azure. Si apprenderà anche come eseguire più rapidamente un ciclo modifica-esecuzione-test.

![](media/common/edit-refresh-see.png)

> [!Note]
> **In caso di problemi** in qualsiasi momento, vedere la sezione [Risoluzione dei problemi](troubleshooting.md) o inserire un commento in questa pagina.

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inizializzare gli asset di debug con l'estensione VS Code
Per prima cosa è necessario configurare il progetto di codice in modo che Visual Studio Code comunichi con lo spazio di sviluppo in Azure. L'estensione Visual Studio Code per Azure Dev Spaces offre un comando helper per l'impostazione della configurazione di debug. 

Aprire il **riquadro comandi** (usando il menu **Visualizza | Riquadro comandi**) e usare il completamento automatico per digitare e selezionare questo comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

In questo modo viene aggiunta la configurazione di debug per Azure Dev Spaces nella cartella `.vscode`. Questo comando non deve essere confuso con il comando `azds prep` che configura il progetto per la distribuzione.

![](media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Selezionare la configurazione di debug di AZDS
1. Per aprire la visualizzazione Debug, fare clic sull'icona Debug nella **barra attività** sul lato di VS Code.
1. Selezionare **Launch Java Program (AZDS)** (Avvia programma Java - AZDS) come configurazione di debug attiva.

![](media/get-started-java/debug-configuration.png)

> [!Note]
> Se nel riquadro comandi non vengono visualizzati comandi di Azure Dev Spaces, assicurarsi di aver installato l'estensione di VS Code per Azure Dev Spaces. Verificare che l'area di lavoro aperta in VS Code sia la cartella contenente azds.yaml.

### <a name="debug-the-container-in-kubernetes"></a>Eseguire il debug del contenitore in Kubernetes
Premere **F5** per eseguire il debug del codice in Kubernetes.

Come con il comando `up`, il codice viene sincronizzato con lo spazio di sviluppo e un contenitore viene compilato e distribuito in Kubernetes. In questo caso, ovviamente, il debugger è associato al contenitore remoto.

> [!Tip]
> La barra di stato di VS Code visualizza un URL selezionabile.

![](media/common/vscode-status-bar-url.png)

Impostare un punto di interruzione in un file di codice sul lato server, ad esempio all'interno della funzione `greeting()` nel file di origine `src/main/java/com/ms/sample/webfrontend/Application.java`. Aggiornando la pagina del browser si raggiunge il punto di interruzione.

Le informazioni di debug, come stack di chiamate, variabili locali, informazioni sulle eccezioni e così via, sono completamente accessibili come in caso di esecuzione del codice in locale.

### <a name="edit-code-and-refresh"></a>Modificare il codice e aggiornare
Con il debugger attivo, apportare una modifica al codice. Ad esempio, modificare il messaggio di saluto in `src/main/java/com/ms/sample/webfrontend/Application.java`. 

```java
public String greeting()
{
    return "I'm debugging Java code in Azure!";
}
```

Salvare il file e quindi nel **riquadro delle azioni di debug** fare clic sul pulsante **Aggiorna**.

![](media/get-started-java/debug-action-refresh.png)

Invece di ricompilare e ridistribuire una nuova immagine del contenitore ogni volta che vengono apportate modifiche al codice, operazione che spesso richiede una notevole quantità di tempo, Azure Dev Spaces ricompilerà in modo incrementale il codice nel contenitore esistente in modo da velocizzare il ciclo di modifica/debug.

Aggiornare l'app Web nel browser. Nell'interfaccia utente verrà visualizzato il messaggio personalizzato.

**È ora possibile usare questo metodo per eseguire rapidamente l'iterazione e il debug di codice direttamente in Kubernetes.** Successivamente, si vedrà come è possibile creare e chiamare un secondo contenitore.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sullo sviluppo multiservizio](multi-service-java.md)


[supported-regions]: about.md#supported-regions-and-configurations
