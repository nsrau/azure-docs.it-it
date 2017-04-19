---
title: Configurare la verifica in due passaggi per l&quot;account aziendale o dell&quot;istituto di istruzione | Documentazione Microsoft
description: "Quando l&quot;azienda configura Azure Multi-Factor Authentication, verrà chiesto di effettuare l&quot;iscrizione per la verifica in due passaggi. Informazioni su come eseguire la configurazione. "
services: multi-factor-authentication
keywords: come usare directory di azure, active directory nel cloud, esercitazione di active directory
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: kgremban
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 4ff028f88e984f28bc0f4a228aabed1fabc90560
ms.openlocfilehash: 6dafec65b2e8091954cae39994d0013d9fd06558
ms.lasthandoff: 12/22/2016

---
# <a name="set-up-my-account-for-two-step-verification"></a>Configurare l'account per la verifica in due passaggi
La verifica in due passaggi è un livello di sicurezza aggiuntiva che consente di proteggere l'account rendendo più difficile l'intromissione di altri utenti. Se si sta leggendo questo articolo, probabilmente è stato ricevuto un messaggio di posta elettronica su Multi-Factor Authentication inviato dall'amministratore dell'azienda o dell'istituto di istruzione. In alternativa, si è tentato di eseguire l'accesso ed è stato ricevuto un messaggio in cui viene chiesto di configurare una verifica aggiuntiva di sicurezza. In questo caso, **non è possibile accedere fino al completamento del processo di registrazione automatica**.

Questo articolo consente di configurare l'**account aziendale o dell'istituto di istruzione**. Se si vuole abilitare la verifica in due passaggi di un account Microsoft personale, vedere [Informazioni sulla verifica in due passaggi](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="determine-how-you-will-use-multi-factor-authentication"></a>Scelta della modalità di utilizzo dell’autenticazione a più fattori
La verifica in due passaggi richiede due tipi di identificazione all'accesso. Prima verranno chiesti il nome utente e la password, secondo la procedura consueta. Quindi si verrà contattati al numero di telefono indicato per verificare la validità del tentativo di accesso.  

Per iniziare il processo di configurazione, provare ad accedere all'account personale seguendo la procedura consueta. Se l'amministratore ha configurato l'account per la verifica in due passaggi, verrà chiesto di avviare il processo di registrazione automatica. Iniziare il processo facendo clic su **Configuralo subito**

![Configurazione](./media/multi-factor-authentication-end-user-first-time/first.png)

La prima domanda posta nel processo di registrazione è relativa alla modalità di contatto desiderata. Esaminare le opzioni nella tabella e passare alla procedura di configurazione per ogni metodo tramite i collegamenti.

| Metodo di contatto | Description |
| --- | --- |
| [App per dispositivi mobili](#use-a-mobile-app-as-the-contact-method) |- **Ricevi notifiche per la verifica.** Questa opzione invia una notifica all'app di autenticazione sul tablet o lo smartphone. Visualizzare la notifica e, se del caso, selezionare **Autentica** nell'app. L'azienda o dell'istituto di istruzione potrebbe richiedere di immettere un PIN prima eseguire l'autenticazione.<br>- **Usare il codice di verifica.** In questa modalità, l'app di autenticazione genera un nuovo codice di verifica ogni 30 secondi. Immettere il codice di verifica più recente nell'interfaccia di accesso.<br>L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| [Chiamata o SMS sul telefono cellulare](#use-your-mobile-phone-as-the-contact-method) |- **Chiamata telefonica** invia una chiamata vocale automatizzata al numero di telefono fornito. Per l'autenticazione, rispondere alla chiamata e premere # sul tastierino telefonico.<br>- **SMS** invia un SNS contenente un codice di verifica. Seguendo la richiesta indicata nel testo, rispondere all'SMS o immettere il codice di verifica fornito nell'interfaccia di accesso. |
| [Chiamata telefonica dell'ufficio](#use-your-office-phone-as-the-contact-method) |Invia una chiamata vocale automatizzata al numero di telefono fornito. Per l'autenticazione, rispondere alla chiamata e premere # sul tastierino telefonico. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Usare un'app per dispositivi mobili come metodo di contatto
L'uso di questo metodo richiede l'installazione di un'app di autenticazione sul telefono o sul tablet. I passaggi in questo articolo sono basati sull'app Microsoft Authenticator, disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Selezionare **App per dispositivi mobili** dall'elenco a discesa.
2. Selezionare l'opzione **Ricevi notifiche per la verifica** o **Use verification code** (Usa codice di verifica), quindi selezionare **Configura**.

    ![Schermata della verifica aggiuntiva di sicurezza](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)
3. Sul telefono o sul tablet aprire l'app e selezionare **+** per aggiungere un account. Sui dispositivi Android selezionare i tre puntini, quindi **Aggiungi account**.
4. Specificare che si desidera aggiungere un account aziendale o dell'istituto di istruzione. Viene aperto lo scanner di codici a matrice sul telefono. Se la fotocamera non funziona correttamente, è possibile selezionare di inserire manualmente le informazioni della società. Per ulteriori informazioni, vedere l'articolo su come [aggiungere manualmente un account](#add-an-account-manually).  
5. Eseguire la scansione dell'immagine del codice QR visualizzata nella schermata di configurazione dell'app mobile.  Fare clic su **Done** (Fine) per chiudere la schermata del codice QR.  

    ![Schermata di codice QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)
6. Una volta completata l'attivazione sul telefono, selezionare **Contact me**(Contattami).  Viene inviata una notifica o un codice di verifica al telefono cellulare. Selezionare **Verifica**.  
7. Se richiesto dall'azienda, immettere il PIN per approvare la verifica di accesso.

    ![Casella per l'immissione di un PIN](./media/multi-factor-authentication-end-user-first-time/scan3.png)
8. Al termine dell'immissione del PIN, selezionare **Chiudi**. A questo punto, la verifica avrà esito positivo.
9. Si consiglia di immettere il proprio numero di telefono cellulare, nel caso in cui si perda l'accesso all'app per dispositivi mobili. Specificare il paese dall'elenco a discesa e immettere il numero di telefono cellulare nella casella accanto al paese. Selezionare **Avanti**.
10. A questo punto viene chiesto di configurare le password dell'app per le app che non sono browser, come Outlook 2010 o versioni precedenti, o per l'app di posta elettronica nativa nei dispositivi Apple. Alcune app non supportano la verifica in due passaggi. Se non si usano queste app, fare clic su **Fine** e ignorare i passaggi successivi.
11. Se si usano queste app, copiare la password dell'app fornita e incollare la password nell'applicazione, al posto della password regolare. È possibile usare la stessa password dell'app per più app. Per altre informazioni, vedere [Guida alle password per le app].
12. Fare clic su **Done**.

### <a name="add-an-account-manually"></a>aggiungere manualmente un account
Se si vuole aggiungere manualmente un account all'app per dispositivi mobili, anziché usare il lettore di codici a matrice, seguire questi passaggi.

1. Fare clic sul pulsante **Enter account manually** (Immetti account manualmente).  
2. Immettere il codice e l'URL riportati nella stessa pagina in cui era contenuto il codice a barre. Queste informazioni devono essere inserite nelle caselle **Code** (Codice) e **URL** dell'app per dispositivi mobili.

    ![Configurazione](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Al termine dell'attivazione, selezionare **Contact me**(Contattami). Viene inviata una notifica o un codice di verifica al telefono cellulare. Selezionare **Verifica**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Usare il telefono cellulare come metodo di contatto
1. Selezionare **Telefono per l'autenticazione** dall'elenco a discesa.  

    ![Configurazione](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Scegliere il paese dall'elenco a discesa e immettere il numero di telefono cellulare.
3. Selezionare il metodo preferito da usare con il telefono cellulare: SMS o telefonata.
4. Selezionare **Contattami** per verificare il numero di telefono. In base alla modalità selezionata si riceverà un SMS o una telefonata. Seguire le istruzioni visualizzate, quindi selezionare **Verifica**.
5. A questo punto viene chiesto di configurare le password dell'app per le app che non sono browser, come Outlook 2010 o versioni precedenti, o per l'app di posta elettronica nativa nei dispositivi Apple. Alcune app non supportano la verifica in due passaggi. Se non si usano queste app, fare clic su **Fine** e ignorare i passaggi successivi.
6. Se si usano queste app, copiare la password dell'app fornita e incollare la password nell'applicazione, al posto della password regolare. È possibile usare la stessa password dell'app per più app. Per altre informazioni, vedere [Guida alle password per le app].
7. Fare clic su **Done**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Usare il telefono dell'ufficio come metodo di contatto
1. Selezionare **Telefono ufficio** dall'elenco a discesa  

    ![Configurazione](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. Nella casella del numero di telefono vengono inserite automaticamente le informazioni di contatto dell'azienda. Se il numero è errato o manca, chiedere all'amministratore di apportare le modifiche appropriate.
3. Selezionare **Contattami** per confermare il numero di telefono; si riceverà una telefonata a questo numero. Seguire le istruzioni visualizzate, quindi selezionare **Verifica**.
4. A questo punto viene chiesto di configurare le password dell'app per le app che non sono browser, come Outlook 2010 o versioni precedenti, o per l'app di posta elettronica nativa nei dispositivi Apple. Alcune app non supportano la verifica in due passaggi. Se non si usano queste app, fare clic su **Fine** e ignorare i passaggi successivi.
5. Se si usano queste app, copiare la password dell'app fornita e incollare la password nell'applicazione, al posto della password regolare. È possibile usare la stessa password dell'app per più app. Per altre informazioni, vedere [Che cosa sono le password per le app?](multi-factor-authentication-end-user-app-passwords.md).
6. Fare clic su **Done**.

## <a name="next-steps"></a>Passaggi successivi
* Modificare le opzioni desiderate e [gestire le impostazioni per la verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md)
* Impostare [password di app](multi-factor-authentication-end-user-app-passwords.md) per tutte le app di dispositivo native che non supportano la verifica in due passaggi.
* Estrarre l'[app Microsoft Authenticator](microsoft-authenticator-app-how-to.md) per eseguire un'autenticazione sicura e veloce, anche in assenza di copertura di rete.

