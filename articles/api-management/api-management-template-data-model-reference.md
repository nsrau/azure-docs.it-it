---
title: Informazioni di riferimento sui modelli di dati per i modelli di Gestione API di Azure | Microsoft Docs
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
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 0f27b6b529c2591e37d48e3386190077fc8efc32
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
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
-   [Accesso utente](#UserSignUp)  
  
##  <a name="API"></a>API  
 L'entità `API` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|id|stringa|Identificatore di risorsa. Identifica in modo univoco l'API all'interno dell'istanza del servizio Gestione API corrente. Il valore è un URL relativo valido nel formato `apis/{id}` dove `{id}` è un identificatore API. Questa proprietà è di sola lettura.|  
|name|stringa|Nome dell'API. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|description|stringa|Descrizione dell'API. Non deve essere vuota. Può includere tag di formattazione HTML. La lunghezza massima consentita è di 1000 caratteri.|  
|serviceUrl|stringa|URL assoluto del servizio back-end che implementa questa API.|  
|path|stringa|URL relativo che identifica in modo univoco questa API e tutti i relativi percorsi delle risorse all'interno dell'istanza del servizio Gestione API. Viene aggiunto all'URL di base dell'endpoint API specificato durante la creazione dell'istanza del servizio in modo da formare un URL pubblico per questa API.|  
|protocols|matrice di valori numero|Descrive su quali protocolli è possibile richiamare le operazioni in questa API. I valori consentiti sono `1 - http` e `2 - https` o entrambi.|  
|authenticationSettings|[Impostazioni di autenticazione del server di autorizzazione](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Raccolta delle impostazioni di autenticazione incluse in questa API.|  
|subscriptionKeyParameterNames|object|Proprietà facoltativa che può essere usata per specificare nomi personalizzati per i parametri di query e/o intestazione contenenti la chiave di sottoscrizione. Quando è presente, questa proprietà deve contenere almeno una delle due proprietà seguenti.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a>Riepilogo delle API  
 L'entità `API summary` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|id|stringa|Identificatore di risorsa. Identifica in modo univoco l'API all'interno dell'istanza del servizio Gestione API corrente. Il valore è un URL relativo valido nel formato `apis/{id}` dove `{id}` è un identificatore API. Questa proprietà è di sola lettura.|  
|name|stringa|Nome dell'API. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|description|stringa|Descrizione dell'API. Non deve essere vuota. Può includere tag di formattazione HTML. La lunghezza massima consentita è di 1000 caratteri.|  
  
##  <a name="Application"></a> Applicazione  
 L'entità `application` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|ID|stringa|Identificatore univoco dell'applicazione.|  
|Titolo|stringa|Titolo dell'applicazione.|  
|DESCRIZIONE|stringa|Descrizione dell'applicazione.|  
|Url|URI|URI per l'applicazione.|  
|Version|stringa|Informazioni sulla versione per l'applicazione.|  
|Requisiti|stringa|Descrizione dei requisiti dell'applicazione.|  
|Stato|number|Stato attuale dell'applicazione.<br /><br /> - 0 - Registrato<br /><br /> - 1 - Inviato<br /><br /> - 2 - Pubblicato<br /><br /> - 3 - Rifiutato<br /><br /> - 4 - Non pubblicato|  
|RegistrationDate|Datetime|Data e ora della registrazione dell'applicazione.|  
|CategoryId|number|Categoria dell'applicazione (finanza, intrattenimento e così via).|  
|DeveloperId|stringa|Identificatore univoco dello sviluppatore che ha inviato l'applicazione.|  
|Attachments|Raccolta di entità [allegato](#Attachment).|Tutti gli allegati per l'applicazione, ad esempio schermate o icone.|  
|Icona|[Allegato](#Attachment)|Icona per l'applicazione.|  
  
##  <a name="Attachment"></a>Allegato  
 L'entità `attachment` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|UniqueId|stringa|Identificatore univoco per l'allegato.|  
|Url|stringa|URL della risorsa.|  
|type|stringa|Tipo di allegato.|  
|ContentType|stringa|Tipo di supporti dell'allegato.|  
  
##  <a name="Sample"></a>Codice di esempio  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|title|stringa|Nome dell'operazione.|  
|snippet|stringa|Questa proprietà è deprecata e non deve essere usata.|  
|brush|stringa|Modello di colorazione della sintassi del codice da usare quando si visualizza il codice di esempio. I valori consentiti sono `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby` e `csharp`.|  
|template|stringa|Nome del modello del codice di esempio.|  
|Corpo|stringa|Segnaposto per la porzione del codice di esempio del frammento di codice.|  
|statico|stringa|Metodo HTTP dell'operazione.|  
|scheme|stringa|Protocollo da usare per la richiesta dell'operazione.|  
|path|stringa|Percorso dell'operazione.|  
|query|stringa|Esempio di stringa di query con parametri definiti.|  
|host|stringa|URL del gateway del servizio Gestione API per l'API che contiene questa operazione.|  
|headers|Raccolta di entità [intestazione](#Header).|Intestazioni per l'operazione.|  
|Parametri|Raccolta di entità[parametro](#Parameter).|Parametri definiti per l'operazione.|  
  
##  <a name="Comment"></a>Commento  
 L'entità `API` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|ID|number|ID del commento.|  
|CommentText|stringa|Corpo del commento. Può includere HTML.|  
|DeveloperCompany|stringa|Nome della società dello sviluppatore.|  
|PostedOn|Datetime|Data e ora di pubblicazione del commento.|  
  
##  <a name="Issue"></a>Problema  
 L'entità `issue` ha le proprietà seguenti.  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|ID|stringa|Identificatore univoco per il problema.|  
|ApiID|stringa|ID per l'API per cui è stato segnalato il problema.|  
|Title|stringa|Titolo del problema.|  
|DESCRIZIONE|stringa|Descrizione del problema.|  
|SubscriptionDeveloperName|stringa|Nome dello sviluppatore che ha segnalato il problema.|  
|IssueState|stringa|Stato attuale del problema. I valori possibili sono Proposto, Aperto e Chiuso.|  
|ReportedOn|Datetime|Data e ora della segnalazione del problema.|  
|Commenti|Raccolta di entità [commento](#Comment).|Commenti sul problema.|  
|Attachments|Raccolta di entità [allegato](api-management-template-data-model-reference.md#Attachment).|Qualsiasi allegato al problema.|  
|Services|Raccolta di entità [API](#API).|Le API sottoscritte dall'utente che ha registrato il problema.|  
  
##  <a name="Filtering"></a>Filtri  
 L'entità `filtering` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|Modello|stringa|Termine di ricerca corrente; o `null` se non è presente alcun termine di ricerca.|  
|Placeholder|stringa|Il testo da visualizzare nella casella di ricerca quando non è presente alcun termine di ricerca specificato.|  
  
##  <a name="Header"></a>Intestazione  
 Questa sezione descrive la rappresentazione di `parameter`.  
  
|Proprietà|DESCRIZIONE|type|  
|--------------|-----------------|----------|  
|name|stringa|Nome del parametro.|  
|description|stringa|Descrizione del parametro.|  
|value|stringa|Valore dell'intestazione.|  
|typeName|stringa|Tipo di dati del valore dell'intestazione.|  
|options|stringa|Opzioni.|  
|Obbligatoria|boolean|Se l'intestazione è obbligatoria.|  
|readOnly|boolean|Se l'intestazione è di sola lettura.|  
  
##  <a name="HTTPRequest"></a>Richiesta HTTP  
 Questa sezione descrive la rappresentazione di `request`.  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|description|stringa|Descrizione della richiesta dell'operazione.|  
|Headers|matrice di entità [Intestazione](#Header).|Intestazioni della richiesta.|  
|Parametri|matrice di valori [Parametro](#Parameter)|Raccolta di parametri della richiesta dell'operazione.|  
|representations|matrice di valori [Rappresentazione](#Representation)|Raccolta di rappresentazioni della richiesta dell'operazione.|  
  
##  <a name="HTTPResponse"></a>Risposta HTTP  
 Questa sezione descrive la rappresentazione di `response`.  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|statusCode|intero positivo|Codice di stato della risposta dell'operazione.|  
|description|stringa|Descrizione della risposta dell'operazione.|  
|representations|matrice di valori [Rappresentazione](#Representation)|Raccolta di rappresentazioni della risposta dell'operazione.|  
  
##  <a name="Operation"></a>Operazione  
 L'entità `operation` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|id|stringa|Identificatore di risorsa. Identifica in modo univoco l'operazione all'interno dell'istanza del servizio Gestione API corrente. Il valore è un URL relativo valido nel formato `apis/{aid}/operations/{id}` dove `{aid}` è un identificatore API e `{id}` è un identificatore di operazione. Questa proprietà è di sola lettura.|  
|name|stringa|Nome dell'operazione. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|description|stringa|Descrizione dell'operazione. Non deve essere vuoto. Può includere tag di formattazione HTML. La lunghezza massima consentita è di 1000 caratteri.|  
|scheme|stringa|Descrive su quali protocolli è possibile richiamare le operazioni in questa API. I valori consentiti sono `http`, `https` o sia `http` sia `https`.|  
|uriTemplate|stringa|Modello di URL relativo che identifica la risorsa di destinazione per questa operazione. Può includere parametri. Esempio: `customers/{cid}/orders/{oid}/?date={date}`|  
|host|stringa|URL del gateway di Gestione API che ospita l'API.|  
|httpMethod|stringa|Metodo HTTP dell'operazione.|  
|richiesta|[Richiesta HTTP](#HTTPRequest)|Entità contenente i dettagli della richiesta.|  
|responses|matrice di valori [Risposta HTTP](#HTTPResponse)|Matrice di entità [Risposta HTTP](#HTTPResponse) dell'operazione.|  
  
##  <a name="Menu"></a>Menu operazione  
 L'entità `operation menu` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|ApiID|stringa|ID dell'API corrente.|  
|CurrentOperationId|stringa|ID dell'operazione corrente.|  
|Azione|stringa|Tipo di menu.|  
|MenuItems|Raccolta di entità [Voce di menu operazione](#MenuItem).|Operazioni per l'API corrente.|  
  
##  <a name="MenuItem"></a>Voce di menu operazione  
 L'entità `operation menu item` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|ID|stringa|ID dell'operazione.|  
|Title|stringa|Descrizione dell'operazione.|  
|HttpMethod|stringa|Metodo HTTP dell'operazione.|  
  
##  <a name="Paging"></a>Paging  
 L'entità `paging` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|Page|number|Numero di pagina corrente.|  
|PageSize|number|Numero massimo di risultati da visualizzare in una singola pagina.|  
|TotalItemCount|number|Numero di elementi per la visualizzazione.|  
|ShowAll|boolean|Se si desidera mostrare tutti risultati in una singola pagina.|  
|PageCount|number|Numero di pagine dei risultati.|  
  
##  <a name="Parameter"></a>Parametro  
 Questa sezione descrive la rappresentazione di `parameter`.  
  
|Proprietà|DESCRIZIONE|type|  
|--------------|-----------------|----------|  
|name|stringa|Nome del parametro.|  
|description|stringa|Descrizione del parametro.|  
|value|stringa|Valore del parametro.|  
|options|matrice di valori string|Valori definiti per i valori del parametro di query.|  
|Obbligatoria|boolean|Indica se il parametro è obbligatorio o no.|  
|kind|number|Se questo parametro è un parametro di percorso (1) o un parametro di stringa di query (2).|  
|typeName|stringa|Tipo di parametro.|  
  
##  <a name="Product"></a>Prodotto  
 L'entità `product` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|ID|stringa|Identificatore di risorsa. Identifica in modo univoco il prodotto all'interno dell'istanza del servizio Gestione API corrente. Il valore è un URL relativo valido nel formato `products/{pid}` dove `{pid}` è un identificatore di prodotto. Questa proprietà è di sola lettura.|  
|Titolo|stringa|Nome del prodotto. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|DESCRIZIONE|stringa|Descrizione del prodotto. Non deve essere vuoto. Può includere tag di formattazione HTML. La lunghezza massima consentita è di 1000 caratteri.|  
|Termini|stringa|Condizioni per l'utilizzo del prodotto. Gli sviluppatori che tentano di sottoscrivere il prodotto vengono presentati e devono accettare queste condizioni prima di poter completare il processo di sottoscrizione.|  
|ProductState|number|Specifica se il prodotto è pubblicato o no. I prodotti pubblicati possono essere individuati dagli sviluppatori nel portale per sviluppatori. I prodotti non pubblicati sono visibili solo agli amministratori.<br /><br /> I valori consentiti per lo stato del prodotto sono:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|AllowMultipleSubscriptions|boolean|Specifica se un utente può avere più sottoscrizioni al prodotto nello stesso momento.|  
|MultipleSubscriptionsCount|number|Numero massimo di sottoscrizioni per questo prodotto di cui può disporre contemporaneamente un utente.|  
  
##  <a name="Provider"></a>Provider  
 L'entità `provider` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|Properties|dizionario di stringhe|Proprietà per questo provider di autenticazione.|  
|AuthenticationType|stringa|Tipo di provider. (Azure Active Directory, account di accesso di Facebook, account Google, account Microsoft, Twitter).|  
|Sottotitolo|stringa|Nome visualizzato del provider.|  
  
##  <a name="Representation"></a>Rappresentazione  
 Questa sezione descrive una `representation`.  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|contentType|stringa|Specifica un tipo di contenuto registrato o personalizzato per questa rappresentazione, ad esempio `application/xml`.|  
|sample|stringa|Un esempio della rappresentazione.|  
  
##  <a name="Subscription"></a>Sottoscrizione  
 L'entità `subscription` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|ID|stringa|Identificatore di risorsa. Identifica in modo univoco la sottoscrizione all'interno dell'istanza del servizio Gestione API corrente. Il valore è un URL relativo valido nel formato `subscriptions/{sid}` dove `{sid}` è un identificatore di sottoscrizione. Questa proprietà è di sola lettura.|  
|ProductId|stringa|Identificatore di risorsa per il prodotto relativo al prodotto sottoscritto. Il valore è un URL relativo valido nel formato `products/{pid}` dove `{pid}` è un identificatore di prodotto.|  
|ProductTitle|stringa|Nome del prodotto. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|ProductDescription|stringa|Descrizione del prodotto. Non deve essere vuoto. Può includere tag di formattazione HTML. La lunghezza massima consentita è di 1000 caratteri.|  
|ProductDetailsUrl|stringa|URL relativo per i dettagli del prodotto.|  
|state|stringa|Stato della sottoscrizione. Gli stati possibili sono elencati di seguito:<br /><br /> - `0 - suspended`: la sottoscrizione è bloccata e il sottoscrittore non può chiamare le API del prodotto.<br /><br /> - `1 - active`: la sottoscrizione è attiva.<br /><br /> - `2 - expired`: la sottoscrizione ha raggiunto la data di scadenza ed è stata disattivata.<br /><br /> - `3 - submitted`: la richiesta di sottoscrizione è stata eseguita dallo sviluppatore, ma non è ancora stata approvata o rifiutata.<br /><br /> - `4 - rejected`: la richiesta di sottoscrizione è stata rifiutata da un amministratore.<br /><br /> - `5 - cancelled`: la sottoscrizione è stata annullata dallo sviluppatore o dall'amministratore.|  
|DisplayName|stringa|Nome visualizzato della sottoscrizione.|  
|CreatedDate|dateTime|Data di creazione della sottoscrizione, in formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|CanBeCancelled|boolean|Se la sottoscrizione può essere annullata dall'utente corrente.|  
|IsAwaitingApproval|boolean|Se la sottoscrizione è in attesa di approvazione.|  
|StartDate|dateTime|Data di inizio per la sottoscrizione, in formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|ExpirationDate|dateTime|Data di scadenza per la sottoscrizione, in formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|NotificationDate|dateTime|Data di notifica per la sottoscrizione, in formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|primaryKey|stringa|Chiave di sottoscrizione primaria. La lunghezza massima consentita è di 256 caratteri.|  
|secondaryKey|stringa|Chiave di sottoscrizione secondaria. La lunghezza massima consentita è di 256 caratteri.|  
|CanBeRenewed|boolean|Se la sottoscrizione può essere rinnovata dall'utente corrente.|  
|HasExpired|boolean|Se la sottoscrizione è scaduta.|  
|IsRejected|boolean|Se la richiesta di sottoscrizione è stata negata.|  
|CancelUrl|stringa|URL relativo per annullare la sottoscrizione.|  
|RenewUrl|stringa|URL relativo per rinnovare la sottoscrizione.|  
  
##  <a name="SubscriptionSummary"></a>Riepilogo delle sottoscrizioni  
 L'entità `subscription summary` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|ID|stringa|Identificatore di risorsa. Identifica in modo univoco la sottoscrizione all'interno dell'istanza del servizio Gestione API corrente. Il valore è un URL relativo valido nel formato `subscriptions/{sid}` dove `{sid}` è un identificatore di sottoscrizione. Questa proprietà è di sola lettura.|  
|DisplayName|stringa|Nome visualizzato della sottoscrizione|  
  
##  <a name="UserAccountInfo"></a>Informazioni sull'account utente  
 L'entità `user account info` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|FirstName|stringa|Nome. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|LastName|stringa|Cognome. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|Email|stringa|Indirizzo di posta elettronica. Non deve essere vuoto e deve essere univoco all'interno dell'istanza del servizio. La lunghezza massima consentita è di 254 caratteri.|  
|Password|stringa|Password dell'account utente.|  
|NameIdentifier|stringa|Identificatore di account, lo stesso usato per la posta elettronica dell'utente.|  
|ProviderName|stringa|Nome del provider di autenticazione.|  
|IsBasicAccount|boolean|True se l'account è stato registrato usando posta elettronica e password; false se l'account è stata registrato usando un provider.|  
  
##  <a name="UseSignIn"></a>Accesso utente  
 L'entità `user sign in` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|Email|stringa|Indirizzo di posta elettronica. Non deve essere vuoto e deve essere univoco all'interno dell'istanza del servizio. La lunghezza massima consentita è di 254 caratteri.|  
|Password|stringa|Password dell'account utente.|  
|ReturnUrl|stringa|URL della pagina in cui l'utente ha fatto clic sull'accesso.|  
|RememberMe|boolean|Se si desidera salvare le informazioni dell'utente corrente.|  
|RegistrationEnabled|boolean|Se la registrazione è abilitata.|  
|DelegationEnabled|boolean|Se l'accesso delegato è abilitato.|  
|DelegationUrl|stringa|URL di accesso delegato, se abilitato.|  
|SsoSignUpUrl|stringa|URL di accesso Single Sign-On per l'utente, se presente.|  
|AuxServiceUrl|stringa|Se l'utente corrente è un amministratore, questo è un collegamento all'istanza del servizio nel portale di Azure.|  
|Providers|Raccolta di entità [Provider](#Provider).|Provider di autenticazione per l'utente.|  
|UserRegistrationTerms|stringa|Condizioni che l'utente deve accettare prima dell'accesso.|  
|UserRegistrationTermsEnabled|boolean|Se le condizioni sono accettate.|  
  
##  <a name="UserSignUp"></a>Iscrizione utente  
 L'entità `user sign up` ha le proprietà seguenti:  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|PasswordConfirm|boolean|Valore usato per il controllo dell'iscrizione [sign-up](api-management-page-controls.md#sign-up).|  
|Password|stringa|Password dell'account utente.|  
|PasswordVerdictLevel|number|Valore usato per il controllo dell'iscrizione [sign-up](api-management-page-controls.md#sign-up).|  
|UserRegistrationTerms|stringa|Condizioni che l'utente deve accettare prima dell'accesso.|  
|UserRegistrationTermsOptions|number|Valore usato per il controllo dell'iscrizione [sign-up](api-management-page-controls.md#sign-up).|  
|ConsentAccepted|boolean|Valore usato per il controllo dell'iscrizione [sign-up](api-management-page-controls.md#sign-up).|  
|Email|stringa|Indirizzo di posta elettronica. Non deve essere vuoto e deve essere univoco all'interno dell'istanza del servizio. La lunghezza massima consentita è di 254 caratteri.|  
|FirstName|stringa|Nome. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|LastName|stringa|Cognome. Non deve essere vuoto. La lunghezza massima consentita è di 100 caratteri.|  
|UserData|stringa|Valore usato per il controllo dell'iscrizione [sign-up](api-management-page-controls.md#sign-up).|  
|NameIdentifier|stringa|Valore usato per il controllo dell'iscrizione [sign-up](api-management-page-controls.md#sign-up).|  
|ProviderName|stringa|Nome del provider di autenticazione.|

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso dei modelli, vedere [Come personalizzare il portale per sviluppatori di Gestione API di Azure con i modelli](api-management-developer-portal-templates.md).
