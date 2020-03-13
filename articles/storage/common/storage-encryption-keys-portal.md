---
title: Usare il portale di Azure per configurare le chiavi gestite dal cliente
titleSuffix: Azure Storage
description: Informazioni su come usare la portale di Azure per configurare le chiavi gestite dal cliente con Azure Key Vault per la crittografia di archiviazione di Azure. Le chiavi gestite dal cliente consentono di creare, ruotare, disabilitare e revocare i controlli di accesso.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5f091341bee65d17b42e289bab1192142d095637
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136126"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configurare chiavi gestite dal cliente con Azure Key Vault usando il portale di Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Questo articolo illustra come configurare un Azure Key Vault con chiavi gestite dal cliente usando il [portale di Azure](https://portal.azure.com/). Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [Guida introduttiva: impostare e recuperare un segreto da Azure Key Vault tramite il portale di Azure](../../key-vault/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Configurare Azure Key Vault

L'uso delle chiavi gestite dal cliente con la crittografia di archiviazione di Azure richiede l'impostazione di due proprietà nell'insieme di credenziali delle chiavi, l' **eliminazione** **temporanea e l'eliminazione.** Queste proprietà non sono abilitate per impostazione predefinita, ma possono essere abilitate tramite PowerShell o l'interfaccia della riga di comando di Azure in un insieme di credenziali delle chiavi nuovo o esistente.

Per informazioni su come abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, vedere le sezioni intitolate **Abilitazione dell'eliminazione** temporanea e **Abilitazione della protezione di ripulitura** in uno degli articoli seguenti:

- [Come usare l'eliminazione temporanea con PowerShell](../../key-vault/key-vault-soft-delete-powershell.md).
- [Come usare l'eliminazione temporanea con l'interfaccia](../../key-vault/key-vault-soft-delete-cli.md)della riga di comando.

Con la crittografia di archiviazione di Azure sono supportate solo le chiavi RSA della dimensione 2048. Per ulteriori informazioni sulle chiavi, vedere **Key Vault chiavi** in [informazioni su Azure Key Vault chiavi, segreti e certificati](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Abilita chiavi gestite dal cliente

Per abilitare le chiavi gestite dal cliente nel portale di Azure, attenersi alla procedura seguente:

1. Passare all'account di archiviazione.
1. Nel pannello **Impostazioni** relativo all'account di archiviazione fare clic su **Crittografia**. Selezionare l'opzione **Usare una chiave personalizzata**, come illustrato nella figura seguente.

    ![Screenshot del portale che mostra l'opzione di crittografia](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Specificare una chiave

Dopo aver abilitato le chiavi gestite dal cliente, si avrà la possibilità di specificare una chiave da associare all'account di archiviazione.

### <a name="specify-a-key-as-a-uri"></a>Specificare una chiave da un URI

Per specificare una chiave come URI, attenersi alla procedura seguente:

1. Per individuare l'URI della chiave nella portale di Azure, passare all'insieme di credenziali delle chiavi e selezionare l'impostazione **chiavi** . Selezionare la chiave desiderata, quindi fare clic sulla chiave per visualizzarne le versioni. Selezionare una versione della chiave per visualizzare le impostazioni per tale versione.
1. Copiare il valore del campo **identificatore chiave** , che fornisce l'URI.

    ![Screenshot che mostra l'URI della chiave di Key Vault](media/storage-encryption-keys-portal/key-uri-portal.png)

1. Nelle impostazioni di **crittografia** per l'account di archiviazione scegliere l'opzione **immettere l'URI del tasto** .
1. Incollare l'URI copiato nel campo **URI chiave** .

   ![Screenshot che illustra come immettere l'URI della chiave](./media/storage-encryption-keys-portal/ssecmk2.png)

1. Specificare la sottoscrizione che contiene l'insieme di credenziali delle chiavi.
1. Salvare le modifiche.

### <a name="specify-a-key-from-a-key-vault"></a>Specificare una chiave da un insieme di credenziali delle chiavi

Per specificare una chiave da un insieme di credenziali delle chiavi, assicurarsi innanzitutto di avere un insieme di credenziali delle chiavi contenente una chiave. Per specificare una chiave da un insieme di credenziali delle chiavi, seguire questa procedura:

1. Scegliere l'opzione **Selezionare la chiave dall'insieme di credenziali delle chiavi**.
2. Selezionare l'insieme di credenziali delle chiavi contenente la chiave che si vuole usare.
3. Selezionare la chiave dall'insieme di credenziali delle chiavi.

   ![Screenshot che mostra l'opzione della chiave gestita dal cliente](./media/storage-encryption-keys-portal/ssecmk3.png)

1. Salvare le modifiche.

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, aggiornare l'account di archiviazione per usare la nuova versione. A tale scopo, seguire questa procedura:

1. Passare all'account di archiviazione e visualizzare le impostazioni di **crittografia** .
1. Immettere l'URI per la nuova versione della chiave. In alternativa, è possibile selezionare di nuovo l'insieme di credenziali delle chiavi e la chiave per aggiornare la versione.
1. Salvare le modifiche.

## <a name="use-a-different-key"></a>Usare una chiave diversa

Per modificare la chiave usata per la crittografia di archiviazione di Azure, seguire questa procedura:

1. Passare all'account di archiviazione e visualizzare le impostazioni di **crittografia** .
1. Immettere l'URI della nuova chiave. In alternativa, è possibile selezionare l'insieme di credenziali delle chiavi e scegliere una nuova chiave.
1. Salvare le modifiche.

## <a name="disable-customer-managed-keys"></a>Disabilitare le chiavi gestite dal cliente

Quando si disabilitano le chiavi gestite dal cliente, l'account di archiviazione viene nuovamente crittografato con le chiavi gestite da Microsoft. Per disabilitare le chiavi gestite dal cliente, attenersi alla seguente procedura:

1. Passare all'account di archiviazione e visualizzare le impostazioni di **crittografia** .
1. Deselezionare la casella di controllo accanto all'impostazione **Usa una chiave personalizzata** .

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
