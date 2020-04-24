---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240257"
---
1. Aprire il progetto client scaricato usando Xcode.

2. Passare alla [portale di Azure](https://portal.azure.com/) e passare all'app per dispositivi mobili creata. `Overview` Nel pannello cercare l'URL che rappresenta l'endpoint pubblico per l'app per dispositivi mobili. Esempio: il SiteName per il nome dell'app "test123" https://test123.azurewebsites.netsarà.

3. Per SWIFT Project, aprire il file `ToDoTableViewController.swift` in questa cartella-ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController. Swift. Il nome dell'applicazione `ZUMOAPPNAME`è.

4. Nel `viewDidLoad()` metodo sostituire `ZUMOAPPURL` il parametro con l'endpoint pubblico precedente.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    diventa
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Per il progetto Objective-C, aprire il `QSTodoService.m` file in questa cartella-ZUMOAPPNAME/ZUMOAPPNAME. Il nome dell'applicazione `ZUMOAPPNAME`è.

6. Nel `init` metodo sostituire `ZUMOAPPURL` il parametro con l'endpoint pubblico precedente.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    diventa
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Fare clic su **Esegui** per creare il progetto e avviare l'app nel simulatore iOS.

8. Nell'app, fare clic sull'icona con**+** il segno più (), digitare un testo significativo, ad esempio *completare l'esercitazione*, quindi fare clic sul pulsante Salva. Verrà inviata una richiesta POST al back-end di Azure distribuito in precedenza. Il back-end inserisce i dati della richiesta nella tabella SQL TodoItem e restituisce le informazioni sugli elementi appena archiviati all'app per dispositivi mobili. L'app per dispositivi mobili consente di visualizzare questi dati nell'elenco.

   ![Guida introduttiva all'app che esegue iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
