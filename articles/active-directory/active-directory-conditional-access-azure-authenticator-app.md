
<properties
	pageTitle="Azure Authenticator per Android | Microsoft Azure"
	description="L'app Microsoft Azure Authenticator può essere usata per eseguire l'accesso alle risorse di lavoro. L'app Azure Authenticator segnala eventuali richieste di verifica a due fattori in sospeso con la visualizzazione di un avviso nel dispositivo mobile."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/24/2015"
	ms.author="femila"/>

# Azure Authenticator per Android

L'amministratore IT può consigliare di usare Microsoft Azure Authenticator per eseguire l'accesso alle risorse di lavoro. Questa applicazione fornisce due opzioni di accesso:

* Multi-Factor Authentication permette di proteggere gli account aziendali o dell'istituto di istruzione con una verifica in due passaggi. Si effettua l'accesso usando un elemento noto, ad esempio la password, e si protegge ulteriormente l'account usando un elemento disponibile, ad esempio una chiave di sicurezza fornita dall'app. L'app Azure Authenticator segnala eventuali richieste di verifica a due fattori in sospeso con la visualizzazione di un avviso nel dispositivo mobile. È sufficiente visualizzare la richiesta nell'app e toccare Verifica o Annulla. In alternativa, può essere richiesta l'immissione del passcode visualizzato nell'app.

* L'account aziendale permette di trasformare il telefono o il tablet Android in un dispositivo attendibile e di fornire l'accesso Single Sign-On (SSO) alle applicazioni aziendali. L'amministratore IT può richiedere l'aggiunta di un account aziendale per accedere alle risorse aziendali. SSO permette di effettuare l'accesso una volta sola e di accedere automaticamente a tutte le applicazioni rese disponibili dall'azienda.

## Installazione dell'app Azure Authenticator

È possibile installare l'app Azure Authenticator da Google Play Store. La procedura per aggiungere un account aziendale da un dispositivo Samsung Android e da un dispositivo non Samsung Android è leggermente diversa. Di seguito sono fornite le istruzioni per entrambi i casi.

Aggiunta dell'account aziendale da un dispositivo Samsung Android
----------------------------------------------------------------------------------------------------------------
###Aggiunta dell'account aziendale nella schermata iniziale dell'app

Le istruzioni seguenti sono applicabili a telefoni Samsung GS3 e modelli superiori o tablet Note2 e modelli superiori.

1. Nella schermata iniziale dell'app accettare il Contratto di Licenza con l'utente finale.
2. Nella schermata Attiva account fare clic sul menu di scelta rapida a destra, quindi selezionare **Account aziendale**.
3. Nella schermata Aggiungi account selezionare **Account aziendale**.
4. Nella schermata relativa all'attivazione dell'amministratore del dispositivo fare clic su **Attiva**.
5. Nella schermata Informativa sulla privacy selezionare la casella di controllo e quindi fare clic su **Conferma**.
6. Nella schermata Aggiunta all'area di lavoro immettere l'ID utente fornito dall'organizzazione, quindi fare clic su **Aggiungi**.
7. Per accedere all'app Azure Authenticator, immettere l'****account aziendale e la password, quindi fare clic su **Accedi**.
8. La schermata successiva che mostra le informazioni relative all'autenticazione a più fattori (MFA) viene presentata per maggiore sicurezza ed è facoltativa. Questa schermata viene visualizzata se l'azienda o l'istituto di istruzione richiede l'autenticazione basata su un secondo fattore per la creazione di account aziendali e include istruzioni per verificare ulteriormente l'account.
9. Nella schermata Aggiunta all'area di lavoro viene visualizzato il messaggio "**Aggiunta all'area di lavoro in corso**". L'app Azure Authenticator sta provando ad aggiungere il dispositivo all'area di lavoro.
10. Nella schermata successiva dovrebbe essere visualizzato il messaggio di completamento dell'aggiunta all'area di lavoro.

>[AZURE.NOTE]Nel dispositivo può essere presente un solo account aziendale.

### Aggiunta dell'account aziendale dal menu Impostazioni
Dopo l'installazione dell'app Azure Authenticator, è anche possibile creare un account aziendale da Android Account Manager.

1. Nel menu Impostazioni passare ad **Account** e scegliere **Aggiungi account**.
2. Seguire i passaggi 3-10 della procedura Aggiunta dell'account aziendale nella schermata iniziale dell'app per aggiungere un account aziendale.

Aggiunta dell'account aziendale da un dispositivo non Samsung Android
------------------------------------------------------------------------------------------------------------------
### Aggiunta dell'account aziendale nella schermata iniziale dell'app

1. Nella schermata iniziale dell'app accettare il Contratto di Licenza con l'utente finale.
2. Nella schermata Attiva account fare clic sul menu di scelta rapida a destra, quindi selezionare **Account aziendale**.
3. Nella schermata Account scegliere **Aggiungi account**.
4. Se viene visualizzata la schermata Account scegliere **Aggiungi account**. Se in precedenza è già stato creato un account aziendale, verrà visualizzata la schermata Sincronizzazione con l'account aziendale esistente. Per mantenere l'account aziendale, è sufficiente toccare la freccia indietro per tornare alla schermata iniziale. In alternativa, è possibile selezionare l'account da rimuovere e ricreare un nuovo account aziendale. Nella schermata Aggiunta all'area di lavoro immettere l'ID utente fornito dall'organizzazione, quindi fare clic su Aggiungi.
5. Per accedere all'app Azure Authenticator, immettere l'account aziendale e la password, quindi fare clic su **Accedi**.
7. La schermata successiva che mostra le informazioni relative all'autenticazione a più fattori (MFA) viene presentata per maggiore sicurezza ed è facoltativa. Questa schermata viene visualizzata se l'azienda o l'istituto di istruzione richiede l'autenticazione basata su un secondo fattore per la creazione di account aziendali e include istruzioni per verificare ulteriormente l'account.
8. Fare clic su **OK** nella schermata successiva. Non cambiare il nome del certificato. Nella schermata Aggiunta all'area di lavoro viene visualizzato il messaggio "Aggiunta all'area di lavoro in corso". L'app Azure Authenticator sta provando ad aggiungere il dispositivo all'area di lavoro. Nella schermata successiva dovrebbe essere visualizzato il messaggio di completamento dell'aggiunta all'area di lavoro.

>[AZURE.NOTE]Nel dispositivo può essere presente un solo account aziendale.

Dopo l'installazione dell'app Azure Authenticator, è anche possibile creare un account aziendale da Android Account Manager.

1. Nel menu Impostazioni passar**e ad **Account** e scegliere **Aggiungi account**.
2. Seguire i passaggi 2-7 della procedura Aggiunta dell'account aziendale nella schermata** iniziale dell'app per aggiungere un account aziendale.

### Come verificare la versione installata

1. È possibile scoprire la versione dell'app Azure Authenticator e dei servizi associati installati nel dispositivo.
2. Scegliere **Informazioni su** dal menu a comparsa.
3. Nella schermata Informazioni su sono visualizzati i servizi dell'app e le versioni installate nel dispositivo.
 
### Invio di file di log per segnalare problemi

1. Seguire le istruzioni sul sito del Supporto Microsoft per segnalare un evento imprevisto con l'app Azure Authenticator, ottenere un numero di richiesta di assistenza e inviare file di log con il numero di richiesta di assistenza assegnato, come mostrato di seguito:
2. Scegliere **Registrazione** dal menu a comparsa.
3. Se è già stata aperta una richiesta di assistenza al Supporto Microsoft, prendere nota del numero corrispondente (sarà necessario in un passaggio successivo). Se non è ancora stata creata una richiesta di assistenza e si vuole ricevere assistenza, seguire le indicazioni del [Supporto Microsoft](https://support.microsoft.com/it-IT/contactus) per creare una nuova richiesta di assistenza.
4. Nella schermata di registrazione fare clic su **Invia ora**.
5. Selezionare il provider di posta elettronica da usare.
7. Se è già stata inviata una richiesta di assistenza al Supporto Microsoft, contattare il tecnico assegnato al problema in questione per scoprire come inviare i dati del log e fare in modo che vengano associati all'evento imprevisto segnalato. Il tecnico del supporto fornirà le informazioni relative all'indirizzo di posta elettronica e all'oggetto del messaggio. Se non è ancora stata creata una richiesta di assistenza, seguire le indicazioni del Supporto Microsoft per creare una nuova richiesta di assistenza.
9. Modificare la riga **A** e **Oggetto** con le informazioni ricevute dal Supporto Microsoft.
10. L'app Azure Authenticator allega il file di log al messaggio di posta elettronica da inviare. Descrivere il problema che si è verificato, aggiornare l'elenco di destinatari, se necessario, quindi inviare il messaggio di posta elettronica.

### Eliminazione dell'account aziendale e uscita dall'area di lavoro

È possibile rimuovere in qualsiasi momento l'account aziendale creato seguendo questa procedura:

**Per eliminare l'account aziendale dal menu Impostazioni**

1. Nello strumento di gestione degli account selezionare **Account aziendale**.
2. In **Impostazioni generali** nella schermata Account aziendale selezionare **Impostazioni account – Esci dalla rete dell'area di lavoro**.
3. Selezionare **Esci** nella schermata **Aggiunta all'area di lavoro**.
4. Fare clic su **OK** quando viene visualizzato il messaggio "Uscire dall'area di lavoro?".
5. In questo modo si garantisce l'eliminazione dell'account aziendale dall'area di lavoro.

>[AZURE.NOTE> È consigliabile non usare l'opzione Rimuovi account per eliminare un account aziendale perché questa opzione potrebbe non funzionare in alcune versioni precedenti di Android.

##Disinstallazione dell'app

Su un dispositivo Samsung Android, prima di disinstallare l'app Azure Authenticator è necessario rimuovere i privilegi di amministratore del dispositivo nel modo seguente. Nel menu **Impostazioni**, in **Sistema**, selezionare **Sicurezza**. 2. In **Amministratore dispositivo** scegliere **Amministratori dispositivo**. Assicurarsi che la casella di controllo accanto ad **Azure Authenticator** sia deselezionata.

##Risoluzione dei problemi

Se viene visualizzato l'errore relativo all'**archivio delle chiavi**, la schermata di blocco potrebbe non essere configurata con un PIN. Per risolvere il problema, disinstallare l'applicazione Azure Authenticator, configurare un PIN per la schermata di blocco e reinstallare l'app.

<!---HONumber=AcomDC_1203_2015-->