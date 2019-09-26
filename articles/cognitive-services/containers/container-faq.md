---
title: Domande frequenti sui contenitori di servizi cognitivi
titleSuffix: Azure Cognitive Services
description: Domande e risposte frequenti.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: dapine
ms.openlocfilehash: 6e218f33bdc33708cef0c94eb85298abf2b8927c
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316632"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Domande frequenti sui contenitori di servizi cognitivi di Azure

## <a name="general-questions"></a>Domande generali

**D: Che cosa è disponibile?**

**R:** Il [supporto dei contenitori nei servizi cognitivi di Azure](../cognitive-services-container-support.md) consente agli sviluppatori di usare le stesse API intelligenti disponibili in Azure, ma con i [vantaggi](../cognitive-services-container-support.md#features-and-benefits) della contenitori. Il supporto dei contenitori è attualmente disponibile in anteprima per un sottoinsieme di servizi cognitivi di Azure, incluse le parti di:

> [!div class="checklist"]
> * [Rilevamento anomalie][ad-containers]
> * [Visione artificiale][cv-containers]
> * [Viso][fa-containers]
> * [Riconoscimento moduli][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [API servizio Voce][sp-containers]
> * [Analisi del testo][ta-containers]
<!-- > * [Translator Text][tt-containers] -->

**D: Esiste una differenza tra il cloud di servizi cognitivi e i contenitori?**

**R:** I contenitori di servizi cognitivi rappresentano un'alternativa al cloud di servizi cognitivi. I contenitori offrono le stesse funzionalità dei servizi cloud corrispondenti. I clienti possono distribuire i contenitori in locale o in Azure. La tecnologia di intelligenza artificiale principale, i piani tariffari, le chiavi API e la firma API sono gli stessi tra il contenitore e i servizi cloud corrispondenti. Ecco le [funzionalità e i vantaggi](../cognitive-services-container-support.md#features-and-benefits) per la scelta dei contenitori rispetto al servizio cloud equivalente.

**D: I contenitori saranno disponibili per tutti i servizi cognitivi e qual è il prossimo set di contenitori che ci si aspetta?**

**R:** Si vuole rendere disponibili più servizi cognitivi come offerte di contenitori. Contattare il gestore di account Microsoft locale per ottenere gli aggiornamenti su nuovi rilasci del contenitore e altri annunci di servizi cognitivi.

**D: Quale sarà il contratto di servizio (SLA) per i contenitori di servizi cognitivi?**

**R:** I contenitori di servizi cognitivi non dispongono di un contratto di servizio.

Le configurazioni dei contenitori di servizi cognitivi delle risorse sono controllate dai clienti, pertanto Microsoft non offrirà un contratto di servizio per la disponibilità generale. I clienti possono distribuire i contenitori in locale, quindi definiscono gli ambienti host.

> [!IMPORTANT]
> Per ulteriori informazioni sui contratti di servizio di servizi cognitivi, [visitare la pagina del contratto](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)di servizio.

**D: Questi contenitori sono disponibili nei cloud sovrani?**

**R:** Non tutti hanno familiarità con il termine "cloud sovrano", quindi iniziamo con la definizione:

> Il "cloud sovrano" è costituito dai cloud 21Vianet di Azure per [enti pubblici](../../azure-government/documentation-government-welcome.md), [Azure Germania](../../germany/germany-welcome.md)e [Azure Cina](https://docs.microsoft.com/azure/china/overview-operations) .

Sfortunatamente, i contenitori di servizi cognitivi *non* sono supportati in modo nativo nei cloud sovrani. I contenitori possono essere eseguiti in questi cloud, ma verranno estratti dal cloud pubblico ed è necessario inviare i dati di utilizzo all'endpoint pubblico.

### <a name="versioning"></a>Controllo delle versioni

**D: In che modo i contenitori vengono aggiornati alla versione più recente?**

**R:** I clienti possono scegliere quando aggiornare i contenitori che hanno distribuito. I contenitori verranno contrassegnati con i [tag Docker](https://docs.docker.com/engine/reference/commandline/tag/) standard, `latest` ad esempio per indicare la versione più recente. Invitiamo i clienti a eseguire il pull della versione più recente dei contenitori non appena vengono rilasciati, per informazioni dettagliate su come ricevere una notifica quando viene aggiornata un'immagine, vedere i [webhook di Azure container Registry](../../container-registry/container-registry-webhook.md) .
 
**D: Quali versioni saranno supportate?**

**R:** Verranno supportate la versione corrente e l'ultima versione principale del contenitore. Tuttavia, invitiamo i clienti a rimanere aggiornati per ottenere la tecnologia più recente.
 
**D: In che modo vengono installate le versioni degli aggiornamenti?**

**R:** Le modifiche alla versione principale indicano che è presente una modifica di rilievo alla firma dell'API. Si prevede che questa operazione in genere coincide con le modifiche alla versione principale dell'offerta del cloud di servizi cognitivi corrispondente. Le modifiche della versione secondaria indicano correzioni di bug, aggiornamenti del modello o nuove funzionalità che non apportano una modifica sostanziale alla firma dell'API.

## <a name="technical-questions"></a>Domande tecniche

**D: Come è possibile eseguire i contenitori di servizi cognitivi nei dispositivi Internet?**

Se non si dispone di una connessione Internet affidabile o se si vuole risparmiare sul costo della larghezza di banda. In alternativa, se si hanno requisiti di bassa latenza o si gestiscono dati sensibili che devono essere analizzati in sede, [Azure IOT Edge con i contenitori di servizi cognitivi](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) garantisce la coerenza con il cloud.

**D: Ricerca per categorie fornire suggerimenti sul prodotto e suggerimenti sulle funzionalità?**

**R:** I clienti sono invitati a esprimere pubblicamente le [proprie preoccupazioni](https://cognitive.uservoice.com/) e a votare altri che hanno fatto lo stesso in cui si sovrappongono i potenziali problemi. È possibile utilizzare lo strumento voce utente per il feedback del prodotto e per le raccomandazioni sulle funzionalità.

**D: Chi posso contattare per ottenere supporto?**

**R:** I canali di supporto clienti sono gli stessi dell'offerta di cloud di servizi cognitivi. Tutti i contenitori di servizi cognitivi includono funzionalità di registrazione che ci aiuteranno a Microsoft e ai clienti del supporto della community. Per ulteriore supporto, vedere le opzioni seguenti.

### <a name="customer-support-plan"></a>Piano di assistenza clienti

I clienti devono fare riferimento al [piano di supporto di Azure](https://azure.microsoft.com/support/plans/) per vedere chi contattare per assistenza.

### <a name="azure-knowledge-center"></a>Centro informazioni di Azure

Il cliente è libero di esplorare il [centro informazioni di Azure](https://azure.microsoft.com/resources/knowledge-center/) per rispondere alle domande e ai problemi di supporto.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack overflow](https://en.wikipedia.org/wiki/Stack_Overflow) è un sito di domande e risposte per i programmatori professionisti e appassionati.

Esaminare i seguenti tag per individuare le domande e le risposte che si allineano alle esigenze.

* [Servizi cognitivi di Azure](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft cognitive](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**D: Come funziona la fatturazione?**

**R:** I clienti vengono addebitati in base al consumo, in modo analogo al cloud di servizi cognitivi. I contenitori devono essere configurati per l'invio di dati di controllo ad Azure e le transazioni verranno fatturate di conseguenza. Le risorse usate nei servizi ospitati e locali si aggiungono a una singola quota con prezzi a livelli, considerando entrambi gli utilizzi. Per informazioni dettagliate, fare riferimento alla pagina dei prezzi dell'offerta corrispondente.

* [Rilevamento anomalie][ad-containers-billing]
* [Visione artificiale][cv-containers-billing]
* [Viso][fa-containers-billing]
* [Riconoscimento moduli][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [API servizio Voce][sp-containers-billing]
* [Analisi del testo][ta-containers-billing]
<!-- * [Translator Text][tt-containers-billing] -->

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano dati dei clienti a Microsoft.
 
**D: Qual è la garanzia di supporto corrente per i contenitori?**

**R:** Non è prevista alcuna garanzia per le anteprime. La garanzia standard Microsoft per il software aziendale verrà applicata quando i contenitori vengono annunciati formalmente come disponibilità generale (GA).
 
**D: Cosa accade ai contenitori di servizi cognitivi quando la connettività Internet viene persa?**

**R:** I contenitori di servizi cognitivi *non sono concessi in licenza* per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre con il servizio di misurazione.

**D: Quanto tempo può funzionare il contenitore senza essere connesso ad Azure?**

**R:** I contenitori di servizi cognitivi *non sono concessi in licenza* per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre con il servizio di misurazione.
 
**D: Quali sono i componenti hardware correnti necessari per eseguire questi contenitori?**

**R:** I contenitori di servizi cognitivi sono contenitori basati su x64 che consentono di eseguire qualsiasi dispositivo Linux, VM e perimetrale compatibile che supporta i contenitori Docker Linux x64. Tutti richiedono processori CPU. Le configurazioni minime e consigliate per ogni offerta del contenitore sono disponibili di seguito:

* [Rilevamento anomalie][ad-containers-recommendations]
* [Visione artificiale][cv-containers-recommendations]
* [Viso][fa-containers-recommendations]
* [Riconoscimento moduli][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [API servizio Voce][sp-containers-recommendations]
* [Analisi del testo][ta-containers-recommendations]
<!-- * [Translator Text][tt-containers-recommendations] -->
 
**D: Questi contenitori sono attualmente supportati in Windows?**

**R:** I contenitori di servizi cognitivi sono contenitori Linux, tuttavia è disponibile un supporto per i contenitori Linux in Windows. Per altre informazioni sui contenitori Linux in Windows, vedere la [documentazione di Docker](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/).
 
**D: Ricerca per categorie individuare i contenitori?**

**R:** I contenitori di servizi cognitivi sono disponibili in varie posizioni, ad esempio portale di Azure, Hub Docker e registri contenitori di Azure. Per le posizioni dei contenitori più recenti, fare riferimento a [repository e immagini del contenitore](../cognitive-services-container-support.md#container-repositories-and-images).

**D: Come vengono confrontati i contenitori di servizi cognitivi con le offerte AWS e Google?**

**R:** Microsoft è il primo provider di servizi cloud per spostare i modelli di intelligenza artificiale pre-sottoposti a training nei contenitori con una fatturazione semplice per transazione, come se i clienti usano un servizio cloud. Microsoft ritiene che un cloud ibrido fornisca ai clienti maggiore scelta.

**D: Quali sono le certificazioni di conformità dei contenitori?**

**R:** I contenitori di servizi cognitivi non dispongono di certificazioni di conformità

**D: In quali aree sono disponibili i contenitori di servizi cognitivi?**

**R:** I contenitori possono essere eseguiti in qualsiasi area, ma richiedono una chiave e richiamano Azure per la misurazione. Tutte le aree supportate per il servizio cloud sono supportate per la chiamata di misurazione dei contenitori.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md
<!-- [tt-containers]: ../translator/how-to-install-containers.md -->

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing
<!-- [tt-containers-billing]: ../translator/how-to-install-containers.md#billing -->

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
<!-- [tt-containers-recommendations]: ../translator/how-to-install-containers.md#container-requirements-and-recommendations -->
