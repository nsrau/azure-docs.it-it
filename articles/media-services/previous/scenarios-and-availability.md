---
title: Scenari comuni Servizi multimediali di Microsoft Azure | Microsoft Docs
description: Questo articolo offre una panoramica degli scenari di Servizi multimediali di Microsoft Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/3/2020
ms.author: inhenkel
ms.openlocfilehash: 001c535a2b39898673f2d587ee807d43b4d5f60a
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348543"
---
# <a name="microsoft-azure-media-services-common-scenarios"></a>Scenari comuni Servizi multimediali di Microsoft Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Non saranno aggiunte nuove caratteristiche o funzionalità a Servizi multimediali v2. Esplorare l'ultima versione, [Servizi multimediali v3](../latest/media-services-overview.md). Vedere anche le [indicazioni sulla migrazione dalla versione 2 alla versione 3](../latest/migrate-from-v2-to-v3.md)

Servizi multimediali di Microsoft Azure (AMS) consente di caricare, archiviare e codificare contenuti video o audio in modo sicuro, nonché creare pacchetti di tali contenuti per la distribuzione in streaming live e on demand a vari client (ad esempio, TV, PC e dispositivi mobili).

Questo articolo illustra scenari comuni per la distribuzione di contenuti live o on demand.

## <a name="overview"></a>Panoramica

### <a name="prerequisites"></a>Prerequisiti

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

    Si consiglia di applicare l'opzione di crittografia di archiviazione all'asset per proteggere il contenuto durante il caricamento e quando è inattivo nell'archiviazione.

1. Codificare in un set di file MP4 a velocità in bit adattiva.

    Si consiglia di applicare l'opzione di crittografia di archiviazione all'asset di output per proteggere il contenuto inattivo.

1. Configurare i criteri di distribuzione degli asset (usati per la creazione dinamica dei pacchetti).

    Se l'asset è protetto con crittografia di archiviazione, è **necessario** configurare i criteri di distribuzione degli asset.
1. Pubblicare l'asset creando un localizzatore OnDemand.
1. Trasmettere in streaming i contenuti pubblicati.

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Proteggere i contenuti nella risorsa di archiviazione e distribuire dinamicamente i flussi multimediali crittografati

![Protezione con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Caricare un file multimediale di alta qualità in un asset. Applicare all'asset l'opzione di crittografia di archiviazione.
1. Codificare in un set di file MP4 a velocità in bit adattiva. Applicare all'asset di output l'opzione di crittografia di archiviazione.
1. Creare una chiave di crittografia simmetrica per l'asset a cui si desidera applicare la crittografia dinamica durante la riproduzione.
1. Configurare i criteri di autorizzazione della chiave simmetrica.
1. Configurare i criteri di distribuzione degli asset (usati per la creazione dinamica dei pacchetti e la crittografia dinamica).
1. Pubblicare l'asset creando un localizzatore OnDemand.
1. Trasmettere in streaming i contenuti pubblicati.

## <a name="deliver-progressive-download"></a>Definizione del download progressivo

1. Caricare un file multimediale di alta qualità in un asset.
1. Codificare in un singolo file MP4.
1. Pubblicare l'asset creando un localizzatore OnDemand o SAS. Se si usa un localizzatore SAS, i contenuti vengono scaricati dall'archiviazione BLOB di Azure. Non è necessario che gli endpoint di streaming siano in stato avviato.
1. Eseguire il download progressivo.

## <a name="delivering-live-streaming-events"></a>Distribuzione di eventi di streaming live

1. Inserire contenuti live usando vari protocolli di streaming live (ad esempio, RTMP o Smooth Streaming).
1. Facoltativamente, codificare il flusso in flusso a bitrate adattivo.
1. Visualizzare in anteprima il flusso live.
1. Distribuire il contenuto tramite:
    1. Protocolli di streaming comuni (ad esempio, MPEG DASH, Smooth, HLS) direttamente ai clienti.
    1. A una rete per la distribuzione di contenuti (CDN) per un'ulteriore distribuzione
    1. Registrare e archiviare il contenuto inserito da trasmettere in un secondo momento (video on demand).

In caso di streaming live, è possibile scegliere una delle route descritte di seguito.

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Uso di canali che ricevono un flusso live a bitrate multipli da codificatori locali (pass-through)

Il diagramma seguente illustra le parti principali della piattaforma AMS coinvolte nel flusso di lavoro **pass-through** .

![Diagramma che mostra le parti principali della piattaforma A M, incluse nel flusso di lavoro "pass-through".](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Per altre informazioni, vedere l'articolo relativo all' [uso di canali che ricevono il flusso live a velocità in bit multipla da codificatori locali](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure

Il diagramma seguente illustra le parti principali della piattaforma AMS incluse nel flusso di lavoro di streaming live in cui è abilitato un canale per eseguire la codifica live con servizi multimediali.

![Flusso di lavoro live](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Per altre informazioni, vedere [Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure](media-services-manage-live-encoder-enabled-channels.md).

## <a name="consuming-content"></a>Utilizzo di contenuto

Servizi multimediali di Azure fornisce gli strumenti necessari per creare applicazioni lettore client avanzate e dinamiche per la maggior parte delle piattaforme, inclusi dispositivi iOS, dispositivi Android, Windows, Windows Phone, Xbox e set-top box.

## <a name="enabling-azure-cdn"></a>Abilitazione della rete CDN di Azure

Servizi multimediali supporta l'integrazione con la rete CDN di Azure. Per informazioni su come abilitare la rete CDN di Azure, vedere [Come gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a>Ridimensionamento di un account di Servizi multimediali

I clienti di AMS possono ridimensionare gli endpoint di streaming, l'elaborazione di contenuti multimediali e lo spazio di archiviazione nei propri account AMS.

* I clienti di Servizi multimediali possono scegliere un endpoint di streaming **Standard** o **Premium**. Un endpoint di streaming **Standard** è adatto per la maggior parte dei carichi di lavoro di streaming. Include le stesse funzionalità degli endpoint di streaming **Premium** e offre la scalabilità automatica della larghezza di banda in uscita.

    Gli endpoint di streaming **Premium** sono ideali per i carichi di lavoro avanzati, in quanto offrono una capacità di larghezza di banda dedicata e scalabile. Per impostazione predefinita, i clienti con un endpoint di streaming **Premium** ottengono un'unità di streaming. L'endpoint di streaming può essere ridimensionato aggiungendo unità di streaming. Ogni unità di streaming fornisce all'applicazione capacità di larghezza di banda aggiuntiva. Per altre informazioni sul ridimensionamento degli endpoint di streaming **Premium** , vedere l'argomento [Ridimensionare gli endpoint di streaming](media-services-portal-scale-streaming-endpoints.md).

* Un account di Servizi multimediali è associato a un tipo di unità riservata che determina la velocità dei processi di elaborazione dei multimedia. È possibile scegliere uno dei seguenti tipi di unità riservata: **S1** , **S2** o **S3**. Lo stesso processo di codifica viene eseguito più velocemente quando si usa ad esempio il tipo di unità riservata **S2** rispetto al tipo **S1**.

    Oltre a specificare il tipo di unità riservata, è possibile specificare di effettuare il provisioning dell'account con **unità riservate** (UR). Il numero delle UR sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account.

    > [!NOTE]
    > UR di lavoro per la parallelizzazione di tutta l'elaborazione di supporti di memorizzazione, tra cui l'indicizzazione di processi tramite Azure Media Indexer. Tuttavia, a differenza della codifica, l'indicizzazione di processi non viene elaborata più velocemente con unità riservate più veloci.

    Per altre informazioni, vedere [ridimensionare l'elaborazione di contenuti multimediali](media-services-portal-scale-media-processing.md).

* È anche possibile ridimensionare l'account di Servizi multimediali aggiungendo account di archiviazione. Per ogni account di archiviazione è previsto un limite di 500 TB. Per altre informazioni, vedere l'argomento relativo alla [gestione degli account di archiviazione](./media-services-managing-multiple-storage-accounts.md).

## <a name="next-steps"></a>Passaggi successivi

[Eseguire la migrazione a Servizi multimediali v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]