---
title: Riproduzione di registrazioni di più giorni - Azure
description: In questa esercitazione si apprenderà come usare le API di Servizi multimediali di Azure per riprodurre una registrazione continua di video di più giorni.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 1ec9260be7241057478b06446ac2aa53c14bcb47
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803433"
---
# <a name="tutorial-playback-of-multi-day-recordings"></a>Esercitazione: Riproduzione di registrazioni di più giorni  

Questa esercitazione si basa sull'esercitazione relativa alla [registrazione continua di video](continuous-video-recording-concept.md). In questa esercitazione si apprenderà come usare le API di Servizi multimediali di Azure per riprodurre una registrazione continua di video di più giorni dal cloud. 

Questa operazione è utile per scenari come la sicurezza pubblica, in cui è necessario conservare una registrazione dei filmati di una videocamera per più giorni (o settimane) e occasionalmente controllare parti specifiche dei filmati.

L'esercitazione illustra come:

> [!div class="checklist"]
> * Eseguire l'app di esempio che illustra come è possibile esplorare il contenuto di una registrazione di più giorni
> * Visualizzare parti selezionate della registrazione

## <a name="suggested-pre-reading"></a>Letture consigliate  

Si consiglia di leggere le pagine di documentazione seguenti:

* [Panoramica di Analisi di video live in IoT Edge](overview.md)
* [Terminologia di Analisi di video live in IoT Edge](terminology.md)
* [Concetti relativi al grafo multimediale](media-graph-concept.md)
* [Registrazione continua di video](continuous-video-recording-concept.md) 
* [Guida pratica alla riproduzione delle registrazioni](playback-recordings-how-to.md)
* [Esercitazione: Registrazione continua di video](continuous-video-recording-tutorial.md)

## <a name="prerequisites"></a>Prerequisiti

* Completare l'[esercitazione sulla registrazione continua di video](continuous-video-recording-tutorial.md). Anche se questa esercitazione e le API pertinenti illustrate nell'[esercitazione sulla registrazione continua di video](continuous-video-recording-tutorial.md) riguardano registrazioni di 5 minuti o più, è consigliabile registrare un video di almeno 5 ore. Le API usate per esplorare le registrazioni sono spiegate meglio se le registrazioni sono più lunghe.
* Si consiglia di eseguire questa esercitazione con l'[esercitazione sulla registrazione continua di video](continuous-video-recording-tutorial.md) ancora in esecuzione, ovvero quando si sta ancora registrando il video nel cloud.

## <a name="run-the-sample"></a>Eseguire l'esempio 

Nel corso dell'[esercitazione sulla registrazione continua di video](continuous-video-recording-tutorial.md) viene creato un account del servizio multimediale. Per questa esercitazione sarà necessario avere accesso completo all'API per tale account. Per creare un'entità servizio, è possibile seguire la procedura descritta in [Ottenere le credenziali per accedere all'API Servizi multimediali](../latest/access-api-howto.md?tabs=portal). Dovrebbe essere possibile ottenere dal portale di Azure un blocco JSON simile al seguente:

```
{
    "AadClientId": "<<INSERT_AZURE_AD_APP_ID_HERE>>",
    "AadSecret": "<<INSERT_AZURE_AD_APP_SECRET_HERE>>",
    "AadTenantDomain": "<<YOUR_TENANT_DOMAIN>>",
    "AadTenantId": "<<YOUR_TENANT_ID>>",
    "AccountName": "<<YOUR_ACCOUNT_NAME>>",
    "Location": "<<YOUR_REGION_NAME>>",
    "ResourceGroup": "<<YOUR_RESOURCE_GROUP>>",
    "SubscriptionId": "<<YOUR_SUBSCRIPTION_ID>>",
    "ArmAadAudience": "https://management.core.windows.net",
    "ArmEndpoint": "https://management.azure.com"
}
```

Quindi, in Visual Studio Code aprire src/ams-asset-player. Questa cartella contiene i file necessari per questa esercitazione. Aprire il file appsettings.json e copiarne il contenuto in un nuovo file appsettings.development.json. Apportare le modifiche seguenti al secondo file:

```
  "AMS" : {
    "subscriptionId" : "Use value of SubscriptionId above",
    "resourceGroup" : "Use value of ResourceGroup above",
    "accountId" : "Use value of AccountName above",
    "aadTenantId" : "Use value of AadTenantId above",
    "aadClientId" : "Use value of AadClientId above",
    "aadSecret" : "Use value of AadSecret above"
} 
```

1. In Visual Studio Code aprire la scheda **Estensioni** (oppure premere CTRL+MAIUSC+X) e cercare Hub IoT di Azure.
1. Fare clic con il pulsante destro del mouse e scegliere **Impostazioni estensione**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Impostazioni estensione&quot;:::
1. Cercare e abilitare &quot;Show Verbose Message" (Visualizza messaggio dettagliato).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Impostazioni estensione&quot;:::
1. Cercare e abilitare &quot;Show Verbose Message":::
1. <!--In Visual Studio Code, you can click-->Fare clic sull'icona Esegui a sinistra (o premere CTRL+MAIUSC+D) per visualizzare le applicazioni disponibili per l'esecuzione:

    ![Screenshot che mostra un menu in Visual Studio Code con l'elemento di esecuzione selezionato.](./media/playback-multi-day-recordings-tutorial/run.png)
1. Selezionare l'applicazione AMS Asset Player dalla casella di riepilogo a discesa, come illustrato di seguito, e premere F5 per avviare il debug.

    ![Screenshot che mostra un menu in Visual Studio Code con l'applicazione AMS Asset Player selezionata.](./media/playback-multi-day-recordings-tutorial/debug.png)

L'applicazione di esempio compilerà e avvierà l'app browser predefinita e aprirà la pagina di AMS Asset Player.

> [!NOTE]
> A seconda delle impostazioni di sicurezza del browser, è possibile che venga visualizzato un messaggio di avviso. Poiché la pagina Web viene eseguita localmente, è possibile scegliere di ignorare l'avviso.

AMS Asset Player richiede di immettere il nome di un asset di Servizi multimediali. È necessario usare il nome dell'asset usato per la registrazione di video nell'[esercitazione sulla registrazione continua di video](continuous-video-recording-tutorial.md).

Quando si digita il nome dell'asset e si preme INVIO, il codice del lettore carica l'URL di streaming. Per altre informazioni, vedere la [guida pratica alla riproduzione delle registrazioni](playback-recordings-how-to.md). Se, come consigliato, si sta ancora registrando nell'asset, il lettore lo rileverà e tenterà di inserire la riproduzione nella parte più recente del video registrato. È possibile visualizzare il timestamp (nell'ora UTC) in altro a sinistra nel lettore. Nella schermata seguente si noti come viene selezionato il pulsante "Live".

![STREAM](./media/playback-multi-day-recordings-tutorial/assetplayer1.png)
 
Sul lato destro del lettore è possibile vedere i controlli per esplorare l'archivio. Gli anni, i mesi e le date in questo controllo vengono popolati usando l'API availableMedia documentata nella [guida pratica alla riproduzione delle registrazioni](playback-recordings-how-to.md).
Quando si espande il giorno, se l'esercitazione sulla registrazione continua di video rimane in esecuzione per diverse ore, verrà visualizzato un risultato simile al seguente:

![Sfogliare l'archivio](./media/playback-multi-day-recordings-tutorial/results.png)

L'origine del feed video nell'esercitazione è un file MKV. Quando il simulatore RSTP (vedere [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) raggiunge la fine del file, termina il flusso. Il nodo di origine RTSP nel grafo multimediale rileva questo problema e ristabilisce la connessione, quindi il video riprende. Tra ogni fine del file e la riconnessione c'è un gap nell'archivio registrato, visualizzato come una nuova voce nei risultati di availableMedia.

![Risultati](./media/playback-multi-day-recordings-tutorial/assetplayer2.png)
 
Quando si fa clic su una voce qualsiasi dell'elenco, l'applicazione crea un URL di streaming con il filtro appropriato, ad esempio https://{hostname}/{locatorId}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS). Il lettore carica questo URL e la riproduzione inizia in corrispondenza del valore startTime specificato.

In alternativa, è possibile usare orari di inizio e di fine specifici, usando i controlli nella parte inferiore della pagina. È possibile usare i risultati della chiamata availableMedia, elencati sulla destra, come guida ai valori consentiti per l'ora di inizio e di fine. I vincoli dettagliati per i filtri startTime ed endTime sono documentati nella [guida pratica alla riproduzione delle registrazioni](playback-recordings-how-to.md). Effettuata la selezione dei valori relativi al tempo, quando si fa clic su Submit (Invia), l'applicazione carica il lettore con un URL di streaming simile a questo: https://{hostname}/{locatorId}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS,endTime= YYYY-MM-DDTHH:MM:SS). La riproduzione inizierà in corrispondenza del valore startTime indicato.

## <a name="next-steps"></a>Passaggi successivi

[Registrazione video basata su eventi nel cloud e riproduzione dal cloud](event-based-video-recording-tutorial.md)
