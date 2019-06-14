---
title: Ottimizzare la rete CDN di Azure per il tipo di distribuzione di contenuti
description: Ottimizzare la rete CDN di Azure per il tipo di distribuzione di contenuti
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: magattus
ms.openlocfilehash: 954d19fb557540e4fdc6b17f313127e01eba97a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60636327"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Ottimizzare la rete CDN di Azure per il tipo di distribuzione di contenuti

Quando si distribuisce il contenuto a un pubblico globale ampio, è fondamentale garantirne la distribuzione ottimizzata. La [Rete di distribuzione dei contenuti di Microsoft Azure (rete CDN)](cdn-overview.md) può ottimizzare l'esperienza di distribuzione in base al tipo di contenuti disponibile. I contenuti possono essere un sito Web, un flusso live, un video o un file di grandi dimensioni per il download. Quando si crea un endpoint della rete CDN, specificare uno scenario nell'opzione di menu **Ottimizzato per**. La scelta determina quale ottimizzazione verrà applicata al contenuto distribuito dall'endpoint della rete per la distribuzione di contenuti.

Le opzioni di ottimizzazione sono progettate per usare i comportamenti basati sulle procedure consigliate per migliorare le prestazioni a livello di distribuzione di contenuti e l'offload dell'origine. Le scelte dello scenario influiranno sulle prestazioni modificando le configurazioni per la memorizzazione nella cache parziale, la suddivisione in blocchi di oggetti e i criteri di ripetizione in caso di errore dell'origine. 

Questo articolo offre una panoramica delle varie funzionalità di ottimizzazione e dello scenario in cui usarle. Per altre informazioni sulle funzionalità e le limitazioni, vedere la documentazione specifica per ogni singolo tipo di ottimizzazione.

> [!NOTE]
> Quando si crea un endpoint della rete CDN, le opzioni **Ottimizzato per** possono variare in base al tipo di profilo in cui viene creato l'endpoint. I provider di Rete CDN di Azure applicano il miglioramento in modi diversi, a seconda dello scenario. 

## <a name="provider-options"></a>Opzioni del provider

I profili di **rete CDN Standard di Azure con tecnologia Microsoft** supportano le ottimizzazioni seguenti:

* [Distribuzione Web generale](#general-web-delivery). Questa ottimizzazione viene usata anche per scaricare flussi multimediali e file di grandi dimensioni.

> [!NOTE]
> Accelerazione sito dinamico da Microsoft è disponibile tramite [servizio di ingresso principale di Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

I profili di **rete CDN Standard di Azure con tecnologia Verizon** e **rete CDN Premium di Azure con tecnologia Verizon** supportano le ottimizzazioni seguenti:

* [Distribuzione Web generale](#general-web-delivery). Questa ottimizzazione viene usata anche per scaricare flussi multimediali e file di grandi dimensioni.

* [Accelerazione sito dinamico](#dynamic-site-acceleration) 


I profili di **rete CDN Standard di Azure con tecnologia Akamai** supportano le ottimizzazioni seguenti:

* [Distribuzione Web generale](#general-web-delivery) 

* [Streaming multimediale generale](#general-media-streaming)

* [Streaming multimediale di video on demand](#video-on-demand-media-streaming)

* [Download di file di grandi dimensioni](#large-file-download)

* [Accelerazione sito dinamico](#dynamic-site-acceleration) 

Per selezionare il provider ottimale per la propria distribuzione, Microsoft consiglia di testare le prestazioni offerte dai diversi provider.

## <a name="select-and-configure-optimization-types"></a>Selezionare e configurare i tipi di ottimizzazione

Quando si crea un nuovo endpoint, selezionare un tipo di ottimizzazione che meglio corrisponde allo scenario e al tipo di contenuto che si vuole distribuire tramite l'endpoint. La selezione predefinita è **Distribuzione Web generale**. Per gli endpoint **rete CDN Standard di Azure con tecnologia Akamai** esistenti, è possibile aggiornare l'opzione di ottimizzazione in qualsiasi momento. Questa modifica non interrompe la distribuzione dalla rete CDN di Azure. 

1. In un profilo di **rete CDN Standard di Azure con tecnologia Akamai** selezionare un endpoint.

    ![Selezione dell'endpoint](./media/cdn-optimization-overview/01_Akamai.png)

2. In IMPOSTAZIONI selezionare **Ottimizzazione**. Quindi selezionare un tipo dall'elenco a discesa **Ottimizzato per**.

    ![Selezione dell'ottimizzazione e del tipo](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Ottimizzazione per scenari specifici

È possibile ottimizzare l'endpoint della rete CDN per uno di questi scenari. 

### <a name="general-web-delivery"></a>Distribuzione Web generale

La distribuzione Web generale è l'opzione di ottimizzazione più comune. È mirata per l'ottimizzazione di contenuto Web generale, ad esempio pagine Web e applicazioni Web. Può essere usata anche per i download di file e video.

Un sito Web tipico contiene contenuto statico e dinamico. Il contenuto statico include immagini, librerie JavaScript e fogli di stile, che possono essere memorizzati nella cache e distribuiti a utenti diversi. Il contenuto dinamico è personalizzato per un singolo utente, ad esempio notizie personalizzate in base al profilo di un utente. Il contenuto dinamico, ad esempio il contenuto di un carrello della spesa, non viene memorizzato nella cache perché è univoco per ogni utente. La distribuzione Web generale consente di ottimizzare l'intero sito Web. 

> [!NOTE]
> Se si usa un profilo di **rete CDN Standard di Azure con tecnologia Akamai**, selezionare questo tipo di ottimizzazione se le dimensioni medie dei file sono inferiori a 10 MB. In caso contrario, se le dimensioni medie dei file sono maggiori di 10 MB, selezionare **Download di file di grandi dimensioni** nell'elenco a discesa **Ottimizza per** .

### <a name="general-media-streaming"></a>Streaming multimediale generale

Selezionare il tipo di ottimizzazione Streaming multimediale generale se è necessario usare l'endpoint sia per lo streaming live sia per lo streaming di video on demand.

Per lo streaming multimediale il tempo è significativo, perché i pacchetti che arrivano in ritardo nel client possono compromettere l'esperienza di visualizzazione, ad esempio con frequenti memorizzazioni nel buffer del contenuto video. L'ottimizzazione dello streaming multimediale riduce la latenza di distribuzione del contenuto multimediale e offre un'esperienza di flusso uniforme agli utenti. 

Si tratta di uno scenario comune per i clienti di servizi multimediali di Azure. Quando si usano i servizi multimediali di Azure, si ottiene un singolo endpoint di streaming che può essere usato per lo streaming live e on demand. In questo scenario i clienti non devono passare a un altro endpoint quando passa dallo streaming live a quello on demand. L'ottimizzazione dello streaming multimediale generale supporta questo tipo di scenario.

Per la **rete CDN Standard di Azure con tecnologia Microsoft**, la **rete CDN Standard di Azure con tecnologia Verizon** e la **rete CDN Premium di Azure con tecnologia Verizon**, usare il tipo di ottimizzazione della distribuzione di contenuti Web generale per distribuire il contenuto multimediale in streaming.

Per altre informazioni sull'ottimizzazione dello streaming multimediale, vedere [Ottimizzazione dello streaming multimediale](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Streaming multimediale di video on demand

L'ottimizzazione dello streaming multimediale di video on demand migliora il contenuto dello streaming di video on demand. Se si usa un endpoint per lo streaming di video on demand, usare questa opzione.

Per i profili di **rete CDN Standard di Azure con tecnologia Microsoft**, **rete CDN Standard di Azure con tecnologia Verizon** e **rete CDN Premium di Azure con tecnologia Verizon**, usare il tipo di ottimizzazione della distribuzione di contenuti Web generale per distribuire contenuto multimediale in streaming video on demand.

Per altre informazioni sull'ottimizzazione dello streaming multimediale, vedere [Ottimizzazione dello streaming multimediale](cdn-media-streaming-optimization.md).

> [!NOTE]
> Se l'endpoint della rete CDN viene usato principalmente per il contenuto di video on demand, usare questo tipo di ottimizzazione. La differenza principale tra questo tipo di ottimizzazione e quello multimediale generale è il timeout per i tentativi di connessione. Il timeout è molto più breve per gli scenari di streaming live.
>

### <a name="large-file-download"></a>Download di file di grandi dimensioni

Per i profili di **rete CDN Standard di Azure con tecnologia Akamai**, i download di file di grandi dimensioni sono ottimizzati per contenuto di dimensioni superiori a 10 MB. Se le dimensioni medie dei file sono inferiori a 10 MB, usare la distribuzione Web generale. Se la dimensioni media dei file è costantemente superiore a 10 MB potrebbe essere più efficiente creare un endpoint separato per i file di grandi dimensioni. Ad esempio, gli aggiornamenti firmware o software sono in genere file di grandi dimensioni. Per la distribuzione di file con dimensioni superiori a 1,8 GB, l'ottimizzazione del download di file di grandi dimensioni è obbligatorio.

Per i profili di **rete CDN Standard di Azure con tecnologia Microsoft**, **rete CDN Standard di Azure con tecnologia Verizon** e **rete CDN Premium di Azure con tecnologia Verizon**, usare il tipo di ottimizzazione della distribuzione di contenuti Web generale per distribuire il contenuto di download di file di grandi dimensioni. Non è presente alcuna limitazione alle dimensioni di download di file.

Per altre informazioni sull'ottimizzazione di file di grandi dimensioni vedere [Ottimizzazione di file di grandi dimensioni](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Accelerazione sito dinamico

 L'accelerazione dinamica del sito è disponibile per i profili di **rete CDN Standard di Azure con tecnologia Akamai**, **rete CDN Standard di Azure con tecnologia Verizon** e **rete CDN Premium di Azure con tecnologia Verizon**. Per usare questa ottimizzazione, viene applicata una tariffa aggiuntiva. Per altre informazioni, vedere [Prezzi della rete per la distribuzione di contenuti](https://azure.microsoft.com/pricing/details/cdn/).

> [!NOTE]
> Accelerazione sito dinamico da Microsoft è disponibile tramite [servizio di ingresso principale di Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview) che è globale [anycast](https://en.wikipedia.org/wiki/Anycast) servizio sfruttando privata rete globale di Microsoft per offrire i carichi di lavoro di app.

L'accelerazione di sito dinamico include diverse tecniche che sfruttano la latenza e le prestazioni di contenuto dinamico. Le tecniche includono l'ottimizzazione di route e rete, l'ottimizzazione TCP e molto altro. 

Per accelerare un'app Web che include numerose risposte non memorizzabili nella cache, è possibile usare questa ottimizzazione. I risultati di ricerca, le transazioni di checkout o i dati in tempo reale sono degli esempi. È possibile continuare a usare le funzionalità di base di memorizzazione nella cache della rete CDN di Azure per la distribuzione di contenuti per i dati statici. 

Per altre informazioni sull'accelerazione sito dinamico, vedere [Accelerazione sito dinamico](cdn-dynamic-site-acceleration.md).



