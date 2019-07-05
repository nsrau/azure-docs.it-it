---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare i contenitori Docker per facilitare la gestione dei dati con Servizi cognitivi.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: c3aaf7537d233b9652f10ee240ebbe4fc4ec995c
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592759"
---
# <a name="container-support-in-azure-cognitive-services"></a>Supporto dei contenitori in Servizi cognitivi di Azure

Il supporto dei contenitori in Servizi cognitivi di Azure consente agli sviluppatori di usare le stesse API avanzate disponibili in Azure e al tempo stesso di usufruire della flessibilità offerta dai [contenitori Docker](https://www.docker.com/what-container) per quanto riguarda la posizione di distribuzione e hosting dei servizi. Supporto dei contenitori è attualmente disponibile in anteprima per un subset di servizi cognitivi di Azure, incluse le parti di:

* [Rilevatore di anomalie](Anomaly-Detector/overview.md)
* [Visione artificiale](Computer-vision/Home.md)
* [Viso](Face/Overview.md)
* [Riconoscimento di form](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409)
* [Language Understanding Intelligent Service](LUIS/luis-container-howto.md) (LUIS)
* [API servizio Voce](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409)
* [Analisi del testo](text-analytics/overview.md)

<!--
* [Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->

La containerizzazione è un approccio alla distribuzione del software in cui un'applicazione o un servizio, incluse le relative dipendenze e configurazione, viene compresso come immagine del contenitore. L'immagine del contenitore può essere distribuita in un host contenitore senza alcuna variazione o con modifiche minime. I contenitori sono isolati fra loro e dal sistema operativo sottostante, con un footprint inferiore a quello di una macchina virtuale. È possibile creare istanze dei contenitori dalle immagini per attività a breve termine. Tali istanze possono essere rimosse quando non sono più necessarie.

Il video seguente illustra l'uso di un contenitore di Servizi cognitivi.

[![Dimostrazione relativa ai contenitori per Servizi cognitivi](./media/index/containers-video-image.png)](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services)

Risorse di servizi cognitivi sono disponibili nel [Microsoft Azure](https://azure.microsoft.com). Accedere al [portale di Azure](https://portal.azure.com/) per creare ed esplorare le risorse di Azure per questi servizi.

## <a name="features-and-benefits"></a>Funzionalità e vantaggi

- **Controllo sui dati**: i clienti possono scegliere la posizione di elaborazione dei dati da parte di Servizi cognitivi. Questo aspetto è fondamentale per i clienti che non possono inviare dati al cloud, ma che richiedono l'accesso alla tecnologia offerta da Servizi cognitivi. È supportata la coerenza in ambienti ibridi, tra i dati e le funzionalità di gestione, identità e sicurezza.
- **Controllo sugli aggiornamenti dei modelli**: ai clienti viene offerta la flessibilità necessaria per il controllo delle versioni e l'aggiornamento dei modelli distribuiti nelle soluzioni.
- **Architettura portabile**: è consentita la creazione di un'architettura di applicazione portabile che può essere distribuita in Azure, nell'ambiente locale e a livello perimetrale. I contenitori possono essere distribuiti direttamente nel [servizio Azure Kubernetes](../aks/index.yml), in [Istanze di Azure Container](../container-instances/index.yml) o in un cluster [Kubernetes](https://kubernetes.io/) distribuito in [Azure Stack](/azure-stack/operator). Per altre informazioni, vedere [Deploy Kubernetes to Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy) (Distribuire Kubernetes in Azure Stack).
- **Velocità effettiva elevata e bassa latenza**: ai clienti vengono offerte opzioni di scalabilità per i requisiti di alta velocità effettiva e bassa latenza consentendo l'esecuzione di Servizi cognitivi fisicamente vicino alla logica e ai dati dell'applicazione. I contenitori non raggiungono il limite di transazioni al secondo e possono offrire scalabilità sia orizzontale che verticale per gestire la domanda, se si forniscono le risorse hardware necessarie. 


## <a name="containers-in-azure-cognitive-services"></a>Contenitori in Servizi cognitivi di Azure

I contenitori di Servizi cognitivi di Azure offrono il set seguente di contenitori Docker, ognuno dei quali contiene un subset di funzionalità dei servizi disponibili in Servizi cognitivi di Azure:

| Service | Piano tariffario è supportato | Contenitore | Descrizione |
|---------|----------|----------|-------------|
|[Rilevatore di anomalie](https://go.microsoft.com/fwlink/?linkid=2083925&clcid=0x409) |F0, S0|**Rilevatore di anomalie** |L'API rilevatore di anomalie consente di monitorare e rilevare le anomalie nei dati delle serie temporali con machine learning.<br>[Richiedere l'accesso](https://aka.ms/adcontainer)|
|[Visione artificiale](Computer-vision/computer-vision-how-to-install-containers.md) |F0, S1|**Riconoscimento del testo** |Estre testo stampato dalle immagini di diversi oggetti con superfici e sfondi diversi, ad esempio ricette, poster e biglietti da visita.<br/><br/>**Importante:** Il contenitore di riconoscimento del testo attualmente funziona solo con la lingua inglese.<br>[Richiedere l'accesso](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Viso](Face/face-how-to-install-containers.md) |F0, S0|**Viso** |Rileva i visi umani nelle immagini e identifica gli attributi, ad esempio i punti di riferimento del viso (come naso e occhi), il sesso, l'età e altre caratteristiche facciali con previsioni basate su computer. Oltre al rilevamento, Viso può verificare se due volti nella stessa immagine o in immagini diverse sono uguali mediante un punteggio di attendibilità oppure può confrontare i visi con un database per verificare se esistono già visi simili o identici. È anche possibile organizzare i visi simili in gruppi mediante caratteristiche condivise.<br>[Richiedere l'accesso](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Riconoscimento di form](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) |F0, S0|**Riconoscimento di form** |Understanding form si applica la tecnologia di machine learning per identificare ed estrarre le coppie chiave-valore e le tabelle da form.<br>[Richiedere l'accesso](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS](LUIS/luis-container-howto.md) |F0, S0|**LUIS** ([immagine](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Carica un modello Language Understanding sottoposto a training o pubblicato, noto anche come app LUIS, in un contenitore Docker e fornisce l'accesso alle stime di query dagli endpoint dell'API del contenitore. È possibile raccogliere i log di query dal contenitore e caricarli nel [portale LUIS](https://www.luis.ai) per migliorare l'accuratezza delle stime dell'app.|
|[API servizio Voce](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409) |F0, S0|**Riconoscimento vocale** |Esegue la trascrizione del parlato continuo in tempo reale in testo.<br>[Richiedere l'accesso](https://aka.ms/speechcontainerspreview/)|
|[API servizio Voce](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409) |F0, S0|**Sintesi vocale** |Converte il testo scritto in un audio che suona naturale.<br>[Richiedere l'accesso](https://aka.ms/speechcontainerspreview/)|
|[Analisi del testo](text-analytics/how-tos/text-analytics-how-to-install-containers.md) |F0, S|**Estrazione frasi chiave** ([immagine](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Estrae le frasi chiave per identificare i punti principali. Ad esempio, per il testo di input "Il cibo era delizioso e il personale era meraviglioso", l'API restituisce i punti rilevanti del discorso, ovvero "cibo" e "personale meraviglioso". |
|[Analisi del testo](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|F0, S|**Rilevamento lingua** ([immagine](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Per un massimo di 120 lingue, rileva la lingua in cui è scritto il testo di input e crea un report relativo a un codice lingua singolo per ogni documento inviato nella richiesta. Il codice lingua è associato a un punteggio che indica il livello di attendibilità. |
|[Analisi del testo](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|F0, S|**Analisi del sentiment** ([immagine](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analizza testo non elaborato per indicazioni su una valutazione positiva o negativa. L'API restituisce un punteggio sentiment compreso tra 0 e 1 per ogni documento, dove 1 è il risultato più positivo. Viene eseguito un training preliminare dei modelli di analisi usando un ampio corpus di testi e tecnologie per l'elaborazione del linguaggio naturale di Microsoft. Per [alcune lingue](./text-analytics/language-support.md), l'API può analizzare e assegnare punteggi a qualsiasi testo non elaborato fornito dall'utente, restituendo direttamente i risultati all'applicazione chiamante. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Inoltre, alcuni contenitori sono supportati in servizi cognitivi [ **offerta All-In-One** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) le chiavi di risorsa. È possibile creare una singola risorsa All-In-One per servizi cognitivi e usare la stessa chiave di fatturazione in tutti i servizi supportati per i servizi seguenti:

* Visione artificiale
* Viso
* LUIS
* Text Analytics

## <a name="container-availability-in-azure-cognitive-services"></a>Disponibilità dei contenitori in Servizi cognitivi di Azure

I contenitori di Servizi cognitivi di Azure sono disponibili pubblicamente attraverso la sottoscrizione di Azure e le immagini dei contenitori Docker possono essere estratte da Registro contenitori di Microsoft o dall'hub Docker. È possibile usare il comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore dal registro appropriato.

> [!IMPORTANT]
> Attualmente, è necessario completare un processo di iscrizione per accedere ai contenitori seguenti, in cui si compilano e invia un questionario con domande sull'utente, l'azienda e il caso d'uso per il quale si desidera implementare i contenitori. Una volta che si ha concesso l'accesso e ha fornito credenziali, è quindi possibile caricare le immagini del contenitore da un registro contenitori privato, ospitato da registro contenitori di Azure.
> * [Anomalie dectector](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Viso](Face/face-how-to-install-containers.md)
> * [Riconoscimento di form](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Riconoscimento del testo](Computer-vision/computer-vision-how-to-install-containers.md)
> * [Per il riconoscimento vocale e sintesi vocale](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

## <a name="prerequisites"></a>Prerequisiti

Per usare i contenitori di Servizi cognitivi di Azure, è necessario soddisfare i prerequisiti seguenti:

**Motore Docker**: il motore Docker deve essere installato in locale. Docker offre pacchetti per la configurazione dell'ambiente in [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) e [Windows](https://docs.docker.com/docker-for-windows/). In Windows Docker deve essere configurato per supportare i contenitori Linux. I contenitori Docker possono anche essere distribuiti direttamente nel [servizio Azure Kubernetes](../aks/index.yml) o in [Istanze di Azure Container](../container-instances/index.yml).

Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure.

**Familiarità con Registro Container Microsoft e Docker**: è opportuno avere una conoscenza di base dei concetti relativi a Registro Container Microsoft e Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.

Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).

Singoli contenitori possono prevedere anche requisiti specifici, tra cui quelli relativi a server e allocazione di memoria.

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Passaggi successivi

Scopri [recipe contenitore](containers/container-reuse-recipe.md) è possibile usare con i servizi cognitivi.

Installare ed esplorare le funzionalità fornite dai contenitori in Servizi cognitivi di Azure:

* [Contenitori di rilevatore di anomalie](Anomaly-Detector/anomaly-detector-container-howto.md)
* [Contenitori di visione artificiale di computer](Computer-vision/computer-vision-how-to-install-containers.md)
* [Contenitori di viso](Face/face-how-to-install-containers.md)
* [Creare contenitori di sistema di riconoscimento](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409)
* [Contenitori di Language Understanding (LUIS)](LUIS/luis-container-howto.md)
* [Contenitori di API del servizio di riconoscimento vocale](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409)
* [Contenitori di Analitica di testo](text-analytics/how-tos/text-analytics-how-to-install-containers.md)

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->
