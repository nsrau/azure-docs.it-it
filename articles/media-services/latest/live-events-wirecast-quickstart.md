---
title: Creare un evento di streaming live di Servizi multimediali di Azure
description: Informazioni su come creare un evento di streaming live di Servizi multimediali di Azure con il portale e Wirecast
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: 76bbb980b6430f7cffc23ec078e2c932128dc637
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265270"
---
# <a name="create-an-azure-media-services-live-stream"></a>Creare un evento di streaming live di Servizi multimediali di Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo argomento di avvio rapido consente di creare un evento di streaming live di Servizi multimediali di Azure con il portale di Azure e Telestream Wirecast. Si presuppone che si abbia una sottoscrizione di Azure e che sia stato creato un account di Servizi multimediali di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Aprire il Web browser e passare al [portale di Microsoft Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

In questo argomento di avvio rapido verrà illustrato come:

- Configurare un codificatore locale con una versione di valutazione gratuita di Telestream Wirecast.
- Configurare un evento di streaming live.
- Configurare gli output dell'evento di streaming live.
- Eseguire un endpoint di streaming predefinito.
- Usare Azure Media Player per visualizzare l'evento di streaming live e l'output su richiesta.

Per semplicità, si useranno un set di impostazioni di codifica per Servizi multimediali di Azure in Wirecast, la codifica cloud pass-through e RTMP.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Configurare un codificatore locale con Wirecast

1. Scaricare e installare Wirecast per il sistema operativo in uso sul [sito Web Telestream](https://www.telestream.net).
1. Avviare l'applicazione e usare l'indirizzo di posta elettronica preferito per registrare il prodotto. Tenere aperta l'applicazione.
1. Nel messaggio di posta elettronica ricevuto, verificare il proprio indirizzo. Quindi, verrà avviata la versione di valutazione dell'applicazione.
1. Consigliato: guardare l'esercitazione video nella schermata di apertura dell'applicazione.

## <a name="set-up-an-azure-media-services-live-stream"></a>Configurare un evento di streaming live di Servizi multimediali di Azure

1. Passare all'account di Servizi multimediali di Azure all'interno del portale, quindi selezionare **Streaming live** nell'elenco **Servizi multimediali**.

   ![Collegamento a streaming live](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. Selezionare **Aggiungi evento live** per creare un nuovo evento di streaming live.

   ![Icona Aggiungi evento live](media/live-events-wirecast-quickstart/add-live-event.png)
1. Immettere un nome per il nuovo evento, ad esempio *TestLiveEvent*, nella casella **Nome evento live**.

   ![Casella Nome evento live](media/live-events-wirecast-quickstart/live-event-name.png)
1. Immettere una descrizione facoltativa dell'evento nella casella **Descrizione**.
1. Selezionare l'opzione **Pass-through - nessuna codifica cloud**.

   ![Opzione per la codifica cloud](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Selezionare l'opzione **RTMP**.
1. Assicurarsi che per **Avvia evento live** sia selezionata l'opzione **No**, per evitare di ricevere addebiti per l'evento live prima che sia pronto. La fatturazione inizierà dopo l'avvio dell'evento live.

   ![Opzione Avvia evento live](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. Selezionare il pulsante **Rivedi e crea** per rivedere le impostazioni.
1. Selezionare il pulsante **Crea** per creare l'evento live. Verrà visualizzato di nuovo l'elenco di eventi live.
1. Selezionare il collegamento all'evento live appena creato. Si noti che l'evento è stato arrestato.
1. Lasciare aperta questa pagina nel browser. Verrà usato più tardi.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Configurare un evento di streaming live con Wirecast Studio

1. Nell'applicazione Wirecast scegliere **Create Empty Documento** (Crea documento vuoto) dal menu principale, quindi selezionare **Continua**.

   ![Schermata iniziale di Wirecast](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Passare il puntatore del mouse sul primo livello nell'area di **livelli Wirecast**.  Selezionare l'icona **Aggiungi** visualizzata e quindi l'input video da trasmettere in streaming.

   ![Icona Aggiungi di Wirecast](media/live-events-wirecast-quickstart/add-icon.png)

   Viene visualizzata la finestra di dialogo **Master Layer 1** (Livello master 1).
1. Scegliere **Video Capture** (Acquisizione video) dal menu e quindi selezionare la fotocamera da usare.

   ![Area di anteprima per acquisizione video](media/live-events-wirecast-quickstart/video-shot-selection.png)

   Nell'area di anteprima viene mostrata la vista della fotocamera.
1. Passare il puntatore del mouse sul secondo livello nell'area di **livelli Wirecast**. Selezionare l'icona **Aggiungi** visualizzata e quindi l'input audio da trasmettere in streaming. Viene visualizzata la finestra di dialogo **Master Layer 2** (Livello master 2).
1. Scegliere **Audio capture** (Acquisizione audio) dal menu e quindi selezionare l'input audio da usare.

   ![Input per l'acquisizione audio](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. Scegliere **Output settings** (Impostazioni output) dal menu principale. Viene visualizzata la finestra di dialogo **Select an Output Destination** (Seleziona destinazione di output).
1. Selezionare **Servizi multimediali di Azure** nell'elenco a discesa **Destinazione**. Per Servizi multimediali di Azure vengono popolate automaticamente la *maggior parte* delle impostazioni di output.

   ![Schermata di impostazioni di output di Wirecast](media/live-events-wirecast-quickstart/azure-media-services.png)


Nella procedura successiva si tornerà in Servizi multimediali di Azure nel browser per copiare l'URL di input da immettere nelle impostazioni di output:

1. Nella pagina Servizi multimediali di Azure del portale selezionare **Avvia** per avviare l'evento di streaming live. La fatturazione inizia in questo momento.

   ![Icona Avvia](media/live-events-wirecast-quickstart/start.png)
2. Selezionare l'interruttore **Sicuro/Non sicuro** per impostarlo su **Non sicuro**. Questo passaggio imposta il protocollo su RTMP invece che su RTMPS.
3. Copiare l'URL della casella **URL di input** negli Appunti.

   ![URL di input](media/live-events-wirecast-quickstart/input-url.png)
4. Passare all'applicazione Wirecast e incollare l'**URL di input** nel campo **Address** (Indirizzo) delle impostazioni di output.

   ![URL di input di Wirecast](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Selezionare **OK**.

## <a name="set-up-outputs"></a>Configurare gli output

In questa parte verranno configurati gli output e sarà possibile salvare una registrazione dell'evento di streaming live.  

> [!NOTE]
> Per trasmettere in streaming questo output, è necessario che l'endpoint di streaming sia in esecuzione. Vedere la sezione [Eseguire l'endpoint di streaming predefinito](#run-the-default-streaming-endpoint) più avanti.

1. Selezionare il collegamento **Create outputs** (Crea output) sotto il visualizzatore di video **output**.
1. Se si desidera, modificare il nome dell'output nella casella **Name** (Nome) specificandone uno più intuitivo e facile da trovare in seguito.
   
   ![Casella del nome di output](media/live-events-wirecast-quickstart/output-name.png)
1. Lasciare invariate tutte le restanti caselle per il momento.
1. Selezionare **Avanti** per aggiungere un localizzatore di streaming.
1. Cambiare il nome del localizzatore specificandone uno più intuitivo, se si desidera.
   
   ![Casella del nome del localizzatore](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Lasciare invariati tutti gli altri campi della schermata per il momento.
1. Selezionare **Create** (Crea).

## <a name="start-the-broadcast"></a>Avviare la trasmissione

1. In Wirecast selezionare **Output** > **Start/Stop broadcasting** > **Start Azure Media Services (Output > Avvia/Arresta trasmissione > Avvia Servizi multimediali di Azure): Servizi multimediali di Azure** dal menu principale.

   ![Voci di menu di avvio della trasmissione](media/live-events-wirecast-quickstart/start-broadcast.png)

   Una volta inviato lo streaming all'evento live, nel lettore video della pagina dell'evento live in Servizi multimediali di Azure viene visualizzata la finestra **Live** di Wirecast.

1. Selezionare il pulsante **Go** (Vai) sotto la finestra di anteprima per avviare la trasmissione del video e dell'audio selezionati per i livelli di Wirecast.

   ![Pulsante Go (Vai) di Wirecast](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Se si verifica un errore, provare a ricaricare il lettore facendo clic sul collegamento **Reload player** (Ricarica lettore).

## <a name="run-the-default-streaming-endpoint"></a>Eseguire l'endpoint di streaming predefinito

1. Selezionare **Endpoint di streaming** nell'elenco Servizi multimediali.

   ![Voce di menu Endpoint di streaming](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Se lo stato dell'endpoint di streaming predefinito è arrestato, selezionarlo. Verrà visualizzata la pagina relativa a questo endpoint.
1. Selezionare **Start**.
   
   ![Pulsante Avvia per l'endpoint di streaming](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Riprodurre l'output trasmesso con Azure Media Player

1. Copiare l'URL di streaming sotto il lettore video **Output**.
1. In un Web browser aprire la [demo di Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Incollare l'URL di streaming nella casella **URL** di Azure Media Player.
1. Selezionare il pulsante **Update Player** (Aggiorna lettore).
1. Selezionare l'icona **Riproduci** sul video per visualizzare lo streaming live.

## <a name="stop-the-broadcast"></a>Arrestare la trasmissione

Quando si ritiene di avere trasmesso un contenuto sufficiente, arrestare la trasmissione.

1. In Wirecast selezionare il pulsante **Broadcast**. La trasmissione verrà arrestata da Wirecast.
1. Nel portale selezionare **Arresta**. Viene quindi visualizzato un messaggio di avviso che indica che lo streaming live verrà arrestato, ma che l'output diventerà ora un asset su richiesta.
1. Selezionare **Arresta** nel messaggio di avviso. Azure Media Player ora visualizzerà un errore, perché lo streaming live non è più disponibile.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Riprodurre l'output su richiesta con Azure Media Player

L'output creato è ora disponibile per lo streaming su richiesta, purché l'endpoint di streaming sia in esecuzione.

1. Passare all'elenco Servizi multimediali e selezionare **Asset**.
1. Trovare l'output dell'evento creato in precedenza e selezionare il collegamento all'asset. Viene visualizzata la pagina di output dell'asset.
1. Copiare l'URL di streaming sotto il lettore video per l'asset.
1. Tornare in Azure Media Player nel browser e incollare l'URL di streaming nella casella URL.
1. Selezionare **Update Player** (Aggiorna il lettore).
1. Selezionare l'icona **Riproduci** nel video per visualizzare l'asset su richiesta.

## <a name="clean-up-resources"></a>Pulire le risorse

> [!IMPORTANT]
> Arrestare i servizi. Una volta completati i passaggi di questo argomento di avvio rapido, assicurarsi di arrestare l'evento live e l'endpoint di streaming, altrimenti si riceveranno addebiti per il tempo in cui rimangono in esecuzione. Per arrestare l'evento live, vedere i passaggi 2 e 3 della procedura [Arrestare la trasmissione](#stop-the-broadcast).

Per arrestare l'endpoint di streaming:

1. Nell'elenco Servizi multimediali selezionare **Endpoint di streaming**.
2. Selezionare l'endpoint di streaming predefinito avviato in precedenza. Verrà visualizzata la pagina dell'endpoint.
3. Selezionare **Arresta**.

> [!TIP]
> Se non si vogliono mantenere gli asset di questo evento, assicurarsi di eliminarli per evitare di ricevere addebiti per l'archiviazione.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eventi live e output live in Servizi multimediali](./live-events-outputs-concept.md)
