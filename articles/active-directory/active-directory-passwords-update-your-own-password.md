---
title: 'Azure AD: reimpostare la password | Microsoft Docs'
description: Usare la funzione di reimpostazione della password self-service per recuperare l'accesso al proprio account utente aziendale o dell'istituto di istruzione
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: a2ba6c6b59ba4decdfd621a62ec07ae04bfb98cb
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="reset-your-work-or-school-password"></a>Reimpostare la password aziendale o scolastica

Se si dimentica la password, la si vuole modificare, non è mai stata inviata dal servizio assistenza dell'azienda oppure l'account è stato bloccato, procedere come segue. Se si conosce la password e la si deve solo modificare, procedere fino alla sezione [Cambiare la password](#change-my-password).

   > [!NOTE]
   > Se si sta provando ad accedere nuovamente al proprio account personale come ad esempio Xbox, hotmail.com o outlook.com, provare i suggerimenti nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Reimpostare o sbloccare la password per un account aziendale o dell'istituto di istruzione

Potrebbe essere impossibile accedere all'account Azure Active Directory (Azure AD) a causa di uno dei motivi seguenti:

* La password non funziona e si desidera reimpostarla.
* Si conosce la password, ma l'account è bloccato e si desidera sbloccarlo.

Usare la seguente procedura per accedere alla reimpostazione password self-service (SSPR) di Azure AD e ritornare al proprio account.

1. In qualsiasi pagina di **Accesso** aziendale o dell'istituto di istruzione selezionare il collegamento **Problemi di accesso all'account?** e quindi selezionare **Account aziendale o dell'istituto di istruzione** oppure passare direttamente alla [pagina per la reimpostazione della password](https://passwordreset.microsoftonline.com/).

    ![Problemi di accesso all'account?][Login]

2. Immettere l'**ID utente** dell'account aziendale o dell'istituto di istruzione, dimostrare di non essere un robot immettendo i caratteri visualizzati e quindi selezionare **Avanti**.

   > [!NOTE]
   > Se il personale IT non ha abilitato questa funzionalità, viene visualizzato un collegamento "Contattare l'amministratore" che consente di ricevere assistenza per posta elettronica o tramite un portale Web.
   >
   > Se è necessario sbloccare l'account, selezionare qui l'opzione **Ricordo la password, ma non riesco ad accedere.**
   >

3. A seconda della configurazione definita dal personale IT, vengono visualizzati uno o più dei metodi di autenticazione seguenti per la reimpostazione della password self-service. L'utente o il personale IT dovrebbero aver specificato alcune di queste informazioni seguendo la procedura nell'articolo [Eseguire la registrazione per la reimpostazione password self-service](active-directory-passwords-reset-register.md).

   * **Invia messaggio di posta elettronica all'indirizzo di posta elettronica alternativo dell'utente**
   * **Invia SMS sul telefono cellulare**
   * **Chiama telefono cellulare**
   * **Chiama telefono ufficio**
   * **Rispondi alle domande di sicurezza**

   Scegliere un'opzione, indicare le risposte corrette, quindi selezionare **Avanti**.

   ![Verificare i dati di autenticazione][Verification]

4. Il personale IT può richiedere un'altra verifica. In questo caso potrebbe essere necessario ripetere il passaggio 3 con un'opzione diversa.
5. Nella pagina **Scegliere una nuova password** immettere una nuova password e confermarla, quindi selezionare **Fine**. La password aziendale o dell'istituto di istruzione potrebbe avere dei requisiti che è necessario rispettare. Si consiglia di scegliere una password che abbia da 8 a 16 caratteri e includa caratteri maiuscoli e minuscoli, un numero e un carattere speciale.
6. Quando viene visualizzato il messaggio **La password è stata reimpostata**, è possibile accedere con la nuova password.

    ![La password è stata reimpostata][Complete]

È ora possibile accedere all'account. Se non è possibile accedere all'account, contattare il personale IT dell'organizzazione per ricevere assistenza.

È possibile che si riceva un messaggio di posta elettronica di conferma proveniente da un account come "Microsoft per conto di \<organizzazione>". Se si riceve un messaggio di posta elettronica di questo tipo pur non avendo usato la reimpostazione password self-service per riottenere l'accesso all'account, contattare il personale IT.

## <a name="change-my-password"></a>Cambiare la password

Se si conosce già la password e si desidera cambiarla, seguire la procedura seguente.

### <a name="change-your-password-from-the-office-365-portal"></a>Cambiare la password dal portale di Microsoft Office 365

Usare questo metodo se in genere si accede alle applicazioni tramite il portale di Office:

1. Accedere al proprio [account Office 365](https://www.office.com) con la password esistente.
2. Selezionare il profilo in alto a destra e quindi selezionare **Visualizza account**.
3. Selezionare **Sicurezza e privacy** > **Password**.
4. Immettere la password precedente, impostare e confermare la nuova password, quindi selezionare **Invia**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Cambiare la password dal pannello di accesso di Azure

Usare questo metodo se in genere si accede alle applicazioni dal Pannello di accesso di Azure (MyApps):

1. Accedere al [Pannello di accesso di Azure](https://myapps.microsoft.com/) con la password esistente.
2. Selezionare il profilo in alto a destra e quindi selezionare **Profilo**.
3. Selezionare **Modifica password**.
4. Immettere la password precedente, impostare e confermare la nuova password, quindi selezionare **Invia**.

## <a name="reset-password-at-sign-in"></a>Reimpostazione della password all'accesso

Se l'amministratore ha abilitato la funzionalità, potrebbe essere disponibile il collegamento **Reimposta password** nella schermata di accesso di Windows 10 Fall Creators Update.

![Schermata di accesso][LoginScreen]

Selezionare il collegamento **Reimposta password** per avviare la procedura di reimpostazione della password self-service nella schermata di accesso. Ciò consente di reimpostare la password senza dover ricorrere alla normale procedura di accesso basata sul Web.

1. Confermare l'ID utente e selezionare **Avanti**.
2. Selezionare e confermare un metodo di contatto per la verifica. Il personale IT può richiedere un'altra verifica. In questo caso potrebbe essere necessario ripetere questo passaggio con un'opzione diversa.

   ![Metodo di contatto][ContactMethod]

3. Nella pagina **Crea una nuova password** immettere una nuova password, confermarla e quindi selezionare **Avanti**. È consigliabile che la password contenga una combinazione di 8-16 caratteri e includa caratteri minuscoli, maiuscoli, numeri e caratteri speciali.

   ![Reimpostazione della password][ResetPassword]

4. Quando viene visualizzato il messaggio **La password è stata reimpostata**, selezionare **Fine**.

È ora possibile accedere all'account. In caso contrario contattare il personale IT dell'organizzazione per ricevere assistenza.

## <a name="common-problems-and-their-solutions"></a>Problemi frequenti e relative soluzioni

 Di seguito sono riportati alcuni degli errori più comuni e le relative soluzioni:

| Scenario di errore| Tipo di errore visualizzato| Soluzione |
| --- | --- | --- |
| Quando si tenta di modificare la password viene visualizzato un errore. | La password contiene una parola o una frase o segue uno schema che la rende facile da indovinare. Riprovare con una password diversa. | Scegliere una password più difficile da indovinare. |
| Dopo aver immesso l'ID utente viene visualizzata una pagina "Contattare l'amministratore" | Contattare l'amministratore. <br> <br> È stato rilevato che la password dell'account utente non è gestita da Microsoft. Di conseguenza, non può essere reimpostata automaticamente. <br> <br> È necessario contattare il personale IT per ricevere assistenza aggiuntiva. | Si sta visualizzato questo messaggio perché il personale IT gestisce la password nell'ambiente locale. Non è possibile reimpostare la password dal collegamento "Problemi di accesso all'account?". <br> <br> Per reimpostare la password, contattare direttamente il personale IT per ricevere assistenza e informare i membri del fatto che si intende reimpostare la password, in modo che abilitino questa funzionalità per l'utente.|
| Dopo aver immesso l'ID utente viene visualizzato il messaggio di errore "Account non abilitato per la reimpostazione della password" | Account non abilitato per la reimpostazione della password. <br> <br> Il personale IT non ha configurato l'account per l'uso di questo servizio. <br> <br> Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password. | Questo messaggio viene visualizzato perché il personale IT non ha abilitato la reimpostazione della password per l'organizzazione dal collegamento "Problemi di accesso all'account?" o non consente all'utente di usare la funzionalità. <br> <br> Per reimpostare la password selezionare il "collegamento per contattare un amministratore" e inviare un messaggio di posta elettronica al personale IT della società per richiedere che venga abilitata la funzionalità di reimpostazione della password per l'utente. |
| Dopo aver immesso l'ID utente viene visualizzato il messaggio di errore "Non è stato possibile verificare l'account" | Non è stato possibile verificare l'account. <br> <br> Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password. | Questo messaggio viene visualizzato perché è abilitata la reimpostazione della password da parte dell'utente, ma non è stata eseguita la registrazione per usare il servizio. Per registrarsi per la reimpostazione della password, andare alla pagina http://aka.ms/ssprsetup dopo aver recuperato l'accesso al proprio account. <br> <br> Per reimpostare la password, selezionare il "collegamento per contattare un amministratore" e inviare un messaggio di posta elettronica al personale della società. |

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
