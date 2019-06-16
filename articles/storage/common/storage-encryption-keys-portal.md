---
title: Configurare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure dal portale di Azure
description: Informazioni su come usare il portale di Azure per configurare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure. Chiavi gestite dall'utente consentono di creare, ruotare, disabilitare e revocare i controlli di accesso.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: baabc5a8e1d063cb51a3edea3a7218591e85aa1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154160"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Configurare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure dal portale di Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Questo articolo illustra come configurare un insieme di credenziali delle chiavi con chiavi gestite dal cliente tramite il [portale di Azure](https://portal.azure.com/). Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault usando il portale di Azure](../../key-vault/quick-create-portal.md). 


> [!IMPORTANT]
> Con chiavi gestite dal cliente con la crittografia di archiviazione di Azure richiede che l'insieme di credenziali delle chiavi hanno due proprietà obbligatorie configurata, **eliminazione temporanea** e **eliminazione**. Queste proprietà sono abilitate per impostazione predefinita quando si crea un nuovo insieme di credenziali chiave nel portale di Azure. Tuttavia, se è necessario abilitare tali proprietà in un insieme di credenziali chiave esistente, è necessario usare PowerShell o CLI di Azure.

## <a name="enable-customer-managed-keys"></a>Abilitare le chiavi gestite dal cliente

Per abilitare le chiavi gestite dal cliente nel portale di Azure, seguire questa procedura:

1. Passare all'account di archiviazione.
1. Nel pannello **Impostazioni** relativo all'account di archiviazione fare clic su **Crittografia**. Selezionare l'opzione **Usare una chiave personalizzata**, come illustrato nella figura seguente.

    ![Opzione di crittografia con screenshot del portale](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Specificare una chiave

Dopo aver abilitato le chiavi gestite dal cliente, si avrà la possibilità di specificare una chiave da associare all'account di archiviazione.

### <a name="specify-a-key-as-a-uri"></a>Specificare una chiave da un URI

Per specificare una chiave come un URI, seguire questa procedura:

1. Per individuare l'URI della chiave nel portale di Azure, passare a insieme di credenziali delle chiavi e selezionare il **chiavi** impostazione. Selezionare la chiave desiderata, quindi fare clic sulla chiave per visualizzarne le impostazioni. Copiare il valore della **identificatore di chiave** campo, che fornisce l'URI.

    ![Screenshot che mostra chiave key vault URI](media/storage-encryption-keys-portal/key-uri-portal.png)

1. Nel **crittografia** impostazioni dell'account di archiviazione, selezionare la **invio URI** opzione.
1. Nel campo **URI della chiave** specificare l'URI.

   ![Screenshot che mostra come immettere l'URI della chiave](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Specificare una chiave da un insieme di credenziali delle chiavi

Per specificare una chiave da un insieme di credenziali delle chiavi, è prima di tutto assicurarsi di avere un insieme di credenziali delle chiavi contenente una chiave. Per specificare una chiave da un insieme di credenziali delle chiavi, seguire questa procedura:

1. Scegliere l'opzione **Selezionare la chiave dall'insieme di credenziali delle chiavi**.
2. Scegliere l'insieme di credenziali delle chiavi contenente la chiave da usare.
3. Scegliere la chiave dall'insieme di credenziali delle chiavi.

   ![Screenshot che Mostra opzione chiavi gestite dal cliente](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, è necessario aggiornare l'account di archiviazione per usare la nuova versione. A tale scopo, seguire questa procedura:

1. Passare all'account di archiviazione e visualizzare il **crittografia** impostazioni.
1. Specificare l'URI per la nuova versione della chiave. In alternativa, è possibile selezionare l'insieme di credenziali delle chiavi e la chiave nuovamente per aggiornare la versione.

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
