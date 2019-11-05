---
title: Interfaccia della riga di comando dell'agente di computer connesso di Azure
description: Documentazione di riferimento per l'interfaccia della riga di comando di Azure Connected Machine Agent
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513198"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Interfaccia della riga di comando dell'agente di computer connesso di Azure

Lo strumento `Azcmagent` (agente del computer connesso di Azure) viene usato per configurare e risolvere i problemi relativi a una connessione di computer non Azure ad Azure.

L'agente è un processo daemon denominato `himdsd` in Linux e un servizio Windows denominato `himds` in Windows.

Nell'uso normale `azcmagent connect` viene usato per stabilire una connessione tra il computer e Azure e `azcmagent disconnect` se si decide di non volere più tale connessione. Gli altri comandi sono per la risoluzione dei problemi o altri casi particolari.

## <a name="options"></a>Opzioni

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>VEDERE ANCHE

* [azcmagent Connect](#azcmagent-connect) -connette il computer ad Azure
* disconnessione [azcmagent: disconnette](#azcmagent-disconnect) questo computer da Azure
* [riconnessione di azcmagent](#azcmagent-reconnect) : riconnette il computer ad Azure
* [azcmagent Show](#azcmagent-show) : recupera i metadati del computer e lo stato dell'agente. Questa operazione è utile principalmente per la risoluzione dei problemi.
* [versione di azcmagent](#azcmagent-version) -Visualizza la versione dell'agente di gestione ibrida

## <a name="azcmagent-connect"></a>connessione azcmagent

Connette il computer ad Azure

### <a name="synopsis"></a>Sinossi

Crea una risorsa in Azure che rappresenta questo computer.

Questo usa le opzioni di autenticazione fornite per creare una risorsa in Azure Resource Manager che rappresenta il computer. La risorsa si trova nella sottoscrizione e nel gruppo di risorse richiesti e i dati relativi al computer vengono archiviati nell'area di Azure specificata dal parametro location.
Se non è stato eseguito l'override, il nome predefinito della risorsa è il nome host del computer.

Un certificato corrispondente all'identità assegnata dal sistema di questo computer viene quindi scaricato e archiviato localmente. Una volta completato questo passaggio, il servizio **metadati del computer connesso di Azure** e l'agente di configurazione Guest iniziano la sincronizzazione con il cloud di Azure.

Opzioni di autenticazione:

* `azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>` token di accesso
* ID dell'entità servizio e segreto `azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* `azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>` accesso dispositivo (interattivo)

### <a name="syntax"></a>Sintassi

```none
azcmagent connect [flags]
```

### <a name="options"></a>Opzioni

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>disconnessione azcmagent

Disconnette questo computer da Azure

### <a name="synopsis"></a>Sinossi

Elimina la risorsa in Azure che rappresenta questo server.

Questo comando usa le opzioni di autenticazione fornite per rimuovere la risorsa Azure Resource Manager che rappresenta il computer. Al termine di questo punto, il **servizio metadati del computer connesso di Azure** e l'agente di configurazione Guest verranno disconnessi. Questo comando non arresta o rimuove i servizi: rimuovere il pacchetto a tale scopo.

Questo comando richiede privilegi più elevati rispetto al ruolo "caricamento del computer connesso di Azure".

Quando un computer è disconnesso, usare `azcmagent connect`, non `azcmagent reconnect` se si vuole creare una nuova risorsa in Azure.

Opzioni di autenticazione:

* `azcmagent disconnect --access-token <>` token di accesso
* ID dell'entità servizio e segreto `azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Accesso interattivo al dispositivo `azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>Sintassi

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>Opzioni

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>riconnessione azcmagent

Riconnette il computer ad Azure

### <a name="synopsis"></a>Sinossi

Riconnettere il computer con credenziali non valide ad Azure.

Se un computer dispone già di una risorsa in Azure, ma non è in grado di eseguire l'autenticazione, è possibile riconnetterla usando questo comando. Questo è possibile se un computer è stato disattivato abbastanza a lungo da scadere del certificato (almeno 45 giorni).

Se un computer è stato disconnesso con `azcmagent disconnect`, usare invece `azcmagent connect`.

Questo comando usa le opzioni di autenticazione fornite per recuperare le nuove credenziali corrispondenti alla risorsa Azure Resource Manager che rappresenta il computer.

Questo comando richiede privilegi più elevati rispetto al ruolo di **onboarding del computer connesso di Azure** .

Opzioni di autenticazione

* `azcmagent reconnect --access-token <>` token di accesso
* ID dell'entità servizio e segreto `azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Accesso interattivo al dispositivo `azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>Sintassi

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>Opzioni

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>azcmagent Show

Ottiene i metadati del computer e lo stato dell'agente. Questa operazione è utile principalmente per la risoluzione dei problemi.

### <a name="synopsis"></a>Sinossi

Ottiene i metadati del computer e lo stato dell'agente. Questa operazione è utile principalmente per la risoluzione dei problemi.


### <a name="syntax"></a>Sintassi

```
azcmagent show [flags]
```

### <a name="options"></a>Opzioni

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>versione di azcmagent

Visualizza la versione dell'agente di gestione ibrida

### <a name="synopsis"></a>Sinossi

Visualizza la versione dell'agente di gestione ibrida

### <a name="syntax"></a>Sintassi

```none
azcmagent version [flags]
```

### <a name="options"></a>Opzioni

```none
  -h, --help   help for version
```
