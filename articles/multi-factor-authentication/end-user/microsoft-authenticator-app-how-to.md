---
title: App Microsoft Authenticator per telefoni cellulari | Documentazione Microsoft
description: "Informazioni su come effettuare l'aggiornamento alla versione più recente di Azure Authenticator."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: cae11f53df768daf5846ac719a45715709326d76
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Introduzione all'app Microsoft Authenticator
L'app Microsoft Authenticator offre un livello di sicurezza aggiuntivo in un account aziendale o dell'istituto di istruzione, ad esempio bsimon@contoso.com, o un account Microsoft, ad esempio bsimon@outlook.com.

L'app può essere usata in uno dei due modi seguenti:

* **Notifica**. L'app consente di impedire l'accesso non autorizzato agli account e arrestare le transazioni illecite effettuando il push di una notifica allo smartphone o al tablet dell'utente. È sufficiente visualizzare la notifica e, se legittima, selezionare **Verifica**. In caso contrario, è possibile selezionare **Nega**.
* **Codice di verifica**. L'app può essere usata come token software per generare un codice di verifica OAuth. Dopo aver inserito il nome utente e la password, si immette il codice fornito dall'app nella schermata di accesso. Il codice di verifica offre una seconda forma di autenticazione.

L'app Microsoft Authenticator sostituisce l'app Azure Authenticator. L'app Azure Authenticator continua a funzionare, ma questo articolo offre informazioni utili nel caso in cui si decida di passare alla nuova app Microsoft Authenticator.  

## <a name="opt-in-for-two-step-verification"></a>Fornire il consenso esplicito per la verifica in due passaggi

L'app Microsoft Authenticator non funziona in modo autonomo. Configurare ogni account in modo da richiedere un secondo metodo di verifica dopo l'accesso con il nome utente e la password.

Per un account aziendale o dell'istituto di istruzione, non è possibile scegliere questa funzionalità in autonomia, ma sarà necessario che un amministratore della sicurezza fornisca il consenso esplicito per conto dell'utente e lo inviti a registrare i metodi di verifica per il proprio account. Per altre informazioni relative a questo scenario, vedere [Quali sono i vantaggi di Azure Multi-Factor Authentication?](multi-factor-authentication-end-user.md).

Per un account personale, è necessario configurare manualmente la verifica in due passaggi. Per un account Microsoft, questi passaggi sono disponibili in [Informazioni sulla verifica in due passaggi](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

È possibile usare l'app Microsoft Authenticator anche con account non Microsoft. La funzionalità potrebbe avere un nome diverso, ma dovrebbe essere facile da individuare tra le impostazioni di sicurezza o di accesso.

## <a name="install-the-app"></a>Installare l'app
L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Aggiungere account all'app
Per ogni account che si vuole aggiungere all'app Microsoft Authenticator, seguire una delle procedure seguenti:

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Aggiungere un account Microsoft personale all'app

Per un account Microsoft personale (che consente di accedere ad Outlook.com, Xbox, Skype e così via), è sufficiente accedere al proprio account nell'app Microsoft Authenticator.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Aggiungere un account aziendale o dell'istituto di istruzione all'app usando il lettore di codici QR
1. Accedere alla schermata delle impostazioni di verifica della sicurezza.  Per informazioni su come accedere a questa schermata, vedere la sezione relativa alla [modifica delle impostazioni di sicurezza](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Selezionare la casella accanto ad **App Authenticator** quindi selezionare **Configura**.

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

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Aggiungere un account all'app tramite le funzionalità di riconoscimento dell'impronta digitale o di riconoscimento facciale del dispositivo
Per completare la verifica, l'organizzazione può richiedere un PIN. L'app Microsoft Authenticator è in grado di usare le funzionalità di riconoscimento dell'impronta digitale o di riconoscimento facciale del dispositivo al posto del PIN. Per configurare questa impostazione alla prima verifica nell'app, si noti l'opzione per l'uso di Touch ID (per iOS) o dell'identificazione dell'impronta digitale. 

Per impostare il Touch ID per Microsoft Authenticator, è necessario completare una richiesta di verifica normale con un PIN. Microsoft Authenticator imposterà automaticamente il Touch ID per i dispositivi che lo supportano. 

![Verifica della configurazione di Touch ID](./media/authenticator-app-how-to/touchid1.png)

Successivamente, ogni volta che verrà richiesto di verificare le informazioni di accesso sarà sufficiente selezionare la notifica push ricevuta ed effettuare la scansione della propria impronta digitale anziché immettere il PIN.

![Notifica push](./media/authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>Usare l'app durante l'accesso

Dopo che l'account è stato aggiunto all'app, potrebbe essere chiesto di eseguire una verifica di prova per assicurarsi che la configurazione sia corretta. Dopo questa verifica, la procedura è terminata. Non è necessario fare altro fino al successivo accesso.

Se si sceglie di usare codici di verifica nell'app, vengono prima visualizzati nella home page. Questi codici cambiano ogni 30 secondi in modo da riceverne sempre uno nuovo ogni volta che è necessario. Tuttavia, non è necessario fare altro finché non si accede e non viene chiesto di immettere un codice di verifica.  
