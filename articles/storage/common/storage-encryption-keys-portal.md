---
title: Usare il portale di Azure per configurare le chiavi gestite dal clienteUse the Azure portal to configure customer-managed keys
titleSuffix: Azure Storage
description: Informazioni su come usare il portale di Azure per configurare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure per la crittografia di Archiviazione di Azure.Learn how to use the Azure portal to configure customer-managed keys with Azure Key Vault for Azure Storage encryption.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bef3b53a160f17248c1a26e97bc85a86843cb3c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061220"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configurare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure tramite il portale di AzureConfigure customer-managed keys with Azure Key Vault by using the Azure portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Questo articolo illustra come configurare un insieme di credenziali delle chiavi di Azure con chiavi gestite dal cliente tramite il portale di Azure.This article shows how to configure an Azure Key Vault with customer-managed keys using the [Azure portal](https://portal.azure.com/). Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [Guida introduttiva: Impostare e recuperare un segreto da Un](../../key-vault/quick-create-portal.md)insieme di credenziali delle chiavi di Azure usando il portale di Azure.To learn how to create a key vault using the Azure portal, see Quickstart: Set and retrieve a secret from Azure Key Vault using the Azure portal.

## <a name="configure-azure-key-vault"></a>Configurare Azure Key Vault

L'uso di chiavi gestite dal cliente con la crittografia di Archiviazione di Azure richiede l'impostazione di due proprietà nell'insieme di credenziali delle chiavi, **Eliminazione temporanea** e **Non eliminare**. Queste proprietà non sono abilitate per impostazione predefinita, ma possono essere abilitate usando PowerShell o l'interfaccia della riga di comando di Azure in un insieme di credenziali delle chiavi nuovo o esistente.

Per informazioni su come abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, vedere le sezioni relative **Abilitazione dell'eliminazione temporanea** e **dell'abilitazione** della protezione delle eliminazioni in uno degli articoli seguenti:

- [Come usare l'eliminazione temporanea con PowerShell](../../key-vault/key-vault-soft-delete-powershell.md).
- [Come utilizzare l'eliminazione temporanea con CLI](../../key-vault/key-vault-soft-delete-cli.md).

Solo le chiavi RSA di dimensioni 2048 sono supportate con la crittografia di Archiviazione di Azure.Only RSA keys of size 2048 are supported with Azure Storage encryption. Per altre informazioni sulle chiavi, vedere **Chiavi dell'insieme di credenziali delle chiavi** in Informazioni su chiavi, segreti e certificati dell'insieme di credenziali delle chiavi di [Azure.](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)

## <a name="enable-customer-managed-keys"></a>Abilitare le chiavi gestite dal clienteEnable customer-managed keys

Per abilitare le chiavi gestite dal cliente nel portale di Azure, eseguire la procedura seguente:To enable customer-managed keys in the Azure portal, follow these steps:

1. Passare all'account di archiviazione.
1. Nel pannello **Impostazioni** relativo all'account di archiviazione fare clic su **Crittografia**. Selezionare l'opzione **Chiavi gestite dal cliente,** come illustrato nell'immagine seguente.

    ![Schermata del portale che mostra l'opzione di crittografia](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Specificare una chiave

Dopo aver abilitato le chiavi gestite dal cliente, è possibile specificare una chiave da associare all'account di archiviazione.

### <a name="specify-a-key-as-a-uri"></a>Specificare una chiave da un URI

Per specificare una chiave come URI, attenersi alla seguente procedura:

1. Per individuare l'URI della chiave nel portale di Azure, passare all'insieme di credenziali delle chiavi e selezionare l'impostazione **Chiavi.To** locate the key URI in the Azure portal, navigate to your key vault, and select the Keys setting. Selezionare il tasto desiderato, quindi fare clic sul tasto per visualizzarne le versioni. Selezionare una versione chiave per visualizzare le impostazioni per tale versione.
1. Copiare il valore del campo **Identificatore chiave,** che fornisce l'URI.

    ![Schermata che mostra l'URI della chiave dell'insieme di credenziali](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. In **Impostazioni di crittografia** per l'account di archiviazione scegliere l'opzione **Immetti URI chiave.**
1. Incollare l'URI copiato nel campo **URI chiave.**

   ![Schermata che mostra come immettere l'URI della chiave](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Specificare la sottoscrizione che contiene l'insieme di credenziali delle chiavi.
1. Salvare le modifiche.

### <a name="specify-a-key-from-a-key-vault"></a>Specificare una chiave da un insieme di credenziali delle chiavi

Per specificare una chiave da un insieme di credenziali delle chiavi, assicurarsi innanzitutto di disporre di un insieme di credenziali delle chiavi contenente una chiave. Per specificare una chiave da un insieme di credenziali delle chiavi, attenersi alla seguente procedura:

1. Scegliere l'opzione **Selezionare la chiave dall'insieme di credenziali delle chiavi**.
1. Selezionare l'insieme di credenziali delle chiavi contenente la chiave che si desidera utilizzare.
1. Selezionare la chiave dall'insieme di credenziali delle chiavi.

   ![Schermata che mostra l'opzione della chiave gestita dal cliente](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Salvare le modifiche.

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, aggiornare l'account di archiviazione per usare la nuova versione. A tale scopo, seguire questa procedura:

1. Passare all'account di archiviazione e visualizzare le impostazioni **di crittografia.**
1. Immettere l'URI per la nuova versione della chiave. In alternativa, è possibile selezionare nuovamente l'insieme di credenziali delle chiavi e la chiave per aggiornare la versione.
1. Salvare le modifiche.

## <a name="use-a-different-key"></a>Utilizzare un tasto diverso

Per modificare la chiave usata per la crittografia di Archiviazione di Azure, eseguire la procedura seguente:To change the key used for Azure Storage encryption, follow these steps:

1. Passare all'account di archiviazione e visualizzare le impostazioni **di crittografia.**
1. Immettere l'URI per la nuova chiave. In alternativa, è possibile selezionare l'insieme di credenziali delle chiavi e scegliere una nuova chiave.
1. Salvare le modifiche.

## <a name="disable-customer-managed-keys"></a>Disabilitare le chiavi gestite dal clienteDisable customer-managed keys

Quando si disabilitano le chiavi gestite dal cliente, l'account di archiviazione viene nuovamente crittografato con le chiavi gestite da Microsoft.When you disable customer-managed keys, your storage account is once encrypted with Microsoft-managed keys. Per disabilitare le chiavi gestite dal cliente, attenersi alla seguente procedura:

1. Passare all'account di archiviazione e visualizzare le impostazioni **di crittografia.**
1. Deselezionare la casella di controllo accanto all'impostazione **Usa chiave personalizzata.**

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia di Archiviazione di Azure per i dati inattiviAzure Storage encryption for data at rest](storage-service-encryption.md)
- [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
