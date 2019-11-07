---
title: Note sulla versione - Servizio visione artificiale personalizzato
titleSuffix: Azure Cognitive Services
description: Ottenere le informazioni più recenti sulle nuove versioni dal team di Visione personalizzata.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: b9606c63ec7b53fb0b69918c21bdd9206f34c555
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647565"
---
# <a name="custom-vision-service-release-notes"></a>Note sulla versione di Servizio visione artificiale personalizzato

## <a name="may-2-2019-and-may-10-2019"></a>2 maggio 2019 e 10 maggio 2019

- Correzioni di bug e miglioramenti al back-end

## <a name="may-23-2019"></a>23 maggio, 2019

- Miglioramento dell'esperienza UX del portale correlato alle sottoscrizioni di Azure, semplificando la selezione delle directory di Azure.

## <a name="april-18-2019"></a>18 aprile 2019 

- Aggiunta dell'esportazione di rilevamento oggetti per Vision AI Dev Kit.
- Modifiche dell'interfaccia utente, inclusa la ricerca di progetti.

## <a name="april-3-2019"></a>3 aprile 2019

- Aumento del limite per il numero di caselle di delimitazione per immagine a 200. 
- Bugfix, incluso un aggiornamento sostanziale delle prestazioni per i modelli esportati in TensorFlow. 

## <a name="march-26-2019"></a>26 marzo 2019

- Servizio visione artificiale personalizzato è stata immessa la disponibilità generale in Azure.
- È stata aggiunta una funzionalità di formazione avanzata con un nuovo back-end di machine learning per migliorare le prestazioni, in particolare per i set di impostazioni complessi e la classificazione con granularità fine Grazie alla formazione avanzata, è possibile specificare un budget per il tempo di calcolo per il training e Visione personalizzata consente di identificare in modo sperimentale le impostazioni migliori per la formazione e l'incremento. Per le iterazioni rapide, è possibile continuare a utilizzare il training rapido esistente.
- Sono state introdotte 3,0 API. È stata annunciata la deprecazione delle API precedenti a 3,0 il 1 ° ottobre 2019. Per esempi su come iniziare, vedere le guide introduttive alla documentazione per [.NET](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [node](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial)o [go](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) .
- Sono state sostituite "iterazioni predefinite" con pubblicazione/Annulla pubblicazione nelle API 3,0.
- Sono state aggiunte nuove destinazioni di esportazione del modello. L'esportazione di Dockerfile è stata aggiornata per supportare ARM per Raspberry Pi 3. Il supporto per l'esportazione è stato aggiunto a [Vision ai Dev Kit.](https://visionaidevkit.com/)..
- Maggiore limite di tag per progetto a 500 per il livello s0. Aumento del limite delle immagini per progetto a 100.000 per il livello s0.
- Il dominio per adulti è stato rimosso. È invece consigliabile usare il dominio generale.
- [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) annunciati per la disponibilità generale.  

## <a name="february-25-2019"></a>25 febbraio 2019

- È stata annunciata la fine dei progetti di valutazione limitati (progetti non associati a una risorsa di Azure), come Visione personalizzata al completamento del passaggio all'anteprima pubblica di Azure. A partire dal 25 marzo 2019, il sito di CustomVision.ai supporterà solo la visualizzazione di progetti associati a una risorsa di Azure, ad esempio la risorsa Visione personalizzata gratuita. Fino al 1 ° ottobre 2019, sarà comunque possibile accedere ai progetti di valutazione limitati esistenti tramite le API Visione personalizzata. Questo consentirà di aggiornare le chiavi API per tutte le app scritte con Visione personalizzata. Dopo il 1 ° ottobre 2019, tutti i progetti di valutazione limitati non spostati in Azure verranno eliminati.

## <a name="january-22-2019"></a>22 gennaio 2019

- È stato aggiunto il supporto per le nuove aree di Azure: Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti orientali 2, Europa occidentale, Europa settentrionale, Asia sudorientale, Australia orientale, India centrale, Regno Unito meridionale, Giappone orientale e Stati Uniti centro-settentrionali. Il supporto è valido anche per gli Stati Uniti centro-meridionali.

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
- Aggiunta la funzionalità "Sposta in Azure" al sito Web per facilitare l'aggiornamento di un progetto in versione di valutazione limitata per il collegamento ad Azure. progetto collegato alle risorse (F0 o S0) È possibile trovarlo nella pagina delle impostazioni del prodotto.  
- Aggiunta l'esportazione in ONNX 1.2 per supportare la versione di aggiornamento di Windows di ottobre 2018 per Windows ML.
Correzioni di bug, ad esempio per l'esportazione ONNX con caratteri speciali.

## <a name="august-14-2018"></a>14 agosto 2018

- Aggiunto il widget "Get Started" al sito customvision.ai per guidare gli utenti nel training del progetto.
- Ulteriori miglioramenti alla pipeline di apprendimento automatico per usufruire dei progetti multietichetta (nuovo livello di perdita).

## <a name="june-28-2018"></a>28 giugno 2018

- Correzioni di bug & miglioramenti del back-end.
- Abilitazione della classificazione multiclasse per i progetti in cui le immagini hanno esattamente un'etichetta. Nelle stime per la modalità multiclasse, le probabilità verranno sommate a una (tutte le immagini vengono classificate tra i tag specificati).

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

- È stata immessa un'anteprima a pagamento e caricata nel portale di Azure. I progetti possono ora essere collegati alle risorse di Azure con un livello F0 (gratuito) o S0 (Standard). 50 progetti di livello S0 che consentono fino a 100 tag e 25.000 immagini.
- Modifiche di back-end alla pipeline di Machine Learning/parametro di normalizzazione. Ciò offrirà ai clienti un migliore controllo sul compromesso precisione/richiamo quando si modifica la soglia di probabilità. Come parte di queste modifiche, il valore predefinito della soglia di probabilità nel portale CustomVision.ai è stato impostato su 50%.

## <a name="december-19-2017"></a>19 dicembre 2017

- L'esportazione in Android (TensorFlow) è stata aggiunta, oltre a quella rilasciata in precedenza in iOS (CoreML). In questo modo è possibile eseguire l'esportazione di un modello compatto sottoposto a training in un'applicazione.
- Sono stati aggiunti i domini compatti Retail e Landmark per consentire l'esportazione dei modelli per questi domini.
- Sono state rilasciate le versioni [1.2 dell'API Training](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) e [1.1 dell'API Prediction](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Le API aggiornate supportano l'esportazione di modelli, è stata introdotta una nuova operazione dell'API Prediction che non salva le immagini in "Predictions" e sono state aggiunte operazioni in batch nell'API Training.
- Modifiche all'esperienza utente, inclusa la possibilità di visualizzare i domini usati per eseguire il training di un'iterazione.
- Sono stati aggiornati l'[SDK C# e l'esempio](https://github.com/Microsoft/Cognitive-CustomVision-Windows).