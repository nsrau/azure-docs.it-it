---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 1d1d593b7305e0cd9899f4ec388cb441ced90b10
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133666"
---
1. In un computer Mac visitare il [portale di Azure]. Fare clic su **Tutti i servizi** > **Servizi app** e quindi selezionare il back-end che è stato creato. Nelle impostazioni dell'app per dispositivi mobili scegliere il linguaggio preferito:

    - Objective-C &ndash; **Guida introduttiva** > **iOS (Objective-C)**
    - Swift &ndash; **Guida introduttiva** > **iOS (Swift)**

    In **3. Configurare l'applicazione client** fare clic su **Scarica**. In questo modo viene scaricato un progetto Xcode preconfigurato completo da connettere al back-end. Aprire il progetto con Xcode.

1. Fare clic su **Esegui** per creare il progetto e avviare l'app nel simulatore iOS.

1. Nell'app digitare un testo significativo, ad esempio *Complete the tutorial* , quindi fare clic sull'icona con il segno PIÙ (**+**). Verrà inviata una richiesta POST al back-end di Azure distribuito in precedenza. Il back-end inserisce i dati della richiesta nella tabella SQL TodoItem e restituisce le informazioni sugli elementi appena archiviati all'app per dispositivi mobili. L'app per dispositivi mobili consente di visualizzare questi dati nell'elenco.

   ![Guida introduttiva all'app che esegue iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Portale di Azure]: https://portal.azure.com/
