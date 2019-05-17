---
title: L'accesso Single sign-on - applicazioni non incluse nella raccolta - piattaforma delle identità di Microsoft | Microsoft Docs
description: Configura accesso single sign-on (SSO) alle applicazioni non incluse nella raccolta nella piattaforma delle identità Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 01/08/2019
ms.author: mimart
ms.reviewer: asmalser,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f55de2d081f12f1bddf7e68a795133c4b394823
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824994"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>Configurare single sign-on alle applicazioni non incluse nella raccolta nella piattaforma delle identità Microsoft

Questo articolo è una funzionalità che consente agli amministratori configurare single sign-on per le applicazioni non presentano nella raccolta di app di Microsoft identity platform *senza scrivere codice*.

Se si cercano invece indicazioni per gli sviluppatori sull'integrazione delle app personalizzate con Azure AD tramite il codice, vedere [Scenari di autenticazione per Azure AD](../develop/authentication-scenarios.md).

Raccolta di applicazioni di Microsoft identity platform fornisce un elenco di applicazioni che supportano una forma di single sign-on con la piattaforma di identità Microsoft, come descritto in [questo articolo](what-is-single-sign-on.md). Dopo aver trovato l'applicazione a cui connettersi, come specialista IT o come integratore di sistemi, è possibile iniziare seguendo le istruzioni dettagliate presentate nel portale di Azure per abilitare l'accesso Single Sign-On.

Sono disponibili anche queste funzionalità in base al contratto di licenza. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/active-directory/).

- Integrazione self-service di un'applicazione che usano un protocollo, ad esempio modern [OpenId Connect/OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) per autenticare gli utenti e ottenere i token per [Microsoft Graph](https://graph.microsoft.com).
- Integrazione self-service di qualsiasi applicazione che supporta i provider di identità SAML 2.0 (avviato dal provider di servizi o dal provider di identità)
- Integrazione self-service di qualsiasi applicazione Web con una pagina di accesso basata su HTML con [SSO basato su password](what-is-single-sign-on.md#password-based-sso)
- Connessione self-service di applicazioni che usano il protocollo SCIM per il provisioning dell'utente ([descritto qui](use-scim-to-provision-users-and-groups.md))
- Possibilità di aggiungere collegamenti a qualsiasi applicazione nell'[icona di avvio delle app di Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) o nel [riquadro di accesso di Azure AD](what-is-single-sign-on.md#linked-sign-on)

Possono essere incluse le applicazioni SaaS usate ma non ancora caricate nella raccolta di applicazioni di Azure AD, nonché le applicazioni Web di terze parti che l'organizzazione ha distribuito nei server sotto il suo controllo, nel cloud o in locale.

Queste funzionalità, note anche come *modelli di integrazione di app*, forniscono punti di connessione basati sugli standard per le app che supportano SAML, SCIM o l'autenticazione basata su moduli e includono opzioni e impostazioni flessibili per assicurare la compatibilità con numerosissime applicazioni.

## <a name="adding-an-unlisted-application"></a>Aggiunta di un'applicazione non pubblicata

La piattaforma Microsoft Identity offre due meccanismi per registrare le applicazioni.

Un'applicazione che usa un protocollo, ad esempio modern [OpenId Connect/OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) per autenticare gli utenti viene registrato usando la [registrazione dell'applicazione portale](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-register-an-app).

Registrazione delle applicazioni che usano tutti gli altri tipi di [supportati meccanismi di autenticazione](what-is-single-sign-on.md) , ad esempio le [SAML](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol) uso del protocollo il **applicazioni aziendali** pannello a collegare se stessi con la piattaforma delle identità Microsoft.

Per connettere un'applicazione usando un modello di integrazione di app, accedere al portale di Azure con l'account di amministratore di Microsoft identity platform. Passare alla sezione **Azure Active Directory > Applicazioni aziendali > Nuova applicazione > Applicazione non nella raccolta** e selezionare **Aggiungi**, quindi **Aggiungere un'applicazione dalla raccolta**.

![Aggiungi applicazione](./media/configure-single-sign-on-non-gallery-applications/customapp1.png)

Nella raccolta di applicazioni è possibile aggiungere un'applicazione non pubblicata selezionando il riquadro **Applicazione non nella raccolta** visualizzato nei risultati della ricerca, se l'applicazione cercata non è stata trovata. Dopo aver immesso un nome per l'applicazione, è possibile configurare le opzioni di comportamento e Single Sign-On.

> [!TIP]
> come procedura consigliata, usare la funzione di ricerca per verificare se l'applicazione esiste già nella raccolta di applicazioni. Se l'applicazione viene trovata e la relativa descrizione indica Single Sign-On, l'applicazione è già supportata per l'accesso Single Sign-On federato.

![Ricerca](./media/configure-single-sign-on-non-gallery-applications/customapp2.png)

La procedura per l'aggiunta di un'applicazione non pubblicata è simile a quella disponibile per le applicazioni preintegrate. Per iniziare, selezionare **Configura Single Sign-On** o fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione. Nella schermata successiva sono disponibili le opzioni per la configurazione del singolo punto di accesso. Queste opzioni vengono descritte nelle sezioni successione di questo articolo.
  
![Opzioni di configurazione](./media/configure-single-sign-on-non-gallery-applications/customapp3.png)

## <a name="saml-based-single-sign-on"></a>Accesso Single Sign-On basato su SAML

Selezionare questa opzione per configurare l'autenticazione basata su SAML per l'applicazione. Per questa opzione è necessario che l'applicazione supporti SAML 2.0. Prima di continuare è consigliabile raccogliere informazioni sull'uso delle funzionalità SAML dell'applicazione. Completare le sezioni seguenti per configurare Single Sign-On tra Azure AD e l'applicazione.

### <a name="enter-basic-saml-configuration"></a>Immettere la configurazione SAML di base

Per configurare Azure AD, immettere la configurazione SAML di base. È possibile immettere manualmente i valori o caricare un file di metadati per estrarre il valore dei campi.

  ![URL e dominio di Litware](./media/configure-single-sign-on-non-gallery-applications/customapp4.png)

- **URL di accesso (avviato da SP solo)** cui l'utente accede per accedere a questa applicazione. Se l'applicazione è configurata per l'esecuzione del servizio avviato dal provider di accesso single sign-on, quindi quando un utente passa a questo URL, il provider di servizi eseguirà il reindirizzamento necessario ad Azure AD per l'autenticazione e accesso dell'utente. Se questo campo è popolato, Azure AD userà questo URL per avviare l'applicazione da Office 365 e il pannello di accesso di AD Azure. Se questo campo è omesso, quindi Azure AD eseguirà invece il provider di identità-avviato sign-on quando l'app viene avviata da Office 365, il pannello di accesso di Azure AD o dall'Azure AD single sign-on di URL (possono essere copiati dalla scheda Dashboard).
- **Identificatore**: deve identificare in modo univoco l'applicazione per cui viene configurato l'accesso Single Sign-On. È possibile trovare questo valore come elemento di autorità di certificazione in AuthRequest (richiesta SAML) inviato dall'applicazione. Questo valore viene inoltre visualizzato come **ID entità** in tutti i metadati SAML forniti dall'applicazione. Per informazioni dettagliate sui valori di ID entità o Audience, controllare la documentazione SAML dell'applicazione. 

    Di seguito è riportato un esempio di come l'identificatore o l'autorità di certificazione viene visualizzato nella richiesta SAML inviata dall'applicazione ad Azure AD:

    ```XML
    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>
    ```

- **URL di risposta** : è l'indirizzo a cui l'applicazione prevede di ricevere il token SAML. Viene anche definito URL del servizio consumer di asserzione (ACS). Per informazioni dettagliate sull'URL ACS o sull'URL di risposta del token SAML, controllare la documentazione SAML dell'applicazione. 

    Per configurare più replyURL, è possibile usare il seguente script PowerShell.

    ```powershell
    $sp = Get-AzureADServicePrincipal -SearchString "<Exact App  name>"
    $app = Get-AzureADApplication -SearchString "<Exact app name>"
    Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls "<ReplyURLs>"
    Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls "<ReplyURLs>"
    ```

Per altre informazioni, vedere [le richieste di autenticazione SAML 2.0 e le risposte che supporta la piattaforma delle identità di Microsoft (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)


### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Esaminare o personalizzare le attestazioni rilasciate nel token SAML

Quando un utente esegue l'autenticazione all'applicazione, Azure AD rilascia un token SAML all'applicazione contenente informazioni (o attestazioni) sull'utente che lo identificano in modo univoco. Per impostazione predefinita sono inclusi il nome utente, l'indirizzo di posta elettronica, il nome e il cognome dell'utente. 

Le attestazioni inviate all'applicazione nel token SAML possono essere visualizzate o modificate nella scheda **Attributi** .

  ![Attributi](./media/configure-single-sign-on-non-gallery-applications/customapp7.png)

I due motivi per cui potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML sono i seguenti:

- L'applicazione è stata scritta per richiedere un set di URI attestazione o di valori attestazione diverso.
- L'applicazione è stata distribuita in modo da richiede un'attestazione NameIdentifier sia diversa dal nome utente (vale a dire, nome dell'entità utente) archiviato nella piattaforma delle identità Microsoft.

Per altre informazioni, vedere [Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure Active Directory](./../develop/../develop/active-directory-saml-claims-customization.md). 

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Esaminare la data di scadenza del certificato, lo stato e la notifica tramite posta elettronica.

Quando si crea un'applicazione nella raccolta o non nella raccolta, Azure AD crea un certificato specifico dell'applicazione con una data di scadenza di tre anni a partire dalla data di creazione. Per configurare il trust tra Azure AD e l'applicazione, è necessario questo certificato. Per informazioni dettagliate sul formato del certificato, vedere la documentazione sul protocollo SAML dell'applicazione. 

Da Azure AD è possibile scaricare il certificato in formato non elaborato o Base64. È possibile ottenere il certificato anche scaricando il file XML dei metadati dell'applicazione o usando l'URL dei metadati della federazione dell'app.

  ![Certificato](./media/configure-single-sign-on-non-gallery-applications/certificate.png)

Verificare che il certificato abbia:

- La data di scadenza desiderata. È possibile configurare la data di scadenza per un periodo massimo di tre anni.
- Lo stato attivo. Se lo stato è inattivo, modificarlo in attivo. Per modificare lo stato, selezionare **Attivo** e quindi salvare la configurazione. 
- Il messaggio di notifica corretto. Quando la data del certificato attivo si avvicina alla scadenza, Azure AD invierà una notifica all'indirizzo di posta elettronica configurato in questo campo.  

Per altre informazioni, vedere [gestire i certificati per single sign-on federato nella piattaforma delle identità Microsoft](manage-certificates-for-federated-single-sign-on.md).

### <a name="set-up-target-application"></a>Configurare l'applicazione di destinazione

Per configurare l'applicazione per un singolo punto di accesso, vedere la documentazione dell'applicazione. Per trovare la documentazione, scorrere fino alla fine della pagina di configurazione basato su SAML sign-on e quindi fare clic su **Configure \<nome applicazione >**. 

I valori richiesti variano in base all'applicazione. Per informazioni dettagliate, vedere la documentazione sul protocollo SAML dell'applicazione. L'URL servizio Single Sign-On e l'URL servizio Sign-Out vengono entrambi risolti nello stesso endpoint, ovvero l'endpoint di gestione delle richieste SAML per questa istanza di Azure AD. L'ID di entità SAML è il valore visualizzato come Autorità di certificazione nel token SAML rilasciato all'applicazione.


### <a name="assign-users-and-groups-to-your-saml-application"></a>Assegnare gli utenti e i gruppi all'applicazione SAML

Dopo aver configurato l'applicazione per l'uso di Azure AD come provider di identità basato su SAML, l'applicazione è quasi pronta per il test. Come controllo di sicurezza, Azure AD non rilascia un token che consente all'utente di accedere all'applicazione, a meno che Azure AD non abbia concesso l'accesso all'utente. Agli utenti l'accesso può essere concesso direttamente o tramite un gruppo di appartenenza. 

Per assegnare un utente o un gruppo all'applicazione, scegliere il pulsante **Assegna utenti** . Selezionare l'utente o il gruppo da assegnare e quindi fare clic sul pulsante **Assegna** .

  ![Assegna utenti](./media/configure-single-sign-on-non-gallery-applications/customapp6.png)

L'assegnazione di un utente consentirà ad Azure AD di emettere un token per l'utente. Inoltre viene visualizzato un riquadro per l'applicazione nel pannello di accesso dell'utente. Se l'utente usa Office 365, il riquadro per l'applicazione viene visualizzato anche nell'utilità di avvio applicazioni di Office 365. 

> [!NOTE] 
> È possibile caricare un logo icona dell'applicazione usando il pulsante **Carica Logo** nella scheda **Configura** dell'applicazione. 


### <a name="test-the-saml-application"></a>Testare l'applicazione SAML

Prima di eseguire il test dell'applicazione SAML, è necessario configurare l'applicazione con Azure AD e gli utenti o i gruppi assegnati all'applicazione. Per testare l'applicazione SAML, vedere [come eseguire il debug basato su SAML single sign-on alle applicazioni nella piattaforma delle identità Microsoft](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Password Single Sign-On

Selezionare questa opzione per configurare il [Single Sign-On basato su password](what-is-single-sign-on.md) per un'applicazione Web con una pagina di accesso HTML. L'SSO basato su password, definito anche insieme di credenziali delle password, consente di gestire l'accesso degli utenti e le password per le applicazioni Web che non supportano la federazione delle identità. Risulta utile anche negli scenari in cui più utenti devono condividere un unico account, ad esempio agli account di app di social media dell'organizzazione. 

Dopo aver selezionato **Avanti**, viene richiesto di immettere l'URL della pagina di accesso basata sul Web dell'applicazione. Questa deve essere la pagina che include i campi di input per nome utente e password. Dopo l'immissione, Azure AD avvia un processo di analisi della pagina di accesso per cercare un nome utente e una password immessi. Se il processo non riesce, viene avviato un processo alternativo per installare un'estensione del browser (richiede Internet Explorer, Chrome o Firefox) che consente di acquisire manualmente i campi.

Dopo che la pagina di accesso è stata acquisita, è possibile assegnare utenti e gruppi ed è possibile impostare le credenziali come nelle normali [app di SSO basato su password](what-is-single-sign-on.md).

> [!NOTE] 
> È possibile caricare un logo icona dell'applicazione usando il pulsante **Carica Logo** nella scheda **Configura** dell'applicazione. 
>

## <a name="existing-single-sign-on"></a>Single Sign-On esistente
Selezionare questa opzione per aggiungere un collegamento a un'applicazione nel riquadro di accesso di Azure AD o nel portale di Office 365 dell'organizzazione. È possibile usare questa opzione per aggiungere collegamenti ad app Web personalizzate che usano Active Directory Federation Services (o un altro servizio federativo) anziché Azure AD per l'autenticazione. In alternativa, è possibile aggiungere collegamenti diretti a pagine specifiche di SharePoint o ad altre pagine Web da visualizzare nei riquadri di accesso dell'utente. 

Dopo aver selezionato **Avanti**, viene richiesto di immettere l'URL dell'applicazione da collegare. Al termine sarà possibile assegnare utenti e gruppi all'applicazione e questa verrà visualizzata nell'[icona di avvio delle app di Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) o nel [riquadro di accesso di Azure AD](end-user-experiences.md) per tali utenti.

> [!NOTE] 
> È possibile caricare un logo icona dell'applicazione usando il pulsante **Carica Logo** nella scheda **Configura** dell'applicazione. 
>

## <a name="related-articles"></a>Articoli correlati

- [Personalizzazione delle attestazioni rilasciate nel token SAML per le app preintegrate](../develop/active-directory-saml-claims-customization.md)
- [Risoluzione dei problemi dell'accesso Single Sign-On basato su SAML](../develop/howto-v1-debug-saml-sso-issues.md)
- [Piattaforma delle identità di Microsoft (Azure Active Directory per gli sviluppatori](https://aka.ms/aaddev)
