---
title: 'Azure Active Directory B2C: risoluzione dei problemi dei criteri personalizzati | Microsoft Docs'
description: Guida a diversi approcci per risolvere gli errori nei criteri personalizzati
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 0f783062cd674edb0b6b49660f5aee69af4b3adb
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---
# <a name="azure-active-directory-b2c-custom-policies-and-identity-experience-framework-troubleshooting"></a>Risoluzione dei problemi del framework di esperienza di gestione delle identità e criteri personalizzati di Azure Active Directory B2C
## <a name="the-basics"></a>Nozioni di base

Gli sviluppatori di identità che usano i criteri personalizzati di Azure AD B2C hanno il difficile compito di configurare il framework di esperienza di gestione delle identità nel corrispondente linguaggio dei criteri in formato XML.  Questa guida comprende un elenco di strumenti consigliati e di suggerimenti per individuare e risolvere rapidamente i problemi.  Imparare a scrivere criteri personalizzati è come apprendere un nuovo linguaggio.  
*Questo articolo illustra come risolvere i problemi di configurazione dei criteri personalizzati di Azure AD B2C e non quelli dell'applicazione relying party o della libreria di identità.*



## <a name="xml-editing-inproperly-formatted-xml-is-the-most-common-error"></a>Dal punto di vista della modifica XML, un codice XML non formattato correttamente è l'errore più comune.

Un valido editor XML, che visualizza il codice XML in modo nativo, applica un colore specifico ai contenuti, precompila i termini comuni, mantiene indicizzati gli elementi XML e riesce a eseguire la convalida con lo schema, è pressoché indispensabile.  I due seguenti sono tra i preferiti.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Notepad++](https://notepad-plus-plus.org/)

Ottenere la definizione dell'XML Schema `TrustFrameworkPolicy_0.3.0.0.xsd` dalla cartella radice dello starter pack. La convalida dell'XML Schema identifica gli errori prima del caricamento.  Nella documentazione dell'editor XML cercare `XML tools` e `XML Validation`

Può essere molto utile esaminare rapidamente le regole del linguaggio XML perché Azure AD B2C rifiuterà gli eventuali errori di formattazione rilevati.  Un codice XML non correttamente formattato a volte può restituire messaggi di errore fuorvianti.

## <a name="uploading-policies-and-policy-validation"></a>Convalida dei criteri e del caricamento dei criteri

 La convalida del **caricamento** è automatica e la maggior parte degli errori causa l'interruzione di un caricamento.  **Tenere presente che la convalida include il file dei criteri da caricare e la catena di file a cui si riferisce.  `RP policy file > Extensions file > Base File` Gli errori di convalida più comuni sono:

Frammento con errore: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* L'elemento ClaimType potrebbe non essere scritto correttamente oppure non esiste nello schema.
* Gli elementi ClaimType devono essere definiti in almeno uno dei file nel criterio.  Ad esempio ` <ClaimType Id="socialIdpUserId">`
* Se l'elemento ClaimType è definito nel file delle estensioni, ma è usato in TechnichalProfile nel file di base, il caricamento del file di base restituirà un errore.

Frammento con errore: `...males a reference to a ClaimsTransformation with id...`
* Come sopra.

Frammento con errore: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Controllare che TenantId negli elementi **<TrustFrameworkPolicy>** e **<BasePolicy>** corrisponda al tenant B2C di destinazione.  



## <a name="runtime-troubleshooting"></a>Risoluzione dei problemi di runtime

* Usare `Run Now` e `https://jwt.io` per testare i criteri indipendentemente dall'applicazione Web o per dispositivi mobili. Questo sito Web agisce da applicazione relying party e visualizza i contenuti del token JWT generato dai criteri Azure AD B2C.  Per creare un'applicazione di test nel framework di esperienza di gestione delle identità.
    * Nome: TestApp
    * App Web/API Web: no
    * Client nativo: no

* Usare [Fiddler](http://www.telerik.com/fiddler) per tracciare lo scambio di messaggi tra il browser client e Azure AD B2C.  Si otterrà un'indicazione del punto in cui, nei passaggi di orchestrazione, il percorso utente non è riuscito.

* Usare **Application Insights** in **Modalità sviluppo** per tracciare il comportamento del percorso utente nel framework di esperienza di gestione delle identità.  In **Modalità sviluppo** è possibile osservare lo scambio di attestazioni tra il framework di esperienza di gestione delle identità e i diversi provider di attestazioni definiti da TechnicalProfiles, ad esempio provider di identità, servizi basati su API, la directory dell'utente di Azure AD B2C (directory di AAD) e altri servizi come Multi-Factor Authentication.  

## <a name="recommended-practies"></a>Procedure consigliate

**Mantenere più versioni degli scenari e raggrupparle in un progetto con l'applicazione.** I file di base, delle estensioni e della relying party (RP) sono reciprocamente dipendenti. Salvarli come gruppo e mantenere versioni di lavoro separate quando vengono realizzate nuove funzionalità nei criteri.  Inserirli temporaneamente nel file system con il codice dell'applicazione con cui interagiscono.  Le applicazioni possono richiamare criteri RP diversi in un tenant e diventare dipendenti dalle attestazioni previste dai criteri di Azure AD B2C.

**Sviluppare e testare i profili tecnici con percorsi utente noti.**  Usare criteri dello starter pack testati per configurare i profili tecnici e testarli separatamente prima di incorporarli nei percorsi utente

**Sviluppare e testare i percorsi utente con i profili tecnici testati** Modificare i passaggi di orchestrazione di un percorso utente un passaggio alla volta, creando gradualmente gli scenari desiderati







Ecco come procedere:

1. Scaricare "active-directory-b2c-custom-policy-starterpack" da GitHub.  [Scaricare il file zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o eseguire
### <a name="built-in"></a>Predefinito



Il framework di criteri estendibile di Azure Active Directory (Azure AD) B2C è il punto di forza del servizio. I criteri descrivono le esperienze di identità utente, ad esempio iscrizione, accesso o modifica del profilo. Ad esempio, i criteri di iscrizione consentono di controllare i comportamenti configurando le impostazioni seguenti:

* Tipi di account (account di social network come Facebook o account locali come un indirizzo di posta elettronica) che gli utenti possono usare per effettuare l'iscrizione all'applicazione.
* Attributi (ad esempio nome, codice postale, numero di scarpe) da raccogliere dall'utente durante la procedura di iscrizione.
* Uso di Multi-Factor Authentication.
* Aspetto di tutte le pagine di iscrizione.
* Informazioni (visualizzate come attestazioni in un token) che l'applicazione riceve al termine dell'esecuzione dei criteri.

È possibile creare più criteri di tipi diversi nel proprio tenant e usarli nelle applicazioni in base alle esigenze. I criteri possono essere usati per più applicazioni. In questo modo, gli sviluppatori possono definire e modificare le esperienze di identità degli utenti con modifiche minime o senza modifiche al codice.

Per usare i criteri, è disponibile una semplice interfaccia per sviluppatori. L'applicazione attiva i criteri tramite una richiesta di autenticazione HTTP standard (passando i parametri dei criteri nella richiesta) e riceve un token personalizzato come risposta. Ad esempio, l'unica differenza tra le richieste che richiamano i criteri di iscrizione e i criteri di accesso risiede nel nome del criterio usato nel parametro della stringa di query "p":

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

Per altre informazioni sul framework di criteri, vedere questo [post di blog](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-policy"></a>Creare un criterio di iscrizione
Per abilitare l'iscrizione nell'applicazione, è necessario creare i criteri di iscrizione. I criteri descrivono l'esperienza utente durante la procedura di iscrizione e il contenuto dei token che l'applicazione riceverà al completamento dell'iscrizione.

1. [Seguire questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **Criteri di iscrizione**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Il **Nome** determina il nome dei criteri di iscrizione usati dall'applicazione. Ad esempio, immettere "SiUp".
5. Fare clic su **Provider di identità** e selezionare "Iscrizione posta elettronica". Facoltativamente, è anche possibile selezionare i provider di identità tramite social network, se già configurati. Fare clic su **OK**.
6. Fare clic su **Attributi iscrizione**. Qui è possibile scegliere gli attributi che si desidera raccogliere dall'utente durante l'iscrizione. Ad esempio, selezionare "Paese/Area", "Nome visualizzato" e "Codice postale". Fare clic su **OK**.
7. Fare clic su **Attestazioni applicazione**. Qui si scelgono le attestazioni che verranno restituite all'applicazione nei token inviati al termine di una corretta esperienza di iscrizione. Selezionare ad esempio "Nome visualizzato", "Provider di identità", "Codice postale", "L'utente è nuovo" e l'"ID oggetto dell'utente".
8. Fare clic su **Crea**. Si noti che il criterio appena creato è visualizzato come "**B2C_1_SiUp**" (il frammento **B2C\_1\_** viene aggiunto automaticamente) nel pannello **Criteri di iscrizione**.
9. Aprire il criterio facendo clic su "**B2C_1_SiUp**".
10. Selezionare "Contoso B2C app" nel menu a discesa **Applicazioni`https://localhost:44321/` e**  nel menu a discesa **URL di risposta / URI di reindirizzamento**.
11. Fare clic su **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di iscrizione per l'applicazione.
    
    > [!NOTE]
    > La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
    > 
    > 

## <a name="create-a-sign-in-policy"></a>Creare un criterio di accesso
Per abilitare l'accesso nell'applicazione, è necessario creare i criteri di accesso. I criteri descrivono l'esperienza utente durante la procedura di accesso e il contenuto dei token che l'applicazione riceverà al completamento dell'accesso.

1. [Seguire questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **Criteri di accesso**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Il **Nome** determina il nome dei criteri di accesso usati dall'applicazione. Ad esempio, immettere "SiIn".
5. Fare clic su **Provider di identità** e selezionare "Accesso all'account locale". Facoltativamente, è anche possibile selezionare i provider di identità tramite social network, se già configurati. Fare clic su **OK**.
6. Fare clic su **Attestazioni applicazione**. Qui è possibile scegliere le attestazioni che verranno restituite all'applicazione nei token inviati al termine di una corretta esperienza di accesso. Selezionare ad esempio "Nome visualizzato", "Provider di identità", "Codice postale" e l'"ID oggetto dell'utente". Fare clic su **OK**.
7. Fare clic su **Crea**. Si noti che il criterio appena creato è visualizzato come "**B2C_1_SiIn**" (il frammento **B2C\_1\_** viene aggiunto automaticamente come prefisso) nel pannello **Criteri di accesso**.
8. Aprire i criteri facendo clic su "**B2C_1_SiIn**".
9. Selezionare "Contoso B2C app" nel menu a discesa **Applicazioni`https://localhost:44321/` e**  nel menu a discesa **URL di risposta/URI di reindirizzamento**.
10. Fare clic su **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di accesso all'applicazione.
    
    > [!NOTE]
    > La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
    > 
    > 

## <a name="create-a-sign-up-or-sign-in-policy"></a>Creare un criterio di iscrizione o accesso
Questo criterio consente di gestire le esperienze di iscrizione e accesso dei consumatori tramite una singola configurazione. I consumatori vengono indirizzati sul percorso corretto (iscrizione o accesso) a seconda del contesto. Vengono inoltre descritti i contenuti dei token che l'applicazione riceverà al completamento dell'iscrizione o dell'accesso.  Un esempio di codice per i criteri di iscrizione o di accesso è [disponibile qui](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

1. [Seguire questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **Criteri di iscrizione o di accesso**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Il **Nome** determina il nome dei criteri di iscrizione usati dall'applicazione. Ad esempio, immettere "SiUpIn".
5. Fare clic su **Provider di identità** e selezionare "Iscrizione posta elettronica". Facoltativamente, è anche possibile selezionare i provider di identità tramite social network, se già configurati. Fare clic su **OK**.
6. Fare clic su **Attributi iscrizione**. Qui è possibile scegliere gli attributi che si desidera raccogliere dall'utente durante l'iscrizione. Ad esempio, selezionare "Paese/Area", "Nome visualizzato" e "Codice postale". Fare clic su **OK**.
7. Fare clic su **Attestazioni applicazione**. Qui è possibile scegliere le attestazioni che verranno restituite all'applicazione nei token inviati al termine di una corretta esperienza di iscrizione o accesso. Selezionare ad esempio "Nome visualizzato", "Provider di identità", "Codice postale", "L'utente è nuovo" e l'"ID oggetto dell'utente".
8. Fare clic su **Crea**. Si noti che il criterio appena creato è visualizzato come "**B2C_1_SiUpIn**" (il frammento **B2C\_1\_** viene aggiunto automaticamente) nel pannello **Criteri di iscrizione o di accesso**.
9. Aprire il criterio facendo clic su "**B2C_1_SiUpIn**".
10. Selezionare "Contoso B2C app" nel menu a discesa **Applicazioni`https://localhost:44321/` e**  nel menu a discesa **URL di risposta/URI di reindirizzamento**.
11. Fare clic su **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di iscrizione o accesso all'applicazione, a seconda della configurazione.
    
    > [!NOTE]
    > La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
    > 
    > 

## <a name="create-a-profile-editing-policy"></a>Creare i criteri di modifica del profilo
Per abilitare la modifica del profilo nell'applicazione, è necessario creare i criteri di modifica del profilo. Questi criteri descrivono l'esperienza utente durante la procedura di modifica del profilo e il contenuto dei token che l'applicazione riceverà al completamento della procedura.

1. [Seguire questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **Criteri di modifica del profilo**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Il **Nome** determina il nome dei criteri di modifica del profilo usati dall'applicazione. Ad esempio, immettere "SiPe".
5. Fare clic su **Provider di identità** e selezionare "Accesso all'account locale". Facoltativamente, è anche possibile selezionare i provider di identità tramite social network, se già configurati. Fare clic su **OK**.
6. Fare clic su **Attributi profilo**. Qui è possibile scegliere gli attributi che l'utente potrà visualizzare e modificare. Ad esempio, selezionare "Paese/Area", "Nome visualizzato" e "Codice postale". Fare clic su **OK**.
7. Fare clic su **Attestazioni applicazione**. Qui si scelgono le attestazioni che verranno restituite all'applicazione nei token inviati al termine di una corretta esperienza di modifica del profilo. Ad esempio, selezionare "Nome visualizzato" e "Codice postale".
8. Fare clic su **Crea**. Si noti che il criterio appena creato è visualizzato come "**B2C_1_SiPe**" (il frammento **B2C\_1\_** viene aggiunto automaticamente) nel pannello **Criteri di modifica del profilo**.
9. Aprire il criterio facendo clic su "**B2C_1_SiPe**".
10. Selezionare "Contoso B2C app" nel menu a discesa **Applicazioni`https://localhost:44321/` e**  nel menu a discesa **URL di risposta/URI di reindirizzamento**.
11. Fare clic su **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di modifica del profilo nell'applicazione.
    
    > [!NOTE]
    > La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
    > 
    > 

## <a name="create-a-password-reset-policy"></a>Creare i criteri di reimpostazione delle password
Per abilitare una reimpostazione dettagliata delle password nell'applicazione, è necessario creare criteri di reimpostazione delle password. Si noti che l'opzione di reimpostazione delle password a livello di tenant specificata [qui](active-directory-b2c-reference-sspr.md) è comunque applicabile per i criteri di accesso. Questi criteri descrivono l'esperienza utente durante la procedura di reimpostazione delle password e il contenuto dei token che l'applicazione riceverà al completamento della procedura.

1. [Seguire questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **Criteri di reimpostazione password**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. **Nome** indica il nome dei criteri di reimpostazione delle password usati dall'applicazione. Ad esempio, immettere "SSPR".
5. Fare clic su **Provider di identità** e selezionare "Reimposta la password usando l'indirizzo di posta elettronica". Fare clic su **OK**.
6. Fare clic su **Attestazioni applicazione**. Qui si scelgono le attestazioni che verranno restituite all'applicazione nei token inviati al termine di una corretta esperienza di reimpostazione delle password. Ad esempio, selezionare "ID oggetto dell'utente".
7. Fare clic su **Crea**. Si noti che il criterio appena creato viene visualizzato come "**B2C_1_SSPR**" (il frammento **B2C\_1\_** viene aggiunto automaticamente) nel pannello **Criteri di reimpostazione password**.
8. Aprire il criterio facendo clic su "**B2C_1_SSPR**".
9. Selezionare "Contoso B2C app" nel menu a discesa **Applicazioni`https://localhost:44321/` e**  nel menu a discesa **URL di risposta/URI di reindirizzamento**.
10. Fare clic su **Esegui adesso**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di reimpostazione delle password nell'applicazione.
    
    > [!NOTE]
    > La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
    > 
    > 

## <a name="how-to-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Come collegare un criterio di iscrizione o di accesso a un criterio di reimpostazione di una password?
Quando si crea un criterio di iscrizione o accesso (con gli account locali), il consumer visualizzerà un messaggio "Password dimenticata?" collegarsi alla prima pagina dell'esperienza. Facendo clic su questo collegamento non si attiva automaticamente un criterio di reimpostazione della password. Al contrario, viene restituito all'applicazione un codice di errore specifico `AADB2C90118`. L'applicazione deve gestire questa situazione e richiamare un criterio di reimpostazione della password specifico. Un esempio che illustra questo approccio di connettere tra loro i criteri si trova [qui](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Configurazione di token, sessione e accesso Single Sign-On](active-directory-b2c-token-session-sso.md).
* [Disabilitare la verifica della posta elettronica durante l'iscrizione dell'utente](active-directory-b2c-reference-disable-ev.md)


