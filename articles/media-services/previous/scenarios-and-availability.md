---
title: Scenari e disponibilità delle funzionalità di Servizi multimediali di Microsoft Azure nei data center | Microsoft Docs
description: Questo argomento offre una panoramica degli scenari e della disponibilità delle funzionalità e dei servizi di Servizi multimediali di Microsoft Azure nei data center.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 99604092ea901458062abd6423d74ad8f1f2c8d7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014410"
---
# <a name="scenarios-and-availability-of-media-services-features-across-datacenters"></a>Scenari e disponibilità delle funzionalità di Servizi multimediali nei data center

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Non saranno aggiunte nuove caratteristiche o funzionalità a Servizi multimediali v2. <br/>Esplorare l'ultima versione, [Servizi multimediali v3](../latest/index.yml). Vedere anche le [indicazioni sulla migrazione dalla versione 2 alla versione 3](../latest/migrate-from-v2-to-v3.md)

Servizi multimediali di Microsoft Azure (AMS) consente di caricare, archiviare e codificare contenuti video o audio in modo sicuro, nonché creare pacchetti di tali contenuti per la distribuzione in streaming live e on demand a vari client (ad esempio, TV, PC e dispositivi mobili).

Servizi multimediali di Azure è compatibile con svariati data center in tutto il mondo. Questi datacenter sono raggruppati in aree geografiche, per lasciare l'utente libero di scegliere dove creare le applicazioni. Per conoscerle, è possibile consultare l' [elenco delle aree e le relative posizioni](https://azure.microsoft.com/regions/). 

Questo argomento illustra scenari comuni per la distribuzione di contenuti [live](#live_scenarios) o su richiesta. e include anche informazioni dettagliate sulla disponibilità delle funzionalità e dei servizi multimediali nei data center.

## <a name="overview"></a>Panoramica

### <a name="prerequisites"></a>Prerequisiti

Per iniziare a utilizzare Servizi multimediali di Azure, è necessario disporre di quanto segue:

* Un account Azure. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com).
* Un account di Servizi multimediali di Azure. Per altre informazioni, vedere [Creare un account](media-services-portal-create-account.md).
* L'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**.

    Quando viene creato l'account AMS, un endpoint di streaming **predefinito** viene aggiunto all'account nello stato **interrotto** . Per avviare lo streaming dei contenuti e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming deve trovarsi nello stato **In esecuzione**.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Oggetti comunemente usati nello sviluppo con il modello OData di AMS

L'immagine seguente illustra alcuni degli oggetti più comuni usati durante lo sviluppo rispetto al modello OData di Servizi multimediali.

Fare clic sull'immagine per visualizzarla a schermo intero.  

[![Diagramma che mostra alcuni degli oggetti usati più di frequente quando si sviluppa con il modello di dati degli oggetti di servizi multimediali di Azure.](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

È possibile visualizzare il modello completo [qui](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Protezione dei contenuti nella risorsa di archiviazione e distribuzione dei flussi multimediali in chiaro (non crittografati)

![Flusso di lavoro VoD](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Caricare un file multimediale di alta qualità in un asset.

    Si consiglia di applicare all'asset l'opzione di crittografia di archiviazione, in modo da proteggere i contenuti sia durante il caricamento sia mentre si trovano nella risorsa di archiviazione.
2. Codificare in un set di file MP4 a velocità in bit adattiva.

    Si consiglia di applicare all'asset di output l'opzione di crittografia di archiviazione, in modo da proteggere i contenuti anche quando non vengono usati.
3. Configurare i criteri di distribuzione degli asset (usati per la creazione dinamica dei pacchetti).

    Se l'asset è protetto con crittografia di archiviazione, è **necessario** configurare i criteri di distribuzione degli asset.
4. Pubblicare l'asset creando un localizzatore OnDemand.
5. Trasmettere in streaming i contenuti pubblicati.

Per informazioni sulla disponibilità nei data center, vedere la sezione relativa alla [disponibilità](#availability).

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Proteggere i contenuti nella risorsa di archiviazione e distribuire dinamicamente i flussi multimediali crittografati

![Protezione con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Caricare un file multimediale di alta qualità in un asset. Applicare all'asset l'opzione di crittografia di archiviazione.
2. Codificare in un set di file MP4 a velocità in bit adattiva. Applicare all'asset di output l'opzione di crittografia di archiviazione.
3. Creare una chiave di crittografia simmetrica per l'asset a cui si desidera applicare la crittografia dinamica durante la riproduzione.
4. Configurare i criteri di autorizzazione della chiave simmetrica.
5. Configurare i criteri di distribuzione degli asset (usati per la creazione dinamica dei pacchetti e la crittografia dinamica).
6. Pubblicare l'asset creando un localizzatore OnDemand.
7. Trasmettere in streaming i contenuti pubblicati.

Per informazioni sulla disponibilità nei data center, vedere la sezione relativa alla [disponibilità](#availability).

## <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Usare Analisi Servizi multimediali per derivare analisi approfondite di utilità pratica dai propri video

Analisi Servizi multimediali è una raccolta di componenti per sintesi vocale e visione artificiale che permettono a organizzazioni e aziende di derivare in modo più semplice analisi approfondite di utilità pratica dai propri file video. Per altre informazioni, vedere [Panoramica di Analisi Servizi multimediali di Azure](./legacy-components.md).

1. Caricare un file multimediale di alta qualità in un asset.
2. Elaborare i video con uno dei servizi di Analisi Servizi multimediali descritti nella [panoramica di Analisi Servizi multimediali](./legacy-components.md).
3. I processori di contenuti multimediali di Analisi Servizi multimediali producono file MP4 o JSON. Se un processore di contenuti multimediali produce un file MP4, è possibile scaricare progressivamente il file. Se un processore di contenuti multimediali produce un file JSON, è possibile scaricare il file dall'archiviazione BLOB di Azure.

Per informazioni sulla disponibilità nei data center, vedere la sezione relativa alla [disponibilità](#availability).

## <a name="deliver-progressive-download"></a>Definizione del download progressivo

1. Caricare un file multimediale di alta qualità in un asset.
2. Codificare in un singolo file MP4.
3. Pubblicare l'asset creando un localizzatore OnDemand o SAS.

    Se si usa un localizzatore SAS, i contenuti vengono scaricati dall'archiviazione BLOB di Azure. In questo caso, non è necessario avere endpoint di streaming con stato avviato.
4. Eseguire il download progressivo.

## <a name="delivering-live-streaming-events"></a><a id="live_scenarios"></a>Distribuzione di eventi di streaming live 

1. Inserire contenuti live usando vari protocolli di streaming live (ad esempio, RTMP o Smooth Streaming).
2. Facoltativamente, codificare il flusso in flusso a bitrate adattivo.
3. Visualizzare in anteprima il flusso live.
4. Distribuire i contenuti tramite protocolli di streaming comuni (ad esempio, MPEG DASH, Smooth o HLS) direttamente ai clienti oppure a una rete per la distribuzione di contenuti (CDN) per una successiva ridistribuzione.

    -oppure-

    Registrare e archiviare i contenuti inseriti affinché possano essere riprodotti in streaming in un secondo momento (video on demand).

In caso di streaming live, è possibile scegliere una delle route descritte di seguito.

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Uso di canali che ricevono un flusso live a bitrate multipli da codificatori locali (pass-through)

Il diagramma seguente illustra le parti principali della piattaforma AMS coinvolte nel flusso di lavoro **pass-through** .

![Flusso di lavoro live](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Per altre informazioni, vedere l'articolo relativo all' [uso di canali che ricevono il flusso live a velocità in bit multipla da codificatori locali](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure

Il seguente diagramma mostra i componenti principali della piattaforma AMS interessati dal flusso di lavoro di streaming live dove un canale è abilitato a eseguire la codifica live con Servizi multimediali.

![Flusso di lavoro live](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Per altre informazioni, vedere [Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure](media-services-manage-live-encoder-enabled-channels.md).

Per informazioni sulla disponibilità nei data center, vedere la sezione relativa alla [disponibilità](#availability).

## <a name="consuming-content"></a>Utilizzo di contenuto

Servizi multimediali di Azure fornisce gli strumenti necessari per creare applicazioni lettore client avanzate e dinamiche per la maggior parte delle piattaforme, inclusi dispositivi iOS, dispositivi Android, Windows, Windows Phone, Xbox e set-top box. 

## <a name="enabling-azure-cdn"></a>Abilitazione della rete CDN di Azure

Servizi multimediali supporta l'integrazione con la rete CDN di Azure. Per informazioni su come abilitare la rete CDN di Azure, vedere [Come gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a><a id="scaling"></a>Ridimensionamento di un account di Servizi multimediali

I clienti di AMS possono ridimensionare gli endpoint di streaming, l'elaborazione di contenuti multimediali e lo spazio di archiviazione nei propri account AMS.

* I clienti di Servizi multimediali possono scegliere un endpoint di streaming **Standard** o **Premium**. Un endpoint di streaming **Standard** è adatto per la maggior parte dei carichi di lavoro di streaming. Include le stesse funzionalità degli endpoint di streaming **Premium** e offre la scalabilità automatica della larghezza di banda in uscita. 

    Gli endpoint di streaming **Premium** sono ideali per i carichi di lavoro avanzati, in quanto offrono una capacità di larghezza di banda dedicata e scalabile. Per impostazione predefinita, i clienti con un endpoint di streaming **Premium** ottengono un'unità di streaming. L'endpoint di streaming può essere ridimensionato aggiungendo unità di streaming. Ogni unità di streaming fornisce all'applicazione capacità di larghezza di banda aggiuntiva. Per altre informazioni sul ridimensionamento degli endpoint di streaming **Premium**, vedere l'argomento [Ridimensionare gli endpoint di streaming](media-services-portal-scale-streaming-endpoints.md).

* Un account di Servizi multimediali è associato a un tipo di unità riservata che determina la velocità dei processi di elaborazione dei multimedia. È possibile scegliere uno dei seguenti tipi di unità riservata: **S1**, **S2** o **S3**. Lo stesso processo di codifica viene eseguito più velocemente quando si usa ad esempio il tipo di unità riservata **S2** rispetto al tipo **S1**.

    Oltre a specificare il tipo di unità riservata, è possibile specificare di effettuare il provisioning dell'account con **unità riservate** (UR). Il numero delle UR sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account.

    >[!NOTE]
    >UR di lavoro per la parallelizzazione di tutta l'elaborazione di supporti di memorizzazione, tra cui l'indicizzazione di processi tramite Azure Media Indexer. Tuttavia, a differenza della codifica, l'indicizzazione di processi non viene elaborata più velocemente con unità riservate più veloci.

    Per altre informazioni, vedere [ridimensionare l'elaborazione di contenuti multimediali](media-services-portal-scale-media-processing.md).
* È anche possibile ridimensionare l'account di Servizi multimediali aggiungendo account di archiviazione. Per ogni account di archiviazione è previsto un limite di 500 TB. Per espandere lo spazio di archiviazione oltre i limiti predefiniti, è possibile scegliere di collegare più account di archiviazione a un singolo account di Servizi Multimediali. Per altre informazioni, vedere l'argomento relativo alla [gestione degli account di archiviazione](./media-services-managing-multiple-storage-accounts.md).

## <a name="availability-of-media-services-features-across-datacenters"></a><a id="availability"></a>Disponibilità delle funzionalità di Servizi multimediali nei data center

Questa sezione offre informazioni dettagliate sulla disponibilità delle funzionalità di Servizi multimediali nei data center.

### <a name="ams-accounts"></a>Account AMS

#### <a name="availability"></a>Disponibilità

Usare i [prodotti di Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) per determinare se servizi multimediali è disponibile in un data center specifico.

### <a name="streaming-endpoints"></a>Endpoint di streaming 

I clienti di Servizi multimediali possono scegliere un endpoint di streaming **Standard** o **Premium**. Per altre informazioni, vedere la sezione relativa al [ridimensionamento](#scaling).

#### <a name="availability"></a>Disponibilità

|Nome|Stato|Data center
|---|---|---|
|Standard|GA|All|
|Premium|GA|All|

### <a name="live-encoding"></a>Codifica live

#### <a name="availability"></a>Disponibilità

È disponibile in tutti i data center tranne Germania, Brasile meridionale, India occidentale, India meridionale e India centrale. 

### <a name="encoding-media-processors"></a>Processori di contenuti multimediali di codifica

AMS offre due codificatori su richiesta: **Media Encoder Standard** e **Flusso di lavoro Premium del codificatore multimediale**. Per altre informazioni, vedere [Panoramica e confronto dei codificatori multimediali su richiesta di Azure](media-services-encode-asset.md). 

#### <a name="availability"></a>Disponibilità

|Nome processore di contenuti multimediali|Stato|Data center
|---|---|---|
|Codificatore multimediale standard|GA|All|
|Flusso di lavoro Premium del codificatore multimediale|GA|Tutti tranne Cina|

### <a name="analytics-media-processors"></a>Processori di contenuti multimediali di analisi

Analisi Servizi multimediali è una raccolta di componenti per sintesi vocale e visione artificiale che permettono a organizzazioni e aziende di derivare in modo più semplice analisi approfondite di utilità pratica dai loro file video. Per altre informazioni, vedere [Panoramica di Analisi Servizi multimediali di Azure](./legacy-components.md).

> [!NOTE]
> Alcuni processori di contenuti multimediali di analisi verranno ritirati. Per informazioni sulle date di ritiro, vedere l'argomento relativo ai [componenti legacy](legacy-components.md).

#### <a name="availability"></a>Disponibilità

|Nome processore di contenuti multimediali|Stato|Data center
|---|---|---|
|Rilevamento multimediale volti di Azure|Anteprima|Tutti|
|Azure Media Indexer|GA|All|
|Rilevatore multimediale di movimento Azure|Anteprima|Tutti|
|Riconoscimento ottico dei caratteri multimediale di Azure|Anteprima|Tutti|
|Azure Media Redactor|GA|All|
|Anteprime video multimediali di Azure|Anteprima|Tutti|

### <a name="protection"></a>Protezione

Servizi multimediali di Microsoft Azure consente di proteggere i file multimediali dal momento in cui escono dal computer fino alle fasi di archiviazione, elaborazione e recapito. Per altre informazioni, vedere l'argomento relativo alla [protezione dei contenuti di AMS](media-services-content-protection-overview.md).

#### <a name="availability"></a>Disponibilità

|Crittografia|Stato|Data center|
|---|---|---| 
|Archiviazione|GA|All|
|Chiavi AES-128|GA|All|
|Fairplay|GA|All|
|PlayReady|GA|All|
|Widevine|GA|Tutti tranne Germania, Governo federale e Cina.

### <a name="reserved-units-rus"></a>Unità riservate

Il numero delle unità riservate sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account. 

Per altre informazioni, vedere la sezione relativa al [ridimensionamento](#scaling).

#### <a name="availability"></a>Disponibilità

Sono disponibili in tutti i data center.

### <a name="reserved-unit-ru-type"></a>Tipo di unità riservata

Un account Servizi multimediali è associato a un tipo di unità riservata che determina la velocità delle attività di elaborazione di contenuti multimediali. È possibile scegliere uno dei tipi di unità riservata seguenti: S1, S2 o S3.

Per altre informazioni, vedere la sezione relativa al [ridimensionamento](#scaling).

#### <a name="availability"></a>Disponibilità

|Nome tipo di unità riservata|Stato|Data center
|---|---|---|
|S1|GA|All|
|S2|GA|Tutti tranne Brasile meridionale e India occidentale|
|S3|GA|Tutti tranne India occidentale|

## <a name="additional-notes"></a>Note aggiuntive

* Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="next-steps"></a>Passaggi successivi

Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]