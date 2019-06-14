---
title: La registrazione di Azure Key Vault | Microsoft Docs
description: Usare questa esercitazione per un'introduzione alla registrazione dell'insieme di credenziali delle chiavi di Azure.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 89f9ef37ed7c53817854442b3a32b32b7d11ae27
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706012"
---
# <a name="azure-key-vault-logging"></a>Registrazione di Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dopo aver creato uno o più insiemi di credenziali chiave, sarà probabilmente si desidera monitorare come e quando gli insiemi di credenziali chiave sono a cui si accede e da chi. Questo scopo, è possibile abilitare la registrazione per Azure Key Vault, che salva le informazioni in un account di archiviazione di Azure specificata dall'utente. Un nuovo contenitore denominato **insights-log-auditevent** viene creato automaticamente per l'account di archiviazione specificato. È possibile usare questo stesso account di archiviazione per raccogliere i log di più insiemi di credenziali chiave.

È possibile accedere alle informazioni di registrazione 10 minuti (al massimo) al termine dell'operazione insieme di credenziali delle chiavi. ma nella maggior parte dei casi si potrà farlo prima.  La gestione dei log nell'account di archiviazione è compito dell'utente:

* Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando l'accesso agli utenti specificati.
* Eliminare i log che non è più necessario mantenere nell'account di archiviazione.

Usare questa esercitazione per un'introduzione alla registrazione dell'insieme di credenziali delle chiavi di Azure. Si sarà creare un account di archiviazione, abilitare la registrazione e interpretare le informazioni di log raccolti.  

> [!NOTE]
> Questa esercitazione non include istruzioni per la creazione di insiemi di credenziali delle chiavi, chiavi o segreti. Per queste informazioni, vedere [Cos'è Azure Key Vault?](key-vault-overview.md). In alternativa, per le istruzioni della riga di comando di Azure cross-platform, consultare [questa esercitazione equivalente](key-vault-manage-with-cli2.md).
>
> Questo articolo fornisce istruzioni per l'aggiornamento della registrazione diagnostica per Azure PowerShell. È anche possibile aggiornare la registrazione diagnostica con monitoraggio di Azure nel **log di diagnostica** sezione del portale di Azure. 
>

Per informazioni generali sull'insieme di credenziali delle chiavi, vedere [cos'è Azure Key Vault?](key-vault-whatis.md). Per informazioni su dove Key Vault è disponibile, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Insieme di credenziali delle chiavi esistente e già in uso.  
* Azure PowerShell, almeno la versione 1.0.0. Per installare Azure PowerShell e associarlo alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview). Se già stato installato Azure PowerShell e non conosce la versione, dalla console di Azure PowerShell, digitare `$PSVersionTable.PSVersion`.  
* Spazio di archiviazione sufficiente in Azure per i log dell'insieme di credenziali delle chiavi.

## <a id="connect"></a>Connettersi alla sottoscrizione di key vault

Il primo passaggio nella configurazione di registrazione dei tasti sia al punto di Azure PowerShell per l'insieme di credenziali delle chiavi che si desidera accedere.

Avviare una sessione di Azure PowerShell e accedere al proprio account Azure con il comando seguente:  

```powershell
Connect-AzAccount
```

Nella finestra del browser a comparsa, immettere il nome utente e la password dell'account Azure. Azure PowerShell recupera tutte le sottoscrizioni associate a questo account. Per impostazione predefinita, PowerShell Usa la prima.

Potrebbe essere necessario specificare la sottoscrizione usata per creare l'insieme di credenziali delle chiavi. Immettere il comando seguente per visualizzare le sottoscrizioni per l'account:

```powershell
Get-AzSubscription
```

Quindi, per specificare la sottoscrizione che ha associato l'insieme di credenziali di che accesso, immettere:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Che punta a PowerShell per la sottoscrizione più appropriata è un passaggio importante, soprattutto se si hanno più sottoscrizioni associate all'account. Per altre informazioni sulla configurazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

## <a id="storage"></a>Creare un account di archiviazione per i log

Sebbene sia possibile usare un account di archiviazione per i log, si creerà un account di archiviazione dedicato ai log di Key Vault. Per praticità quando è necessario specificarli in seguito, si archivieranno i dettagli in una variabile denominata **sa**.

Per rendere ancora più facile gestione, si userà anche stesso gruppo di risorse è quello che contiene l'insieme di credenziali delle chiavi. Dal [esercitazione introduttiva](key-vault-get-started.md), questo gruppo di risorse è denominato **ContosoResourceGroup**, e si continuerà a usare l'area Asia orientale. Sostituire questi valori con quelli personalizzati, come applicabile:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Se si decide di usare un account di archiviazione esistente, è necessario usare la stessa sottoscrizione come insieme di credenziali delle chiavi. E deve utilizzare il modello di distribuzione Azure Resource Manager, anziché il modello di distribuzione classica.
>
>

## <a id="identify"></a>Identificare l'insieme di credenziali delle chiavi per i log

Nel [esercitazione introduttiva](key-vault-get-started.md), il nome dell'insieme di credenziali chiave è stata **ContosoKeyVault**. Microsoft continuerà a usare il nome e archiviare i dettagli in una variabile denominata **kv**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Abilitare la registrazione

Per abilitare la registrazione per Key Vault, si userà il **Set-AzDiagnosticSetting** cmdlet, insieme alle variabili create per il nuovo account di archiviazione e l'insieme di credenziali delle chiavi. Impostiamo anche il **-abilitata** flag **$true** e impostare la categoria **AuditEvent** (l'unica categoria per la registrazione di Key Vault):

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

Questo output conferma che la registrazione è abilitata per l'insieme di credenziali delle chiavi e salverà informazioni all'account di archiviazione.

Facoltativamente, è possibile impostare un criterio di conservazione per i log in modo che i log meno recenti vengano eliminati automaticamente. Ad esempio, impostare criteri di conservazione impostando il **- RetentionEnabled** flag **$true**e impostare il **- RetentionInDays** parametro **90**in modo che i log antecedenti a 90 giorni vengono eliminati automaticamente.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Informazioni registrate:

* Tutte autenticate richieste dell'API REST, tra cui le richieste non riuscite a causa di autorizzazioni di accesso, errori di sistema o richieste non valide.
* Operazioni per la chiave dell'insieme di credenziali stesso, tra cui creazione, eliminazione e impostazione dei criteri di accesso dell'insieme di credenziali chiave e aggiornare gli attributi dell'insieme di credenziali chiave come i tag.
* Operazioni sulle chiavi e segreti nell'insieme di credenziali chiave, tra cui:
  * Creazione, modifica o eliminazione di chiavi o segreti.
  * Firma, verifica, la crittografia, decrittografia, ritorno a capo e rimozione del wrapping delle chiavi, recupero di informazioni riservate ed elenco chiavi e segreti (e relative versioni).
* Richieste non autenticate che generano una risposta 401. Esempi sono le richieste senza un token di connessione, che sono in formato non valido o è scaduto o che hanno un token non valido.  

## <a id="access"></a>Accedere ai log

I log di Key Vault sono archiviati nel **insights-log-auditevent** contenitore nell'account di archiviazione fornito dall'utente. Per visualizzare i log, è necessario scaricare i BLOB.

Creare prima una variabile per il nome contenitore. È possibile usare questa variabile in tutto il resto della procedura dettagliata.

```powershell
$container = 'insights-logs-auditevent'
```

Per elencare tutti i BLOB nel contenitore, immettere:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

L'output è simile al seguente:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

- - -
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Come si vede dall'output, i BLOB seguono una convenzione di denominazione: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

I valori di data e ora sono nel formato UTC.

Poiché è possibile usare lo stesso account di archiviazione per raccogliere i log per più risorse, è utile per accedere o scaricare solo i blob che è necessario l'ID risorsa completo nel nome del blob. Prima di procedere, si vedrà però come scaricare tutti i BLOB.

Creare una cartella per scaricare i BLOB. Ad esempio:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Ottenere quindi un elenco di tutti i BLOB:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Inviare l'elenco tramite pipe **Get-AzStorageBlobContent** per scaricare i BLOB nella cartella di destinazione:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Quando si esegue questo secondo comando, il delimitatore **/** nei nomi dei BLOB crea una struttura di cartelle completa nella cartella di destinazione. Si userà questa struttura per scaricare e archiviare i BLOB come file.

Per scaricare BLOB in modo selettivo, usare caratteri jolly. Ad esempio:

* Se sono disponibili più insiemi di credenziali delle chiavi e si vogliono scaricare i log per un solo insieme di credenziali delle chiavi denominato CONTOSOKEYVAULT3, usare:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Se sono disponibili più gruppi di risorse e si vogliono scaricare i log per un solo gruppo di risorse, usare `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Se si vuole scaricare tutti i log per il mese di gennaio 2019, usare `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

A questo punto si può iniziare a osservare il contenuto dei log. Ma prima di procedere a quello, è necessario conoscere due altri comandi:

* Per eseguire una query sullo stato delle impostazioni di diagnostica per la risorsa insieme di credenziali delle chiavi, usare:`Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Per disabilitare la registrazione per la risorsa insieme di credenziali delle chiavi, usare: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Interpretare i log dell'insieme di credenziali delle chiavi

I singoli BLOB vengono archiviati come testo, formattati come BLOB JSON. Verrà ora esaminato un esempio di voce di log. Eseguire questo comando:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Restituisce una voce di log simile alla seguente:

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

La tabella seguente elenca i nomi di campo e descrizioni:

| Nome campo | Descrizione |
| --- | --- |
| **time** |Data e ora in formato UTC. |
| **resourceId** |ID della risorsa di Azure Resource Manager. Per i log dell'insieme di credenziali delle chiavi questo è sempre l'ID della risorsa insieme di credenziali delle chiavi. |
| **operationName** |Nome dell'operazione, come illustrato nella tabella seguente. |
| **operationVersion** |Versione dell'API REST richiesta dal client. |
| **category** |Tipo di risultato. Per i log di Key Vault **AuditEvent** è il solo valore disponibile. |
| **resultType** |Risultato della richiesta API REST. |
| **resultSignature** |Stato HTTP. |
| **resultDescription** |Descrizione aggiuntiva del risultato, se disponibile. |
| **durationMs** |Tempo impiegato per soddisfare la richiesta API REST, in millisecondi. Non include la latenza di rete, quindi il tempo misurato sul lato client potrebbe non corrispondere a questo valore. |
| **callerIpAddress** |Indirizzo IP del client che ha effettuato la richiesta. |
| **correlationId** |GUID facoltativo che il client può passare per correlare i log sul lato client con quelli sul lato servizio (insieme di credenziali delle chiavi). |
| **identity** |Identità del token presentato nella richiesta di API REST. Si tratta in genere un "utente", una "entità servizio" o la combinazione "utente + appId," come nel caso di una richiesta risultante da un cmdlet di PowerShell di Azure. |
| **properties** |Informazioni che variano a seconda dell'operazione (**operationName**). Nella maggior parte dei casi, questo campo contiene informazioni sul client (la stringa agente utente passata dal client), l'URI della richiesta API REST esatte e il codice di stato HTTP. Inoltre, quando un oggetto viene restituito come risultato di una richiesta (ad esempio, **KeyCreate** oppure **VaultGet**), include anche la chiave URI (come "id"), l'insieme di credenziali, URI o URI del segreto. |

Il **operationName** i valori dei campi presenti *Oggettoverbo* formato. Ad esempio:

* Tutte le operazioni di insieme di credenziali delle chiavi hanno il `Vault<action>` formato, ad esempio `VaultGet` e `VaultCreate`.
* Tutte le operazioni sulle chiavi hanno il `Key<action>` formato, ad esempio `KeySign` e `KeyList`.
* Tutte le operazioni sui segreti hanno il `Secret<action>` formato, ad esempio `SecretGet` e `SecretListVersions`.

La tabella seguente elenca i **operationName** valori e i corrispondenti comandi dell'API REST:

| operationName | Comando API REST |
| --- | --- |
| **autenticazione** |Eseguire l'autenticazione tramite l'endpoint Azure Active Directory |
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
| **SecretGet** |[Ottenere una chiave privata](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Aggiornare un segreto](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Eliminare un segreto](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Elencare i segreti in un insieme di credenziali](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Elencare le versioni di un segreto](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Usare i log di monitoraggio di Azure

È possibile usare la soluzione Key Vault nei log di monitoraggio di Azure per esaminare insieme di credenziali delle chiavi **AuditEvent** i log. Nei log, monitoraggio di Azure è usare le query di log per analizzare i dati e ottenere le informazioni che necessarie. 

Per altre informazioni, incluse le procedure impostare questa funzionalità, vedere [soluzione Azure Key Vault in Monitoraggio di Azure log](../azure-monitor/insights/azure-key-vault.md). Questo articolo contiene istruzioni anche se è necessario eseguire la migrazione dalla soluzione Key Vault precedente che è stato offerto durante i log di monitoraggio di Azure in anteprima, in cui è indirizzato prima di tutto i log a un account di archiviazione di Azure e log di monitoraggio di Azure configurata per la lettura.

## <a id="next"></a>Passaggi successivi

Per un'esercitazione che usa Azure Key Vault in un'applicazione web .NET, vedere [usare Azure Key Vault da un'applicazione web](tutorial-net-create-vault-azure-web-app.md).

Per i riferimenti alla programmazione, vedere [Guida per gli sviluppatori dell'insieme di credenziali chiave Azure](key-vault-developers-guide.md).

Per un elenco dei cmdlet Azure PowerShell 1.0 per Azure Key Vault, vedere [i cmdlet di Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Per un'esercitazione sulla rotazione delle chiavi e il controllo dei log con Azure Key Vault, vedere [configurare l'insieme di credenziali delle chiavi con rotazione delle chiavi end-to-end e il controllo](key-vault-key-rotation-log-monitoring.md).
