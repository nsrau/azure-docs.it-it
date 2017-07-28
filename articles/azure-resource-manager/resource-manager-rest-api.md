---
title: API REST di Resource Manager | Microsoft Docs
description: Panoramica dell&quot;autenticazione ed esempi di utilizzo delle API REST di Resource Manager
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: e8d7a1d2-1e82-4212-8288-8697341408c5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/13/2017
ms.author: navale;tomfitz;
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 2f7ba23775545637de865f9ef63680ae22c62164
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017


---
# <a name="resource-manager-rest-apis"></a>API REST di Resource Manager
> [!div class="op_single_selector"]
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [Interfaccia della riga di comando di Azure](xplat-cli-azure-resource-manager.md)
> * [Portale](resource-group-portal.md) 
> * [API REST](resource-manager-rest-api.md)
> 
> 

Ogni chiamata ad Azure Resource Manager, ogni modello sviluppato e ogni account di archiviazione configurato sono basati su una o più chiamate all'API REST di Azure Resource Manager. Questo argomento è dedicato a tali API e spiega come chiamarle senza usare alcun SDK. Questa opzione è molto utile se si vuole il controllo completo di tutte le richieste ad Azure oppure se l'SDK per il linguaggio preferito non è disponibile o non supporta le operazioni che si vuole eseguire.

Questo articolo non esamina ogni API esposta in Azure, ma usa alcune operazioni come esempio per illustrare come connettersi a esse. Una volta acquisite le nozioni di base, è possibile leggere [Azure Resource Manager REST API Reference](https://docs.microsoft.com/rest/api/resources/) (Informazioni di riferimento sull'API REST di Azure Resource Manager) che contiene informazioni dettagliate su come usare le altre API.

## <a name="authentication"></a>Autenticazione
L'autenticazione per Resource Manager viene gestita da Azure Active Directory (AD). Per connettersi a un'API, prima di tutto è necessario eseguire l'autenticazione con Azure AD per ricevere un token di autenticazione che è possibile passare a ogni richiesta. Poiché si descrive una chiamata reale direttamente all'API REST, si presuppone che non si desideri eseguire l'autenticazione tramite una richiesta di nome utente e password. Si suppone inoltre che l'utente non usi i meccanismi di autenticazione a due fattori. Verranno quindi create una cosiddetta applicazione Azure AD e un'entità servizio che verranno usate per l'accesso. Si ricordi tuttavia che Azure AD supporta diverse procedure di autenticazione e che tutte possono essere usate per recuperare il token di autenticazione necessario per le richieste API successive.
Per istruzioni dettagliate, vedere [Creare un'applicazione e un'entità servizio di Azure AD](resource-group-create-service-principal-portal.md).

### <a name="generating-an-access-token"></a>Generazione di un token di accesso
L'autenticazione in Azure AD viene eseguita chiamando Azure AD, all'indirizzo login.microsoftonline.com. Per eseguire l'autenticazione, sono necessarie le informazioni seguenti:

* ID tenant di Azure AD (nome della directory di Azure AD usata per l'accesso, spesso, ma non necessariamente, lo stesso della società)
* ID applicazione (ottenuto durante il passaggio di creazione dell'applicazione Azure AD)
* Password (selezionata durante la creazione dell'applicazione Azure AD)

Nella richiesta HTTP seguente verificare che "Azure AD Tenant ID", "Application ID" e "Password" vengano sostituiti con i valori corretti.

**Richiesta HTTP generica:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

se l'autenticazione riesce, verrà restituita una risposta simile alla seguente:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(access_token nella risposta precedente è stato abbreviato per renderlo più leggibile)

**Generazione del token di accesso con Bash:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net/&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Generazione del token di accesso con Powershell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

La risposta contiene un token di accesso, informazioni sul periodo di validità del token e informazioni sulla risorsa per cui è possibile usare il token.
Il token di accesso ricevuto nella precedente chiamata HTTP deve attraversare tutte le richieste nell'API di Gestione risorse. Viene passato come un valore di intestazione denominato "Autorizzazione" con il valore "Bearer YOUR_ACCESS_TOKEN". Si noti lo spazio tra "Bearer" e il token di accesso.

Come si può osservare dal risultato HTTP precedente, il token è valido per un periodo di tempo specifico durante il quale è consigliabile memorizzarlo nella cache per poterlo usare di nuovo. Anche se è possibile eseguire l'autenticazione in Azure AD per ogni chiamata API, non sarebbe per nulla efficiente.

## <a name="calling-resource-manager-rest-apis"></a>Chiamata alle API REST di Gestione risorse
In questo articolo vengono usate alcune API per illustrare l'utilizzo di base delle operazioni REST. Per informazioni su tutte le operazioni, vedere [Azure Resource Manager REST APIs](https://docs.microsoft.com/rest/api/resources/) (API REST di Azure Resource Manager).

### <a name="list-all-subscriptions"></a>Elenco di tutte le sottoscrizioni
Una delle operazioni più semplici da eseguire consiste nell'elencare le sottoscrizioni disponibili a cui è possibile accedere. Nella richiesta seguente è possibile osservare che il token di accesso viene passato come intestazione:

Sostituire YOUR_ACCESS_TOKEN con il token di accesso vero e proprio.

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

Il risultato sarà un elenco di sottoscrizioni a cui questa entità servizio può accedere

(Gli ID sottoscrizione seguenti sono stati abbreviati per renderli più leggibili)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Elencare tutti i gruppi di risorse in una sottoscrizione specifica
Tutte le risorse disponibili con le API di Gestione risorse vengono annidate in un gruppo di risorse. È possibile eseguire query in Gestione risorse per gruppi di risorse esistenti nella sottoscrizione usando la richiesta HTTP GET seguente. Si noti che questa volta l'ID sottoscrizione viene passato come parte dell'URL.

(Sostituire YOUR_ACCESS_TOKEN e SUBSCRIPTION_ID con il token di accesso e l'ID sottoscrizione effettivi)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

La risposta ottenuta dipenderà dalla presenza o meno di gruppi di risorse definiti e, se presenti, dal numero dei gruppi.

(Gli ID sottoscrizione seguenti sono stati abbreviati per renderli più leggibili)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Finora, le query sono state eseguite in API di Gestione risorse solo per informazioni. È ora di creare delle risorse iniziando dalla più semplice, il gruppo di risorse. La richiesta HTTP seguente crea un gruppo di risorse in un'area/percorso di propria scelta e aggiunge un tag.

Sostituire YOUR_ACCESS_TOKEN, SUBSCRIPTION_ID, RESOURCE_GROUP_NAME con il token di accesso, l'ID sottoscrizione e il nome del gruppo di risorse effettivi che si vuole creare.

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Se l'operazione riesce, si ottiene una risposta simile alla seguente:

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

È stato creato un gruppo di risorse in Azure. Congratulazioni.

### <a name="deploy-resources-to-a-resource-group-using-a-resource-manager-template"></a>Distribuire risorse in un gruppo di risorse usando un modello di Gestione risorse
Con Gestione risorse, è possibile distribuire le risorse usando i modelli. Un modello definisce diverse risorse e le relative dipendenze. In questa sezione si presuppone che l'utente abbia familiarità con i modelli di Gestione risorse e verrà illustrato come fare in modo che la chiamata API inizi la distribuzione. Per altre informazioni sulla creazione dei modelli, vedere [Authoring Azure Resource Manager templates](resource-group-authoring-templates.md) (Creazione di modelli di Gestione risorse).

La distribuzione di un modello non è molto diversa dalla chiamata alle altre API. Un aspetto importante è che la distribuzione di un modello può richiedere molto tempo. La chiamata di API viene restituita e spetta all'utente in qualità di sviluppatore eseguire query per lo stato della distribuzione per scoprire quando la distribuzione viene eseguita. Per ulteriori informazioni, vedere [Track asynchronous Azure operations](resource-manager-async-operations.md) (Tenere traccia delle operazioni asincrone di Azure).

Per questo esempio, verrà usato un modello esposto pubblicamente disponibile su [GitHub](https://github.com/Azure/azure-quickstart-templates). Il modello che utilizziamo distribuisce una VM Linux nell'area degli Stati Uniti occidentali. Anche se questo esempio usa un modello disponibile in un repository pubblico come GitHub, è possibile scegliere di passare il modello completo come parte della richiesta. Si noti che nell'ambito della richiesta vengono forniti i valori dei parametri che vengono usati nel modello distribuito.

(Sostituire SUBSCRIPTION_ID, RESOURCE_GROUP_NAME, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME,ADMIN_PASSWORD e DNS_NAME_FOR_PUBLIC_IP con i valori appropriati per la richiesta)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

La lunga risposta JSON per questa richiesta è stata omessa per migliorare la leggibilità della documentazione. La risposta contiene informazioni sulla distribuzione basata sui modelli appena creati.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla gestione delle operazioni REST asincrone, vedere [Track asynchronous Azure operations](resource-manager-async-operations.md) (Tenere traccia delle operazioni asincrone di Azure).

