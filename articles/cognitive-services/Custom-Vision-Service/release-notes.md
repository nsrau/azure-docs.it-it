---
title: Note sulla versione di Servizio visione artificiale personalizzato
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.date: 08/28/2018
ms.author: anroth
ms.openlocfilehash: 8423051fa5169eb8acddc7297e36188e297cb9e4
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43288653"
---
# <a name="custom-vision-service-release-notes"></a>Note sulla versione di Servizio visione artificiale personalizzato

## <a name="august-14-2018"></a>14 agosto 2018
- Aggiunto il widget "Get Started" al sito customvision.ai per guidare gli utenti nel training del progetto. 
- Ulteriori miglioramenti alla pipeline di apprendimento automatico per usufruire dei progetti multietichetta (nuovo livello di perdita).

## <a name="june-28-2018"></a>28 giugno 2018
- Miglioramenti alla correzione di bug e back-end.
- Abilitata la classificazione multiclasse, per i progetti in cui le immagini hanno esattamente un'etichetta. Nelle stime per la modalità multiclasse, le probabilità avranno la somma di uno (tutte le immagini vengono classificate tra i tag specificati).

## <a name="june-13-2018"></a>13 giugno 2018
- Aggiornamento UX, incentrato sulla facilità d'uso e accessibilità. 
- Miglioramenti alla pipeline di apprendimento automatico per usufruire dei progetti multietichetta con un elevato numero di tag.
- Correzione del bug nell'esportazione TensorFlow. Abilitata la gestione della versione dei modelli esportati, in modo che le iterazioni possano essere esportate più volte. 

## <a name="may-7-2018"></a>7 maggio 2018
- È stata introdotta la funzionalità Rilevamento di oggetti di anteprima per i progetti della versione di valutazione gratuita limitata.
- È stato eseguito l'aggiornamento alle API 2.0
- Il livello S0 è stato espanso fino a 250 tag e 50.000 immagini. 
- Miglioramenti di back-end significativi per la pipeline di Machine Learning per i progetti di classificazione delle immagini. Questi aggiornamenti saranno disponibili per i progetti sottoposti a training dopo il 27 aprile 2018.
- È stata aggiunta l'esportazione di modelli in ONNX, per l'uso con Machine Learning di Windows.
- È stata aggiunta l'esportazione di modelli in Dockerfile. Ciò consente di scaricare gli artefatti per la creazione di contenitori Windows o Linux personalizzati, inclusi DockerFile, un modello TensorFlow e un codice di servizio. 
- Per i modelli di cui è stato eseguito il training esportati in TensorFlow nei domini General (Compact) e Landmark (Compact), i [valori medi sono ora (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample) per uniformità tra i progetti. 

## <a name="march-1-2018"></a>1 marzo 2018
- L'anteprima a pagamento è stata caricata nel portale di Azure. I progetti possono ora essere collegati alle risorse di Azure con un livello F0 (gratuito) o S0 (Standard). 50 progetti di livello S0 che consentono fino a 100 tag e 25.000 immagini. 
- Modifiche di back-end alla pipeline di Machine Learning/parametro di normalizzazione. Ciò offrirà ai clienti un migliore controllo sul compromesso precisione/richiamo quando si modifica la soglia di probabilità. Come parte di queste modifiche, il valore predefinito della soglia di probabilità nel portale CustomVision.ai è stato impostato su 50%.

## <a name="december-19-2017"></a>19 dicembre 2017

- È stata aggiunta l'esportazione in Android (TensorFlow), oltre all'esportazione già rilasciata in iOS (CoreML). Ciò consente l'esportazione di un modello compatto con training da eseguire offline in un'applicazione.
- Sono stati aggiunti i domini compatti Retail e Landmark per consentire l'esportazione dei modelli per questi domini.
- Sono state rilasciate le versioni [1.2 dell'API Training](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) e [1.1 dell'API Prediction](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Le API aggiornate supportano l'esportazione di modelli, è stata introdotta una nuova operazione dell'API Prediction che non salva le immagini in "Predictions" e sono state aggiunte operazioni in batch nell'API Training.
- Modifiche all'esperienza utente, inclusa la possibilità di visualizzare i domini usati per eseguire il training di un'iterazione.
- Sono stati aggiornati l'[SDK C# e l'esempio](https://github.com/Microsoft/Cognitive-CustomVision-Windows).

