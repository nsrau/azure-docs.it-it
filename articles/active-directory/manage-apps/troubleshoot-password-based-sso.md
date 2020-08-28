---
title: Risolvere i problemi di Single Sign-On basata su password in Azure AD
description: Come risolvere i problemi relativi a un'app Azure AD configurata per Single Sign-On basati su password.
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 436fbdde1283bd8406e631fd37b559bfe11e234e
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051942"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Risolvere i problemi di Single Sign-On basata su password in Azure AD

Per usare la Single Sign-On basata su password (SSO) in app personali, è necessario installare l'estensione del browser. L'estensione viene scaricata automaticamente quando si seleziona un'app configurata per l'accesso SSO basato su password. Per informazioni sull'uso delle app personali dal punto di vista dell'utente finale, vedere [la guida del portale delle app personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="my-apps-browser-extension-not-installed"></a>Estensione del browser app personali non installata
Verificare che l'estensione del browser sia installata. Per altre informazioni, vedere [pianificare un Azure Active Directory la distribuzione di app personali](access-panel-deployment-plan.md). 

## <a name="single-sign-on-not-configured"></a>Single Sign-on non configurato
Assicurarsi che Single Sign-On basata su password sia configurato. Per altre informazioni, vedere [configurare Single Sign-on basate su password](configure-password-single-sign-on-non-gallery-applications.md).

## <a name="users-not-assigned"></a>Utenti non assegnati
Assicurarsi che l'utente sia assegnato all'app. Per altre informazioni, vedere [assegnare un utente o un gruppo a un'app](assign-user-or-group-access-portal.md).

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Le credenziali vengono compilate, ma l'estensione non le invia

Questo problema si verifica in genere se il fornitore dell'applicazione ha modificato di recente la pagina di accesso per aggiungere un campo, ha modificato un identificatore usato per rilevare i campi nome utente e password o ha modificato l'esperienza di accesso alla propria applicazione. In molti casi Microsoft può collaborare con i fornitori di applicazioni per risolvere i problemi rapidamente.

Mentre Microsoft dispone di tecnologie per rilevare automaticamente le interruzioni nelle integrazioni, potrebbe non essere possibile rilevare i problemi immediatamente oppure i problemi richiedono tempo per essere risolti. Nel caso in cui una di queste integrazioni non funzioni correttamente, aprire un caso di supporto in modo che il problema possa essere risolto il più rapidamente possibile.

**Se l'utente è in contatto con il fornitore dell'applicazione, **può inviargli la soluzione offerta in modo che Microsoft possa lavorare con loro per integrare nativamente la loro applicazione con Azure Active Directory. Per iniziare, è possibile indirizzare il fornitore all'[Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Le credenziali vengono compilate e inoltrate, ma un messaggio nella pagina indica che le credenziali sono errate

Per risolvere questo problema, provare innanzitutto queste soluzioni:

- Chiedere all'utente di provare ad **accedere direttamente al sito Web dell'applicazione** con le credenziali archiviate.

  * Se l'accesso funziona, fare in modo che l'utente faccia clic sul pulsante **Aggiorna credenziali** nel **riquadro dell'applicazione** nella sezione **app** di [app personali](https://myapps.microsoft.com/) per aggiornarle al nome utente e alla password funzionanti più recenti.

  * Se è stato un amministratore ad assegnare le credenziali per questo utente, individuare l'assegnazione dell'applicazione dell'utente o del gruppo passando alla scheda **Utenti gruppi** dell'applicazione, selezionando l'assegnazione e facendo clic sul pulsante**Aggiorna credenziali**.

- Se è stato l'utente ad assegnarsi le credenziali, chiedergli di **verificare che la password dell'applicazione non sia scaduta** ed eventualmente **aggiornarla** accedendo all'applicazione direttamente.

  * Dopo che la password è stata aggiornata nell'applicazione, richiedere all'utente di fare clic sul pulsante **Aggiorna credenziali** nel **riquadro dell'applicazione** nella sezione **app** di [app personali](https://myapps.microsoft.com/) per aggiornarle al nome utente e alla password più recenti.

  * Se è stato un amministratore ad assegnare le credenziali per questo utente, individuare l'assegnazione dell'applicazione dell'utente o del gruppo passando alla scheda **Utenti gruppi** dell'applicazione, selezionando l'assegnazione e facendo clic sul pulsante**Aggiorna credenziali**.

- Verificare che l'estensione del browser app personali sia in esecuzione e abilitata nel browser dell'utente.

- Assicurarsi che gli utenti non stiano tentando di accedere all'applicazione da app personali in **incognito, InPrivate o in modalità privata**. L'estensione app personali non è supportata in queste modalità.

Se il problema persiste, è possibile che sia stata apportata una modifica sul lato applicazione che ha interrotto temporaneamente l'integrazione dell'applicazione con Azure AD. Il fornitore dell'applicazione potrebbe, ad esempio, aver introdotto nella pagina uno script con un comportamento che varia tra l'input manuale e l'input automatico e che causa l'interruzione nell'integrazione automatica. In molti casi Microsoft può collaborare con i fornitori di applicazioni per risolvere i problemi rapidamente.

Mentre Microsoft dispone di tecnologie per rilevare automaticamente le interruzioni delle integrazioni delle applicazioni, potrebbe non essere possibile rilevare i problemi immediatamente oppure i problemi richiedono tempo per essere risolti. Quando un'integrazione non funziona correttamente, è possibile aprire un caso di supporto per risolvere il problema nel più breve tempo possibile. 

**Se l'utente è inoltre in contatto con il fornitore dell'applicazione,** **può inviargli la soluzione offerta da Microsoft** affinché si possa collaborare a integrare in modo nativo l'applicazione con Azure Active Directory. Per iniziare, è possibile indirizzare il fornitore all'[Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Controllare se la pagina di accesso dell'applicazione è cambiata di recente o richiede un campo aggiuntivo

Se la pagina di accesso dell'applicazione è cambiata drasticamente, talvolta è possibile che si verifichi un'interruzione dell'integrazione. Un esempio è quando il fornitore dell'applicazione aggiunge un campo di accesso, un captcha o l'autenticazione a più fattori alle proprie esperienze. In molti casi Microsoft può collaborare con i fornitori di applicazioni per risolvere i problemi rapidamente.

Mentre Microsoft dispone di tecnologie per rilevare automaticamente le interruzioni delle integrazioni delle applicazioni, potrebbe non essere possibile rilevare i problemi immediatamente oppure i problemi richiedono tempo per essere risolti. Quando un'integrazione non funziona correttamente, è possibile aprire un caso di supporto per risolvere il problema nel più breve tempo possibile. 

**Se l'utente è inoltre in contatto con il fornitore dell'applicazione,** **può inviargli la soluzione offerta da Microsoft** affinché si possa collaborare a integrare in modo nativo l'applicazione con Azure Active Directory. Per iniziare, è possibile indirizzare il fornitore all'[Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

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
    La pagina viene automaticamente frammentata per le caselle di input nome utente e password. È ora possibile usare Azure AD per trasmettere in modo sicuro le password a tale app usando l'estensione del browser app personali.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Acquisire manualmente i campi di accesso per un'app

Per acquisire manualmente i campi di accesso, è necessario che sia installata l'estensione del browser app personali. Inoltre, il browser non può essere eseguito in modalità *InPrivate*, *incognito*o *privata* .

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
10. Selezionare **Configure * &lt; appname &gt; * password Single Sign-on Settings**.
11. Selezionare **rileva manualmente i campi di accesso**.
14. Selezionare **OK**.
15. Selezionare **Salva**.
16. Seguire le istruzioni per usare le app personali.


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
- L'acquisizione manuale sembra funzionare, ma SSO non si verifica quando gli utenti passano all'app dalle app personali.

Se si verifica uno di questi problemi, effettuare le operazioni seguenti:
- Assicurarsi che sia *installata e abilitata*la versione più recente dell'estensione del browser app personali.
- Assicurarsi che il browser non sia in modalità *incognito*, *InPrivate*o *privata* durante il processo di acquisizione. L'estensione app personali non è supportata in queste modalità.
- Assicurarsi che gli utenti non stiano tentando di accedere all'app dalle app personali in *incognito*, *InPrivate*o in *modalità privata*.
- Provare a eseguire di nuovo il processo di acquisizione manuale. Assicurarsi che i marcatori rossi si trovino sui campi corretti.
- Se il processo di acquisizione manuale sembra smettere di rispondere o la pagina di accesso non risponde, provare a eseguire di nuovo il processo di acquisizione manuale. Questa volta, tuttavia, dopo aver completato il processo, premere il tasto F12 per aprire la console per sviluppatori del browser. Selezionare la scheda **console** . digitare **Window. location = "* &lt; URL di accesso specificato durante la configurazione dell' &gt; app*"** e quindi premere INVIO. In questo modo viene forzato un reindirizzamento della pagina che termina il processo di acquisizione e archivia i campi acquisiti.

## <a name="request-support"></a>Richiedere supporto 
Se viene visualizzato un messaggio di errore quando si configura SSO e si assegnano utenti, aprire un ticket di supporto. Includere la maggior parte delle informazioni riportate di seguito:

-   ID errore di correlazione
-   UPN (indirizzo di posta elettronica dell'utente)
-   ID tenant
-   Tipo di browser
-   Fuso orario e tempo/intervallo di tempo in cui si è verificato l'errore
-   Tracce Fiddler

### <a name="view-portal-notification-details"></a>Visualizza i dettagli delle notifiche del portale

Per visualizzare i dettagli di tutte le notifiche del portale, attenersi alla seguente procedura:
1. Selezionare l'icona **notifiche** (la Campanella) nell'angolo superiore destro della portale di Azure.
2. Selezionare una notifica che mostra uno stato di *errore* . (Hanno una "!" rossa).
   > [!NOTE]
   > Non è possibile selezionare notifiche con stato di *esito positivo* o *in corso* .
3. Viene aperto il riquadro **Dettagli notifica**. Leggere le informazioni per conoscere il problema.
5. Se è ancora necessaria assistenza, condividere le informazioni con un tecnico del supporto o con il gruppo di prodotti. Selezionare l'icona **copia** a destra della casella **copia errore** per copiare i dettagli della notifica da condividere.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Inviare i dettagli della notifica a un tecnico del supporto per ottenere assistenza

È importante condividere *tutti* i dettagli elencati in questa sezione con supporto in modo che possano aiutarti rapidamente. Per registrarlo, è possibile eseguire una schermata o selezionare **copia errore**.

Nelle informazioni seguenti viene illustrato il significato di ogni elemento di notifica e vengono forniti esempi.

#### <a name="essential-notification-items"></a>Elementi di notifica essenziali

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

    Esempio: *tperkins \@ F128.info*

- **ID tenant**: ID univoco del tenant di cui è membro l'utente che ha eseguito l'operazione.

    Esempio: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID oggetto utente**: ID univoco dell'utente che ha eseguito l'operazione.

    Esempio: *17f84be4-51f8-483A-B533-383791227a99*

#### <a name="detailed-notification-items"></a>Elementi di notifica dettagliati

- **Nome visualizzato**: (può essere vuoto) un nome visualizzato più dettagliato per l'errore.

    Esempio: *impostazioni del proxy di applicazione*

- **Stato**: lo stato specifico della notifica.

    Esempio: *failed*

- **ID oggetto**: (può essere vuoto) ID oggetto su cui è stata eseguita l'operazione.

   Esempio: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Dettagli**: Descrizione dettagliata di ciò che si è verificato come risultato dell'operazione.

    Esempio: l' *URL interno ' <https://bing.com/> ' non è valido perché è già in uso.*

- **Copia errore**: consente di selezionare l' **icona di copia** a destra della casella di testo **copia errore** per copiare i dettagli della notifica in modo da agevolare il supporto.

    Esempio   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Passaggi successivi
* [Serie di guide di avvio rapido sulla gestione delle applicazioni](view-applications-portal.md)
* [Pianificare una distribuzione di App personali](access-panel-deployment-plan.md)
