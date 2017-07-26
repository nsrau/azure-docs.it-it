---
title: Ottimizzare la distribuzione di contenuto per lo scenario in uso
description: Come ottimizzare la distribuzione di contenuto per scenari specifici
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 7dd497109964c3993742a324aace9c98f4a4050f
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---
# <a name="optimize-content-delivery-for-your-scenario"></a>Ottimizzare la distribuzione di contenuto per lo scenario in uso

Per la distribuzione di contenuto a un pubblico globale ampio, è fondamentale garantire la distribuzione ottimizzata del contenuto. La rete CDN di Azure consente di ottimizzare l'esperienza di distribuzione in base al tipo di contenuto disponibile, sia che si tratti di un sito Web, di uno streaming live, un video o un file di grandi dimensioni per il download, al fine di assicurare prestazioni migliori. Quando si crea un endpoint della rete CDN, specificare uno scenario nell'opzione di menu "ottimizzato per". La scelta determina quale ottimizzazione verrà applicata al contenuto distribuito dall'endpoint della rete CDN

Le opzioni di ottimizzazione sono progettate per usare i comportamenti basati sulle "procedure consigliate" per migliorare le prestazioni a livello di distribuzione di contenuti e migliorare l'offload dell'origine. Le scelte dello scenario influiranno sulle prestazioni modificando le configurazioni per la memorizzazione nella cache parziale, la suddivisione in blocchi di oggetti, i criteri di ripetizione in caso di errore dell'origine e così via. 

Questo documento fornisce una panoramica generale delle varie funzionalità di ottimizzazione e dello scenario in cui usarle. Per altri dettagli sulle funzionalità e le limitazioni, vedere la documentazione specifica per ogni singolo tipo di ottimizzazione.

> [!NOTE]
> Le opzioni di menu "ottimizzate per" possono variare in base al provider selezionato. I provider di rete CDN applicano il miglioramento in modi diversi, a seconda dello scenario. 

> * La rete CDN di Azure fornita da Akamai supporta la distribuzione Web generale, lo streaming multimediale generale, lo streaming multimediale di video on demand e il download di file di grandi dimensioni. 

> * La rete CDN di Azure fornita da Verizon supporta solo la distribuzione Web generale, che può essere usata con video on demand e download di file di grandi dimensioni senza selezionare un tipo di ottimizzazione.

> * Per selezionare il provider ottimale per la propria distribuzione, è consigliabile testare le prestazioni offerte dai diversi provider.

## <a name="selecting-and-configuring-optimization-types"></a>Selezione e configurazione dei tipi di ottimizzazione

È possibile iniziare la creazione di un nuovo endpoint della rete CDN selezionando dal menu a discesa quello che corrisponde maggiormente allo scenario e il tipo di contenuto da distribuire. La selezione predefinita è "Distribuzione Web generale". È possibile aggiornare l'opzione di ottimizzazione per qualsiasi endpoint Akamai esistente in qualsiasi momento. Questa modifica non interromperà la distribuzione della rete CDN.  

1. Selezionare un endpoint all'interno di un profilo Akamai Standard.

    ![Selezionare un endpoint.](./media/cdn-optimization-overview/01_Akamai.png)

2. Selezionare Ottimizzazione dalla barra laterale, quindi selezionare un tipo nell'elenco a discesa.

    ![Selezionare l'ottimizzazione e il tipo.](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Ottimizzazione per scenari specifici

È possibile ottimizzare l'endpoint della rete CDN per uno dei seguenti scenari:  

1.  Distribuzione Web generale
2.  Streaming multimediale generale
3.  Streaming multimediale di video on demand
4.  Download di file di grandi dimensioni

I quattro tipi di configurazione dell'ottimizzazione di scenari vengono illustrati di seguito. 

### <a name="general-web-delivery-optimization"></a>Ottimizzazione distribuzione Web generale

La distribuzione Web generale è l'opzione più comune. È mirata per l'ottimizzazione di contenuto Web generale, ad esempio pagine Web e applicazioni Web. Può essere usata anche per i download di file e di video.

Un sito Web tipico contiene in genere contenuto statico e dinamico. Il contenuto statico include immagini, librerie JavaScript e fogli di stile, che possono essere memorizzati nella cache e distribuiti a utenti diversi. Il contenuto dinamico è personalizzato per un singolo utente, ad esempio notizie personalizzate in base al profilo di un utente. Non è necessario inserire nella cache il contenuto dinamico perché è univoco per ogni utente, ad esempio il contenuto di un carrello della spesa. La distribuzione Web generale consente di ottimizzare l'intero sito Web.  

> [!NOTE]
> Quando si usa?

> Se si usa una rete CDN di Azure di Akamai, è possibile scegliere questa ottimizzazione quando le dimensioni medie dei file sono inferiori a 10 MB. Usare "ottimizzato per: download di file di grandi dimensioni" quando le dimensioni medie dei file superano i 10 MB.

### <a name="general-media-streaming-optimization"></a>Ottimizzazione dello streaming multimediale generale

Lo streaming multimediale generale è consigliabile se è necessario usare l'endpoint sia per lo streaming live sia per lo streaming di video on demand.

Lo streaming multimediale, ad esempio, è ora sensibile all'ora perché i pacchetti che vengono ricevuti in ritardo sul client possono compromettere l'esperienza di visualizzazione, ad esempio con frequenti memorizzazioni nel buffer del contenuto video. L'ottimizzazione dello streaming multimediale riduce la latenza di distribuzione del contenuto multimediale e offre un'esperienza di flusso uniforme agli utenti finali. 

Si tratta di uno scenario comune per i clienti di servizi multimediali di Azure. Quando si usano servizi multimediali di Azure, si otterrà un endpoint di streaming che può essere usato per lo streaming live e on demand. In questo scenario il cliente non deve passare a un altro endpoint quando passa dallo streaming live a quello on demand. L'ottimizzazione dello streaming multimediale generale supporta questo tipo di scenario.

La rete CDN di Azure fornita da Verizon consente di usare l'ottimizzazione di tipo distribuzione Web generale per distribuire contenuto multimediale.

Altre informazioni sull'ottimizzazione dello streaming multimediale sono disponibili [qui](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-streaming-optimization"></a>Ottimizzazione dello streaming di video on demand

L'ottimizzazione dello streaming multimediale video on demand (VOD) migliora il contenuto dello streaming VOD. Quando si usa un endpoint per lo streaming VOD, è possibile usare questa opzione.

La rete CDN di Azure fornita da Verizon consente di usare l'ottimizzazione di tipo distribuzione Web generale per distribuire contenuto multimediale.

Altre informazioni sull'ottimizzazione dello streaming multimediale sono disponibili [qui](cdn-media-streaming-optimization.md).

> [!NOTE]
> Quando si usa?

> Quando l'endpoint gestisce principalmente contenuto VOD. La differenza principale tra questa ottimizzazione e quella multimediale generale è il timeout per i tentativi di connessione che è molto più breve per funzionare con gli scenari di streaming live.

### <a name="large-file-download-optimization"></a>Ottimizzazione di tipo download di file di grandi dimensioni

Per la rete CDN di Azure di Akamai è necessario usare questa ottimizzazione per distribuire file di dimensioni superiori a 1,8 GB.  La rete CDN di Azure di Verizon non prevede un limite alla dimensione del download di file nell'ottimizzazione della distribuzione Web generale.

Quando si usa una rete CDN di Azure di Akamai, i download di file di grandi dimensioni sono ottimizzati per il contenuto che ha dimensioni superiori a 10 MB. Quando la dimensione media dei file è inferiore a 10 MB, è possibile usare la distribuzione Web generale. Se la dimensioni media dei file è costantemente superiore a 10 MB, ad esempio per gli aggiornamenti software e firmware, potrebbe essere più efficiente creare un endpoint separato per i file di grandi dimensioni. 

La rete CDN di Azure fornita da Verizon consente di usare l'ottimizzazione di tipo distribuzione Web generale per distribuire contenuto multimediale.

Altre informazioni sull'ottimizzazione di file di grandi dimensioni sono disponibili [qui](cdn-large-file-optimization.md).



