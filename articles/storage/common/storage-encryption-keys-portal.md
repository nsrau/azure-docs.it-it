---
title: Configurare chiavi gestite dal cliente con Azure Key Vault usando il portale di Azure-archiviazione di Azure
description: Informazioni su come usare la portale di Azure per configurare le chiavi gestite dal cliente con Azure Key Vault per la crittografia di archiviazione di Azure. Le chiavi gestite dal cliente consentono di creare, ruotare, disabilitare e revocare i controlli di accesso.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b72f30a636d0177edb8b3254d14be941b2d4e6bc
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665909"
---
# <a name="configure-customer-managed-keys-for-azure-storage-by-using-the-azure-portal"></a>Configurare le chiavi gestite dal cliente per archiviazione di Azure usando il portale di Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Questo articolo illustra come configurare un Azure Key Vault con chiavi gestite dal cliente usando il [portale di Azure](https://portal.azure.com/). Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [Guida introduttiva: impostare e recuperare un segreto da Azure Key Vault tramite il portale di Azure](../../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> L'uso delle chiavi gestite dal cliente con la crittografia di archiviazione di Azure richiede l'impostazione di due proprietà nell'insieme di credenziali delle chiavi, l' **eliminazione** **temporanea e l'eliminazione.** Queste proprietà non sono abilitate per impostazione predefinita. Per abilitare queste proprietà, usare PowerShell o l'interfaccia della riga di comando di Azure.
> Sono supportate solo le chiavi RSA e le dimensioni della chiave 2048.

## <a name="enable-customer-managed-keys"></a>Abilita chiavi gestite dal cliente

Per abilitare le chiavi gestite dal cliente nel portale di Azure, attenersi alla procedura seguente:

1. Passare all'account di archiviazione.
1. Nel pannello **Impostazioni** relativo all'account di archiviazione fare clic su **Crittografia**. Selezionare l'opzione **Usare una chiave personalizzata**, come illustrato nella figura seguente.

    ![Screenshot del portale che mostra l'opzione di crittografia](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Specificare una chiave

Dopo aver abilitato le chiavi gestite dal cliente, si avrà la possibilità di specificare una chiave da associare all'account di archiviazione.

### <a name="specify-a-key-as-a-uri"></a>Specificare una chiave da un URI

Per specificare una chiave come URI, attenersi alla procedura seguente:

1. Per individuare l'URI della chiave nella portale di Azure, passare all'insieme di credenziali delle chiavi e selezionare l'impostazione **chiavi** . Selezionare la chiave desiderata, quindi fare clic sulla chiave per visualizzarne le impostazioni. Copiare il valore del campo **identificatore chiave** , che fornisce l'URI.

    ![Screenshot che mostra l'URI della chiave di Key Vault](media/storage-encryption-keys-portal/key-uri-portal.png)

1. Nelle impostazioni di **crittografia** per l'account di archiviazione scegliere l'opzione **immettere l'URI del tasto** .
1. Nel campo **URI della chiave** specificare l'URI.

   ![Screenshot che illustra come immettere l'URI della chiave](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Specificare una chiave da un insieme di credenziali delle chiavi

Per specificare una chiave da un insieme di credenziali delle chiavi, assicurarsi innanzitutto di avere un insieme di credenziali delle chiavi contenente una chiave. Per specificare una chiave da un insieme di credenziali delle chiavi, seguire questa procedura:

1. Scegliere l'opzione **Selezionare la chiave dall'insieme di credenziali delle chiavi**.
2. Scegliere l'insieme di credenziali delle chiavi contenente la chiave da usare.
3. Scegliere la chiave dall'insieme di credenziali delle chiavi.

   ![Screenshot che mostra l'opzione della chiave gestita dal cliente](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, è necessario aggiornare l'account di archiviazione per usare la nuova versione. Seguire questa procedura:

1. Passare all'account di archiviazione e visualizzare le impostazioni di **crittografia** .
1. Specificare l'URI per la nuova versione della chiave. In alternativa, è possibile selezionare di nuovo l'insieme di credenziali delle chiavi e la chiave per aggiornare la versione.

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
