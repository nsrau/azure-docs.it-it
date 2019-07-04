---
title: Problemi di configurazione SSO basato su password per un'applicazione non inclusa nella raccolta | Microsoft Docs
description: Problemi comuni che si verificano quando si configura la password single sign-on (SSO) per le app personalizzate che non sono nella raccolta di applicazioni Azure AD.
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
ms.openlocfilehash: 24330dc874173ba1c6f15abb7b4caf9f23e2e00c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440346"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problemi di configurazione password single sign-on per un'applicazione non inclusa nella raccolta

Questo articolo descrive i problemi comuni che possono verificarsi quando si configurano *single sign-on basato su password* (SSO) per un'app non inclusa nella raccolta.

## <a name="capture-sign-in-fields-for-an-app"></a>Acquisire i campi di accesso per un'app

Acquisizione dei campi di accesso è supportata solo per basate su HTML delle pagine di accesso. Non è supportato per non standard delle pagine di accesso, come quelli che utilizzano Adobe Flash o altre tecnologie non abilitato per HTML.

Esistono due modi per acquisire i campi di accesso per le app personalizzate:

- **Acquisizione automatica dei campi Accedi** funziona bene con la maggior parte delle basate su HTML delle pagine di accesso, *se si avvalgono di ID DIV noti* per i campi nome e una password utente. Il codice HTML della pagina è scorporato per ricerca di ID DIV che soddisfano determinati criteri. Tali metadati viene salvato in modo che possono essere riprodotte in un secondo momento per l'app.

- **Acquisizione manuale dei campi Accedi** viene usato se il fornitore dell'app *non etichettare i campi di input Accedi*. Acquisizione manuale viene utilizzata anche se il fornitore *esegue il rendering di più campi che non possono essere rilevati automaticamente*. Azure Active Directory (Azure AD) può archiviare i dati per tanti campi quante sono disponibili nella pagina di accesso, se si indicano dove si trovano questi campi nella pagina.

In generale, se Acquisisci campi di accesso automatico non persiste, provare l'opzione manuale.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Acquisire automaticamente i campi di accesso per un'app

Per configurare l'accesso SSO basato su password tramite l'acquisizione automatica dei campi Accedi, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/). Accedere come un amministratore globale o un coamministratore.

2. Nel riquadro di spostamento a sinistra, selezionare **tutti i servizi** per aprire l'estensione Azure AD.

3. Tipo di **Azure Active Directory** nella casella di ricerca filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se non viene visualizzata l'app che vuole, usare il **filtro** nella parte superiore del controllo il **tutte le applicazioni** elenco. Impostare il **mostrare** opzione per "Tutte le applicazioni."

6. Selezionare l'app che si desidera configurare per SSO.

7. Una volta caricata l'app, selezionare **l'accesso Single sign-on** nel riquadro di spostamento a sinistra.

8. Selezionare **Password-based Sign-on** modalità.

9. Immettere il **Sign-on di URL**, ovvero l'URL della pagina in cui gli utenti immettono il nome utente e la password per l'accesso. *Assicurarsi che i campi di accesso siano visibili nella pagina per l'URL fornito*.

10. Selezionare **Salva**.

    La pagina verrà creata automaticamente per l'utente nome e una password caselle di input. È ora possibile usare Azure AD per trasmettere in modo sicuro le password per l'app usando l'estensione Pannello di accesso del browser.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Acquisire manualmente i campi di accesso per un'app

Per acquisire manualmente i campi di accesso, deve avere l'estensione Pannello di accesso del browser installato. Inoltre, il browser non può essere in esecuzione in *inPrivate*, *incognito*, o *private* modalità.

Per installare l'estensione, vedere la [installare l'estensione del Browser del pannello accesso](#install-the-access-panel-browser-extension) sezione di questo articolo.

Per configurare l'accesso SSO basato su password per un'app con acquisizione manuale dei campi Accedi, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/). Accedere come un amministratore globale o un coamministratore.

2. Nel riquadro di spostamento a sinistra, selezionare **tutti i servizi** per aprire l'estensione Azure AD.

3. Tipo di **Azure Active Directory** nella casella di ricerca filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE] 
   > Se non viene visualizzata l'app che vuole, usare il **filtro** nella parte superiore del controllo il **tutte le applicazioni** elenco. Impostare il **mostrare** opzione per "Tutte le applicazioni."

6. Selezionare l'app che si desidera configurare per SSO.

7. Una volta caricata l'app, selezionare **l'accesso Single sign-on** nel riquadro di spostamento a sinistra.

8. Selezionare **Password-based Sign-on** modalità.

9. Immettere il **Sign-on di URL**, ovvero la pagina in cui gli utenti immettono il nome utente e la password per l'accesso. *Assicurarsi che i campi di accesso siano visibili nella pagina per l'URL fornito*.

10. Selezionare **Configure *&lt;NomeApp&gt;* impostazioni Password Single Sign-on**.

11. Selezionare **rilevare manualmente i campi di accesso**.

14. Selezionare **accettabile**.

15. Selezionare **Salva**.

16. Seguire le istruzioni per utilizzare Pannello di accesso.

## <a name="troubleshoot-problems"></a>Risoluzione dei problemi

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Viene visualizzato un errore "Impossibile trovare tutti i campi di accesso a tale URL"

Viene visualizzato questo messaggio di errore quando il rilevamento automatico dei campi di accesso ha esito negativo. Per risolvere il problema, provare il rilevamento di campi di accesso manuali. Vedere le [acquisire manualmente i campi di accesso per un'applicazione](#manually-capture-sign-in-fields-for-an-app) sezione di questo articolo.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Viene visualizzato "Impossibile salvare la configurazione dell'accesso single sign-on" errore

Raramente, l'aggiornamento della configurazione di SSO avrà esito negativo. Per risolvere questo problema, provare a salvare di nuovo la configurazione.

Se si continua a ricevere l'errore, aprire un caso di supporto. Includere le informazioni che sono descritti nel [visualizzare i dettagli di notifica del portale](#view-portal-notification-details) e [inviare i dettagli di notifica a un tecnico del supporto per ottenere assistenza](#send-notification-details-to-a-support-engineer-to-get-help) sezioni di questo articolo.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>È possibile non è possibile rilevare manualmente i campi di accesso per l'app

Quando il rilevamento manuale non funziona, è possibile osservare i comportamenti seguenti:

- Il processo di acquisizione manuale sembrava funzionare, ma i campi acquisiti non sono corretti.

- I campi corretti non vengono evidenziati quando viene eseguito il processo di acquisizione.

- Il processo di acquisizione consente di visualizzare la pagina dell'app Accedi come previsto, ma non accade nulla.

- Acquisizione manuale sembra funzionare, ma non avviene l'accesso SSO quando gli utenti accedono all'app dal Pannello di accesso.

Se si verificano questi problemi, eseguire le operazioni seguenti:

- Assicurarsi di avere la versione più recente dell'estensione del browser del Pannello di accesso *installato e abilitato*. Vedere le [installare l'estensione Pannello di accesso del browser](#install-the-access-panel-browser-extension) sezione di questo articolo.

- Assicurarsi che il browser non è presente nel *incognito*, *inPrivate*, o *Private* modalità durante il processo di acquisizione. L'estensione Pannello di accesso non è supportata in queste modalità.

- Assicurarsi che gli utenti non sono provando ad accedere all'app dal Pannello di accesso mentre in *incognito*, *inPrivate*, o *modalità privata*.

- Provare di nuovo il processo di acquisizione manuale. Assicurarsi che gli indicatori rossi si trovino sui campi corretti.

- Se il processo di acquisizione manuale sembra bloccarsi o la pagina di accesso non invia alcuna risposta, riprovare il processo di acquisizione manuale. Ma questa volta, al termine del processo, premere il tasto F12 per aprire la console per sviluppatori del browser. Selezionare il **console** scheda. Tipo di **Window. Location = " *&lt;l'URL di accesso specificato durante la configurazione dell'app&gt;* "** , quindi premere INVIO. In tal modo un reindirizzamento della pagina che termina il processo di acquisizione e memorizza i campi che sono stati acquisiti.

### <a name="contact-support"></a>Contattare il supporto tecnico

Se continuano a verificarsi problemi, aprire un caso con il supporto tecnico Microsoft. Viene descritto ciò che si è cercato. Includere i dettagli descritti nel [visualizzare i dettagli di notifica del portale](#view-portal-notification-details) e [inviare i dettagli di notifica a un tecnico del supporto per ottenere assistenza](#send-notification-details-to-a-support-engineer-to-get-help) sezioni di questo articolo (se applicabile).

## <a name="install-the-access-panel-browser-extension"></a>Installare l'estensione Pannello di accesso del browser

A tale scopo, seguire questa procedura:

1. Aprire [Pannello di accesso](https://myapps.microsoft.com) in un browser supportato. Accedi ad Azure AD come una *utente*.

2. Selezionare **applicazione SSO basato su password** nel Pannello di accesso.

3. Quando viene chiesto di installare il software, selezionare **Installa ora**.

4. Si verrà indirizzati a una pagina di download del browser in uso. Desidera **Add** l'estensione.

5. Se richiesto, selezionare **abilitare** oppure **Consenti**.

6. Dopo l'installazione, riavviare il browser.

7. Accedere al pannello di accesso. Vedere se è possibile aprire le app abilitate per la password-SSO.

È possibile scaricare direttamente l'estensione del browser per Chrome e Firefox tramite i collegamenti seguenti:

-   [Estensione Pannello di accesso per Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Estensione Pannello di accesso di Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Visualizzare i dettagli di notifica del portale

Per visualizzare i dettagli di qualsiasi notifica del portale, seguire questa procedura:

1. Selezionare il **notifiche** icona (la campanella) nell'angolo superiore destro del portale di Azure.

2. Selezionare una notifica che mostra un *errore* dello stato. (Dispongono di una linea rossa "!".)

   > [!NOTE]
   > Non è possibile selezionare le notifiche nel *Successful* oppure *In corso* dello stato.

3. Viene aperto il riquadro **Dettagli notifica**. Leggere le informazioni per conoscere il problema.

5. Se è ancora necessaria assistenza, condividere informazioni con un tecnico del supporto o il gruppo di prodotto. Selezionare il **copia** a destra dell'icona le **copia errore** per copiare i dettagli di notifica da condividere.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Inviare i dettagli di notifica a un tecnico del supporto per ottenere assistenza

È importante condividere *tutti* i dettagli sono elencati in questa sezione con il supporto in modo che possono aiutarti a rapidamente. Per registrare lo, è possibile acquisire una schermata o selezionare **copia errore**.

Le informazioni seguenti viene illustrato che cosa significa che ogni elemento di notifica e vengono forniti esempi.

### <a name="essential-notification-items"></a>Elementi essenziali della notifica

- **Titolo**: il titolo descrittivo della notifica.

   Esempio: *Impostazioni proxy di applicazione*

- **Descrizione**: che cosa si è verificato a seguito dell'operazione.

   Esempio: *URL interno immesso è già in uso da un'altra applicazione.*

- **ID notifica**: l'ID univoco della notifica.

    Esempio: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID richiesta client**: l'ID richiesta specifico che ha effettuato il browser.

    Esempio: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Timestamp (UTC)** : il timestamp di quando cui si è verificata la notifica, in formato UTC.

    Esempio: *2017-03-23T19:50:43.7583681Z*

- **ID transazione interna**: l'ID interno che consente di cercare l'errore nel sistema.

    Esempio: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: L'utente che ha eseguito l'operazione.

    Esempio: *tperkins\@f128.info*

- **ID tenant**: l'ID univoco del tenant di cui l'utente che ha eseguito l'operazione è un membro di.

    Esempio: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID oggetto utente**: ID univoco dell'utente che ha eseguito l'operazione.

    Esempio: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Elementi della notifica dettagliati

- **Nome visualizzato**: (può essere vuoto) un nome di visualizzazione più dettagliata dell'errore.

    Esempio: *Impostazioni proxy di applicazione*

- **Stato**: lo stato specifico della notifica.

    Esempio: *Non riuscito*

- **ID di oggetto**: (può essere vuoto) l'ID di oggetto su cui è stata eseguita l'operazione.

   Esempio: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Dettagli**: la descrizione dettagliata di cosa si è verificato a seguito dell'operazione.

    Esempio: *Url interno '<https://bing.com/>' non è valido perché è già in uso.*

- **Copia errore**: Consente di selezionare il **icona di copia** a destra del **copia errore** nella casella di testo per copiare i dettagli della notifica per facilitare il supporto tecnico.

    Esempio:   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md)
