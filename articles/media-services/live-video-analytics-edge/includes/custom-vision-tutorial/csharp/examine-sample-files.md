---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 359c5f93516ea6f0561865bd86e4f51dedb4c3a5
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358239"
---
1. In Visual Studio Code passare a src/edge. Verranno visualizzati il file con estensione env creato e alcuni file del modello di distribuzione.

    Il modello di distribuzione fa riferimento al manifesto della distribuzione per il dispositivo perimetrale con alcuni valori segnaposto. Il file con estensione env include i valori per queste variabili.
1. Passare quindi alla cartella src/cloud-to-device-console-app. Verrà visualizzato il file appsettings.json creato insieme ad altri file:

    * c2d-console-app.csproj: file di progetto per Visual Studio Code.
    * operations.json: questo file elenca le diverse operazioni che il programma deve eseguire.
    * Program.cs codice del programma di esempio, che:

        * Carica le impostazioni dell'app.
        * Richiama Analisi video live sui metodi diretti del modulo IoT Edge per creare la topologia, creare un'istanza del grafo e attivare il grafo.
        * Sospende l'esecuzione per consentire di esaminare l'output del grafo nella finestra **TERMINALE** e gli eventi inviati all'hub IoT nella finestra **OUTPUT**.
        * Disattiva ed elimina l'istanza del grafo, quindi elimina la topologia del grafo.
