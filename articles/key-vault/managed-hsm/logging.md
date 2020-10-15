---
title: Registrazione del modulo di protezione hardware gestito di Azure
description: Questa esercitazione offre un'introduzione alla registrazione del modulo di protezione hardware gestito.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 22abd38ead1257b49eeae98acfcd74349f563811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90992294"
---
# <a name="managed-hsm-logging"></a>Registrazione del modulo di protezione hardware gestito 

Dopo aver creato uno o più moduli di protezione hardware gestiti, può essere utile monitorare come, quando e da chi vengono usati. A questo scopo è possibile abilitare la registrazione, che salva le informazioni in un account di archiviazione di Azure specificato. Per tale account di archiviazione viene creato automaticamente un contenitore denominato **insights-log-auditevent**, che può essere usato per raccogliere i log relativi a più moduli di protezione hardware gestiti.

È possibile accedere alle informazioni di registrazione dopo massimo 10 minuti dall'operazione del modulo di protezione hardware gestito, ma nella maggior parte dei casi si potrà farlo prima.  La gestione dei log nell'account di archiviazione è compito dell'utente:

* Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando l'accesso agli utenti specificati.
* Eliminare i log che non è più necessario mantenere nell'account di archiviazione.

Questa esercitazione offre un'introduzione alla registrazione del modulo di protezione hardware gestito. Verrà creato un account di archiviazione, verrà abilitata la registrazione e verranno interpretate le informazioni relative ai log raccolte.  

> [!NOTE]
> Questa esercitazione non include le istruzioni relative alla creazione di chiavi o moduli di protezione hardware gestiti. Questo articolo fornisce le istruzioni per l'aggiornamento della registrazione diagnostica con l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

* Una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interfaccia della riga di comando di Azure versione 2.12.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).
* Un modulo di protezione hardware gestito nella sottoscrizione. Vedere [Avvio rapido: Effettuare il provisioning di un modulo di protezione hardware gestito e attivarlo tramite l'interfaccia della riga di comando di Azure](quick-create-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Connettersi alla sottoscrizione di Azure

Il primo passaggio per la configurazione della registrazione delle chiavi consiste nell'indirizzare l'interfaccia della riga di comando di Azure al modulo di protezione hardware gestito che si vuole registrare.

```azurecli-interactive
az login
```

Per altre informazioni sulle opzioni di accesso con l'interfaccia della riga di comando, vedere [Accedere con l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

Potrebbe essere necessario specificare la sottoscrizione usata per creare il modulo di protezione hardware gestito. Immettere il comando seguente per visualizzare le sottoscrizioni relative all'account:

## <a name="identify-the-managed-hsm-and-storage-account"></a>Identificare il modulo di protezione hardware gestito e l'account di archiviazione

```azurecli-interactive
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name ContosoMHSMLogs --query id -o tsv)
```

## <a name="enable-logging"></a>Abilitazione della registrazione

Per abilitare la registrazione per il modulo di protezione hardware gestito, si userà usare il comando **az monitor diagnostic-settings create**, unitamente alle variabili create per il nuovo account di archiviazione e il modulo di protezione hardware gestito. Inoltre, il flag **-Enabled** verrà impostato su **$true** e la categoria su **AuditEvent**, la sola disponibile per la registrazione del modulo di protezione hardware gestito:

Questo output conferma che la registrazione è abilitata per il modulo di protezione hardware gestito e che le informazioni vengono salvate nell'account di archiviazione.

Facoltativamente, è possibile impostare un criterio di conservazione per i log, in modo che i log meno recenti vengano eliminati automaticamente. Ad esempio, specificare i criteri di conservazione impostando il flag **-RetentionEnabled** su **$true** e il parametro **-RetentionInDays** su **90** per fare in modo che i log che risalgono a più di 90 giorni prima vengano eliminati automaticamente.

```azurecli-interactive
az monitor diagnostic-settings create --name ContosoMHSM-Diagnostics --resource $hsmresource --logs '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource
```

Informazioni registrate:

* Tutte le richieste API REST autenticate, incluse le richieste non riuscite a causa di autorizzazioni di accesso, errori di sistema o richieste non valide.
* Operazioni sul modulo di protezione hardware gestito stesso, tra cui creazione, eliminazione e aggiornamento degli attributi, ad esempio i tag.
* Operazioni correlate al dominio di sicurezza, ad esempio inizializzazione e download, ripristino dell'inizializzazione e caricamento
* Operazioni di backup e ripristino completo e ripristino selettivo del modulo di protezione hardware
* Operazioni sulle chiavi, tra cui:
  * Creazione, modifica o eliminazione delle chiavi.
  * Firma, verifica, crittografia, decrittografia, wrapping e annullamento del wrapping delle chiavi, elenco delle chiavi.
  * Backup, ripristino e rimozione definitiva delle chiavi
* Richieste non autenticate che generano una risposta 401. Alcuni esempi sono le richieste che non hanno un token di connessione, hanno un formato non valido, sono scadute o hanno un token non valido.  

## <a name="access-your-logs"></a>Accedere ai log

I log del modulo di protezione hardware gestito vengono archiviati nel contenitore **insights-log-auditevent** nell'account di archiviazione specificato. Per visualizzare i log, è necessario scaricare i BLOB. Per informazioni su Archiviazione di Azure, vedere [Creare, scaricare ed elencare BLOB con l'interfaccia della riga di comando di Azure](../../storage/blobs/storage-quickstart-blobs-cli.md).

I singoli BLOB vengono archiviati come testo in formato JSON. Ecco un esempio di voce di registro. L'esempio seguente illustra la voce del log quando al modulo di protezione hardware gestito viene inviata una richiesta di creazione di un backup completo.

```json
[
  {
    "TenantId": "766eaf62-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "time": "2020-08-31T19:52:39.763Z",
    "resourceId": "/SUBSCRIPTIONS/A1BA9AAA-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/MANAGEDHSMS/CONTOSOMHSM",
    "operationName": "BackupCreate",
    "operationVersion": "7.0",
    "category": "AuditEvent",
    "resultType": "Success",
    "properties": {
        "PoolType": "M-HSM",
        "sku_Family": "B",
        "sku_Name": "Standard_B1"
    },
    "durationMs": 488,
    "callerIpAddress": "X.X.X.X",
    "identity": "{\"claim\":{\"appid\":\"04b07795-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"http_schemas_microsoft_com_identity\":{\"claims\":{\"objectidentifier\":\"b1c52bf0-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"}},\"http_schemas_xmlsoap_org_ws_2005_05_identity\":{\"claims\":{\"upn\":\"admin@contoso.com\"}}}}",
    "clientInfo": "azsdk-python-core/1.7.0 Python/3.8.2 (Linux-4.19.84-microsoft-standard-x86_64-with-glibc2.29) azsdk-python-azure-keyvault/7.2",
    "correlationId": "8806614c-ebc3-11ea-9e9b-00155db778ad",
    "subnetId": "(unknown)",
    "httpStatusCode": 202,
    "PoolName": "mhsmdemo",
    "requestUri": "https://ContosoMHSM.managedhsm.azure.net/backup",
    "resourceGroup": "ContosoResourceGroup",
    "resourceProvider": "MICROSOFT.KEYVAULT",
    "resource": "ContosoMHSM",
    "resourceType": "managedHSMs"
  }
]
```

La tabella seguente elenca i nomi dei campi e le descrizioni:

| Nome campo | Descrizione |
| --- | --- |
| **TenantId** | ID tenant di Azure Active Directory della sottoscrizione in cui viene creato il modulo di protezione hardware gestito |
| **time** |Data e ora in formato UTC. |
| **resourceId** |ID della risorsa di Azure Resource Manager. Per i log del modulo di protezione hardware gestito, corrisponde sempre all'ID risorsa del modulo di protezione hardware gestito. |
| **operationName** |Nome dell'operazione, come illustrato nella tabella seguente. |
| **operationVersion** |Versione dell'API REST richiesta dal client. |
| **category** |Tipo di risultato. Per i log del modulo di protezione hardware gestito, **AuditEvent** è il solo valore disponibile. |
| **resultType** |Risultato della richiesta API REST. |
| **properties** |Informazioni diverse in base all'operazione (**operationName**).|
| **resultSignature** |Stato HTTP. |
| **resultDescription** |Descrizione aggiuntiva del risultato, se disponibile. |
| **durationMs** |Tempo impiegato per soddisfare la richiesta API REST, in millisecondi. Non include la latenza di rete, quindi il tempo misurato sul lato client potrebbe non corrispondere a questo valore. |
| **callerIpAddress** |Indirizzo IP del client che ha eseguito la richiesta. |
| **correlationId** |GUID facoltativo che il client può passare per correlare i log sul lato client con quelli sul lato servizio. |
| **identity** |Identità del token presentato nella richiesta API REST. È in genere un "utente" o un'"entità servizio". |
| **requestUri** | URI della richiesta dell'API REST |
| **clientInfo** | 

## <a name="use-azure-monitor-logs"></a>Usare i log di Monitoraggio di Azure

È possibile usare la soluzione Key Vault nei log di Monitoraggio di Azure per esaminare i log **AuditEvent** del modulo di protezione hardware gestito. Nei log di Monitoraggio di Azure è possibile usare le query di log per analizzare i dati e ottenere le informazioni necessarie. 

## <a name="next-steps"></a>Passaggi successivi

- Vedere le [procedure consigliate](best-practices.md) per effettuare il provisioning di un modulo di protezione hardware gestito e usarlo
- Vedere l'articolo su [come eseguire backup e ripristino](backup-restore.md) di un modulo di protezione hardware gestito
