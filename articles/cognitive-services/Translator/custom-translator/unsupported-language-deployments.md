---
title: Distribuzioni di linguaggio non supportato - traduzione personalizzati
titleSuffix: Azure Cognitive Services
description: Come distribuire coppie lingua non supportata nella funzione di conversione personalizzata.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: v-pawal
ms.openlocfilehash: 0938ba2e839be603c557cc2a87dd0c5aa1dfe1e3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390577"
---
# <a name="unsupported-language-deployments"></a>Distribuzioni in lingue non supportate

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Con l'imminente ritiro di Microsoft Translator Hub, Microsoft sarà possibile annullare la distribuzione di tutti i modelli attualmente distribuiti tramite l'Hub. Molti dispongono di modelli distribuiti nell'Hub di cui coppie di linguaggi non sono supportate in funzione di conversione personalizzata.  Non vogliamo che gli utenti in questo caso abbiano alcuna fa ricorso per la conversione di contenuto.

È ora disponibile un processo che consente di distribuire i modelli non supportati tramite la funzione di conversione personalizzata.  Questo processo consente di continuare a convertire il contenuto usando le API più recenti di V3.  Questi modelli verranno ospitati fino a quando non si sceglie di annullare la distribuzione di loro o la coppia di linguaggi viene reso disponibile in Microsoft Translator personalizzato.  Questo articolo illustra la procedura per distribuire i modelli con coppie di linguaggi non supportati.

## <a name="prerequisites"></a>Prerequisiti

Affinché i modelli essere candidati per la distribuzione, si devono soddisfare i criteri seguenti:
* Il progetto che contiene il modello deve sono stato migrato dall'Hub per la funzione di conversione personalizzata usando lo strumento di migrazione.  Il processo di migrazione di progetti e le aree di lavoro sono reperibili [qui](how-to-migrate.md).
* Il modello deve essere in stato distribuito quando si verifica della migrazione.  
* La coppia di linguaggi del modello deve essere una coppia di linguaggio non supportato nella funzione di conversione personalizzata.  Coppie di linguaggi, in cui una lingua supportata da o verso l'inglese, ma la coppia di se stesso non include l'inglese, sono candidati per le distribuzioni di linguaggio non supportato.  Ad esempio, un modello di Hub per francese-to-pair in lingua tedesca viene considerato una coppia di lingua supportata anche se in francese per inglese e in lingua inglese per tedesco sono anche coppia lingua non supportata.

## <a name="process"></a>Process
Dopo avere eseguito la migrazione di modelli dall'Hub di candidati per la distribuzione, è possibile trovarli, passare al **le impostazioni** pagina relativa all'area di lavoro e lo scorrimento alla fine della pagina in cui verrà visualizzato un **non supportata Corsi di formazione di Microsoft Translator Hub** sezione.  In questa sezione viene visualizzata solo se si hanno progetti che soddisfino i prerequisiti indicati in precedenza.

![Come eseguire la migrazione da Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

All'interno di **non supportato corsi di formazione di Microsoft Translator Hub** pagina Selezione il **acknowledgement corsi di formazione** scheda contiene i modelli che sono idonei per la distribuzione.  Selezionare i modelli che si desidera distribuire e inviare una richiesta.   Prima della scadenza di distribuzione al 30 aprile, è possibile selezionare tutti i modelli come desiderato per la distribuzione.
 
![Come eseguire la migrazione da Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Dopo l'invio, il modello non saranno disponibile nel **acknowledgement corsi di formazione** scheda e verrà invece visualizzata nel **richiesto corsi di formazione** scheda.  È possibile visualizzare i corsi di formazione richiesti in qualsiasi momento.

![Come eseguire la migrazione da Hub](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Passaggi successivi

I modelli selezionati per la distribuzione vengono salvati dopo che l'Hub viene messo fuori servizio e tutti i modelli vengono annullati.  Hai tempo fino al 24 maggio l'invio di richieste per la distribuzione di modelli non supportati.  Questi modelli dal 15 giugno a quel punto sarà accessibile tramite l'API di Microsoft Translator V3 verrà distribuita.  Inoltre, saranno disponibili tramite l'API V2 fino al 1 ° luglio.  

Per altre informazioni sulle date importanti nella rimozione del controllo Hub [qui](https://www.microsoft.com/translator/business/hub/).
Una volta distribuiti, le normali costi di hosting verranno applicati.  Visualizzare [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) per informazioni dettagliate.  

A differenza dei modelli di funzione di conversione personalizzata standard, i modelli di Hub sarà disponibili solo in una singola area, in modo che non si applicheranno gli addebiti di hosting di più aree.  Una volta distribuito, sarà possibile annullare la distribuzione del modello Hub in qualsiasi momento tramite il progetto di funzione di conversione personalizzata migrato.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il training di un modello](how-to-train-model.md).
- Iniziare a usare il modello di traduzione personalizzata distribuito tramite l'[API Traduzione testuale Microsoft V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
