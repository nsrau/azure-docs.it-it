<properties 
	pageTitle="Creare un pacchetto dell&#39;app per le API" 
	description="Informazioni su come creare un pacchetto dell&#39;app per le API." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="guayan"/>

# Creare un pacchetto dell'app per le API

## Panoramica

Questo articolo descrive come creare un pacchetto dell'app per le API da pubblicare in [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/).

- Per informazioni su come creare un'app per le API, vedere [Creare un'app per le API con Visual Studio](app-service-dotnet-create-api-app.md).
- Per informazioni su come pubblicare un pacchetto dell'app per le API in Azure Marketplace, vedere [Pubblicare un pacchetto dell'app per le API in Azure Marketplace](app-service-api-publish-package).

## Struttura di cartelle

Un pacchetto NuGet \( file con estensione nupkg\) per un'app per le API include i file e le cartelle seguenti nella cartella *Content*:

    apiapp.json
    Metadata
        apiDefinition.swagger.json
        icons
            <icon files>
        screenshots
            <screenshot files>
        deploymentTemplates
            <template files>
        UIDefinition.json
    <other artifacts like source code, binary, etc.>

Quando si crea un pacchetto per un progetto di Visual Studio che contiene *apiapp.json* e *Metadata* nella cartella di progetto, viene creato un file con estensione *nupkg*, come mostrato nella figura seguente:

![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-create-package/metadatainse.png)

Le sezioni seguenti descrivono i file e le cartelle nella struttura di cartelle dell'app per le API, per poi illustrare come creare un pacchetto del progetto e distribuirlo.

## apiapp.json

File manifesto per l'app per le API.

|Nome \(grassetto=obbligatorio\)|Tipo|Formato|Descrizione|
|:---------------------|:-----|:-------|:------------|
|**id**|string|[a-zA-Z0-9\_.]|ID di questo pacchetto. Deve essere univoco all'interno di uno spazio dei nomi e può contenere solo caratteri alfanumerici, "\_" e ".". Deve iniziare con un carattere alfanumerico.|
|**namespace**|string|nome di dominio|Spazio dei nomi che, con la proprietà **id**, identifica in modo univoco l'app per le API. Deve essere uno dei nomi di dominio del tenant AAD dell'editore.|
|**version**|string|[semver](http://docs.nuget.org/Create/Versioning)|Versione del pacchetto. Quando gli utenti abilitano l'aggiornamento automatico per il pacchetto, questo verrà applicato solo alle nuove versioni all'interno della stessa versione principale.|
|**gateway**|string|2015-01-14|Versione API del gateway usata dal pacchetto. Un gateway è una speciale app Web attraverso la quale vengono instradate tutte le richieste alle app per le API in un gruppo di risorse. Una delle sue funzioni principali è gestire l'autenticazione. Attualmente l'unica versione del gateway è 2015-01-14. In futuro, quando verranno rilasciate nuove versioni del gateway, questa proprietà consentirà di evitare modifiche a posteriori e di continuare a usare l'API del gateway precedente.| 
|**title**|string||Nome visualizzato dell'app per le API.|
|**summary**|string|max. 100 caratteri|Breve riepilogo dell'app per le API.
|description|string|max. 1500 caratteri|Descrizione completa dell'app per le API. Può contenere codice HTML. Gli elementi e gli attributi consentiti sono "h1", "h2", "h3", "h4", "h5", "p", "ol", "ul", "li", "a[target\|href]", "br", "strong", "em", "b", "i".|
|**author**|string|max. 256 caratteri|Autore/i dell'app per le API.|
|homepage|string|URL|Home page dell'app per le API.|
|endpoints|oggetto||Elenco di endpoint su cui la piattaforma di app per le API può eseguire query per ottenere informazioni sui metodi e sullo stato dell'app per le API.|
|endpoints.apiDefinition|string|Percorso URL|URL relativo di un'API esposto dall'app per le API che restituisce una definizione API Swagger 2.0 su una richiesta GET, ad esempio "/swagger/docs/v1". Se è impostato, verrà usato al posto del file apiDefinition.swagger.json statico nel pacchetto, se presente.|
|endpoints.status|string|Percorso URL|URL relativo di un'API esposto dall'app per le API che restituisce informazioni sullo stato di runtime relative all'app per le API su una richiesta GET.|
|categories|string[]|community, social network, enterprise, integrazione, protocollo, servizi dati/app, altro|Categoria di Azure Marketplace in cui verrà visualizzato il pacchetto dell'app per le API. Per impostazione predefinita, l'app per le API verrà sempre visualizzata nella categoria Community. Dopo l'approvazione, l'app per le API verrà visualizzata nella categoria specificata.|
|license|object||Licenza dell'app per le API.|
|**license.type**|string||Identificatore licenza SPDX, ad esempio MIT.|
|license.url|string|URL|URL assoluto che punta al testo completo della licenza.|
|license.requireAcceptance|bool|true, false|Indica se una licenza deve essere accettata prima dell'installazione. Valore predefinito: false.|
|links|object[]||Matrice di collegamenti da aggiungere alla pagina del Marketplace.|
|**links.text**|string||Testo del collegamento.|
|**links.url**|string|URL|URL assoluto del collegamento.|
|authentication|object[]||Matrice indicante il tipo di autenticazione necessaria all'app per le API per eseguire chiamate API in uscita. Attualmente, per il pacchetto dell'app per le API è supportata una sola autenticazione.|
|**authentication.type**|string|Box, DropBox, Facebook, Google, Office365, OneDrive, Quickbooks, Salesforce, SharePointOnline, Twitter, Yammer|Autenticazione richiesta dall'app per le API. Attualmente sono supportati 11 tipi di autenticazione e ne verranno aggiunti altri a breve.| 
|authentication.scopes|string[]||Matrice di ambiti specifici del tipo di autenticazione.|
|copyright|string|Informazioni sul copyright dell'app per le API.
|brandColor|string|Qualsiasi formato compatibile con CSS|Colore personalizzato facoltativo per l'interfaccia utente. Ad esempio, la finestra di progettazione di app per la logica usa questa proprietà per impostare il colore di sfondo dell'intestazione della scheda.|

## metadata/apiDefinition.swagger.json

Qui è possibile specificare un file JSON Swagger 2.0 statico per esporre la definizione dell'API dell'app per le API. La piattaforma verificherà innanzitutto se la proprietà **endpoints.apiDefinition** è configurata in **apiapp.json**. In caso affermativo, otterrà la definizione dell'API dall'URL specificato nella proprietà. In caso contrario, tenterà di trovare il file.

- Per informazioni sullo standard Swagger 2.0, vedere [http://swagger.io/](http://swagger.io/). \<!--todo specificare gli URL
- Per informazioni su come personalizzare la definizione dell'API e ottimizzarla per le app per la logica, vedere [titolo del documento]().
- Per informazioni su come esporre una definizione dell'API dinamica, vedere [titolo del documento](). --\>

## metadata/icons

È possibile specificare un set di icone personalizzate per il pacchetto dell'app per le API. Se i file icona necessari non vengono forniti, verrà usata un'icona predefinita, come illustrato di seguito.

![Icona grande predefinita dell'app per le API](./media/app-service-api-create-package/api-app-default-large-icon.png)

|File |Larghezza|Altezza|Descrizione|
|:--------------------|:----|:-----|:----------|
|metadata/icons/small.png|40px|40px|Obbligatorio se si preferisce usare icone personalizzate.|
|metadata/icons/medium.png|90px|90px|Obbligatorio se si preferisce usare icone personalizzate.|
|metadata/icons/large.png|115px|115px|Obbligatorio se si preferisce usare icone personalizzate.|
|metadata/icons/wide.png|255px|115px|Obbligatorio se si preferisce usare icone personalizzate.|
|metadata/icons/hero.png|815px|290px|Facoltativo se si preferisce usare icone personalizzate.|

## metadata/screenshots

È possibile specificare un massimo di 5 catture di schermata per il pacchetto dell'app per le API.

|File|Larghezza|Altezza|Descrizione|
|:--------------------|:----|:-----|:----------|
|metadata/screenshots/\*.png|533px|324px|Catture di schermata del pacchetto dell'app per le API.|

## metadata/deploymentTemplates

Talvolta, un pacchetto dell'app per le API richiede alcune configurazioni personalizzate durante la distribuzione. Ad esempio, il [connettore BLOB di Archiviazione di Azure](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/) richiede l'URI del contenitore BLOB di archiviazione di Azure. Facoltativamente, è possibile configurare una chiave di accesso.

Per supportare questo scenario, è possibile aggiungere un elenco di file JSON di modello di Gestione risorse di Azure in questa cartella per personalizzare la distribuzione di app per le API. La piattaforma App per le API consente di unire i modelli di Gestione risorse di Azure personalizzati al modello di sistema per generare un modello finale per la distribuzione. Tutti i parametri definiti nei modelli di Gestione risorse di Azure \(ad eccezione di **$system**\) verranno anche richiesti automaticamente nel pannello **crea** del portale di anteprima di Azure, in modo che gli utenti dell'app per le API possano immettere i valori.

Di seguito è riportato un modello di Gestione risorse di Azure che mostra come richiedere l'URI del contenitore BLOB e la chiave di accesso durante la distribuzione di app per le API.

    {
      "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "$system": {
          "type": "object"
        },
        "BlobConnector_ContainerUrl": {
          "type": "string"
        },
        "BlobConnector_AccessKey": {
          "type": "securestring"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-04-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('$system').siteName]",
          "location": "[parameters('$system').location]",
          "properties": {
            "siteConfig": {
              "appSettings": [
                {
                  "name": "BlobConnector_ContainerUrl",
                  "value": "[parameters('BlobConnector_ContainerUrl')]"
                },
                {
                  "name": "BlobConnector_AccessKey",
                  "value": "[parameters('BlobConnector_AccessKey')]"
                }
              ]
            }
          }
        }
      ]
    }

Il pannello di creazione del portale di anteprima di Azure corrispondente è illustrato nella schermata seguente. La schermata mostra un pacchetto dell'app per le API che usa il file UIDefinition.json per migliorare il pannello di creazione. Per informazioni dettagliate, vedere [metadata/UIDefinition.json](#metadata-uidefinition-json).

![Esempio di modello di Gestione risorse di Azure per il pannello Crea](./media/app-service-api-create-package/custom-arm-template-create-blade-example.png)

Per altre informazioni, vedere il [linguaggio del modello di Gestione risorse di Azure](https://msdn.microsoft.com/library/dn835138).

## metadata/UIDefinition.json

Quando è necessario usare modelli di Gestione risorse di Azure personalizzati per specificare le configurazioni personalizzate per la distribuzione di un'app per le API, il pannello **crea** del portale di anteprima di Azure richiede automaticamente i parametri del modello di Gestione risorse di Azure, in modo che gli utenti dell'app per le API possano immettere i valori. È possibile usare il file *UIDefinition.json* per migliorare l'interfaccia utente del pannello **crea** specificando valori predefiniti, descrizioni comandi, convalide e così via.

Per specificare un *file UIDefinition.json*, iniziare dalla struttura seguente, quindi seguire la tabella per configurare ogni parametro.


    {
      "parameters": {
        "<your API app package id>": {
            "<name of the ARM template parameter>": { },
            "<name of the ARM template parameter>": { }
        }
      }
    }

|Nome |Tipo|Descrizione|
|:---------------------|:-------|:------------|
|defaultValue|string|Valore predefinito del controllo di input del pannello di creazione nel portale di anteprima di Azure.|
|displayName|string|Etichetta del controllo di input del pannello di creazione nel portale di anteprima di Azure. Deve essere breve.|
|description|string|Descrizione del controllo di input.|
|tooltip|string|Descrizione comandi del controllo di input del pannello di creazione nel portale di anteprima di Azure. Verrà visualizzata quando gli utenti fanno clic sulla finestra delle informazioni a destra dell'etichetta. Può essere lunga e completa.|
|constraints|Object|Vincoli da verificare in base al parametro.|
|constraints.required|bool|Indica se il parametro è obbligatorio o meno. Il valore predefinito è false.|

Ad esempio, ecco il file *UIDefinition.json* per il [connettore BLOB di Archiviazione di Azure](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/):

    {
      "parameters": {
        "AzureStorageBlobConnector": {
          "BlobConnector_ContainerUrl": {
            "defaultValue": "",
            "displayName": "Container/SAS URI",
            "description": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "tooltip": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "constraints": {
              "required": "true"
            }
          },
          "BlobConnector_AccessKey": {
            "defaultValue": "",
            "displayName": "Access Key",
            "description": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "tooltip": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "constraints": {
              "required": "false"
            }
          }
        }
      }
    }

Questo file consente di configurare il pannello **crea** del portale di anteprima di Azure in modo che visualizzi il pannello **Impostazioni pacchetto** seguente.

![Esempio di file UIDefinition per il pannello Crea](./media/app-service-api-create-package/uidefinition-create-blade-example.png)

<!--todo add when ready to document status URI
A URI to a web service Get method that returns a value that indicates the API app's current status. If you provide this URI, the portal will show the API app's current operational status along with other information about the API app, for example:  running, nearing quota, SSL certificate expiring, etc. The format of the JSON the portal expects to receive is shown below, following the end of this table. If you don't provide an endpoints.status URI, the portal shows the Azure platform status as the API app's status.
Here is an example that shows the expected format of the JSON response from the Get method that `endpoints.status` points to:
		{
		  "status":[{ 
		    "name":"Nearing Quota", 
		    "message":"One or more quotas is nearing their limit", 
		    "level":"Warning" 
		  }, { 
		    "name":"Expiring Certificate", 
		    "message":"The SSL Certificate associated with this website is about to expire", 
		    "level":"Error"
		  }, {
		    "name":"Running", 
		    "message":"The website is handling traffic", 
		    "level":"Info"
		  }]
		}
The `name` property is a short description of the status, `message` is a longer description, and `level` can be "Error", "Warning", or "Info" for normal status.
-->

## Creare un pacchetto dell'app per le API

Per creare un pacchetto dell'app per le API

1. [Scaricare e installare l'interfaccia della riga di comando multipiattaforma di Azure](xplat-cli).
2. Passare alla modalità Gestione risorse di Azure eseguendo il comando seguente

        azure config mode arm

3. Creare un pacchetto NuGet per l'app per le API eseguendo il comando seguente

        azure apiapp package create -p <package folder> -o <destination folder>

    ad esempio:

        azure apiapp package create -p c:\ContactList\ContactList -o c:\ContactListPackage

In questo modo sarà possibile convalidare il contenuto del pacchetto per verificare che

- Segua il formato indicato sopra
- Il file Metadata\\apiDefinition.swagger.json, se specificato, contenga una definizione dell'API Swagger 2.0 valida

Se vengono rilevati problemi, verranno visualizzate informazioni dettagliate per poter risolvere il problema e creare un pacchetto dell'app per le API valido.

## Passaggi successivi

Il pacchetto dell'app per le API è pronto per essere pubblicato in Azure Marketplace. Per informazioni su come fare, seguire i passaggi indicati nell'esercitazione [Pubblicare un pacchetto dell'app per le API](app-service-api-publish-package).

<!--HONumber=52-->
