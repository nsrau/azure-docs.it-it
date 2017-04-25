---
title: 'Azure AD: reimpostare la password | Microsoft Docs'
description: Usare la reimpostazione password self-service per riottenere l&quot;accesso al proprio account
services: active-directory
keywords: gestione delle password in Active Directory, gestione delle password, reimpostazione password self-service di Azure AD, SSPR
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: bb62d6973dff00932e72ddb4b2c344f070537828
ms.lasthandoff: 04/14/2017


---
# <a name="help-i-forgot-my-password"></a>Password dimenticata

Nessun problema

Se gli scenari seguenti si applicano al caso specifico, è disponibile una soluzione

* Non si è certi di come accedere all'account e non si riesce a ricordare la password
* Non è stata assegnata una password e l'amministratore ha indirizzato l'utente a questa pagina

## <a name="unlock-my-account"></a>Sbloccare l'account personale

Se l'obiettivo è sbloccare il proprio account, seguire questa procedura. Quando nel passaggio 6 riportato di seguito viene visualizzata la pagina **scegliere una nuova password**, è possibile sbloccare l'account o cambiare la password e ottenere lo sblocco.

## <a name="reset-my-password"></a>Reimpostare la password

Per accedere nuovamente all'account, seguire questa procedura.
1. In qualsiasi pagina di accesso aziendale o dell'istituto di istruzione fare clic sul collegamento **Problemi di accesso all'account?** e quindi su **Account aziendale o dell'istituto di istruzione** oppure andare direttamente alla [pagina per la reimpostazione della password](https://passwordreset.microsoftonline.com/).

    ![Problemi di accesso all'account?][Login]

2. Immettere l'**ID utente** aziendale o dell'istituto di istruzione, dimostrare di non essere un robot rispondendo al test CAPTCHA e immettendo il testo visualizzato e quindi fare clic su **Avanti**.

   > [!NOTE]
   > Se l'amministratore non ha abilitato questa funzionalità, a questo punto viene visualizzato un collegamento "Contattare l'amministratore" che consente di ricevere assistenza dall'amministratore per posta elettronica o tramite un portale Web.
   >

3. A seconda della configurazione definita dall'amministratore, verranno visualizzate una o più delle opzioni seguenti.
    * **Invia messaggio di posta elettronica all'indirizzo di posta elettronica alternativo dell'utente**: invia un messaggio di posta elettronica con un codice di 6 cifre all'indirizzo di posta elettronica alternativo o all'indirizzo di posta elettronica per l'autenticazione, in base all'opzione scelta dall'utente.
    * **Invia SMS sul telefono cellulare**: invia un SMS con un codice di 6 cifre al cellulare dell'utente o al telefono per l'autenticazione, in base all'opzione scelta dall'utente.
    * **Chiama telefono cellulare**: chiama il cellulare dell'utente o il telefono per l'autenticazione, in base all'opzione scelta dall'utente. Premere il tasto # per verificare la chiamata.
    * **Chiama telefono ufficio**: chiama il telefono dell'ufficio dell'utente. Premere il tasto # per verificare la chiamata.
    * **Rispondi alle domande di sicurezza**: visualizza le domande di sicurezza registrate in precedenza in modo che l'utente possa rispondere.
4. Inserire le risposte alle domande del test nei campi obbligatori e fare clic su **Avanti**.

    ![Verificare i dati di autenticazione][Verification]

5. L'amministratore può richiedere un passaggio di verifica aggiuntivo. In tal caso, è necessario ripetere il passaggio 4 con un'opzione diversa.
6. Nella pagina **scegliere una nuova password** immettere una nuova password che soddisfi i requisiti dell'organizzazione, confermarla e quindi fare clic su **Fine**.

    ![Cambiare la password][Change]

7. Quando viene visualizzato il messaggio **La password è stata reimpostata**, è possibile accedere con la nuova password.

    ![La password è stata reimpostata][Complete]

Dopo aver usato questo metodo per lo sblocco o la reimpostazione della password, si potrebbe ricevere un messaggio di posta elettronica che conferma il completamento del processo da un account come "Microsoft per conto di Organizzazione". Se si riceve un messaggio di posta elettronica di questo tipo pur non avendo usato la reimpostazione password self-service per riottenere l'accesso al proprio account, contattare l'amministratore.

## <a name="change-my-password"></a>Cambiare la password

Se si conosce già la password e si vuole cambiarla, provare a seguire queste procedure.

### <a name="change-your-password-from-the-office-365-portal"></a>Cambiare la password dal portale di Microsoft Office 365

1. Fare clic sul profilo in alto a destra e quindi su **Visualizza account**
2. **Sicurezza e privacy**
3. **Password**
4. Immettere la password precedente e quindi impostare e confermare la nuova
5. **Invia**

### <a name="change-your-password-from-the-azure-access-panel"></a>Cambiare la password dal pannello di accesso di Azure

1. Accedere al [portale di accesso di Azure](https://myapps.microsoft.com/) usando la password esistente
2. Fare clic sul profilo in alto a destra e quindi su **Profilo**
3. **Cambia password**
4. Immettere la password precedente e quindi impostare e confermare la nuova
5. **Invia**

## <a name="next-steps"></a>Passaggi successivi

* [Come eseguire la registrazione per usare la reimpostazione password self-service](active-directory-passwords-reset-register.md)
* [Pagina di registrazione per la reimpostazione della password](http://aka.ms/ssprsetup)
* [Portale di reimpostazione della password](https://passwordreset.microsoftonline.com/)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Collegamento "Problemi di accesso all'account?" nella pagina di accesso"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Verificare i dati di autenticazione"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Cambiare la password"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "La password è stata reimpostata"

