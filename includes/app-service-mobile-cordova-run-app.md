---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240248"
---
1. Passare al file della soluzione nel progetto client (con estensione sln) e aprirlo con Visual Studio.

2. In Visual Studio, scegliere la piattaforma di soluzioni (Android, iOS o Windows) dall'elenco a discesa accanto alla freccia di inizio. Selezionare un dispositivo di distribuzione specifico o un emulatore facendo clic sul menu a discesa sulla freccia verde. È possibile usare la piattaforma Android e l'emulatore Ripple predefiniti. Per esercitazioni più avanzate (ad esempio, sulle notifiche push) sarà necessario selezionare un dispositivo o un emulatore supportato.

3. Aprire il file `ToDoActivity.java` in questa cartella - ZUMOAPPNAME/app/src/main/java/com/esempio/zumoappname. Il nome dell'applicazione è `ZUMOAPPNAME`.

4. Andare alla [portale di Azure](https://portal.azure.com/) e passare all'app per dispositivi mobili che è stato creato. Nel `Overview` pannello, cercare l'URL a cui l'endpoint pubblico per l'app per dispositivi mobili. Esempio: il nome di sito per il nome di app "test123" saranno https://test123.azurewebsites.net.

5. Andare alla `index.js` file in ZUMOAPPNAME/www/js/index.js e nella `onDeviceReady()` metodo, sostituire `ZUMOAPPURL` parametro con endpoint pubblici precedente.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    diventa
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Per compilare ed eseguire l'app Cordova, premere F5 o fare clic sulla freccia verde. Se nell'emulatore viene visualizzata una finestra di dialogo di sicurezza che richiede di accedere alla rete, accettare.

7. Dopo che l'app viene avviata nel dispositivo o emulatore, digitare un testo significativo nel **immettere nuovo testo**, ad esempio *completare l'esercitazione* e quindi fare clic sul **Aggiungi** pulsante.

Il back-end inserisce i dati della richiesta nella tabella TodoItem del database SQL e restituisce informazioni sugli elementi appena archiviati all'app per dispositivi mobili. L'app per dispositivi mobili consente di visualizzare questi dati nell'elenco.

È possibile ripetere i passaggi da 3 a 5 per altre piattaforme.