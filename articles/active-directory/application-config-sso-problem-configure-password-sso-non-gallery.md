---
title: Problema nella configurazione dell'accesso Single Sign-On basato su password per un'applicazione non inclusa nella raccolta | Microsoft Docs
description: Informazioni sui problemi comuni che si possono incontrare durante la configurazione dell'accesso Single Sign-On basato su password per applicazioni personalizzate non incluse nella raccolta delle applicazioni di Azure AD
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: e5c8562b89b70236d9d65a5f8765ae386d6831b4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problema nella configurazione dell'accesso Single Sign-On basato su password per un'applicazione non inclusa nella raccolta

L'articolo descrive i problemi comuni che si possono incontrare durante la configurazione dell'accesso**Single Sign-On basato su password** per un'applicazione non inclusa nella raccolta.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>Come acquisire i campi di accesso per un'applicazione

L'acquisizione dei campi di accesso è supportata solo per le pagine di accesso basate su HTML e **non è supportata per le pagine di accesso non standard**, ad esempio le pagine che usano Flash o altre tecnologie non basate su HTML.

Esistono due modi per acquisire i campi di accesso per le applicazioni personalizzate:

-   Acquisizione automatica dei campi di accesso

-   Acquisizione manuale dei campi di accesso

L'**acquisizione automatica dei campi di accesso** funziona bene con la maggior parte delle pagine di accesso basate su HTML, se usano **ID DIV noti per il campo di input del nome utente e della password**. Questo approccio esamina il codice HTML nella pagina alla ricerca di ID DIV che corrispondano ai criteri specifici e salva i metadati per questa applicazione consentendo così di riprodurre le password in momenti successivi.

L'**acquisizione manuale dei campi di accesso** può essere usata nel caso in cui il **fornitore dell'applicazione non etichetti** i campi di input usati per l'accesso. L'acquisizione manuale dei campi di accesso può inoltre essere usata nel caso in cui il **fornitore esegue il rendering di più campi** che non possono essere rilevati automaticamente. Azure AD può archiviare i dati per tutti i campi presenti nella pagina di accesso purché venga indicato dove si trovano questi campi nella pagina.

Come regola generale, **se non funziona l'acquisizione automatica dei campi di accesso, provare l'approccio manuale.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>Come acquisire automaticamente i campi di accesso per un'applicazione

Per configurare l'**accesso Single Sign-On basato su password** per un'applicazione usando l'**acquisizione automatica dei campi di accesso**, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8.  Selezionare la modalità **Accesso basato su password**.

9.  Immettere l'**URL di accesso**, ovvero l'URL in cui gli utenti immetteranno il nome utente e la password per eseguire l'accesso. **Assicurarsi che i campi di accesso siano visibili nell'URL fornito**.

10. Fare clic sul pulsante **Salva**.

11. Dopo questa operazione, dall'URL verrà creata automaticamente una casella di input per nome utente e password e sarà possibile usare Azure AD per la trasmissione sicura delle password all'applicazione tramite l'estensione del browser del pannello di accesso.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>Come acquisire manualmente i campi di accesso per un'applicazione

Per acquisire manualmente i campi di accesso, è necessario prima di tutto avere installato l'estensione del browser per il pannello di accesso e **non deve essere in esecuzione in modalità privata o in incognito.** Per installare l'estensione del browser, seguire la procedura descritta nella sezione [Come installare l'estensione del browser per il pannello di accesso](#i-cannot-manually-detect-sign-in-fields-for-my-application).

Per configurare l'**accesso Single Sign-On basato su password** per un'applicazione usando l'**acquisizione manuale dei campi di accesso**, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8.  Selezionare la modalità **Accesso basato su password**.

9.  Immettere l'**URL di accesso**, ovvero l'URL in cui gli utenti immetteranno il nome utente e la password per eseguire l'accesso. **Assicurarsi che i campi di accesso siano visibili nell'URL fornito**.

10. Fare clic sul pulsante **Salva**.

11. Dopo questa operazione, dall'URL verrà creata automaticamente una casella di input per nome utente e password e sarà possibile usare Azure AD per la trasmissione sicura delle password all'applicazione tramite l'estensione del browser del pannello di accesso. Nel caso in cui l'operazione non riesca, è possibile **modificare la modalità di accesso per usare l'acquisizione manuale dei campi di accesso** andando al passaggio 12.

12. Fare clic su **Configura impostazioni Password Single Sign-On per &lt;nomeapp&gt;**.

13. Selezionare l'opzione di configurazione **Rileva manualmente i campi di accesso**.

14. Fare clic su **OK**.

15. Fare clic su **Salva**.

16. Seguire le istruzioni visualizzate per usare il pannello di accesso.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Errore "Non sono stati trovati campi di accesso nell'URL specificato"

Questo errore viene visualizzato quando il rilevamento automatico dei campi di accesso ha esito negativo. Per risolvere il problema, provare il rilevamento manuale dei campi di accesso seguendo la procedura descritta nella sezione [Come acquisire manualmente i campi di accesso per un'applicazione](#how-to-manually-capture-sign-in-fields-for-an-application).

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>Errore "Non è possibile salvare la configurazione Single Sign-On"

In casi rari, l'aggiornamento della configurazione Single Sign-On può avere esito negativo. Per risolvere il problema, provare a salvare nuovamente la configurazione Single Sign-On.

Se il problema persiste, aprire un caso di supporto e immettere le informazioni raccolte nelle sezioni[Come visualizzare i dettagli di una notifica del portale](#i-cannot-manually-detect-sign-in-fields-for-my-application) e [Come ottenere assistenza inviando i dettagli di notifica a un tecnico del supporto](#how-to-get-help-by-sending-notification-details-to-a-support-engineer).

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>Non è possibile rilevare manualmente i campi di accesso per l'applicazione

Tra i comportamenti che potrebbero verificarsi quando il rilevamento manuale non funziona sono inclusi i seguenti:

-   È sembrato che il processo di acquisizione manuale funzionasse, ma i campi acquisiti non sono corretti

-   Non vengono evidenziati i campi giusti quando si esegue il processo di acquisizione

-   Il processo di acquisizione indirizza alla pagina di accesso dell'applicazione come previsto, ma non accade nulla

-   L'acquisizione manuale sembra funzionare, ma non avviene l'accesso SSO quando gli utenti passano all'applicazione dal pannello di accesso.

Se si verifica uno di questi problemi, verificare quanto segue:

-   Assicurarsi di avere **installato** e **abilitato** la versione più recente dell'estensione del browser del pannello di accesso seguendo la procedura indicata nella sezione [Come installare l'estensione del browser del pannello di accesso](#how-to-install-the-access-panel-browser-extension).

-   Non tentare di eseguire il processo di acquisizione mentre il browser è in **modalità privata o in incognito**. L'estensione del pannello di accesso non è supportata in queste modalità.

-   Assicurarsi che gli utenti non stiano tentando di accedere all'applicazione dal pannello di accesso in **modalità privata o in incognito**. L'estensione del pannello di accesso non è supportata in queste modalità.

-   Provare di nuovo il processo di acquisizione manuale, assicurarsi che gli indicatori rossi si trovino sui campi corretti.

-   Se il processo di acquisizione manuale sembra bloccarsi o nella pagina di accesso non esegue alcuna operazione (caso 3 precedente), provare a eseguirlo nuovamente. Questa volta, tuttavia, al termine del processo, premere il tasto **F12** per aprire la console per sviluppatori del browser. Aprire la **console** e digitare **window.location="&lt;immettere l'URL di accesso specificato durante la configurazione dell'app&gt;"** e quindi premere **INVIO**. Questa operazione forza il reindirizzamento della pagina, che termina il processo di acquisizione e archivia i campi acquisiti.

Se nessuno di questi approcci risolve il problema, rivolgersi al supporto Microsoft. Aprire una richiesta di assistenza con i dettagli delle azioni tentate, nonché le informazioni raccolte nelle sezioni [Come visualizzare i dettagli di una notifica del portale](#i-cannot-manually-detect-sign-in-fields-for-my-application) e [Come ottenere assistenza inviando i dettagli di notifica a un tecnico del supporto](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) (se applicabile).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Come installare l'estensione del browser per il pannello di accesso

Per installare l'estensione del browser per il pannello di accesso, seguire questa procedura:

1.  Aprire il [pannello di accesso](https://myapps.microsoft.com) in uno dei browser supportati e accedere come **utente** ad Azure AD.

2.  Fare clic su un'**applicazione con accesso SSO basato su password** nel pannello di accesso.

3.  Quando viene richiesto di installare il software, selezionare **Installa ora**.

4.  A seconda del browser in uso, si verrà indirizzati al collegamento per il download. **Aggiungere** l'estensione al browser.

5.  Se richiesto dal browser, scegliere se **abilitare** o **consentire** l'uso dell'estensione.

6.  Al termine dell'installazione, **riavviare** la sessione del browser.

7.  Accedere al pannello di accesso e verificare se è possibile **avviare** le applicazioni con accesso SSO basato su password.

È anche possibile scaricare l'estensione per Chrome e Firefox dai collegamenti diretti seguenti:

-   [Estensione Pannello di accesso per Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Estensione Pannello di accesso per Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Come visualizzare i dettagli di una notifica del portale

È possibile visualizzare i dettagli di qualsiasi notifica del portale seguendo questa procedura:

1.  Fare clic sull'icona **Notifiche** (la campanella) in alto a destra nel portale di Azure

2.  Selezionare una notifica con stato **Errore** contrassegnata con un punto esclamativo (!) rosso.

  >!NOTA] Non è possibile fare clic sulle notifiche in uno stato di **Operazione completata** o **In corso**.
  >
  >

3.  Viene aperto il riquadro **Dettagli notifica**.

4.  Usare le informazioni per ottenere maggiori dettagli sul problema.

5.  Nel caso in cui sia ancora necessaria assistenza sul problema, è possibile condividere le informazioni con un tecnico di supporto o con il gruppo del prodotto.

6.  Fare clic sull'**icona** **Copia** a destra della casella di testo **Copia errore** per copiare tutti i dettagli della notifica da condividere con un tecnico del supporto o con il gruppo del prodotto.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Come ottenere assistenza inviando i dettagli di notifica a un tecnico del supporto

È molto importante condividere **tutti i dettagli elencati di seguito** con un tecnico del supporto per poter ricevere assistenza immediata. È possibile **acquisire uno screenshot** o fare clic sull'**icona Copia errore** che si trova a destra della casella di testo **Copia errore**.

## <a name="notification-details-explained"></a>Spiegazione dei dettagli della notifica

La sezione seguente illustra in dettaglio il significato degli elementi della notifica e offre esempi per ognuno di essi.

### <a name="essential-notification-items"></a>Elementi essenziali della notifica

-   **Titolo**: il titolo descrittivo della notifica

    -   Esempio: **Impostazioni proxy di applicazione**

-   **Descrizione**: la descrizione di ciò che si è verificato a seguito dell'operazione

    -   Esempio: **L'URL interno immesso è già usato da un'altra applicazione**

-   **ID notifica**: ID univoco della notifica

    -   Esempio: **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **ID richiesta client**: ID specifico della richiesta creato dal browser

    -   Esempio: **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Timestamp (UTC)**: il timestamp in cui si è verificata la notifica, basato sul sistema UTC

    -   Esempio: **2017-03-23T19:50:43.7583681Z**

-   **ID transazione interna**: ID interno usato per cercare l'errore nei sistemi

    -   Esempio: **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** : l'utente che ha eseguito l'operazione

    -   Esempio: **tperkins@f128.info**

-   **ID tenant**: ID univoco del tenant di cui è membro l'utente che ha eseguito l'operazione

    -   Esempio: **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **ID oggetto utente**: ID univoco dell'utente che ha eseguito l'operazione

    -   Esempio: **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Elementi della notifica dettagliati

-   **Nome visualizzato**: **(può essere vuoto)** un nome visualizzato più dettagliato per l'errore

    -   Esempio*: **impostazioni del proxy di applicazione**

-   **Stato**: lo stato specifico della notifica

    -   Esempio*: **Operazione non riuscita**

-   **ID oggetto**: **(può essere vuoto)** ID dell'oggetto su cui è stata eseguita l'operazione

    -   Esempio: **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Dettagli**: la descrizione dettagliata di ciò che si è verificato come conseguenza dell'operazione

    -   Esempio: **L'URL interno "http://bing.com/" non è valido perché è già in uso**

-   **Copia errore**: fare clic sull'**icona Copia** a destra della casella di testo **Copia errore** per copiare tutti i dettagli di notifica da condividere con un tecnico di supporto o con il gruppo del prodotto

    -   Esempio: ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md)

