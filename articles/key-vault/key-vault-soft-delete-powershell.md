---
title: Azure Key Vault - Come usare la funzionalità di eliminazione temporanea con PowerShell
description: Esempi di casi d'uso della funzionalità di eliminazione temporanea con frammenti di codice di PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 6a24f2dd52c3ac3c51df54bf5c01c7b31ca16147
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985763"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Come usare l'eliminazione temporanea di Key Vault con PowerShell

La funzionalità di eliminazione temporanea di Azure Key Vault consente il recupero di insiemi di credenziali e di oggetti di insiemi di credenziali eliminati. In particolare, l'eliminazione temporanea viene applicata negli scenari seguenti:

- Supporto per l'eliminazione reversibile di un insieme di credenziali delle chiavi
- Supporto per l'eliminazione reversibile di oggetti di insiemi di credenziali delle chiavi: chiavi, segreti e certificati

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 o versione successiva - Se non è già installato, installare Azure PowerShell e associarlo alla sottoscrizione di Azure. A questo proposito, vedere [Come installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> È **possibile** che nell'ambiente venga caricata una versione obsoleta del file di formattazione dell'output di PowerShell per Azure Key Vault, anziché la versione corretta. È prevista una versione aggiornata di PowerShell contenente la correzione necessaria per la formattazione dell'output, al cui rilascio questo argomento verrà aggiornato. Di seguito è indicata la soluzione alternativa corrente, qualora si riscontri questo problema di formattazione:
> - Usare la query seguente se non viene visualizzata la proprietà abilitata per l'eliminazione temporanea descritta in questo argomento: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Per informazioni di riferimento specifiche di Key Vault per PowerShell, consultare la [documentazione su PowerShell per Azure Key Vault](/powershell/module/az.keyvault).

## <a name="required-permissions"></a>Autorizzazioni necessarie

Le operazioni di Key Vault vengono gestite separatamente tramite autorizzazioni del controllo degli accessi in base al ruolo, come indicato di seguito:

| Operazione | DESCRIZIONE | Autorizzazione utente |
|:--|:--|:--|
|Elenco|Elenca gli insiemi di credenziali delle chiavi eliminati.|Microsoft.KeyVault/deletedVaults/read|
|Recupera|Recupera un insieme di credenziali delle chiavi eliminato.|Microsoft.KeyVault/vaults/write|
|Ripulisci|Rimuove in modo permanente un insieme di credenziali delle chiavi eliminato e tutti i relativi contenuti.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Per altre informazioni sulle autorizzazioni e il controllo degli accessi, vedere [Proteggere l'insieme di credenziali delle chiavi](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Abilitazione della funzione di eliminazione temporanea

La funzione di eliminazione temporanea viene abilitata per consentire il recupero di un insieme di credenziali delle chiavi eliminato o degli oggetti archiviati in un insieme di credenziali delle chiavi.

> [!IMPORTANT]
> L'abilitazione dell'eliminazione temporanea per un insieme di credenziali delle chiavi è un'azione irreversibile. Dopo che la proprietà di eliminazione temporanea è stata impostata su "true", non può essere modificata o rimossa.  

### <a name="existing-key-vault"></a>Insieme di credenziali delle chiavi esistente

Per un insieme di credenziali delle chiavi esistente denominato ContosoVault, è possibile abilitare l'eliminazione temporanea come indicato di seguito. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Insieme di credenziali delle chiavi nuovo

È possibile abilitare la funzione di eliminazione temporanea per un nuovo insieme di credenziali delle chiavi al momento della creazione aggiungendo "soft-delete enable" al comando di creazione.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Verificare l'abilitazione della funzione di eliminazione temporanea

Per verificare che in un insieme di credenziali delle chiavi sia abilitata la funzione di eliminazione temporanea, eseguire il comando *show* e controllare se l'attributo "Soft Delete Enabled?" ("Eliminazione temporanea abilitata?") è presente:

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Eliminazione di un insieme di credenziali delle chiavi protetto dalla funzione di eliminazione temporanea

Il comportamento del comando per eliminare un insieme di credenziali delle chiavi è diverso a seconda che l'eliminazione temporanea sia abilitata o meno.

> [!IMPORTANT]
>Se si esegue il comando seguente per un insieme di credenziali delle chiavi in cui la funzione di eliminazione temporanea non è abilitata, l'insieme di credenziali delle chiavi e tutti i relativi contenuti verranno eliminati in modo permanente, senza possibilità di recupero.

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Come la funzione di eliminazione temporanea protegge gli insiemi di credenziali delle chiavi

Con la funzione di eliminazione temporanea abilitata:

- Un insieme di credenziali delle chiavi viene rimosso dal relativo gruppo di risorse e inserito in uno spazio dei nomi riservato associato al percorso in cui è stato creato. 
- Gli oggetti eliminati, come chiavi, segreti e certificati, risultano non accessibili finché l'insieme di credenziali delle chiavi in cui sono contenuti è in stato "eliminato". 
- Il nome DNS per un insieme di credenziali delle chiavi eliminato è riservato e non è quindi possibile creare un nuovo insieme di credenziali delle chiavi con lo stesso nome.  

È possibile visualizzare gli insiemi di credenziali delle chiavi in stato "eliminato" associati alla propria sottoscrizione usando il comando seguente:

```powershell
Get-AzKeyVault -InRemovedState 
```

- L'*ID* può essere usato per identificare la risorsa durante il recupero o l'eliminazione definitiva. 
- L'*ID risorsa* è l'ID risorsa originale di questo insieme di credenziali. Poiché l'insieme di credenziali delle chiavi è ora in stato "eliminato", non è presente alcuna risorsa con questo ID. 
- La *data di eliminazione definitiva programmata* indica il momento in cui l'insieme di credenziali verrà eliminato in modo definitivo se non viene eseguita alcuna operazione. Il periodo di memorizzazione predefinito usato per calcolare il campo *Scheduled Purge Date* (Data eliminazione definitiva programmata) è di 90 giorni.

## <a name="recovering-a-key-vault"></a>Recupero di un insieme di credenziali delle chiavi

Per recuperare un insieme di credenziali delle chiavi, si specifica il nome, il gruppo di risorse e il percorso. Annotare il percorso e il gruppo di risorse dell'insieme di credenziali delle chiavi eliminato, perché saranno necessari nel processo di recupero.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Quando viene recuperato un insieme di credenziali delle chiavi, viene creata una nuova risorsa con l'ID risorsa originale dell'insieme di credenziali delle chiavi. Se il gruppo di risorse originale viene rimosso, ne deve essere creato uno con lo stesso nome prima di tentare il recupero.

## <a name="deleting-and-purging-key-vault-objects"></a>Eliminazione e pulizia di oggetti di insiemi di credenziali delle chiavi

Il comando seguente elimina la chiave 'ContosoFirstKey' in un insieme di credenziali delle chiavi denominato 'ContosoVault' in cui è abilitata l'eliminazione temporanea:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Se in un insieme di credenziali delle chiavi è abilitata la funzione di eliminazione temporanea, una chiave eliminata continua a essere visualizzata come da eliminare, a meno che non vengano elencate in modo esplicito le chiavi eliminate. La maggior parte delle operazioni eseguite su una chiave con stato "eliminata" ha esito negativo, ad eccezione delle operazioni di elenco, recupero ed eliminazione definitiva. 

Il comando seguente, ad esempio, elenca le chiavi eliminate nell'insieme di credenziali delle chiavi 'ContosoVault':

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Stato di transizione 

Quando si elimina una chiave in un insieme di credenziali delle chiavi con eliminazione temporanea abilitata, per completare la transizione possono essere necessari alcuni secondi. Durante questa transizione, è possibile che in apparenza la chiave non risulti attiva o eliminata. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Utilizzo della funzione di eliminazione temporanea con gli oggetti di un insieme di credenziali delle chiavi

Analogamente agli insiemi di credenziali delle chiavi, anche una chiave, un segreto o un certificato eliminato rimane in stato "eliminato" per un massimo di 90 giorni, a meno che non si scelga di recuperarlo o eliminarlo in modo definitivo. 

#### <a name="keys"></a>Chiavi

Per recuperare una chiave eliminata temporaneamente:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Per eliminare definitivamente (ripulire) una chiave eliminata temporaneamente:

> [!IMPORTANT]
> Dopo essere stata eliminata in modo definitivo, una chiave non è più recuperabile. 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

Alle azioni di **recupero** ed **eliminazione definitiva** sono associate autorizzazioni specifiche nei criteri di accesso dell'insieme di credenziali delle chiavi. Per poter eseguire un'azione **recover** o **purge**, quindi, un utente o un'entità servizio deve avere la relativa autorizzazione per la chiave o il segreto. Per impostazione predefinita, **purge** non viene aggiunta ai criteri di accesso dell'insieme di credenziali delle chiavi se viene usato il collegamento "all" per concedere tutte le autorizzazioni. L'autorizzazione **purge** deve essere concessa esplicitamente. 

#### <a name="set-a-key-vault-access-policy"></a>Configurare i criteri di accesso dell'insieme di credenziali delle chiavi

Il comando seguente, concede a user@contoso.com l'autorizzazione per usare diverse operazioni sulle chiavi in *ContosoVault*, tra cui **purge**:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Se si ha un insieme di credenziali delle chiavi in cui è stata appena abilitata la funzione di eliminazione temporanea, è possibile che non si disponga delle autorizzazioni di **recupero** ed **eliminazione definitiva**.

#### <a name="secrets"></a>Segreti

Come le chiavi, i segreti vengono gestiti con comandi specifici:

- Eliminare un segreto denominato SQLPassword: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Elencare tutti i segreti eliminati in un insieme di credenziali delle chiavi: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Ripristinare un segreto in stato "eliminato": 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Eliminare in modo definitivo un segreto in stato "eliminato": 

  > [!IMPORTANT]
  > Dopo essere stato eliminato in modo definitivo, un segreto non è più recuperabile.

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>Pulizia di un insieme di credenziali delle chiavi protetto dalla funzione di eliminazione temporanea

> [!IMPORTANT]
> Dopo l'operazione di pulizia, un insieme di credenziali delle chiavi o un oggetto dell'insieme non è più recuperabile.

La funzione di pulizia viene usata per eliminare in modo permanente un oggetto dell'insieme di credenziali delle chiavi, o un intero insieme, che in precedenza è stato eliminato temporaneamente. Come illustrato nella sezione precedente, gli oggetti archiviati in un insieme di credenziali delle chiavi con la funzionalità di eliminazione temporanea abilitata, possono passare attraverso più stati:
- **Attivo**: prima dell'eliminazione.
- **Eliminato temporaneamente**: dopo l'eliminazione, può essere recuperato e ripristinato allo stato attivo.
- **Eliminato definitivamente**: dopo la pulizia, non può più essere recuperato.


Lo stesso vale per l'insieme di credenziali delle chiavi. Per eliminare in modo definitivo un insieme di credenziali delle chiavi eliminato temporaneamente e il relativo contenuto, è necessario ripulire l'intero insieme di credenziali delle chiavi.

### <a name="purging-a-key-vault"></a>Pulizia di un insieme di credenziali delle chiavi

Quando un insieme di credenziali delle chiavi viene ripulito, ne viene eliminato in modo definitivo l'intero contenuto, inclusi certificati, chiavi e segreti. Per ripulire un insieme di credenziali delle chiavi eliminato temporaneamente, usare il comando `Remove-AzKeyVault` con l'opzione `-InRemovedState` e specificare il percorso dell'insieme di credenziali delle chiavi eliminato con l'argomento `-Location location`. È possibile trovare il percorso di un insieme di credenziali delle chiavi eliminato usando il comando `Get-AzKeyVault -InRemovedState`.

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Autorizzazioni di eliminazione definitiva necessarie
- Per ripulire un insieme di credenziali delle chiavi eliminato, l'utente necessita dell'autorizzazione di Controllo degli accessi in base al ruolo per l'operazione *Microsoft.KeyVault/locations/deletedVaults/purge/action*. 
- Per elencare un insieme di credenziali delle chiavi eliminato, l'utente necessita dell'autorizzazione di Controllo degli accessi in base al ruolo per l'operazione *Microsoft.KeyVault/deletedVaults/read*. 
- Per impostazione predefinita, solo un amministratore della sottoscrizione ha queste autorizzazioni. 

### <a name="scheduled-purge"></a>Eliminazione temporanea programmata

Quando si elencano gli oggetti di un insieme di credenziali eliminato, viene indicato anche quando ne è programmata l'eliminazione definitiva da Key Vault. La *data eliminazione definitiva programmata* indica il momento in cui un oggetto dell'insieme di credenziali delle chiavi verrà eliminato in modo definitivo se non viene eseguita alcuna operazione. Per impostazione predefinita, il periodo di memorizzazione di un oggetto di un insieme di credenziali delle chiavi eliminato è di 90 giorni.

>[!IMPORTANT]
>Un oggetto di un insieme di credenziali delle chiavi eliminato in modo permanente, attivato dal campo *Scheduled Purge Date* (Data eliminazione definitiva programmata), viene eliminato in modo definitivo e non è più recuperabile.

## <a name="enabling-purge-protection"></a>Abilitazione del flag di protezione dall'eliminazione

Quando la protezione dall'eliminazione è attivata, un insieme di credenziali o un oggetto nello stato eliminato non può essere ripulito finché non ha superato il periodo di conservazione di 90 giorni. Tale insieme di credenziali o oggetto può essere comunque ripristinato. Questa funzionalità offre maggiore sicurezza che un oggetto o un insieme di credenziali non venga mai eliminato definitivamente prima che sia trascorso il periodo di conservazione.

È possibile abilitare la protezione dall'eliminazionesolo se è abilitata anche l'eliminazione temporanea. 

Per attivare l'eliminazione temporanea e la protezione dall'eliminazione durante la creazione di un insieme di credenziali, usare il cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0):

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Per aggiungere la protezione dall'eliminazione a un insieme di credenziali esistente (per cui è già abilitata l'eliminazione temporanea), usare i cmdlet [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0) e [Set-AzResource](/powershell/module/az.resources/set-azresource?view=azps-1.5.0):

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Altre risorse:

- Per una panoramica della funzionalità di eliminazione temporanea di Key Vault, vedere [Panoramica della funzionalità di eliminazione temporanea di Azure Key Vault](key-vault-ovw-soft-delete.md).
- Per una panoramica generale dell'uso di Azure Key Vault, vedere [Cos'è Azure Key Vault?](key-vault-overview.md)
