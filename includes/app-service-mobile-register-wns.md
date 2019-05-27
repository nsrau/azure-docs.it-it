---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 75bcb9d27ee6f66a1d9c15093d9f933a3ad25881
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140589"
---
1. In Esplora soluzioni di Visual Studio, fare clic con il pulsante destro del mouse sul progetto di app di Windows Store e quindi scegliere **Store** > **Associa applicazione a Store**.

    ![Associa l’app con Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. Nella procedura guidata selezionare **Avanti**. Accedere quindi con il proprio account Microsoft. In **Riserva un nuovo nome dell'app** digitare un nome per l'app e quindi selezionare **Riserva**.
3. Al termine della creazione della registrazione dell'app, selezionare il nuovo nome dell'app. Selezionare **Avanti** e quindi **Associa**. Questo processo aggiunge le informazioni di registrazione a Windows Store necessarie al manifesto dell'applicazione.
4. Ripetere i passaggi 1 e 3 per il progetto app di Windows Phone Store usando la stessa registrazione creata in precedenza per l'app di Windows Store.  
5. Passare a [Windows Dev Center](https://dev.windows.com/en-us/overview) e quindi accedere con il proprio account Microsoft. In **Le mie app** selezionare la nuova registrazione di app. Espandere quindi **Servizi** > **Notifiche push**.
6. Nella pagina **Notifiche push** selezionare il **sito dei servizi Live** in **Windows Push Notification Services (WNS) and Microsoft Azure Mobile Apps** (Servizi notifica Push Windows (WNS) e app per dispositivi mobili di Microsoft Azure).  Prendere nota dei valori del **SID pacchetto** e del valore *attuale* in **Segreto dell'applicazione**. 

    ![Impostazione dell’app nel centro per sviluppatori](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Il segreto applicazione e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri utenti né distribuirli con l'app.
   >
   >
