---
title: Usare il portale di Azure per configurare le chiavi gestite dal clienteUse the Azure portal to configure customer-managed keys
titleSuffix: Cognitive Services
description: Informazioni su come usare il portale di Azure per configurare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure.Learn how to use the Azure portal to configure customer-managed keys with Azure Key Vault. Le chiavi gestite dal cliente consentono di creare, ruotare, disabilitare e revocare i controlli di accesso.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 22bd3afcf30b8b8ebce18b22d5419d49ec8c3b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053599"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configurare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure tramite il portale di AzureConfigure customer-managed keys with Azure Key Vault by using the Azure portal

È necessario usare l'insieme di credenziali delle chiavi di Azure per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API dell'insieme di credenziali delle chiavi di Azure per generare le chiavi. La risorsa Servizi cognitivi e l'insieme di credenziali delle chiavi devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse. Per altre informazioni su Archiviazione delle chiavi di Azure, vedere [Che cos'è l'insieme di](https://docs.microsoft.com/azure/key-vault/key-vault-overview)credenziali delle chiavi di Azure?.

Questo articolo illustra come configurare un insieme di credenziali delle chiavi di Azure con chiavi gestite dal cliente tramite il portale di Azure.This article shows how to configure an Azure Key Vault with customer-managed keys using the [Azure portal](https://portal.azure.com/). Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [Guida introduttiva: Impostare e recuperare un segreto da Un](../../key-vault/quick-create-portal.md)insieme di credenziali delle chiavi di Azure usando il portale di Azure.To learn how to create a key vault using the Azure portal, see Quickstart: Set and retrieve a secret from Azure Key Vault using the Azure portal.

## <a name="configure-azure-key-vault"></a>Configurare Azure Key Vault

L'utilizzo di chiavi gestite dal cliente richiede l'impostazione di due proprietà nell'insieme di credenziali delle chiavi, **Eliminazione temporanea** e **Non eliminare**. Queste proprietà non sono abilitate per impostazione predefinita, ma possono essere abilitate usando PowerShell o l'interfaccia della riga di comando di Azure in un insieme di credenziali delle chiavi nuovo o esistente.

> [!IMPORTANT]
> Se le proprietà **Soft Delete** e **Do Not Purge** sono abilitate e si elimina la chiave, non sarà possibile recuperare i dati nella risorsa del servizio cognitivo.

Per informazioni su come abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, vedere le sezioni relative **Abilitazione dell'eliminazione temporanea** e **dell'abilitazione** della protezione delle eliminazioni in uno degli articoli seguenti:

- [Come usare l'eliminazione temporanea con PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Come utilizzare l'eliminazione temporanea con CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

Solo le chiavi RSA di dimensioni 2048 sono supportate con la crittografia di Archiviazione di Azure.Only RSA keys of size 2048 are supported with Azure Storage encryption. Per altre informazioni sulle chiavi, vedere **Chiavi dell'insieme di credenziali delle chiavi** in Informazioni su chiavi, segreti e certificati dell'insieme di credenziali delle chiavi di [Azure.](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)

## <a name="enable-customer-managed-keys"></a>Abilitare le chiavi gestite dal clienteEnable customer-managed keys

Per abilitare le chiavi gestite dal cliente nel portale di Azure, eseguire la procedura seguente:To enable customer-managed keys in the Azure portal, follow these steps:

1. Passare alla risorsa Servizi cognitivi.
1. Nel pannello **Impostazioni** per la risorsa Servizi cognitivi fare clic su **Crittografia**. Selezionare l'opzione **Chiavi gestite dal cliente,** come illustrato nella figura seguente.

    ![Screenshot che mostra come selezionare le chiavi gestite dal cliente](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Specificare una chiave

Dopo aver abilitato le chiavi gestite dal cliente, è possibile specificare una chiave da associare alla risorsa Servizi cognitivi.

### <a name="specify-a-key-as-a-uri"></a>Specificare una chiave da un URI

Per specificare una chiave come URI, attenersi alla seguente procedura:

1. Per individuare l'URI della chiave nel portale di Azure, passare all'insieme di credenziali delle chiavi e selezionare l'impostazione **Chiavi.To** locate the key URI in the Azure portal, navigate to your key vault, and select the Keys setting. Selezionare il tasto desiderato, quindi fare clic sul tasto per visualizzarne le versioni. Selezionare una versione chiave per visualizzare le impostazioni per tale versione.
1. Copiare il valore del campo **Identificatore chiave,** che fornisce l'URI.

    ![Schermata che mostra l'URI della chiave dell'insieme di credenziali](../media/cognitive-services-encryption/key-uri-portal.png)

1. In **Impostazioni di crittografia** per l'account di archiviazione scegliere l'opzione **Immetti URI chiave.**
1. Incollare l'URI copiato nel campo **URI chiave.**

   ![Schermata che mostra come immettere l'URI della chiave](../media/cognitive-services-encryption/ssecmk2.png)

1. Specificare la sottoscrizione che contiene l'insieme di credenziali delle chiavi.
1. Salvare le modifiche.

### <a name="specify-a-key-from-a-key-vault"></a>Specificare una chiave da un insieme di credenziali delle chiavi

Per specificare una chiave da un insieme di credenziali delle chiavi, assicurarsi innanzitutto di disporre di un insieme di credenziali delle chiavi contenente una chiave. Per specificare una chiave da un insieme di credenziali delle chiavi, attenersi alla seguente procedura:

1. Scegliere l'opzione **Selezionare la chiave dall'insieme di credenziali delle chiavi**.
1. Selezionare l'insieme di credenziali delle chiavi contenente la chiave che si desidera utilizzare.
1. Selezionare la chiave dall'insieme di credenziali delle chiavi.

   ![Schermata che mostra l'opzione della chiave gestita dal cliente](../media/cognitive-services-encryption/ssecmk3.png)

1. Salvare le modifiche.

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, aggiornare la risorsa Servizi cognitivi per usare la nuova versione. A tale scopo, seguire questa procedura:

1. Passare alla risorsa Servizi cognitivi e visualizzare le impostazioni **di crittografia.**
1. Immettere l'URI per la nuova versione della chiave. In alternativa, è possibile selezionare nuovamente l'insieme di credenziali delle chiavi e la chiave per aggiornare la versione.
1. Salvare le modifiche.

## <a name="use-a-different-key"></a>Utilizzare un tasto diverso

Per modificare la chiave utilizzata per la crittografia, attenersi alla seguente procedura:

1. Passare alla risorsa Servizi cognitivi e visualizzare le impostazioni **di crittografia.**
1. Immettere l'URI per la nuova chiave. In alternativa, è possibile selezionare l'insieme di credenziali delle chiavi e scegliere una nuova chiave.
1. Salvare le modifiche.

## <a name="disable-customer-managed-keys"></a>Disabilitare le chiavi gestite dal clienteDisable customer-managed keys

Quando si disabilitano le chiavi gestite dal cliente, la risorsa Servizi cognitivi viene quindi crittografata con chiavi gestite da Microsoft.When you disable customer-managed keys, your Cognitive Services resource is then encrypted with Microsoft-managed keys. Per disabilitare le chiavi gestite dal cliente, attenersi alla seguente procedura:

1. Passare alla risorsa Servizi cognitivi e visualizzare le impostazioni **di crittografia.**
1. Deselezionare la casella di controllo accanto all'impostazione **Usa chiave personalizzata.**

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Modulo di richiesta chiave gestita dal cliente di Servizi cognitivi](https://aka.ms/cogsvc-cmk)
* [Crittografia Face Services dei dati inattivi](../Face/face-encryption-of-data-at-rest.md)
* [Crittografia QnA Maker dei dati inattivi](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Lingua Informazioni sulla crittografia del servizio dei dati inattivi](../LUIS/luis-encryption-of-data-at-rest.md)
* [Content Moderator crittografia dei dati inattivi](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Traduttore crittografia dei dati inattivi](../translator/translator-encryption-of-data-at-rest.md)
* [Crittografia personalizzate dei dati inattivi](../personalizer/personalizer-encryption-of-data-at-rest.md)
