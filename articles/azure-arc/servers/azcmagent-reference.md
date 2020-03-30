---
title: Interfaccia della riga di comando di Azure Connected Machine Agent
description: Documentazione di riferimento per l'interfaccia della riga di comando dell'agente di macchine connesso di AzureReference documentation for the Azure Connected Machine agent CLI
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513198"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Interfaccia della riga di comando di Azure Connected Machine Agent

Lo `Azcmagent` strumento (Agente computer connesso ad Azure) viene usato per configurare e risolvere i problemi di una connessione di macchine non azzurre ad Azure.The (Azure Connected Machine Agent) tool is used to configure and troubleshoot a non-azure machines connection to Azure.

L'agente stesso è un `himdsd` processo daemon chiamato `himds` su Linux e un servizio Windows chiamato su Windows.

In uso `azcmagent connect` normale, viene usato per stabilire una `azcmagent disconnect` connessione tra questo computer e Azure e se si decide di non voler più la connessione. Gli altri comandi sono per la risoluzione dei problemi o altri casi speciali.

## <a name="options"></a>Opzioni

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>VEDERE ANCHE

* [azcmagent connect](#azcmagent-connect) - Connette questa macchina ad Azureazcmagent connect - Connects this machine to Azure
* [azcmagent disconnect](#azcmagent-disconnect) - Disconnette la macchina da Azureazcmagent disconnect - Disconnects this machine from Azure
* [Azcmagent reconnect](#azcmagent-reconnect) - Riconnette il computer ad AzureAzcmagent reconnect - Reconnects this machine to Azure
* [azcmagent show](#azcmagent-show) - Ottiene i metadati del computer e lo stato dell'agente. Ciò è utile principalmente per la risoluzione dei problemi.
* [versione azcmagent](#azcmagent-version) - Visualizzare la versione dell'agente di gestione ibrido

## <a name="azcmagent-connect"></a>azcmagent connect

Connette questa macchina ad Azure

### <a name="synopsis"></a>Riepilogo

Crea una risorsa in Azure che rappresenta questa macchina.

In questo modo vengono utilizzate le opzioni di autenticazione fornite per creare una risorsa in Azure Resource Manager che rappresenta questa macchina. La risorsa si trova nella sottoscrizione e nel gruppo di risorse richiesto e i dati relativi al computer vengono archiviati nell'area di Azure specificata dal parametro location.
Il nome di risorsa predefinito è il nome host del computer, se non sottoposto a override.

Un certificato corrispondente all'identità assegnata dal sistema del computer viene quindi scaricato e archiviato in locale. Al termine di questo passaggio, il servizio **metadati del computer connesso di Azure** e l'agente di configurazione guest iniziano la sincronizzazione con il cloud di Azure.Once this step completes the Azure Connected Machine Metadata Service and Guest Configuration Agent begin synchronizing with Azure cloud.

Opzioni di autenticazione:

* Token di accesso`azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* ID entità servizio e segreto`azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Accesso al dispositivo (interattivo)`azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

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

## <a name="azcmagent-disconnect"></a>azcmagent disconnessione

Disconnette la macchina da AzureDisconnects this machine from Azure

### <a name="synopsis"></a>Riepilogo

Elimina la risorsa in Azure che rappresenta questo server.

Questo comando usa le opzioni di autenticazione fornite per rimuovere la risorsa di Azure Resource Manager che rappresenta questa macchina. Dopo questo punto il **servizio metadati del computer connesso di Azure** e l'agente di configurazione guest verranno disconnessi. Questo comando non arresta o rimuove i servizi: rimuovere il pacchetto per farlo.

Questo comando richiede privilegi più elevati rispetto al ruolo "Azure Connected Machine Onboarding".

Dopo aver disconnesso `azcmagent connect`un `azcmagent reconnect` computer, usare , non se si vuole creare una nuova risorsa in Azure.Once a machine is disconnected, use , not if you want to create a new resource for it in Azure.

Opzioni di autenticazione:

* Token di accesso`azcmagent disconnect --access-token <>`
* ID entità servizio e segreto`azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Accesso interattivo al dispositivo`azcmagent disconnect --tenant-id <>`

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

## <a name="azcmagent-reconnect"></a>azcmagent riconnessione

Riconnette il computer ad AzureReconnects this machine to Azure

### <a name="synopsis"></a>Riepilogo

Riconnettere il computer con credenziali non valide ad Azure.Reconnect machine with invalid credentials to Azure.

Se un computer dispone già di una risorsa in Azure ma non è in grado di eseguire l'autenticazione, è possibile riconnettersi usando questo comando. Ciò è possibile se un computer è stato spento abbastanza a lungo per la scadenza del certificato (almeno 45 giorni).

Se un computer `azcmagent disconnect`è `azcmagent connect` stato disconnesso con , utilizzare.

Questo comando usa le opzioni di autenticazione fornite per recuperare le nuove credenziali corrispondenti alla risorsa di Azure Resource Manager che rappresenta questo computer.

Questo comando richiede privilegi più elevati rispetto al ruolo **di onboarding del computer connesso** ad Azure.This command requires higher privileges than the Azure Connected Machine Onboarding role.

Opzioni di autenticazione

* Token di accesso`azcmagent reconnect --access-token <>`
* ID entità servizio e segreto`azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Accesso interattivo al dispositivo`azcmagent reconnect --tenant-id <>`

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

## <a name="azcmagent-show"></a>azcmagent show

Ottiene i metadati del computer e lo stato dell'agente. Ciò è utile principalmente per la risoluzione dei problemi.

### <a name="synopsis"></a>Riepilogo

Ottiene i metadati del computer e lo stato dell'agente. Ciò è utile principalmente per la risoluzione dei problemi.


### <a name="syntax"></a>Sintassi

```
azcmagent show [flags]
```

### <a name="options"></a>Opzioni

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>versione azcmagent

Visualizzare la versione dell'agente di gestione ibrido

### <a name="synopsis"></a>Riepilogo

Visualizzare la versione dell'agente di gestione ibrido

### <a name="syntax"></a>Sintassi

```none
azcmagent version [flags]
```

### <a name="options"></a>Opzioni

```none
  -h, --help   help for version
```
