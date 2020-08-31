---
ms.openlocfilehash: 46e8609be2ec57600e666ef9dab33eae2900e1ce
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691119"
---
1. In Visual Studio Code passare a *src/edge*. Verranno visualizzati il file *.env* e alcuni file modello di distribuzione.

    Il modello di distribuzione fa riferimento al manifesto della distribuzione per il dispositivo perimetrale, in cui per alcune proprietà vengono usate delle variabili. Il file *.env* contiene i valori per queste variabili.
1. Passare alla cartella *src/cloud-to-device-console-app*. Sono inclusi il file *appsettings.json* e alcuni altri file:

    * ***operations.json***: un elenco di operazioni che il programma dovrà eseguire.
    * **main.py**: il codice del programma di esempio. Questo codice:
    
      * Carica le impostazioni dell'app.
      * Richiama i metodi diretti esposti dal modulo Analisi video live in IoT Edge. È possibile usare il modulo per analizzare i flussi video live richiamando i [metodi diretti](../../../direct-methods.md).
      * Sospende l'esecuzione per consentire di esaminare l'output del programma nella finestra **TERMINALE** e gli eventi generati dal modulo nella finestra **OUTPUT**.
      * Richiama i metodi diretti per pulire le risorse.   

