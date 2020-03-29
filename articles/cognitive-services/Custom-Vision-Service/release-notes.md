---
title: Note sulla versione - Servizio visione artificiale personalizzato
titleSuffix: Azure Cognitive Services
description: Ottieni le informazioni più recenti sulle nuove versioni dal team di Custom Vision.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: b9606c63ec7b53fb0b69918c21bdd9206f34c555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647565"
---
# <a name="custom-vision-service-release-notes"></a>Note sulla versione di Servizio visione artificiale personalizzato

## <a name="may-2-2019-and-may-10-2019"></a>2 maggio 2019 e 10 maggio 2019

- Correzioni di bug e miglioramenti del back-end

## <a name="may-23-2019"></a>giovedì 23 maggio 2019

- Esperienza dell'esperienza utente del portale migliorata correlata alle sottoscrizioni di Azure, semplificando la selezione delle directory di Azure.Improved portal UX experience related to Azure subscriptions, making it easier to select your Azure directories.

## <a name="april-18-2019"></a>18 aprile 2019 

- Aggiunta l'esportazione di rilevamento oggetti per il kit di sviluppo Vision AI.
- Modifiche dell'interfaccia utente, inclusa la ricerca di progetto.

## <a name="april-3-2019"></a>giovedì 3 aprile 2019

- Aumento del limite di numero di rettangoli di selezione per immagine a 200. 
- Correzioni di bug, incluso un aggiornamento sostanziale delle prestazioni per i modelli esportati in TensorFlow.Bugfixes, including substantial performance update for models exported to TensorFlow. 

## <a name="march-26-2019"></a>26 marzo 2019

- Il servizio di visione personalizzato ha immesso Disponibilità generale in Azure.Custom Vision Service has entered General Availability on Azure!
- Aggiunta la funzionalità di formazione avanzata con un nuovo back-end di apprendimento automatico per migliorare le prestazioni, in particolare per i set di dati impegnativi e la classificazione granulare. Con la formazione avanzata, è possibile specificare un budget di tempo di calcolo per l'allenamento e Visione personalizzata identificherà sperimentalmente le migliori impostazioni di training e aumento. Per le iterazioni rapide, è possibile continuare a utilizzare il training rapido esistente.
- Sono state introdotte le API 3.0. Annunciato l'arrivo della deprecazione delle API pre-3.0 il 1 ottobre 2019. Per alcuni esempi su come iniziare, vedere le guide introduttive della documentazione relativa a [.Net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [Node](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial)o [Go](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) .
- Sostituito "Iterazioni predefinite" con Pubblica/Annulla pubblicazione nelle API 3.0.
- Sono stati aggiunti nuovi obiettivi di esportazione del modello. Dockerfile esportazione è stato aggiornato per supportare ARM per Raspberry Pi 3. Il supporto per l'esportazione è stato aggiunto al Kit di [sviluppo Vision AI.](https://visionaidevkit.com/).
- Limite di tag per progetto a 500 per il livello S0. Limite di immagini per progetto a 100.000 per il livello S0.
- Rimosso dominio per adulti. È invece consigliabile il dominio generale.
- [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) annunciati per la disponibilità generale.  

## <a name="february-25-2019"></a>25 febbraio 2019

- Annunciata la fine dei progetti di valutazione limitata (progetti non associati a una risorsa di Azure), poiché Visione personalizzata si avvicina al completamento del passaggio all'anteprima pubblica di Azure.Announced the end of Limited Trial projects (projects not associated with an Azure resource), as Custom Vision nears completion of its move to Azure public preview. A partire dal 25 marzo 2019, il sito CustomVision.ai supporterà solo la visualizzazione dei progetti associati a una risorsa di Azure, ad esempio la risorsa visione personalizzata gratuita. Fino al 1 ottobre 2019, potrai comunque accedere ai tuoi progetti di prova limitati esistenti tramite le API Di visione personalizzata. Questo ti darà il tempo di aggiornare le chiavi API per tutte le app che hai scritto con Custom Vision. Dopo il 1 ottobre 2019, tutti i progetti di valutazione limitati che non sono stati spostati in Azure verranno eliminati.

## <a name="january-22-2019"></a>22 gennaio 2019

- Supporto aggiunto per le nuove aree di Azure: Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti orientali 2, Europa occidentale, Nord Europa, Asia sud-orientale, Australia orientale, India centrale, Regno Unito meridionale, Giappone orientale e Stati Uniti centro-settentrionali. Il supporto è valido anche per gli Stati Uniti centro-meridionali.

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
- Aggiunta la funzionalità "Sposta in Azure" al sito Web per facilitare l'aggiornamento di un progetto in versione di valutazione limitata per il collegamento ad Azure. progetto collegato alle risorse (F0 o S0. Puoi trovarlo nella pagina Impostazioni del tuo prodotto.  
- Aggiunta l'esportazione in ONNX 1.2 per supportare la versione di aggiornamento di Windows di ottobre 2018 per Windows ML.
Correzioni di bug, anche per l'esportazione ONNX con caratteri speciali.

## <a name="august-14-2018"></a>14 agosto 2018

- Aggiunto il widget "Get Started" al sito customvision.ai per guidare gli utenti nel training del progetto.
- Ulteriori miglioramenti alla pipeline di apprendimento automatico per usufruire dei progetti multietichetta (nuovo livello di perdita).

## <a name="june-28-2018"></a>28 giugno 2018

- Correzioni di bug & miglioramenti di back-end.
- Classificazione multiclasse abilitata, per i progetti in cui le immagini hanno esattamente un'etichetta. In Predictions for multiclass mode, le probabilità verranno sommate a una (tutte le immagini sono classificate tra i tag specificati).

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

- È stata immessa l'anteprima a pagamento e l'onboarding nel portale di Azure.Entered paid preview and onboarded onto the Azure portal. I progetti possono ora essere collegati alle risorse di Azure con un livello F0 (gratuito) o S0 (Standard). 50 progetti di livello S0 che consentono fino a 100 tag e 25.000 immagini.
- Modifiche di back-end alla pipeline di Machine Learning/parametro di normalizzazione. Ciò offrirà ai clienti un migliore controllo sul compromesso precisione/richiamo quando si modifica la soglia di probabilità. Come parte di queste modifiche, il valore predefinito della soglia di probabilità nel portale CustomVision.ai è stato impostato su 50%.

## <a name="december-19-2017"></a>19 dicembre 2017

- Esportazione in Android (TensorFlow) aggiunto, oltre all'esportazione rilasciata in precedenza in iOS (CoreML.) Ciò consente l'esportazione di un modello compatto sottoposto a training da eseguire offline in un'applicazione.
- Sono stati aggiunti i domini compatti Retail e Landmark per consentire l'esportazione dei modelli per questi domini.
- Sono state rilasciate le versioni [1.2 dell'API Training](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) e [1.1 dell'API Prediction](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Le API aggiornate supportano l'esportazione di modelli, è stata introdotta una nuova operazione dell'API Prediction che non salva le immagini in "Predictions" e sono state aggiunte operazioni in batch nell'API Training.
- Modifiche all'esperienza utente, inclusa la possibilità di visualizzare i domini usati per eseguire il training di un'iterazione.
- Sono stati aggiornati l'[SDK C# e l'esempio](https://github.com/Microsoft/Cognitive-CustomVision-Windows).