---
title: Informazioni di riferimento sui modelli di dati per i modelli di Gestione API di Azure | Documentazione Microsoft
description: "Informazioni sulle rappresentazioni di entità e tipi per elementi comuni usati nei modelli di dati per i modelli del portale per sviluppatori in Gestione API di Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 72936a4d38f809934ddea74e5ae4a6029450a97c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-template-data-model-reference"></a>Informazioni di riferimento sui modelli di dati per i modelli di Gestione API di Azure
Questo argomento descrive le rappresentazioni di entità e tipi per elementi comuni usati nei modelli di dati per i modelli del portale per sviluppatori in Gestione API di Azure.  
  
 Per altre informazioni sull'uso dei modelli, vedere [Come personalizzare il portale per sviluppatori di Gestione API usando i modelli](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
-   [API](#API)  
-   [Riepilogo delle API](#APISummary)  
-   [Applicazione](#Application)  
-   [Allegato](#Attachment)  
-   [Codice di esempio](#Sample)  
-   [Comment](#Comment)  
-   [Filtri](#Filtering)  
-   [Intestazione](#Header)  
-   [Richiesta HTTP](#HTTPRequest)  
-   [Risposta HTTP](#HTTPResponse)  
-   [Problema](#Issue)  
-   [Operazione](#Operation)  
-   [Menu operazione](#Menu)  
-   [Voce di menu operazione](#MenuItem)  
-   [Paging](#Paging)  
-   [Parametro](#Parameter)  
-   [Prodotto](#Product)  
-   [Provider](#Provider)  
-   [Rappresentazione](#Representation)  
-   [Sottoscrizione](#Subscription)  
-   [Riepilogo delle sottoscrizioni](#SubscriptionSummary)  
-   [Informazioni sull'account utente](#UserAccountInfo)  
-   [Accesso utente](#UseSignIn)  
-   [Iscrizione utente](#UserSignUp)  
  
##  <a name="API"></a>API  
 L'entità `API` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|id|string|Identificatore di risorsa. Identifica in modo univoco l'API all'interno dell'istanza del servizio Gestione API corrente. Il valore è un URL relativo valido nel formato `apis/{id}` dove `{id}` è un identificatore API. Questa proprietà è di sola lettura.|  
|name|string|Nome dell'API. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|description|string|Descrizione dell'API. Non deve essere vuota. Può includere tag di formattazione HTML. La lunghezza massima consentita è di 1000 caratteri.|  
|serviceUrl|string|URL assoluto del servizio back-end che implementa questa API.|  
|path|string|URL relativo che identifica in modo univoco questa API e tutti i relativi percorsi delle risorse all'interno dell'istanza del servizio Gestione API. Viene aggiunto all'URL di base dell'endpoint API specificato durante la creazione dell'istanza del servizio in modo da formare un URL pubblico per questa API.|  
|protocols|matrice di valori numero|Descrive su quali protocolli è possibile richiamare le operazioni in questa API. I valori consentiti sono `1 - http` e `2 - https` o entrambi.|  
|authenticationSettings|[Impostazioni di autenticazione del server di autorizzazione](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Raccolta delle impostazioni di autenticazione incluse in questa API.|  
|subscriptionKeyParameterNames|object|Proprietà facoltativa che può essere usata per specificare nomi personalizzati per i parametri di query e/o intestazione contenenti la chiave di sottoscrizione. Quando è presente, questa proprietà deve contenere almeno una delle due proprietà seguenti.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a>Riepilogo delle API  
 L'entità `API summary` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|id|string|Identificatore di risorsa. Identifica in modo univoco l'API all'interno dell'istanza del servizio Gestione API corrente. Il valore è un URL relativo valido nel formato `apis/{id}` dove `{id}` è un identificatore API. Questa proprietà è di sola lettura.|  
|name|string|Nome dell'API. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|description|string|Descrizione dell'API. Non deve essere vuota. Può includere tag di formattazione HTML. La lunghezza massima consentita è di 1000 caratteri.|  
  
##  <a name="Application"></a>Applicazione  
 L'entità `application` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|ID|string|Identificatore univoco dell'applicazione.|  
|Titolo|string|Titolo dell'applicazione.|  
|Descrizione|string|Descrizione dell'applicazione.|  
|Url|URI|URI per l'applicazione.|  
|Versione|string|Informazioni sulla versione per l'applicazione.|  
|Requisiti|string|Descrizione dei requisiti dell'applicazione.|  
|Stato|number|Stato attuale dell'applicazione.<br /><br /> - 0 - Registrato<br /><br /> - 1 - Inviato<br /><br /> - 2 - Pubblicato<br /><br /> - 3 - Rifiutato<br /><br /> - 4 - Non pubblicato|  
|RegistrationDate|DateTime|Data e ora della registrazione dell'applicazione.|  
|CategoryId|number|Categoria dell'applicazione (finanza, intrattenimento e così via).|  
|DeveloperId|string|Identificatore univoco dello sviluppatore che ha inviato l'applicazione.|  
|Attachments|Raccolta di entità [allegato](#Attachment).|Tutti gli allegati per l'applicazione, ad esempio schermate o icone.|  
|Icona|[Allegato](#Attachment)|Icona per l'applicazione.|  
  
##  <a name="Attachment"></a>Allegato  
 L'entità `attachment` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|UniqueId|string|Identificatore univoco per l'allegato.|  
|Url|string|URL della risorsa.|  
|Tipo|string|Tipo di allegato.|  
|ContentType|string|Tipo di supporti dell'allegato.|  
  
##  <a name="Sample"></a>Codice di esempio  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|title|string|Nome dell'operazione.|  
|snippet|string|Questa proprietà è deprecata e non deve essere usata.|  
|brush|string|Modello di colorazione della sintassi del codice da usare quando si visualizza il codice di esempio. I valori consentiti sono `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby` e `csharp`.|  
|template|string|Nome del modello del codice di esempio.|  
|body|string|Segnaposto per la porzione del codice di esempio del frammento di codice.|  
|statico|string|Metodo HTTP dell'operazione.|  
|scheme|string|Protocollo da usare per la richiesta dell'operazione.|  
|path|string|Percorso dell'operazione.|  
|query|string|Esempio di stringa di query con parametri definiti.|  
|host|string|URL del gateway del servizio Gestione API per l'API che contiene questa operazione.|  
|Headers|Raccolta di entità [intestazione](#Header).|Intestazioni per l'operazione.|  
|Parametri|Raccolta di entità[parametro](#Parameter).|Parametri definiti per l'operazione.|  
  
##  <a name="Comment"></a>Commento  
 L'entità `API` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|ID|number|ID del commento.|  
|CommentText|string|Corpo del commento. Può includere HTML.|  
|DeveloperCompany|string|Nome della società dello sviluppatore.|  
|PostedOn|DateTime|Data e ora di pubblicazione del commento.|  
  
##  <a name="Issue"></a>Problema  
 L'entità `issue` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|ID|string|Identificatore univoco per il problema.|  
|ApiID|string|ID per l'API per cui è stato segnalato il problema.|  
|Titolo|string|Titolo del problema.|  
|Descrizione|string|Descrizione del problema.|  
|SubscriptionDeveloperName|string|Nome dello sviluppatore che ha segnalato il problema.|  
|IssueState|string|Stato attuale del problema. I valori possibili sono Proposto, Aperto e Chiuso.|  
|ReportedOn|DateTime|Data e ora della segnalazione del problema.|  
|Commenti|Raccolta di entità [commento](#Comment).|Commenti sul problema.|  
|Attachments|Raccolta di entità [allegato](api-management-template-data-model-reference.md#Attachment).|Qualsiasi allegato al problema.|  
|Services|Raccolta di entità [API](#API).|Le API sottoscritte dall'utente che ha registrato il problema.|  
  
##  <a name="Filtering"></a>Filtri  
 L'entità `filtering` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|Modello|string|Termine di ricerca corrente; o `null` se non è presente alcun termine di ricerca.|  
|Placeholder|string|Il testo da visualizzare nella casella di ricerca quando non è presente alcun termine di ricerca specificato.|  
  
##  <a name="Header"></a>Intestazione  
 Questa sezione descrive la rappresentazione di `parameter`.  
  
|Proprietà|Descrizione|Tipo|  
|--------------|-----------------|----------|  
|name|string|Nome del parametro.|  
|description|string|Descrizione del parametro.|  
|value|string|Valore dell'intestazione.|  
|typeName|string|Tipo di dati del valore dell'intestazione.|  
|options|string|Opzioni.|  
|Obbligatoria|boolean|Se l'intestazione è obbligatoria.|  
|readOnly|boolean|Se l'intestazione è di sola lettura.|  
  
##  <a name="HTTPRequest"></a>Richiesta HTTP  
 Questa sezione descrive la rappresentazione di `request`.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|description|string|Descrizione della richiesta dell'operazione.|  
|Headers|matrice di entità [Intestazione](#Header).|Intestazioni della richiesta.|  
|Parametri|matrice di valori [Parametro](#Parameter)|Raccolta di parametri della richiesta dell'operazione.|  
|representations|matrice di valori [Rappresentazione](#Representation)|Raccolta di rappresentazioni della richiesta dell'operazione.|  
  
##  <a name="HTTPResponse"></a>Risposta HTTP  
 Questa sezione descrive la rappresentazione di `response`.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|statusCode|intero positivo|Codice di stato della risposta dell'operazione.|  
|description|string|Descrizione della risposta dell'operazione.|  
|representations|matrice di valori [Rappresentazione](#Representation)|Raccolta di rappresentazioni della risposta dell'operazione.|  
  
##  <a name="Operation"></a>Operazione  
 L'entità `operation` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|id|string|Identificatore di risorsa. Identifica in modo univoco l'operazione all'interno dell'istanza del servizio Gestione API corrente. Il valore è un URL relativo valido nel formato `apis/{aid}/operations/{id}` dove `{aid}` è un identificatore API e `{id}` è un identificatore di operazione. Questa proprietà è di sola lettura.|  
|name|string|Nome dell'operazione. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|description|string|Descrizione dell'operazione. Non deve essere vuota. Può includere tag di formattazione HTML. La lunghezza massima consentita è di 1000 caratteri.|  
|scheme|string|Descrive su quali protocolli è possibile richiamare le operazioni in questa API. I valori consentiti sono `http`, `https` o sia `http` sia `https`.|  
|uriTemplate|string|Modello di URL relativo che identifica la risorsa di destinazione per questa operazione. Può includere parametri. Esempio: `customers/{cid}/orders/{oid}/?date={date}`|  
|host|string|URL del gateway di Gestione API che ospita l'API.|  
|httpMethod|string|Metodo HTTP dell'operazione.|  
|richiesta|[Richiesta HTTP](#HTTPRequest)|Entità contenente i dettagli della richiesta.|  
|responses|matrice di valori [Risposta HTTP](#HTTPResponse)|Matrice di entità [Risposta HTTP](#HTTPResponse) dell'operazione.|  
  
##  <a name="Menu"></a>Menu operazione  
 L'entità `operation menu` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|ApiID|string|ID dell'API corrente.|  
|CurrentOperationId|string|ID dell'operazione corrente.|  
|Azione|string|Tipo di menu.|  
|MenuItems|Raccolta di entità [Voce di menu operazione](#MenuItem).|Operazioni per l'API corrente.|  
  
##  <a name="MenuItem"></a>Voce di menu operazione  
 L'entità `operation menu item` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|ID|string|ID dell'operazione.|  
|Titolo|string|Descrizione dell'operazione.|  
|HttpMethod|string|Metodo HTTP dell'operazione.|  
  
##  <a name="Paging"></a>Paging  
 L'entità `paging` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|Page|number|Numero di pagina corrente.|  
|PageSize|number|Numero massimo di risultati da visualizzare in una singola pagina.|  
|TotalItemCount|number|Numero di elementi per la visualizzazione.|  
|ShowAll|boolean|Se si desidera mostrare tutti risultati in una singola pagina.|  
|PageCount|number|Numero di pagine dei risultati.|  
  
##  <a name="Parameter"></a>Parametro  
 Questa sezione descrive la rappresentazione di `parameter`.  
  
|Proprietà|Descrizione|Tipo|  
|--------------|-----------------|----------|  
|name|string|Nome del parametro.|  
|description|string|Descrizione del parametro.|  
|value|string|Valore del parametro.|  
|options|matrice di valori string|Valori definiti per i valori del parametro di query.|  
|Obbligatoria|boolean|Indica se il parametro è obbligatorio o no.|  
|kind|number|Se questo parametro è un parametro di percorso (1) o un parametro di stringa di query (2).|  
|typeName|string|Tipo di parametro.|  
  
##  <a name="Product"></a>Prodotto  
 L'entità `product` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|ID|string|Identificatore di risorsa. Identifica in modo univoco il prodotto all'interno dell'istanza del servizio Gestione API corrente. Il valore è un URL relativo valido nel formato `products/{pid}` dove `{pid}` è un identificatore di prodotto. Questa proprietà è di sola lettura.|  
|Titolo|string|Nome del prodotto. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|Descrizione|string|Descrizione del prodotto. Non deve essere vuoto. Può includere tag di formattazione HTML. La lunghezza massima consentita è di 1000 caratteri.|  
|Termini|string|Condizioni per l'utilizzo del prodotto. Gli sviluppatori che tentano di sottoscrivere il prodotto vengono presentati e devono accettare queste condizioni prima di poter completare il processo di sottoscrizione.|  
|ProductState|number|Specifica se il prodotto è pubblicato o no. I prodotti pubblicati possono essere individuati dagli sviluppatori nel portale per sviluppatori. I prodotti non pubblicati sono visibili solo agli amministratori.<br /><br /> I valori consentiti per lo stato del prodotto sono:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|AllowMultipleSubscriptions|boolean|Specifica se un utente può avere più sottoscrizioni al prodotto nello stesso momento.|  
|MultipleSubscriptionsCount|number|Numero di sottoscrizioni a questo prodotto da parte dell'utente corrente.|  
  
##  <a name="Provider"></a>Provider  
 L'entità `provider` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|Proprietà|dizionario di stringhe|Proprietà per questo provider di autenticazione.|  
|AuthenticationType|string|Tipo di provider. (Azure Active Directory, account di accesso di Facebook, account Google, account Microsoft, Twitter).|  
|Sottotitolo|string|Nome visualizzato del provider.|  
  
##  <a name="Representation"></a>Rappresentazione  
 Questa sezione descrive una `representation`.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|contentType|string|Specifica un tipo di contenuto registrato o personalizzato per questa rappresentazione, ad esempio `application/xml`.|  
|sample|string|Un esempio della rappresentazione.|  
  
##  <a name="Subscription"></a>Sottoscrizione  
 L'entità `subscription` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|ID|string|Identificatore di risorsa. Identifica in modo univoco la sottoscrizione all'interno dell'istanza del servizio Gestione API corrente. Il valore è un URL relativo valido nel formato `subscriptions/{sid}` dove `{sid}` è un identificatore di sottoscrizione. Questa proprietà è di sola lettura.|  
|ProductId|string|Identificatore di risorsa per il prodotto relativo al prodotto sottoscritto. Il valore è un URL relativo valido nel formato `products/{pid}` dove `{pid}` è un identificatore di prodotto.|  
|ProductTitle|string|Nome del prodotto. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|ProductDescription|string|Descrizione del prodotto. Non deve essere vuoto. Può includere tag di formattazione HTML. La lunghezza massima consentita è di 1000 caratteri.|  
|ProductDetailsUrl|string|URL relativo per i dettagli del prodotto.|  
|state|string|Stato della sottoscrizione. Gli stati possibili sono elencati di seguito:<br /><br /> - `0 - suspended`: la sottoscrizione è bloccata e il sottoscrittore non può chiamare le API del prodotto.<br /><br /> - `1 - active`: la sottoscrizione è attiva.<br /><br /> - `2 - expired`: la sottoscrizione ha raggiunto la data di scadenza ed è stata disattivata.<br /><br /> - `3 - submitted`: la richiesta di sottoscrizione è stata eseguita dallo sviluppatore, ma non è ancora stata approvata o rifiutata.<br /><br /> - `4 - rejected`: la richiesta di sottoscrizione è stata rifiutata da un amministratore.<br /><br /> - `5 - cancelled`: la sottoscrizione è stata annullata dallo sviluppatore o dall'amministratore.|  
|DisplayName|string|Nome visualizzato della sottoscrizione.|  
|CreatedDate|dateTime|Data di creazione della sottoscrizione, in formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|CanBeCancelled|boolean|Se la sottoscrizione può essere annullata dall'utente corrente.|  
|IsAwaitingApproval|boolean|Se la sottoscrizione è in attesa di approvazione.|  
|StartDate|dateTime|Data di inizio per la sottoscrizione, in formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|ExpirationDate|dateTime|Data di scadenza per la sottoscrizione, in formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|NotificationDate|dateTime|Data di notifica per la sottoscrizione, in formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|primaryKey|string|Chiave di sottoscrizione primaria. La lunghezza massima consentita è di 256 caratteri.|  
|secondaryKey|string|Chiave di sottoscrizione secondaria. La lunghezza massima consentita è di 256 caratteri.|  
|CanBeRenewed|boolean|Se la sottoscrizione può essere rinnovata dall'utente corrente.|  
|HasExpired|boolean|Se la sottoscrizione è scaduta.|  
|IsRejected|boolean|Se la richiesta di sottoscrizione è stata negata.|  
|CancelUrl|string|URL relativo per annullare la sottoscrizione.|  
|RenewUrl|string|URL relativo per rinnovare la sottoscrizione.|  
  
##  <a name="SubscriptionSummary"></a>Riepilogo delle sottoscrizioni  
 L'entità `subscription summary` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|ID|string|Identificatore di risorsa. Identifica in modo univoco la sottoscrizione all'interno dell'istanza del servizio Gestione API corrente. Il valore è un URL relativo valido nel formato `subscriptions/{sid}` dove `{sid}` è un identificatore di sottoscrizione. Questa proprietà è di sola lettura.|  
|DisplayName|string|Nome visualizzato della sottoscrizione|  
  
##  <a name="UserAccountInfo"></a>Informazioni sull'account utente  
 L'entità `user account info` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|FirstName|string|Nome. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|LastName|string|Cognome. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|Email|string|Indirizzo di posta elettronica. Non deve essere vuoto e deve essere univoco all'interno dell'istanza del servizio. La lunghezza massima consentita è di 254 caratteri.|  
|Password|string|Password dell'account utente.|  
|NameIdentifier|string|Identificatore di account, lo stesso usato per la posta elettronica dell'utente.|  
|ProviderName|string|Nome del provider di autenticazione.|  
|IsBasicAccount|boolean|True se l'account è stato registrato usando posta elettronica e password; false se l'account è stata registrato usando un provider.|  
  
##  <a name="UseSignIn"></a>Accesso utente  
 L'entità `user sign in` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|Email|string|Indirizzo di posta elettronica. Non deve essere vuoto e deve essere univoco all'interno dell'istanza del servizio. La lunghezza massima consentita è di 254 caratteri.|  
|Password|string|Password dell'account utente.|  
|ReturnUrl|string|URL della pagina in cui l'utente ha fatto clic sull'accesso.|  
|RememberMe|boolean|Se si desidera salvare le informazioni dell'utente corrente.|  
|RegistrationEnabled|boolean|Se la registrazione è abilitata.|  
|DelegationEnabled|boolean|Se l'accesso delegato è abilitato.|  
|DelegationUrl|string|URL di accesso delegato, se abilitato.|  
|SsoSignUpUrl|string|URL di accesso Single Sign-On per l'utente, se presente.|  
|AuxServiceUrl|string|Se l'utente corrente è un amministratore, questo è un collegamento all'istanza del servizio nel portale di Azure classico.|  
|Providers|Raccolta di entità [Provider](#Provider).|Provider di autenticazione per l'utente.|  
|UserRegistrationTerms|string|Condizioni che l'utente deve accettare prima dell'accesso.|  
|UserRegistrationTermsEnabled|boolean|Se le condizioni sono accettate.|  
  
##  <a name="UserSignUp"></a>Iscrizione utente  
 L'entità `user sign up` ha le proprietà seguenti.  
  
|Proprietà|Tipo|Descrizione|  
|--------------|----------|-----------------|  
|PasswordConfirm|boolean|Valore usato per il controllo dell'iscrizione [sign-up](api-management-page-controls.md#sign-up).|  
|Password|string|Password dell'account utente.|  
|PasswordVerdictLevel|number|Valore usato per il controllo dell'iscrizione [sign-up](api-management-page-controls.md#sign-up).|  
|UserRegistrationTerms|string|Condizioni che l'utente deve accettare prima dell'accesso.|  
|UserRegistrationTermsOptions|number|Valore usato per il controllo dell'iscrizione [sign-up](api-management-page-controls.md#sign-up).|  
|ConsentAccepted|boolean|Valore usato per il controllo dell'iscrizione [sign-up](api-management-page-controls.md#sign-up).|  
|Email|string|Indirizzo di posta elettronica. Non deve essere vuoto e deve essere univoco all'interno dell'istanza del servizio. La lunghezza massima consentita è di 254 caratteri.|  
|FirstName|string|Nome. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|LastName|string|Cognome. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|UserData|string|Valore usato per il controllo dell'iscrizione [sign-up](api-management-page-controls.md#sign-up).|  
|NameIdentifier|string|Valore usato per il controllo dell'iscrizione [sign-up](api-management-page-controls.md#sign-up).|  
|ProviderName|string|Nome del provider di autenticazione.|

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sull'uso dei modelli, vedere [Come personalizzare il portale per sviluppatori di Gestione API usando i modelli](api-management-developer-portal-templates.md).
