---
title: Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory | Microsoft Docs
description: 'Informazioni sulla connessione self-service di applicazioni in Azure Active Directory con SAML e SSO basato su password '
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2016
ms.author: asmalser

---
# <a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory
Questo articolo illustra una funzionalità che consente agli amministratori di configurare l'accesso Single Sign-On alle applicazioni non presenti nella raccolta di app di Azure Active Directory *senza scrivere codice*. Questa funzionalità è stata rilasciata dall'anteprima tecnica il 18 novembre 2015 ed è inclusa in [Azure Active Directory Premium](active-directory-editions.md). Se si cercano invece indicazioni per gli sviluppatori sull'integrazione delle app personalizzate con Azure AD tramite il codice, vedere [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md).

La raccolta di applicazioni di Azure Active Directory offre un elenco di applicazioni che supportano un formato di Single Sign-On con Azure Active Directory, come descritto in [questo articolo](active-directory-appssoaccess-whatis.md). Dopo aver trovato l'applicazione a cui connettersi, come specialista IT o come integratore di sistemi, è possibile iniziare seguendo le istruzioni dettagliate presentate nel portale di gestione di Azure per abilitare il Single Sign-On.

I clienti con licenze [Azure Active Directory Premium](active-directory-editions.md) ottengono anche le funzionalità aggiuntive seguenti:

* Integrazione self-service di qualsiasi applicazione che supporta i provider di identità SAML 2.0 (avviato dal provider di servizi o dal provider di identità)
* Integrazione self-service di qualsiasi applicazione Web con una pagina di accesso basata su HTML con [SSO basato su password](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Connessione self-service di applicazioni che usano il protocollo SCIM per il provisioning dell'utente ([descritto qui](active-directory-scim-provisioning.md))
* Possibilità di aggiungere collegamenti a qualsiasi applicazione nell'[icona di avvio delle app di Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) o nel [riquadro di accesso di Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

Possono essere incluse le applicazioni SaaS usate ma non ancora caricate nella raccolta di applicazioni di Azure AD, nonché le applicazioni Web di terze parti che l'organizzazione ha distribuito nei server sotto il suo controllo, nel cloud o in locale.

Queste funzionalità, note anche come *modelli di integrazione di app*, forniscono punti di connessione basati sugli standard per le app che supportano SAML, SCIM o l'autenticazione basata su moduli e includono opzioni e impostazioni flessibili per assicurare la compatibilità con numerosissime applicazioni. 

## <a name="adding-an-unlisted-application"></a>Aggiunta di un'applicazione non pubblicata
Per connettere un'applicazione usando un modello di integrazione di app, accedere al portale di gestione di Azure con l'account di amministratore di Azure Active Directory e trovare la sezione **Active Directory > [Directory] > Applicazioni**, scegliere **Aggiungi** e quindi **Aggiungere un'applicazione dalla raccolta**. 

![][1]

Nella raccolta di applicazioni è possibile aggiungere un'applicazione non pubblicata usando la categoria **Personalizzata** a sinistra oppure selezionando il collegamento **Aggiungi un'applicazione non pubblicata** visualizzato nei risultati della ricerca, se l'applicazione cercata non è stata trovata. Dopo aver immesso un nome per l'applicazione, è possibile configurare le opzioni di comportamento e Single Sign-On. 

**Suggerimento rapido**: come procedura consigliata, usare la funzione di ricerca per verificare se l'applicazione esiste già nella raccolta di applicazioni. Se l'applicazione viene trovata e la relativa descrizione parla di "Single Sign-On", l'applicazione è già supportata per il Single Sign-On federato. 

![][2]

La procedura per l'aggiunta di un'applicazione non pubblicata è molto simile a quella disponibile per le applicazioni preintegrate. Per iniziare, selezionare **Configura accesso Single Sign-On**. Nella schermata successiva sono disponibili le tre opzioni seguenti per la configurazione del Single Sign-On, descritte nelle sezioni successive.

![][3]

## <a name="azure-ad-single-sign-on"></a>Single Sign-On di Microsoft Azure AD
Selezionare questa opzione per configurare l'autenticazione basata su SAML per l'applicazione. Per questa opzione è necessario che l'applicazione supporti SAML 2.0. Prima di continuare è consigliabile raccogliere informazioni sull'uso delle funzionalità SAML dell'applicazione. Dopo aver selezionato **Avanti**, viene richiesto di immettere tre diversi URL corrispondenti agli endpoint SAML per l'applicazione. 

![][4]

Si tratta di:

* **URL di accesso (avviato solo da provider di servizi)** da cui l'utente accede all'applicazione. Se l'applicazione è configurata per eseguire l'accesso Single Sign-On avviato dal provider di servizi, quando un utente passa a questo URL, il provider di servizi eseguirà il reindirizzamento necessario ad Azure AD per l'autenticazione e l'accesso dell'utente. Se questo campo è popolato, Azure AD userà questo URL per avviare l'applicazione da Office 365 e il pannello di accesso di AD Azure. Se questo campo è omesso, Azure AD eseguirà l'accesso Single Sing-On avviato dal provider di identità quando l'app viene avviata da Office 365, dal pannello di accesso di Azure AD o dall'URL di accesso Single Sign-On di Azure AD, che è possibile copiare dalla scheda Dashboard.
* **URL dell'autorità di certificazione** : l'URL dell'autorità di certificazione deve identificare in modo univoco l'applicazione per cui viene configurato l'accesso Single Sign-On. Si tratta del valore che Azure AD invia all'applicazione come parametro **Audience** del token SAML e l'applicazione deve convalidarlo. Questo valore viene inoltre visualizzato come **ID entità** in tutti i metadati SAML forniti dall'applicazione. Per informazioni dettagliate sui valori ID entità o Audience, controllare la documentazione SAML dell'applicazione. Di seguito è riportato un esempio di come l'URL di Audience viene visualizzato nel token SAML restituito all'applicazione:

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* **URL di risposta** : è l'indirizzo a cui l'applicazione prevede di ricevere il token SAML. Viene anche definito **URL del servizio consumer di asserzione (ACS)**. Per informazioni dettagliate sull'URL ACS o sull'URL di risposta del token SAML, controllare la documentazione SAML dell'applicazione.
  Dopo averli immessi, fare clic su **Avanti** per passare alla schermata successiva. Questa schermata fornisce informazioni su ciò che è necessario configurare sul lato applicazione in modo che questa accetti un token SAML da Azure AD. 

![][5]

I valori necessari variano a seconda dell'applicazione. Per informazioni dettagliate, vedere la documentazione SAML dell'applicazione. L'URL servizio **Single Sign-On** e l'URL servizio **Sign-Out** vengono entrambi risolti nello stesso endpoint, ovvero l'endpoint di gestione delle richieste SAML per questa istanza di Azure AD. L'URL autorità di certificazione è il valore visualizzato come "Autorità di certificazione" all'interno del token SAML rilasciato all'applicazione. 

Dopo aver configurato l'applicazione, fare clic sul pulsante **Avanti** e quindi su **Operazione completata** per chiudere la finestra di dialogo. 

## <a name="assigning-users-and-groups-to-your-saml-application"></a>Assegnazione di utenti e gruppi all'applicazione SAML
Dopo aver configurato l'applicazione per l'uso di Azure AD come provider di identità basato su SAML, l'applicazione è quasi pronta per il test. Come controllo di sicurezza, Azure AD non rilascia un token per l'accesso all'applicazione a meno che l'accesso non sia stato concesso con Azure AD. L'accesso può essere concesso direttamente agli utenti o attraverso un gruppo di cui sono membri. 

Per assegnare un utente o un gruppo all'applicazione, scegliere il pulsante **Assegna utenti** . Selezionare l'utente o il gruppo da assegnare e quindi fare clic sul pulsante **Assegna** . 

![][6]

L'assegnazione consente ad Azure AD di rilasciare un token per l'utente e di visualizzare un riquadro relativo all'applicazione nel riquadro di accesso dell'utente. Se l'utente usa Office 365, il riquadro per l'applicazione viene visualizzato anche nell'utilità di avvio applicazioni di Office 365. 

È possibile caricare un logo icona dell'applicazione usando il pulsante **Carica Logo** nella scheda **Configura** dell'applicazione. 

### <a name="customizing-the-claims-issued-in-the-saml-token"></a>Personalizzazione delle attestazioni rilasciate nel token SAML
Quando un utente esegue l'autenticazione all'applicazione, Azure AD rilascia un token SAML all'applicazione contenente informazioni (o attestazioni) sull'utente che lo identificano in modo univoco. Per impostazione predefinita sono inclusi il nome utente, l'indirizzo di posta elettronica, il nome e il cognome dell'utente. 

Le attestazioni inviate all'applicazione nel token SAML possono essere visualizzate o modificate nella scheda **Attributi** . 

![][7]

Esistono due possibili motivi per i quali potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML: •L’applicazione è stata scritta in modo da richiedere un diverso set di URI dell’attestazione o valori dell’attestazione •L’applicazione è stata distribuita in modo da richiedere un’attestazione NameIdentifier diversa dal nome utente (nome principale utente AKA) archiviato in Azure Active Directory. 

Per informazioni su come aggiungere e modificare attestazioni per questi scenari, vedere l' [articolo sulla personalizzazione di attestazioni](active-directory-saml-claims-customization.md). 

### <a name="testing-the-saml-application"></a>Test dell'applicazione SAML
Dopo aver configurato il certificato e gli URL SAML in Azure AD e nell'applicazione, assegnato gli utenti o gruppi all'applicazione in Azure ed esaminato le attestazioni, modificandole se necessario, l'utente è pronto per accedere all'applicazione. 

Per il test, è sufficiente usare il riquadro di accesso di Azure AD all'indirizzo https://myapps.microsoft.com per eseguire l'accesso usando un account utente assegnato all'applicazione e quindi fare clic sul riquadro per l'applicazione per avviare il processo Single Sign-On. In alternativa, è possibile passare direttamente all'URL Single Sign-On per l'applicazione ed eseguire l'accesso da lì. 

Per suggerimenti relativi al debug, vedere questo [articolo su come eseguire il debug di applicazioni Single Sign-On basate su SAML](active-directory-saml-debugging.md) 

## <a name="password-single-sign-on"></a>Password Single Sign-On
Selezionare questa opzione per configurare il [Single Sign-On basato su password](active-directory-appssoaccess-whatis.md) per un'applicazione Web con una pagina di accesso HTML. L'SSO basato su password, definito anche insieme di credenziali delle password, consente di gestire l'accesso degli utenti e le password per le applicazioni Web che non supportano la federazione delle identità. Risulta utile anche negli scenari in cui più utenti devono condividere un unico account, ad esempio agli account di app di social media dell'organizzazione. 

Dopo aver selezionato **Avanti**, viene richiesto di immettere l'URL della pagina di accesso basata sul Web dell'applicazione. Questa deve essere la pagina che include i campi di input per nome utente e password. Dopo l'immissione, Azure AD avvia un processo di analisi della pagina di accesso per cercare un nome utente e una password immessi. Se il processo non riesce, viene avviato un processo alternativo per installare un'estensione del browser (richiede Internet Explorer, Chrome o Firefox) che consente di acquisire manualmente i campi.

Dopo che la pagina di accesso è stata acquisita, è possibile assegnare utenti e gruppi ed è possibile impostare le credenziali come nelle normali [app di SSO basato su password](active-directory-appssoaccess-whatis.md).

Nota: è possibile caricare un logo icona dell'applicazione usando il pulsante **Carica Logo** nella scheda **Configura** dell'applicazione. 

## <a name="existing-single-sign-on"></a>Single Sign-On esistente
Selezionare questa opzione per aggiungere un collegamento a un'applicazione nel riquadro di accesso di Azure AD o nel portale di Office 365 dell'organizzazione. È possibile usare questa opzione per aggiungere collegamenti ad app Web personalizzate che usano Active Directory Federation Services di Azure (o un altro servizio federativo) anziché Azure AD per l'autenticazione. In alternativa, è possibile aggiungere collegamenti diretti a pagine specifiche di SharePoint o ad altre pagine Web da visualizzare nei riquadri di accesso dell'utente. 

Dopo aver selezionato **Avanti**, viene richiesto di immettere l'URL dell'applicazione da collegare. Al termine sarà possibile assegnare utenti e gruppi all'applicazione e questa verrà visualizzata nell'[icona di avvio delle app di Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) o nel [riquadro di accesso di Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) per tali utenti.

Nota: è possibile caricare un logo icona dell'applicazione usando il pulsante **Carica Logo** nella scheda **Configura** dell'applicazione.

## <a name="related-articles"></a>Articoli correlati
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Come personalizzare lle attestazioni rilasciate nel token SAML per le app preintegrate](active-directory-saml-claims-customization.md)
* [Risoluzione dei problemi dell'accesso Single Sign-On basato su SAML](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png



<!--HONumber=Oct16_HO2-->


