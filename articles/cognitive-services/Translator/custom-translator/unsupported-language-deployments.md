---
title: Distribuzioni del linguaggio non supportate-convertitore personalizzato
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come distribuire coppie di lingue non supportate nel traduttore personalizzato di servizi cognitivi di Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 3c5c74fc853b5a2425a58e1704aad43350cba212
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837443"
---
# <a name="unsupported-language-deployments"></a>Distribuzioni in lingue non supportate

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Con l'imminente ritiro dell'Hub Microsoft Translator, Microsoft disinstallerà tutti i modelli attualmente distribuiti tramite l'hub. Molti di questi modelli sono distribuiti nell'hub le cui coppie di linguaggi non sono supportate in un convertitore personalizzato.  Non si vuole che gli utenti in questa situazione non abbiano alcun riciclo per la conversione del contenuto.

È ora disponibile un processo che consente di distribuire i modelli non supportati tramite il convertitore personalizzato.  Questo processo consente di continuare a tradurre il contenuto usando la versione più recente dell'API V3.  Questi modelli saranno ospitati fino a quando non si sceglie di annullare la distribuzione o la coppia di lingue diventa disponibile nel convertitore personalizzato.  Questo articolo illustra il processo di distribuzione dei modelli con coppie di lingue non supportate.

## <a name="prerequisites"></a>Prerequisiti

Affinché i modelli siano candidati per la distribuzione, è necessario che soddisfino i criteri seguenti:
* Il progetto che contiene il modello deve essere stato migrato dall'hub al convertitore personalizzato usando lo strumento di migrazione.  Il processo per la migrazione dei progetti e delle aree di lavoro è disponibile [qui](how-to-migrate.md).
* Il modello deve trovarsi nello stato distribuito quando viene eseguita la migrazione.  
* La coppia di lingue del modello deve essere una coppia di lingue non supportata nel convertitore personalizzato.  Le coppie di lingue in cui una lingua è supportata da o verso l'inglese, ma la coppia non include l'inglese, sono candidati per le distribuzioni di linguaggio non supportate.  Ad esempio, un modello di hub per una coppia di lingua francese-tedesca viene considerato una coppia di lingue non supportata anche se la coppia di lingue è supportata da Francese a inglese e da inglese a tedesco.

## <a name="process"></a>Process
Dopo aver eseguito la migrazione dei modelli dall'hub candidati per la distribuzione, è possibile trovarli nella pagina **Impostazioni** per l'area di lavoro e scorrere fino alla fine della pagina in cui verrà visualizzata una sezione **Training Hub di conversione non supportata** .  Questa sezione viene visualizzata solo se sono presenti progetti che soddisfano i prerequisiti menzionati in precedenza.

![Come eseguire la migrazione da Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Nella pagina di selezione **Training Hub di conversione non supportata** la scheda **Training non richiesti** contiene modelli idonei per la distribuzione.  Selezionare i modelli che si desidera distribuire e inviare una richiesta.   Prima della scadenza della distribuzione del 30 aprile, è possibile selezionare tutti i modelli desiderati per la distribuzione.
 
![Come eseguire la migrazione da Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Una volta inviato, il modello non sarà più disponibile nella scheda **Training non richiesti** e verrà invece visualizzato nella scheda **training richiesti** .  È possibile visualizzare i training richiesti in qualsiasi momento.

![Come eseguire la migrazione da Hub](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Passaggi successivi

I modelli selezionati per la distribuzione vengono salvati dopo la rimozione delle autorizzazioni dell'hub e la distribuzione di tutti i modelli.  Si ha fino al 24 maggio per inviare le richieste per la distribuzione di modelli non supportati.  Questi modelli verranno distribuiti a partire dal 15 giugno e saranno accessibili tramite l'API Translator V3.  Inoltre, saranno disponibili tramite l'API v2 fino al 1 ° luglio.  

Per ulteriori informazioni sulle date importanti per la deprecazione dell'hub, vedere [qui](https://www.microsoft.com/translator/business/hub/).
Una volta distribuiti, verranno applicati i normali addebiti per l'hosting.  Per informazioni dettagliate, vedere [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) .  

A differenza dei modelli di conversione personalizzati standard, i modelli di hub saranno disponibili solo in una singola area, quindi gli addebiti per l'hosting su più aree non verranno applicati.  Una volta distribuita, sarà possibile annullare la distribuzione del modello Hub in qualsiasi momento tramite il progetto di conversione personalizzata migrato.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il training di un modello](how-to-train-model.md).
- Iniziare a usare il modello di traduzione personalizzata distribuito tramite l'[API Traduzione testuale Microsoft V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
