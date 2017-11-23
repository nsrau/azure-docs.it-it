---
title: 'Azure AD: reimpostare la password | Microsoft Docs'
description: Utilizzare la funzione di reimpostazione della password self-service per recuperare l'accesso al proprio account utente aziendale o dell'istituto di istruzione
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: gahug
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 9595b318b8bd974f778dc12b38cccd949a4e7a1a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="reset-your-work-or-school-password"></a>Reimpostare la password aziendale o scolastica

Se si dimentica la password, la si vuole modificare, non è mai stata inviata dal servizio assistenza dell'azienda oppure l'account è stato bloccato, procedere come segue. Se si conosce la password ed necessario soltanto modificarla, procedere fino alla sezione [Cambiare la password](#change-my-password) in basso.

   > [!NOTE]
   > Se si sta provando ad accedere nuovamente al proprio account personale come hotmail.com o outlook.com, provare i [suggerimenti disponibili in questo articolo](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Reimpostare o sbloccare la password per un account aziendale o dell'istituto di istruzione

Se è impossibile accedere al proprio account per una delle seguenti condizioni:

* La password non funziona e si desidera reimpostarla.
* Si conosce la password, ma l'account è bloccato e si desidera sbloccare l'account.

Eseguire i passaggi descritti di seguito per reimpostare la password self-service di Azure AD, procedura chiamata anche SSPR, e per poter effettuare nuovamente accesso all'account.

1. In qualsiasi pagina di accesso aziendale o dell'istituto di istruzione fare clic sul collegamento **Problemi di accesso all'account?** e quindi su **Account aziendale o dell'istituto di istruzione** oppure passare direttamente alla [pagina per la reimpostazione della password](https://passwordreset.microsoftonline.com/).

    ![Problemi di accesso all'account?][Login]

2. Immettere l'**ID utente** dell'account aziendale o dell'istituto di istruzione, dimostrare di non essere un robot immettendo i caratteri visualizzati e quindi fare clic su **Avanti**.

   > [!NOTE]
   > Se il personale IT non ha abilitato questa funzionalità, viene visualizzato un collegamento "Contattare l'amministratore" che consente di ricevere assistenza per posta elettronica o tramite un portale Web.
   >
   > Se è necessario sbloccare l'account, scegliere qui l'opzione "Ricordo la password, ma non riesco ad accedere".
   >

3. A seconda della configurazione definita dal personale IT, verranno visualizzati uno o più dei metodi di autenticazione seguenti per la reimpostazione della password self-service. Prima di usare l'articolo [Eseguire la registrazione per la reimpostazione password self-service](active-directory-passwords-reset-register.md), l'utente o il personale IT hanno specificato alcune di queste informazioni.

   * **Invia messaggio di posta elettronica all'indirizzo di posta elettronica alternativo dell'utente**
   * **Invia SMS sul telefono cellulare**
   * **Chiama telefono cellulare**
   * **Chiama telefono ufficio**
   * **Rispondi alle domande di sicurezza**

   Scegliere un'opzione, indicare le risposte corrette e fare clic su **Avanti**.

   ![Verificare i dati di autenticazione][Verification]

4. Il personale IT può richiedere un'altra verifica. In questo caso sarà necessario ripetere il passaggio 3 con un'opzione diversa.
5. Nella pagina **Scegliere una nuova password** immettere una nuova password e confermarla, quindi fare clic su **Fine**. La password aziendale o scolastica potrebbe avere requisiti specifici: è consigliabile che la password contenga una combinazione di 8-16 caratteri minuscoli, maiuscoli, numeri e caratteri speciali.
6. Quando viene visualizzato il messaggio **La password è stata reimpostata**, è possibile accedere con la nuova password.

    ![La password è stata reimpostata][Complete]

A questo punto dovrebbe essere possibile accedere all'account, in caso contrario contattare il personale IT dell'organizzazione per assistenza.

È possibile che si riceva un messaggio di posta elettronica di conferma proveniente da un account come "Microsoft per conto di \<organizzazione>". Se si riceve un messaggio di posta elettronica di questo tipo pur non avendo usato la reimpostazione password self-service per riottenere l'accesso all'account, contattare il personale IT.

## <a name="change-my-password"></a>Cambiare la password

Se si conosce già la password e si vuole cambiarla, seguire questa procedura.

### <a name="change-your-password-from-the-office-365-portal"></a>Cambiare la password dal portale di Microsoft Office 365

Usare questo metodo se in genere si accede alle applicazioni tramite il portale di Office

1. Accedere al proprio [account Office 365](https://www.office.com) con la password esistente
2. Fare clic sul profilo in alto a destra e quindi su **Visualizza account**
3. Fare clic su **Sicurezza e privacy** > **Password**
4. Immettere la password precedente, impostare e confermare la nuova password, quindi fare clic su **Invia**

### <a name="change-your-password-from-the-azure-access-panel"></a>Cambiare la password dal pannello di accesso di Azure

Usare questo metodo se in genere si accede alle applicazioni tramite il portale di accesso di Azure

1. Accedere al [portale di accesso di Azure](https://myapps.microsoft.com/) usando la password esistente
2. Fare clic sul profilo in alto a destra e quindi su **Profilo**
3. Fare clic su **Cambia password**
4. Immettere la password precedente, impostare e confermare la nuova password, quindi fare clic su **Invia**

## <a name="reset-password-at-login"></a>Reimpostazione della password all'accesso

Se l'amministratore ha abilitato la funzionalità, potrebbe essere disponibile il collegamento **Reimposta password** nella schermata di accesso di Windows 10 Fall Creators Update.

![Schermata di accesso][LoginScreen]

Fare clic sul collegamento **Reimposta password** per avviare la procedura di reimpostazione della password self-service nella schermata di accesso. Ciò consente di reimpostare la password senza dover ricorrere alla normale procedura di accesso basata sul Web.

1. Confermare l'ID utente e quindi fare clic su **Avanti**
2. Scegliere e confermare un metodo di contatto per la verifica. Il personale IT può richiedere un'altra verifica. In questo caso sarà necessario ripetere questo passaggio con un'opzione diversa.

   ![Metodo di contatto][ContactMethod]

3. Nella pagina **Creazione nuova password** immettere una nuova password e confermarla e quindi fare clic su **Avanti**. È consigliabile che la password contenga una combinazione di 8-16 caratteri minuscoli, maiuscoli, numeri e caratteri speciali.

   ![Reimpostazione della password][ResetPassword]

4. Quando viene visualizzato il messaggio **La password è stata reimpostata**, fare clic su **Fine**

A questo punto dovrebbe essere possibile accedere all'account, in caso contrario contattare il personale IT dell'organizzazione per assistenza.

## <a name="common-problems-and-their-solutions"></a>Problemi frequenti e relative soluzioni

 Di seguito sono riportati alcuni degli errori più comuni e le relative soluzioni:

| Scenario di errore| Tipo di errore visualizzato| Soluzione |
| --- | --- | --- |
| Dopo aver immesso l'ID utente viene visualizzata una pagina "Contattare l'amministratore" | Contattare l'amministratore <br> <br> È stato rilevato che la password dell'account utente non è gestita da Microsoft. Di conseguenza, non può essere reimpostata automaticamente. <br> <br> È necessario contattare il personale IT per ricevere assistenza aggiuntiva. | Questo messaggio viene visualizzato perché il personale IT gestisce la password nell'ambiente locale e non consente di reimpostarla dal collegamento "Problemi di accesso all'account?". <br> <br> Per reimpostare la password, contattare direttamente il personale IT per ricevere assistenza e informarne i membri che si intende reimpostare la password, in modo che abilitino questa funzionalità per l'utente.|
| Dopo aver immesso l'ID utente viene visualizzato il messaggio di errore "Account non abilitato per la reimpostazione della password"  | Account non abilitato per la reimpostazione della password <br> <br> Il personale IT non ha configurato l'account per l'uso di questo servizio. <br> <br> Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password. | Questo messaggio viene visualizzato perché il personale IT non ha abilitato la reimpostazione della password per l'organizzazione dal collegamento "Problemi di accesso all'account?" o non consente all'utente di usare la funzionalità. <br> <br> Per reimpostare la password, fare clic sul collegamento per contattare un amministratore e inviare un messaggio di posta elettronica al personale IT della società per richiedere che venga abilitata la funzionalità di reimpostazione della password per l'utente. |
| Dopo aver immesso l'ID utente viene visualizzato il messaggio di errore "Non è stato possibile verificare l'account". | Non è stato possibile verificare l'account <br> <br> Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password. | Questo messaggio viene visualizzato perché è abilitata la reimpostazione della password da parte dell'utente, ma non è stata eseguita la registrazione per usare il servizio. Per registrarsi per la reimpostazione della password, andare alla pagina http://aka.ms/ssprsetup dopo aver recuperato l'accesso al proprio account. <br> <br> Per reimpostare la password, fare clic sul collegamento per contattare un amministratore e inviare un messaggio di posta elettronica al personale della società. |

## <a name="next-steps"></a>Passaggi successivi

* [Come eseguire la registrazione per usare la reimpostazione password self-service](active-directory-passwords-reset-register.md)
* [Pagina di registrazione per la reimpostazione della password](http://aka.ms/ssprsetup)
* [Portale di reimpostazione della password](https://passwordreset.microsoftonline.com/)
* [Non è possibile accedere all'account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Collegamento "Problemi di accesso all'account?" nella pagina di accesso"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Verificare i dati di autenticazione"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Cambiare la password"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "La password è stata reimpostata"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Collegamento Reimposta password nella schermata di accesso di Windows 10 Fall Creators Update"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "Verificare i dati di autenticazione"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Cambiare la password"
