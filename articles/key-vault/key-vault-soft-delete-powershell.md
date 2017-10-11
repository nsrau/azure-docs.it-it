---
ms.assetid: 
title: "Azure Key Vault - Come usare la funzionalità di eliminazione temporanea con PowerShell"
description: "Esempi di casi d'uso della funzionalità di eliminazione temporanea con frammenti di codice di PowerShell"
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/21/2017
ms.author: bruceper
ms.openlocfilehash: 8cf0674f7eb139e50da4a3c22a8d8376a86b0dcc
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Come usare l'eliminazione temporanea di Key Vault con PowerShell

La funzionalità di eliminazione temporanea di Azure Key Vault consente il recupero di insiemi di credenziali e di oggetti di insiemi di credenziali eliminati. In particolare, l'eliminazione temporanea viene applicata negli scenari seguenti:

- Supporto per l'eliminazione reversibile di un insieme di credenziali delle chiavi
- Supporto per l'eliminazione reversibile di oggetti di insiemi di credenziali delle chiavi: chiavi, segreti e certificati

## <a name="prerequisites"></a>Prerequisiti

- Azure PowerShell 4.0.0 o versione successiva - Se non è già installato, installare Azure PowerShell e associarlo alla sottoscrizione di Azure. A questo proposito, vedere [Come installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> È **possibile** che nell'ambiente venga caricata una versione obsoleta del file di formattazione dell'output di PowerShell per Azure Key Vault, anziché la versione corretta. È prevista una versione aggiornata di PowerShell contenente la correzione necessaria per la formattazione dell'output, al cui rilascio questo argomento verrà aggiornato. Di seguito è indicata la soluzione alternativa corrente, qualora si riscontri questo problema di formattazione:
> - Usare la query seguente se non viene visualizzata la proprietà abilitata per l'eliminazione temporanea descritta in questo argomento: `$vault = Get-AzureRmKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Per informazioni sui comandi di PowerShell relativi a Key Vault, vedere [Azure Key Vault PowerShell reference](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0) (Documentazione su PowerShell per Azure Key Vault).

## <a name="required-permissions"></a>Autorizzazioni necessarie

Le operazioni di Key Vault vengono gestite separatamente tramite autorizzazioni del controllo degli accessi in base al ruolo, come indicato di seguito:

| Operazione | Descrizione | Autorizzazione utente |
|:--|:--|:--|
|Elenco|Elenca gli insiemi di credenziali delle chiavi eliminati.|Microsoft.KeyVault/deletedVaults/read|
|Recupera|Recupera un insieme di credenziali delle chiavi eliminato.|Microsoft.KeyVault/vaults/write|
|Ripulisci|Rimuove in modo permanente un insieme di credenziali delle chiavi eliminato e tutti i relativi contenuti.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Per altre informazioni sulle autorizzazioni e il controllo degli accessi, vedere [Proteggere l'insieme di credenziali delle chiavi](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Abilitazione della funzione di eliminazione temporanea

Per poter recuperare un insieme di credenziali delle chiavi eliminato o oggetti archiviati in un insieme di credenziali delle chiavi eliminato, è necessario prima abilitare la funzione di eliminazione temporanea per l'insieme di credenziali.

### <a name="existing-key-vault"></a>Insieme di credenziali delle chiavi esistente

Per un insieme di credenziali delle chiavi esistente denominato ContosoVault, è possibile abilitare l'eliminazione temporanea come indicato di seguito. 

>[!NOTE]
>Attualmente è necessario usare la funzione di modifica delle risorse di Azure Resource Manager per scrivere direttamente la proprietà *enableSoftDelete* nella risorsa di Key Vault.

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Insieme di credenziali delle chiavi nuovo

È possibile abilitare la funzione di eliminazione temporanea per un nuovo insieme di credenziali delle chiavi al momento della creazione aggiungendo "soft-delete enable" al comando di creazione.

```powershell
New-AzureRmKeyVault -VaultName "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Verificare l'abilitazione della funzione di eliminazione temporanea

Per verificare che in un insieme di credenziali delle chiavi sia abilitata la funzione di eliminazione temporanea, eseguire il comando *get* e controllare se l'attributo "Soft Delete Enabled?" ("Eliminazione temporanea abilitata?") è impostato su true o false.

```powershell
Get-AzureRmKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Eliminazione di un insieme di credenziali delle chiavi protetto dalla funzione di eliminazione temporanea

Il comando per eliminare (o rimuovere) un insieme di credenziali delle chiavi rimane invariato, ma il comportamento cambia a seconda che la funzione di eliminazione temporanea sia abilitata o meno.

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoVault'
```

> [!IMPORTANT]
>Se si esegue il comando precedente per un insieme di credenziali delle chiavi in cui la funzione di eliminazione temporanea non è abilitata, l'insieme di credenziali delle chiavi e tutti i relativi contenuti verranno eliminati in modo permanente, senza possibilità di recupero.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Come la funzione di eliminazione temporanea protegge gli insiemi di credenziali delle chiavi

Con la funzione di eliminazione temporanea abilitata:

- Quando un insieme di credenziali delle chiavi viene eliminato, l'insieme viene rimosso dal relativo gruppo di risorse e inserito in uno spazio dei nomi riservato associato solo al percorso in cui è stato creato. 
- Gli oggetti inclusi in un insieme di credenziali delle chiavi, come chiavi, segreti e certificati, risultano non accessibili per tutto il tempo in cui l'insieme di credenziali delle chiavi in cui sono contenuti è in stato "eliminato". 
- Il nome DNS relativo a un insieme di credenziali delle chiavi in stato "eliminato" rimane riservato e non è quindi possibile creare un insieme di credenziali delle chiavi con lo stesso nome.  

È possibile visualizzare gli insiemi di credenziali delle chiavi in stato "eliminato" associati alla propria sottoscrizione usando il comando seguente:

```powershell
PS C:\> Get-AzureRmKeyVault -InRemovedStateVault 

Name           : ContosoVault
Location             : westus
Id                   : /subscriptions/xxx/providers/Microsoft.KeyVault/locations/westus/deletedVaults/ContosoVault
Resource ID          : /subscriptions/xxx/resourceGroups/ContosoVault/providers/Microsoft.KeyVault/vaults/ContosoVault
Deletion Date        : 5/9/2017 12:14:14 AM
Scheduled Purge Date : 8/7/2017 12:14:14 AM
Tags                 :
```

Il valore *ID risorsa* nell'output fa riferimento all'ID risorsa originale di questo insieme di credenziali. Poiché l'insieme di credenziali delle chiavi è ora in stato "eliminato", non è presente alcuna risorsa con questo ID. Il campo *Id* può essere usato per identificare la risorsa durante il recupero o l'eliminazione definitiva. Il campo *Scheduled Purge Date* (Data eliminazione definitiva programmata) indica il momento in cui l'insieme di credenziali delle chiavi verrà eliminato in modo definitivo se sull'insieme di credenziali non viene eseguita alcuna operazione. Il periodo di memorizzazione predefinito usato per calcolare il campo *Scheduled Purge Date* (Data eliminazione definitiva programmata) è di 90 giorni.

## <a name="recovering-a-key-vault"></a>Recupero di un insieme di credenziali delle chiavi

Per recuperare un insieme di credenziali delle chiavi, è necessario specificarne il nome, il gruppo di risorse e il percorso. Annotare il percorso e il gruppo di risorse dell'insieme di credenziali delle chiavi eliminato, poiché saranno necessari nel processo di recupero dell'insieme di credenziali.

```powershell
Undo-AzureRmKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Quando viene recuperato un insieme di credenziali delle chiavi, il risultato è una nuova risorsa con l'ID risorsa originale dell'insieme di credenziali delle chiavi. Se il gruppo di risorse in cui si trova l'insieme di credenziali delle chiavi è stato rimosso, prima di poter recuperare l'insieme di credenziali delle chiavi è necessario creare un nuovo gruppo di risorse con lo stesso nome.

## <a name="key-vault-objects-and-soft-delete"></a>Oggetti di Key Vault ed eliminazione temporanea

Per eliminare in modo definitivo una chiave "ContosoFirstKey" in un insieme di credenziali delle chiavi denominato "ContosoVault" con la funzione di eliminazione temporanea abilitata, seguire l'esempio seguente.

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Se in un insieme di credenziali delle chiavi è abilitata la funzione di eliminazione temporanea, una chiave eliminata continua a essere visualizzata come eliminata a meno che non venga elencata o recuperata in modo esplicito. La maggior parte delle operazioni eseguite su una chiave in stato "eliminato" ha esito negativo. Una chiave eliminata, infatti, può essere solo elencata, recuperata o eliminata in modo definitivo. 

Per richiedere di compilare un elenco delle chiavi eliminate in un insieme di credenziali delle chiavi, usare il comando seguente:

```powershell
Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Stato di transizione 

Quando si elimina una chiave in un insieme di credenziali delle chiavi con eliminazione temporanea abilitata, per completare la transizione possono essere necessari alcuni secondi. Durante questo stato di transizione, è possibile che la chiave non risulti in stato attivo o in stato eliminato. Questo comando elencherà tutte le chiavi eliminate nell'insieme di credenziali delle chiavi denominato "ContosoVault".

```powershell
  Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
  Vault Name           : ContosoVault
  Name                 : ContosoFirstKey
  Id                   : https://ContosoVault.vault.azure.net:443/keys/ContosoFirstKey
  Deleted Date         : 2/14/2017 8:20:52 PM
  Scheduled Purge Date : 5/15/2017 8:20:52 PM
  Enabled              : True
  Expires              :
  Not Before           :
  Created              : 2/14/2017 8:16:07 PM
  Updated              : 2/14/2017 8:16:07 PM
  Tags                 :
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Utilizzo della funzione di eliminazione temporanea con gli oggetti di un insieme di credenziali delle chiavi

Analogamente agli insiemi di credenziali delle chiavi, anche una chiave, un segreto o un certificato eliminato rimarrà in stato "eliminato" per un massimo di 90 giorni a meno che non si scelga di recuperarlo o eliminarlo in modo definitivo. 

#### <a name="keys"></a>Chiavi

Per recuperare una chiave eliminata:

```powershell
Undo-AzureKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Per eliminare in modo definitivo una chiave:

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

>[!NOTE]
>Dopo essere stata eliminata in modo definitivo, una chiave non è più recuperabile.

Alle azioni di **recupero** ed **eliminazione definitiva** sono associate autorizzazioni specifiche nei criteri di accesso dell'insieme di credenziali delle chiavi. Per poter eseguire un'azione di **recupero** o **eliminazione definitiva**, quindi, un utente o un'entità servizio deve avere la relativa autorizzazione sull'oggetto (chiave o segreto) nell'ambito dei criteri di accesso dell'insieme di credenziali delle chiavi. Per impostazione predefinita, l'autorizzazione di **eliminazione definitiva** non viene aggiunta ai criteri di accesso dell'insieme di credenziali delle chiavi se viene usato il collegamento "all" per concedere tutte le autorizzazioni a un utente. L'autorizzazione di **eliminazione definitiva** deve essere concessa esplicitamente. Il comando seguente, ad esempio, concede l'autorizzazione user@contoso.com per eseguire varie operazioni sulle chiavi nell'insieme *ContosoVault*, tra cui l'**eliminazione definitiva**.

#### <a name="set-a-key-vault-access-policy"></a>Configurare i criteri di accesso dell'insieme di credenziali delle chiavi

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Se si ha un insieme di credenziali delle chiavi in cui è stata appena abilitata la funzione di eliminazione temporanea, è possibile che non si disponga delle autorizzazioni di **recupero** ed **eliminazione definitiva**.

#### <a name="secrets"></a>Segreti

Come per le chiavi, è possibile eseguire operazioni sui segreti presenti in un insieme di credenziali delle chiavi usando i relativi comandi. Di seguito sono elencati i comandi per eliminare, elencare, recuperare ed eliminare in modo definitivo i segreti.

- Eliminare un segreto denominato SQLPassword: 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- Elencare tutti i segreti eliminati in un insieme di credenziali delle chiavi: 
```powershell
Get-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState
```

- Ripristinare un segreto in stato "eliminato": 
```powershell
Undo-AzureKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
```

- Eliminare in modo definitivo un segreto in stato "eliminato": 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
```

>[!NOTE]
>Dopo essere stato eliminato in modo definitivo, un segreto non è più recuperabile.

## <a name="purging-and-key-vaults"></a>Eliminazione definitiva e insiemi di credenziali delle chiavi

### <a name="key-vault-objects"></a>Oggetti nell'insieme di credenziali delle chiavi

Dopo essere stato eliminato in modo definitivo, una chiave, un segreto o un certificato non è più recuperabile. L'insieme di credenziali delle chiavi che conteneva l'oggetto eliminato rimarrà tuttavia invariato, così come qualsiasi altro oggetto presente nell'insieme di credenziali delle chiavi. 

### <a name="key-vaults-as-containers"></a>Insiemi di credenziali delle chiavi come contenitori
Quando viene eliminato un insieme di credenziali delle chiavi, vengono rimossi in modo permanente anche tutti i relativi contenuti, tra cui certificati, chiavi e segreti. Per eliminare in modo definitivo un insieme di credenziali delle chiavi, usare il comando `Remove-AzureRmKeyVault` con l'opzione `-InRemovedState` e specificare il percorso dell'insieme di credenziali delle chiavi eliminato con l'argomento `-Location location`. È possibile trovare il percorso di un insieme di credenziali delle chiavi eliminato usando il comando `Get-AzureRmKeyVault -InRemovedState`.

```powershell
Remove-AzureRmKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

>[!NOTE]
>Dopo essere stato eliminato in modo definitivo, un insieme di credenziali delle chiavi non è più recuperabile.

### <a name="purge-permissions-required"></a>Autorizzazioni di eliminazione definitiva necessarie
- Per eliminare in modo definitivo un insieme di credenziali delle chiavi eliminato, così che l'insieme di credenziali e i relativi contenuti vengano rimossi in modo permanente, l'utente deve avere l'autorizzazione RBAC per eseguire un'operazione *Microsoft.KeyVault/locations/deletedVaults/purge/action*. 
- Per elencare la chiave eliminata in un insieme di credenziali delle chiavi, un utente deve avere l'autorizzazione RBAC per eseguire l'autorizzazione *Microsoft.KeyVault/deletedVaults/read*. 
- Per impostazione predefinita, solo un amministratore della sottoscrizione ha queste autorizzazioni. 

### <a name="scheduled-purge"></a>Eliminazione temporanea programmata

Quando si elencano gli oggetti di un insieme di credenziali eliminato, viene indicato anche quando ne è programmata l'eliminazione definitiva da Key Vault. Il campo *Scheduled Purge Date* (Data eliminazione definitiva programmata) indica il momento in cui un oggetto dell'insieme di credenziali delle chiavi verrà eliminato in modo definitivo se non viene eseguita alcuna operazione. Per impostazione predefinita, il periodo di memorizzazione di un oggetto di un insieme di credenziali delle chiavi eliminato è di 90 giorni.

>[!NOTE]
>Un oggetto di un insieme di credenziali delle chiavi eliminato in modo permanente, attivato dal campo *Scheduled Purge Date* (Data eliminazione definitiva programmata), viene eliminato in modo definitivo e non è più recuperabile.

## <a name="other-resources"></a>Altre risorse:

- Per una panoramica della funzionalità di eliminazione temporanea di Key Vault, vedere [Panoramica della funzionalità di eliminazione temporanea di Azure Key Vault](key-vault-ovw-soft-delete.md).
- Per una panoramica generale dell'utilizzo di Azure Key Vault, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](key-vault-get-started.md).

