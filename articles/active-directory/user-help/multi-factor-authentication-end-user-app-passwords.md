---
title: Come gestire le password delle app - Azure Active Directory Documenti Microsoft
description: Scopri le password delle app e a cosa servono per quanto riguarda la verifica in due passaggi.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 5f81181ac3107307a352cdbcd0b5cc4a555deacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253221"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Gestire le password per le app per la verifica in due passaggi

>[!Important]
>L'amministratore potrebbe non consentire di usare le password delle app. Se l'opzione **Password dell'app** non è visibile, non è disponibile nell'organizzazione.

Quando si usano password per le app, è importante tenere presente quanto segue:

- Le password delle app vengono generate automaticamente e devono essere create e immesse una sola volta per ogni app.

- Il limite è di 40 password per utente. Se si prova a crearne un'altra oltre questo limite, verrà chiesto di eliminare una password esistente prima di poter creare quella nuova.

    >[!Note]
    >I client di Office 2013, tra cui Outlook, supportano i nuovi protocolli di autenticazione e possono essere usati con la verifica in due passaggi. Questo supporto significa che dopo l'attivazione della verifica in due passaggi non sono più necessarie le password per le app per i client Office 2013. Per altre informazioni, vedere l'articolo [Funzionamento dell'autenticazione moderna per le applicazioni client di Office 2013 e Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="create-new-app-passwords"></a>Creare nuove password per l'app

Durante il processo iniziale di registrazione della verifica a due fattori, viene fornita una singola password per l'app. Se ne hai bisogno di più di uno, dovrai crearlo tu stesso. Puoi creare password per le app da più aree, a seconda di come viene impostata la verifica a due fattori nella tua organizzazione. Per ulteriori informazioni sulla registrazione per utilizzare la verifica a due fattori con l'account aziendale o dell'istituto di istruzione, vedere [Panoramica della verifica a due fattori, dell'account aziendale o dell'istituto](multi-factor-authentication-end-user-first-time.md) di istruzione e degli articoli correlati.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Dove creare ed eliminare password per le app

Puoi creare ed eliminare le password delle app, in base al modo in cui usi la verifica a due fattori:

- **L'organizzazione utilizza la verifica a due fattori e la pagina Verifica di sicurezza aggiuntiva.** Se usi il tuo account aziendale o alain@contoso.comdell'istituto di istruzione (ad esempio, ) con la verifica a due fattori nell'organizzazione, puoi gestire le password dell'app dalla pagina Verifica di [sicurezza aggiuntiva.](https://account.activedirectory.windowsazure.com/Proofup.aspx) Per istruzioni dettagliate, vedere [Creare ed eliminare password dell'app usando la pagina Verifica](#create-and-delete-app-passwords-from-the-additional-security-verification-page) di sicurezza aggiuntiva in questo articolo.

- **L'organizzazione usa la verifica a due fattori e il portale di Office 365.** Se si usa l'account aziendale o alain@contoso.comdell'istituto di istruzione (ad esempio, ), la verifica a due fattori e le app di Office 365 nell'organizzazione, è possibile gestire le password dell'app dalla pagina del portale di [Office 365.](https://www.office.com) Per istruzioni dettagliate, vedere Creare ed eliminare password per le app tramite il portale di [Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) in questo articolo.

- **Si utilizza la verifica a due fattori con un account Microsoft personale.** Se si usa un account Microsoft personale alain@outlook.com(ad esempio, ) con la verifica a due fattori, è possibile gestire le password dell'app dalla [pagina Nozioni di base sulla sicurezza.](https://account.microsoft.com/security/) Per istruzioni dettagliate, vedere [Utilizzo delle password delle app con app che non supportano](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification)la verifica in due passaggi.

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Creare ed eliminare le password dell'app dalla pagina Verifica di sicurezza aggiuntiva

Puoi creare ed eliminare le password dell'app dalla pagina **Verifica di sicurezza aggiuntiva** per il tuo account aziendale o dell'istituto di istruzione.

1. Accedere alla [pagina Verifica di sicurezza aggiuntiva](https://account.activedirectory.windowsazure.com/Proofup.aspx)e quindi selezionare **Password dell'app**.

    ![Pagina Password dell'app, con la scheda Password app evidenziata](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Selezionare **Crea**, digitare il nome dell'app che richiede la password dell'app e quindi selezionare **Avanti**.

    ![Pagina Crea password app, con il nome dell'app che ha bisogno di password](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Copiare la password dalla pagina **Password dell'app** e quindi selezionare **Chiudi**.

    ![Pagina della password dell'app con la password per l'app specificata](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Nella pagina **Password app** verificare che l'app sia presente nell'elenco.

     ![Pagina Password dell'app, con la nuova app visualizzata nell'elenco](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Aprire l'app per cui è stata creata la password dell'app, ad esempio Outlook 2010, quindi incollare la password dell'app quando richiesto. Dovresti farlo solo una volta per app.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Per eliminare una password dell'app tramite la pagina Password app

1. Nella pagina **Password app** selezionare **Elimina** accanto alla password dell'app che si desidera eliminare.

   ![Eliminare una password di app](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selezionare **Sì** per confermare l'eliminazione della password e quindi **Chiudi**.

    La password viene eliminata.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Creare ed eliminare password per le app tramite il portale di Office 365

Se si usa la verifica in due passaggi con l'account aziendale o dell'istituto di istruzione e le app di Office 365, è possibile creare ed eliminare le password per le app tramite il portale di Office 365.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Per creare password per le app tramite il portale di Office 365

1. Accedere a Office 365 e quindi passare alla [pagina Account personale](https://portal.office.com), selezionare Sicurezza & **privacy**e quindi espandere Verifica di **sicurezza aggiuntiva**.

    ![Portale di Office che mostra l'area Verifica aggiuntiva di sicurezza espansa](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Seleziona il testo **Crea e gestisci le password dell'app** per aprire la pagina **Password app.**

    ![Pagina Password dell'app, con la scheda Password app evidenziata](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. Selezionare **Crea**, digitare il nome dell'app che richiede la password dell'app e quindi selezionare **Avanti**.

    ![Pagina Crea password app, con il nome dell'app che ha bisogno di password](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Copiare la password dalla pagina **Password dell'app** e quindi selezionare **Chiudi**.

    ![Pagina della password dell'app con la password per l'app specificata](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. Nella pagina **Password app** verificare che l'app sia presente nell'elenco.

     ![Pagina Password dell'app, con la nuova app visualizzata nell'elenco](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Aprire l'app per cui è stata creata la password dell'app, ad esempio Outlook 2010, quindi incollare la password dell'app quando richiesto. Dovresti farlo solo una volta per app.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Per eliminare le password delle app tramite la pagina Password app

1. Nella pagina **Password app** selezionare **Elimina** accanto alla password dell'app che si desidera eliminare.

   ![Eliminare una password di app](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selezionare **Sì** nella casella di conferma e quindi **Chiudi**.

    La password viene eliminata.

## <a name="if-your-app-passwords-arent-working-properly"></a>Se le app per le password non funzionano correttamente

Assicurarsi di aver digitato correttamente la password. Se si è certi di aver immesso la password correttamente, è possibile provare ad accedere di nuovo e creare una nuova password. Se nessuna di queste opzioni consente di risolvere il problema, contattare l'help desk dell'organizzazione in modo che possa eliminare le password dell'app esistenti, consentendo di crearne di nuove di zecca.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le impostazioni della verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md)

- Provare l'[app Microsoft Authenticator](user-help-auth-app-download-install.md) per verificare gli accessi con le notifiche delle app, invece di ricevere messaggi o chiamate.
