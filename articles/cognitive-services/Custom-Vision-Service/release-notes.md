---
title: Note sulla versione - Servizio visione artificiale personalizzato
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: 2b0d8b8a86c3105b1bda7fb0d72cbcb72ed82995
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785726"
---
# <a name="custom-vision-service-release-notes"></a>Note sulla versione di Servizio visione artificiale personalizzato

## <a name="march-26-2019"></a>26 marzo 2019

- Servizio visione artificiale personalizzato entrato in disponibilità generale in Azure.
- Aggiunta funzionalità di formazione avanzata con un nuovo di machine learning back-end per migliorare le prestazioni, soprattutto nel set di dati complessa e la classificazione con granularità fine. Con corsi di formazione avanzata, è possibile specificare che un budget di tempo di calcolo per il training e visione artificiale personalizzato sperimentale identificherà le impostazioni migliori di training e di aumento. Per le iterazioni rapide, è possibile continuare a usare il training rapido esistente.
- Introdotto 3.0 API. Annunciato prossimi deprecazione delle API di pre-3,0 1 ottobre 2019. Vedere le guide rapide di documentazione per [.Net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [nodo](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial), o [passare](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) per alcuni esempi su come iniziare a usare.
- Sostituire "Default iterazioni" con pubblicare/annullare la pubblicazione in 3.0 API.
- Sono state aggiunte nuove destinazioni di esportazione del modello. Esportazione di Dockerfile è stato aggiornato per il supporto ARM per Raspberry Pi 3. Supporto per l'esportazione è stato aggiunto per il [Dev Kit di intelligenza artificiale per visione artificiale.](https://visionaidevkit.com/).
- Aumentare il limite di tag per ogni progetto fino a 500 per il livello S0. Aumentare il limite di immagini per ogni progetto a 100.000 per il livello S0.
- Dominio Adult rimossi. Settore generale è consigliabile invece.
- Annunciato [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) per la disponibilità generale.  

## <a name="february-25-2019"></a>25 febbraio 2019

- Fine della versione di valutazione limitata progetti (non associata a una risorsa di Azure), annunciato come visione artificiale personalizzato si avvicina al completamento del passaggio in anteprima pubblica di Azure. Il 25 marzo 2019, a partire il sito CustomVision.ai supporterà solo i progetti di visualizzazione associati a una risorsa di Azure, ad esempio la risorsa di Custom Vision gratuita. Tramite data 1 ottobre 2019, è comunque possibile accedere ai progetti di valutazione limitati esistenti tramite le API visione artificiale personalizzato. Questo fornirà utente il tempo necessario per aggiornare le chiavi API per le app che già scritto con visione artificiale personalizzato. Dopo il 1 ottobre 2019, verranno eliminati tutti i progetti alla versione di valutazione limitati è stato eseguito l'aggiornamento ad Azure.

## <a name="january-22-2019"></a>22 gennaio 2019

- Supporto aggiunto per le nuove aree di Azure: Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti orientali 2, Europa occidentale, Europa settentrionale, Asia sud-orientale, Australia orientale, India centrale, Regno Unito meridionale, Giappone orientale e Stati Uniti centro-settentrionali. Il supporto è valido anche per gli Stati Uniti centro-meridionali.

## <a name="december-12-2018"></a>12 dicembre 2018

- Aggiunto il supporto per l'esportazione dei modelli di rilevamento di oggetti (è stato introdotto il dominio compatto per il rilevamento di oggetti).
- Risolti alcuni problemi di accessibilità per una migliore lettura dello schermo e una più agevole navigazione da tastiera.
- Aggiornata l'esperienza utente per il visualizzatore delle immagini e migliorata l'esperienza di assegnazione dei tag per il rilevamento di oggetti per consentire l'assegnazione più rapida dei tag.  
- Aggiornato il modello di base per il dominio di rilevamento di oggetti per ottenere una qualità migliore nel rilevamento di oggetti.
- Correzioni di bug.

## <a name="november-6-2018"></a>6 novembre 2018

- Aggiunto il supporto per il dominio Logo nella funzionalità per il rilevamento di oggetti.

## <a name="october-9-2018"></a>9 ottobre 2018

- Rilevamento di oggetti entra in anteprima a pagamento. È ora possibile creare progetti Rilevamento di oggetti con una risorsa di Azure.
- Aggiunta la funzionalità "Sposta in Azure" al sito Web per facilitare l'aggiornamento di un progetto in versione di valutazione limitata per il collegamento ad Azure. progetto con risorse collegate (F0 o S0). È possibile trovarlo nella pagina Impostazioni per il prodotto.  
- Aggiunta l'esportazione in ONNX 1.2 per supportare la versione di aggiornamento di Windows di ottobre 2018 per Windows ML.
Correzioni di bug, tra cui per l'esportazione ONNX con caratteri speciali.

## <a name="august-14-2018"></a>14 agosto 2018

- Aggiunto il widget "Get Started" al sito customvision.ai per guidare gli utenti nel training del progetto.
- Ulteriori miglioramenti alla pipeline di apprendimento automatico per usufruire dei progetti multietichetta (nuovo livello di perdita).

## <a name="june-28-2018"></a>28 giugno 2018

- Correzioni di bug e miglioramenti di back-end.
- Abilitare la classificazione multiclasse, per i progetti in cui le immagini hanno esattamente un'etichetta. Nelle stime per la modalità multiclasse, le probabilità saranno la somma di uno (tutte le immagini vengono classificate tra i tag specificati).

## <a name="june-13-2018"></a>13 giugno 2018

- Aggiornamento UX, incentrato sulla facilità d'uso e accessibilità.
- Miglioramenti alla pipeline di apprendimento automatico per usufruire dei progetti multietichetta con un elevato numero di tag.
- Correzione del bug nell'esportazione TensorFlow. Abilitata la gestione della versione dei modelli esportati, in modo che le iterazioni possano essere esportate più di una volta.

## <a name="may-7-2018"></a>7 maggio 2018

- È stata introdotta la funzionalità Rilevamento di oggetti di anteprima per i progetti della versione di valutazione gratuita limitata.
- È stato eseguito l'aggiornamento alle API 2.0
- Il livello S0 è stato espanso fino a 250 tag e 50.000 immagini.
- Miglioramenti di back-end significativi per la pipeline di Machine Learning per i progetti di classificazione delle immagini. Questi aggiornamenti saranno disponibili per i progetti sottoposti a training dopo il 27 aprile 2018.
- È stata aggiunta l'esportazione di modelli in ONNX, per l'uso con Machine Learning di Windows.
- È stata aggiunta l'esportazione di modelli in Dockerfile. Ciò consente di scaricare gli artefatti per la creazione di contenitori Windows o Linux personalizzati, inclusi DockerFile, un modello TensorFlow e un codice di servizio.
- Per i modelli di cui è stato eseguito il training esportati in TensorFlow nei domini General (Compact) e Landmark (Compact), i [valori medi sono ora (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample) per uniformità tra i progetti.

## <a name="march-1-2018"></a>1 marzo 2018

- Immissione di anteprima a pagamento e caricato nel portale di Azure. I progetti possono ora essere collegati alle risorse di Azure con un livello F0 (gratuito) o S0 (Standard). 50 progetti di livello S0 che consentono fino a 100 tag e 25.000 immagini.
- Modifiche di back-end alla pipeline di Machine Learning/parametro di normalizzazione. Ciò offrirà ai clienti un migliore controllo sul compromesso precisione/richiamo quando si modifica la soglia di probabilità. Come parte di queste modifiche, il valore predefinito della soglia di probabilità nel portale CustomVision.ai è stato impostato su 50%.

## <a name="december-19-2017"></a>19 dicembre 2017

- È stata aggiunta l'esportazione in Android (TensorFlow), oltre all'esportazione già rilasciata in iOS (CoreML). Ciò consente l'esportazione di un modello compatto con training da eseguire offline in un'applicazione.
- Sono stati aggiunti i domini compatti Retail e Landmark per consentire l'esportazione dei modelli per questi domini.
- Sono state rilasciate le versioni [1.2 dell'API Training](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) e [1.1 dell'API Prediction](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Le API aggiornate supportano l'esportazione di modelli, è stata introdotta una nuova operazione dell'API Prediction che non salva le immagini in "Predictions" e sono state aggiunte operazioni in batch nell'API Training.
- Modifiche all'esperienza utente, inclusa la possibilità di visualizzare i domini usati per eseguire il training di un'iterazione.
- Sono stati aggiornati l'[SDK C# e l'esempio](https://github.com/Microsoft/Cognitive-CustomVision-Windows).