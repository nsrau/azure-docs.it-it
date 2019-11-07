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
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 958acd042acba2a8c6c38ad1e6bac614db509da8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73604437"
---
# <a name="container-support-in-azure-cognitive-services"></a>Supporto dei contenitori in Servizi cognitivi di Azure

Il supporto dei contenitori in Servizi cognitivi di Azure consente agli sviluppatori di usare le stesse API avanzate disponibili in Azure e al tempo stesso di usufruire della flessibilità offerta dai [contenitori Docker](https://www.docker.com/what-container) per quanto riguarda la posizione di distribuzione e hosting dei servizi. Il supporto dei contenitori è attualmente disponibile per un sottoinsieme di servizi cognitivi di Azure, incluse le parti di:

> [!div class="checklist"]
> * [Rilevamento anomalie][ad-containers]
> * [Visione artificiale][cv-containers]
> * [Viso][fa-containers]
> * [Riconoscimento moduli][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [API servizio Voce][sp-containers]
> * [Analisi del testo][ta-containers]
> * [Traduzione testuale][tt-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

La containerizzazione è un approccio alla distribuzione del software in cui un'applicazione o un servizio, incluse le relative dipendenze e configurazione, viene compresso come immagine del contenitore. L'immagine del contenitore può essere distribuita in un host contenitore senza alcuna variazione o con modifiche minime. I contenitori sono isolati fra loro e dal sistema operativo sottostante, con un footprint inferiore a quello di una macchina virtuale. È possibile creare istanze dei contenitori dalle immagini per attività a breve termine. Tali istanze possono essere rimosse quando non sono più necessarie.

Le risorse di servizi cognitivi sono disponibili in [Microsoft Azure](https://azure.microsoft.com). Accedere al [portale di Azure](https://portal.azure.com/) per creare ed esplorare le risorse di Azure per questi servizi.

## <a name="features-and-benefits"></a>Funzionalità e vantaggi

- **Controllo dei dati**: consente ai clienti di scegliere il punto in cui questi servizi cognitivi elaborano i dati. Questo aspetto è fondamentale per i clienti che non possono inviare dati al cloud, ma che richiedono l'accesso alla tecnologia offerta da Servizi cognitivi. È supportata la coerenza in ambienti ibridi, tra i dati e le funzionalità di gestione, identità e sicurezza.
- **Controllo sugli aggiornamenti dei modelli**: ai clienti viene offerta la flessibilità necessaria per il controllo delle versioni e l'aggiornamento dei modelli distribuiti nelle soluzioni.
- **Architettura**portabile: consente la creazione di un'architettura di applicazione portabile che può essere distribuita in Azure, in locale e nei dispositivi perimetrali. I contenitori possono essere distribuiti direttamente nel [servizio Azure Kubernetes](../aks/index.yml), in [Istanze di Azure Container](../container-instances/index.yml) o in un cluster [Kubernetes](https://kubernetes.io/) distribuito in [Azure Stack](/azure-stack/operator). Per altre informazioni, vedere [Deploy Kubernetes to Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy) (Distribuire Kubernetes in Azure Stack).
- **Velocità effettiva elevata/bassa latenza**: offrire ai clienti la possibilità di scalare i requisiti di velocità effettiva elevata e bassa latenza, abilitando i servizi cognitivi a funzionare fisicamente vicino alla logica e ai dati dell'applicazione. I contenitori non raggiungono il limite di transazioni al secondo e possono offrire scalabilità sia orizzontale che verticale per gestire la domanda, se si forniscono le risorse hardware necessarie. 

## <a name="containers-in-azure-cognitive-services"></a>Contenitori in Servizi cognitivi di Azure

I contenitori di Servizi cognitivi di Azure offrono il set seguente di contenitori Docker, ognuno dei quali contiene un subset di funzionalità dei servizi disponibili in Servizi cognitivi di Azure:

| Service | Piano tariffario supportato | Contenitore | Descrizione |
|---------|----------|----------|-------------|
|[Rilevamento anomalie][ad-containers] |F0, S0|**Rilevamento anomalie** |L'API Rilevamento anomalie permette di monitorare e rilevare le anomalie nei dati di serie temporali con l'apprendimento automatico.<br>[Richiedere l'accesso](https://aka.ms/adcontainer)|
|[Visione artificiale][cv-containers] |F0, S1|**Lettura** |Estre testo stampato dalle immagini di diversi oggetti con superfici e sfondi diversi, ad esempio ricette, poster e biglietti da visita. Il contenitore di lettura rileva anche il *testo scritto a mano* nelle immagini e fornisce il supporto per PDF/TIFF/più pagine.<br/><br/>**Importante:** Il contenitore di lettura attualmente funziona solo con l'inglese.|
|[Viso][fa-containers] |F0, S0|**Viso** |Rileva i visi umani nelle immagini e identifica gli attributi, ad esempio i punti di riferimento del viso (come naso e occhi), il sesso, l'età e altre caratteristiche facciali con previsioni basate su computer. Oltre al rilevamento, Viso può verificare se due volti nella stessa immagine o in immagini diverse sono uguali mediante un punteggio di attendibilità oppure può confrontare i visi con un database per verificare se esistono già visi simili o identici. È anche possibile organizzare i visi simili in gruppi mediante caratteristiche condivise.<br>[Richiedere l'accesso](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Riconoscimento moduli][fr-containers] |F0, S0|**Riconoscimento moduli** |La comprensione dei moduli applica la tecnologia di machine learning per identificare ed estrarre le coppie chiave-valore e le tabelle dai moduli.<br>[Richiedere l'accesso](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**LUIS** ([immagine](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Carica un modello Language Understanding sottoposto a training o pubblicato, noto anche come app LUIS, in un contenitore Docker e fornisce l'accesso alle stime di query dagli endpoint dell'API del contenitore. È possibile raccogliere i log di query dal contenitore e caricarli nel [portale LUIS](https://www.luis.ai) per migliorare l'accuratezza delle stime dell'app.|
|[API servizio Voce][sp-containers-stt] |F0, S0|**Riconoscimento vocale** |Esegue la trascrizione del parlato continuo in tempo reale in testo.|
|[API servizio Voce][sp-containers-cstt] |F0, S0|**Da Riconoscimento vocale personalizzato a testo** |Trascrive il discorso continuo in tempo reale nel testo usando un modello personalizzato.|
|[API servizio Voce][sp-containers-tts] |F0, S0|**Sintesi vocale** |Converte il testo scritto in un audio che suona naturale.|
|[API servizio Voce][sp-containers-ctts] |F0, S0|**Sintesi vocale personalizzata** |Converte il testo in un riconoscimento vocale naturale usando un modello personalizzato.|
|[Analisi del testo][ta-containers] |F0, S|**Estrazione frasi chiave** ([immagine](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Estrae le frasi chiave per identificare i punti principali. Ad esempio, per il testo di input "Il cibo era delizioso e il personale era meraviglioso", l'API restituisce i punti rilevanti del discorso, ovvero "cibo" e "personale meraviglioso". |
|[Analisi del testo][ta-containers]|F0, S|**Rilevamento lingua** ([immagine](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Per un massimo di 120 lingue, rileva la lingua in cui è scritto il testo di input e crea un report relativo a un codice lingua singolo per ogni documento inviato nella richiesta. Il codice lingua è associato a un punteggio che indica il livello di attendibilità. |
|[Analisi del testo][ta-containers]|F0, S|**Analisi del sentiment** ([immagine](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analizza testo non elaborato per indicazioni su una valutazione positiva o negativa. L'API restituisce un punteggio sentiment compreso tra 0 e 1 per ogni documento, dove 1 è il risultato più positivo. Viene eseguito un training preliminare dei modelli di analisi usando un ampio corpus di testi e tecnologie per l'elaborazione del linguaggio naturale di Microsoft. Per [alcune lingue](./text-analytics/language-support.md), l'API può analizzare e assegnare punteggi a qualsiasi testo non elaborato fornito dall'utente, restituendo direttamente i risultati all'applicazione chiamante. |
|[Traduzione testuale][tt-containers]| **N/D** | **Traduzione testuale** | Traduzione testuale è un servizio di traduzione automatica basato su cloud che può essere usato per tradurre il testo quasi in tempo reale tramite una semplice chiamata API REST.<br>[Richiedere l'accesso](https://aka.ms/translatorcontainerform) |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Inoltre, alcuni contenitori sono supportati in Servizi cognitivi [**All-in-One che offrono**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) chiavi di risorsa. È possibile creare una sola risorsa all-in-One di servizi cognitivi e usare la stessa chiave di fatturazione tra i servizi supportati per i servizi seguenti:

* Visione artificiale
* Viso
* LUIS
* Text Analytics

## <a name="container-availability-in-azure-cognitive-services"></a>Disponibilità dei contenitori in Servizi cognitivi di Azure

I contenitori di Servizi cognitivi di Azure sono disponibili pubblicamente attraverso la sottoscrizione di Azure e le immagini dei contenitori Docker possono essere estratte da Registro contenitori di Microsoft o dall'hub Docker. È possibile usare il comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore dal registro appropriato.

> [!IMPORTANT]
> Attualmente, è necessario completare un processo di iscrizione per accedere ai contenitori seguenti, in cui si compila e si invia un questionario con domande sull'utente, sulla società e sul caso di utilizzo per il quale si desidera implementare i contenitori. Dopo aver concesso l'accesso e le credenziali fornite, è possibile eseguire il pull delle immagini del contenitore da un registro contenitori privato ospitato da Azure Container Registry.
> * [Rilevamento anomalie](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Viso](Face/face-how-to-install-containers.md)
> * [Riconoscimento moduli](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Lettura](computer-vision/computer-vision-how-to-install-containers.md)
> * [Sintesi vocale e sintesi vocale](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)
> * [Traduzione testuale](translator/how-to-install-containers.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Prerequisiti

Per usare i contenitori di Servizi cognitivi di Azure, è necessario soddisfare i prerequisiti seguenti:

**Motore Docker**: il motore Docker deve essere installato localmente. Docker offre pacchetti per la configurazione dell'ambiente in [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) e [Windows](https://docs.docker.com/docker-for-windows/). In Windows Docker deve essere configurato per supportare i contenitori Linux. I contenitori Docker possono anche essere distribuiti direttamente nel [servizio Azure Kubernetes](../aks/index.yml) o in [Istanze di Azure Container](../container-instances/index.yml).

Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure.

**Familiarità con Registro contenitori di Microsoft e Docker**: è opportuno avere una conoscenza di base dei concetti relativi a Registro contenitori di Microsoft e Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.

Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).

Singoli contenitori possono prevedere anche requisiti specifici, tra cui quelli relativi a server e allocazione di memoria.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [ricette del contenitore](containers/container-reuse-recipe.md) che è possibile usare con i servizi cognitivi.

Installare ed esplorare le funzionalità fornite dai contenitori in Servizi cognitivi di Azure:

* [Contenitori dei rilevatori di anomalie][ad-containers]
* [Contenitori di Visione artificiale][cv-containers]
* [Contenitori viso][fa-containers]
* [Contenitori riconoscimento moduli][fr-containers]
* [Contenitori Language Understanding (LUIS)][lu-containers]
* [Contenitori dell'API del servizio vocale][sp-containers]
* [Contenitori di Analisi del testo][ta-containers]
* [Contenitori di Traduzione testuale][tt-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: translator/how-to-install-containers.md