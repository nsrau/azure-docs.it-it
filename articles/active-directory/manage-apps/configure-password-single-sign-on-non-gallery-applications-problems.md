---
title: Problemi durante la configurazione della password SSO per app non della raccolta
description: Problemi comuni che si verificano quando si configura l'accesso Single Sign-On (SSO) della password per le app personalizzate che non si trovano nella raccolta di applicazioni di Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed8bafe7f5bc28cf37205107f8ab6dd5cdb4907c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274139"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problemi durante la configurazione dell'accesso Single Sign-On con password per un'applicazione non di raccolta

In questo articolo vengono descritti i problemi comuni che possono verificarsi quando si configura *l'accesso Single Sign-On* (SSO) password per un'app non di raccolta.

## <a name="capture-sign-in-fields-for-an-app"></a>Acquisire campi di accesso per un'app

L'acquisizione dei campi di accesso è supportata solo per le pagine di accesso abilitate per HTML. Non è supportato per le pagine di accesso non standard, come quelle che utilizzano Adobe Flash o altre tecnologie non abilitate per HTML.

Esistono due modi per acquisire i campi di accesso per le app personalizzate:

- **L'acquisizione automatica** dei campi di accesso funziona bene con la maggior parte delle pagine di accesso abilitate per HTML, *se utilizzano ID DIV noti* per i campi nome utente e password. Il codice HTML nella pagina viene raschiato per trovare ID DIV che corrispondono a determinati criteri. Tali metadati vengono salvati in modo che possano essere riprodotti nell'app in un secondo momento.

- **L'acquisizione manuale** del campo di accesso viene utilizzata se il fornitore dell'app non etichetta i campi di input di *accesso.* L'acquisizione manuale viene utilizzata anche se il fornitore esegue il rendering di *più campi che non possono essere rilevati automaticamente.* Azure Active Directory (Azure AD) può archiviare i dati per tutti i campi disponibili nella pagina di accesso, indicando la posizione di tali campi nella pagina.

In generale, se l'acquisizione automatica del campo di accesso non funziona, prova l'opzione manuale.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Acquisire automaticamente i campi di accesso per un'app

Per configurare SSO basato su password utilizzando l'acquisizione automatica del campo di accesso, attenersi alla seguente procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/). Accedere come amministratore globale o coamministratore.

2. Nel riquadro di spostamento sul lato sinistro selezionare **Tutti i servizi** per aprire l'estensione di Azure AD.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare Azure Active **Directory**.

4. Selezionare **Applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se l'app desiderata non è visualizzata, usare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni.** Impostare l'opzione **Mostra su** "Tutte le applicazioni".

6. Selezionare l'app che si desidera configurare per SSO.

7. Dopo il caricamento dell'app, seleziona **Single Sign-On** nel riquadro di spostamento sul lato sinistro.

8. Selezionare Modalità **di accesso basata su password.**

9. Immettere **l'URL di accesso,** ovvero l'URL della pagina in cui gli utenti immettono il nome utente e la password per accedere. *Assicurarsi che i campi di accesso siano visibili nella pagina per l'URL fornito.*

10. Selezionare **Salva**.

    La pagina viene automaticamente raschiata per le caselle di immissione del nome utente e della password. È ora possibile usare Azure AD per trasmettere in modo sicuro le password all'app usando l'estensione del browser Pannello di accesso.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Acquisire manualmente i campi di accesso per un'app

Per acquisire manualmente i campi di accesso, è necessario che sia installata l'estensione del browser Pannello di accesso. Inoltre, il browser non può essere in esecuzione in modalità *inPrivate*, *in incognito*o *privata.*

Per installare l'estensione, vedere la sezione [Installare l'estensione browser del pannello](#install-the-access-panel-browser-extension) di accesso di questo articolo.

Per configurare SSO basato su password per un'app utilizzando l'acquisizione manuale del campo di accesso, attenersi alla seguente procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/). Accedere come amministratore globale o coamministratore.

2. Nel riquadro di spostamento sul lato sinistro selezionare **Tutti i servizi** per aprire l'estensione di Azure AD.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare Azure Active **Directory**.

4. Selezionare **Applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE] 
   > Se l'app desiderata non è visualizzata, usare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni.** Impostare l'opzione **Mostra su** "Tutte le applicazioni".

6. Selezionare l'app che si desidera configurare per SSO.

7. Dopo il caricamento dell'app, seleziona **Single Sign-On** nel riquadro di spostamento sul lato sinistro.

8. Selezionare Modalità **di accesso basata su password.**

9. Immettere **l'URL di accesso,** ovvero la pagina in cui gli utenti immettono il nome utente e la password per accedere. *Assicurarsi che i campi di accesso siano visibili nella pagina per l'URL fornito.*

10. Selezionare **Configura * &lt;&gt; * impostazioni Single Sign-On password nomeapp**.

11. Selezionare **Rileva manualmente i campi di accesso**.

14. Selezionare **Ok**.

15. Selezionare **Salva**.

16. Seguire le istruzioni per utilizzare il pannello di accesso.

## <a name="troubleshoot-problems"></a>Risoluzione dei problemi

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Viene visualizzato l'errore "Non è stato possibile trovare campi di accesso in tale URL"

Questo messaggio di errore viene visualizzato quando il rilevamento automatico dei campi di accesso non riesce. Per risolvere il problema, provare il rilevamento manuale dei campi di accesso. Vedere la sezione Acquisire manualmente i campi di [accesso per un'applicazione](#manually-capture-sign-in-fields-for-an-app) di questo articolo.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Viene visualizzato un errore "Impossibile salvare la configurazione Single Sign-On"

Raramente, l'aggiornamento della configurazione SSO ha esito negativo. Per risolvere il problema, provare a salvare nuovamente la configurazione.

Se continui a ricevere l'errore, apri un caso di supporto. Includere le informazioni descritte in [Visualizzare i dettagli](#view-portal-notification-details) delle notifiche del portale e [Inviare i dettagli](#send-notification-details-to-a-support-engineer-to-get-help) della notifica a un tecnico del supporto per ottenere le sezioni della Guida di questo articolo.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Non riesco a rilevare manualmente i campi di accesso per la mia app

Quando il rilevamento manuale non funziona, è possibile che si verifichino i seguenti comportamenti:

- Il processo di acquisizione manuale sembrava funzionare, ma i campi acquisiti non sono corretti.

- I campi corretti non vengono evidenziati durante l'esecuzione del processo di acquisizione.

- Il processo di acquisizione consente di accedere alla pagina di accesso dell'app come previsto, ma non accade nulla.

- L'acquisizione manuale sembra funzionare, ma SSO non si verifica quando gli utenti accedono all'app dal pannello di accesso.

Se si verifica uno di questi problemi, eseguire le operazioni seguenti:

- Assicurarsi di avere *installato e abilitato*l'estensione del browser Pannello di accesso . Vedere la sezione [Installare l'estensione del browser del pannello](#install-the-access-panel-browser-extension) di accesso di questo articolo.

- Assicurarsi che il browser non sia in modalità *in incognito*, *inPrivate*o *Privata* durante il processo di acquisizione. L'estensione Pannello di accesso non è supportata in queste modalità.

- Assicurati che gli utenti non stiano tentando di accedere all'app dal Pannello di accesso mentre sono in modalità *in incognito,* *inPrivate*o *Private.*

- Riprovare il processo di acquisizione manuale. Assicurarsi che gli indicatori rossi si trovino sopra i campi corretti.

- Se il processo di acquisizione manuale sembra bloccarsi o la pagina di accesso non risponde, provare di nuovo il processo di acquisizione manuale. Ma questa volta, dopo aver completato il processo, premere il tasto F12 per aprire la console di sviluppo del browser. Selezionare la scheda **della console.** Digitare **window.location""*&lt;l'URL&gt;di accesso specificato durante la configurazione dell'app*"**, quindi premere INVIO. In questo modo viene forzato un reindirizzamento di pagina che termina il processo di acquisizione e archivia i campi acquisiti.

### <a name="contact-support"></a>Contattare il supporto tecnico

Se i problemi persistono, aprire un caso con il supporto tecnico Microsoft. Descrivi quello che hai provato. Includere i dettagli descritti in Visualizza i dettagli della [notifica del portale](#view-portal-notification-details) e Invia dettagli notifica a un tecnico del supporto tecnico per ottenere le sezioni della [Guida](#send-notification-details-to-a-support-engineer-to-get-help) di questo articolo (se applicabile).

## <a name="install-the-access-panel-browser-extension"></a>Installare l'estensione del browser Pannello di accesso

A tale scopo, seguire questa procedura:

1. Aprire [il pannello di accesso](https://myapps.microsoft.com) in un browser supportato. Accedere ad Azure AD come *utente.*

2. Selezionare **l'applicazione password-SSO** nel pannello di accesso.

3. Quando viene richiesto di installare il software, selezionare **Installa ora**.

4. Verrai indirizzato a una pagina di download per il tuo browser. Scegliere **Aggiungi** l'estensione.

5. Se richiesto, selezionare **Abilita** o **Consenti**.

6. Dopo l'installazione, riavviare il browser.

7. Accedere al Pannello di accesso. Verifica se riesci ad aprire le app abilitate per SSO con password.

Puoi anche scaricare direttamente l'estensione del browser per Chrome e Firefox tramite questi link:

-   [Estensione del pannello di accesso Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Estensione Firefox Access Panel](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Visualizzare i dettagli delle notifiche del portale

Per visualizzare i dettagli di qualsiasi notifica del portale, attenersi alla seguente procedura:

1. Selezionare l'icona Notifiche (la campana) nell'angolo superiore destro del portale di Azure.Select the **Notifications** icon (the bell) in the upper-right corner of the Azure portal.

2. Selezionare qualsiasi notifica che mostra uno stato *di errore.* (Hanno un "!".) rosso

   > [!NOTE]
   > Non è possibile selezionare le notifiche che si trovano nello stato *Riuscito* o *In corso.*

3. Viene aperto il riquadro **Dettagli notifica**. Leggere le informazioni per informazioni sul problema.

5. Se hai ancora bisogno di aiuto, condividi le informazioni con un tecnico del supporto o con il gruppo di prodotti. Selezionare l'icona di **copia** a destra della casella **di errore Copia** per copiare i dettagli della notifica da condividere.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Inviare i dettagli della notifica a un tecnico del supporto per ottenere assistenza

È importante condividere *tutti i* dettagli elencati in questa sezione con il supporto in modo che possano aiutarti rapidamente. Per registrarlo, è possibile acquisire uno screenshot o selezionare **Copia errore**.

Le informazioni seguenti illustrano il significato di ogni elemento di notifica e forniscono esempi.

### <a name="essential-notification-items"></a>Elementi di notifica essenziali

- **Titolo**: il titolo descrittivo della notifica.

   Esempio: *impostazioni proxy dell'applicazione*

- **Descrizione**: cosa si è verificato a seguito dell'operazione.

   Esempio: *l'URL interno immesso è già utilizzato da un'altra applicazione.*

- **ID notifica**: l'ID univoco della notifica.

    Esempio: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID richiesta client**: l'ID richiesta specifico effettuato dal browser.

    Esempio: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Time Stamp UTC**: il timestamp di quando si è verificata la notifica, in UTC.

    Esempio: *2017-03-23T19:50:43.7583681*

- **ID transazione interno**: l'ID interno utilizzato per cercare l'errore nei nostri sistemi.

    Esempio: **71a2f329-ca29-402f-aa72-bc00a7a603**

- **UPN**: L'utente che ha eseguito l'operazione.

    Esempio: *f128.info tperkins\@*

- **ID tenant:** l'ID univoco del tenant di cui è membro l'utente che ha eseguito l'operazione.

    Esempio: *7918d4b5-0442-4a97-be2d-36f9962ece*

- **ID oggetto utente**: l'ID univoco dell'utente che ha eseguito l'operazione.

    Esempio: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Elementi di notifica dettagliati

- **Nome visualizzato**: (può essere vuoto) un nome visualizzato più dettagliato per l'errore.

    Esempio: *impostazioni proxy dell'applicazione*

- **Status**: lo stato specifico della notifica.

    Esempio: *non riuscitoExample: Failed*

- **ID oggetto**: (può essere vuoto) l'ID oggetto su cui è stata eseguita l'operazione.

   Esempio: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Dettagli**: la descrizione dettagliata di ciò che si è verificato a seguito dell'operazione.

    Esempio: *URL<https://bing.com/>interno ' non è valido perché è già in uso.*

- **Copia errore**: Consente di selezionare l'icona di **copia** a destra della casella di testo **Copia errore** per copiare i dettagli della notifica per facilitare il supporto.

    Esempio:```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md)
