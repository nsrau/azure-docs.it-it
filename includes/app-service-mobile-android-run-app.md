---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240327"
---
1. Aprire il progetto usando **Android Studio**, con **Import project (Eclipse ADT, Gradle, etc.)** (Importa progetto - Eclipse ADT, Gradle e così via). Assicurarsi di selezionare questa importazione per evitare eventuali errori JDK.

2. Aprire il `ToDoActivity.java` file in questa cartella - NOME/app/src/main/java/com/example/zumoappname. Il nome `ZUMOAPPNAME`dell'applicazione è .

3. Passare al portale di [Azure](https://portal.azure.com/) e passare all'app per dispositivi mobili creata. Nel `Overview` pannello cercare l'URL che è l'endpoint pubblico per l'app per dispositivi mobili. Esempio: il nome del sito per il nome https://test123.azurewebsites.netdell'app "test123" sarà .

4. Nel `onCreate()` metodo `ZUMOAPPURL` sostituire il parametro con l'endpoint pubblico precedente.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    diventa
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Fare clic sul pulsante **Esegui ‘app’** per creare il progetto e avviare l'app nel simulatore Android.

4. Nell'app digitare un testo significativo, ad esempio *Completare l'esercitazione,* quindi fare clic sul pulsante 'Aggiungi'. Verrà inviata una richiesta POST al back-end di Azure distribuito in precedenza. Il back-end inserisce i dati della richiesta nella tabella SQL TodoItem e restituisce le informazioni sugli elementi appena archiviati all'app per dispositivi mobili. L'app per dispositivi mobili consente di visualizzare questi dati nell'elenco.
    ![Guida introduttiva Android](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)