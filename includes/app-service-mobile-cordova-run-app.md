---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240248"
---
1. Passare al file di soluzione nel progetto client (sln) e aprirlo con Visual Studio.Navigate to the solution file in the client project (.sln) and open it using Visual Studio.

2. In Visual Studio, scegliere la piattaforma di soluzioni (Android, iOS o Windows) dall'elenco a discesa accanto alla freccia di inizio. Selezionare un dispositivo di distribuzione specifico o un emulatore facendo clic sul menu a discesa sulla freccia verde. È possibile usare la piattaforma Android e l'emulatore Ripple predefiniti. Per esercitazioni più avanzate (ad esempio, sulle notifiche push) sarà necessario selezionare un dispositivo o un emulatore supportato.

3. Aprire il `ToDoActivity.java` file in questa cartella - NOME/app/src/main/java/com/example/zumoappname. Il nome `ZUMOAPPNAME`dell'applicazione è .

4. Passare al portale di [Azure](https://portal.azure.com/) e passare all'app per dispositivi mobili creata. Nel `Overview` pannello cercare l'URL che è l'endpoint pubblico per l'app per dispositivi mobili. Esempio: il nome del sito per il nome https://test123.azurewebsites.netdell'app "test123" sarà .

5. Andare al `index.js` file in NOME/www/js/index.js e, `onDeviceReady()` nel `ZUMOAPPURL` metodo, sostituire il parametro con l'endpoint pubblico sopra.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    diventa
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Per compilare ed eseguire l'app Cordova, premere F5 o fare clic sulla freccia verde. Se nell'emulatore viene visualizzata una finestra di dialogo di sicurezza che richiede di accedere alla rete, accettare.

7. Dopo aver avviato l'app nel dispositivo o nell'emulatore, digitare un testo significativo in **Immettere nuovo testo,** ad esempio *Completare l'esercitazione* e quindi fare clic sul pulsante **Aggiungi.**

Il back-end inserisce i dati della richiesta nella tabella TodoItem del database SQL e restituisce informazioni sugli elementi appena archiviati all'app per dispositivi mobili. L'app per dispositivi mobili consente di visualizzare questi dati nell'elenco.

È possibile ripetere i passaggi da 3 a 5 per altre piattaforme.