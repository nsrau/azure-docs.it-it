---
title: Domande frequenti sui contenitori di Servizi cognitivi
titleSuffix: Azure Cognitive Services
description: Domande frequenti e risposte.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8aae650065e8de11ccdc55a8a056c379a219fcb4
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876507"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Domande frequenti sui contenitori di Servizi cognitivi di AzureAzure Cognitive Services containers frequently asked questions (FAQ)

## <a name="general-questions"></a>Domande generali

**D: Cosa è disponibile?**

**R:** [Il supporto dei contenitori in Servizi cognitivi](../cognitive-services-container-support.md) di Azure consente agli sviluppatori di usare le stesse API intelligenti disponibili in Azure, ma con i [vantaggi](../cognitive-services-container-support.md#features-and-benefits) della containerizzazione. Il supporto dei contenitori è attualmente disponibile in anteprima per un sottoinsieme di Servizi cognitivi di Azure, incluse parti di:Container support is currently available in preview for a subset of Azure Cognitive Services, including parts of:

> [!div class="checklist"]
> * [Rilevamento anomalie][ad-containers]
> * [Visione artificiale][cv-containers]
> * [Viso][fa-containers]
> * [Riconoscimento modulo][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [API servizio Voce][sp-containers]
> * [Text Analytics][ta-containers]

**D: Esiste una differenza tra il cloud di Servizi cognitivi e i contenitori?**

**A:** I contenitori di Servizi cognitivi sono un'alternativa al cloud di Servizi cognitivi. I contenitori offrono le stesse funzionalità dei servizi cloud corrispondenti. I clienti possono distribuire i contenitori in locale o in Azure.Customers can deploy the containers on-premises or in Azure. La tecnologia di iaaa-aria di base, i piani tariffari, le chiavi API e la firma DELL'API sono gli stessi tra il contenitore e i servizi cloud corrispondenti. Ecco le [funzionalità e i vantaggi](../cognitive-services-container-support.md#features-and-benefits) per la scelta dei contenitori rispetto all'equivalente del servizio cloud.

**D: I contenitori saranno disponibili per tutti i servizi cognitivi e quali sono il set di contenitori successivo da aspettarsi?**

**A:** Vorremmo rendere disponibili più Servizi cognitivi come offerte di contenitori. Contattare il proprio account manager Microsoft locale per ottenere aggiornamenti sulle nuove versioni dei contenitori e altri annunci di Servizi cognitivi.

**D: Quale sarà il contratto di servizio (SLA) per i contenitori di Servizi cognitivi?**

**A:** I contenitori di Servizi cognitivi non dispongono di un servizio di controllo di emergenza.

Le configurazioni dei contenitori dei servizi cognitivi delle risorse sono controllate dai clienti, pertanto Microsoft non offrirà un servizio di controllo di accesso sLA per la disponibilità generale. I clienti sono liberi di distribuire contenitori in locale, quindi definiscono gli ambienti host.

> [!IMPORTANT]
> Per ulteriori informazioni sui contratti di servizio di Servizi cognitivi, [visitare la pagina Del](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)servizio Clienti .

**D: Questi contenitori sono disponibili nelle nuvole sovrane?**

**A:** Non tutti hanno familiarità con il termine "nuvola sovrana", quindi cominciamo con la definizione:

> Il "cloud sovrano" è costituito dai cloud [di Azure per enti pubblici](../../azure-government/documentation-government-welcome.md), Azure [Germania](../../germany/germany-welcome.md)e Azure [China 21Vianet.](https://docs.microsoft.com/azure/china/overview-operations)

Sfortunatamente, i contenitori di Servizi cognitivi *non* sono supportati in modo nativo nei cloud sovrani. I contenitori possono essere eseguiti in questi cloud, ma verranno estratti dal cloud pubblico e dovranno inviare i dati di utilizzo all'endpoint pubblico.

### <a name="versioning"></a>Controllo delle versioni

**D: In che modo i contenitori vengono aggiornati alla versione più recente?**

**A:** I clienti possono scegliere quando aggiornare i contenitori distribuiti. I contenitori verranno contrassegnati `latest` con [tag Docker](https://docs.docker.com/engine/reference/commandline/tag/) standard, ad esempio per indicare la versione più recente. Incoraggiamo i clienti a estrarre la versione più recente dei contenitori quando vengono [rilasciati, estrarre i webhook del Registro](../../container-registry/container-registry-webhook.md) di sistema del contenitore di Azure per informazioni dettagliate su come ricevere una notifica quando un'immagine viene aggiornata.
 
**D: Quali versioni saranno supportate?**

**A:** Verranno supportate la versione corrente e l'ultima versione principale del contenitore. Tuttavia, incoraggiamo i clienti a rimanere aggiornati per ottenere la tecnologia più recente.
 
**D: Come vengono sottoposti a controllo delle versioni degli aggiornamenti?**

**A:** Le modifiche principali apportate alla versione indicano che la firma dell'API è stata apportata in modo non necessario. Prevediamo che questo coinciderà generalmente con le modifiche principali della versione per l'offerta cloud del servizio cognitivo corrispondente. Modifiche minori alla versione indicano correzioni di bug, aggiornamenti del modello o nuove funzionalità che non apportano una modifica sostanziale alla firma dell'API.

## <a name="technical-questions"></a>Domande tecniche

**D: Come è necessario eseguire i contenitori di Servizi cognitivi nei dispositivi IoT?**

Se non si dispone di una connessione internet affidabile, o si desidera risparmiare sui costi di larghezza di banda. In alternativa, se hanno requisiti a bassa latenza o si gestiscono dati sensibili che devono essere analizzati in loco, [Azure IoT Edge con i contenitori di Servizi cognitivi](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) offre coerenza con il cloud.

**D: Come posso fornire feedback sui prodotti e consigli sulle funzionalità?**

**A:** I clienti sono incoraggiati a esprimere pubblicamente [le loro preoccupazioni](https://cognitive.uservoice.com/) e ad up-vote altri che hanno fatto lo stesso dove potenziali problemi si sovrappongono. Lo strumento vocale dell'utente può essere utilizzato sia per il feedback del prodotto che per i consigli sulle funzionalità.

**D: Chi posso contattare per ottenere assistenza?**

**A:** I canali di supporto clienti sono gli stessi offerti dall'offerta cloud di Servizi cognitivi. Tutti i contenitori di Servizi cognitivi includono funzionalità di registrazione che aiuteranno noi e la community a supportare i clienti. Per ulteriore supporto, vedere le opzioni seguenti.

### <a name="customer-support-plan"></a>Piano di assistenza clienti

I clienti devono fare riferimento al piano di supporto di [Azure](https://azure.microsoft.com/support/plans/) per vedere chi contattare per il supporto.

### <a name="azure-knowledge-center"></a>Centro benrete di AzureAzure knowledge center

I clienti sono liberi di esplorare il [centro informazioni](https://azure.microsoft.com/resources/knowledge-center/) di Azure per rispondere a domande e problemi di supporto.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) è un sito di domande e risposte per programmatori professionisti e appassionati.

Esplora i tag seguenti per potenziali domande e risposte in linea con le tue esigenze.

* [Servizi cognitivi di Azure](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft cognitivo](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**D: Come funziona la fatturazione?**

**A:** I clienti vengono addebitati in base al consumo, in modo simile al cloud di Servizi cognitivi. I contenitori devono essere configurati per inviare dati di misurazione ad Azure e le transazioni verranno fatturate di conseguenza. Le risorse utilizzate nei servizi ospitati e locali verranno aggiunte a una singola quota con prezzi a più livelli, contando su entrambi gli utilizzi. Per maggiori dettagli, consulta la pagina dei prezzi dell'offerta corrispondente.

* [Rilevamento anomalie][ad-containers-billing]
* [Visione artificiale][cv-containers-billing]
* [Viso][fa-containers-billing]
* [Riconoscimento modulo][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [API servizio Voce][sp-containers-billing]
* [Text Analytics][ta-containers-billing]

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano dati dei clienti a Microsoft.
 
**D: Qual è la garanzia di supporto corrente per i contenitori?**

**A:** Non esiste una garanzia per le anteprime. La garanzia standard di Microsoft per il software aziendale si applicherà quando i contenitori vengono formalmente annunciati come disponibilità generale (GA).
 
**D: Cosa accade ai contenitori di Servizi cognitivi quando la connettività Internet viene persa?**

**A:** I contenitori di Servizi cognitivi non sono *concessi in licenza* per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono abilitare sempre i contenitori per comunicare con il servizio di misurazione.

**D: Per quanto tempo può funzionare il contenitore senza essere connesso ad Azure?**

**A:** I contenitori di Servizi cognitivi non sono *concessi in licenza* per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono abilitare sempre i contenitori per comunicare con il servizio di misurazione.
 
**D: Che cos'è l'hardware corrente necessario per eseguire questi contenitori?**

**A:** I contenitori di Servizi cognitivi sono contenitori basati su x64 che possono eseguire qualsiasi nodo Linux compatibile, VM e dispositivo perimetrale che supporta contenitori X64 Linux Docker.Cognitive Services containers are x64 based containers that can run any compatible Linux node, VM, and edge device that supports x64 Linux Docker Containers. Tutti richiedono processori CPU. Le configurazioni minime e consigliate per ogni offerta di contenitori sono disponibili di seguito:

* [Rilevamento anomalie][ad-containers-recommendations]
* [Visione artificiale][cv-containers-recommendations]
* [Viso][fa-containers-recommendations]
* [Riconoscimento modulo][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [API servizio Voce][sp-containers-recommendations]
* [Text Analytics][ta-containers-recommendations]
 
**D: Questi contenitori sono attualmente supportati in Windows?**

**A:** I contenitori di Servizi cognitivi sono contenitori Linux, tuttavia è disponibile un certo supporto per i contenitori Linux in Windows.The Cognitive Services containers are Linux containers, however there is some support for Linux containers on Windows. Per ulteriori informazioni sui contenitori Linux in Windows, vedere la documentazione di [Docker](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/).
 
**D: Come si individuano i contenitori?**

**A:** I contenitori di Servizi cognitivi sono disponibili in varie posizioni, ad esempio il portale di Azure, l'hub Docker e i registri dei contenitori di Azure.Cognitive Services containers are available in various locations, such as the Azure portal, Docker hub, and Azure container registries. Per le posizioni dei contenitori più recenti, fare riferimento ai repository e alle immagini dei [contenitori.](../cognitive-services-container-support.md#container-repositories-and-images)

**D: In che modo i contenitori dei servizi cognitivi sono confrontati con le offerte AWS e Google?**

**A:** Microsoft è il primo provider di cloud a spostare i modelli di ia-AI pre-addestrati in contenitori con fatturazione semplice per transazione, come se i clienti stesseno usando un servizio cloud. Microsoft ritiene che un cloud ibrido offra ai clienti più scelta.

**D: Quali certificazioni di conformità hanno i contenitori?**

**A:** I contenitori dei servizi cognitivi non hanno certificazioni di conformità

**D: In quali aree sono disponibili i contenitori di Servizi cognitivi?**

**A:** I contenitori possono essere eseguiti in qualsiasi punto in qualsiasi area, tuttavia hanno bisogno di una chiave e di richiamare ad Azure per la misurazione. Tutte le aree supportate per il servizio cloud sono supportate per la chiamata di misurazione dei contenitori.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
