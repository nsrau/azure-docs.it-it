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
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95b7cbcf4e485dc93d49b9559dcb7d0d4f597ebe
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550343"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>Configurare single sign-on alle applicazioni non incluse nella raccolta nella piattaforma delle identità Microsoft

Questo articolo è una funzionalità che consente agli amministratori di configurare single sign-on per le applicazioni non presente nella raccolta di app di Microsoft identity platform *senza scrivere codice*.

Se si cercano invece indicazioni per gli sviluppatori sull'integrazione delle app personalizzate con Azure AD tramite il codice, vedere [Scenari di autenticazione per Azure AD](../develop/authentication-scenarios.md).

Raccolta di applicazioni di Microsoft identity platform fornisce un elenco di applicazioni che supportano una forma di single sign-on con la piattaforma di identità Microsoft, come descritto in [questo articolo](what-is-single-sign-on.md). Dopo aver trovato l'applicazione a cui connettersi, come specialista IT o come integratore di sistemi, è possibile iniziare seguendo le istruzioni dettagliate presentate nel portale di Azure per abilitare l'accesso Single Sign-On.

Le funzionalità seguenti sono disponibili, anche in base al contratto di licenza. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/active-directory/).

- Integrazione self-service di un'applicazione che usano un protocollo, ad esempio modern [OpenId Connect/OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) per autenticare gli utenti e ottenere i token per [Microsoft Graph](https://graph.microsoft.com).
- Integrazione self-service di qualsiasi applicazione che supporta [Security Assertion Markup Language (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) provider di identità (avviato da SP o avviata da IdP)
- Integrazione self-service di qualsiasi applicazione Web con una pagina di accesso basata su HTML con [SSO basato su password](what-is-single-sign-on.md#password-based-sso)
- Connessione self-service delle applicazioni che usano il [System per il protocollo di Cross-Domain Identity Management (SCIM) per il provisioning utenti](use-scim-to-provision-users-and-groups.md)
- Possibilità di aggiungere collegamenti a qualsiasi applicazione nell'[icona di avvio delle app di Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) o nel [riquadro di accesso di Azure AD](what-is-single-sign-on.md#linked-sign-on)

Queste funzionalità possono includere integrazione self-service di un software come un'applicazione di servizio (SaaS) che si usa, anche se nessun utente ha eseguito l'onboarding ancora l'applicazione alla raccolta di applicazioni Azure AD. Un'altra funzionalità sia di integrazione self-service di un'applicazione web di terze parti che l'organizzazione ha distribuito ai server di che controllo, nel cloud o in locale.

Noto anche come *modelli di integrazione di app*, queste funzionalità forniscono punti di connessione basati sugli standard per le applicazioni che supportano SAML, SCIM o l'autenticazione basata su form. Le funzionalità includono opzioni e impostazioni per la compatibilità con numerosissime applicazioni flessibili.

## <a name="adding-an-unlisted-application"></a>Aggiunta di un'applicazione non pubblicata

La piattaforma delle identità Microsoft offre due meccanismi per registrare le applicazioni.

Un'applicazione che usa un protocollo, ad esempio modern [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) per autenticare gli utenti è registrato con il [portale di registrazione dell'applicazione](../develop/quickstart-register-app.md).

Registrare le applicazioni utilizzando tutti gli altri tipi di [supportati meccanismi di autenticazione](what-is-single-sign-on.md), ad esempio le [SAML](../develop/single-sign-on-saml-protocol.md) protocollo, usare il **applicazioni aziendali** pannello per la connessione esse con la piattaforma delle identità Microsoft.

Per connettere un'applicazione non pubblicata usando un modello di integrazione di app, eseguire questi passaggi:

1. Accedi per il [portale di Azure Active Directory](https://aad.portal.azure.com/) con l'account di amministratore di Microsoft identity platform.
2. Selezionare **applicazioni aziendali** > **nuova applicazione**.
3. (Facoltativo ma consigliato) Nel **Aggiungi dalla raccolta** casella di ricerca, immettere il nome visualizzato dell'applicazione. Se l'applicazione viene visualizzato nei risultati della ricerca, selezionarlo e ignorare il resto di questa procedura.
4. Selezionare **applicazione Non nella raccolta**. Il **aggiungere la propria applicazione** verrà visualizzata la pagina.

   ![Aggiunta di un'applicazione](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Immettere il nome visualizzato per la nuova applicazione.
6. Selezionare **Aggiungi**.

Tramite l'aggiunta di un'applicazione in questo modo, si offrono un'esperienza simile a quello disponibile per le applicazioni preintegrate. Selezionare innanzitutto **l'accesso Single sign-on** dalla barra laterale dell'applicazione. Nella pagina successiva (**selezionare un metodo single sign-on**) presenta le opzioni per la configurazione dell'accesso SSO:

- **SAML**
- **Basato su password**
- **Collegato**

![Selezionare un metodo di accesso Single Sign-On](./media/configure-single-sign-on-non-gallery-applications/select-a-single-sign-on-method.png)

Per altre informazioni su queste opzioni, vedere le sezioni seguenti di questo articolo.

## <a name="saml-based-single-sign-on"></a>Accesso Single Sign-On basato su SAML

Selezionare il **SAML** opzione per configurare l'autenticazione basata su SAML per l'applicazione. (Questa opzione richiede che l'applicazione supporta SAML 2.0). Il **impostata su Single Sign-On con SAML** verrà visualizzata la pagina.

![Configurare single sign-on con SAML](./media/configure-single-sign-on-non-gallery-applications/set-up-single-sign-on-with-saml.png)

Questa pagina include cinque voci diverse:

| Numero di intestazione | Nome dell'intestazione | Per un riepilogo di questa intestazione, vedere: |
| --- | --- | --- |
| 1 | **Configurazione SAML di base** | [Immettere basic configurazione SAML](#enter-basic-saml-configuration) |
| 2 | **Gli attributi utente e le attestazioni** | [Revisione o personalizzazione delle attestazioni rilasciate nel token SAML](#review-or-customize-the-claims-issued-in-the-saml-token) |
| 3 | **Certificato di firma SAML** | [Data di scadenza certificato revisione e stato notifica tramite posta elettronica](#review-certificate-expiration-data-status-and-email-notification) |
| 4 | **Configurare \<nome applicazione >** | [Configurare l'applicazione di destinazione](#set-up-target-application) |
| 5 | **Testare single sign-on con \<nome applicazione >** | [Testare l'applicazione SAML](#test-the-saml-application) |

Ora è possibile raccogliere informazioni su come usare le funzionalità SAML dell'applicazione prima di continuare. Completare le sezioni seguenti per configurare l'accesso SSO tra Azure AD e l'applicazione.

### <a name="enter-basic-saml-configuration"></a>Immettere la configurazione SAML di base

Per configurare Azure AD, passare al **base SAML Configuration** sull'intestazione e selezionare relativo **modificare** icona (una matita). È possibile immettere manualmente i valori o caricare un file di metadati per estrarre il valore dei campi.

![Configurazione di base SAML](./media/configure-single-sign-on-non-gallery-applications/basic-saml-configuration.png)

Sono necessari i seguenti due campi:

- **Identificatore**. Questo valore deve identificare in modo univoco l'applicazione per cui single sign-on è stata configurata. È possibile trovare questo valore come la **dell'autorità di certificazione** elemento le **AuthnRequest** (richiesta SAML) inviato dall'applicazione. Questo valore viene inoltre visualizzato come **ID entità** in tutti i metadati SAML forniti dall'applicazione. Controllare la documentazione SAML dell'applicazione per informazioni dettagliate sulle novità relative **ID entità** o **Audience** valore è.

  Il codice seguente illustra come la **Identifier** o **autorità di certificazione** viene visualizzata nella richiesta SAML inviato dall'applicazione ad Azure AD:

  ```xml
  <samlp:AuthnRequest
  xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
  ID="id6c1c178c166d486687be4aaf5e482730"
  Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
  </samlp:AuthnRequest>
  ```

- **URL di risposta**. L'URL di risposta è in cui l'applicazione si aspetta di ricevere il token SAML. Questo URL è detta anche l'URL di servizio (ACS) di consumer di asserzione. Per informazioni dettagliate sull'URL ACS o sull'URL di risposta del token SAML, controllare la documentazione SAML dell'applicazione.

  Per configurare più URL di risposta, è possibile usare lo script di PowerShell seguente.

  ```powershell
  $sp = Get-AzureADServicePrincipal -SearchString "<Exact app name>"
  $app = Get-AzureADApplication -SearchString "<Exact app name>"
  $urllist = New-Object "System.Collections.Generic.List[String]"
  $urllist.Add("<reply URL 1>")
  $urllist.Add("<reply URL 2>")
  $urllist.Add("<reply URL 3>")
  Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls $urllist
  Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls $urllist
  ```

I tre campi seguenti sono facoltativi:

- **URL di accesso (avviato da SP solo)** . Questo valore indica dove l'utente passa per accedere a questa applicazione. Se l'applicazione esegue SSO avviato da SP, quindi quando un utente passa a questo URL, stored procedure esegue il reindirizzamento necessario ad Azure AD per l'autenticazione e accesso dell'utente. Se si specifica questo campo, Azure AD Usa questo URL per avviare l'applicazione dal Pannello di accesso di Azure AD e Office 365. Se si omette questo campo, Azure AD esegue avviata da IdP sign-on durante l'avvio dell'applicazione da Office 365, il pannello di accesso di Azure AD o l'URL di accesso SSO di Azure AD (che è possibile copiare dal **Dashboard** pagina).

- **Stato dell'inoltro**. È possibile specificare uno stato di inoltro in SAML per indicare all'applicazione dove reindirizzare gli utenti dopo l'autenticazione. Il valore è in genere un URL o percorso che indirizza gli utenti a una posizione specifica all'interno dell'applicazione.

- **URL di disconnessione**. Questo valore viene utilizzato per inviare la risposta di disconnessione SAML all'applicazione.

Per altre informazioni, vedere [l'accesso Single sign-on di protocollo SAML](../develop/single-sign-on-saml-protocol.md).

### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Esaminare o personalizzare le attestazioni rilasciate nel token SAML

Quando un utente esegue l'autenticazione all'applicazione, Azure AD rilascia all'applicazione un token SAML con informazioni (o attestazioni) sull'utente che identifica in modo. Per impostazione predefinita, queste informazioni includono il nome utente, indirizzo di posta elettronica, nome e cognome dell'utente.

Per visualizzare o modificare le attestazioni inviate nel token SAML all'applicazione:

- Andare alla **attributi utente e le attestazioni** sull'intestazione e selezionare il **modificare** icona. Il **attributi utente e le attestazioni** verrà visualizzata la pagina.

![Attestazioni e gli attributi utente](./media/configure-single-sign-on-non-gallery-applications/user-attributes-and-claims.png)

Potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML per due motivi:

- L'applicazione richiede un set diverso di URI di attestazione o valori di attestazione.
- L'applicazione richiede la **denominare il valore dell'ID** attestazione sia diversa dal nome utente (noto anche come il nome dell'entità utente) archiviato nella piattaforma delle identità Microsoft.

Per altre informazioni, vedere [Procedura: Personalizzazione delle attestazioni rilasciate nel token SAML per applicazioni aziendali](../develop/active-directory-saml-claims-customization.md).

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Esaminare la data di scadenza del certificato, lo stato e la notifica tramite posta elettronica.

Quando si crea una raccolta o un'applicazione non inclusa nella raccolta, Azure AD crea un certificato specifico dell'applicazione alla scadenza di tre anni dalla relativa data di creazione. Per configurare il trust tra Azure AD e l'applicazione, è necessario questo certificato. Per informazioni dettagliate sul formato del certificato, vedere la documentazione sul protocollo SAML dell'applicazione.

Da Azure AD, è possibile scaricare il certificato attivo in formato Base64 o non elaborati direttamente dalla pagina principale **impostata su Single Sign-On con SAML** pagina. In alternativa, è possibile ottenere il certificato attivo, scaricare il file XML dei metadati dell'applicazione o tramite l'URL dei metadati federazione dell'App.

Per visualizzare, creare o scaricare i certificati (attivi o inattivi), passare al **certificato di firma SAML** sull'intestazione e selezionare il **modificare** icona. Il **certificato di firma SAML** viene visualizzata.

![Certificato di firma SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)

Verificare che il certificato abbia:

- *La data di scadenza desiderate.* È possibile configurare fino a tre anni la data di scadenza nel futuro.
- *Stato attivo per il certificato desiderato.* Se lo stato è **Inactive**, impostare lo stato su **Active**. Per modificare lo stato, fare clic sulla riga del certificato desiderato e selezionare **Rendi attivo il certificato**.
- *L'opzione relativa alla firma corretta e l'algoritmo.*
- *Indirizzi di posta elettronica di notifica corretto.* Quando il certificato attivo si avvicina la scadenza, Azure AD invia una notifica all'indirizzo di posta elettronica configurata in questo campo.  

Per altre informazioni, vedere [gestire i certificati per single sign-on federato](manage-certificates-for-federated-single-sign-on.md) e [avanzata di certificati di firma nel token SAML opzioni](certificate-signing-options.md).

### <a name="set-up-target-application"></a>Configurare l'applicazione di destinazione

Per configurare l'applicazione per l'accesso SSO, individuare la documentazione dell'applicazione. Per trovare la documentazione, andare alla **configurare \<nome applicazione >** titolo e selezionare **visualizzare istruzioni dettagliate**. La documentazione viene visualizzata nel **Configura sign-on** pagina. Questa pagina illustra come compilare il **URL di accesso**, **identificatore di Azure AD**, e **Logout URL** i valori del **configurare \<nome dell'applicazione >** intestazione.

I valori richiesti variano in base all'applicazione. Per informazioni dettagliate, vedere la documentazione sul protocollo SAML dell'applicazione. Il **Login URL** e **URL di disconnessione** valori restituiscono entrambi allo stesso endpoint, ovvero l'endpoint di gestione delle richieste SAML per l'istanza di Azure Active Directory. Il **identificatore di Azure AD** è il valore della **autorità di certificazione** nel token SAML rilasciato all'applicazione.

### <a name="assign-users-and-groups-to-your-saml-application"></a>Assegnare gli utenti e i gruppi all'applicazione SAML

Dopo aver configurato l'applicazione per usare Azure AD come provider di identità basato su SAML, è quasi pronto per eseguire il test. Come controllo di sicurezza, Azure AD rilascia solo un token che consente all'utente di accedere all'applicazione se Azure AD ha concesso l'accesso all'utente. Gli utenti possono accedere direttamente o tramite l'appartenenza a un gruppo.

Per assegnare un nuovo utente o gruppo all'applicazione:

1. Nella barra laterale di applicazione, selezionare **utenti e gruppi**. Il  **\<nome applicazione >-gli utenti e gruppi** verrà visualizzata la pagina, che mostra l'elenco corrente di utenti e gruppi assegnati.
2. Selezionare **aggiungere utenti**. Il **aggiungere assegnazioni** verrà visualizzata la pagina.
3. Selezionare **utenti e gruppi (\<numero > selezionati)** . Il **utenti e gruppi** viene visualizzata la pagina che mostra un elenco di utenti e gruppi disponibili.
4. Tipo o scorrere per trovare l'utente o gruppo che si desidera assegnare dall'elenco.
5. Selezionare ogni utente o gruppo che si desidera aggiungere e quindi selezionare il **seleziona** pulsante. Il **utenti e gruppi** pagina scompare.
6. Nel **aggiungere assegnazioni** pagina, selezionare **assegnare**. Il  **\<nome applicazione >-gli utenti e gruppi** verrà visualizzata la pagina con gli altri utenti visualizzati nell'elenco.

   ![I gruppi e utenti applicazione](./media/configure-single-sign-on-non-gallery-applications/application-users-and-groups.png)

In questo elenco, è possibile:

- Rimuovere un utente.
- Modificare il proprio ruolo.
- Aggiornare le proprie credenziali (nome utente e password) in modo che l'utente possa autenticarsi con l'applicazione dal Pannello di accesso dell'utente di.

È possibile modificare o rimuovere più utenti o gruppi contemporaneamente.

Assegna un utente consente ad Azure AD di emettere un token utente. Inoltre viene visualizzato un riquadro per l'applicazione nel pannello di accesso dell'utente. Se l'utente sta usando Office 365, un riquadro dell'applicazione viene visualizzata anche nell'utilità di avvio di applicazioni di Office 365.

> [!NOTE]
> È possibile caricare un logo icona dell'applicazione usando il pulsante **Carica Logo** nella scheda **Configura** dell'applicazione.

### <a name="test-the-saml-application"></a>Testare l'applicazione SAML

Prima di testare l'applicazione SAML, è necessario avere già configurare l'applicazione con Azure AD e assegnare utenti o gruppi all'applicazione. Per testare l'applicazione SAML, selezionare **l'accesso Single sign-on**, per tornare alle **basato su SAML sign-on** pagina. (Se un altro metodo SSO è stato in effetti, selezionare **modificare le modalità single sign-on** > **SAML** troppo.) Quindi nel **testare single sign-on con \<nome applicazione >** titolo, selezionare **Test**. Per altre informazioni, vedere [basato su SAML con il Debug di single sign-on alle applicazioni in Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Password Single Sign-On

Selezionare questa opzione per configurare [basato su password single sign-on](what-is-single-sign-on.md) per un'applicazione web con una pagina di accesso HTML. L'SSO basato su password, definito anche insieme di credenziali delle password, consente di gestire l'accesso degli utenti e le password per le applicazioni Web che non supportano la federazione delle identità. È anche utile per scenari in cui più utenti devono condividere un singolo account, ad esempio agli account di app di social media dell'organizzazione.

Dopo aver selezionato **basato su Password**, viene chiesto di immettere l'URL della pagina dell'applicazione web di accesso.

![Single Sign-On basato su password](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

Quindi eseguire questi passaggi:

1. Immettere l'URL. Questa stringa deve essere la pagina che include il campo di input di nome utente.
2. Selezionare **Salva**. Azure AD tenta di analizzare la pagina di accesso per un nome utente e una password immessi.
3. Se Azure AD dell'analisi tentativo ha esito negativo, selezionare **Configure \<nome applicazione > Impostazioni Password Single Sign-on** per visualizzare il **Configura sign-on** pagina. (Se il tentativo ha esito positivo, è possibile ignorare il resto di questa procedura.)
4. Selezionare **rilevare manualmente i campi di accesso**. Verranno visualizzate le istruzioni aggiuntive che descrivono il rilevamento manuale dei campi di accesso.

   ![Configurazione manuale di basato su password single sign-on](./media/configure-single-sign-on-non-gallery-applications/password-configure-sign-on.png)
5. Selezionare **acquisire i campi di accesso**. Verrà visualizzata una pagina di stato di acquisizione in una nuova scheda, viene visualizzato il messaggio **è in corso l'acquisizione dei metadati**.
6. Se il **l'estensione Pannello di accesso necessaria** verrà visualizzata la finestra in una nuova scheda, selezionare **Installa ora** per installare il **sicuro alle App personali estensione di accesso** estensione del browser. (L'estensione del browser richiede Microsoft Edge, Chrome o Firefox). Quindi installare, avviare e abilitare l'estensione e aggiornare la pagina di stato di acquisizione.

   L'estensione del browser apre quindi un'altra scheda che visualizza l'URL immesso.
7. Nella scheda con l'URL immesso passare attraverso il processo di accesso. Compilare i campi nome utente e password e tenta di accedere. (Non è necessario fornire la password corretta.)

   Un messaggio chiede di salvare i campi di accesso acquisiti.
8. Selezionare **OK**. Chiude la scheda, l'estensione del browser aggiorna la pagina dello stato di acquisizione con il messaggio **metadati sono stati aggiornati per l'applicazione**e tale browser scheda anche si chiude.
9. In Azure AD **Configura sign-on** pagina, selezionare **Ok, ho potuto effettuare l'accesso all'app è stata**.
10. Selezionare **OK**.

Dopo l'acquisizione della pagina di accesso, si può assegnare utenti e gruppi ed è possibile configurare criteri per le credenziali come nelle normali [applicazioni SSO con password](what-is-single-sign-on.md).

> [!NOTE]
> È possibile caricare un logo icona dell'applicazione usando il pulsante **Carica Logo** nella scheda **Configura** dell'applicazione.

## <a name="existing-single-sign-on"></a>Single Sign-On esistente

Selezionare questa opzione per aggiungere un collegamento all'applicazione nel portale dal Pannello di accesso di Azure AD o Office 365 dell'organizzazione. È possibile utilizzare questo metodo per aggiungere collegamenti alle applicazioni web personalizzate che attualmente usano Active Directory Federation Services (o altro servizio federativo) anziché Azure AD per l'autenticazione. In alternativa, è possibile aggiungere collegamenti diretti a pagine specifiche di SharePoint o ad altre pagine Web da visualizzare nei riquadri di accesso dell'utente.

Dopo aver selezionato **collegato**, viene chiesto di immettere l'URL dell'applicazione a cui collegarsi. Digitare l'URL e selezionare **salvare**. È possibile assegnare utenti e gruppi per l'applicazione, che fa sì che l'applicazione viene visualizzata nel [avvio delle app di Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) o il [Pannello di accesso AD Azure](end-user-experiences.md) per tali utenti.

> [!NOTE]
> È possibile caricare un logo icona dell'applicazione usando il pulsante **Carica Logo** nella scheda **Configura** dell'applicazione.

## <a name="related-articles"></a>Articoli correlati

- [Procedura: Personalizzazione delle attestazioni rilasciate nel token SAML per applicazioni aziendali](../develop/active-directory-saml-claims-customization.md)
- [Eseguire il debug basato su SAML single sign-on alle applicazioni in Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
- [Piattaforma delle identità di Microsoft (noto in precedenza come Azure Active Directory per gli sviluppatori)](../develop/index.yml)
