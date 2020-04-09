---
title: Spostamento dell'autenticazione dell'applicazione da ADFS ad Azure Active Directory
description: Questo articolo illustra come le organizzazioni possono spostare le applicazioni in Azure Active Directory, in particolare applicazioni SaaS federate.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb39606cdbb6488ccdee2828029d3617d689508
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891939"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Spostamento dell'autenticazione dell'applicazione da Active Directory Federation Services ad Azure Active Directory

[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) offre una piattaforma di identità universale che offre a persone, partner e clienti un'unica identità per accedere alle applicazioni e collaborare da qualsiasi piattaforma e dispositivo. Azure AD dispone di una [suite completa di funzionalità di gestione delle identità.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) La standardizzazione dell'autenticazione e dell'autorizzazione dell'applicazione (app) in Azure AD consente di offrire i vantaggi offerti da queste funzionalità. 

> [!NOTE]
> Questo articolo è incentrato sullo spostamento dell'autenticazione dell'applicazione da Active Directory locale e Active Directory Federation Services ad Azure AD. Per una panoramica della pianificazione di questo spostamento, vedere il white paper [Migrazione dell'autenticazione dell'applicazione ad Azure AD.](https://aka.ms/migrateapps/whitepaper) Nel white paper viene illustrato come pianificare la migrazione, i test e le informazioni dettagliate.

## <a name="introduction"></a>Introduzione

Se si dispone di una directory locale che contiene account utente, è probabile che si disponga di molte applicazioni a cui gli utenti eseguono l'autenticazione. Ognuna di queste app è configurata per consentire agli utenti di accedere usando le proprie identità. 


Gli utenti possono anche eseguire l'autenticazione direttamente con Active Directory locale. Active Directory Federation Services (ADFS) è uno standard basato sul servizio di identità locale. ADFS estende la possibilità di utilizzare la funzionalità Single Sign-On (SSO) tra partner commerciali attendibili senza richiedere agli utenti di accedere separatamente a ogni applicazione. Questa operazione è nota come Federazione.

Molte organizzazioni dispongono di software as a Service (SaaS) o di app line-of-Business (LOB) personalizzate federate direttamente in ADFS, insieme ad Office 365 e alle app basate su Azure AD. 

![Applicazioni connesse direttamente in locale](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**Per aumentare la sicurezza delle applicazioni, l'obiettivo è disporre di un unico set di controlli di accesso e criteri negli ambienti locali e cloud.** 

![Applicazioni connesse tramite Azure ADApplications connected through Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Tipi di app di cui eseguire la migrazione

La migrazione di tutta l'autenticazione dell'applicazione ad Azure AD è ottimale, in quanto offre un unico piano di controllo per la gestione di identità e accessi.

Le applicazioni possono utilizzare protocolli moderni o legacy per l'autenticazione. Prendere in considerazione la migrazione prima di applicazioni che utilizzano protocolli di autenticazione moderni (ad esempio SAML e Open ID Connect). Queste app possono essere riconfigurate per l'autenticazione con Azure AD tramite un connettore incorporato nella raccolta di app o registrando l'applicazione in Azure AD. Le app che utilizzano protocolli meno recenti possono essere integrate utilizzando [il proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy). 

Per altre informazioni, vedere [Quali tipi di applicazioni è possibile integrare con Azure AD?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)

È possibile usare il [report attività dell'applicazione ADFS per eseguire la migrazione delle applicazioni in Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) se Azure AD Connect Health è [abilitato.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs) 

### <a name="the-migration-process"></a>Il processo di migrazione

Durante il processo di spostamento dell'autenticazione dell'app in Azure AD, testare adeguatamente le app e la configurazione. È consigliabile continuare a utilizzare ambienti di test esistenti per il test di migrazione che passa all'ambiente di produzione. Se un ambiente di test non è attualmente disponibile, è possibile configurarne uno usando il [servizio app](https://azure.microsoft.com/services/app-service/) di Azure o le macchine virtuali di [Azure,](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)a seconda dell'architettura dell'applicazione.

È possibile scegliere di configurare un tenant di Azure AD di test separato da usare durante lo sviluppo delle configurazioni dell'app. 

Il processo di migrazione potrebbe essere simile al seguente:Your migration process may look like this:

**Fase 1 - Stato corrente: autenticazione dell'app di produzione con ADFS**

![Fase 1 della migrazione ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**Fase 2 – OPTIONAL: L'istanza di test dell'app che punta a testare il tenant di Azure**

Aggiornare la configurazione in modo che l'istanza di test dell'app punti a un tenant di Azure AD di test e apportare le modifiche necessarie. L'app può essere testata con gli utenti nel tenant di Azure AD di test. Durante il processo di sviluppo, è possibile utilizzare strumenti come [Fiddler](https://www.telerik.com/fiddler) per confrontare e verificare le richieste e le risposte.

Se la configurazione di un tenant di test separato non è fattibile, ignorare questa fase e recuperare un'istanza di test di un'app e indirizzarla al tenant di Azure AD di produzione come descritto nella fase 3 seguente.

![Fase 2 della migrazione ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**Fase 3: testare l'app che punta al tenant di Azure di produzione**

Aggiornare la configurazione in modo che l'istanza di test dell'app punti all'istanza di produzione di Azure.Update the configuration to point your test instance of the app to your production instance of Azure. È ora possibile eseguire il test con gli utenti nell'istanza di produzione. Se necessario, rivedere la sezione di questo articolo sulla transizione degli utenti.

![Fase di migrazione 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**Fase 4 - App di produzione che punta al tenant di AD di produzione**

Aggiornare la configurazione dell'applicazione di produzione in modo che punti al tenant di Azure di produzione.

![Fase 1 della migrazione ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Le app che eseguono l'autenticazione con ADFS possono usare i gruppi di Active Directory per le autorizzazioni. Usare la [sincronizzazione](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) di Azure AD Connect per sincronizzare i dati di identità tra l'ambiente locale e Azure AD prima di iniziare la migrazione. Verificare tali gruppi e l'appartenenza prima della migrazione in modo da poter concedere l'accesso agli stessi utenti durante la migrazione dell'applicazione.

### <a name="line-of-business-lob-apps"></a>App line-of-business (LOB)

Le app così business ide/o sono sviluppate internamente dall'organizzazione o sono disponibili come prodotto con pacchetto standard installato nel data center. Gli esempi includono le app basate su Windows Identity Foundation e le app di SharePoint (non SharePoint Online). 

Le app line-of-business che usano OAuth 2.0, OpenID Connect o WS-Federation possono essere integrate con Azure AD come [registrazioni di app.](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users) Integrare app personalizzate che usano SAML 2.0 o WS-Federation come [applicazioni non di raccolta](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) nella pagina delle applicazioni aziendali nel portale di [Azure.](https://portal.azure.com/)

## <a name="saml-based-single-sign-on"></a>Single Sign-On basato su SAML

Le app che usano SAML 2.0 per l'autenticazione possono essere configurate per l'accesso [Single Sign-On basato su SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (SSO basato su SAML). Con [SSO basato su SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on), è possibile eseguire il mapping degli utenti a ruoli applicazione specifici in base alle regole definite nelle attestazioni SAML. 

Per configurare un'applicazione SaaS per l'accesso Single Sign-On basato su SAML, vedere [Configure SAML-based single sign-on](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications). 

![Schermate dell'utente SAML SSO ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
Molte applicazioni SaaS dispongono di [un'esercitazione specifica dell'applicazione](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) che consente di eseguire la configurazione per l'accesso Single Sign-On basato su SAML.

![esercitazione sull'app](media/migrate-adfs-apps-to-azure/app-tutorial.png)

La migrazione di alcune app può essere eseguita facilmente. Le app con requisiti più complessi, come le attestazioni personalizzate, potrebbero richiedere operazioni di configurazione aggiuntive in Azure AD e/o Azure AD Connect. Per informazioni sui mapping delle attestazioni supportati, vedere [Mapping delle attestazioni in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)

Tenere presente le limitazioni seguenti durante il mapping degli attributi:Keep in mind the following limitations when mapping attributes:

* Non tutti gli attributi che possono essere emessi in ADFS verranno visualizzati in Azure AD come attributi da generare nei token SAML, anche se tali attributi vengono sincronizzati. Quando si modifica l'attributo, l'elenco a discesa Valore mostrerà i diversi attributi disponibili in Azure AD. Controllare la configurazione della [sincronizzazione](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) di Azure AD Connect per assicurarsi che un attributo obbligatorio, ad esempio samAccountName, venga sincronizzato con Azure AD. È possibile usare gli attributi di estensione per generare qualsiasi attestazione che non faccia parte dello schema utente standard in Azure AD.

* Negli scenari più comuni, per un'app sono necessarie solo l'attestazione NameID e altre attestazioni dell'ID utente comuni. Per determinare se sono necessarie attestazioni aggiuntive, esaminare le attestazioni estratta da ADFS.

* Non tutte le attestazioni possono essere problemi poiché alcune attestazioni sono protette in Azure AD. 

* La possibilità di utilizzare token SAML crittografati è ora in anteprima. Vedere [Procedura: personalizzare le attestazioni emesse nel token SAML per](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)le applicazioni aziendali.

 

### <a name="software-as-a-service-saas-apps"></a>Applicazioni SaaS (Software as a Service)

Se l'utente accede alle app SaaS, ad esempio Salesforce, ServiceNow o Workday, e sono integrati con ADFS, si usa l'accesso federato per le app SaaS. 

La maggior parte delle applicazioni SaaS può essere già configurata in Azure AD. Microsoft dispone di molte connessioni preconfigurate alle app SaaS nella raccolta di app di [Azure AD,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)che seterranno la transizione. Le applicazioni SAML 2.0 possono essere integrate con Azure AD tramite la raccolta di app di Azure AD o come [applicazioni non di raccolta.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

Analogamente, le app che usano OAuth 2.0 oppure OpenID Connect possono essere integrate con Azure AD come [registrazioni di app](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users). Le app che usano protocolli legacy possono usare il [proxy di applicazione di Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) per eseguire l'autenticazione con Azure AD.

Per qualsiasi problema con l'onboarding delle app SaaS, è possibile contattare l'alias di supporto di [SaaS Application Integration.](mailto:SaaSApplicationIntegrations@service.microsoft.com)

**Certificati di firma SAML per SSO:** la firma dei certificati è una parte importante di qualsiasi distribuzione SSO. Azure AD crea i certificati di firma per stabilire SSO federato basato su SAML alle applicazioni SaaS. Dopo aver aggiunto le applicazioni della raccolta o non della raccolta, è necessario configurare l'applicazione aggiunta utilizzando l'opzione SSO federato. Vedere [Gestire i certificati per l'accesso Single Sign-On federato in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on)

### <a name="apps-and-configurations-that-can-be-moved-today"></a>App e configurazioni che possono essere spostate oggi

Le app che puoi spostare facilmente oggi includono app SAML 2.0 che usano il set standard di elementi di configurazione e attestazioni:

* Nome entità utente

* Indirizzo di posta elettronica

* Nome specificato

* Surname

* Un attributo alternativo come **NameID** SAML, ad esempio l'attributo Mail, il prefisso di posta elettronica, l'ID dipendente o gli attributi dell'estensione 1-15 di Azure AD oppure l'attributo **SamAccountName** locale. Per altre informazioni, vedere [Modifica dell'attestazione NameIdentifier](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

* Attestazioni personalizzate.

Per eseguire la migrazione ad Azure AD, sono necessari passaggi di configurazione aggiuntivi:The following require additional configuration steps to migrate to Azure AD:

* Autorizzazione personalizzata o regole di autenticazione a più fattori (MFA) in ADFS. È possibile configurarli utilizzando la funzionalità Accesso condizionale di [Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

* App con più endpoint URL di risposta. È possibile configurarli in Azure AD usando PowerShell o l'interfaccia del portale di Azure.You configure them in Azure AD by using PowerShell or in the Azure portal interface.

* App WS-Federation come le app SharePoint che richiedono token SAML versione 1.1, È possibile configurarli manualmente tramite PowerShell.You can configure them manually using PowerShell. È inoltre possibile aggiungere un modello generico preintegrato per le applicazioni SharePoint e SAML 1.1 dalla raccolta. Supportiamo il protocollo SAML 2.0.

* Il rilascio di attestazioni complesse trasforma le regole.Complex claims ssuance transforms rules. Per informazioni sui mapping delle attestazioni supportati, vedere:For information about supported claims mappings, see:
   *  [Mapping di attestazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>App e configurazioni attualmente non supportate in Azure AD

Le app che richiedono le funzionalità seguenti non possono essere migrate oggi.

**Funzionalità del protocollo**

* Supporto per il modello WS-Trust ActAs

* Risoluzione artefatto SAML

* Verifica della firma delle richieste SAML firmate  
Si noti che le richieste firmate vengono accettate, ma la firma non viene verificata.  
Dato che Azure AD restituirà il token solo agli endpoint preconfigurati nell'applicazione, la verifica della firma probabilmente non è necessaria nella maggior parte dei casi.

**Attestazioni nelle funzionalità token**

* Attestazioni da archivi di attributi diversi dalla directory di Azure AD, a meno che i dati non vengano sincronizzati con Azure AD. Per altre informazioni, vedere [Panoramica dell'API](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)di sincronizzazione di Azure AD.

* Emissione di attributi a più valori di directory. Ad esempio, al momento non è possibile rilasciare un'attestazione multivalore per gli indirizzi proxy.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Eseguire il mapping delle impostazioni dell'app da ADFS ad Azure ADMap app settings from AD FS to Azure AD

La migrazione ha inizio con la valutazione della configurazione dell'applicazione in locale e il mapping di tale configurazione ad Azure AD. AD FS e Azure AD funzionano in modo analogo, pertanto i concetti di configurazione dell'attendibilità, degli URL di accesso e disconnessione e degli identificatori si applicano in entrambi i casi. Documentare le impostazioni di configurazione di ADFS delle applicazioni in modo da poterle configurare facilmente in Azure AD.

### <a name="map-app-configuration-settings"></a>Mappare le impostazioni di configurazione dell'app

The following table describes some of the most common mapping of settings between an AD FS Relying Party Trust to Azure AD Enterprise Application:

* ADFS: trovare l'impostazione nell'attendibilità della relying party di ADFS per l'app. Fare clic con il pulsante destro del mouse sulla relying party e scegliere Proprietà.Right-click the relying party and select Properties. 

* Azure AD: l'impostazione è configurata all'interno del portale di [Azure](https://portal.azure.com/) nelle proprietà Single Sign-On di ogni applicazione.

| Impostazione di configurazione| AD FS| Come configurare in Azure ADHow to configure in Azure AD| Token SAML |
| - | - | - | - |
| **URL di accesso dell'app** <p>URL dell'utente che deve accedere all'app in un flusso SAML avviato dal provider di servizi (SP).| N/D| Aprire la configurazione SAML di base dall'accesso basato su SAML| N/D |
| **URL di risposta dell'app** <p>URL dell'app dal punto di vista del provider di identità (IdP). L'IdP invia l'utente e il token dopo che l'utente ha eseguito l'accesso all'IdP.  Questo è noto anche come **endpoint consumer di asserzione SAML**.| Selezionare la scheda **Endpoint**| Aprire la configurazione SAML di base dall'accesso basato su SAML| Elemento di destinazione nel token SAML. Valore di esempio:Example value:[https://contoso.my.salesforce.com](https://contoso.my.salesforce.com/) |
| **URL di disconnessione dell'app** <p>Si tratta dell'URL a cui vengono inviate le richieste di "pulizia disconnessione" quando un utente si disconnette da un'app. L'IdP invia la richiesta di disconnettere l'utente anche da tutte le altre app.| Selezionare la scheda **Endpoint**| Aprire la configurazione SAML di base dall'accesso basato su SAML| N/D |
| **Identificatore dell'app** <p>Questo è l'identificatore dell'app dal punto di vista dell'IdP. Il valore dell'URL di accesso viene spesso utilizzato per l'identificatore (ma non sempre).  A volte l'app chiama questo "ID entità".| Selezionare la scheda **Identificatori**|Aprire la configurazione SAML di base dall'accesso basato su SAML| Esegue il mapping all'elemento **Audience** nel token SAML. |
| **Metadati di federazione dell'app** <p>Questo è il percorso dei metadati federativi dell'app. Viene usata dal provider di identità per aggiornare automaticamente specifiche impostazioni di configurazione come gli endpoint o i certificati di crittografia.| Selezionare la scheda **Monitoraggio**| N/D. Azure AD non supporta direttamente l'utilizzo dei metadati federativi dell'applicazione. È possibile importare manualmente i metadati federativi.| N/D |
| **Identificatore utente/ID nome** <p>Attributo usato per indicare in modo univoco l'identità utente da Azure AD o AD FS all'app.  Questo attributo è in genere l'UPN o l'indirizzo di posta elettronica dell'utente.| Regole attestazione. Nella maggior parte dei casi, la regola attestazione genera un'attestazione con un tipo che termina con NameIdentifier.In most cases, the claim rule issues a claim with a type that ends with the NameIdentifier.| L'identificatore è riportato nell'intestazione **Attributi utente e attestazioni**. Per impostazione predefinita, viene utilizzato l'UPN| Esegue il mapping all'elemento **NameID** nel token SAML. |
| **Altre indicazioni** <p>Esempi di altre informazioni sulle attestazioni che vengono comunemente inviate dall'IdP all'app includono Nome, Cognome, Indirizzo di posta elettronica e appartenenza al gruppo.| In AD FS sono riportate come altre regole attestazioni per la relying party.| L'identificatore è riportato nell'intestazione **Attributi utente & Attestazioni**. Selezionare **Visualizza e modifica tutti gli altri attributi utente**.| N/D |


### <a name="map-identity-provider-idp-settings"></a>Impostazioni del provider di identità mappa (IdP)Map Identity Provider (IdP) settings

Configurare le applicazioni in modo che puntino ad Azure AD rispetto ad AD FS per SSO. Qui, ci stiamo concentrando sulle app SaaS che utilizzano il protocollo SAML. Tuttavia, questo concetto si estende anche alle app business personalizzate.

> [!NOTE]
> I valori di configurazione per Azure AD seguono il modello in cui l'ID tenant di Azure sostituisce l'ID tenant di Azure e l'ID applicazione sostituisce l'ID-applicazione. Queste informazioni sono disponibili nel portale di Azure in Azure Active Directory > Properties:You find this information in the [Azure portal](https://portal.azure.com/) under Azure Active Directory > Properties: 

* Selezionare ID directory per visualizzare l'ID tenant. 

* Selezionare ID applicazione per visualizzare l'ID applicazione.

 A livello generale, eseguire il mapping degli elementi di configurazione delle app SaaS seguenti ad Azure AD. 

 

| Elemento| Valore di configurazione |
| - | - |
| Emittente del provider di identità| [https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| URL di accesso del provider di identità| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| URL di disconnessione del provider di identità| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Percorso dei metadati federativi| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>Mappare le impostazioni SSO per le app SaaS

Le app SaaS devono sapere dove inviare le richieste di autenticazione e come convalidare i token ricevuti. La tabella seguente descrive gli elementi per configurare le impostazioni SSO nell'app e i relativi valori o percorsi all'interno di ADFS e Azure AD

| Impostazione di configurazione| AD FS| Come configurare in Azure ADHow to configure in Azure AD |
| - | - | - |
| **URL di accesso IdP** <p>URL di accesso dell'IdP dal punto di vista dell'app (dove l'utente viene reindirizzato per l'accesso).| L'URL di accesso di ADFS è il nome del servizio federativo ADFS seguito da "/adfs/ls/". <p>Per esempio:[https://fs.contoso.com/adfs/ls/](https://fs.contoso.com/adfs/ls/)| Sostituirlo con l'ID tenant. <p> Per le app che usano il protocollo SAML-P:[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>Per le app che usano il protocollo WS-Federation:[https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **URL di disconnessione IdP**<p>Url di disconnessione dell'IdP dal punto di vista dell'app (dove l'utente viene reindirizzato quando sceglie di disconnettersi dall'app).| L'URL di disconnessione è uguale all'URL di accesso o lo stesso URL con l'aggiunta di "wa-wsignout1.0". Per esempio:[https://fs.contoso.com/adfs/ls/?wa=wsignout1.0](https://fs.contoso.com/adfs/ls/?wa=wsignout1.0)| Sostituirlo con l'ID tenant.<p>Per le app che usano il protocollo SAML-P:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> Per le app che usano il protocollo WS-Federation:[https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certificato per la firma di token**<p>Il PID utilizza la chiave privata del certificato per firmare i token emessi. Verifica che il token provenga dallo stesso provider di identità per cui è stato configurato il trust nell'app.| Il certificato per la firma di token di AD FS si trova in **Certificati** in Gestione AD FS.| Trovarlo nel portale di Azure nelle **proprietà Single Sign-On** dell'applicazione sotto l'intestazione **Certificato di firma SAML**. da dove può essere scaricato per il caricamento nell'app.  <p>Se l'applicazione dispone di più certificati, è possibile trovare tutti i certificati nel file XML dei metadati federativi. |
| **Identificatore/ "emittente"**<p>Identificatore dell'IdP dal punto di vista dell'app (talvolta denominato "ID emittente").<p>Nel token SAML, il valore viene visualizzato come elemento Issuer.| L'identificatore per ADFS è in genere l'identificatore del servizio federativo in Gestione AD FS in **Servizio > Modifica proprietà servizio federativo**. Per esempio:[http://fs.contoso.com/adfs/services/trust](http://fs.contoso.com/adfs/services/trust)| Sostituirlo con l'ID tenant.<p>[https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| **Metadati di federazione IdP**<p>Percorso dei metadati federativi disponibili pubblicamente dell'IdP. Alcune app usano i metadati di federazione come alternativa alla configurazione di URL, identificatore e certificato per la firma di token eseguita singolarmente dall'amministratore.| Trovare l'URL dei metadati federativi di ADFS in Gestione AD FS in **Endpoint > metadati > > Tipo: Metadati federativi**. Per esempio:[https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml](https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml)| Il valore corrispondente per Azure [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml)AD segue il modello . Sostituire il nome del tenant nel formato "contoso.onmicrosoft.com".   <p>Per altre informazioni, vedere [Metadati della federazione](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata). |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Rappresentare i criteri di sicurezza di ADFS in Azure ADRepresent AD FS security policies in Azure ADRepresent AD FS security policies in Azure

Quando si sposta l'autenticazione dell'app in Azure AD, creare mapping dai criteri di sicurezza esistenti alle varianti equivalenti o alternative disponibili in Azure AD. Garantire che questi mapping possano essere eseguiti rispettando gli standard di sicurezza richiesti dai proprietari delle app renderà il resto della migrazione delle app notevolmente più semplice.

Per ogni tipo di regola e i relativi esempi, è consigliabile suggerire in questo modo come la regola si presenta come in ADFS, il codice equivalente del linguaggio delle regole di ADFS e come questa mappa in Azure AD.

### <a name="map-authorization-rules"></a>Regole di autorizzazione mappa

Di seguito sono riportati esempi di tipi di regole di autorizzazione in ADFS e come è possibile mapparle ad Azure AD:The following are examples of types of authorization rules in AD FS, and how you can map them to Azure AD:

#### <a name="example-1-permit-access-to-all-users"></a>Esempio 1: Consentire l'accesso a tutti gli utentiExample 1: Permit access to all users

Consentire l'accesso a tutti gli utenti è simile in ADFS:Permit Access to All Users looks like in AD FS: 

![Fase 1 della migrazione ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Questo mapping ad Azure AD in uno dei modi seguenti:This maps to Azure AD in one of the following ways:

Nel [portale di Azure:](https://portal.azure.com/)
* Opzione 1: impostare Assegnazione utente richiesta su NoOption 1: Set User assignment required to No ![modificare i criteri di controllo di accesso per le app SaaS ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Si noti che l'impostazione dell'opzione Assegnazione utente necessaria su Sì richiede che gli utenti siano assegnati all'applicazione per ottenere l'accesso. Se impostato su No, tutti gli utenti hanno accesso. Questa opzione non controlla ciò che viene visualizzato per gli utenti nell'esperienza My Apps. 

 
* Opzione 2: nella scheda Utenti e gruppi, assegnare l'applicazione al gruppo automatico "Tutti gli utenti". <p>
È necessario abilitare i [gruppi dinamici](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) nel tenant di Azure AD affinché il gruppo predefinito "Tutti gli utenti" sia disponibile.

   ![Le mie app SaaS in Azure ADMy SaaS Apps in Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>Esempio 2: Consentire un gruppo in modo esplicitoExample 2: Allow a group explicitly

Autorizzazione esplicita del gruppo in ADFS:Explicit group authorization in AD FS:


![Regole di autorizzazione per l'rilascio ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


Ecco come la regola esegue il mapping ad Azure AD:This is how the rule maps to Azure AD:

Nel [portale](https://portal.azure.com/)di Azure si [creerà](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) innanzitutto un gruppo di utenti che corrisponde al gruppo di utenti di ADFS e quindi si assegnerà le autorizzazioni dell'app a tale gruppo:

![Aggiungi assegnazione ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>Esempio 3: Autorizzare un utente specificoExample 3: Authorize a specific user

Autorizzazione utente esplicita in ADFS:Explicit user authorization in AD FS:

![Regole di autorizzazione per l'rilascio ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Ecco come la regola esegue il mapping ad Azure AD:This is how the rule maps to Azure AD:

Nel [portale di Azure](https://portal.azure.com/)aggiungere un utente all'app tramite la scheda Aggiungi assegnazione dell'app, come illustrato di seguito:

![Le mie app SaaS in Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>Mappare le regole di Multi-Factor AuthenticationMap Multi-Factor Authentication rules 

Una distribuzione locale di [Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) e ADFS continuerà a funzionare dopo la migrazione perché si è federati con ADFS. Tuttavia, prendere in considerazione la migrazione alle funzionalità di autenticazione a più fattori predefinite di Azure che sono legate ai flussi di lavoro di accesso condizionale di Azure AD. 

Di seguito sono riportati esempi di tipi di regole di autenticazione a più fattori in ADFS e come è possibile mapparle ad Azure AD in base a condizioni diverse:The following are examples of types of MFA rules in AD FS, and how you can map them to Azure AD based on different conditions:

Impostazioni delle regole di autenticazione a più fattori in ADFS:MFA rule settings in AD FS:

![Impostazioni dell'autenticazione a più fattori di Azure ADAzure AD MFA settings](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Esempio 1: Applicare l'autenticazione a più fattori in base a utenti/gruppiExample 1: Enforce MFA based on users/groups

Il selettore Utente/Gruppi è una regola che consente di applicare l'autenticazione a più fattori in base ai gruppi (SID di gruppo) o a utente (SID primario). Oltre alle assegnazioni Utente/Gruppi, tutte le caselle di controllo aggiuntive nell'interfaccia utente di configurazione ad ADFS come regole aggiuntive che vengono valutate dopo l'applicazione della regola Utente/Gruppi. 


Specificare le regole di autenticazione a più fattori per un utente o un gruppo in Azure AD:Specify MFA rules for a user or a group in Azure AD:

1. Creare un [nuovo criterio di accesso condizionale](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Selezionare **Assegnazioni**. Aggiungere gli utenti o i gruppi per i quali si desidera applicare l'autenticazione a più fattori.

3. Configurare le opzioni **dei controlli** di accesso come illustrato di seguito:  
‎

![Impostazioni di AAD MFA](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Esempio 2: Applicare l'autenticazione a più fattori per i dispositivi non registratiExample 2: Enforce MFA for unregistered devices

Specificare le regole di autenticazione a più fattori per i dispositivi non registrati in Azure AD:Specify MFA rules for unregistered devices in Azure AD:

1. Creare un [nuovo criterio di accesso condizionale](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Impostare **Assegnazioni** su **Tutti gli utenti**.

3. Configurare le opzioni **dei controlli** di accesso come illustrato di seguito:  
‎

![Impostazioni di AAD MFA](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
Quando imposti l'opzione Per più controlli su Richiedi uno dei controlli selezionati, significa che se una delle condizioni specificate dalla casella di controllo viene soddisfatta dall'utente, gli verrà concesso l'accesso all'app.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Esempio 3: Applicare l'autenticazione a più fattori in base alla posizioneExample 3: Enforce MFA based on location

Specificare le regole di autenticazione a più fattori in base alla posizione di un utente in Azure AD:Specify MFA rules based on a user's location in Azure AD:

1. Creare un [nuovo criterio di accesso condizionale](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

1. Impostare **Assegnazioni** su **Tutti gli utenti**.

1. [Configurare percorsi denominati in Azure AD,](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) altrimenti la federazione dall'interno della rete aziendale è attendibile. 

1. Configurare le **regole condizioni** per specificare i percorsi per i quali si desidera applicare l'autenticazione a più fattori.

![Impostazioni dell'autenticazione a più fattori di Azure ADAzure AD MFA settings](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Configurare le opzioni **dei controlli** di accesso come illustrato di seguito:


![Mappare i criteri di controllo di accessoMap access control policies](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>Mappare gli attributi di emettitore come regola attestazioniMap emit attributes as Claims rule

Di seguito è riportato un esempio di come vengono mappati gli attributi in ADFS:Here is an example of how attributes are mapped in AD FS:

![Impostazioni dell'autenticazione a più fattori di Azure ADAzure AD MFA settings](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


Ecco come la regola esegue il mapping ad Azure AD:This is how the rule maps to Azure AD:

Nel [portale di Azure](https://portal.azure.com/)selezionare **Applicazioni Enterprise**, **Single Sign-On**e aggiungere **gli attributi del token SAML** come illustrato di seguito:

![Impostazioni dell'autenticazione a più fattori di Azure ADAzure AD MFA settings](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Mappare i criteri di controllo di accesso incorporatiMap built-In access control policies

AD FS 2016 dispone di diversi criteri di controllo di accesso incorporati che è possibile scegliere tra:AD FS 2016 has several built-in access control policies that you can choose from:

![Controllo degli accessi integrato in Azure ADAzure AD built in access control](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
Per implementare criteri predefiniti in Azure AD, è possibile usare nuovi criteri di [accesso condizionale](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json) e configurare i controlli di accesso oppure è possibile usare la finestra di progettazione criteri personalizzati in ADFS 2016 per configurare i criteri di controllo di accesso. L'Editor delle regole dispone di un elenco completo di opzioni Permesso e Eccezione che possono aiutarti a creare tutti i tipi di permutazioni.

![Criteri di controllo di accesso di Azure ADAzure AD access control policies](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



In questa tabella sono elencate alcune utili opzioni Permit e Except e la modalità di mapping ad Azure AD. 


| | Come configurare l'opzione Di autorizzazione in Azure AD?| Come configurare l'opzione Except in Azure AD? |
| - | - | - |
| Da specifico rete| Esegue il mapping a [una posizione denominata](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) in Azure ADMaps to Named Location in Azure AD| Utilizzare l'opzione **Escludi** per [i percorsi attendibili](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) |
| Da specifico gruppi| [Impostazione di un'assegnazione utente/gruppi](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| Utilizzare l'opzione **Escludi** in Utenti e gruppi |
| Da dispositivi con livello di attendibilità specifico| Impostare questa opzione dal controllo 'Stato dispositivo' in Assegnazioni -> Condizioni| Utilizzare l'opzione **Escludi** in Condizione stato dispositivo e Includi **tutti i dispositivi** |
| Con rivendicazioni specifiche nella richiesta| Questa impostazione non può essere migrata| Questa impostazione non può essere migrata |


Un esempio di come configurare l'opzione Escludi per i percorsi attendibili nel portale di Azure:An example of how to configure the Exclude option for trusted locations in the Azure Portal:

![Schermata dei criteri di controllo di accesso di mapping](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Eseguire la transizione degli utenti da ADFS ad Azure ADTransition users from AD FS to Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Sincronizzare i gruppi DI ADFS in Azure ADSync AD FS groups in Azure AD

Quando si esegue il mapping delle regole di autorizzazione, le app che eseguono l'autenticazione con ADFS possono usare i gruppi di Active Directory per le autorizzazioni. In tal caso, usare [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) per sincronizzare questi gruppi con Azure AD prima di eseguire la migrazione delle applicazioni. Assicurarsi di verificare tali gruppi e l'appartenenza prima della migrazione in modo da poter concedere l'accesso agli stessi utenti durante la migrazione dell'applicazione.

Per ulteriori informazioni, vedere [Prerequisiti per l'utilizzo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims)degli attributi di gruppo sincronizzati da Active Directory .

### <a name="setup-user-self-provisioning"></a>Configurare il provisioning automatico degli utenti 

Alcune applicazioni SaaS supportano la possibilità di eseguire il provisioning automatico degli utenti quando accedono per la prima volta all'applicazione. In Azure Active Directory (Azure AD) il termine provisioning delle app si riferisce alla creazione automatica di identità utente e ruoli nelle applicazioni cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) a cui gli utenti devono accedere. Gli utenti di cui viene eseguita la migrazione disfaranno già di un account nell'applicazione SaaS. Sarà necessario eseguire il provisioning di tutti i nuovi utenti aggiunti dopo la migrazione. Testare il [provisioning dell'app SaaS](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) dopo la migrazione dell'applicazione.

### <a name="sync-external-users-in-azure-ad"></a>Sincronizzare gli utenti esterni in Azure ADSync external users in Azure AD

Gli utenti esterni esistenti possono essere impostati in due modi principali all'interno di ADFS:Your existing external users may be set up in two main ways within AD FS:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Utenti esterni con un account locale all'interno dell'organizzazione

Continuerai a essere in grado di utilizzare questi account nello stesso modo in cui funzionano gli account utente interni. Questi account utente esterni hanno un nome principale all'interno dell'organizzazione, anche se il messaggio di posta elettronica dell'account potrebbe puntare esternamente. Durante l'avanzamento della migrazione, è possibile sfruttare i vantaggi offerti da [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) eseguendo la migrazione di questi utenti per l'utilizzo della propria identità aziendale quando è disponibile un'identità di questo tipo. Questo semplifica il processo di accesso per gli utenti, in quanto spesso accedono con il proprio account di accesso aziendale. Anche l'amministrazione dell'organizzazione verrà allimitata, non dovendo più gestire gli account per gli utenti esterni.

#### <a name="federated-external-identities"></a>Identità esterne federate

Se si esegue la federazione con un'organizzazione esterna, è necessario adottare alcuni approcci:If you are currently federating with an external organization, you have a few approaches to take:

* [Aggiungere gli utenti di Collaborazione B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)di Azure nel portale di Azure. È possibile inviare in modo proattivo inviti di collaborazione B2B dal portale amministrativo di Azure AD all'organizzazione partner affinché i singoli membri possano continuare a usare le app e le risorse a cui sono abituati. 

* Creare un flusso di lavoro di [iscrizione B2B self-service](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal) che genera una richiesta per singoli utenti nell'organizzazione partner utilizzando l'API di invito B2B.

Indipendentemente dalla configurazione degli utenti esterni esistenti, è probabile che dispongano di autorizzazioni associate al proprio account, in appartenenza al gruppo o autorizzazioni specifiche. Valutare se è necessario eseguire la migrazione o la pulizia di queste autorizzazioni. Gli account all'interno dell'organizzazione che rappresentano un utente esterno devono essere disabilitati dopo la migrazione dell'utente a un'identità esterna. Il processo di migrazione deve essere discusso con i partner commerciali, in quanto potrebbe verificarsi un'interruzione nella capacità di connettersi alle risorse.

## <a name="migrate-and-test-your-apps"></a>Eseguire la migrazione e il test delle appMigrate and test your apps

Seguire il processo di migrazione descritto in questo articolo.

Passare quindi al portale di [Azure](https://aad.portal.azure.com/) per verificare se la migrazione è stata esito positivo. Seguire le istruzioni riportate di seguito:
1. Selezionare **Applicazioni** > aziendali**Tutte le applicazioni** e trovare l'app dall'elenco.

1. Seleziona **Gestisci** > **utenti e gruppi** per assegnare almeno un utente o un gruppo all'app.

1. Selezionare **Gestisci** > **accesso condizionale**. Esaminare l'elenco dei criteri e assicurarsi di non bloccare l'accesso all'applicazione con criteri [di accesso condizionale.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

A seconda della configurazione dell'app, verificare che SSO funzioni correttamente. 

| Tipo di autenticazione| Test |
| - | - |
| OAuth / OpenID Connect| Selezionare **Applicazioni > autorizzazioni** aziendali e assicurarsi di aver acconsentito all'applicazione da utilizzare nell'organizzazione nelle impostazioni utente per l'app.  
‎ |
| SSO basato su SAML| Utilizzare il pulsante [Test impostazioni SAML](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues) disponibile in **Single Sign-On**.  
‎ |
| SSO basato su password| Scaricare e installare [myApps Secure Sign](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[in Extension](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction). Questa estensione consente di avviare qualsiasi app cloud dell'organizzazione che richiede l'utilizzo di un processo SSO.  
‎ |
| Proxy dell'applicazione| Assicurarsi che il connettore sia in esecuzione e assegnato all'applicazione. Per ulteriore assistenza, consulta la guida[ ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)alla risoluzione dei problemi del proxy di [applicazione.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)  
‎ |

> [!NOTE]
> I cookie dell'ambiente ADFS precedente saranno ancora persistenti nei computer dell'utente. Questi cookie potrebbero causare problemi con la migrazione in quanto gli utenti potrebbero essere indirizzati all'ambiente di accesso ADFS precedente rispetto al nuovo account di accesso di Azure AD. Potrebbe essere necessario cancellare i cookie del browser utente manualmente o utilizzando uno script. È anche possibile usare System Center Configuration Manager o una piattaforma simile.

### <a name="troubleshoot"></a>Risolvere problemi

Se sono presenti errori dal test delle applicazioni migrate, la risoluzione dei problemi potrebbe essere il primo passaggio prima di eseguire il fallback alle relying party ADFS esistenti. Vedere [Come eseguire il debug dell'accesso Single Sign-On basato su SAML alle applicazioni in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues)

### <a name="rollback-migration"></a>Rollback della migrazione

Se la migrazione non riesce, è consigliabile lasciare le relying party esistenti nei server ADFS e rimuovere l'accesso alle entità di lavoro. Ciò consentirà un fallback rapido, se necessario, durante la distribuzione.

### <a name="end-user-communication"></a>Comunicazione con l'utente finale

Anche se la finestra di interruzione pianificata può essere minima, è comunque consigliabile pianificare la comunicazione di questi intervalli di tempo in modo proattivo ai dipendenti, creando il cut-over da AD FS ad Azure AD. Assicurati che l'esperienza dell'app abbia un pulsante Feedback o puntatori al supporto tecnico per i problemi.

Una volta completata la distribuzione, è possibile inviare comunicazioni informando gli utenti della distribuzione riuscita e ricordare loro eventuali nuovi passaggi che devono eseguire.

* Indicare agli utenti di utilizzare il [pannello di accesso](https://myapps.microsoft.com.com/) per accedere a tutte le applicazioni migrate. 

* Ricordare agli utenti che potrebbero avere bisogno di aggiornare le impostazioni di autenticazione a più fattori. 

* Se viene distribuita la reimpostazione della password self-service, gli utenti potrebbero dover aggiornare o verificare i metodi di autenticazione. Vedere Modelli di comunicazione per l'utente finale [di MFA](https://aka.ms/mfatemplates) e [SSPR.](https://aka.ms/ssprtemplates)

Comunicazione con utenti esterni: questo gruppo di utenti è in genere il più colpito in modo critico in caso di problemi. Ciò è particolarmente vero se la posizione di sicurezza determina un set diverso di regole di accesso condizionale o profili di rischio per i partner esterni. Assicurarsi che i partner esterni siano a conoscenza della pianificazione della migrazione cloud e abbiano un intervallo di tempo durante il quale vengono incoraggiati a partecipare a una distribuzione pilota che testa tutti i flussi univoci per la collaborazione esterna. Infine, assicurarsi che abbiano un modo per accedere all'helpdesk in caso di problemi di interruzione.

## <a name="next-steps"></a>Passaggi successivi
Leggere [Migrazione dell'autenticazione dell'applicazione in Azure ADRead Migrating application authentication to Azure AD](https://aka.ms/migrateapps/whitepaper)<p>
Configurare [l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) e l'autenticazione [a più fattoriSet](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) up Conditional Access and MFA
