---
title: Come eseguire lo streaming live con Servizi multimediali di Azure per creare flussi a bitrate multipli con il portale di Azure | Documentazione Microsoft
description: "Questa esercitazione illustra i passaggi per creare un canale che riceve un flusso live a velocità in bit singola e lo codifica in un flusso a più velocità in bit utilizzando il portale di Azure."
services: media-services
documentationcenter: 
author: anilmur
manager: cfowler
editor: 
ms.assetid: 504f74c2-3103-42a0-897b-9ff52f279e23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: e764936afda8bd498f97a8dc3426136815c18a5a
ms.contentlocale: it-it
ms.lasthandoff: 01/13/2017

---
# <a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-the-azure-portal"></a>Come eseguire lo streaming live con Servizi multimediali di Azure per creare flussi a bitrate multipli con il portale di Azure
> [!div class="op_single_selector"]
> * [Portale](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [API REST](https://docs.microsoft.com/rest/api/media/operations/channel)
> 
> 

Questa esercitazione illustra i passaggi per creare un **canale** che riceve un flusso live a velocità in bit singola e lo codifica in un flusso a più velocità in bit.

> [!NOTE]
> Per altre informazioni concettuali sui canali correlati abilitati per la codifica live, vedere [Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure](media-services-manage-live-encoder-enabled-channels.md).
> 
> 

## <a name="common-live-streaming-scenario"></a>Scenario comune di streaming live
Di seguito sono descritti i passaggi generali relativi alla creazione di applicazioni comuni di streaming live.

> [!NOTE]
> Attualmente, la durata massima consigliata per un evento live è 8 ore. Se è necessario eseguire un canale per una durata superiore, contattare amslived in Microsoft.com.
> 
> 

1. Connettere una videocamera a un computer. Avviare e configurare un codificatore live locale che può restituire un flusso a velocità in bit singola in uno dei protocolli seguenti: RTMP, Smooth Streaming o RTP (MPEG-TS). Per altre informazioni, vedere l'argomento relativo a [codificatori live e supporto RTMP di Servizi multimediali di Azure](http://go.microsoft.com/fwlink/?LinkId=532824).
   
    Questa operazione può essere eseguita anche dopo la creazione del canale.
2. Creare e avviare un canale. 
3. Recuperare l'URL di inserimento del canale. 
   
    L'URL di inserimento viene usato dal codificatore live per inviare il flusso al canale.
4. Recuperare l'URL di anteprima del canale. 
   
    Usare questo URL per verificare che il canale riceva correttamente il flusso live.
5. Creare un evento o un programma, l'operazione creerà anche un asset. 
6. Pubblicare l'evento. L'operazione creerà un localizzatore OnDemand per l'asset associato.    
7. Avviare l'evento quando si è pronti ad avviare lo streaming e l'archiviazione.
8. Facoltativamente, il codificatore live può ricevere il segnale per l'avvio di un annuncio. L'annuncio viene inserito nel flusso di output.
9. Arrestare l'evento ogni volta che si vuole arrestare lo streaming e l'archiviazione dell'evento.
10. Eliminare l'evento e, facoltativamente, l'asset.   

## <a name="in-this-tutorial"></a>Contenuto dell'esercitazione:
In questa esercitazione il portale di Azure viene usato per eseguire le attività seguenti: 

1. Creare un canale abilitato per eseguire la codifica live.
2. Ottenere l'URL di inserimento per fornirlo al codificatore live. Il codificatore live userà questo URL per inserire il flusso nel canale.
3. Creare un evento o un programma e un asset.
4. Pubblicare l'asset e ottenere gli URL di streaming.  
5. Riprodurre i contenuti.
6. Pulizia.

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione è necessario quanto segue.

* Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. 
  Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* Account di Servizi multimediali. Per creare un account di Servizi multimediali, vedere l'argomento relativo alla [creazione di un account](media-services-portal-create-account.md).
* Una webcam e un codificatore in grado di inviare un flusso live a velocità in bit singola.

## <a name="create-a-channel"></a>Creare un CANALE
1. Nel [portale di Azure](https://portal.azure.com/) selezionare Servizi multimediali e fare clic sul nome dell'account Servizi multimediali.
2. Selezionare **Streaming live**.
3. Selezionare **Creazione personalizzata**. Questa opzione permette di creare un canale abilitato per la codifica live.
   
    ![Creare un CANALE](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
4. Fare clic su **Impostazioni**.
   
   1. Scegliere il tipo di canale **Codifica live** . Questo tipo specifica che si vuole creare un canale abilitato per la codifica live. Ciò significa che il flusso in ingresso a velocità in bit singola viene inviato al canale e codificato in un flusso a più velocità in bit usando le impostazioni del codificatore live specificato. Per altre informazioni, vedere [Streaming live con Servizi multimediali di Azure per creare flussi a bitrate multipli](media-services-manage-live-encoder-enabled-channels.md). Fare clic su OK.
   2. Specificare il nome di un canale.
   3. Fare clic su OK nella parte inferiore della schermata.
5. Selezionare la scheda **Inserisci** .
   
   1. In questa pagina è possibile selezionare un protocollo di streaming. Per il tipo di canale **Codifica live** , le opzioni del protocollo valide sono le seguenti:
      
      * MP4 frammentato (Smooth Streaming) a velocità in bit singola
      * RTMP a velocità in bit singola
      * RTP (MPEG-TS): MPEG-2 Transport Stream su RTP.
        
        Per una descrizione dettagliata di ogni protocollo, vedere [Streaming live con Servizi multimediali di Azure per creare flussi a bitrate multipli](media-services-manage-live-encoder-enabled-channels.md).
        
        Non è possibile modificare l'opzione relativa al protocollo durante l'esecuzione del canale o dei relativi eventi o programmi associati. Se sono necessari protocolli diversi, è necessario creare canali separati per ciascun protocollo di streaming.  
   2. È possibile applicare la restrizione IP all'inserimento. 
      
       È possibile definire gli indirizzi IP autorizzati a inserire video in questo canale. Gli indirizzi IP consentiti possono essere specificati come un singolo indirizzo IP, ad esempio '10.0.0.1', come un intervallo IP usando un indirizzo IP e una subnet mask CIDR, ad esempio '10.0.0.1/22' o come un intervallo IP usando un indirizzo IP e una subnet mask decimale puntata, ad esempio '10.0.0.1(255.255.252.0)'.
      
       Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.
6. Nella scheda **Anteprima** applicare la restrizione IP sull'anteprima.
7. Nella scheda **Codifica** specificare il set di impostazioni di codifica. 
   
    Attualmente, l'unica impostazione predefinita di sistema che è possibile selezionare è **720p (valore predefinito)**. Per specificare un set di impostazioni personalizzato, aprire un ticket di supporto Microsoft. Quindi, immettere il nome del set di impostazioni creato automaticamente. 

> [!NOTE]
> Attualmente, l'avvio del canale può richiedere più di 30 minuti. La reimpostazione del canale può richiedere fino a 5 minuti.
> 
> 

Dopo aver creato il canale è possibile farvi clic e passare a **Impostazioni** , dove vengono visualizzate le configurazioni dei canali. 

Per altre informazioni, vedere [Streaming live con Servizi multimediali di Azure per creare flussi a bitrate multipli](media-services-manage-live-encoder-enabled-channels.md).

## <a name="get-ingest-urls"></a>Ottenere gli URL di inserimento
Dopo avere creato il canale, è possibile ottenere gli URL di inserimento da fornire al codificatore live. Questi URL vengono usati dal codificatore per inserire un flusso live.

![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)

## <a name="create-and-manage-events"></a>Creare e gestire eventi
### <a name="overview"></a>Overview
Un canale è associato a programmi o eventi che consentono di controllare la pubblicazione e l'archiviazione di segmenti in un flusso live. Eventi e programmi sono gestiti dai canali. La relazione tra queste due entità è molto simile a quella che intercorre tra di essi nei media tradizionali, in cui un canale è costituito da un flusso costante di contenuti, mentre un programma ha come ambito una serie di eventi programmati sul canale.

È possibile specificare il numero di ore per cui si vuole mantenere il contenuto registrato per l'evento impostando la durata dell' **Intervallo di archiviazione** . Il valore impostato può essere compreso tra 5 minuti e 25 ore. La lunghezza della finestra di archiviazione determina anche il limite di tempo per cui i client possono eseguire ricerche a ritroso nel tempo dalla posizione live corrente. Gli eventi possono essere eseguiti per l'intervallo di tempo specificato, ma il contenuto che supera la durata prevista viene scartato in modo continuo. Il valore della proprietà determina anche il tempo per cui i manifesti client possono crescere.

Ogni evento è associato a un asset. Per pubblicare l'evento, è necessario creare un localizzatore OnDemand per l'asset associato. Con questo localizzatore sarà possibile creare un URL di streaming da fornire ai client.

Un canale supporta fino a tre eventi in esecuzione simultanea, quindi consente di creare più archivi dello stesso flusso in ingresso. Questo consente di pubblicare e archiviare parti diverse di un evento a seconda delle necessità. Si consideri ad esempio uno scenario in cui un'azienda richiede l'archiviazione di sei ore di un evento e la trasmissione solo degli ultimi 10 minuti. A tale scopo, è necessario creare due eventi in esecuzione simultanea. Uno è impostato per l'archiviazione di sei ore dell'evento, ma il programma non viene pubblicato. L'altro è impostato per l'archiviazione di 10 minuti e il programma viene pubblicato.

Non riutilizzare i programmi esistenti per nuovi eventi. Al contrario, creare e avviare un nuovo programma per ogni evento.

Avviare un programma o un evento quando si è pronti ad avviare lo streaming e l'archiviazione. Arrestare l'evento ogni volta che si vuole arrestare lo streaming e l'archiviazione dell'evento. 

Per eliminare il contenuto archiviato, arrestare ed eliminare l'evento e quindi eliminare l'asset associato. Non è possibile eliminare un asset che sia usato da un evento. Per eliminarlo è prima necessario eliminare l'evento. 

Anche dopo l'arresto e l'eliminazione dell'evento, gli utenti saranno in grado di riprodurre in streaming il contenuto archiviato sotto forma di video on demand, finché non si elimina l'asset.

Se si desidera mantenere il contenuto archiviato ma non averlo disponibile per lo streaming, eliminare il localizzatore di streaming.

### <a name="createstartstop-events"></a>Creare, avviare o arrestare eventi
Dopo l'avvio del flusso nel canale, è possibile iniziare l'evento di streaming creando un localizzatore di asset, programma e streaming. In questo modo il flusso viene archiviato e reso disponibile agli utenti tramite l'endpoint di streaming. 

>[!NOTE]
>Quando l'account AMS viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. 

Per avviare l'evento è possibile procedere in due modi: 

1. Nella pagina **Canale** fare clic su **Evento live** per aggiungere un nuovo evento.
   
    Specificare il nome dell'evento, il nome dell'asset, l'intervallo di archiviazione e l'opzione di crittografia.
   
    ![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
   
    Se l'opzione **Pubblica subito questo evento live** è stata lasciata selezionata, verranno creati gli URL di pubblicazione dell'evento.
   
    Per avviare lo streaming dell'evento, fare clic su **Avvia**.
   
    Dopo aver avviato l'evento, è possibile fare clic su **Guarda** per avviare la riproduzione del contenuto.
2. In alternativa, è possibile usare un collegamento e fare clic sul pulsante **Go Live** nella pagina **Canale**. In questo modo verrà creato un localizzatore predefinito di asset, programma e streaming.
   
    L'evento è denominato **default** e l'intervallo di archiviazione è impostato su otto ore.

Per guardare l'evento pubblicato è possibile usare la pagina **Evento live** . 

Facendo clic su **Sospendi trasmissione**vengono arrestati tutti gli eventi live. 

## <a name="watch-the-event"></a>Guardare l'evento
Per guardare l'evento, fare clic su **Watch** (Guarda) nel portale di Azure o copiare l'URL di streaming e usare un lettore a propria scelta. 

![Data di creazione](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

Quando viene arrestato, l'evento live converte automaticamente gli eventi in contenuto su richiesta.

## <a name="clean-up"></a>Eseguire la pulizia
Se al termine dello streaming degli eventi si vuole eliminare le risorse di cui in precedenza è stato effettuato il provisioning, attenersi alla procedura seguente.

* Interrompere il push del flusso dal codificatore.
* Arrestare il canale. Una volta arrestato, il canale non subirà modifiche. Quando occorrerà riavviarlo, avrà lo stesso URL di inserimento, per cui non sarà necessario riconfigurare il codificatore.
* È possibile arrestare l'endpoint di streaming, a meno che non si voglia continuare a fornire l'archivio dell'evento live come flusso su richiesta. Se il canale è arrestato, non subirà modifiche.

## <a name="view-archived-content"></a>Visualizzare il contenuto archiviato
Anche dopo l'arresto e l'eliminazione dell'evento, gli utenti saranno in grado di riprodurre in streaming il contenuto archiviato sotto forma di video on demand, finché non si elimina l'asset. Un asset non può essere eliminato se è usato da un evento. Per farlo, eliminare prima l'evento. 

Per gestire gli asset, selezionare**Impostazione** e fare clic su **Asset**.

![asset](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

## <a name="considerations"></a>Considerazioni
* Attualmente, la durata massima consigliata per un evento live è 8 ore. Se è necessario eseguire un canale per lunghi periodi di tempo, contattare amslived in Microsoft.com.
* Verificare che l'endpoint di streaming da cui si vuole trasmettere il contenuto sia nello stato **In esecuzione**.

## <a name="next-step"></a>Passaggio successivo
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


