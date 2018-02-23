---
title: Eseguire la migrazione ad Azure di app locali di AD FS | Microsoft Docs
description: Questo documento illustra come le organizzazioni possono eseguire la migrazione ad Azure AD di applicazioni locali, in particolare di applicazioni SaaS federate.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/29/2018
ms.author: billmath
ms.openlocfilehash: ec0731534da2543d48bedc575bf882b790fa136b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="migrate-ad-fs-on-premises-apps-to-azure"></a>Eseguire la migrazione ad Azure di app locali di AD FS 

Questo documento illustra come le organizzazioni possono eseguire la migrazione ad Azure AD di applicazioni locali  ed è incentrato sulle applicazioni SaaS federate.  Non offre istruzioni dettagliate,  ma indicazioni concettuali che consentiranno di realizzare la migrazione comprendendo come le configurazioni locali verranno convertite in Azure AD. Vengono inoltre descritti i requisiti degli scenari più comuni.

## <a name="introduction"></a>Introduzione

Se si ha una directory locale contenente account utente, è probabile che si abbiano almeno una o due app  e che queste siano configurate in modo che gli utenti possano accedervi usando tali identità.

Nella maggior parte delle organizzazioni vengono adottate in varia misura identità e applicazioni cloud  ed è possibile che vengano usati Office 365 e Azure AD Connect  e siano state configurate applicazioni SaaS basate sul cloud per alcuni carichi di lavoro chiave ma non per tutti.  

Molte organizzazioni hanno applicazioni line-of-business personalizzate o SaaS che sono federate direttamente a un servizio di accesso locale come Active Directory Federation Service (AD FS), nonché app basate su Office 365 e Azure AD.  Questa guida alla migrazione descrive perché e come eseguire la migrazione ad Azure AD di applicazioni locali.

>[!NOTE]
>Questa guida contiene informazioni dettagliate sulla configurazione e la migrazione di app SaaS, con informazioni generali sulle applicazioni line-of-business personalizzate.  Indicazioni più dettagliate per le app line-of-business personalizzate saranno disponibili in futuro.

Figura 1: App connesse direttamente in locale![In locale](media/migrate-adfs-apps-to-azure/migrate1.png)

Figura 2: App federate tramite Azure AD ![Azure](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="why-migrate-apps-to-azure-ad"></a>Vantaggi della migrazione di app ad Azure AD

Per un'organizzazione che usa già AD FS, Ping o un altro provider di autenticazione locale, la migrazione delle app ad Azure AD offre i vantaggi seguenti:

**Accesso più sicuro**
- È possibile configurare controlli di accesso per applicazione granulari, inclusa l'autenticazione a più fattori (MFA), usando l'[accesso condizionale di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).  I criteri possono essere applicati alle app SaaS e personalizzate così come eventualmente già applicati per Office 365.
- Per rilevare le minacce e proteggere l'accesso in base ad apprendimento automatico ed euristica per identificare il traffico rischioso, è possibile sfruttare le funzionalità predefinite e in continua evoluzione di Azure AD con [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection).

**Collaborazione B2B di Azure AD**
- Quando l'accesso alle app SaaS è basato su Azure AD, è possibile concedere ai partner l'accesso alle risorse cloud con [Collaborazione B2B di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

**Esperienza di amministrazione più semplice e funzionalità aggiuntive di Azure AD**
- Come provider di identità per le app SaaS, Azure AD supporta funzionalità aggiuntive come i certificati per la firma di token per applicazione, le [date di scadenza dei certificati configurabili](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs) e il [provisioning automatizzato](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) degli account utente (nelle principali app della raccolta) in base alle identità di Azure AD.

**Mantenimento dei vantaggi di un provider di identità locale**
- Pur ottenendo i vantaggi di Azure AD, è possibile continuare a usare la soluzione locale per l'autenticazione usufruendo così di vantaggi come registrazione, controllo e soluzioni di autenticazione a più fattori (MFA) locali. 

**Accelerazione del ritiro del provider di identità locale**
- Per le organizzazioni che intendono ritirare il prodotto di autenticazione locale, la migrazione delle app ad Azure AD facilita la transizione rimuovendo parte del lavoro. 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>Mapping tra tipi di app in locale e tipi di app in Azure AD
In base al tipo di accesso usato, la maggior parte delle app rientra in una specifica categoria.  Queste categorie determinano il modo in cui l'app viene rappresentata in Azure AD.

In breve, le applicazioni SAML 2.0 possono essere integrate con Azure AD tramite la raccolta di applicazioni di Azure AD o come applicazioni non incluse nella raccolta.  Analogamente, le app che usano OAuth 2.0 oppure OpenID Connect possono essere integrate con Azure AD come "registrazioni di app".  Continuare a leggere per altri dettagli.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>App line-of-business personalizzate e app SaaS federate
Le app federate includono le app che rientrano nelle categorie elencate.

- App SaaS 
    - Se gli utenti accedono ad app SaaS come Salesforce, ServiceNow o Workday e si esegue l'integrazione in un provider di identità locale come AD FS o Ping, si usa l'accesso federato per le app SaaS.
    - Le app in genere usano il protocollo SAML 2.0 per l'accesso federato.
    - Le applicazioni appartenenti a questa categoria possono essere integrate con Azure AD come applicazioni aziendali, dalla raccolta oppure come applicazioni non incluse nella raccolta.
- Applicazioni line-of-business personalizzate
    - Si tratta di app non SaaS sviluppate internamente dall'organizzazione o disponibili come prodotto confezionato standard installato nel data center,  ad esempio app SharePoint e app basate su Windows Identity Foundation (WIF).
    - Per l'accesso federato, le app possono usare SAML 2.0, WS-Federation, OAuth oppure OpenID Connect.
    - Le app personalizzate che usano Oauth 2.0, OpenID Connect o WS-Federation possono essere integrate con Azure AD come registrazioni di app, mentre quelle che usano SAML 2.0 o WS-Federation possono essere integrate come applicazioni non incluse nella raccolta nell'ambito delle applicazioni aziendali.

### <a name="non-federated-apps"></a>App non federate
Con Azure AD possono essere integrate anche app non federate, usando il proxy di applicazione di Azure AD e le funzionalità correlate.  Per altre informazioni sulle funzionalità disponibili, visitare i collegamenti seguenti:
- App che usano l'autenticazione integrata di Windows direttamente in Active Directory
    - Queste app possono essere integrate in Azure AD tramite il [proxy di applicazione di Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal)
- App che vengono integrate con il provider di servizi Single Sign-On tramite un agente e usano intestazioni per l'autorizzazione
    - Le app locali che usano un agente installato per l'accesso e l'autorizzazione basata su intestazione possono essere configurate per l'accesso basato su Azure AD usando il proxy di applicazione di Azure AD con [PingAccess per Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/)

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Conversione delle app federate locali in Azure AD 
Fortunatamente, AD FS e Azure AD presentano un funzionamento simile, di conseguenza i concetti relativi alla configurazione degli identificatori e degli URL di accesso, disconnessione e trust si applicano in entrambi i casi.  Esistono tuttavia alcune lievi differenze che è necessario conoscere quando si effettua la transizione.

Nella tabella sono illustrati diversi concetti chiave condivisi da AD FS, Azure AD e app SaaS, per facilitare la conversione. 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Rappresentazione dell'app in Azure AD o AD FS
La migrazione ha inizio con la valutazione della configurazione dell'applicazione in locale e il mapping di tale configurazione ad Azure AD.  Di seguito è riportato il mapping tra gli elementi di configurazione della relying party di AD FS e gli elementi corrispondenti in Azure AD.  
- Termine in AD FS: componente o attendibilità del componente (relying party o trust della relying party)
- Termine in Azure AD: applicazione aziendale o registrazione di app (a seconda del tipo di app)

|Elemento di configurazione dell'app|DESCRIZIONE|Nella configurazione di AD FS|Posizione corrispondente nella configurazione di Azure AD|Elemento nel token SAML|
|-----|-----|-----|-----|-----|
|URL di accesso dell'app|URL della pagina di accesso dell'applicazione. È la pagina a cui l'utente passa per avviare l'accesso all'app in un flusso SAML avviato dal provider di servizi.|N/D|In Azure AD, l'URL di accesso viene configurato nel portale di Azure, nel campo URL di accesso delle proprietà di Single Sign-On dell'applicazione.</br></br>Per visualizzare l'URL di accesso potrebbe essere necessario fare clic su Mostra impostazioni URL avanzate.||
|URL di risposta dell'app|URL dell'app dal punto di vista del provider di identità.  È l'URL a cui vengono inviati l'utente e il token dopo l'accesso dell'utente nel provider di identità.</br></br>  È talvolta definito endpoint consumer di asserzione SAML.|Si trova nel trust della relying party di AD FS per l'app.  Fare clic con il pulsante destro del mouse sulla relying party e scegliere "Proprietà" -> scheda "Endpoint".|In Azure AD, l'URL di risposta viene configurato nel portale di Azure, nel campo URL di risposta delle proprietà di Single Sign-On dell'applicazione.</br></br>Per visualizzare l'URL di risposta potrebbe essere necessario fare clic su Mostra impostazioni URL avanzate.|Viene eseguito il mapping all'elemento Destination nel token SAML.</br></br>  Valore di esempio: https://contoso.my.salesforce.com|
|URL di disconnessione dell'app|URL a cui vengono inviate le richieste di "pulizia di disconnessione" quando un utente si disconnette da un'app, per disconnettere tutte le altre app a cui il provider di identità ha effettuato l'accesso dell'utente.|Si trova in Attendibilità componente in Gestione AD FS.  Fare clic con il pulsante destro del mouse sulla relying party, quindi scegliere "Proprietà" e fare clic sulla scheda "Endpoint".|N/D. Azure AD non supporta un "punto di disconnessione singolo", ossia la disconnessione di tutte le app.  L'utente viene disconnesso semplicemente da Azure AD.|ND|
|Identificatore dell'app|Identificatore dell'app dal punto di vista del provider di identità. Come identificatore viene usato spesso il valore dell'URL di accesso, ma non sempre.</br></br>  Talvolta nell'app viene chiamato "ID entità".|In AD FS è l'ID della relying party. Fare clic con il pulsante destro del mouse sul trust della relying party, quindi scegliere "Proprietà" e fare clic sulla scheda "Identificatori".|In Azure AD, l'identificatore viene configurato nel portale di Azure, nel campo Identificatore sotto URL e dominio nelle proprietà di Single Sign-On dell'applicazione. Potrebbe essere necessario fare clic sulla casella di controllo "Mostra impostazioni URL avanzate".|Corrisponde all'elemento Audience nel token SAML.|
|Metadati di federazione dell'app|Posizione dei metadati di federazione dell'app.  Viene usata dal provider di identità per aggiornare automaticamente specifiche impostazioni di configurazione come gli endpoint o i certificati di crittografia.|L'URL dei metadati di federazione dell'app si trova nel trust della relying party di AD FS per l'app.  Fare clic con il pulsante destro del mouse sul trust, scegliere Proprietà e quindi fare clic sulla scheda Monitoraggio.|N/D. Azure AD non supporta l'utilizzo diretto dei metadati di federazione dell'applicazione.|ND|
|ID utente/NameID|Attributo usato per indicare in modo univoco l'identità utente da Azure AD o AD FS all'app.</br></br>  In genere è il nome dell'entità utente (UPN) o l'indirizzo di posta elettronica dell'utente.|In AD FS è riportato come regola attestazioni per la relying party.  Nella maggior parte dei casi è la regola attestazioni che rilascia un'attestazione con un tipo che termina con "nameidentifier".|In Azure AD, l'ID utente è riportato nel portale di Azure sotto l'intestazione Attributi utente nelle proprietà di Single Sign-On dell'applicazione.</br></br>Per impostazione predefinita, viene usato il nome dell'entità utente.|Viene comunicato dal provider di identità all'app come elemento "NameID" nel token SAML.|
|Altre attestazioni che devono essere inviate all'app|In aggiunta a ID utente/NameID, dal provider di identità all'app vengono in genere inviate altre informazioni di attestazione come nome, cognome, indirizzo di posta elettronica e gruppi di cui l'utente è membro.|In AD FS sono riportate come altre regole attestazioni per la relying party.|In Azure AD sono riportate nel portale di Azure sotto l'intestazione Attributi utente nelle proprietà di Single Sign-On dell'applicazione. Fare clic su Visualizza e modifica tutti gli altri attributi utente.|| 

### <a name="representing-azure-ad-as-an-identity-provider-idp-in-a-saas-app"></a>Rappresentazione di Azure AD come provider di identità (IdP) in un'app SaaS
Come parte della migrazione, l'app deve essere configurata in modo da puntare ad Azure AD anziché al provider di identità locale.  Questa sezione è incentrata principalmente sulle app SaaS che usano il protocollo SAML e non sulle app line-of-business/personalizzate. I concetti descritti sono tuttavia estendibili alle app line-of-business/personalizzate. 

A livello generale, affinché un'app SaaS punti ad Azure AD sono necessari alcuni elementi chiave.
- Autorità di certificazione del provider di identità: https&#58;//sts.windows.net/{id-tenant}/
- URL di accesso del provider di identità: https&#58;//login.microsoftonline.com/{id-tenant}/saml2
- URL di disconnessione del provider di identità: https&#58;//login.microsoftonline.com/{id-tenant}/saml2 
- Posizione dei metadati di federazione: https&#58;//login.windows.net/{id-tenant} <id-tenant>/federationmetadata/2007-06/federationmetadata.xml?appid={<id-applicazione} 

{id-tenant} verrà sostituito con l'ID del tenant, riportato come "ID directory" nel portale di Azure in Azure Active Directory -> Proprietà, mentre {id-applicazione} verrà sostituito con l'ID dell'applicazione riportato come "ID applicazione" nelle proprietà dell'applicazione.

La tabella descrive in modo più dettagliato i principali elementi di configurazione del provider di identità per configurare le impostazioni SSO nell'app, con i rispettivi valori o posizioni in AD FS e Azure AD.  La tabella fa riferimento a un'app SaaS, che deve sapere dove inviare le richieste di autenticazione e come convalidare i token ricevuti.

|Elemento di configurazione|DESCRIZIONE|AD FS|Azure AD|
|---|---|---|---|
|URL </br>di accesso </br>provider di identità|URL di accesso del provider di identità dal punto di vista dell'app, ossia l'URL a cui l'utente verrà reindirizzato per l'accesso.|L'URL di accesso di AD FS è il nome del servizio federativo AD FS seguito da "/adfs/ls/", ad esempio https&#58;//fs.contoso.com/adfs/ls/|Il valore corrispondente per Azure AD segue questo modello, in cui {id-tenant} verrà sostituito con l'ID del tenant, riportato come "ID directory" nel portale di Azure in Azure Active Directory -> Proprietà.</br></br>Per le app che usano il protocollo SAML-P, è https&#58;//login.microsoftonline.com</br>/{id-tenant}/saml2 </br></br>Per le app che usano il protocollo WS-Federation, è https&#58;//login.microsoftonline.com</br>/{id-tenant}/wsfed|
|URL </br>di disconnessione </br>provider di identità|URL di disconnessione del provider di identità dal punto di vista dell'app, ossia l'URL a cui l'utente viene reindirizzato quando sceglie di "disconnettersi" dall'app.|Per AD FS, l'URL di disconnessione è uguale all'URL di accesso oppure è lo stesso URL con l'aggiunta di "wa=wsignout1.0", ad esempio https&#58;//fs.contoso.com/adfs/ls /?wa=wsignout1.0|Il valore corrispondente per Azure AD varia a seconda che l'app supporti o meno la disconnessione SAML 2.0.</br></br>Se l'app supporta la disconnessione SAML, il valore segue questo modello, in cui {id-tenant} verrà sostituito con l'ID del tenant, riportato come "ID directory" nel portale di Azure in Azure Active Directory -> Proprietà. https&#58;//login.microsoftonline.com</br>/{id-tenant}/saml2</br></br>Se l'app non supporta la disconnessione SAML, è https&#58;//login.microsoftonline.com</br>/common /wsfederation?wa=wsignout1.0|
|Certificato </br>per la firma </br>di token|Certificato la cui chiave privata viene usata dal provider di identità per firmare i token rilasciati.  Verifica che il token provenga dallo stesso provider di identità per cui è stato configurato il trust nell'app.|Il certificato per la firma di token di AD FS si trova in Certificati in Gestione AD FS.|In Azure AD, il certificato per la firma di token è riportato nel portale di Azure sotto l'intestazione Certificato di firma SAML nelle proprietà di Single Sign-On dell'applicazione, da dove può essere scaricato per il caricamento nell'app.</br></br>  Se l'applicazione ha più certificati, si trovano tutti nel file XML dei metadati di federazione.|
|Identificatore/ </br>"autorità di certificazione"|Identificatore del provider di identità dal punto di vista dell'app, talvolta denominato "autorità di certificazione" o "ID autorità di certificazione".</br></br>Nel token SAML, il valore è presente come elemento "Issuer".|L'identificatore per AD FS è in genere l'identificatore del servizio federativo riportato in Servizio -> Modifica proprietà servizio federativo in Gestione AD FS.  Ad esempio, http&#58;//fs.contoso.com/adfs/services/trust|Il valore corrispondente per Azure AD segue questo modello, in cui {id-tenant} verrà sostituito con l'ID del tenant, riportato come "ID directory" nel portale di Azure in Azure Active Directory -> Proprietà.  https&#58;//sts.windows.net/{id-tenant}/|
|Metadati </br>federazione </br>provider di identità|Posizione dei metadati di federazione disponibili pubblicamente del provider di identità.  I metadati di federazione vengono usati da alcune app come alternativa alla configurazione di URL, identificatore e certificato per la firma di token eseguita singolarmente dall'amministratore.|L'URL dei metadati di federazione di AD FS è riportato in Servizio -> Endpoint -> Metadati -> Tipo: Metadati federativi in Gestione AD FS. Ad esempio: https&#58;//fs.contoso.com/ FederationMetadata/2007-06/</br>FederationMetadata.xml|Il valore corrispondente per Azure AD segue il modello https&#58;//login.microsoftonline.com</br>/{NomeDominioTenant}/FederationMetadata/2007-06/</br>FederationMetadata.xml, dove il valore di {NomeDominioTenant} verrà sostituito con il nome del tenant nel formato "contoso.onmicrosoft.com". </br></br>[Altre informazioni](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata) sui metadati di federazione in Azure AD.

## <a name="migrating-saas-apps"></a>Migrazione delle app SaaS
La migrazione di app SaaS da AD FS o un altro provider di identità ad Azure AD è attualmente un processo manuale. Per istruzioni per app specifiche, [vedere l'elenco delle esercitazioni sull'integrazione delle app SaaS disponibili nella raccolta](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Le esercitazioni sull'integrazione presuppongono che si esegua un'integrazione "vergine".  Nel corso della pianificazione, della valutazione, della configurazione e della migrazione completa delle app è consigliabile conoscere alcuni concetti chiave specifici della migrazione.  
- Per quanto la migrazione di alcune app possa essere eseguita facilmente, le app con requisiti più complessi, come le attestazioni personalizzate, possono richiedere operazioni di configurazione aggiuntive in Azure AD e/o Azure AD Connect.
- Negli scenari più comuni, per un'app sono necessarie solo l'attestazione NameId e altre attestazioni dell'ID utente comuni. Per determinare se sono necessarie attestazioni aggiuntive, esaminare le attestazioni rilasciate da AD FS o dal provider di identità di terze parti.
- Dopo aver determinato che sono richieste attestazioni aggiuntive, si deve verificare che siano disponibili in Azure AD.  È necessario controllare la configurazione di sincronizzazione di Azure AD Connect per assicurarsi che un attributo obbligatorio, ad esempio samAccountName, venga sincronizzato con Azure AD.
- Quando gli attributi sono disponibili in Azure AD, aggiungere regole di rilascio delle attestazioni in Azure AD per includere tali attributi come attestazioni nei token rilasciati.  Questa operazione viene eseguita nelle proprietà di Single Sign-On dell'app in Azure AD.

### <a name="assessing-what-can-be-migrated"></a>Valutazione della possibilità di migrazione
Le applicazioni SAML 2.0 possono essere integrate con Azure AD tramite la raccolta di applicazioni di Azure AD o come applicazioni non incluse nella raccolta.  

Esistono alcune configurazioni che richiedono passaggi aggiuntivi per la configurazione in Azure AD e alcune che non sono attualmente supportate.  Per determinare le app che possono essere spostate, esaminare la configurazione corrente di ogni app, in particolare quanto segue:
- Regole attestazioni configurate (regole di trasformazione rilascio)
- Formato e attributo NameID SAML
- Versioni di token SAML rilasciate
- Altre configurazioni come le regole di autorizzazione rilascio o i criteri di controllo di accesso e le regole di autenticazione a più fattori (autenticazione aggiuntiva)

#### <a name="what-can-be-migrated-today"></a>App di cui è attualmente possibile eseguire la migrazione
Attualmente è possibile eseguire facilmente la migrazione delle app SAML 2.0 che usano il set standard di elementi di configurazione e attestazioni.  Queste app possono includere:
- Nome dell'entità utente
- Indirizzo di posta elettronica
- Nome
- Surname
- Un attributo alternativo come NameID SAML, ad esempio l'attributo Mail, il prefisso di posta elettronica, l'ID dipendente, gli attributi dell'estensione 1-15 di Azure AD o l'attributo SamAccountName locale (vedere [Modifica dell'attestazione NameIdentifier)](./develop/active-directory-saml-claims-customization.md)
- Attestazioni personalizzate (per informazioni sui mapping di attestazioni supportati, vedere [questo documento](active-directory-claims-mapping.md) e [questo](./develop/active-directory-saml-claims-customization.md))

Oltre alle attestazioni personalizzate e agli elementi nameID, le configurazioni che richiedono passaggi di configurazione aggiuntivi in Azure AD come parte della migrazione sono le seguenti:
- Regole MFA o di autorizzazione personalizzate in AD FS (configurate con la funzionalità di [accesso condizionale di Azure AD](active-directory-conditional-access-azure-portal.md))
- App con più endpoint SAML, configurabili in Azure AD con PowerShell (questa funzionalità non è disponibile nel portale)
- App WS-Federation come le app SharePoint che richiedono token SAML versione 1.1, che devono essere configurate manualmente con PowerShell

#### <a name="appsconfigurations-not-supported-in-azure-ad-today"></a>App/configurazioni attualmente non supportate in Azure AD
Non è attualmente possibile eseguire la migrazione delle app che richiedono le funzionalità riportate di seguito.  Se si hanno app che richiedono tali funzionalità, inserire commenti e suggerimenti nella sezione dei commenti.
- Funzionalità a livello di protocollo
    - Supporto per un punto di disconnessione singolo (SLO, Single Logout) SAML per tutte le app a cui è stato eseguito l'accesso
    - Supporto per il modello WS-Trust ActAs
    - Risoluzione artefatto SAML 
    - Verifica della firma delle richieste SAML firmate (si noti che le richieste firmate vengono accettate, ma la firma non viene verificata)
 - Funzionalità a livello di token 
     - Crittografia di token SAML 
     - Token SAML versione 1.1 nelle risposte del protocollo SAML 
- Attestazioni nelle funzionalità per i token
    - Rilascio dei nomi dei gruppi locali come attestazioni
    - Attestazioni da archivi diversi da Azure AD
    - Regole di trasformazione rilascio delle attestazioni complesse (per informazioni sui mapping di attestazioni supportati, vedere questo documento e questo)
    - Rilascio delle estensioni della directory come attestazioni
    - Specifica personalizzata del formato NameID
    - Rilascio di attributi multivalore

>[!NOTE]
>Azure AD è in costante evoluzione per l'aggiunta di altre funzionalità in quest'area. Questo documento viene aggiornato regolarmente. 

### <a name="configuring-azure-ad"></a>Configurazione di Azure AD    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Configurare le impostazioni Single Sign-On (SSO) per l'app SaaS

In Azure AD, la configurazione dell'accesso SAML richiesto dall'app viene eseguita nelle proprietà di Single Sign-On dell'app in Attributi utente, come illustrato:

![](media/migrate-adfs-apps-to-azure/migrate3.png)
- Fare clic su "Visualizza e modifica tutti gli altri attributi utente" per visualizzare gli attributi da inviare come attestazioni nel token di sicurezza.

![](media/migrate-adfs-apps-to-azure/migrate4.png)
- Fare clic sulla riga di un attributo specifico da modificare oppure su "Aggiungi attributo" per aggiungere un nuovo attributo. 
![](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Assegnare utenti all'app
Affinché gli utenti in Azure AD possano accedere a una determinata app SaaS, è necessario concedere loro l'accesso da Azure AD.

Per assegnare utenti nel portale di Azure AD, passare alla schermata dell'app SaaS nel portale e quindi fare clic su "Utenti e gruppi" nella barra laterale. Per aggiungere un utente o un gruppo, fare clic su "Aggiungi utente" nella parte superiore della schermata. 

![](media/migrate-adfs-apps-to-azure/migrate6.png) 

![](media/migrate-adfs-apps-to-azure/migrate7.png) Ai fini della verifica dell'accesso, l'app SaaS dovrebbe essere visualizzata nel [pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction), all'indirizzo http://myapps.microsoft.com, quando l'utente esegue l'accesso. Nell'esempio, all'utente è stato assegnato correttamente l'accesso sia a Salesforce che a ServiceNow.

![](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configuring-the-saas-app"></a>Configurazione dell'app SaaS
Il processo di migrazione completa dalla federazione locale ad Azure AD varia a seconda che l'app SaaS usata supporti o meno più provider di identità (IdP).  Di seguito sono riportate alcune domande comuni sul supporto di più IdP.

   **D: Che cosa significa per un'app il supporto di più IdP?**
    
   R: Le app SaaS che supportano più IdP consentono di immettere tutte le informazioni sul nuovo provider di identità (in questo caso, Azure AD) prima di eseguire il commit della modifica dell'esperienza di accesso.  Al termine della configurazione, è possibile cambiare la configurazione di autenticazione dell'app in modo da puntare ad Azure AD.

   D: Perché è importante se l'app SaaS supporta più IdP?

   R: Se non sono supportati più IdP, l'amministratore dovrà riservare un breve intervallo di tempo come interruzione di servizio o manutenzione per configurare Azure AD come nuovo provider di identità dell'app. Durante questa interruzione, agli utenti dovrà essere notificato che non potranno accedere ai propri account.

   Se un'app supporta più IdP, la configurazione del provider di identità aggiuntivo può essere eseguita in anticipo e l'amministratore può quindi cambiare semplicemente il provider di identità al momento della migrazione completa ad Azure.

   Se l'app supporta più IdP e si sceglie di gestire l'autenticazione per l'accesso con più IdP contemporaneamente, inoltre, all'utente viene offerta la possibilità di scegliere il provider di identità per l'autenticazione nella propria pagina di accesso.

#### <a name="example-multiple-idp-support"></a>Esempio: supporto di più IdP
In Salesforce, ad esempio, la configurazione del provider di identità si trova in Settings (Impostazioni) -> Company Settings (Impostazioni società) -> My Domain (Dominio personale) -> Authentication Configuration (Configurazione autenticazione).

![](media/migrate-adfs-apps-to-azure/migrate9.png)

Grazie alla configurazione creata in precedenza in Identity (Identità) -> Single sign-on settings (Impostazioni Single Sign-On), sarà possibile modificare il provider di identità per la configurazione di autenticazione da AD FS, ad esempio, ad Azure AD. 

![](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Facoltativo: configurare il provisioning utenti in Azure AD
Facoltativamente, se si vuole che Azure AD gestisca direttamente il provisioning utenti per una determinata app SaaS, vedere questo documento sulla gestione del provisioning degli account utente per le app aziendali in Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- [Gestione di applicazioni con Azure Active Directory](active-directory-enable-sso-scenario.md)
- [Manage access to apps](active-directory-managing-access-to-apps.md) (Gestire l'accesso alle app)
- [Azure AD Connect e federazione](active-directory-aadconnectfed-whatis.md)