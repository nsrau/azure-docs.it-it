---
title: Creare uno spazio di sviluppo Kubernetes nel cloud | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: quickstart
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenitori
manager: douge
ms.openlocfilehash: d17a1c7700f1e0ac9533b7117b50a14431d0e5f1
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716622"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-vs-code"></a>Guida introduttiva: Creare uno spazio di sviluppo Kubernetes con Azure Dev Spaces (.NET Core e VS Code)

In questa guida si apprenderà come:

- Configurare Azure Dev Spaces con un cluster Kubernetes gestito in Azure.
- Sviluppare codice in modo iterativo nei contenitori con VS Code e la riga di comando.
- Eseguire il debug del codice nello spazio di sviluppo da Visual Studio Code

> [!Note]
> **In caso di problemi** in qualsiasi momento, vedere la sezione [Risoluzione dei problemi](troubleshooting.md) o inserire un commento in questa pagina. È anche possibile provare l'[esercitazione](get-started-netcore.md) più dettagliata.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha un account, è possibile [crearne uno gratuito](https://azure.microsoft.com/free).
- Un [cluster Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks) che esegue Kubernetes 1.9.6 o versione successiva nell'area EastUS, CentralUS, WestUS2, WestEurope, CanadaCentral o CanadaEast con il **routing HTTP dell'applicazione** abilitato.

  ![Assicurarsi di abilitare il routing HTTP dell'applicazione.](media/common/Kubernetes-Create-Cluster-3.PNG)

- [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="set-up-azure-dev-spaces"></a>Configurare Azure Dev Spaces

L'interfaccia della riga di comando di Azure e l'estensione Azure Dev Spaces possono essere installate ed eseguite nei computer Windows, Mac o Linux. Per Linux, sono supportate le distribuzioni seguenti: Ubuntu (18.04, 16.04 e 14.04), Debian 8 e 9, RHEL 7, Fedora 26+, CentOS 7, openSUSE 42.2 e SLES 12.

Seguire questa procedura per configurare Azure Dev Spaces:

1. Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) (versione 2.0.43 o successiva).
1. Configurare Dev Spaces nel cluster AKS: `az aks use-dev-spaces -g MyResourceGroup -n MyAKS`
1. Scaricare l'[estensione Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) per Visual Studio Code. Fare clic su Installa nella pagina del Marketplace dell'estensione e di nuovo in VS Code.

## <a name="build-and-run-code-in-kubernetes"></a>Compilare ed eseguire codice in Kubernetes

1. Scaricare il codice di esempio da GitHub: [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) 
1. Passare alla cartella webfrontend: `cd dev-spaces/samples/dotnetcore/getting-started/webfrontend`
1. Generare gli asset dei chart Docker e Helm: `azds prep --public`
1. Compilare ed eseguire il codice in AKS. Dalla **cartella webfrontend** nella finestra del terminale eseguire questo comando: `azds up`
1. Analizzare l'output della console per informazioni sull'URL che è stato creato con il comando `up`. Sarà nel formato: 

   `(pending registration) Service 'webfrontend' port 'http' will be available at <url>\r\nService 'webfrontend' port 80 (TCP) is available at http://localhost:<port>` 

   Aprire l'URL in una finestra del browser; si dovrebbe visualizzare il caricamento dell'applicazione Web. 
   
   > [!Note]
   > Alla prima esecuzione, possono essere necessari alcuni minuti prima che il DNS pubblico sia pronto. Se l'URL pubblico non viene risolto, è possibile usare l'URL http://localhost:<portnumber> alternativo visualizzato nell'output della console. Se si usa l'URL localhost, potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito in AKS. Per motivi di praticità e per semplificare l'interazione con il servizio nel computer locale, Azure Dev Spaces crea un tunnel SSH temporaneo al contenitore in esecuzione in Azure. È possibile tornare in seguito per tentare di usare l'URL pubblico quando il record DNS sarà pronto.

### <a name="update-a-content-file"></a>Aggiornare un file di contenuto

1. Individuare un file, ad esempio `./Views/Home/Index.cshtml`, e apportare una modifica al codice HTML. Ad esempio, modificare la riga 70 contenente `<h2>Application uses</h2>` come segue: `<h2>Hello k8s in Azure!</h2>`
1. Salvare il file. Poco dopo, nella finestra del terminale verrà visualizzato un messaggio che informa che un file nel contenitore in esecuzione è stato aggiornato.
1. Passare al browser e aggiornare la pagina. Nella pagina Web verrà visualizzato il codice HTML aggiornato.

Che cosa è successo? Le modifiche ai file di contenuto, come HTML e CSS, non richiedono la ricompilazione in un'app Web .NET Core, di conseguenza un comando `azds up` attivo sincronizza automaticamente qualsiasi file di contenuto modificato nel contenitore in esecuzione in Azure ed è possibile visualizzare immediatamente le modifiche apportate al contenuto.

### <a name="update-a-code-file"></a>Aggiornare un file di codice
L'aggiornamento di file di codice richiede alcune operazioni aggiuntive, perché un'app .NET Core deve essere ricompilata e deve generare i file binari dell'applicazione aggiornati.

1. Nella finestra del terminale premere `Ctrl+C` per arrestare `azds up`.
1. Aprire il file di codice denominato `Controllers/HomeController.cs` e modificare il messaggio che verrà visualizzato nella pagina About: `ViewData["Message"] = "Your application description page.";`
1. Salvare il file.
1. Eseguire `azds up` nella finestra del terminale. 

Questo comando ricompila l'immagine del contenitore e ridistribuisce il grafico Helm. Per visualizzare l'applicazione delle modifiche apportate al codice nell'app in esecuzione, passare al menu About nell'app Web.

Esiste tuttavia un *metodo ancora più rapido* per lo sviluppo di codice, che verrà esaminato nella sezione successiva. 

## <a name="debug-a-container-in-kubernetes"></a>Eseguire il debug di un contenitore in Kubernetes

In questa sezione si userà VS Code per eseguire direttamente il debug del contenitore in esecuzione in Azure. Si apprenderà anche come eseguire più rapidamente un ciclo modifica-esecuzione-test.

![](./media/common/edit-refresh-see.png)

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inizializzare gli asset di debug con l'estensione VS Code
Per prima cosa è necessario configurare il progetto di codice in modo che Visual Studio Code comunichi con lo spazio di sviluppo in Azure. L'estensione Visual Studio Code per Azure Dev Spaces offre un comando helper per l'impostazione della configurazione di debug. 

Aprire il **riquadro comandi** (usando il menu **Visualizza | Riquadro comandi**) e usare il completamento automatico per digitare e selezionare questo comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

In questo modo viene aggiunta la configurazione di debug per Azure Dev Spaces nella cartella `.vscode`. Questo comando non deve essere confuso con il comando `azds prep` che configura il progetto per la distribuzione.

![](./media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Selezionare la configurazione di debug di AZDS
1. Per aprire la visualizzazione Debug, fare clic sull'icona Debug nella **barra attività** sul lato di VS Code.
1. Selezionare **.NET Core Launch (AZDS)** (Avvio .NET Core - AZDS) come configurazione di debug attiva.

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> Se nel riquadro comandi non vengono visualizzati comandi di Azure Dev Spaces, assicurarsi di aver installato l'estensione di VS Code per Azure Dev Spaces. Verificare che l'area di lavoro aperta in VS Code sia la cartella contenente azds.yaml.


### <a name="debug-the-container-in-kubernetes"></a>Eseguire il debug del contenitore in Kubernetes
Premere **F5** per eseguire il debug del codice in Kubernetes.

Come con il comando `up`, il codice viene sincronizzato con lo spazio di sviluppo e un contenitore viene compilato e distribuito in Kubernetes. In questo caso, ovviamente, il debugger è associato al contenitore remoto.

> [!Tip]
> La barra di stato di VS Code visualizza un URL selezionabile.

Impostare un punto di interruzione in un file di codice sul lato server, ad esempio all'interno della funzione `Index()` nel file di origine `Controllers/HomeController.cs`. Aggiornando la pagina del browser si raggiunge il punto di interruzione.

Le informazioni di debug, come stack di chiamate, variabili locali, informazioni sulle eccezioni e così via, sono completamente accessibili come in caso di esecuzione del codice in locale.

### <a name="edit-code-and-refresh"></a>Modificare il codice e aggiornare
Con il debugger attivo, apportare una modifica al codice. Ad esempio, modificare il messaggio della pagina About in `Controllers/HomeController.cs`. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

Salvare il file e quindi nel **riquadro delle azioni di debug** fare clic sul pulsante **Aggiorna**. 

![](media/get-started-netcore/debug-action-refresh.png)

Invece di ricompilare e ridistribuire una nuova immagine del contenitore ogni volta che vengono apportate modifiche al codice, operazione che spesso richiede una notevole quantità di tempo, Azure Dev Spaces ricompilerà in modo incrementale il codice nel contenitore esistente in modo da velocizzare il ciclo di modifica/debug.

Aggiornare l'app Web nel browser e passare alla pagina delle informazioni. Nell'interfaccia utente verrà visualizzato il messaggio personalizzato.

**È ora possibile usare questo metodo per eseguire rapidamente l'iterazione e il debug di codice direttamente in Kubernetes.**

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come Azure Dev Spaces consente di sviluppare app più complesse in più contenitori e su come è possibile semplificare lo sviluppo collaborativo usando versioni o rami diversi del codice in spazi diversi. 

> [!div class="nextstepaction"]
> [Uso di più contenitori e sviluppo in team](team-development-netcore.md)
