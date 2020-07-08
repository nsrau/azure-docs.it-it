---
title: Usare Processo locale con Kubernetes con Visual Studio (anteprima)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Informazioni su come usare il processo locale con Kubernetes con Visual Studio per connettere il computer di sviluppo a un cluster Kubernetes con Azure Dev Spaces
keywords: Processo locale con Kubernetes, Azure Dev Spaces, spazi di sviluppo, Docker, Kubernetes, Azure, AKS, servizio Kubernetes di Azure, contenitori
ms.openlocfilehash: 8e89baeac8d9042a87685ed1268ed694db8d9455
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316552"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-preview"></a>Usare Processo locale con Kubernetes con Visual Studio (anteprima)

Il processo locale con Kubernetes consente di eseguire ed eseguire il debug del codice nel computer di sviluppo, mentre è ancora connesso al cluster Kubernetes con il resto dell'applicazione o dei servizi. Ad esempio, se si dispone di un'architettura di microservizi di grandi dimensioni con molti servizi e database interdipendenti, la replica di tali dipendenze nel computer di sviluppo può essere difficile. Inoltre, la compilazione e la distribuzione di codice nel cluster Kubernetes per ogni modifica del codice durante lo sviluppo a ciclo interno possono essere lenti, dispendiose in termini di tempo e difficili da usare con un debugger.

Il processo locale con Kubernetes evita di dover compilare e distribuire il codice nel cluster creando invece una connessione direttamente tra il computer di sviluppo e il cluster. Connettendo il computer di sviluppo al cluster durante il debug è possibile testare e sviluppare rapidamente il servizio nel contesto dell'applicazione completa senza creare una configurazione Docker o Kubernetes.

Il processo locale con Kubernetes reindirizza il traffico tra il cluster Kubernetes connesso e il computer di sviluppo. Questo reindirizzamento del traffico consente il codice nel computer di sviluppo e i servizi in esecuzione nel cluster Kubernetes per comunicare come se fossero nello stesso cluster Kubernetes. Il processo locale con Kubernetes fornisce anche un modo per replicare le variabili di ambiente e i volumi montati disponibili per i Pod nel cluster Kubernetes nel computer di sviluppo. Fornire l'accesso alle variabili di ambiente e ai volumi montati nel computer di sviluppo consente di lavorare rapidamente sul codice senza dover replicare manualmente tali dipendenze.

In questa guida si apprenderà come usare il processo locale con Kubernetes per reindirizzare il traffico tra il cluster Kubernetes e il codice in esecuzione nel computer di sviluppo. Questa guida fornisce anche uno script per la distribuzione di un'applicazione di esempio di grandi dimensioni con più microservizi in un cluster Kubernetes.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][preview-terms]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="before-you-begin"></a>Prima di iniziare

Questa guida usa l' [applicazione di esempio Azure Dev Spaces bike sharing][bike-sharing-github] per dimostrare la connessione del computer di sviluppo a un cluster Kubernetes. Se si dispone già di un'applicazione in esecuzione in un cluster Kubernetes, è comunque possibile seguire la procedura seguente e usare i nomi dei propri servizi.

### <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
* [L'interfaccia della riga di comando di Azure installata][azure-cli].
* [Visual Studio 2019][visual-studio] versione 16,7 Preview 2 o versione successiva in esecuzione in Windows 10 con i carichi di lavoro *sviluppo di ASP.NET e sviluppo Web* e *sviluppo di Azure* installati.
* INTERFACCIA della riga di comando [Azure Dev Spaces installata][azds-cli].

### <a name="enable-the-local-process-with-kubernetes-preview-feature-in-visual-studio"></a>Abilitare il processo locale con la funzionalità di anteprima di Kubernetes in Visual Studio

Per abilitare il processo locale con Kubernetes in Visual Studio, fare clic su *strumenti*  >  *Opzioni*  >  *ambiente*  >  *Anteprima funzionalità*. Selezionare *Abilita debug locale per i servizi Kubernetes*.

Inoltre, per le applicazioni console .NET, installare il pacchetto NuGet *Microsoft. VisualStudio. Azure. Kubernetes. Tools. targets* .

## <a name="create-a-kubernetes-cluster"></a>Creare un cluster Kubernetes

Creare un cluster AKS in un' [area supportata][supported-regions]. I comandi seguenti creano un gruppo di risorse denominato *MyResourceGroup* e un cluster del servizio Azure Kubernetes denominato *MyAKS*.

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --location eastus \
    --node-count 3 \
    --generate-ssh-keys
```

## <a name="install-the-sample-application"></a>Installare l'applicazione di esempio

Installare l'applicazione di esempio nel cluster usando lo script fornito. È possibile eseguire questo script nel computer di sviluppo o usando il [Azure cloud Shell][azure-cloud-shell].

```azurecli-interactive
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/
chmod +x ./local-process-quickstart.sh
./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
```

Passare all'applicazione di esempio che esegue il cluster aprendo il relativo URL pubblico, che viene visualizzato nell'output dello script di installazione.

```console
$ ./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
Defaulting Dev spaces repository root to current directory : ~/dev-spaces
Setting the Kube context
...
To try out the app, open the url:
dev.bikesharingweb.EXTERNAL_IP.nip.io
```

Nell'esempio precedente l'URL pubblico è `dev.bikesharingweb.EXTERNAL_IP.nip.io` .

## <a name="connect-to-your-cluster-and-debug-a-service"></a>Connettersi al cluster ed eseguire il debug di un servizio

Nel computer di sviluppo scaricare e configurare l'interfaccia della riga di comando di Kubernetes per connettersi al cluster Kubernetes usando [AZ AKS Get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Aprire *dev-Spaces/Samples/BikeSharingApp/ReservationEngine/app. csproj* dall' [applicazione di esempio Azure Dev Spaces bike sharing][bike-sharing-github] in Visual Studio.

Nel progetto selezionare *processo locale con Kubernetes* dall'elenco a discesa Impostazioni di avvio, come illustrato di seguito.

![Scegliere il processo locale con Kubernetes](../media/local-process-kubernetes-visual-studio/choose-local-process.png)

Fare clic sul pulsante Start accanto a *processo locale con Kubernetes*. Nella finestra di dialogo *processo locale con Kubernetes* :

* Selezionare la propria sottoscrizione.
* Selezionare *MyAKS* per il cluster.
* Selezionare *dev* per lo spazio dei nomi.
* Selezionare *reservationengine* per il servizio da reindirizzare.
* Selezionare *app* per il profilo di avvio.
* Selezionare `http://dev.bikesharingweb.EXTERNAL_IP.nip.io` l'URL per avviare il browser.

![Scegliere il processo locale con il cluster Kubernetes](../media/local-process-kubernetes-visual-studio/choose-local-process-cluster.png)

> [!IMPORTANT]
> È possibile reindirizzare solo i servizi che dispongono di un singolo POD.

Fare clic su *Salva e avviare il debug*.

Tutto il traffico nel cluster Kubernetes viene reindirizzato per il servizio *reservationengine* alla versione dell'applicazione in esecuzione nel computer di sviluppo. Il processo locale con Kubernetes instrada anche tutto il traffico in uscita dall'applicazione al cluster Kubernetes.

> [!NOTE]
> Verrà richiesto di consentire l'esecuzione di *KubernetesDNSManager* con privilegi elevati e di modificare il file degli host.

Il computer di sviluppo è connesso quando la barra di stato indica che si è connessi al servizio *reservationengine* .

![Computer di sviluppo connesso](../media/local-process-kubernetes-visual-studio/development-computer-connected.png)

> [!NOTE]
> All'avvio di subesquent, non verrà visualizzata la finestra di dialogo *processo locale con Kubernetes* . Le impostazioni vengono aggiornate nel riquadro *debug* delle proprietà del progetto.

Quando il computer di sviluppo è connesso, il traffico inizia a reindirizzare al computer di sviluppo per il servizio che si sta sostituendo.

## <a name="set-a-break-point"></a>Imposta un punto di rottura

Aprire [BikesHelper.cs][bikeshelper-cs-breakpoint] e fare clic in un punto qualsiasi della riga 26 per posizionare il cursore. Per impostare un punto di interruzione, premere *F9* oppure fare clic su *debug* e quindi su *Imposta/Rimuovi*punto

Passare all'applicazione di esempio aprendo l'URL pubblico. Selezionare *Aurelia Briggs (Customer)* come utente, quindi selezionare una bicicletta da affittare. Fare clic su *Rent Bike*. Tornare a Visual Studio e osservare che la riga 26 è evidenziata. Il punto di interruzione impostato ha sospeso il servizio alla riga 26. Per riprendere il servizio, premere *F5* oppure fare clic su *Debug* e quindi su *Continua*. Tornare al browser e verificare che la pagina indichi che la bicicletta è stata affittata.

Rimuovere il punto di interruzione inserendo il cursore sulla riga 26 in `BikesHelper.cs` e premendo *F9*.

> [!NOTE]
> Per impostazione predefinita, l'arresto dell'attività di debug disconnette anche il computer di sviluppo dal cluster Kubernetes. È possibile modificare questo comportamento modificando *Disconnetti dopo il debug* su *false* nella sezione *strumenti di debug Kubernetes* delle opzioni di debug. Dopo aver aggiornato questa impostazione, il computer di sviluppo rimarrà connesso quando si arresta e si avvia il debug. Per disconnettere il computer di sviluppo dal cluster, fare clic sul pulsante *Disconnetti* sulla barra degli strumenti.
>
> Se Visual Studio termina improvvisamente la connessione al cluster o termina, il servizio da reindirizzare potrebbe non essere ripristinato allo stato originale prima della connessione al processo locale con Kubernetes. Per risolvere questo problema, vedere la [Guida alla risoluzione dei problemi][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>Uso della registrazione e della diagnostica

È possibile trovare i log di diagnostica in `Azure Dev Spaces` directory nella [directory *temporanea* del computer di sviluppo][azds-tmp-dir].

## <a name="remove-the-sample-application-from-your-cluster"></a>Rimuovere l'applicazione di esempio dal cluster

Usare lo script fornito per rimuovere l'applicazione di esempio dal cluster.

```azurecli-interactive
./local-process-quickstart.sh -c -g MyResourceGroup -n MyAKS
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare le azioni Azure Dev Spaces e GitHub per testare le modifiche da una richiesta pull direttamente in AKS prima che la richiesta pull venga unita al ramo principale del repository.

> [!div class="nextstepaction"]
> [Azioni di GitHub & servizio Azure Kubernetes][gh-actions]

[azds-cli]: install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-cloud-shell]: ../../cloud-shell/overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[bikeshelper-cs-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/ReservationEngine/BikesHelper.cs#L26
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[visual-studio]: https://www.visualstudio.com/vs/