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
1. Aprire il progetto client scaricato utilizzando Xcode.

2. Passare al portale di [Azure](https://portal.azure.com/) e passare all'app per dispositivi mobili creata. Nel `Overview` pannello cercare l'URL che è l'endpoint pubblico per l'app per dispositivi mobili. Esempio: il nome del sito per il nome https://test123.azurewebsites.netdell'app "test123" sarà .

3. Per il progetto Swift, aprire il file `ToDoTableViewController.swift` in questa cartella: .UMOAPPNAME/'OMO.APPNAME/ToDoTableViewController.swift. Il nome `ZUMOAPPNAME`dell'applicazione è .

4. Nel `viewDidLoad()` metodo `ZUMOAPPURL` sostituire il parametro con l'endpoint pubblico precedente.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    diventa
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Per il progetto Objective-C, aprire il file `QSTodoService.m` in questa cartella, ovvero NOME.UOAPPNAME o .UMOAPPNAME. Il nome `ZUMOAPPNAME`dell'applicazione è .

6. Nel `init` metodo `ZUMOAPPURL` sostituire il parametro con l'endpoint pubblico precedente.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    diventa
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Fare clic su **Esegui** per creare il progetto e avviare l'app nel simulatore iOS.

8. Nell'app fare clic**+** sull'icona più ( ), digitare testo significativo, ad esempio *Completare l'esercitazione*, quindi fare clic sul pulsante Salva. Verrà inviata una richiesta POST al back-end di Azure distribuito in precedenza. Il back-end inserisce i dati della richiesta nella tabella SQL TodoItem e restituisce le informazioni sugli elementi appena archiviati all'app per dispositivi mobili. L'app per dispositivi mobili consente di visualizzare questi dati nell'elenco.

   ![Guida introduttiva all'app che esegue iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
