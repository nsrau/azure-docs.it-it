---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 852a3b00e8513f9ce68c5aae54c974505d0c9af6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180645"
---
1. In Mac avviare **Keychain Access**. Aprire **My Certificates** (Certificati personali) in **Categoria** nella barra di spostamento sinistra. Trovare il certificato SSL scaricato nella sezione precedente e quindi divulgarne il contenuto. Selezionare solo il certificato (non la chiave privata), quindi [esportarlo](https://support.apple.com/kb/PH20122?locale=en_US).
2. Nel [portale di Azure](https://portal.azure.com/) selezionare **Esplora tutto** > **Servizi app**. Selezionare quindi il back-end di App per dispositivi mobili. 
3. In **Impostazioni** selezionare **App Service Push** (Push servizio app). Selezionare quindi il nome dell'hub di notifica. 
4. Andare a **Apple Push Notification Services** > **Carica certificato**. Caricare il file con estensione p12, selezionando la **modalità** corretta, a seconda che il certificato SSL client di prima sia di produzione o sandbox. Salvare le modifiche.

Il servizio è ora configurato per l'uso con le notifiche push in iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
