<properties
	pageTitle="API REST di gestione di Ricerca di Azure versione 28/02/2015 | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="API REST di gestione di Ricerca di Azure versione 2015-02-28"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/04/2016"
	ms.author="heidist" />

# API di gestione: versione 2015-02-28

Ricerca di Azure è un servizio di ricerca ospitato sul cloud in Microsoft Azure. In questo documento viene descritta la versione **2015-02-28** dell’API REST di gestione di Ricerca di Azure. E’ stata quindi sostituita dalle versioni più recenti. Per la versione più recente, vedere [API REST di gestione di ricerca di Azure 2015-08-19](https://msdn.microsoft.com/library/dn832684.aspx) su MSDN. \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

##Operazioni di gestione del servizio

L'API REST di gestione del servizio Ricerca di Azure fornisce l'accesso a livello di codice a gran parte delle funzionalità disponibili tramite il portale, consentendo agli amministratori di automatizzare le operazioni seguenti:

- Creazione o eliminazione di un servizio Ricerca di Azure.
- Creare, rigenerare o recuperare `api-keys` per automatizzare le normali modifiche alle chiavi di amministrazione utilizzate per l'autenticazione delle operazioni di ricerca dei dati. 
- Regolare la scalabilità di un servizio di Ricerca di Azure in risposta alle modifiche nei requisiti di volume di query o archiviazione.

Per amministrare completamente il servizio a livello di codice, saranno necessarie due API: l'API REST di gestione di Ricerca di Azure e l’[API REST di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) comune. L'API di Gestione risorse viene utilizzata per operazioni generali non specifiche del servizio, ad esempio l’esecuzione di query dei dati di sottoscrizione, l’elenco delle georilevazioni e così via. Per creare e gestire i servizi di Ricerca di Azure nella sottoscrizione, assicurarsi che la richiesta HTTP includa l'endpoint di Gestione risorse, l’ID sottoscrizione, il provider (in questo caso, Ricerca di Azure) e l'operazione di ricerca specifica del servizio.

[Introduzione all'API REST di gestione di Ricerca di Azure](http://go.microsoft.com/fwlink/p/?linkID=516968) è una procedura dettagliata del codice di esempio nella quale vengono illustrate le operazioni di configurazione delle applicazioni e di gestione dei servizi. L’applicazione di esempio invia richieste all’API di Gestione risorse di Azure e all’API di gestione dei servizi per Ricerca di Azure, illustrando, in tal modo, come mettere insieme un’applicazione logica che utilizzi entrambe le API.

### Endpoint ###

L'endpoint per le operazioni di amministrazione dei servizi è l'URL di Gestione risorse di Azure, `https://management.azure.com`.

Si noti che tutte le chiamate API di gestione devono includere l'ID sottoscrizione e una versione dell'API.

### Versioni ###

La versione corrente dell’API REST di gestione di Ricerca di Azure è `api-version=2015-02-28`. La versione precedente, `api-version=2014-07-31-Preview` è deprecata. Sebbene continuerà a funzionare per molti dei prossimi mesi, si consiglia di passare alla nuova versione appena possibile.

### Autenticazione e controllo di accesso###

L'API REST gestione di Ricerca di Azure è un'estensione di Gestione risorse di Azure e ne condivide le dipendenze. Di conseguenza, Active Directory è un prerequisito per l'amministrazione dei servizi di Ricerca di Azure. Tutte le richieste amministrative dal codice client devono essere autenticate tramite Azure Active Directory prima che la richiesta raggiunga la gestione risorse.

Si noti che se il codice dell'applicazione gestisce le *operazioni di gestione dei servizi* e anche le *operazioni dati* sugli indici o sui documenti, si utilizzeranno due metodi di autenticazione per ognuna delle API di Ricerca di Azure:

- L'amministrazione dei servizi e delle chiavi, a causa della dipendenza da Gestione risorse, si basa su Active Directory per l'autenticazione.
- Le richieste di dati all'endpoint del servizio Ricerca di Azure, ad esempio, Create Index o Search Documents, utilizzano una `api-key` nell'intestazione della richiesta. Per informazioni sull'autenticazione di una richiesta di dati, vedere [API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Nell'applicazione di esempio documentata in [Introduzione all'API REST di gestione di Ricerca di Azure](http://go.microsoft.com/fwlink/p/?linkID=516968) vengono illustrate le tecniche di autenticazione per ogni tipo di operazione. Nella Guida introduttiva sono incluse le istruzioni per configurare un'applicazione client all’uso di Active Directory.

Controllo di accesso per Gestione risorse di Azure utilizza i ruoli predefiniti Proprietario, Collaboratore e Lettore. Per impostazione predefinita, tutti gli amministratori dei servizi sono membri del ruolo Proprietario. Per informazioni dettagliate, vedere [Controllo di accesso basato sui ruoli nel portale di Azure classico](../active-directory/role-based-access-control-configure.md).


### Riepilogo delle API ##

Le operazioni includono le API indicate di seguito.

- <a name="CreateService">Create Search Service</a>

    `PUT	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="GetService">Get Search Service</a>

    `GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="ListService">List Search Services</a>

    `GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2015-02-28`

- <a name="DeleteService">Delete Search Service</a>

    `DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="UpdateService">Update Search Service</a>

    `PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="ListAdminKey">List Admin Keys</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2015-02-28`

- <a name="RegenAdminKey">Regenerate Admin Key</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2015-02-28`

- <a name="CreateQueryKey">Create Query Key</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2015-02-28`

- <a name="ListQueryKey">List Query Keys</a>

    `GET	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2015-02-28`

- <a name="DeleteQueryKey">Delete Query Keys</a>

    `DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2015-02-28`

<a name="ServiceOps"></a>
##Operazioni di servizio

È possibile effettuare il provisioning o il deprovisioning dei servizi di Ricerca di Azure inviando richieste HTTP per la propria sottoscrizione Azure. Gli scenari abilitati tramite queste operazioni includono la creazione di strumenti di amministrazione personalizzati o di un ambiente di produzione o di sviluppo end-to-end (dalla creazione del servizio, fino al popolamento di un indice). Analogamente, i fornitori di soluzioni che progettano e vendono soluzioni cloud potrebbero desiderare un approccio automatizzato e ripetibile per il provisioning di servizi per ogni nuovo cliente.

**Operazioni in un servizio**

Le opzioni correlate ai servizi includono le API indicate di seguito:

- <a name="CreateService">Create Search Service</a>
- <a name="GetService">Get Search Service</a>
- <a name="ListService">List Search Services</a>
- <a name="DeleteService">Delete Search Service</a>
- <a name="UpdateService">Update Search Service</a>


<a name="CreateService"></a>
###Create Search Service

Le operazioni **Create Search Service** effettuano il provisioning di un nuovo servizio di ricerca con i parametri specificati. Questa API può essere utilizzata anche per aggiornare una definizione di servizio esistente.

    PUT	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

#### Richiedere parametri URI

`subscriptionId`: richiesto. L’`subscriptionID` per l'utente di Azure. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`resourceGroupName`: richiesto. Il nome del gruppo di risorse all'interno della sottoscrizione dell'utente. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`serviceName`: richiesto. Nome del servizio di ricerca all’interno del gruppo di risorse specificato. I nomi di servizio devono contenere solo lettere minuscole, cifre o trattini, non possono utilizzare il trattino come primi due caratteri o come ultimo carattere, non possono contenere trattini consecutivi e devono essere di lunghezza compresa tra 2 e 15 caratteri. Poiché tutti i nomi sono nomi di servizio <name> devono essere univoci a livello globale. Due servizi all'interno della stessa sottoscrizione o dello stesso gruppo di risorse o tra le sottoscrizioni e i gruppi di risorse non possono avere lo stesso nome. Non è possibile modificare il nome del servizio una volta creato.

`api-version`: richiesto. Specifica la versione del protocollo utilizzato per la richiesta. La versione corrente è `2015-02-28`.


#### Intestazioni richiesta

`Content-Type`: richiesto. Impostare questa intestazione su application/json.

`x-ms-client-request-id`: facoltativo. Valore GUID generato dal client che identifica la richiesta. Se specificato, questo valore viene incluso nelle informazioni di risposta per consentire il mapping della richiesta.


#### Request Body

{ "location": "location of search service", "tags": { "key": "value", ... }, "properties": { "sku": { "name": "free | standard | standard2" }, "replicaCount": 1 | 2 | 3 | 4 | 5 | 6, "partitionCount": 1 | 2 | 3 | 4 | 6 | 12 } }

#### Parametri del corpo della richiesta#

`location`: richiesto. Una delle aree geografiche di Azure supportate e registrate (ad esempio, Stati Uniti Occidentali, Stati Uniti Orientali, Sud-est asiatico e così via). Si noti che una volta creato, il percorso di una risorsa non può essere modificato.

`tags`: facoltativo. Elenco di coppie chiave-valore che descrivono la risorsa. Questi tag possono essere utilizzati nella visualizzazione e nel raggruppamento di una risorsa tra gruppi di risorse. È possibile fornire un massimo di 10 tag per una risorsa. Ogni tag deve avere una chiave non superiore a 128 caratteri con un valore che non superi i 256 caratteri.

`sku`: richiesto. I valori validi sono `free` e `standard`. Sebbene anche `standard2` sia valido, può essere utilizzato solo quando è abilitato per la sottoscrizione di Azure dal supporto tecnico Microsoft. `free` esegue il provisioning del servizio in cluster condivisi. `standard` esegue il provisioning del servizio in cluster dedicati. È possibile creare un solo servizio di ricerca a livello di prezzo gratuito. Gli altri servizi di ricerca devono essere creati a livello di prezzo standard. Per impostazione predefinita, un servizio viene creato con una partizione e una replica. Il prezzo delle repliche e delle partizioni aggiuntive viene definito in termini di unità di ricerca. Per dettagli, vedere [Limitazioni e vincoli](search-limits-quotas-capacity.md). Una volta creato il servizio, non è possibile modificare l’`sku`.

`replicaCount`: facoltativo. Il valore predefinito è 1. I valori validi sono da 1 a 6. Valido solo quando `sku` è `standard`.

`partitionCount`: facoltativo. Il valore predefinito è 1. I valori validi includono 1, 2, 3, 4, 6 o 12. Valido solo quando `sku` è `standard`.


### Response

Viene restituito HTTP 200 (OK) quando viene aggiornata una definizione del servizio. Viene restituito HTTP 201 (Created) quando viene creato un nuovo servizio.


#### Intestazioni della risposta

`Content-Type`: questa intestazione è sempre impostata su application/json.

`x-ms-request-id`: identificatore univoco per l'operazione corrente generato dal servizio.


#### Contenuto risposta 

Per HTTP 200 e 201, il corpo della risposta contiene la definizione del servizio.
    
    { 
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]", 
    
      "name": "service name", 
      "location": "location of search service", 
    "type": " Microsoft.Search/searchServices", 
      "tags": {
        "key": "value",
        ...
      },  
    "properties": { 
    	"sku": { 
          "name": "free | standard | standard2" 
       		 }, 
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6, 
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12, 
        "status": "running | provisioning | deleting | degraded | disabled | error", 
        "statusDetails": "details of the status", 
        "provisioningState": "succeeded | provisioning | failed" 
      } 
    } 


#### Elementi del corpo della risposta

`id`: l'ID è l'URL (escluso nome host/schema) per questo servizio di ricerca.

`name`: nome del servizio di ricerca.

`location`: una delle aree geografiche di Azure supportate e registrate (ad esempio, Stati Uniti Occidentali, Stati Uniti Orientali, Sud-est asiatico e così via).

`tags`: elenco di coppie chiave-valore che descrivono la risorsa, utilizzate nella visualizzazione e raggruppamento delle risorse tra gruppi di risorse.

`sku`: indica il livello di prezzo. I valori validi includono:

- `free`: cluster condiviso
- `standard`: cluster dedicato
- `standard2`: utilizzare solo sotto la supervisione del supporto tecnico Microsoft. 

`replicaCount`: indica il numero di repliche presenti nel servizio. I valori validi sono da 1 a 6.

`partitionCount`: indica il numero di partizioni presenti nel servizio. I valori validi includono 1, 2, 3, 4, 6 o 12.

`status`: stato del servizio di ricerca al momento della chiamata dell'operazione. I valori validi includono:

- `running`: viene creato il servizio di ricerca.
- `provisioning`: viene effettuato il provisioning del servizio di ricerca.
- `deleting`: il servizio di ricerca viene eliminato.
- `degraded`: le prestazioni del servizio di ricerca vengono diminuite. Ciò può verificarsi quando il cluster ha rilevato un errore che può impedire o meno che il servizio funzioni correttamente.
- `disabled`: la ricerca è disabilitata. In questo stato, il servizio rifiuterà tutte le richieste API.
- `error`: il servizio di ricerca è in stato di errore. 

**Nota**: se il servizio è nello stato `degraded`, `disabled` o `error`, il team di Ricerca di Azure sta investigando attivamente il problema sottostante. I servizi dedicati in questi stati sono ancora addebitabili in base al numero di unità di ricerca di cui è stato effettuato il provisioning.

`statusDetails`: i dettagli dello stato.

`provisioningState`: indica lo stato corrente del provisioning dei servizi. I valori validi includono:

- `succeeded`: il provisioning viene completato correttamente.
- `provisioning`: viene effettuato il provisioning del servizio.
- `failed`: non è stato possibile effettuare il provisioning del servizio. 

Il provisioning è uno stato intermedio che si verifica quando viene stabilita la capacità del servizio. Una volta configurata la capacità, `provisioningState` passa a "riuscito" o "non riuscito". Le applicazioni client possono eseguire il polling dello stato del provisioning (la scelta consigliata per l’intervallo di polling è da 30 secondi a un minuto) utilizzando l’operazione **Get Search Service** per controllare quando viene completata un'operazione. Se si utilizza il servizio gratuito, questo valore tende a tornare come "riuscito", direttamente nella chiamata di creazione del servizio. Ciò avviene perché il servizio gratuito utilizza una capacità che è già configurata.

<a name="GetService"></a>
### Get Search Service

L’operazione **Get Search Service** restituisce le proprietà per il servizio di ricerca specificato. Si noti che le chiavi di amministrazione non vengono restituite. Utilizzare l’operazione **Get Admin Keys** per recuperare le chiavi di amministrazione.

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

#### URI della richiesta

`subscriptionId`: richiesto. L'ID sottoscrizione per l'utente di Azure. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`resourceGroupName`: richiesto. Il nome del gruppo di risorse all'interno della sottoscrizione dell'utente. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`serviceName`: richiesto. Nome del servizio di ricerca all’interno del gruppo di risorse specificato. Se non si conosce il nome del servizio, è possibile ottenere un elenco utilizzando List Search Services (API di Ricerca di Azure).

`api-version`: richiesto. Specifica la versione del protocollo utilizzato per la richiesta. La versione corrente è `2015-02-28`.

#### Intestazioni richiesta

`x-ms-client-request-id`: facoltativo. Valore GUID generato dal client che identifica la richiesta. Se specificato, questo valore viene incluso nelle informazioni di risposta per consentire il mapping della richiesta.


#### Request Body

Nessuno.


#### Codice di stato della risposta

HTTP 200 (OK) in caso di esito positivo.


#### Intestazioni della risposta

`Content-Type`: questa intestazione è sempre impostata su application/json.

`x-ms-request-id`: identificatore univoco per l'operazione corrente generato dal servizio.

#### Contenuto risposta

    {
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]",
    
      "name": "service name",
      "location": "location of search service",
    "type": " Microsoft.Search/searchServices",
      "tags": {
        "key": "value",
        ... 
      }, 
    "properties": {
    	"sku": {
          "name": "free | standard | standard2"
    	},
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
        "status": "running | provisioning | deleting | degraded | disabled | error",
        "statusDetails": "details of the status",
        "provisioningState": "succeeded | provisioning | failed"
      }
    }

#### Elementi del corpo della risposta

`id`: l'ID è l'URL (escluso nome host/schema) per questo servizio di ricerca.

`name`: nome del servizio di ricerca.

`location`: percorso della risorsa. Sarà una delle aree geografiche di Azure supportate e registrate (ad esempio, Stati Uniti Occidentali, Stati Uniti Orientali, Sud-est asiatico e così via).

`tags`: i tag sono un elenco di coppie chiave-valore che descrivono la risorsa. Questi tag possono essere utilizzati nella visualizzazione e nel raggruppamento di una risorsa tra gruppi di risorse.

`sku`: indica il livello di prezzo. I valori validi includono:

- `free`: cluster condiviso
- `standard`: cluster dedicato
- `standard2`: utilizzare solo sotto la supervisione del supporto tecnico Microsoft.

`replicaCount`: indica il numero di repliche presenti nel servizio. I valori validi sono da 1 a 6.

`partitionCount`: indica il numero di partizioni presenti nel servizio. I valori validi includono 1, 2, 3, 4, 6 o 12.

`status`: stato del servizio di ricerca al momento della chiamata dell'operazione. I valori validi includono:

- `running`: viene creato il servizio di ricerca.
- `provisioning`: viene effettuato il provisioning del servizio di ricerca.
- `deleting`: il servizio di ricerca viene eliminato.
- `degraded`: le prestazioni del servizio di ricerca vengono diminuite. Ciò può verificarsi quando il cluster ha rilevato un errore che può impedire o meno che il servizio funzioni correttamente.
- `disabled`: la ricerca è disabilitata. In questo stato, il servizio rifiuterà tutte le richieste API.
- `error`: il servizio di ricerca è in stato di errore. 
 
**Nota**: se il servizio è nello stato `degraded`, `disabled` o `error`, il team di Ricerca di Azure sta investigando attivamente il problema sottostante. I servizi dedicati in questi stati sono ancora addebitabili in base al numero di unità di ricerca di cui è stato effettuato il provisioning.
 
`statusDetails`: i dettagli dello stato.

`provisioningState`: i valori validi includono.

- `succeeded`: il provisioning ha avuto esito positivo.
- `provisioning`: viene effettuato il provisioning del servizio.
- `failed`: il provisioning ha avuto esito negativo.


<a name="ListService"></a>
### List Search Services

L’operazione **List Services** restituisce un elenco di tutti i servizi di ricerca nella sottoscrizione di un gruppo di risorse specifico. Questa operazione restituisce le definizioni dei servizi, meno le chiavi api di amministrazione. Utilizzare l’operazione **Get Admin Keys** per recuperare le chiavi di amministrazione.

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2015-02-28
    
#### Richiedere parametri URI

`subscriptionId`: richiesto. L’`subscriptionID` per l'utente di Azure. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`resourceGroupName`: richiesto. Il nome del gruppo di risorse all'interno della sottoscrizione dell'utente. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`api-version`: richiesto. Specifica la versione del protocollo utilizzato per la richiesta. La versione corrente è `2015-02-28`.

#### Intestazioni richiesta

`x-ms-client-request-id`: facoltativo. Valore GUID generato dal client che identifica la richiesta. Se specificato, questo valore viene incluso nelle informazioni di risposta per consentire il mapping della richiesta.

####Request Body

Nessuno.

####Response

Il codice di stato è HTTP 200 (OK) in caso di esito positivo.

### Intestazioni della risposta ###

`Content-Type`: questa intestazione è sempre impostata su application/json.

`x-ms-request-id`: identificatore univoco per l'operazione corrente generato dal servizio.


####Contenuto risposta 

Il corpo della risposta è un elenco di servizi, restituiti come matrice JSON, nel quale ogni servizio segue il formato nell’operazione **Get Search Service**.

Si noti che il campo `nextLink` è sempre null poiché la versione corrente non supporta il paging. La restituzione con un valore vuoto viene eseguita per mantenere la compatibilità futura.

    {
      "value": [
     	{
          "id": "id of service 1",
          "name": "service name",
          "location": "location of search service 1",
    	"type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          }, 
    	"properties": {
            "sku": {
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
    	}
      },   
      {
          "id": "id of service 2",
          "name": "service name 2",
          "location": "location of search service 2",
   	 	"type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          },
    	"properties": {
            "sku": { 
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
    	}
      }
    ],
    "nextLink": null
    }


<a name="DeleteService"></a>
### Delete service

L’operazione **Delete Service** consente di eliminare il servizio di ricerca e i dati della ricerca, inclusi tutti gli indici e i documenti.
    
    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

**Nota:** amministratori e sviluppatori sono abituati a eseguire il backup dei dati dell'applicazione prima di eliminarla da un server di produzione. In Ricerca di Azure, non esiste alcuna operazione di backup. Se si utilizza l'indice come archiviazione primaria per l'applicazione, sarà necessario utilizzare un'operazione di ricerca per restituire tutti i dati dell'indice, che è possibile archiviare esternamente.

###Richiedere parametri URI###

`subscriptionId`: richiesto. L'ID sottoscrizione per l'utente di Azure. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`resourceGroupName`: richiesto. Il nome del gruppo di risorse all'interno della sottoscrizione dell'utente. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`serviceName`: richiesto. Nome del servizio di ricerca all’interno del gruppo di risorse specificato. Se non si conosce il nome del servizio, è possibile ottenere un elenco utilizzando List Search Services (API di Ricerca di Azure).

`api-version`: richiesto. Specifica la versione del protocollo utilizzato per la richiesta. La versione corrente è `2015-02-28`.

###Intestazioni richiesta###

`x-ms-client-request-id`: facoltativo. Valore GUID generato dal client che identifica la richiesta. Se specificato, questo valore viene incluso nelle informazioni di risposta per consentire il mapping della richiesta.

###Request Body###

Nessuno.

###Response###

Per HTTP 200, il corpo della risposta sarà vuoto. HTTP 200 (OK) è la risposta corretta se la risorsa non esiste.

È possibile utilizzare **Get Search Service API** per eseguire il polling dello stato del servizio di eliminazione. È consigliabile utilizzare intervalli di polling da 30 secondi a un minuto.

###Intestazioni della risposta###

`x-ms-request-id`: identificatore univoco per l'operazione corrente generato dal servizio.

###Contenuto risposta###

Nessuno.

<a name="UpdateService"></a>
### Update Service ##

L’operazione **Update Service** modifica la configurazione del servizio di ricerca. Le modifiche valide includono la modifica dei tag, della partizione o del conteggio di replica che aggiunge o rimuove le unità di ricerca per il servizio come evento fatturabile. Se si tenta di ridurre le partizioni al di sotto della quantità necessaria per archiviare il corpo di ricerche esistente, si verificherà un errore e il blocco dell’operazione. Le modifiche alla topologia del servizio possono richiedere alcuni minuti. Lo spostamento dei dati e la configurazione o la rimozione cluster nel centro dati richiedono tempo.

Si noti che non è possibile modificare il nome, il percorso e l’sku. La modifica di una di queste proprietà richiede la creazione di un nuovo servizio.

    PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

In alternativa, è possibile utilizzare PUT.

    PUT https://management.azure.com/subscriptions/[subscriptionId]/resourcegroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

###Richiedere parametri URI###

`subscriptionId`: richiesto. L’`subscriptionID` per l'utente di Azure. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`resourceGroupName`: richiesto. Il nome del gruppo di risorse all'interno della sottoscrizione dell'utente. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`serviceName`: richiesto. Nome del servizio di ricerca all’interno del gruppo di risorse specificato. Se non si conosce il nome del servizio, è possibile ottenere un elenco utilizzando List Search Services (API di Ricerca di Azure).

`api-version`: richiesto. Specifica la versione del protocollo utilizzato per la richiesta. La versione corrente è `2015-02-28`.

###Intestazioni richiesta###

`Content-Type`: richiesto. Impostare questa intestazione su application/json.

`x-ms-client-request-id`: facoltativo. Valore GUID generato dal client che identifica la richiesta. Se specificato, questo valore viene incluso nelle informazioni di risposta per consentire il mapping della richiesta.

###Request Body###

    {
      "tags": {
        "key": "value",
        ...
      }, 
    	"properties": {
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12
     	}
    }

###Parametri del corpo della richiesta###

`tags`: facoltativo. Elenco di coppie chiave-valore che descrivono la risorsa. Questi tag possono essere utilizzati nella visualizzazione e nel raggruppamento di questa risorsa (tra gruppi di risorse). È possibile fornire un massimo di 10 tag per una risorsa. Ogni tag deve avere una chiave non superiore a 128 caratteri con un valore che non superi i 256 caratteri.

`replicaCount`: facoltativo. Il valore predefinito è 1. I valori validi sono da 1 a 6. Valido solo quando `sku` è `standard`.

`partitionCount`: facoltativo. Il valore predefinito è 1. I valori validi includono 1, 2, 3, 4, 6 o 12. Valido solo quando `sku` è `standard`.

###Response###

Se l'operazione ha esito positivo, viene restituito HTTP 200 (OK). È possibile utilizzare **Get Search Service API** per eseguire il polling dello stato del servizio di aggiornamento. È consigliabile utilizzare intervalli di polling da 30 secondi a un minuto.


### Intestazioni della risposta ###

`Content-Type`: questa intestazione è sempre impostata su application/json.

`x-ms-request-id`: identificatore univoco per l'operazione corrente generato dal servizio.

### Contenuto risposta ###

Il corpo della risposta contiene la definizione del servizio aggiornata. Per un esempio, vedere **Get Search Service API**.


<a name="KeyOps"></a>
## Operazioni chiave

L'autenticazione a un servizio di Ricerca di Azure richiede due informazioni: un URL del servizio di ricerca e una chiave API. Le chiavi API vengono generate al momento della creazione del servizio e possono essere rigenerate su richiesta dopo il provisioning del servizio. Sono disponibili due tipi di chiave API:

- chiave di amministrazione: concede l'accesso a tutte le operazioni (massimo 2 per ogni servizio)
- chiave di query: autentica soltanto le richieste di query (massimo di 50 per ogni servizio)

La possibilità di gestire a livello di programmazione le chiavi di amministrazione e di query del servizio di Ricerca di Azure fornisce i mezzi per la creazione di strumenti personalizzati, il rollover delle chiavi periodicamente come best practice di sicurezza di routine, il rollover delle chiavi quando un dipendente lascia l'azienda o la generazione e l'acquisizione di chiavi durante il provisioning del servizio, quando viene utilizzato un approccio a livello di codice o tramite script per distribuire la soluzione.

Le chiavi di query possono essere acquisite, create ed eliminate. Le operazioni relative alle chiavi di amministrazione sono limitate all’acquisizione e alla rigenerazione dei valori di chiave esistenti. L’eliminazione di una chiave di amministrazione potrebbe bloccare il servizio in modo permanente, pertanto l'operazione non è disponibile.

Le chiavi sono stringhe composte da una combinazione casuale di numeri e lettere maiuscole. Una chiave API è utilizzabile solo con il servizio per cui è stata creata e può cambiare a intervalli regolari (se si adotta una strategia di rollover della chiave come protezione ottimale).

È opportuno accertarsi di trattare le chiavi API, specialmente le chiavi di amministrazione, come dati sensibili. Tutti gli utenti che acquisiscono le chiavi di amministrazione hanno la capacità di eliminare o leggere i dati dagli indici.

**Operations sulle chiavi**

Le operazioni relative alle chiavi includono le API indicate di seguito:

- <a name="ListAdminKey">List Admin Keys</a>
- <a name="RegenAdminKey">Regenerate Admin Key</a>
- <a name="CreateQueryKey">Create Query Key</a>
- <a name="ListQueryKey">List Query Keys</a>
- <a name="DeleteQueryKey">Delete Query Keys</a>


<a name="ListAdminKey"></a>
## List Admin Keys ##

L’operazione **List Admin Keys** restituisce le chiavi di amministrazione primaria e secondaria per il servizio di ricerca specificato. Viene utilizzato il metodo POST in quanto questa azione restituisce chiavi di lettura-scrittura.

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2015-02-28

Le chiavi di amministrazione vengono create con il servizio. Si tratta sempre di due chiavi, primaria e secondaria. È possibile rigenerare le chiavi, ma non eliminarle.

###Richiedere parametri URI###

`subscriptionId`: richiesto. L'ID sottoscrizione per l'utente di Azure. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`resourceGroupName`: richiesto. Il nome del gruppo di risorse all'interno della sottoscrizione dell'utente. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`serviceName`: richiesto. Nome del servizio di ricerca all’interno del gruppo di risorse specificato. Se non si conosce il nome del servizio, è possibile ottenere un elenco utilizzando List Search Services (API di Ricerca di Azure).

`api-version`: richiesto. Specifica la versione del protocollo utilizzato per la richiesta. La versione corrente è `2015-02-28`.

`listAdminKeys`: elemento obbligatorio. Questa azione consente di recuperare le di amministrazione primarie e secondarie per il servizio di ricerca.

###Request Headers###

`x-ms-client-request-id`: facoltativo. Valore GUID generato dal client che identifica la richiesta. Se specificato, questo valore viene incluso nelle informazioni di risposta per consentire il mapping della richiesta.

###Request Body###

Nessuno.

###Response###

Se l'operazione ha esito positivo, viene restituito HTTP 200 (OK).

### Intestazioni della risposta ###

`Content-Type`: questa intestazione è sempre impostata su application/json.

`x-ms-request-id`: identificatore univoco per l'operazione corrente generato dal servizio.

###Contenuto risposta###

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }
    

<a name="RegenAdminKey"></a>
## Regenerate Admin Keys ##

L’operazione **Regenerate Admin Keys** elimina e rigenera la chiave primaria o quella secondaria. È possibile rigenerare solo una chiave per volta. Quando si rigenerano le chiavi, considerare le modalità di gestione dell'accesso al servizio. Esiste una chiave secondaria in modo che sia disponibile una chiave quando si esegue il rollover della chiave primaria. Ogni servizio dispone sempre di entrambe le chiavi. È possibile rigenerare le chiavi, ma non è possibile eliminarle o eseguire un servizio senza di esse.
 
    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2015-02-28

###Richiedere parametri URI###

`subscriptionId`: richiesto. L'ID sottoscrizione per l'utente di Azure. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`resourceGroupName`: richiesto. Il nome del gruppo di risorse all'interno della sottoscrizione dell'utente. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`serviceName`: richiesto. Nome del servizio di ricerca all’interno del gruppo di risorse specificato. Se non si conosce il nome del servizio, è possibile ottenere un elenco utilizzando List Search Services (API di Ricerca di Azure).

`api-version`: richiesto. Specifica la versione del protocollo utilizzato per la richiesta. La versione corrente è `2015-02-28`.
	
`regenerateKey`: elemento obbligatorio. Questa azione consente di specificare che verrà rigenerata la chiave di amministrazione primaria o secondaria.

`keyKind`: richiesto. Specifica la chiave da rigenerare. I valori validi includono:

- `primary`
- `secondary`

###Request Headers###

`Content-Type`: richiesto. Impostare questa intestazione su application/json.

`x-ms-client-request-id`: facoltativo. Valore GUID generato dal client che identifica la richiesta. Se specificato, questo valore viene incluso nelle informazioni di risposta per consentire il mapping della richiesta.

###Request Body###

Nessuno.

###Response###

Se l'operazione ha esito positivo, viene restituito HTTP 200 (OK).

### Intestazioni della risposta ###

`Content-Type`: questa intestazione è sempre impostata su application/json.

`x-ms-request-id`: identificatore univoco per l'operazione corrente generato dal servizio.

###Contenuto risposta###

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }
    
###Elementi del corpo della risposta###

`primaryKey`: la chiave di amministrazione primaria, se è stata rigenerata.

`secondaryKey`: la chiave di amministrazione secondaria, se è stata rigenerata.



<a name="CreateQueryKey"></a>
## Create Query Key ##

L’operazione **Create Query Key** genera una nuova chiave di query per il servizio di ricerca. È possibile creare fino a 50 chiavi di query per servizio.

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2015-02-28

###Richiedere parametri URI###

`subscriptionId`: richiesto. L'ID sottoscrizione per l'utente di Azure. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`resourceGroupName`: richiesto. Il nome del gruppo di risorse all'interno della sottoscrizione dell'utente. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`serviceName`: richiesto. Nome del servizio di ricerca all’interno del gruppo di risorse specificato. Se non si conosce il nome del servizio, è possibile ottenere un elenco utilizzando List Search Services (API di Ricerca di Azure).

`api-version`: richiesto. Specifica la versione del protocollo utilizzato per la richiesta. La versione corrente è `2015-02-28`.

`createQueryKey`: elemento obbligatorio. Questa azione crea una chiave di query per il servizio di ricerca.

`name`: richiesto. Il nome della nuova chiave.

###Request Headers###

`x-ms-client-request-id`: facoltativo. Valore GUID generato dal client che identifica la richiesta. Se specificato, questo valore viene incluso nelle informazioni di risposta per consentire il mapping della richiesta.

###Request Body###

Nessuno.

###Response###

Se l’operazione ha esito positivo, il codice di stato della risposta è HTTP 200 (OK).

### Intestazioni della risposta ###

`Content-Type`: questa intestazione è sempre impostata su application/json.

`x-ms-request-id`: identificatore univoco per l'operazione corrente generato dal servizio.

###Contenuto risposta###

    {
      "name": "name of key",
      "key": "api key"
    }


###Elementi del corpo della risposta###

`name`: nome della chiave di query.

`key`: valore della chiave di query.

<a name="ListQueryKey"></a>
## List Query Keys ##


L’operazione **List Query Keys** restituisce le chiavi di query per il servizio di ricerca specificato. Le chiavi di query vengono utilizzate per inviare chiamate delle API di query (in sola lettura) a un servizio di ricerca. Possono essere presenti fino a 50 chiavi di query per servizio.

    GET	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2015-02-28

###Richiedere parametri URI###

`subscriptionId`: richiesto. L'ID sottoscrizione per l'utente di Azure. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`resourceGroupName`: richiesto. Il nome del gruppo di risorse all'interno della sottoscrizione dell'utente. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`serviceName`: richiesto. Nome del servizio di ricerca all’interno del gruppo di risorse specificato. Se non si conosce il nome del servizio, è possibile ottenere un elenco utilizzando List Search Services (API di Ricerca di Azure).

`api-version`: richiesto. Specifica la versione del protocollo utilizzato per la richiesta. La versione corrente è `2015-02-28`.
	
`listQueryKeys`: elemento obbligatorio. Questa azione consente di recuperare le chiavi di query per il servizio di ricerca.

###Request Headers###

`x-ms-client-request-id`: facoltativo. Valore GUID generato dal client che identifica la richiesta. Se specificato, questo valore viene incluso nelle informazioni di risposta per consentire il mapping della richiesta.

###Request Body###

Nessuno.

###Response###

Se l’operazione ha esito positivo, il codice di stato della risposta è HTTP 200 (OK).

### Intestazioni della risposta ###

`Content-Type`: questa intestazione è sempre impostata su application/json.

`x-ms-request-id`: identificatore univoco per l'operazione corrente generato dal servizio.

###Contenuto risposta###

    {
      "value": [
    	{
          "name": "name of key 1",
          "key": "key 1"
      	},   
      	{   
          "name": "name of key 2",
          "key": "key 2"
      	}
      ],
    "nextLink": null
    }

###Elementi del corpo della risposta###

`name`: nome della chiave di query.

`key`: valore della chiave di query.


<a name="DeleteQueryKey"></a>
## Delete Query Keys ##

L’operazione **Delete Query Key** elimina la chiave di query specificata. Le chiavi di query sono facoltative e vengono utilizzate per le query di sola lettura.

    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2015-02-28

A differenza delle chiavi di amministrazione, le chiavi di query non vengono rigenerate. Il processo di rigenerazione di una chiave di query consiste nell'eliminarla e quindi nel ricrearla.

###Richiedere parametri URI###

`subscriptionId`: richiesto. L'ID sottoscrizione per l'utente di Azure. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`resourceGroupName`: richiesto. Il nome del gruppo di risorse all'interno della sottoscrizione dell'utente. È possibile ottenere questo valore dall'API di Gestione risorse di Azure o dal portale.

`serviceName`: richiesto. Nome del servizio di ricerca all’interno del gruppo di risorse specificato. Se non si conosce il nome del servizio, è possibile ottenere un elenco utilizzando List Search Services (API di Ricerca di Azure).

`api-version`: richiesto. Specifica la versione del protocollo utilizzato per la richiesta. La versione corrente è `2015-02-28`.

`deleteQueryKey`: elemento obbligatorio. Questa azione elimina una chiave di query esistente per il servizio di ricerca.

`key`: richiesto. La chiave da eliminare.

###Request Headers###

`x-ms-client-request-id`: facoltativo. Valore GUID generato dal client che identifica la richiesta. Se specificato, questo valore viene incluso nelle informazioni di risposta per consentire il mapping della richiesta.

###Request Body###

Nessuno.

###Response###

Se l’operazione ha esito positivo, il codice di stato della risposta è HTTP 200 (OK).

### Intestazioni della risposta ###

`Content-Type`: questa intestazione è sempre impostata su application/json.

`x-ms-request-id`: identificatore univoco per l'operazione corrente generato dal servizio.

###Contenuto risposta###

Nessuna.

<!---HONumber=AcomDC_0224_2016-->