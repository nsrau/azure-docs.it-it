---
title: Come abilitare la registrazione Azure Key Vault
description: Come abilitare la registrazione per Azure Key Vault, che salva le informazioni in un account di archiviazione di Azure fornito dall'utente.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 5e0007f3b0dad8a68e9d81cebbe9fe24b5a7db3c
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285640"
---
# <a name="how-to-enable-key-vault-logging"></a>Come abilitare la registrazione di Key Vault

Dopo aver creato una o più insiemi di credenziali delle chiavi, può essere utile monitorare come, quando e da chi vengono usate. Per informazioni dettagliate sulla funzionalità, vedere [Key Vault registrazione](logging.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione è necessario quanto segue:

* Insieme di credenziali delle chiavi esistente e già in uso.  
* Interfaccia della riga di comando di Azure o Azure PowerShell.
* Spazio di archiviazione sufficiente in Azure per i log dell'insieme di credenziali delle chiavi.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, sarà necessaria la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Per accedere ad Azure usando l'interfaccia della riga di comando è possibile digitare:

```azurecli-interactive
az login
```

Se si sceglie di installare e usare PowerShell in locale, sarà necessario il modulo Azure PowerShell versione 1.0.0 o successiva. Digitare `$PSVersionTable.PSVersion` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

```powershell-interactive
Connect-AzAccount
```

## <a name="connect-to-your-key-vault-subscription"></a>Connettersi alla sottoscrizione di Key Vault

Il primo passaggio per la configurazione della registrazione delle chiavi è la connessione alla sottoscrizione che contiene l'insieme di credenziali delle chiavi. Questo è particolarmente importante se sono presenti più sottoscrizioni associate all'account.

Con l'interfaccia della riga di comando di Azure è possibile visualizzare tutte le sottoscrizioni usando il comando [AZ account list](/cli/azure/account?view=azure-cli-latest#az_account_list) e quindi connettersi a una usando [AZ account set](/cli/azure/account?view=azure-cli-latest#az_account_set):

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

Con Azure PowerShell è possibile elencare prima le sottoscrizioni usando il cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-4.7.0) e quindi connettersi a una usando il cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-4.7.0) : 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>Creare un account di archiviazione per i log

Sebbene sia possibile usare un account di archiviazione esistente per i log, verrà creato un nuovo account di archiviazione dedicato ai log Key Vault. 

Per rendere la gestione ancora più facile, si userà anche lo stesso gruppo di risorse che contiene l'insieme di credenziali delle chiavi. Nella [Guida introduttiva dell'interfaccia](quick-create-cli.md) della riga di comando di Azure e [Azure PowerShell avvio rapido](quick-create-powershell.md)questo gruppo di risorse è denominato **myResourceGroup** e il percorso è *eastus*. Sostituire questi valori con quelli personalizzati, come applicabile. 

È anche necessario specificare un nome di account di archiviazione. I nomi degli account di archiviazione devono essere univoci, con una lunghezza compresa tra 3 e 24 caratteri e usare solo numeri e lettere minuscole.  Infine, verrà creato un account di archiviazione dello SKU "Standard_LRS".

Con l'interfaccia della riga di comando di Azure usare il comando [AZ storage account create](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create) .

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

Con Azure PowerShell, usare il cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount?view=azps-4.7.0) . Sarà necessario specificare il percorso corrispondente al gruppo di risorse.

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

In entrambi i casi, prendere nota dell'"ID" dell'account di archiviazione. L'operazione dell'interfaccia della riga di comando di Azure restituisce l'ID nell'output. Per ottenere "ID" con Azure PowerShell, usare [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-4.7.0) e assegnare l'output a una variabile $SA. È quindi possibile visualizzare l'account di archiviazione con $sa. ID. ("$Sa. Verrà usata anche la proprietà Context, più avanti in questo articolo.

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

Il "ID" dell'account di archiviazione sarà nel formato "/subscriptions/<your-Subscription-ID>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/<il nome-Unique-storage-account-name>".

> [!NOTE]
> Se si decide di usare un account di archiviazione esistente, è necessario usare la stessa sottoscrizione dell'insieme di credenziali delle chiavi e usare il modello di distribuzione Azure Resource Manager, anziché il modello di distribuzione classica.

## <a name="obtain-your-key-vault-resource-id"></a>Ottenere l'ID risorsa dell'insieme di credenziali delle chiavi

Nella [Guida introduttiva dell'interfaccia](quick-create-cli.md) della riga di comando e in [PowerShell](quick-create-powershell.md)è stata creata una chiave con un nome univoco.  Usare nuovamente questo nome nei passaggi seguenti.  Se non è possibile ricordare il nome dell'insieme di credenziali delle chiavi, è possibile usare l'interfaccia della riga di comando di Azure [AZ Key Vault list](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_list) oppure il cmdlet Azure PowerShell [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) per elencarli.

Usare il nome dell'insieme di credenziali delle chiavi per trovare il relativo ID risorsa.  Con l'interfaccia della riga di comando di Azure usare il comando [AZ Vault Show](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_show) .

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

Con Azure PowerShell, usare il cmdlet [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) .

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

L'ID di risorsa per l'insieme di credenziali delle chiavi sarà nel formato "/subscriptions/<your-Subscription-ID>/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/<Your-Unique-Key Vault-Name>". Si noti che per il passaggio successivo.

## <a name="enable-logging-using-azure-powershell"></a>Abilitare la registrazione con Azure PowerShell

Per abilitare la registrazione per Key Vault, usare l'interfaccia della riga di comando di Azure [AZ monitor Diagnostic-Settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest) oppure il cmdlet [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) , insieme all'ID dell'account di archiviazione e all'ID della risorsa dell'insieme di credenziali delle chiavi.

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

Con Azure PowerShell, verrà usato il cmdlet [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) , con il flag **-Enabled** impostato su **$true** e la categoria impostata su `AuditEvent` (l'unica categoria per la registrazione Key Vault):

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

Facoltativamente, è possibile impostare un criterio di conservazione per i log, in modo che i log meno recenti vengano eliminati automaticamente dopo un periodo di tempo specificato. Ad esempio, è possibile impostare criteri di conservazione per l'eliminazione automatica dei log più vecchi di 90 giorni.

<!-- With the Azure CLI, use the [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_update) command. 

```azurecli-interactive
az monitor diagnostic-settings update 
```
-->

Con Azure PowerShell, usare il cmdlet [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) . 

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Cosa viene registrato:

* Tutte le richieste API REST autenticate, incluse le richieste non riuscite a causa di autorizzazioni di accesso, errori di sistema o richieste non valide.
* Operazioni sull'insieme di credenziali delle chiavi stesso, tra cui creazione, eliminazione e impostazione di criteri di accesso all'insieme di credenziali delle chiavi, nonché aggiornamento degli attributi dell'insieme di credenziali delle chiavi, quali i tag.
* Operazioni su chiavi e segreti nell'insieme di credenziali delle chiavi, tra cui:
  * Creazione, modifica o eliminazione di queste chiavi o segreti.
  * Firma, verifica, crittografia, decrittografia, wrapping e annullamento del wrapping delle chiavi, recupero di segreti ed elenco di chiavi e segreti (e delle relative versioni).
* Richieste non autenticate che generano una risposta 401. Alcuni esempi sono le richieste che non hanno un token di connessione, hanno un formato non valido, sono scadute o hanno un token non valido.  
* Eventi di notifica di modifica di Griglia di eventi per i criteri di accesso all'insieme di credenziali delle chiavi vicini alla scadenza o scaduti (l'evento su nuove versioni non viene registrato). Gli eventi vengono registrati indipendentemente dalla disponibilità di una sottoscrizione di eventi creata nell'insieme di credenziali delle chiavi. Per altre informazioni, vedere [Schema di eventi di Griglia di eventi per Key Vault](../../event-grid/event-schema-key-vault.md)

## <a name="access-your-logs"></a>Accedere ai log

Key Vault log vengono archiviati nel contenitore "Insights-logs-AuditEvent" nell'account di archiviazione specificato. Per visualizzare i log, è necessario scaricare i BLOB.

Per prima cosa, elencare tutti i BLOB nel contenitore.  Con l'interfaccia della riga di comando di Azure usare il comando [AZ storage BLOB list](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_list) .

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

Con Azure PowerShell, usare l'elenco [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) per tutti i BLOB in questo contenitore, immettere:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Come si vedrà dall'output del comando dell'interfaccia della riga di comando di Azure o dal cmdlet di Azure PowerShell, il nome dei BLOB è nel formato `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json` . I valori di data e ora sono nel formato UTC.

Poiché è possibile usare lo stesso account di archiviazione per raccogliere i log per più risorse, l'ID completo della risorsa nel nome del BLOB è utile per accedere solo ai BLOB necessari o per scaricarli. Prima di procedere, si vedrà però come scaricare tutti i BLOB.

Con l'interfaccia della riga di comando di Azure, usare il comando [AZ storage BLOB download](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_download) , passare i nomi dei BLOB e il percorso del file in cui si desidera salvare i risultati.

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

Con Azure PowerShell, usare il cmdlet [gt-AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) per ottenere un elenco di BLOB, quindi inviarlo tramite pipe al cmdlet [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent?view=azps-4.7.0) per scaricare i log nel percorso scelto.

```powershell-interactive
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

Quando si esegue questo secondo cmdlet in PowerShell, il **/** delimitatore nei nomi dei BLOB crea una struttura di cartelle completa nella cartella di destinazione. Questa struttura verrà usata per scaricare e archiviare i BLOB come file.

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

Per informazioni dettagliate su come leggere i log, vedere [Key Vault registrazione: interpretare i log di Key Vault](logging.md#interpret-your-key-vault-logs)

## <a name="use-azure-monitor-logs"></a>Usare i log di Monitoraggio di Azure

È possibile usare la soluzione Key Vault nei log di Monitoraggio di Azure per esaminare i log `AuditEvent` di Key Vault. Nei log di Monitoraggio di Azure è possibile usare le query di log per analizzare i dati e ottenere le informazioni necessarie.

Per altre informazioni, anche su come configurare la soluzione, vedere [Azure Key Vault nei log di Monitoraggio di Azure](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni concettuali, incluse le modalità di interpretazione dei log Key Vault, vedere [Key Vault registrazione](logging.md)
- Per un'esercitazione sull'uso di Azure Key Vault in un'applicazione Web .NET, vedere [Usare Azure Key Vault da un'app Web](tutorial-net-create-vault-azure-web-app.md).
- Per i riferimenti alla programmazione, vedere [Guida per gli sviluppatori dell'insieme di credenziali chiave Azure](developers-guide.md).