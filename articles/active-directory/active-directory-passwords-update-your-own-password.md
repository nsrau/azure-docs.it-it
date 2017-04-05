---
title: Come aggiornare la password con Azure Active Directory | Microsoft Docs
description: Informazioni su come registrarsi per reimpostare o modificare la password e come reimpostarla in caso di password dimenticata.
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 47bd039ce61351b18e19408679b1d847cc70eef3
ms.lasthandoff: 03/28/2017


---
# <a name="update-your-own-password"></a>Aggiornare la password
Questo articolo fornisce informazioni utili su come gestire la password dell'account aziendale o dell'istituto di istruzione. Informazioni su come registrarsi per la reimpostazione della password, modificare o reimpostare la password e sbloccare l'account.

## <a name="dont-lose-access-to-your-account"></a>Mantenere l'accesso all'account

> [!IMPORTANT]
> **Perché viene visualizzato questo messaggio?** Se si è selezionato un collegamento, è probabile che questo messaggio sia stato visualizzato perché l'amministratore richiede che venga eseguita la registrazione per la reimpostazione della password per ottenere l'accesso all'app. Potrebbero essere richieste informazioni relative al numero di telefono o all'indirizzo di posta elettronica oppure l'impostazione di domande di sicurezza. Nessun problema. Queste informazioni verranno usate solo per mantenere più sicuro l'account. La procedura qui illustrata consentirà di raggiungere l'obiettivo.
>

Il modo più rapido per registrarsi per la reimpostazione della password è visitare la pagina di [registrazione per la reimpostazione della password](http://aka.ms/ssprsetup).  

1. Passare alla pagina di [registrazione per la reimpostazione della password](http://aka.ms/ssprsetup).
2. Immettere nome utente e password.
3. Fare clic su **Imposta ora** per scegliere un'opzione di registrazione. Questo esempio mostra come registrarsi usando un **Telefono per l'autenticazione**. L'amministratore potrebbe richiedere all'utente di eseguire la registrazione per più di un'opzione. 

   ![Opzioni di configurazione][101]
4. Selezionare il codice del paese dall'elenco a discesa, immettere il numero di telefono completo, incluso il prefisso, e scegliere **Invia un SMS** o **Chiama**. Entrambe le opzioni forniscono un codice. 

   ![Verifica telefonica][102]
5. Immettere il codice ricevuto e quindi fare clic su **Verifica**. 

   ![Esempio di codice di verifica][103]
6. Fare clic su **Fine** per completare la reimpostazione della password. Ora è possibile usare i dati ottenuti con la registrazione per reimpostare la password in qualsiasi momento, passando alla pagina [Identificarsi](https://passwordreset.microsoftonline.com).

   ![Informazioni di ripristino][104]

> [!IMPORTANT]
> Se l'amministratore consente di registrarsi per più di una opzione, è consigliabile registrare anche un'opzione di backup nel caso in cui si perda il telefono o non sia possibile accedere alla posta elettronica.
   >
   >

## <a name="change-your-password-in-office-365"></a>Modificare la password in Office 365
Seguire questa procedura per modificare la password dell'account aziendale o dell'istituto di istruzione in Office 365. Per reimpostare la password dimenticata, seguire la procedura riportata nella sezione [Reimpostare la password](#how-to-reset-your-password).

1. Accedere a Office 365 con l'account aziendale o dell'istituto di istruzione.
2. Passare a **Impostazioni** > **Impostazioni di Office 365** > **Password** > **Cambia password**.
3. Immettere la vecchia password, quindi immettere una nuova password e confermarla.
4. Fare clic su **Save**.

Per altre informazioni sulla modifica della password in Office 365, vedere il [centro documentazione di Office 365](https://support.office.com/article/Change-my-password-in-Office-365-for-business-d1efbaee-63a7-4c08-ab1d-71bf932bbb5d).

## <a name="change-your-password-from-the-access-panel"></a>Modificare la password dal pannello di accesso
Seguire questa procedura per modificare la password dell'account aziendale o dell'istituto di istruzione dal [pannello di accesso](https://myapps.microsoft.com). Per reimpostare la password dimenticata, seguire la procedura riportata nella sezione [Reimpostare la password](#how-to-reset-your-password).

1. Accedere a https://myapps.microsoft.com con l'account aziendale o dell'istituto di istruzione.
2. Fare clic sulla scheda **Profilo**. 
3. Fare clic su **Cambia password**.
4. Immettere la vecchia password, quindi immettere una nuova password e confermarla.
5. Fare clic su **Submit**.

In caso di problemi nella modifica della password, vedere la sezione [Problemi frequenti e relative soluzioni](#common-problems-and-their-solutions).

## <a name="reset-your-password"></a>Reimpostare la password
Seguire questa procedura per reimpostare la password dell'account aziendale o dell'istituto di istruzione da qualsiasi pagina di accesso dell'account.

> [!IMPORTANT]
> Questa funzionalità è disponibile solo se abilitata dall'amministratore. Se l'account non è abilitato per questa funzionalità, viene visualizzato un messaggio di avviso. È possibile usare il collegamento **Contattare l'amministratore** per chiedere all'amministratore di sbloccare l'account.
>
> Se l'amministratore ha abilitato l'account per questa funzionalità, per poterla usare è prima necessario effettuare l'iscrizione. A tale scopo, passare alla pagina di [registrazione per la reimpostazione della password](http://aka.ms/ssprsetup).
>
>

1. In una qualsiasi pagina di accesso dell'account aziendale o dell'istituto di istruzione fare clic sul collegamento **Problemi di accesso all'account?** o passare alla pagina [Identificarsi](https://passwordreset.microsoftonline.com).

   ![Accesso all'account][110]

2. Immettere l'ID dell'account aziendale o dell'istituto di istruzione e rispondere al test CAPTCHA. 

   ![Conferma dell'identità][111]

3. Fare clic su **Avanti**.
4. Scegliere un'opzione per reimpostare la password. In base alla configurazione del sistema da parte dell'amministratore, viene visualizzata una o più delle opzioni seguenti:

   * **Invia messaggio di posta elettronica all'indirizzo di posta elettronica alternativo dell'utente**: invia un messaggio di posta elettronica con un codice di 6 cifre all'**indirizzo di posta elettronica alternativo** o all'**indirizzo di posta elettronica per l'autenticazione**, in base all'opzione preferita dall'utente.
   * **Invia SMS sul telefono cellulare**: invia un messaggio di testo con un codice di 6 cifre al **cellulare** dell'utente o all'**indirizzo di posta elettronica per l'autenticazione**, in base all'opzione preferita dall'utente.
   * **Chiama telefono cellulare**: chiama il **cellulare** dell'utente o il **telefono per l'autenticazione**, in base all'opzione preferita dall'utente. Premere il tasto *#* per verificare la chiamata.
   * **Chiama telefono ufficio**: chiama il **telefono dell'ufficio** dell'utente. Premere il tasto *#* per verificare la chiamata.
   * **Rispondi alle domande di sicurezza**: consente di visualizzare le domande di sicurezza registrate in precedenza per poter rispondere.

   ![Opzioni di reimpostazione delle password][109]

5. L'esempio mostra l'opzione **Invia SMS sul telefono cellulare** selezionata. Per le opzioni basate sul telefono, immettere il numero di telefono completo e quindi scegliere **Testo** per verificarlo. Viene inviato un SMS con un codice di 6 cifre al telefono dell'utente.

6. Dopo aver ricevuto il messaggio di testo, assicurarsi di usare il codice di verifica contenuto nel corpo del messaggio, non il numero da cui il codice è stato inviato. La ricezione del messaggio SMS potrebbe richiedere alcuni minuti.

7. Immettere il codice di sei cifre ricevuto e fare clic su **Avanti**.

8. L'amministratore potrebbe richiedere un passaggio di verifica aggiuntivo. In tal caso, ripetere il passaggio 4 selezionando un'altra opzione.

9. Nella pagina **Scegliere una nuova password** immettere una nuova password e confermarla, quindi fare clic su **Fine**.

   ![Conferma della nuova password][107]

10. Se la password viene accettata, è possibile accedere con la nuova password.

    ![Conferma di reimpostazione della password][108]

In caso di problemi nella reimpostazione della password, vedere la sezione [Problemi frequenti e relative soluzioni](#common-problems-and-their-solutions).

## <a name="unlock-your-account"></a>Sbloccare l'account
Seguire questa procedura per sbloccare l'account locale da qualsiasi pagina di accesso dell'account aziendale o dell'istituto di istruzione. 

> [!NOTE]
> È possibile sbloccare l'account solo se è stato bloccato a livello locale.

> [!IMPORTANT]
> Questa funzionalità è disponibile solo se abilitata dall'amministratore. Se l'account non è abilitato per questa funzionalità, viene visualizzato un messaggio di avviso. È possibile usare il collegamento **Contattare l'amministratore** per chiedere all'amministratore di sbloccare l'account.
>
> Se l'amministratore ha abilitato l'account per questa funzionalità, per poterla usare è prima necessario effettuare l'iscrizione. A tale scopo, passare alla pagina di [registrazione per la reimpostazione della password](http://aka.ms/ssprsetup).
>
>

1. In una qualsiasi pagina di accesso dell'account aziendale o dell'istituto di istruzione fare clic sul collegamento **Problemi di accesso all'account?** o passare alla pagina [Identificarsi](https://passwordreset.microsoftonline.com).

   ![Accesso all'account][110]
2. Nella pagina **Identificarsi** immettere l'ID dell'account aziendale o dell'istituto di istruzione e rispondere al test CAPTCHA. 
3. Fare clic su **Avanti**.

   ![Conferma dell'identità][111]
4. Scegliere un'opzione per sbloccare l'account. In base alla configurazione del sistema da parte dell'amministratore, viene visualizzata una o più delle opzioni seguenti:

   * **Invia messaggio di posta elettronica all'indirizzo di posta elettronica alternativo dell'utente**: invia un messaggio di posta elettronica con un codice di 6 cifre all'**indirizzo di posta elettronica alternativo** o all'**indirizzo di posta elettronica per l'autenticazione**, in base all'opzione preferita dall'utente.
   * **Invia SMS sul telefono cellulare**: invia un messaggio di testo con un codice di 6 cifre al **cellulare** dell'utente o all'**indirizzo di posta elettronica per l'autenticazione**, in base all'opzione preferita dall'utente.
   * **Chiama telefono cellulare**: chiama il **cellulare** dell'utente o il **telefono per l'autenticazione**, in base all'opzione preferita dall'utente. Premere il tasto *#* per verificare la chiamata.
   * **Chiama telefono ufficio**: chiama il **telefono dell'ufficio** dell'utente. Premere il tasto *#* per verificare la chiamata.
   * **Rispondi alle domande di sicurezza**: consente di visualizzare le domande di sicurezza registrate in precedenza per poter rispondere.

   ![Opzioni di verifica dell'identità][112]
5. Questo esempio mostra l'opzione **Rispondi alle domande di sicurezza** selezionata. Compilare le risposte alle domande di sicurezza e fare clic su **Avanti** per verificare l'identità.

6. L'amministratore potrebbe richiedere un passaggio di verifica aggiuntivo. In tal caso, ripetere il passaggio 4 selezionando un'altra opzione.
7. Quando viene visualizzata la pagina di notifica dell'esito positivo, l'account locale è stato sbloccato ed è possibile eseguire l'accesso.

   ![Accesso all'account sbloccato][113]

> [!IMPORTANT]
> Tenere sempre aggiornati tutti i dispositivi con la password più recente. Spesso, la causa del problema che provoca il blocco dell'account è un'app inaffidabile con una password vecchia, ad esempio il client di posta elettronica del telefono.
   >
   >


## <a name="common-problems-and-their-solutions"></a>Problemi frequenti e relative soluzioni
Di seguito sono riportati alcuni degli errori più comuni e le relative soluzioni:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Scenario di errore</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Tipo di errore visualizzato</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Soluzione</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Dopo aver immesso l'ID utente viene visualizzata una pagina **Contattare l'amministratore**.</p>
            </td>
            <td>
              <p>Contattare l'amministratore. <br><br>È stato rilevato che la password dell'account utente non è gestita da Microsoft. Di conseguenza, non può essere reimpostata automaticamente. <br><br>Per ulteriore assistenza, sarà necessario contattare l'amministratore o il supporto tecnico. </p>
            </td>
            <td>
              <p>Questo messaggio viene visualizzato perché l'amministratore gestisce la password nell'ambiente locale e non consente di reimpostarla dal collegamento <b>Problemi di accesso all'account?</b>. <br><br> Per reimpostare la password, contattare direttamente l'amministratore per assistenza e richiedere che venga abilitata la funzionalità di reimpostazione della password da Office 365 per l'utente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Dopo aver immesso l'ID utente viene visualizzato il messaggio di errore "Account non abilitato per la reimpostazione della password".</p>
            </td>
            <td>
              <p>Account non abilitato per la reimpostazione della password.<br><br>L'amministratore non ha configurato l'account per l'utilizzo di questo servizio.<br><br> Se si desidera, è possibile contattare un amministratore dell'organizzazione per reimpostare la password.</p>
            </td>
            <td>
              <p>Questo messaggio viene visualizzato perché l'amministratore non ha abilitato la reimpostazione della password per l'organizzazione dal collegamento <b>Problemi di accesso all'account?</b> o non consente all'utente di usare la funzionalità. <br><br> Per reimpostare la password, fare clic sul collegamento <b>Contattare l'amministratore</b> per inviare un messaggio di posta elettronica all'amministrazione dell'organizzazione e richiedere che venga abilitata la funzionalità di reimpostazione della password da Office 365 per l'utente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Dopo aver immesso l'ID utente viene visualizzato il messaggio di errore "Non è stato possibile verificare l'account".</p>
            </td>
            <td>
              <p>Non è stato possibile verificare l'account.<br><br>Se si desidera, è possibile contattare un amministratore dell'organizzazione per reimpostare la password. </p>
            </td>
            <td>
              <p>Questo messaggio viene visualizzato perché è abilitata la reimpostazione della password da parte dell'utente, ma non è stata eseguita la registrazione per usare il servizio. Per registrarsi per la reimpostazione della password, andare alla pagina di [registrazione per la reimpostazione della password](http://aka.ms/ssprsetup) dopo aver recuperato l'accesso all'account. <br><br> Per reimpostare la password, fare clic sul collegamento <b>Contattare l'amministratore</b> per inviare un messaggio di posta elettronica all'amministrazione dell'organizzazione</p>
            </td>
          </tr>
        </tbody></table>

## <a name="next-steps"></a>Passaggi successivi
Per altre domande sulla reimpostazione della password self-service, contattare l'amministratore o selezionare questi collegamenti:

* [È necessario registrare le informazioni per la reimpostazione della password self-service?](http://aka.ms/ssprsetup)
* [Problemi di accesso all'account?](https://passwordreset.microsoftonline.com)
* [Informazioni sulla reimpostazione della password in Office 365](https://support.office.com/en-us/article/Reset-user-passwords-in-Office-365-3254c031-04c9-44f1-8fda-2563847a6b31?ui=en-US&rs=en-US&ad=US)
* [Pannello di accesso](https://myapps.microsoft.com)

[101]: ./media/active-directory-passwords-update-your-own-password/password-1-dont-lose-access.png "password-1-dont-lose-access.png"
[102]: ./media/active-directory-passwords-update-your-own-password/password-2-verification-response.png "password-2-verification-response.png"
[103]: ./media/active-directory-passwords-update-your-own-password/password-2-verification-text.png "password-2-verification-text.png"
[104]: ./media/active-directory-passwords-update-your-own-password/password-3-registration-complete.png "password-3-registration-complete.png"
[105]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-cant-access.png "password-4-reset-cant-access.png"
[106]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-captcha.png "password-4-reset-captcha.png"
[107]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-change.png "password-4-reset-change.png"
[108]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-finished.png "password-4-reset-finished.png"
[109]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-verification.png "password-4-reset-verification.png"
[110]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-cant-access.png "password-5-unlock-cant-access.png"
[111]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-captcha.png "password-5-unlock-captcha.png"
[112]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-verification.png "password-5-unlock-verification.png"
[113]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-finished.png "password-5-unlock-finished.png"

