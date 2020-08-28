---
title: Configurare chiavi gestite dal cliente per l'account Azure Batch con Azure Key Vault e identità gestite
description: Informazioni su come crittografare i dati in batch usando le chiavi
author: pkshultz
ms.topic: how-to
ms.date: 07/17/2020
ms.author: peshultz
ms.openlocfilehash: 35780f915247e88a5de093594b653ddcebdfb06b
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008880"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Configurare chiavi gestite dal cliente per l'account Azure Batch con Azure Key Vault e identità gestite

Per impostazione predefinita Azure Batch USA chiavi gestite dalla piattaforma per crittografare tutti i dati dei clienti archiviati nel servizio Azure Batch, ad esempio i certificati, i metadati di processi o attività. Facoltativamente, è possibile usare chiavi personalizzate, ovvero chiavi gestite dal cliente, per crittografare i dati archiviati in Azure Batch.

Le chiavi fornite devono essere generate in [Azure Key Vault](../key-vault/general/basic-concepts.md)e gli account batch che si vuole configurare con le chiavi gestite dal cliente devono essere abilitati con l' [identità gestita di Azure](../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Il supporto per le chiavi gestite dal cliente in Azure Batch è attualmente disponibile in anteprima pubblica per le aree Europa occidentale, Europa settentrionale, Svizzera settentrionale, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali 2, US Gov Virginia e US Gov Arizona.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Creare un account batch con identità gestita assegnata dal sistema

### <a name="azure-portal"></a>Portale di Azure

Nel [portale di Azure](https://portal.azure.com/), quando si creano gli account batch, scegliere **sistema assegnato** nel tipo di identità nella scheda **Avanzate** .

![Nuovo account batch con tipo di identità assegnato dal sistema](./media/batch-customer-managed-key/create-batch-account.png)

Dopo aver creato l'account, è possibile trovare un GUID univoco nel campo **ID entità identità** nella sezione **Proprietà** . Viene visualizzato il **tipo di identità** `SystemAssigned` .

![GUID univoco nel campo ID entità identità](./media/batch-customer-managed-key/linked-batch-principal.png)
 
### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Quando si crea un nuovo account batch, specificare `SystemAssigned` per il `--identity` parametro.

```powershell
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

Dopo aver creato l'account, è possibile verificare che l'identità gestita assegnata dal sistema sia stata abilitata in questo account. Tenere presente che `PrincipalId` , poiché questo valore sarà necessario per concedere a questo account batch l'accesso al Key Vault.

```powershell
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> L'identità gestita assegnata dal sistema creata in un account batch viene utilizzata solo per il recupero delle chiavi gestite dal cliente dal Key Vault. Questa identità non è disponibile nei pool di batch.

## <a name="configure-your-azure-key-vault-instance"></a>Configurare l'istanza di Azure Key Vault

### <a name="create-an-azure-key-vault"></a>Creare un Azure Key Vault

Quando si crea un'istanza di Azure Key Vault con chiavi gestite dal cliente per Azure Batch, assicurarsi che sia abilitata la protezione **eliminazione** temporanea e **ripulitura** .

![Schermata di creazione Key Vault](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Aggiungere un criterio di accesso all'istanza di Azure Key Vault

Nel portale di Azure, dopo la creazione del Key Vault, nell' **impostazione** **criteri di accesso** aggiungere l'accesso all'account batch usando identità gestita. In **autorizzazioni chiave**selezionare **Get**, **Wrap Key** e **Unwrap Key**. 

![Aggiungere un criterio di accesso](./media/batch-customer-managed-key/key-permissions.png)

Nel campo **Seleziona** in **principale**, compilare il `principalId` precedentemente recuperato o il nome dell'account batch.

![Immettere principalId](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Generare una chiave in Azure Key Vault

Nel portale di Azure passare all'istanza di Key Vault nella sezione **chiave** , selezionare **genera/importa**. Selezionare il **tipo di chiave** `RSA` e la **dimensione della chiave RSA** come almeno `2048` bit. `EC` i tipi di chiave non sono attualmente supportati come chiave gestita dal cliente in un account batch.

![Creare una chiave](./media/batch-customer-managed-key/create-key.png)

Dopo aver creato la chiave, fare clic sulla chiave appena creata e sulla versione corrente, copiare l' **identificatore di chiave** nella sezione **Proprietà** .  Assicurarsi che nelle **operazioni consentite**, la chiave a **capo** e la **chiave Unwrap** siano entrambe selezionate.

## <a name="enable-customer-managed-keys-on-azure-batch-account"></a>Abilitare le chiavi gestite dal cliente per l'account Azure Batch

### <a name="azure-portal"></a>Portale di Azure

Nella [portale di Azure](https://portal.azure.com/)passare alla pagina account batch. Nella sezione **crittografia** abilitare **chiave gestita dal cliente**. È possibile usare direttamente l'identificatore di chiave oppure è possibile selezionare l'insieme di credenziali delle chiavi e quindi fare clic su selezionare un insieme di credenziali delle **chiavi e una chiave**.

![In crittografia abilitare chiave gestita dal cliente](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Dopo che l'account batch è stato creato con l'identità gestita assegnata dal sistema e l'accesso a Key Vault è stato concesso, aggiornare l'account batch con l' `{Key Identifier}` URL in `keyVaultProperties` parametro. Impostare anche **encryption_key_source** come `Microsoft.KeyVault` .

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="update-the-customer-managed-key-version"></a>Aggiornare la versione della chiave gestita dal cliente

Quando si crea una nuova versione di una chiave, aggiornare l'account batch per usare la nuova versione. Seguire questa procedura:

1. Passare all'account batch in portale di Azure e visualizzare le impostazioni di crittografia.
2. Immettere l'URI per la nuova versione della chiave. In alternativa, è possibile selezionare di nuovo l'insieme di credenziali delle chiavi e la chiave per aggiornare la versione.
3. Salvare le modifiche.

È anche possibile usare l'interfaccia della riga di comando di Azure per aggiornare la versione.

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```
## <a name="use-a-different-key-for-batch-encryption"></a>Usare una chiave diversa per la crittografia batch

Per modificare la chiave usata per la crittografia batch, attenersi alla procedura seguente:

1. Passare all'account batch e visualizzare le impostazioni di crittografia.
2. Immettere l'URI della nuova chiave. In alternativa, è possibile selezionare l'insieme di credenziali delle chiavi e scegliere una nuova chiave.
3. Salvare le modifiche.

È anche possibile usare l'interfaccia della riga di comando di Azure per usare una chiave diversa.

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```
## <a name="frequently-asked-questions"></a>Domande frequenti
  * **Le chiavi gestite dal cliente sono supportate per gli account batch esistenti?** No. Le chiavi gestite dal cliente sono supportate solo per i nuovi account batch.
  * **È possibile selezionare dimensioni della chiave RSA maggiori di 2048 bit?** Sì, sono supportate anche le dimensioni delle chiavi RSA di `3072` e `4096` BITS.
  * **Quali operazioni sono disponibili dopo la revoca di una chiave gestita dal cliente?** L'unica operazione consentita è l'eliminazione dell'account se il batch perde l'accesso alla chiave gestita dal cliente.
  * **Come si ripristina l'accesso all'account batch se si elimina accidentalmente la chiave di Key Vault?** Poiché sono abilitate la protezione dall'eliminazione e l'eliminazione temporanea, è possibile ripristinare le chiavi esistenti. Per ulteriori informazioni, vedere la pagina relativa al [ripristino di un Azure Key Vault](../key-vault/general/soft-delete-cli.md#recovering-a-key-vault).
  * **È possibile disabilitare le chiavi gestite dal cliente?** È possibile impostare di nuovo il tipo di crittografia dell'account batch su "Microsoft Managed Key" in qualsiasi momento. Successivamente, è possibile eliminare o modificare la chiave.
  * **Come è possibile ruotare le chiavi?** Le chiavi gestite dal cliente non vengono ruotate automaticamente. Per ruotare la chiave, aggiornare l'identificatore di chiave a cui è associato l'account.
  * **Dopo il ripristino dell'accesso, quanto tempo sarà necessario per il corretto funzionamento dell'account batch?** Possono essere necessari fino a 10 minuti prima che l'account sia nuovamente accessibile dopo il ripristino dell'accesso.
  * **Mentre l'account batch non è disponibile, cosa accade alle risorse?** Tutti i pool in esecuzione quando l'accesso batch alle chiavi gestite dal cliente viene perso continuerà a essere eseguito. Tuttavia, i nodi passeranno a uno stato non disponibile e le attività smetteranno di funzionare e verranno riaccodate. Una volta ripristinato l'accesso, i nodi diventeranno nuovamente disponibili e le attività verranno riavviate.
  * **Questo meccanismo di crittografia si applica ai dischi delle macchine virtuali in un pool di batch?** No. Per i pool di configurazione dei servizi cloud, non viene applicata alcuna crittografia per il sistema operativo e il disco temporaneo. Per i pool di configurazione delle macchine virtuali, il sistema operativo e i dischi dati specificati verranno crittografati con una chiave gestita della piattaforma Microsoft per impostazione predefinita. Attualmente, non è possibile specificare la propria chiave per questi dischi. Per crittografare il disco temporaneo delle macchine virtuali per un pool di batch con una chiave gestita dalla piattaforma Microsoft, è necessario abilitare la proprietà [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) nel pool di [configurazione della macchina virtuale](/rest/api/batchservice/pool/add#virtualmachineconfiguration) . Per gli ambienti altamente sensibili, è consigliabile abilitare la crittografia del disco temporaneo ed evitare l'archiviazione di dati sensibili nei dischi del sistema operativo e dei dati. Per altre informazioni, vedere [creare un pool con crittografia del disco abilitata](./disk-encryption.md)
  * **L'identità gestita assegnata dal sistema per l'account batch è disponibile nei nodi di calcolo?** No. Questa identità gestita viene attualmente utilizzata solo per accedere alla Azure Key Vault per la chiave gestita dal cliente.
  
