<properties 
	pageTitle="Procedura: Modificare o reimpostare la password di Azure AD | Microsoft Azure" 
	description="Informazioni su come registrarsi per reimpostare o modificare la password e come reimpostarla in caso di password dimenticata." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="asteen"/>

# Come aggiornare la password
Questo articolo fornisce informazioni utili su come gestire la password dell'account aziendale o dell'istituto di istruzione. Di seguito viene illustrato come eseguire le operazioni più comuni, ad esempio modificare o reimpostare una password o registrarsi per la reimpostazione della password.

* [**Come registrarsi per la reimpostazione della password**](#how-to-register-for-password-reset)
* [**Come modificare la password da Office 365**](#how-to-change-your-password-from-o365)
* [**Come modificare la password dal pannello di accesso**](#how-to-change-your-password-from-the-access-panel)
* [**Come reimpostare la password**](#how-to-reset-your-password)
* [**Come sbloccare l'account**](#how-to-unlock-your-account)
* [**Problemi frequenti e relative soluzioni**](#common-problems-and-their-solutions)

## Come registrarsi per la reimpostazione della password
Il modo più rapido per registrarsi per la reimpostazione della password è andare alla pagina http://aka.ms/ssprsetup.

 1. Accedere a http://aka.ms/ssprsetup.
 2. Immettere nome utente e password.
 3. Scegliere un'opzione per la registrazione facendo clic su **Imposta ora**. In questo caso viene illustrata la registrazione del **Telefono per l'autenticazione**.
 
    ![][011]
   
 4. Selezionare il codice paese nell'elenco a discesa e immettere il **numero di telefono esteso + indicativo di località**.
 
    ![][012] ![][013]
   
 5. Selezionare una opzione tra **Invia messaggio di testo** e **Chiama**. In questo caso viene selezionato **Invia messaggio di testo**. In questo modo verrà inviato un codice di 6 cifre al numero di telefono indicato. Attendere la ricezione del codice sul telefono.
 
    ![][014]
 
 6. Immettere il codice ricevuto nella casella di input, quindi fare clic su "Verifica".
 7. Quando viene visualizzato il messaggio **Grazie**, l'operazione è terminata. Ora è possibile usare i dati ottenuti con la registrazione per reimpostare la password in qualsiasi momento, visitando il sito https://passwordreset.microsoftonline.com.
 
    ![][015]

 >[AZURE.IMPORTANT]Se l'amministratore consente di registrarsi per più di una opzione, è consigliabile registrare anche un'opzione di backup nel caso in cui si perda il telefono o non sia possibile accedere alla posta elettronica.

## Come modificare la password da O365
Seguire questa procedura per modificare la password dell'account aziendale o dell'istituto di istruzione in Office 365. Per reimpostare la password dimenticata, seguire la procedura riportata [qui](#how-to-reset-your-password).

 1. Accedere a Office 365 con l'account aziendale o dell'istituto di istruzione.
 2. Andare a **Impostazioni** > **Impostazioni di Office 365** > **Password** > **Cambia password**.
 3. Digitare la vecchia password, quindi digitare una nuova password e confermarla.
 4. Fare clic su **Save**.

Altre informazioni al riguardo sono disponibili nel [centro di documentazione di Office 365](https://support.office.com/article/Change-my-password-in-Office-365-for-business-d1efbaee-63a7-4c08-ab1d-71bf932bbb5d).

## Come modificare la password dal pannello di accesso
Seguire questa procedura per modificare la password dell'account aziendale o dell'istituto di istruzione dal [pannello di accesso](https://myapps.microsoft.com). Per reimpostare la password dimenticata, seguire la procedura riportata [qui](#how-to-reset-your-password).

 1. Accedere a https://myapps.microsoft.com con l'account aziendale o dell'istituto di istruzione.
 2. Fare clic sulla scheda **Profilo**.
 3. Fare clic sul riquadro di **modifica della password** sul lato destro dello schermo.
 4. Digitare la vecchia password, quindi digitare una nuova password e confermarla.
 5. Fare clic su **Save**.
 
 In caso di problemi nella modifica della password, vedere la sezione [Problemi frequenti e relative soluzioni](#common-problems-and-their-solutions).

## Come reimpostare la password


Seguire questa procedura per reimpostare la password dell'account aziendale o dell'istituto di istruzione da qualsiasi schermata di accesso dell'account.

 >[AZURE.IMPORTANT]Questa funzionalità è disponibile solo se abilitata dall'amministratore. Se l'account non è abilitato per questa funzionalità, viene visualizzato un messaggio di avviso. È possibile contattare l'amministratore usando l'apposito collegamento, in questo caso per chiedere di sbloccare l'account.<br><br>Se questa funzionalità è stata abilitata dall'amministratore, per usarla è necessario prima di tutto effettuare l'iscrizione. A questo scopo, accedere a: http://aka.ms/ssprsetup.
 

 1. In una qualsiasi pagina di accesso dell'account aziendale o dell'istituto di istruzione fare clic sul collegamento "Problemi di accesso all'account?" o "Password dimenticata?" oppure passare direttamente a https://passwordreset.microsoftonline.com.
 
    ![][001]
 
 2. Nella pagina "Identificarsi" immettere l'ID dell'account aziendale o dell'istituto di istruzione e rispondere al test captcha.
 
    ![][002]
 
 3. Fare clic sul pulsante "Avanti".
 4. Scegliere un'opzione per reimpostare la password. In base alla configurazione del sistema da parte dell'amministratore, viene visualizzata una o più delle opzioni seguenti:
     * **Invia messaggio di posta elettronica all'indirizzo di posta elettronica alternativo dell'utente**: invia un messaggio di posta elettronica con un codice di 6 cifre all'**indirizzo di posta elettronica alternativo** o all'**indirizzo di posta elettronica per l'autenticazione** (scegliere l'opzione preferita).
	 * **Invia SMS sul telefono cellulare**: invia un messaggio di testo con un codice di 6 cifre al **cellulare** dell'utente o all'**indirizzo di posta elettronica per l'autenticazione** (scegliere l'opzione preferita).
	 * **Chiama cellulare utente**: chiama il **cellulare** dell'utente o il **telefono per l'autenticazione** (scegliere l'opzione preferita). Premere il tasto *#* per verificare la chiamata.
	 * **Chiama telefono ufficio**: chiama il **telefono dell'ufficio** dell'utente. Premere il tasto *#* per verificare la chiamata.
	 * **Rispondi alle domande di sicurezza**: consente di visualizzare le domande di sicurezza registrate in precedenza per poter rispondere.

    ![][003]
	 
 5. Come esempio viene usata l'opzione "Invia SMS sul telefono cellulare". Se si usa un'opzione basata sul telefono, è necessario verificare il numero di telefono prima che sia possibile ricevere un SMS. Immettere il numero di telefono completo e quindi fare clic su **Avanti** per verificare che sia corretto e ricevere un SMS.
 
    ![][004]
 
 6. Dopo aver ricevuto il messaggio di testo, assicurarsi di usare il codice di verifica contenuto nel corpo del messaggio, non il numero da cui il codice è stato inviato. Potrebbero passare alcuni minuti prima di ricevere il messaggio di testo.
 
    ![][009]
 
 8. A questo punto, immettere il codice ricevuto sul telefono nella casella di input della pagina.
 
    ![][005]
 
 9. L'amministratore può richiedere un secondo passaggio di verifica. In tal caso, ripetere il passaggio 4 selezionando un'opzione diversa.
 10. Nella schermata "Scegliere una nuova password", selezionare una nuova password e confermarla, quindi fare clic su **Fine**.
 
    ![][006] ![][007]
  
 11. Quando viene visualizzata la pagina di notifica dell'esito positivo, l'operazione è terminata. Ora è possibile eseguire l'accesso con la nuova password.
 
    ![][008]
 
In caso di problemi nella reimpostazione della password, vedere la sezione [Problemi frequenti e relative soluzioni](#common-problems-and-their-solutions).

## Come sbloccare l'account
Seguire questa procedura per sbloccare l'account locale da qualsiasi schermata di accesso dell'account aziendale o dell'istituto di istruzione. **Nota: è possibile sbloccare l'account solo se è stato bloccato a livello locale.**

 >[AZURE.IMPORTANT]Questa funzionalità è disponibile solo se abilitata dall'amministratore. Se l'account non è abilitato per questa funzionalità, viene visualizzato un messaggio di avviso. È possibile contattare l'amministratore usando l'apposito collegamento, in questo caso per chiedere di sbloccare l'account.<br><br>Se questa funzionalità è stata abilitata dall'amministratore, per usarla è necessario prima di tutto effettuare l'iscrizione. A questo scopo, accedere a: http://aka.ms/ssprsetup.


 1. In una qualsiasi pagina di accesso dell'account aziendale o dell'istituto di istruzione fare clic sul collegamento "Problemi di accesso all'account?" o "Password dimenticata?" oppure passare direttamente a https://passwordreset.microsoftonline.com.
 
    ![][001]
 
 2. Nella pagina "Identificarsi" immettere l'ID dell'account aziendale o dell'istituto di istruzione e rispondere al test captcha.
 
    ![][002]
 
 3. Fare clic sul pulsante "Avanti".
 4. Scegliere un'opzione per sbloccare l'account. In base alla configurazione del sistema da parte dell'amministratore, viene visualizzata una o più delle opzioni seguenti:
     * **Invia messaggio di posta elettronica all'indirizzo di posta elettronica alternativo dell'utente**: invia un messaggio di posta elettronica con un codice di 6 cifre all'**indirizzo di posta elettronica alternativo** o all'**indirizzo di posta elettronica per l'autenticazione** (scegliere l'opzione preferita).
	 * **Invia SMS sul telefono cellulare**: invia un messaggio di testo con un codice di 6 cifre al **cellulare** dell'utente o all'**indirizzo di posta elettronica per l'autenticazione** (scegliere l'opzione preferita).
	 * **Chiama cellulare utente**: chiama il **cellulare** dell'utente o il **telefono per l'autenticazione** (scegliere l'opzione preferita). Premere il tasto *#* per verificare la chiamata.
	 * **Chiama telefono ufficio**: chiama il **telefono dell'ufficio** dell'utente. Premere il tasto *#* per verificare la chiamata.
	 * **Rispondi alle domande di sicurezza**: consente di visualizzare le domande di sicurezza registrate in precedenza per poter rispondere.

    ![][003]
	 
 5. Come esempio viene usata l'opzione "Invia SMS sul telefono cellulare". Se si usa un'opzione basata sul telefono, è necessario verificare il numero di telefono prima che sia possibile ricevere un SMS. Immettere il numero di telefono completo e quindi fare clic su **Avanti** per verificare che sia corretto e ricevere un SMS.
 
    ![][004]
 
 6. Dopo aver ricevuto il messaggio di testo, assicurarsi di usare il codice di verifica contenuto nel corpo del messaggio, non il numero da cui il codice è stato inviato. Potrebbero passare alcuni minuti prima di ricevere il messaggio di testo.
 
    ![][009]
 
 8. A questo punto, immettere il codice ricevuto sul telefono nella casella di input della pagina.
 
    ![][005]
 
 9. L'amministratore può richiedere un secondo passaggio di verifica. In tal caso, ripetere il passaggio 4 selezionando un'opzione diversa.
 
 11. Quando viene visualizzata la pagina di notifica dell'esito positivo, l'operazione è terminata. L'account locale è stato sbloccato ed è nuovamente possibile eseguire l'accesso.
 
    ![][010]
  
 >[AZURE.IMPORTANT]Tenere sempre aggiornati tutti i dispositivi con la password più recente. Accade spesso, infatti, che la causa del problema sia un'app con una password vecchia (ad esempio il client di posta elettronica del telefono) che ha provocato il blocco dell'account.
 
In caso di problemi nello sblocco dell'account, vedere la sezione [Problemi frequenti e relative soluzioni](#common-problems-and-their-solutions).

## Problemi frequenti e relative soluzioni
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
              <p>Dopo aver immesso l'ID utente viene visualizzata una pagina "Contattare l'amministratore".</p>
            </td>
            <td>
              <p>Contattare l'amministratore <br><br>È stato rilevato che la password dell'account utente non è gestita da Microsoft. Di conseguenza, non può essere reimpostata automaticamente. <br><br>Per ulteriore assistenza, sarà necessario contattare l'amministratore o il supporto tecnico. </p>
            </td>
            <td>
              <p>Questo messaggio viene visualizzato perché l'amministratore gestisce la password nell'ambiente locale e non consente di reimpostarla dal collegamento <b>Problemi di accesso all'account?</b>. <br><br> Per reimpostare la password, contattare l'amministratore direttamente per assistenza e richiedere che venga abilitata la funzionalità di reimpostazione della password da Office 365 per l'utente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Dopo aver immesso l'ID utente viene visualizzato il messaggio di errore "Account non abilitato per la reimpostazione della password" </p>
            </td>
            <td>
              <p>Account non abilitato per la reimpostazione della password<br><br>L'amministratore non ha configurato l'account per l'utilizzo di questo servizio.<br><br> Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password.</p>
            </td>
            <td>
              <p>Questo messaggio viene visualizzato perché l'amministratore non ha abilitato la reimpostazione della password per l'organizzazione dal collegamento <b>Problemi di accesso all'account?</b> o non consente all'utente di usare questa la funzionalità. <br><br> Per reimpostare la password, fare clic sul collegamento <b>contattare un amministratore</b> per inviare un messaggio di posta elettronica all'amministrazione della società e richiedere che venga abilitata la funzionalità di reimpostazione della password da Office 365 per l'utente.</p>
            </td>
          </tr>
		  <tr>
            <td>
              <p>Dopo aver immesso l'ID utente viene visualizzato il messaggio di errore "Non è stato possibile verificare l'account".</p>
            </td>
            <td>
              <p>Non è stato possibile verificare l'account<br><br>Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password. </p>
            </td>
            <td>
              <p>Questo messaggio viene visualizzato perché è abilitata la reimpostazione della password da parte dell'utente, ma non è stata eseguita la registrazione per usare il servizio. Per registrarsi per la reimpostazione della password, andare alla pagina http://aka.ms/ssprsetup dopo aver recuperato l'accesso al proprio account. <br><br> Per reimpostare la password, fare clic sul collegamento <b>contattare un amministratore</b> per inviare un messaggio di posta elettronica all'amministrazione della società.</p>
            </td>
          </tr>
        </tbody></table>

## Collegamenti alla documentazione relativa alla reimpostazione della password
Di seguito vengono forniti collegamenti a tutte le pagine della documentazione relative alla reimpostazione della password in Azure AD:

* [**Funzionamento**](active-directory-passwords-how-it-works.md): informazioni sui sei diversi componenti del servizio e sulle relative funzioni
* [**Introduzione**](active-directory-passwords-getting-started.md): informazioni su come consentire agli utenti di reimpostare e modificare le password cloud o locali
* [**Personalizzazione**](active-directory-passwords-customize.md): informazioni su come personalizzare l'aspetto e il comportamento del servizio in base alle esigenze dell'organizzazione
* [**Procedure consigliate**](active-directory-passwords-best-practices.md): informazioni su come distribuire rapidamente e gestire in modo efficace le password nell'organizzazione
* [**Informazioni dettagliate**](active-directory-passwords-get-insights.md): informazioni sulle funzionalità di creazione report integrate
* [**Domande frequenti**](active-directory-passwords-faq.md): risposte alle domande frequenti
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere rapidamente eventuali problemi con il servizio
* [**Ulteriori informazioni**](active-directory-passwords-learn-more.md): approfondimenti sui dettagli tecnici del funzionamento del servizio



[001]: ./media/active-directory-passwords-update-your-own-password/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-update-your-own-password/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-update-your-own-password/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-update-your-own-password/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-update-your-own-password/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-update-your-own-password/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-update-your-own-password/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-update-your-own-password/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-update-your-own-password/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-update-your-own-password/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-update-your-own-password/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-update-your-own-password/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-update-your-own-password/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-update-your-own-password/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-update-your-own-password/015.jpg "Image_015.jpg"

<!---HONumber=Nov15_HO4-->