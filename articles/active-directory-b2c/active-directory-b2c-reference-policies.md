<properties
	pageTitle="Anteprima di Azure Active Directory B2C: Framework di criteri estendibile | Microsoft Azure"
	description="Argomento che descrive il framework di criteri estendibile di Azure Active Directory B2C e che illustra come creare vari tipi di criteri"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: Framework di criteri estendibile

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Nozioni di base

Il framework di criteri estendibile di Azure Active Directory (AD) B2C è il punto di forza del servizio. I criteri descrivono completamente le esperienze di identità utente, ad esempio iscrizione, accesso o modifica del profilo. Ad esempio, i criteri di iscrizione consentono di controllare i comportamenti configurando le impostazioni seguenti:

- Tipi di account (account dei social network, come ad esempio Facebook e Google e/o account locali come indirizzo di posta elettronica/nome utente e password) che gli utenti possono usare per effettuare l'iscrizione all'applicazione.
- Attributi (ad esempio nome, codice postale, numero di scarpe e così via) da raccogliere dall'utente durante la procedura di iscrizione. 
- Uso dell'autenticazione a più fattori.
- Aspetto di tutte le pagine di iscrizione.
- Informazioni (visualizzate come attestazioni in un token) che l'applicazione riceve al termine dell'esecuzione dei criteri.

È possibile creare più criteri di tipi diversi nella propria directory e usarli nelle applicazioni in base alle esigenze. I criteri possono essere riutilizzati per più applicazioni. In questo modo, gli sviluppatori possono definire e modificare le esperienze di identità degli utenti con modifiche minime o senza modifiche al codice. Continueremo ad aggiungere tipi di criteri più avanzati al servizio.

Per usare i criteri, è disponibile una semplice interfaccia per sviluppatori. L'applicazione attiva i criteri tramite una richiesta di autenticazione HTTP standard (passando i parametri dei criteri nella richiesta) e riceve un token personalizzato come risposta. Ad esempio, l'unica differenza tra richiamare i criteri di iscrizione e i criteri di accesso sta nel nome dei criteri usato nel parametro della stringa di query "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

## Come creare i criteri di iscrizione

Per abilitare l'iscrizione nell'applicazione, sarà necessario creare i criteri di iscrizione. I criteri descrivono l'esperienza utente durante la procedura di iscrizione e il contenuto dei token che l'applicazione riceverà al completamento dell'iscrizione.

1. [Passare al pannello delle funzionalità B2C nel portale di anteprima di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **Criteri di iscrizione**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Il **Nome** determina il nome dei criteri di iscrizione usati dall'applicazione. Ad esempio, immettere "SiUp".
5. Fare clic su **Provider di identità** e selezionare "Indirizzo di posta elettronica". Facoltativamente, è anche possibile selezionare i provider di identità tramite social network, se già configurati. Fare clic su **OK**.
6. Fare clic su **Attributi iscrizione**. Qui è possibile scegliere gli attributi che si desidera raccogliere dall'utente durante l'iscrizione. Ad esempio, selezionare "Città", "Nome visualizzato" e "Codice postale". Fare clic su **OK**.
7. Fare clic su **Attestazioni applicazione**. Qui è possibile scegliere le attestazioni che verranno restituite all'applicazione nei token al termine di una corretta esperienza di iscrizione. Selezionare ad esempio "Nome visualizzato", "Provider di identità", "Codice postale", "L'utente è nuovo" e l'"ID oggetto dell'utente".
8. Fare clic su **Crea**. Si noti che il criterio appena creato è visualizzato come "**B2C\_1\_SiUp**" (il frammento **B2C\_1\_** viene aggiunto automaticamente come prefisso) nel pannello **Criteri di iscrizione**.
9. Aprire il criterio facendo clic su "**B2C\_1\_SiUp**".
10. Selezionare "Contoso B2C app" nel menu a discesa **Applicazioni** e `https://localhost:44321/` nel menu a discesa **URL di risposta / URI di reindirizzamento**. Fare clic sul pulsante **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di iscrizione per l'applicazione.

    > [AZURE.NOTE]La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.

## Come creare i criteri di accesso

Per abilitare l'accesso nell'applicazione, sarà necessario creare i criteri di accesso. I criteri descrivono l'esperienza utente durante la procedura di accesso e il contenuto dei token che l'applicazione riceverà al completamento dell'accesso.

1. [Passare al pannello delle funzionalità B2C nel portale di anteprima di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **Criteri di accesso**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Il **Nome** determina il nome dei criteri di accesso usati dall'applicazione. Ad esempio, immettere "SiIn".
5. Fare clic su **Provider di identità** e selezionare "Indirizzo di posta elettronica". Facoltativamente, è anche possibile selezionare i provider di identità tramite social network, se già configurati. Fare clic su **OK**.
6. Fare clic su **Attestazioni applicazione**. Qui è possibile scegliere le attestazioni che verranno restituite all'applicazione nei token al termine di una corretta esperienza di accesso. Selezionare ad esempio "Nome visualizzato", "Provider di identità", "Codice postale" e l'"ID oggetto dell'utente". Fare clic su **OK**.
7. Fare clic su **Crea**. Si noti che il criterio appena creato è visualizzato come "**B2C\_1\_SiIn**" (il frammento **B2C\_1\_** viene aggiunto automaticamente come prefisso) nel pannello **Criteri di accesso**.
8. Aprire il criterio facendo clic su "**B2C\_1\_SiIn**".
9. Selezionare "Contoso B2C app" nel menu a discesa **Applicazioni** e `https://localhost:44321/` nel menu a discesa **URL di risposta / URI di reindirizzamento**. Fare clic sul pulsante **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di accesso all'applicazione.

    > [AZURE.NOTE]La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.

## Come creare i criteri di modifica del profilo

Per abilitare la modifica del profilo nell'applicazione, è necessario creare i criteri di modifica del profilo. Questi criteri descrivono l'esperienza utente durante la procedura di modifica del profilo e il contenuto dei token che l'applicazione riceverà al completamento della procedura.

1. [Passare al pannello delle funzionalità B2C nel portale di anteprima di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **Criteri di modifica del profilo**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Il **Nome** determina il nome dei criteri di modifica del profilo usati dall'applicazione. Ad esempio, immettere "SiPe".
5. Fare clic su **Provider di identità** e selezionare "Indirizzo di posta elettronica". Facoltativamente, è anche possibile selezionare i provider di identità tramite social network, se già configurati. Fare clic su **OK**.
6. Fare clic su **Attributi profilo**. Qui è possibile scegliere gli attributi che l'utente potrà visualizzare e modificare. Ad esempio, selezionare "Città", "Nome visualizzato" e "Codice postale". Fare clic su **OK**.
7. Fare clic su **Attestazioni applicazione**. Qui è possibile scegliere le attestazioni che verranno restituite all'applicazione nei token al termine di una corretta esperienza di modifica del profilo. Ad esempio, selezionare "Nome visualizzato" e "Codice postale".
8. Fare clic su **Crea**. Si noti che il criterio appena creato è visualizzato come "**B2C\_1\_SiPe**" (il frammento **B2C\_1\_** viene aggiunto automaticamente come prefisso) nel pannello **Criteri di modifica del profilo**.
9. Aprire il criterio facendo clic su "**B2C\_1\_SiPe**".
10. Selezionare "Contoso B2C app" nel menu a discesa **Applicazioni** e `https://localhost:44321/` nel menu a discesa **URL di risposta / URI di reindirizzamento**. Fare clic sul pulsante **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di modifica del profilo nell'applicazione.

    > [AZURE.NOTE]La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.

<!---HONumber=Sept15_HO3-->