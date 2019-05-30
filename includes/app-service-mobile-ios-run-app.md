---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240257"
---
1. Aprire il progetto client scaricato con Xcode.

2. Andare alla [portale di Azure](https://portal.azure.com/) e passare all'app per dispositivi mobili che è stato creato. Nel `Overview` pannello, cercare l'URL a cui l'endpoint pubblico per l'app per dispositivi mobili. Esempio: il nome di sito per il nome di app "test123" saranno https://test123.azurewebsites.net.

3. Per il progetto Swift, aprire il file `ToDoTableViewController.swift` in questa cartella - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. Il nome dell'applicazione è `ZUMOAPPNAME`.

4. Nelle `viewDidLoad()` metodo, sostituire `ZUMOAPPURL` parametro con endpoint pubblici precedente.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    diventa
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Per il progetto di Objective-C, aprire il file `QSTodoService.m` in questa cartella - ZUMOAPPNAME/ZUMOAPPNAME. Il nome dell'applicazione è `ZUMOAPPNAME`.

6. Nelle `init` metodo, sostituire `ZUMOAPPURL` parametro con endpoint pubblici precedente.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    diventa
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Fare clic su **Esegui** per creare il progetto e avviare l'app nel simulatore iOS.

8. Nell'app fare clic sull'icona del segno più ( **+** ), digitare un testo significativo, ad esempio *Complete the tutorial*, quindi fare clic sul pulsante Salva. Verrà inviata una richiesta POST al back-end di Azure distribuito in precedenza. Il back-end inserisce i dati della richiesta nella tabella SQL TodoItem e restituisce le informazioni sugli elementi appena archiviati all'app per dispositivi mobili. L'app per dispositivi mobili consente di visualizzare questi dati nell'elenco.

   ![Guida introduttiva all'app che esegue iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
