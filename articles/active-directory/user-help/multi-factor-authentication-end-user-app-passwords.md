---
title: Come gestire le password dell'app-Azure Active Directory | Microsoft Docs
description: Informazioni sulle password delle app e su come vengono usate per la verifica in due passaggi.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: lizross
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc6f6f7b02db664ca6cd62dc7aad61baf5f132fa
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088414"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Gestire le password per le app per la verifica in due passaggi

>[!Important]
>L'amministratore potrebbe non consentire di usare le password delle app. Se l'opzione **Password dell'app** non è visibile, non è disponibile nell'organizzazione.

Quando si usano password per le app, è importante tenere presente quanto segue:

- Le password per le app vengono generate automaticamente e immesse una sola volta per ogni app.

- Il limite è di 40 password per utente. Se si prova a crearne un'altra oltre questo limite, verrà chiesto di eliminare una password esistente prima di poter creare quella nuova.

- Usare una sola password per dispositivo, non per app. Ad esempio, creare una singola password per tutte le app del portatile e un'altra per tutte le app sul PC desktop.

    >[!Note]
    >I client di Office 2013, tra cui Outlook, supportano i nuovi protocolli di autenticazione e possono essere usati con la verifica in due passaggi. Questo supporto significa che dopo l'attivazione della verifica in due passaggi non sono più necessarie le password per le app per i client Office 2013. Per altre informazioni, vedere l'articolo [Funzionamento dell'autenticazione moderna per le applicazioni client di Office 2013 e Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="create-new-app-passwords"></a>Creare nuove password per l'app

Durante il processo iniziale di registrazione della verifica a due fattori, viene fornita una sola password dell'app. Se è necessario più di uno, sarà necessario crearlo autonomamente. È possibile creare password di app da più aree, a seconda della configurazione della verifica a due fattori nell'organizzazione. Per altre informazioni sulla registrazione per usare la verifica a due fattori con l'account aziendale o dell'Istituto di istruzione, vedere [Panoramica per la verifica a due fattori e l'account aziendale o dell'Istituto di istruzione](multi-factor-authentication-end-user-first-time.md) e gli articoli correlati.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Dove creare ed eliminare password per le app

È possibile creare ed eliminare le password dell'app in base all'uso della verifica a due fattori:

- **L'organizzazione usa la verifica a due fattori e la pagina verifica aggiuntiva di sicurezza.** Se si usa l'account aziendale o dell'Istituto di istruzione (ad alain@contoso.comesempio,) con la verifica a due fattori all'interno dell'organizzazione, è possibile gestire le password dell'app dalla [pagina di verifica aggiuntiva di sicurezza](https://account.activedirectory.windowsazure.com/Proofup.aspx). Per istruzioni dettagliate, vedere [creare ed eliminare password di app usando la pagina verifica aggiuntiva di sicurezza](#create-and-delete-app-passwords-from-the-additional-security-verification-page) in questo articolo.

- **L'organizzazione usa la verifica a due fattori e il portale di Office 365.** Se si usa l'account aziendale o dell'Istituto di istruzione (ad alain@contoso.comesempio,), la verifica a due fattori e le app di Office 365 nell'organizzazione, è possibile gestire le password dell'app dalla [pagina del portale di Office 365](https://www.office.com). Per istruzioni dettagliate, vedere [creare ed eliminare password di app usando il portale di Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) in questo articolo.

- **Si sta usando la verifica a due fattori con un account Microsoft personale.** Se si usa un account Microsoft personale (ad esempio, alain@outlook.com) con la verifica a due fattori, è possibile gestire le password dell'app dalla pagina delle nozioni di base sulla [sicurezza](https://account.microsoft.com/security/). Per istruzioni dettagliate, vedere [uso di password di app con app che non supportano la verifica in due passaggi](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification).

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Creare ed eliminare le password dell'app dalla pagina verifica aggiuntiva di sicurezza

È possibile creare ed eliminare le password dell'app dalla pagina di **verifica aggiuntiva di sicurezza** per l'account aziendale o dell'Istituto di istruzione.

1. Accedere alla [pagina verifica aggiuntiva di sicurezza](https://account.activedirectory.windowsazure.com/Proofup.aspx)e quindi selezionare **password app**.

    ![Pagina password app, con la scheda Password app evidenziata](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Selezionare **Crea**, digitare il nome dell'app che richiede la password dell'app e quindi fare clic su **Avanti**.

    ![Pagina creare password dell'app, con il nome dell'app che richiede la password](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Copiare la password dalla pagina della **password dell'app** e quindi fare clic su **Chiudi**.

    ![Pagina della password dell'app con la password per l'app specificata](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Nella pagina **password dell'app** verificare che l'app sia elencata.

     ![Pagina password app, con nuova app visualizzata nell'elenco](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Aprire l'app per cui è stata creata la password dell'app, ad esempio Outlook 2010, quindi incollare la password dell'app quando viene richiesta. Questa operazione deve essere eseguita una sola volta per ogni app.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Per eliminare una password dell'app usando la pagina password dell'app

1. Nella pagina **password app** selezionare **Elimina** accanto alla password dell'app che si vuole eliminare.

   ![Eliminare una password di app](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selezionare **Sì** per confermare l'eliminazione della password e quindi **Chiudi**.

    La password viene eliminata.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Creare ed eliminare password per le app tramite il portale di Office 365

Se si usa la verifica in due passaggi con l'account aziendale o dell'istituto di istruzione e le app di Office 365, è possibile creare ed eliminare le password per le app tramite il portale di Office 365.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Per creare password per le app tramite il portale di Office 365

1. Accedere a Office 365, quindi andare alla [pagina account personale](https://portal.office.com), selezionare **sicurezza & privacy**, quindi espandere **verifica aggiuntiva di sicurezza**.

    ![Portale di Office che mostra l'area Verifica aggiuntiva di sicurezza espansa](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Selezionare il testo che indica come **creare e gestire le password dell'app** per aprire la pagina **password dell'app** .

    ![Pagina password app, con la scheda Password app evidenziata](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. Selezionare **Crea**, digitare il nome dell'app che richiede la password dell'app e quindi fare clic su **Avanti**.

    ![Pagina creare password dell'app, con il nome dell'app che richiede la password](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Copiare la password dalla pagina della **password dell'app** e quindi fare clic su **Chiudi**.

    ![Pagina della password dell'app con la password per l'app specificata](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. Nella pagina **password dell'app** verificare che l'app sia elencata.

     ![Pagina password app, con nuova app visualizzata nell'elenco](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Aprire l'app per cui è stata creata la password dell'app, ad esempio Outlook 2010, quindi incollare la password dell'app quando viene richiesta. Questa operazione deve essere eseguita una sola volta per ogni app.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Per eliminare le password dell'app usando la pagina password dell'app

1. Nella pagina **password app** selezionare **Elimina** accanto alla password dell'app che si vuole eliminare.

   ![Eliminare una password di app](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selezionare **Sì** nella casella di conferma e quindi fare clic su **Chiudi**.

    La password viene eliminata.

## <a name="if-your-app-passwords-arent-working-properly"></a>Se le app per le password non funzionano correttamente

Assicurarsi di aver digitato correttamente la password. Se si è certi di aver immesso la password correttamente, è possibile provare ad accedere di nuovo e creare una nuova password. Se nessuna di queste opzioni consente di risolvere il problema, contattare il supporto tecnico dell'organizzazione per eliminare le password dell'app esistenti, consentendo di crearne di nuove.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le impostazioni della verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md)

- Provare l'[app Microsoft Authenticator](user-help-auth-app-download-install.md) per verificare gli accessi con le notifiche delle app, invece di ricevere messaggi o chiamate.
