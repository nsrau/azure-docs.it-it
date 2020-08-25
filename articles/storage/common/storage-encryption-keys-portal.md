---
title: Usare il portale di Azure per configurare le chiavi gestite dal cliente
titleSuffix: Azure Storage
description: Informazioni su come usare la portale di Azure per configurare le chiavi gestite dal cliente con Azure Key Vault per la crittografia di archiviazione di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/31/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a506f59d3f2d331e4c7680565f3c110b9cd12956
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799171"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configurare le chiavi gestite dal cliente con Azure Key Vault tramite il portale di Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Questo articolo illustra come configurare un Azure Key Vault con chiavi gestite dal cliente usando il [portale di Azure](https://portal.azure.com/). Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [Guida introduttiva: impostare e recuperare un segreto da Azure Key Vault tramite il portale di Azure](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Configurare Azure Key Vault

L'uso delle chiavi gestite dal cliente con la crittografia di archiviazione di Azure richiede l'impostazione di due proprietà nell'insieme di credenziali delle chiavi, l' **eliminazione** **temporanea e l'eliminazione.** Queste proprietà non sono abilitate per impostazione predefinita, ma possono essere abilitate tramite PowerShell o l'interfaccia della riga di comando di Azure in un insieme di credenziali delle chiavi nuovo o esistente.

Per informazioni su come abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, vedere le sezioni **Abilitazione dell'eliminazione temporanea** e **Abilitazione della protezione dall'eliminazione** in uno degli articoli seguenti:

- [Come usare l'eliminazione temporanea con PowerShell](../../key-vault/general/soft-delete-powershell.md).
- [Come usare l'eliminazione temporanea con l'interfaccia](../../key-vault/general/soft-delete-cli.md)della riga di comando.

La crittografia di archiviazione di Azure supporta chiavi RSA e RSA-HSM di dimensioni 2048, 3072 e 4096. Per ulteriori informazioni sulle chiavi, vedere **Key Vault chiavi** in [informazioni su Azure Key Vault chiavi, segreti e certificati](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Abilitare chiavi gestite dal cliente

Per abilitare le chiavi gestite dal cliente nel portale di Azure, attenersi alla procedura seguente:

1. Passare all'account di archiviazione.
1. Nel pannello **Impostazioni** relativo all'account di archiviazione fare clic su **Crittografia**. Selezionare l'opzione **chiavi gestite dal cliente** , come illustrato nella figura seguente.

    ![Screenshot del portale che mostra l'opzione di crittografia](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Specificare una chiave

Dopo aver abilitato le chiavi gestite dal cliente, si avrà la possibilità di specificare una chiave da associare all'account di archiviazione. È anche possibile indicare se archiviazione di Azure deve aggiornare automaticamente la chiave gestita dal cliente alla versione più recente o se la versione della chiave verrà aggiornata manualmente.

### <a name="specify-a-key-from-a-key-vault"></a>Specificare una chiave da un insieme di credenziali delle chiavi

Quando si seleziona una chiave gestita dal cliente da un insieme di credenziali delle chiavi, è abilitato l'aggiornamento automatico della versione della chiave. Per gestire manualmente la versione della chiave, specificare invece l'URI della chiave e includere la versione della chiave. Per altre informazioni, vedere [specificare una chiave come URI](#specify-a-key-as-a-uri).

Per specificare una chiave da un insieme di credenziali delle chiavi, seguire questa procedura:

1. Scegliere l'opzione **Selezionare la chiave dall'insieme di credenziali delle chiavi**.
1. Selezionare **selezionare un insieme di credenziali delle chiavi e una chiave**.
1. Selezionare l'insieme di credenziali delle chiavi contenente la chiave che si vuole usare.
1. Selezionare la chiave dall'insieme di credenziali delle chiavi.

   ![Screenshot che illustra come selezionare Key Vault e Key](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Salvare le modifiche.

### <a name="specify-a-key-as-a-uri"></a>Specificare una chiave da un URI

Archiviazione di Azure può aggiornare automaticamente la chiave gestita dal cliente usata per la crittografia per usare la versione più recente della chiave. Quando la chiave gestita dal cliente viene ruotata in Azure Key Vault, archiviazione di Azure inizierà automaticamente a usare la versione più recente della chiave per la crittografia.

> [!NOTE]
> Per ruotare una chiave, creare una nuova versione della chiave in Azure Key Vault. Archiviazione di Azure non gestisce la rotazione della chiave in Azure Key Vault, pertanto sarà necessario ruotare manualmente la chiave o creare una funzione per ruotarla in base a una pianificazione.

Quando si specifica l'URI della chiave, omettere la versione della chiave dall'URI per abilitare l'aggiornamento automatico alla versione più recente della chiave. Se si include la versione della chiave nell'URI della chiave, l'aggiornamento automatico non è abilitato ed è necessario gestire manualmente la versione della chiave. Per ulteriori informazioni sull'aggiornamento della versione della chiave, vedere [aggiornamento manuale della versione della chiave](#manually-update-the-key-version).

Per specificare una chiave come URI, attenersi alla procedura seguente:

1. Per individuare l'URI della chiave nella portale di Azure, passare all'insieme di credenziali delle chiavi e selezionare l'impostazione **chiavi** . Selezionare la chiave desiderata, quindi fare clic sulla chiave per visualizzarne le versioni. Selezionare una versione della chiave per visualizzare le impostazioni per tale versione.
1. Copiare il valore del campo **identificatore chiave** , che fornisce l'URI.

    ![Screenshot che mostra l'URI della chiave di Key Vault](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. Nelle impostazioni della **chiave di crittografia** per l'account di archiviazione scegliere l'opzione immettere l'URI del **tasto** .
1. Incollare l'URI copiato nel campo **URI chiave** . Omettere la versione della chiave dall'URI per abilitare l'aggiornamento automatico della versione della chiave.

   ![Screenshot che illustra come immettere l'URI della chiave](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Specificare la sottoscrizione che contiene l'insieme di credenziali delle chiavi.
1. Salvare le modifiche.

Dopo aver specificato la chiave, il portale di Azure indica se l'aggiornamento automatico della versione della chiave è abilitato e visualizza la versione della chiave attualmente in uso per la crittografia.

:::image type="content" source="media/storage-encryption-keys-portal/portal-auto-rotation-enabled.png" alt-text="Screenshot che illustra l'aggiornamento automatico della versione della chiave abilitata":::

## <a name="manually-update-the-key-version"></a>Aggiornare manualmente la versione della chiave

Per impostazione predefinita, quando si crea una nuova versione di una chiave gestita dal cliente in Key Vault, archiviazione di Azure usa automaticamente la nuova versione per la crittografia con chiavi gestite dal cliente, come descritto nelle sezioni precedenti. Se si sceglie di gestire manualmente la versione della chiave, è necessario aggiornare la versione della chiave associata all'account di archiviazione ogni volta che si crea una nuova versione della chiave.

Per aggiornare l'account di archiviazione per usare la nuova versione della chiave, seguire questa procedura:

1. Passare all'account di archiviazione e visualizzare le impostazioni di **crittografia** .
1. Immettere l'URI per la nuova versione della chiave. In alternativa, è possibile selezionare di nuovo l'insieme di credenziali delle chiavi e la chiave per aggiornare la versione.
1. Salvare le modifiche.

## <a name="switch-to-a-different-key"></a>Passa a una chiave diversa

Per modificare la chiave usata per la crittografia di archiviazione di Azure, seguire questa procedura:

1. Passare all'account di archiviazione e visualizzare le impostazioni di **crittografia** .
1. Immettere l'URI della nuova chiave. In alternativa, è possibile selezionare l'insieme di credenziali delle chiavi e scegliere una nuova chiave.
1. Salvare le modifiche.

## <a name="disable-customer-managed-keys"></a>Disabilitare le chiavi gestite dal cliente

Quando si disabilitano le chiavi gestite dal cliente, l'account di archiviazione viene nuovamente crittografato con le chiavi gestite da Microsoft. Per disabilitare le chiavi gestite dal cliente, attenersi alla seguente procedura:

1. Passare all'account di archiviazione e visualizzare le impostazioni di **crittografia** .
1. Deselezionare la casella di controllo accanto all'impostazione **Usa una chiave personalizzata** .

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
