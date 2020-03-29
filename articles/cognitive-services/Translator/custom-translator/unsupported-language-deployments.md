---
title: Distribuzioni di lingue non supportate - Traduttore personalizzato
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come distribuire coppie di lingue non supportate in Azure Cognitive Services Custom Translator.This article shows you how to deploy unsupported language pairs in Azure Cognitive Services Custom Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 3c5c74fc853b5a2425a58e1704aad43350cba212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837443"
---
# <a name="unsupported-language-deployments"></a>Distribuzioni in lingue non supportate

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Con il prossimo ritiro di Microsoft Translator Hub, Microsoft anali' la distribuzione di tutti i modelli attualmente distribuiti tramite l'Hub. Molti di voi hanno modelli distribuiti nell'hub le cui coppie di lingue non sono supportate in Custom Translator.  Non vogliamo che gli utenti in questa situazione non abbiano alcun ricorso per tradurre il loro contenuto.

Ora abbiamo un processo che ti permette di distribuire i tuoi modelli non supportati tramite il traduttore personalizzato.  Questo processo consente di continuare a tradurre il contenuto utilizzando l'API V3 più recente.  Questi modelli verranno ospitati fino a quando non si sceglie di annullare la distribuzione o la coppia di lingue diventa disponibile in Custom Translator.  In questo articolo viene illustrato il processo di distribuzione di modelli con coppie di lingue non supportate.

## <a name="prerequisites"></a>Prerequisiti

Affinché i modelli siano candidati per la distribuzione, devono soddisfare i seguenti criteri:
* Il progetto contenente il modello deve essere stato migrato dall'hub al traduttore personalizzato utilizzando lo strumento di migrazione.  Il processo di migrazione di progetti e aree di lavoro è disponibile [qui](how-to-migrate.md).
* Il modello deve essere nello stato distribuito quando si verifica la migrazione.  
* La coppia di lingue del modello deve essere una coppia di lingue non supportata in Custom Translator.  Le coppie di lingue in cui una lingua è supportata dall'inglese o dall'inglese, ma la coppia stessa non include l'inglese, sono candidati per distribuzioni in lingue non supportate.  Ad esempio, un modello Hub per una coppia di lingue dal francese al tedesco è considerato una coppia di lingue non supportata anche se il francese e l'inglese al tedesco sono coppie di lingue supportate.

## <a name="process"></a>Process
Dopo aver eseguito la migrazione dei modelli dall'hub candidati per la distribuzione, è possibile trovarli accedendo alla pagina **Impostazioni** per l'area di lavoro e scorrendo fino alla fine della pagina in cui verrà visualizzata una sezione **Formazione hub traduttore non supportato.**  Questa sezione viene visualizzata solo se si dispone di progetti che soddisfano i prerequisiti indicati in precedenza.

![Come eseguire la migrazione da Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Nella pagina di selezione **Formazione hub traduttore non supportata,** la scheda Formazione non richiesto contiene modelli **idonei** per la distribuzione.  Selezionare i modelli che si desidera distribuire e inviare una richiesta.   Prima della scadenza della distribuzione del 30 aprile, è possibile selezionare tutti i modelli desiderati per la distribuzione.
 
![Come eseguire la migrazione da Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Una volta inviato, il modello non sarà più disponibile nella scheda Formazione non **richiesti** e verrà invece visualizzato nella scheda **Formazione richiesti.**  È possibile visualizzare i corsi di formazione richiesti in qualsiasi momento.

![Come eseguire la migrazione da Hub](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Quali sono le operazioni successive?

I modelli selezionati per la distribuzione vengono salvati dopo la disattivazione dell'hub e la dismissione di tutti i modelli.  Fino al 24 maggio sono disponibili richieste di distribuzione di modelli non supportati.  Questi modelli verranno distribuiti il 15 giugno, a quel punto saranno accessibili tramite l'API Translator V3.  Inoltre, saranno disponibili tramite l'API V2 fino al 1 luglio.  

Per ulteriori informazioni sulle date importanti della deprecazione del controllo Hub [qui](https://www.microsoft.com/translator/business/hub/).
Una volta distribuito, verranno applicati i normali costi di hosting.  Vedi [i prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) per i dettagli.  

A differenza dei modelli standard di Custom Translator, i modelli Hub saranno disponibili solo in una singola area, pertanto i costi di hosting in più aree non saranno applicabili.  Una volta distribuito, sarà possibile annullare la distribuzione del modello Hub in qualsiasi momento tramite il progetto Custom Translator migrato.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il training di un modello](how-to-train-model.md).
- Iniziare a usare il modello di traduzione personalizzata distribuito tramite l'[API Traduzione testuale Microsoft V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
