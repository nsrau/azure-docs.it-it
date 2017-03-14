---
title: App Microsoft Authenticator per telefoni cellulari | Documentazione Microsoft
description: "Informazioni su come effettuare l&quot;aggiornamento alla versione più recente di Azure Authenticator."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/25/2016
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6872b216ab2bfd5823b65b1bb70d503c7628ee13
ms.openlocfilehash: 26d9f04f9d3019ab5fcad48ef1e9821a4dd6a724
ms.lasthandoff: 03/01/2017

---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Introduzione all'app Microsoft Authenticator
L'app Microsoft Authenticator offre un livello di sicurezza aggiuntivo in un account Azure (ad esempio, bsimon@contoso.onmicrosoft.com), un account aziendale locale (ad esempio, bsimon@contoso.com) o un account Microsoft (ad esempio, bsimon@outlook.com).

L'app può essere usata in uno dei due modi seguenti:

* **Notifica**. L'app consente di impedire l'accesso non autorizzato agli account e arrestare le transazioni illecite effettuando il push di una notifica allo smartphone o al tablet dell'utente. È sufficiente visualizzare la notifica e, se legittima, selezionare **Verifica**. In caso contrario, è possibile selezionare **Nega**. Per informazioni su come negare le notifiche, vedere l'argomento relativo a come usare le funzionalità Nega e segnala illecito per Multi-Factor Authentication.
* **Password con codice di verifica**. L'app può essere usata come token software per generare un codice di verifica OAuth. Dopo aver inserito il nome utente e la password, si immette il codice fornito dall'app nella schermata di accesso. Il codice di verifica offre una seconda forma di autenticazione.

L'app Microsoft Authenticator sostituisce l'app Azure Authenticator.  L'app Azure Authenticator continuerà a funzionare, ma questo articolo offre informazioni utili nel caso in cui si decida di passare alla nuova app Microsoft Authenticator.  

## <a name="install-the-app"></a>Installare l'app
L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Aggiungere account all'app
Per ogni account che si vuole aggiungere all'app Microsoft Authenticator, seguire una delle procedure seguenti.

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Aggiungere un account Microsoft personale all'app

Per un account Microsoft personale (che consente di accedere ad Outlook.com, Xbox, Skype e così via), è sufficiente accedere al proprio account nell'app Microsoft Authenticator.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Aggiungere un account aziendale o dell'istituto di istruzione all'app usando il lettore di codici QR
1. Accedere alla schermata delle impostazioni di verifica della sicurezza.  Per informazioni su come accedere a questa schermata, vedere la sezione relativa alla [modifica delle impostazioni di sicurezza](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Selezionare la casella accanto ad **App Azure Authenticator** quindi selezionare **Configura**.

    ![Pulsante Configura nella schermata delle impostazioni di verifica della sicurezza](./media/authenticator-app-how-to/azureauthe.png)

    Verrà visualizzata una schermata contenente un codice a matrice.

    ![Schermata contenente il codice a matrice](./media/authenticator-app-how-to/barcode2.png)
3. Aprire l'app Microsoft Authenticator. Nella schermata **account** selezionare **+** e quindi specificare che si vuole aggiungere un account aziendale o dell'istituto di istruzione.
4. Usare la fotocamera per effettuare la scansione del codice a matrice e quindi selezionare **Operazione completata** per chiudere la relativa schermata.

    Se la fotocamera non funziona correttamente è possibile [immettere manualmente il codice a matrice e l'URL](#add-an-account-to-the-app-manually).

5. Quando l'app mostra il nome dell'account con un codice di sei cifre riportato sotto il nome, la procedura è terminata. 

    ![Schermata Account](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Aggiungere manualmente un account all'app
1. Accedere alla schermata delle impostazioni di verifica della sicurezza.  Per informazioni su come accedere a questa schermata, vedere la sezione relativa alla [modifica delle impostazioni di sicurezza](multi-factor-authentication-end-user-manage-settings.md).
2. Selezionare **Configura**.

    ![Pulsante Configura nella schermata delle impostazioni di verifica della sicurezza](./media/authenticator-app-how-to/azureauthe.png)

    Verrà visualizzata una schermata contenente un codice a matrice.  Prendere nota del codice e dell'URL.

    ![Schermata contenente il codice a matrice e l'URL](./media/authenticator-app-how-to/barcode2.png)
3. Aprire l'app Microsoft Authenticator. Nella schermata **account** selezionare **+** e quindi specificare che si vuole aggiungere un account aziendale o dell'istituto di istruzione.

4. Nello scanner selezionare **In alternativa, immettere il codice manualmente**.

    ![Schermata per la scansione di un codice a matrice](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. Immettere il codice e l'URL nelle caselle appropriate nell'app, quindi selezionare **Fine**.

    ![Schermata per l'immissione del codice e dell'URL](./media/authenticator-app-how-to/manual.png)

6. Quando l'app mostra il nome dell'account con un codice di sei cifre riportato sotto il nome, la procedura è terminata.

    ![Schermata Account](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-touch-id"></a>Aggiungere un account all'app mediante Touch ID
In iOS, l'app Microsoft Authenticator supporta Touch ID.  Azure Multi-Factor Authentication consente alle organizzazioni di richiedere un PIN per i dispositivi. Con Touch ID, non è necessario che gli utenti iOS immettano un PIN. Possono invece effettuare la scansione della propria impronta digitale e selezionare **Approva**.

Configurare Touch ID con Microsoft Authenticator è semplice. Si completa una normale richiesta di verifica con un PIN. Se il dispositivo supporta Touch ID, verrà automaticamente configurato da Microsoft Authenticator per l'account.

![Verifica della configurazione di Touch ID](./media/authenticator-app-how-to/touchid1.png)

Successivamente, ogni volta che verrà richiesto di verificare le informazioni di accesso sarà sufficiente selezionare la notifica push ricevuta ed effettuare la scansione della propria impronta digitale anziché immettere il PIN.

![Notifica push](./media/authenticator-app-how-to/touchid2.png)


