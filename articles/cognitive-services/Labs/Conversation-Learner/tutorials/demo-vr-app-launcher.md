---
title: Modello dimostrativo di apprendimento della conversazione, utilità di avvio di app di realtà virtuale - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come creare un modello dimostrativo di apprendimento della conversazione.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 31efcb2b2f05bd7a49701be708a89db8f2ef26f7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229698"
---
# <a name="demo-virtual-reality-app-launcher"></a>Demo: Utilità di avvio di app di realtà virtuale

Questa demo illustra un'utilità di avvio di un modello di realtà virtuale che supporta comandi come "start Skype and put in on the wall" (avvia Skype e mettilo sul desktop). Per avviare l'app, un utente deve immettere un nome di app e una posizione. L'avvio del modello viene gestito da una chiamata API. Quando viene riconosciuto un nome di app proveniente dall'utente, entityDetectionCallback controlla se l'app richiesta corrisponde a una o più app nell'elenco delle app installate e gestisce i casi in cui l'app richiesta non è installata o il nome di app è ambiguo, ossia corrisponde a più app installate.

## <a name="video"></a>Video

[![Anteprima dell'app di realtà virtuale dimostrativa](https://aka.ms/cl-demo-vrapp-preview)](https://aka.ms/blis-demo-vrapp)

## <a name="requirements"></a>Requisiti

Per questa esercitazione è necessario che il bot VRAppLauncher sia in esecuzione.

    npm run demo-vrapp
    
### <a name="open-the-demo"></a>Aprire la demo

Nell'elenco Model (Modello) dell'interfaccia utente Web fare clic su VRAppLauncher. 

## <a name="entities"></a>Entità

Sono state create quattro entità.

- AppName: ad esempio, Skype
- PlacementLocation: ad esempio, la bacheca
- UnknownAppName: entità a livello di codice che viene impostata dal sistema quando non riconosce un nome di entità immesso dall'utente, ad esempio perché l'app non è stata installata.
- DisAmbigAppNames: matrice di due o più nomi di app installate che corrispondono all'input dell'utente. 

![](../media/tutorial_vrapplauncher_entities.PNG)

### <a name="actions"></a>Azioni

È stato creato un set di azioni che include un'API denominata LaunchApp che avvierà la chiamata di funzione per l'avvio di un'app.

![](../media/tutorial_vrapplauncher_actions.PNG)

### <a name="training-dialogs"></a>Dialoghi di training
Sono stati definiti diversi dialoghi di training.

![](../media/tutorial_vrapplauncher_dialogs.PNG)

A titolo di esempio, si proverà una sessione di training.

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
1. Immettere 'hi'.
2. Fare clic su Score Action (Punteggio azione).
3. Fare clic per selezionare "Which app do you want to start?".
4. Immettere "outlook".
    - LUIS riconosce questa parola come entità.
5. Fare clic su Score Actions (Punteggio azioni).
3. Fare clic per selezionare "Where do you want it placed?".
4. Immettere "on the wall".
    - LUIS riconosce questa frase come entità PlacementLocation.
2. Passare a Score Actions (Punteggio azioni).
6. Selezionare "LaunchApp".
7. Sistema: "starting outlook on the wall".
    - Viene così attivata una chiamata API. Il codice per la chiamata si trova in C:\<\percorsoinstallazione>\src\demos\demoVRAppLauncher.ts. Questo file, tuttavia, non contiene effettivamente il codice per avviare Outlook per questa demo
    - e cancella le entità AppName e PlacementLocation. Restituisce quindi la stringa riportata sopra come risposta.
4. Fare clic su Done Teaching (Training completato).

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

Avviare un'altra sessione di training per gestire le entità sconosciute o ambigue.

1. Fare clic su New Train Dialog (Nuovo dialogo di training).
1. Immettere "start OneNote". 
    - Il modello riconosce OneNote come nome dell'app. La funzione `EntityDetectionCallback` definita nel codice risolve il nome immesso dall'utente in un nome di app confrontandolo con l'elenco di app definito nel codice. Restituisce quindi il set di tutte le app corrispondenti. 
    - Se l'elenco contiene zero corrispondenze, ciò significa che l'app non è installata e il nome viene inserito in unknownAppName.
    - Se vengono trovate più app, vengono copiate in `DisambigAppNames` e viene cancellata l'entità AppName.
2. Fare clic su Score Action (Punteggio azione).
3. Fare clic per selezionare "Sorry, I don't know the app $UknownAppName".
4. Immettere "start Amazon". Si proverà l'altro percorso.
5. Fare clic su Score Actions (Punteggio azioni).
    - Amazon Video e Amazon Music sono ora nella memoria di `DisambigAppNames` ed è stata cancellata la memoria e OneNote.
3. Fare clic per selezionare "There are few apps that sound like that...".
    - Il punteggio non è molto elevato perché al momento sono disponibili pochi dialoghi di training. La definizione di un maggior numero di finestre di dialogo di training renderebbe il modello più determinante.
2. Passare a Score Actions (Punteggio azioni).
4. Fare clic su Done Teaching (Training completato).

È stato dimostrato come eseguire la risoluzione delle entità. La demo ha anche illustrato i callback API e un modello per raccogliere le informazioni, verificare presenza e ambiguità ed eseguire la conseguente azione corretta.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuzione di un bot di Conversation Learner](../deploy-to-bf.md)
