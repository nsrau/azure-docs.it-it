---
title: Azure Authenticator per Android | Documentazione Microsoft
description: "L&quot;app Microsoft Azure Authenticator può essere usata per eseguire l&quot;accesso alle risorse di lavoro. L&quot;app Azure Authenticator segnala eventuali richieste di verifica a due fattori in sospeso con la visualizzazione di un avviso nel dispositivo mobile."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
ms.assetid: b7ceca0d-5c9d-45c4-942c-b3a9b6bad36c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: femila
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 5dd6701f25c69f5e870d3add79c312f9aeec6bf4


---
# <a name="azure-authenticator-for-android"></a>Azure Authenticator per Android
L'amministratore IT può consigliare di usare Microsoft Azure Authenticator per eseguire l'accesso alle risorse di lavoro. Questa applicazione fornisce due opzioni di accesso:

* Multi-Factor Authentication permette di proteggere gli account aziendali o dell'istituto di istruzione con una verifica in due passaggi. Si effettua l'accesso usando un elemento noto, ad esempio la password, e si protegge ulteriormente l'account usando un elemento disponibile, ad esempio una chiave di sicurezza fornita dall'app. L'app Azure Authenticator segnala eventuali richieste di verifica a due fattori in sospeso con la visualizzazione di un avviso nel dispositivo mobile. È sufficiente visualizzare la richiesta nell'app e toccare Verifica o Annulla. In alternativa, può essere richiesta l'immissione del passcode visualizzato nell'app.
* L'account aziendale permette di trasformare il telefono o il tablet Android in un dispositivo attendibile e di fornire l'accesso Single Sign-On (SSO) alle applicazioni aziendali. L'amministratore IT può richiedere l'aggiunta di un account aziendale per accedere alle risorse aziendali. SSO permette di effettuare l'accesso una volta sola e di accedere automaticamente a tutte le applicazioni rese disponibili dall'azienda.

## <a name="installing-the-azure-authenticator-app"></a>Installazione dell'app Azure Authenticator
È possibile installare l'app Azure Authenticator da Google Play Store.
La procedura per aggiungere un account aziendale da un dispositivo Samsung Android e da un dispositivo non Samsung Android è leggermente diversa. Di seguito sono fornite le istruzioni per entrambi i casi.

## <a name="adding-the-work-account-from-samsung-android-device"></a>Aggiunta dell'account aziendale da un dispositivo Samsung Android
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Aggiunta dell'account aziendale nella schermata iniziale dell'app
Le istruzioni seguenti sono applicabili a telefoni Samsung GS3 e modelli superiori o tablet Note2 e modelli superiori.

1. Nella schermata iniziale dell'app accettare il Contratto di Licenza con l'utente finale.
2. Nella schermata Attiva account fare clic sul menu di scelta rapida a destra, quindi selezionare **Account aziendale**.
3. Nella schermata Aggiungi account selezionare** Account aziendale**.
4. Nella schermata relativa all'attivazione dell'amministratore del dispositivo fare clic su **Attiva**.
5. Nella schermata Informativa sulla privacy selezionare la casella di controllo e quindi fare clic su **Conferma**.
6. Nella schermata Aggiunta all'area di lavoro immettere l'ID utente fornito dall'organizzazione, quindi fare clic su **Aggiungi**.
7. Per accedere all'app Azure Authenticator, immettere l'****account aziendale e la password, quindi fare clic su **Accedi**.
8. La schermata successiva che mostra le informazioni relative all'autenticazione a più fattori (MFA) viene presentata per maggiore sicurezza ed è facoltativa. Questa schermata viene visualizzata se l'azienda o l'istituto di istruzione richiede l'autenticazione basata su un secondo fattore per la creazione di account aziendali e include istruzioni per verificare ulteriormente l'account.
9. Nella schermata Workplace Join viene visualizzato il messaggio "**Aggiunta all'area di lavoro in corso**". L'app Azure Authenticator sta provando ad aggiungere il dispositivo all'area di lavoro.
10. Nella schermata successiva dovrebbe essere visualizzato il messaggio di completamento dell'aggiunta all'area di lavoro.

> [!NOTE]
> Nel dispositivo può essere presente un solo account aziendale.
> 
> 

### <a name="adding-the-work-account-from-the-settings-menu"></a>Aggiunta dell'account aziendale dal menu Impostazioni
Dopo l'installazione dell'app Azure Authenticator, è anche possibile creare un account aziendale da Android Account Manager.

1. Nel menu Impostazioni passare ad **Account** e fare clic su **Aggiungi account**.
2. Seguire i passaggi 3-10 della procedura Aggiunta dell'account aziendale nella schermata iniziale dell'app per aggiungere un account aziendale.

## <a name="adding-the-work-account-from-a-non-samsung-android-device"></a>Aggiunta dell'account aziendale da un dispositivo non Samsung Android
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Aggiunta dell'account aziendale nella schermata iniziale dell'app
1. Nella schermata iniziale dell'app accettare il Contratto di Licenza con l'utente finale.
2. Nella schermata Attiva account fare clic sul menu di scelta rapida a destra, quindi selezionare **Account aziendale**.
3. Nella schermata Account scegliere **Aggiungi account**.
4. Se viene visualizzata la schermata account, fare clic su **Aggiungi account**. Se in precedenza è già stato creato un account aziendale, verrà visualizzata la schermata Sincronizzazione con l'account aziendale esistente. Per mantenere l'account aziendale, è sufficiente toccare la freccia indietro per tornare alla schermata iniziale. In alternativa, è possibile selezionare l'account da rimuovere e ricreare un nuovo account aziendale. Nella schermata Aggiunta all'area di lavoro immettere l'ID utente fornito dall'organizzazione, quindi fare clic su Aggiungi.
5. Per accedere all'app Azure Authenticator, immettere l'account aziendale e la password, quindi fare clic su **Accedi**.
6. La schermata successiva che mostra le informazioni relative all'autenticazione a più fattori (MFA) viene presentata per maggiore sicurezza ed è facoltativa. Questa schermata viene visualizzata se l'azienda o l'istituto di istruzione richiede l'autenticazione basata su un secondo fattore per la creazione di account aziendali e include istruzioni per verificare ulteriormente l'account.
7. Fare clic su **OK** nella schermata successiva. Non cambiare il nome del certificato.
   Nella schermata Aggiunta all'area di lavoro viene visualizzato il messaggio "Aggiunta all'area di lavoro in corso". L'app Azure Authenticator sta provando ad aggiungere il dispositivo all'area di lavoro.
   Nella schermata successiva dovrebbe essere visualizzato il messaggio di completamento dell'aggiunta all'area di lavoro.

> [!NOTE]
> Nel dispositivo può essere presente un solo account aziendale.
> 
> 

Dopo l'installazione dell'app Azure Authenticator, è anche possibile creare un account aziendale da Android Account Manager.

1. Nel menu **Impostazioni** passare a Account e fare clic su **Aggiungi account**.
2. Seguire i passaggi 2-7 della procedura Aggiunta dell'account aziendale nella schermata** iniziale dell'app per aggiungere un account aziendale.

### <a name="how-to-find-out-which-version-is-installed"></a>Come verificare la versione installata
1. È possibile scoprire la versione dell'app Azure Authenticator e dei servizi associati installati nel dispositivo.
2. Scegliere **Informazioni su**dal menu a comparsa.
3. Nella schermata Informazioni su sono visualizzati i servizi dell'app e le versioni installate nel dispositivo.

### <a name="sending-log-files-to-report-issues"></a>Invio di file di log per segnalare problemi
1. Seguire le istruzioni sul sito del Supporto Microsoft per segnalare un evento imprevisto con l'app Azure Authenticator, ottenere un numero di richiesta di assistenza e inviare file di log con il numero di richiesta di assistenza assegnato, come mostrato di seguito:
2. Scegliere **Registrazione**dal menu a comparsa.
3. Se è già stata aperta una richiesta di assistenza al Supporto Microsoft, prendere nota del numero corrispondente (sarà necessario in un passaggio successivo). Se non è ancora stata creata una richiesta di assistenza e si vuole ricevere assistenza, seguire le indicazioni del [Supporto Microsoft](https://support.microsoft.com/en-us/contactus) per creare una nuova richiesta di assistenza.
4. Nella schermata di registrazione fare clic su **Invia ora**.
5. Selezionare il provider di posta elettronica da usare.
6. Se è già stata inviata una richiesta di assistenza al Supporto Microsoft, contattare il tecnico assegnato al problema in questione per scoprire come inviare i dati del log e fare in modo che vengano associati all'evento imprevisto segnalato. Il tecnico del supporto fornirà le informazioni relative all'indirizzo di posta elettronica e all'oggetto del messaggio. Se non è ancora stata creata una richiesta di assistenza, seguire le indicazioni del Supporto Microsoft per creare una nuova richiesta di assistenza.
7. Modificare la riga **A** e **Oggetto** con le informazioni ricevute dal Supporto Microsoft.
8. L'app Azure Authenticator allega il file di log al messaggio di posta elettronica da inviare. Descrivere il problema che si è verificato, aggiornare l'elenco di destinatari, se necessario, quindi inviare il messaggio di posta elettronica.

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>Eliminazione dell'account aziendale e uscita dall'area di lavoro
È possibile rimuovere in qualsiasi momento l'account aziendale creato seguendo questa procedura:

**Per eliminare l'account aziendale dal menu Impostazioni**

1. Nello strumento di gestione degli account selezionare **Account aziendale**.
2. In **Impostazioni generali** nella schermata Account aziendale selezionare **Impostazioni account – Esci dalla rete dell'area di lavoro**.
3. Selezionare **Esci** nella schermata **Aggiunta all'area di lavoro**.
4. Fare clic su **OK** quando viene visualizzato il messaggio "Uscire dall'area di lavoro?".
5. In questo modo si garantisce l'eliminazione dell'account aziendale dall'area di lavoro.

> [!NOTE]
> È consigliabile non usare l'opzione Rimuovi account per eliminare un account aziendale perché questa opzione potrebbe non funzionare in alcune versioni precedenti di Android.
> 
> 

## <a name="uninstalling-the-app"></a>Disinstallazione dell'app
Su un dispositivo Samsung Android, è necessario rimuovere i privilegi di amministratore del dispositivo nel modo seguente prima di disinstallare 

1. Nel menu **Impostazioni**, in **Sistema**, selezionare **Sicurezza**.
2. In **Amministrazione dispositivo** scegliere **Amministratori del dispositivo**. Assicurarsi che la casella di controllo accanto ad **Azure Authenticator** sia deselezionata.

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se viene visualizzato l'errore relativo all'**archivio delle chiavi**, la schermata di blocco potrebbe non essere configurata con un PIN. Per risolvere il problema, disinstallare l'applicazione Azure Authenticator, configurare un PIN per la schermata di blocco e reinstallare l'app.




<!--HONumber=Feb17_HO3-->


