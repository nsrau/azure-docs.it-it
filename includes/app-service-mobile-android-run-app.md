---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240327"
---
1. Aprire il progetto usando **Android Studio**, con **Import project (Eclipse ADT, Gradle, etc.)** (Importa progetto - Eclipse ADT, Gradle e così via). Assicurarsi di selezionare questa importazione per evitare eventuali errori JDK.

2. Aprire il file `ToDoActivity.java` in questa cartella - ZUMOAPPNAME/app/src/main/java/com/esempio/zumoappname. Il nome dell'applicazione è `ZUMOAPPNAME`.

3. Andare alla [portale di Azure](https://portal.azure.com/) e passare all'app per dispositivi mobili che è stato creato. Nel `Overview` pannello, cercare l'URL a cui l'endpoint pubblico per l'app per dispositivi mobili. Esempio: il nome di sito per il nome di app "test123" saranno https://test123.azurewebsites.net.

4. Nelle `onCreate()` metodo, sostituire `ZUMOAPPURL` parametro con endpoint pubblici precedente.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    diventa
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Fare clic sul pulsante **Esegui ‘app’** per creare il progetto e avviare l'app nel simulatore Android.

4. Nell'app digitare un testo significativo, ad esempio *Complete the tutorial* quindi fare clic sul pulsante ’Add’. Verrà inviata una richiesta POST al back-end di Azure distribuito in precedenza. Il back-end inserisce i dati della richiesta nella tabella SQL TodoItem e restituisce le informazioni sugli elementi appena archiviati all'app per dispositivi mobili. L'app per dispositivi mobili consente di visualizzare questi dati nell'elenco.
    ![Avvio rapido di Android](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)