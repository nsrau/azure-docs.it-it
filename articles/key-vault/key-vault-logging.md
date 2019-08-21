---
title: Registrazione di Azure Key Vault | Microsoft Docs
description: Usare questa esercitazione per un'introduzione alla registrazione dell'insieme di credenziali delle chiavi di Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: fc0f259a4866f7eb2438938c7a10989db9200aa4
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976966"
---
# <a name="azure-key-vault-logging"></a>Registrazione di Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dopo aver creato una o più insiemi di credenziali delle chiavi, può essere utile monitorare come, quando e da chi vengono usate. A questo scopo è possibile abilitare la registrazione per Azure Key Vault, che salva le informazioni in un account di archiviazione di Azure specificato. Per tale account di archiviazione viene creato automaticamente un contenitore denominato **insights-log-auditevent**, che può essere usato per raccogliere i log relativi a più insiemi di credenziali delle chiavi.

È possibile accedere alle informazioni di registrazione dopo massimo 10 minuti dall'operazione sull'insieme di credenziali delle chiavi, ma nella maggior parte dei casi si potrà farlo prima.  La gestione dei log nell'account di archiviazione è compito dell'utente:

* Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando l'accesso agli utenti specificati.
* Eliminare i log che non è più necessario mantenere nell'account di archiviazione.

Usare questa esercitazione per un'introduzione alla registrazione dell'insieme di credenziali delle chiavi di Azure. Verrà creato un account di archiviazione, verrà abilitata la registrazione e verranno interpretate le informazioni relative ai log raccolte.  

> [!NOTE]
> Questa esercitazione non include istruzioni per la creazione di insiemi di credenziali delle chiavi, chiavi o segreti. Per queste informazioni, vedere [Cos'è Azure Key Vault?](key-vault-overview.md). In alternativa, per le istruzioni relative all'interfaccia della riga di comando di Azure, vedere [questa esercitazione equivalente](key-vault-manage-with-cli2.md).
>
> Questo articolo fornisce istruzioni per l'aggiornamento della registrazione diagnostica per Azure PowerShell. È anche possibile aggiornare la registrazione diagnostica usando Monitoraggio di Azure nella sezione **Log di diagnostica** del portale di Azure. 
>

Per informazioni generali su Key Vault, vedere [Cos'è Azure Key Vault?](key-vault-whatis.md). Per informazioni sulla disponibilità di Key Vault, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Insieme di credenziali delle chiavi esistente e già in uso.  
* Azure PowerShell, versione minima 1.0.0. Per installare Azure PowerShell e associarlo alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview). Se Azure PowerShell è già stato installato ma non si conosce la versione, dalla console di Azure PowerShell immettere `$PSVersionTable.PSVersion`.  
* Spazio di archiviazione sufficiente in Azure per i log dell'insieme di credenziali delle chiavi.

## <a id="connect"></a>Connettersi alla sottoscrizione di Key Vault

Il primo passaggio per la configurazione della registrazione delle chiavi consiste nell'indirizzare Azure PowerShell all'insieme di credenziali delle chiavi che si vuole registrare.

Avviare una sessione di Azure PowerShell e accedere al proprio account Azure con il comando seguente:  

```powershell
Connect-AzAccount
```

Nella finestra del browser a comparsa, immettere il nome utente e la password dell'account Azure. Azure PowerShell recupera tutte le sottoscrizioni associate a questo account e usa la prima per impostazione predefinita.

Potrebbe essere necessario specificare la sottoscrizione usata per creare l'insieme di credenziali delle chiavi. Immettere il comando seguente per visualizzare le sottoscrizioni relative all'account:

```powershell
Get-AzSubscription
```

Quindi, per specificare la sottoscrizione associata all'insieme di credenziali delle chiavi da registrare, immettere:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Indirizzare PowerShell alla sottoscrizione giusta è un passaggio importante, particolarmente utile se si hanno più sottoscrizioni associate all'account. Per altre informazioni sulla configurazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

## <a id="storage"></a>Creare un account di archiviazione per i log

Anche se è possibile usare un account di archiviazione esistente per i log, si creerà un account di archiviazione dedicato ai log di Key Vault. Per praticità, si archivieranno i dettagli in una variabile denominata **sa**, in modo che siano disponibili quando sarà necessario specificarli in seguito.

Per rendere la gestione ancora più facile, si userà anche lo stesso gruppo di risorse che contiene l'insieme di credenziali delle chiavi. Dall'[esercitazione introduttiva](key-vault-get-started.md) questo gruppo di risorse è denominato **ContosoResourceGroup** e si continuerà a usare l'area geografica Asia orientale. Sostituire questi valori con i propri, a seconda dei casi:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Se si sceglie di usare un account di archiviazione esistente, questo dovrà usare la stessa sottoscrizione del proprio insieme di credenziali delle chiavi. L'account dovrà anche usare il modello di distribuzione Azure Resource Manager, anziché il modello di distribuzione classica.
>
>

## <a id="identify"></a>Identificare l'insieme di credenziali delle chiavi per i log

Nell'[esercitazione introduttiva](key-vault-get-started.md), il nome dell'insieme di credenziali delle chiavi è **ContosoKeyVault**. Si continuerà a usare quel nome e si archivieranno i dettagli in una variabile denominata **kv**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Abilitare la registrazione

Per abilitare la registrazione per Key Vault, si userà il cmdlet **Set-AzDiagnosticSetting** con le variabili create per il nuovo account di archiviazione e l'insieme di credenziali delle chiavi. Inoltre, il flag **-Enabled** verrà impostato su **$true** e la categoria su **AuditEvent**, la sola disponibile per la registrazione di Key Vault:

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

L'output è simile al seguente:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Questo output conferma che la registrazione è abilitata per l'insieme di credenziali delle chiavi e che le informazioni vengono salvate nell'account di archiviazione.

Facoltativamente, è possibile impostare un criterio di conservazione per i log, in modo che i log meno recenti vengano eliminati automaticamente. Ad esempio, specificare i criteri di conservazione impostando il flag **-RetentionEnabled** su **$true** e il parametro **-RetentionInDays** su **90** per fare in modo che i log che risalgono a più di 90 giorni prima vengano eliminati automaticamente.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Informazioni registrate:

* Tutte le richieste API REST autenticate, incluse le richieste non riuscite a causa di autorizzazioni di accesso, errori di sistema o richieste non valide.
* Operazioni sull'insieme di credenziali delle chiavi stesso, tra cui creazione, eliminazione e impostazione di criteri di accesso all'insieme di credenziali delle chiavi, nonché aggiornamento degli attributi dell'insieme di credenziali delle chiavi, quali i tag.
* Operazioni su chiavi e segreti nell'insieme di credenziali delle chiavi, tra cui:
  * Creazione, modifica o eliminazione di queste chiavi o segreti.
  * Firma, verifica, crittografia, decrittografia, wrapping e annullamento del wrapping delle chiavi, recupero di segreti ed elenco di chiavi e segreti (e delle relative versioni).
* Richieste non autenticate che generano una risposta 401. Alcuni esempi sono le richieste che non hanno un token di connessione, hanno un formato non valido, sono scadute o hanno un token non valido.  

## <a id="access"></a>Accedere ai log

I log di Key Vault vengono archiviati nel contenitore **insights-log-auditevent** nell'account di archiviazione specificato. Per visualizzare i log, è necessario scaricare i BLOB.

Creare prima una variabile per il nome contenitore. Questa variabile verrà usata nel resto della procedura dettagliata.

```powershell
$container = 'insights-logs-auditevent'
```

Per elencare tutti i BLOB in questo contenitore, immettere:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

L'output è simile al seguente:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Come si vede dall'output, i BLOB seguono una convenzione di denominazione: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

I valori di data e ora sono nel formato UTC.

Poiché è possibile usare lo stesso account di archiviazione per raccogliere i log per più risorse, l'ID completo della risorsa nel nome del BLOB è utile per accedere solo ai BLOB necessari o per scaricarli. Prima di procedere, si vedrà però come scaricare tutti i BLOB.

Creare una cartella per scaricare i BLOB. Ad esempio:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Ottenere quindi un elenco di tutti i BLOB:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Inviare l'elenco tramite pipe con **Get-AzStorageBlobContent** per scaricare i BLOB nella cartella di destinazione:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Quando si esegue questo secondo comando, il delimitatore **/** nei nomi dei BLOB crea una struttura di cartelle completa nella cartella di destinazione. Questa struttura verrà usata per scaricare e archiviare i BLOB come file.

Per scaricare BLOB in modo selettivo, usare caratteri jolly. Ad esempio:

* Se sono disponibili più insiemi di credenziali delle chiavi e si vogliono scaricare i log per un solo insieme di credenziali delle chiavi denominato CONTOSOKEYVAULT3, usare:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Se sono disponibili più gruppi di risorse e si vogliono scaricare i log per un solo gruppo di risorse, usare `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Per scaricare tutti i log per il mese di gennaio 2019, usare `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

A questo punto si può iniziare a osservare il contenuto dei log. Prima di procedere, tuttavia, è necessario esaminare altri due comandi:

* Per eseguire una query sullo stato delle impostazioni di diagnostica per la risorsa insieme di credenziali delle chiavi, usare:`Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Per disabilitare la registrazione per la risorsa insieme di credenziali delle chiavi, usare: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Interpretare i log dell'insieme di credenziali delle chiavi

I singoli BLOB vengono archiviati come testo, formattati come BLOB JSON. Ecco un esempio di voce di registro. Eseguire questo comando:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Verrà restituita una voce di log simile alla seguente:

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

La tabella seguente elenca i nomi dei campi e le descrizioni:

| Nome campo | DESCRIZIONE |
| --- | --- |
| **time** |Data e ora in formato UTC. |
| **resourceId** |ID della risorsa di Azure Resource Manager. Per i log dell'insieme di credenziali delle chiavi questo è sempre l'ID della risorsa insieme di credenziali delle chiavi. |
| **operationName** |Nome dell'operazione, come illustrato nella tabella seguente. |
| **operationVersion** |Versione dell'API REST richiesta dal client. |
| **category** |Tipo di risultato. Per i log di Key Vault, **AuditEvent** è il solo valore disponibile. |
| **resultType** |Risultato della richiesta API REST. |
| **resultSignature** |Stato HTTP. |
| **resultDescription** |Descrizione aggiuntiva del risultato, se disponibile. |
| **durationMs** |Tempo impiegato per soddisfare la richiesta API REST, in millisecondi. Non include la latenza di rete, quindi il tempo misurato sul lato client potrebbe non corrispondere a questo valore. |
| **callerIpAddress** |Indirizzo IP del client che ha eseguito la richiesta. |
| **correlationId** |GUID facoltativo che il client può passare per correlare i log sul lato client con quelli sul lato servizio (insieme di credenziali delle chiavi). |
| **identity** |Identità del token presentato nella richiesta API REST. In genere si tratta di un "utente", una "entità servizio" o una combinazione "utente+appId", come nel caso di una richiesta generata da un cmdlet di Azure PowerShell. |
| **properties** |Informazioni diverse in base all'operazione (**operationName**). Nella maggior parte dei casi, questo campo contiene informazioni sul client (la stringa agente dell'utente passata dal client), l'URI esatto della richiesta dell'API REST e il codice di stato HTTP. Quando un oggetto viene restituito come risultato di una richiesta, ad esempio **KeyCreate** o **VaultGet**, conterrà anche l'URI della chiave (come "id"), l'URI dell'insieme di credenziali o l'URI del segreto. |

I valori del campo **operationName** sono nel formato *OggettoVerbo*. Ad esempio:

* Tutte le operazioni sull'insieme di credenziali delle chiavi hanno il formato `Vault<action>`, ad esempio `VaultGet` e `VaultCreate`.
* Tutte le operazioni sulle chiavi hanno il formato `Key<action>`, ad esempio `KeySign` e `KeyList`.
* Tutte le operazioni sui segreti hanno il formato `Secret<action>`, ad esempio `SecretGet` e `SecretListVersions`.

La tabella seguente include un elenco di valori **operationName** con il comando API REST corrispondente:

| operationName | Comando API REST |
| --- | --- |
| **autenticazione** |Eseguire l'autenticazione tramite l'endpoint di Azure Active Directory |
| **VaultGet** |[Ottenere informazioni su un insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Creare o aggiornare un insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Eliminare un insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Aggiornare un insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Elencare tutti gli insiemi di credenziali delle chiavi in un gruppo di risorse](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Creare una chiave](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Ottenere informazioni su una chiave](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importare una chiave in un insieme di credenziali](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Eseguire il backup di una chiave](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Eliminare una chiave](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Ripristinare una chiave](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Firmare con una chiave](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Verificare con una chiave](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Eseguire il wrapping di una chiave](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Annullare il wrapping di una chiave](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Crittografare con una chiave](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Decrittografare con una chiave](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Aggiornare una chiave](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Elencare le chiavi in un insieme di credenziali](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Elencare le versioni di una chiave](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Creare un segreto](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Ottenere un segreto](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Aggiornare un segreto](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Eliminare un segreto](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Elencare i segreti in un insieme di credenziali](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Elencare le versioni di un segreto](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Usare i log di Monitoraggio di Azure

È possibile usare la soluzione Key Vault nei log di Monitoraggio di Azure per esaminare i log **AuditEvent** di Key Vault. Nei log di Monitoraggio di Azure è possibile usare le query di log per analizzare i dati e ottenere le informazioni necessarie. 

Per altre informazioni e per approfondire l'impostazione di questa funzionalità vedere [Soluzione Azure Key Vault nei log di Monitoraggio di Azure](../azure-monitor/insights/azure-key-vault.md). Questo articolo contiene anche istruzioni su come eseguire la migrazione, nel caso fosse necessario, dalla precedente soluzione Key Vault offerta nell'anteprima dei log di Monitoraggio di Azure, dove sono stati indirizzati la prima volta i log a un account di Archiviazione di Azure e da dove sono stati configurati i log di Monitoraggio di Azure per la lettura.

## <a id="next"></a>Passaggi successivi

Per un'esercitazione sull'uso di Azure Key Vault in un'applicazione Web .NET, vedere [Usare Azure Key Vault da un'app Web](tutorial-net-create-vault-azure-web-app.md).

Per i riferimenti alla programmazione, vedere [Guida per gli sviluppatori dell'insieme di credenziali chiave Azure](key-vault-developers-guide.md).

Per un elenco di cmdlet di Azure PowerShell 1.0 per Azure Key Vault, vedere [Cmdlet per Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Per un'esercitazione sulla rotazione delle chiavi e il controllo dei log con Azure Key Vault, vedere [Configurare l'insieme di credenziali delle chiavi con rotazione e controllo delle chiavi end-to-end](key-vault-key-rotation-log-monitoring.md).
