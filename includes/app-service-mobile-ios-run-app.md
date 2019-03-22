---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: f4ba467b6d80c9ccafba0a91c1f04152b92cf869
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58115183"
---
1. In un computer Mac visitare il [portale di Azure]. Fare clic su **Tutti i servizi** > **Servizi app** e quindi selezionare il back-end che è stato creato. Nelle impostazioni dell'app per dispositivi mobili scegliere il linguaggio preferito:

   - Objective-C &ndash; **Guida introduttiva** > **iOS (Objective-C)**
   - Swift &ndash; **Guida introduttiva** > **iOS (Swift)**

     In **3. Configurare l'applicazione client** fare clic su **Scarica**. In questo modo viene scaricato un progetto Xcode preconfigurato completo da connettere al back-end. Aprire il progetto con Xcode.

1. Fare clic su **Esegui** per creare il progetto e avviare l'app nel simulatore iOS.

1. Nell'app fare clic sull'icona del segno più (**+**), digitare un testo significativo, ad esempio *Complete the tutorial*, quindi fare clic sul pulsante Salva. Verrà inviata una richiesta POST al back-end di Azure distribuito in precedenza. Il back-end inserisce i dati della richiesta nella tabella SQL TodoItem e restituisce le informazioni sugli elementi appena archiviati all'app per dispositivi mobili. L'app per dispositivi mobili consente di visualizzare questi dati nell'elenco.

   ![Guida introduttiva all'app che esegue iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Portale di Azure]: https://portal.azure.com/
