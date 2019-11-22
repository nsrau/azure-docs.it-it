---
title: Problemi di configurazione dell'accesso Single Sign-on basato su password per app non della raccolta
description: Problemi comuni che si verificano quando si configura la Single Sign-On delle password (SSO) per le app personalizzate che non si trovano nella raccolta di applicazioni Azure AD.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274139"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problemi di configurazione Single Sign-On delle password per un'applicazione non della raccolta

Questo articolo descrive i problemi comuni che possono verificarsi quando si configura la *Single Sign-on delle password* (SSO) per un'app non della raccolta.

## <a name="capture-sign-in-fields-for-an-app"></a>Acquisire i campi di accesso per un'app

L'acquisizione dei campi di accesso è supportata solo per le pagine di accesso abilitate per HTML. Non è supportata per le pagine di accesso non standard, ad esempio quelle che usano Adobe Flash o altre tecnologie non abilitate per HTML.

Esistono due modi per acquisire i campi di accesso per le app personalizzate:

- L' **acquisizione automatica dei campi di accesso** funziona correttamente con la maggior parte delle pagine di accesso abilitate per HTML, *se usano ID div noti* per i campi nome utente e password. Il codice HTML nella pagina viene raschiato per trovare gli ID DIV che corrispondono a determinati criteri. I metadati vengono salvati in modo che possano essere riprodotti nell'app in un secondo momento.

- L' **acquisizione manuale dei campi di accesso** viene usata se il fornitore dell'app *non etichetta i campi di input di accesso*. L'acquisizione manuale viene utilizzata anche se il fornitore *esegue il rendering di più campi che non possono essere rilevati automaticamente*. Azure Active Directory (Azure AD) può archiviare i dati per tutti i campi presenti nella pagina di accesso, se si indica la posizione in cui si trovano i campi nella pagina.

In generale, se l'acquisizione automatica dei campi di accesso non funziona, provare l'opzione manuale.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Acquisisci automaticamente i campi di accesso per un'app

Per configurare l'accesso SSO basato su password usando l'acquisizione automatica dei campi di accesso, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/). Accedere come amministratore globale o coamministratore.

2. Nel riquadro di spostamento sul lato sinistro selezionare tutti i **Servizi** per aprire l'estensione Azure ad.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento Azure ad.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se non viene visualizzata l'app desiderata, usare il controllo **filtro** all'inizio dell'elenco **tutte le applicazioni** . Impostare l'opzione **Mostra** su "tutte le applicazioni".

6. Selezionare l'app che si vuole configurare per SSO.

7. Al termine del caricamento dell'app, selezionare **Single Sign-on** nel riquadro di spostamento sul lato sinistro.

8. Selezionare la modalità **accesso basato su password** .

9. Immettere l' **URL di accesso**, ovvero l'URL della pagina in cui gli utenti immettono il nome utente e la password per l'accesso. *Assicurarsi che i campi di accesso siano visibili nella pagina per l'URL fornito*.

10. Selezionare **Salva**.

    La pagina viene automaticamente frammentata per le caselle di input nome utente e password. È ora possibile usare Azure AD per trasmettere in modo sicuro le password a tale app usando l'estensione del browser del pannello di accesso.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Acquisire manualmente i campi di accesso per un'app

Per acquisire manualmente i campi di accesso, è necessario che sia installata l'estensione del browser del pannello di accesso. Inoltre, il browser non può essere eseguito in modalità *InPrivate*, *incognito*o *privata* .

Per installare l'estensione, vedere la sezione [installare l'estensione del browser del pannello di accesso](#install-the-access-panel-browser-extension) di questo articolo.

Per configurare l'accesso SSO basato su password per un'app usando l'acquisizione manuale dei campi di accesso, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/). Accedere come amministratore globale o coamministratore.

2. Nel riquadro di spostamento sul lato sinistro selezionare tutti i **Servizi** per aprire l'estensione Azure ad.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento Azure ad.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE] 
   > Se non viene visualizzata l'app desiderata, usare il controllo **filtro** all'inizio dell'elenco **tutte le applicazioni** . Impostare l'opzione **Mostra** su "tutte le applicazioni".

6. Selezionare l'app che si vuole configurare per SSO.

7. Al termine del caricamento dell'app, selezionare **Single Sign-on** nel riquadro di spostamento sul lato sinistro.

8. Selezionare la modalità **accesso basato su password** .

9. Immettere l' **URL di accesso**, ovvero la pagina in cui gli utenti immettono il nome utente e la password per l'accesso. *Assicurarsi che i campi di accesso siano visibili nella pagina per l'URL fornito*.

10. Selezionare **configura *&lt;AppName&gt;* impostazioni di Single Sign-on per la password**.

11. Selezionare **rileva manualmente i campi di accesso**.

14. Selezionare **OK**.

15. Selezionare **Salva**.

16. Seguire le istruzioni per usare il pannello di accesso.

## <a name="troubleshoot-problems"></a>Risolvere i problemi

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Viene ricevuto un errore "non sono stati trovati campi di accesso in questo URL"

Questo messaggio di errore viene ricevuto quando il rilevamento automatico dei campi di accesso ha esito negativo. Per risolvere il problema, provare il rilevamento manuale dei campi di accesso. Vedere la sezione [relativa all'acquisizione manuale dei campi di accesso per un'applicazione](#manually-capture-sign-in-fields-for-an-app) di questo articolo.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Viene ricevuto un errore "Impossibile salvare la configurazione Single Sign-On"

Raramente, l'aggiornamento della configurazione SSO ha esito negativo. Per risolvere il problema, provare a salvare di nuovo la configurazione.

Se si continua a ricevere l'errore, aprire un caso di supporto. Includere le informazioni descritte nella sezione visualizzare i [Dettagli della notifica del portale](#view-portal-notification-details) e inviare i dettagli della notifica [a un tecnico del supporto per ottenere](#send-notification-details-to-a-support-engineer-to-get-help) le sezioni della Guida di questo articolo.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Non è possibile rilevare manualmente i campi di accesso per l'app

Quando il rilevamento manuale non funziona, è possibile osservare i comportamenti seguenti:

- Il processo di acquisizione manuale sembra funzionare, ma i campi acquisiti non sono corretti.

- I campi corretti non vengono evidenziati quando viene eseguito il processo di acquisizione.

- Il processo di acquisizione porta alla pagina di accesso dell'app come previsto, ma non accade nulla.

- L'acquisizione manuale sembra funzionare, ma SSO non si verifica quando gli utenti passano all'app dal pannello di accesso.

Se si verifica uno di questi problemi, effettuare le operazioni seguenti:

- Assicurarsi che sia *installata e abilitata*la versione più recente dell'estensione del browser del pannello di accesso. Vedere la sezione [installare l'estensione del browser](#install-the-access-panel-browser-extension) per il pannello di accesso di questo articolo.

- Assicurarsi che il browser non sia in modalità *incognito*, *InPrivate*o *privata* durante il processo di acquisizione. L'estensione del pannello di accesso non è supportata in queste modalità.

- Assicurarsi che gli utenti non stiano tentando di accedere all'app dal pannello di accesso in *modalità privata*o in *incognito*.

- Provare a eseguire di nuovo il processo di acquisizione manuale. Assicurarsi che i marcatori rossi si trovino sui campi corretti.

- Se il processo di acquisizione manuale sembra smettere di rispondere o la pagina di accesso non risponde, provare a eseguire di nuovo il processo di acquisizione manuale. Questa volta, tuttavia, dopo aver completato il processo, premere il tasto F12 per aprire la console per sviluppatori del browser. Selezionare la scheda **console** . digitare **Window. location = " *&lt;l'URL di accesso specificato durante la configurazione dell'app&gt;* "** , quindi premere INVIO. In questo modo viene forzato un reindirizzamento della pagina che termina il processo di acquisizione e archivia i campi acquisiti.

### <a name="contact-support"></a>Contattare il supporto tecnico

Se si verificano ancora problemi, aprire un caso con supporto tecnico Microsoft. Descrivere gli elementi che sono stati tentati. Includere i dettagli descritti in visualizzare i dettagli della [notifica del portale](#view-portal-notification-details) e inviare i dettagli della notifica [a un tecnico del supporto per ottenere](#send-notification-details-to-a-support-engineer-to-get-help) le sezioni della Guida di questo articolo (se applicabile).

## <a name="install-the-access-panel-browser-extension"></a>Installare l'estensione del browser per il pannello di accesso

Seguire questa procedura:

1. Aprire il [Pannello di accesso](https://myapps.microsoft.com) in un browser supportato. Accedere per Azure AD come *utente*.

2. Selezionare **applicazione password-SSO** nel pannello di accesso.

3. Quando viene richiesto di installare il software, selezionare **Installa ora**.

4. Si verrà indirizzati a una pagina di download per il browser. Scegliere di **aggiungere** l'estensione.

5. Se richiesto, selezionare **Abilita** o **Consenti**.

6. Al termine dell'installazione, riavviare il browser.

7. Accedere al pannello di accesso. Vedere se è possibile aprire le app abilitate per la password.

È anche possibile scaricare direttamente l'estensione del browser per Chrome e Firefox tramite i collegamenti seguenti:

-   [Estensione pannello di accesso Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Estensione pannello di accesso di Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Visualizza i dettagli delle notifiche del portale

Per visualizzare i dettagli di tutte le notifiche del portale, attenersi alla seguente procedura:

1. Selezionare l'icona **notifiche** (la Campanella) nell'angolo superiore destro della portale di Azure.

2. Selezionare una notifica che mostra uno stato di *errore* . (Hanno una "!" rossa).

   > [!NOTE]
   > Non è possibile selezionare notifiche con stato di *esito positivo* o *in corso* .

3. Viene aperto il riquadro **Dettagli notifica**. Leggere le informazioni per conoscere il problema.

5. Se è ancora necessaria assistenza, condividere le informazioni con un tecnico del supporto o con il gruppo di prodotti. Selezionare l'icona **copia** a destra della casella **copia errore** per copiare i dettagli della notifica da condividere.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Inviare i dettagli della notifica a un tecnico del supporto per ottenere assistenza

È importante condividere *tutti* i dettagli elencati in questa sezione con supporto in modo che possano aiutarti rapidamente. Per registrarlo, è possibile eseguire una schermata o selezionare **copia errore**.

Nelle informazioni seguenti viene illustrato il significato di ogni elemento di notifica e vengono forniti esempi.

### <a name="essential-notification-items"></a>Elementi di notifica essenziali

- **Title**: titolo descrittivo della notifica.

   Esempio: *impostazioni del proxy di applicazione*

- **Descrizione**: cosa si è verificato come risultato dell'operazione.

   Esempio: l' *URL interno immesso è già usato da un'altra applicazione.*

- **ID notifica**: ID univoco della notifica.

    Esempio: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID richiesta client**: ID richiesta specifico creato dal browser.

    Esempio: *302fd775-3329-4670-A9F3-bea37004f0bc*

- Timestamp **time (UTC**): timestamp di quando si è verificata la notifica, in formato UTC.

    Esempio: *2017-03-23T19:50:43.7583681 z*

- **Internal Transaction ID**: ID interno usato per cercare l'errore nei sistemi.

    Esempio: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: l'utente che ha eseguito l'operazione.

    Esempio: *tperkins\@F128.info*

- **ID tenant**: ID univoco del tenant di cui è membro l'utente che ha eseguito l'operazione.

    Esempio: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID oggetto utente**: ID univoco dell'utente che ha eseguito l'operazione.

    Esempio: *17f84be4-51f8-483A-B533-383791227a99*

### <a name="detailed-notification-items"></a>Elementi di notifica dettagliati

- **Nome visualizzato**: (può essere vuoto) un nome visualizzato più dettagliato per l'errore.

    Esempio: *impostazioni del proxy di applicazione*

- **Stato**: lo stato specifico della notifica.

    Esempio: *failed*

- **ID oggetto**: (può essere vuoto) ID oggetto su cui è stata eseguita l'operazione.

   Esempio: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Dettagli**: Descrizione dettagliata di ciò che si è verificato come risultato dell'operazione.

    Esempio: l' *URL interno '<https://bing.com/>' non è valido perché è già in uso.*

- **Copia errore**: consente di selezionare l' **icona di copia** a destra della casella di testo **copia errore** per copiare i dettagli della notifica in modo da agevolare il supporto.

    Esempio: ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md)
