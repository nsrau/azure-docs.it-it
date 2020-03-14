---
title: Usare il portale di Azure per configurare le chiavi gestite dal cliente
titleSuffix: Cognitive Services
description: Informazioni su come usare la portale di Azure per configurare le chiavi gestite dal cliente con Azure Key Vault. Le chiavi gestite dal cliente consentono di creare, ruotare, disabilitare e revocare i controlli di accesso.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: ad97bde447a7bfbddca480b8561403f2cee7e25a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372282"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configurare chiavi gestite dal cliente con Azure Key Vault usando il portale di Azure

È necessario utilizzare Azure Key Vault per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. La risorsa Servizi cognitivi e l'insieme di credenziali delle chiavi devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse. Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Questo articolo illustra come configurare un Azure Key Vault con chiavi gestite dal cliente usando il [portale di Azure](https://portal.azure.com/). Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere [Guida introduttiva: impostare e recuperare un segreto da Azure Key Vault tramite il portale di Azure](../../key-vault/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Configurare Azure Key Vault

Con le chiavi gestite dal cliente è necessario impostare due proprietà nell'insieme di credenziali delle chiavi, **eliminare temporaneamente** e non **ripulire**. Queste proprietà non sono abilitate per impostazione predefinita, ma possono essere abilitate tramite PowerShell o l'interfaccia della riga di comando di Azure in un insieme di credenziali delle chiavi nuovo o esistente.

> [!IMPORTANT]
> Se non si dispone dell' **eliminazione** temporanea e non si **ripuliscono** le proprietà abilitate e si elimina la chiave, non sarà possibile recuperare i dati nella risorsa del servizio cognitive.

Per informazioni su come abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, vedere le sezioni intitolate **Abilitazione dell'eliminazione** temporanea e **Abilitazione della protezione di ripulitura** in uno degli articoli seguenti:

- [Come usare l'eliminazione temporanea con PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Come usare l'eliminazione temporanea con l'interfaccia](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli)della riga di comando.

Con la crittografia di archiviazione di Azure sono supportate solo le chiavi RSA della dimensione 2048. Per ulteriori informazioni sulle chiavi, vedere **Key Vault chiavi** in [informazioni su Azure Key Vault chiavi, segreti e certificati](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Abilita chiavi gestite dal cliente

Per abilitare le chiavi gestite dal cliente nel portale di Azure, attenersi alla procedura seguente:

1. Passare alla risorsa Servizi cognitivi.
1. Nel pannello **Impostazioni** della risorsa Servizi cognitivi fare clic su **crittografia**. Selezionare l'opzione **chiavi gestite dal cliente** , come illustrato nella figura seguente.

    ![Screenshot che illustra come selezionare le chiavi gestite dal cliente](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Specificare una chiave

Dopo aver abilitato le chiavi gestite dal cliente, sarà possibile specificare una chiave da associare alla risorsa Servizi cognitivi.

### <a name="specify-a-key-as-a-uri"></a>Specificare una chiave da un URI

Per specificare una chiave come URI, attenersi alla procedura seguente:

1. Per individuare l'URI della chiave nella portale di Azure, passare all'insieme di credenziali delle chiavi e selezionare l'impostazione **chiavi** . Selezionare la chiave desiderata, quindi fare clic sulla chiave per visualizzarne le versioni. Selezionare una versione della chiave per visualizzare le impostazioni per tale versione.
1. Copiare il valore del campo **identificatore chiave** , che fornisce l'URI.

    ![Screenshot che mostra l'URI della chiave di Key Vault](../media/cognitive-services-encryption/key-uri-portal.png)

1. Nelle impostazioni di **crittografia** per l'account di archiviazione scegliere l'opzione **immettere l'URI del tasto** .
1. Incollare l'URI copiato nel campo **URI chiave** .

   ![Screenshot che illustra come immettere l'URI della chiave](../media/cognitive-services-encryption/ssecmk2.png)

1. Specificare la sottoscrizione che contiene l'insieme di credenziali delle chiavi.
1. Salvare le modifiche.

### <a name="specify-a-key-from-a-key-vault"></a>Specificare una chiave da un insieme di credenziali delle chiavi

Per specificare una chiave da un insieme di credenziali delle chiavi, assicurarsi innanzitutto di avere un insieme di credenziali delle chiavi contenente una chiave. Per specificare una chiave da un insieme di credenziali delle chiavi, seguire questa procedura:

1. Scegliere l'opzione **Selezionare la chiave dall'insieme di credenziali delle chiavi**.
1. Selezionare l'insieme di credenziali delle chiavi contenente la chiave che si vuole usare.
1. Selezionare la chiave dall'insieme di credenziali delle chiavi.

   ![Screenshot che mostra l'opzione della chiave gestita dal cliente](../media/cognitive-services-encryption/ssecmk3.png)

1. Salvare le modifiche.

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, aggiornare la risorsa Servizi cognitivi per usare la nuova versione. A tale scopo, seguire questa procedura:

1. Passare alla risorsa Servizi cognitivi e visualizzare le impostazioni di **crittografia** .
1. Immettere l'URI per la nuova versione della chiave. In alternativa, è possibile selezionare di nuovo l'insieme di credenziali delle chiavi e la chiave per aggiornare la versione.
1. Salvare le modifiche.

## <a name="use-a-different-key"></a>Usare una chiave diversa

Per modificare la chiave usata per la crittografia, attenersi alla procedura seguente:

1. Passare alla risorsa Servizi cognitivi e visualizzare le impostazioni di **crittografia** .
1. Immettere l'URI della nuova chiave. In alternativa, è possibile selezionare l'insieme di credenziali delle chiavi e scegliere una nuova chiave.
1. Salvare le modifiche.

## <a name="disable-customer-managed-keys"></a>Disabilitare le chiavi gestite dal cliente

Quando si disabilitano le chiavi gestite dal cliente, la risorsa Servizi cognitivi viene quindi crittografata con le chiavi gestite da Microsoft. Per disabilitare le chiavi gestite dal cliente, attenersi alla seguente procedura:

1. Passare alla risorsa Servizi cognitivi e visualizzare le impostazioni di **crittografia** .
1. Deselezionare la casella di controllo accanto all'impostazione **Usa una chiave personalizzata** .

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Modulo di richiesta chiave gestita dal cliente di servizi cognitivi](https://aka.ms/cogsvc-cmk)
* [Crittografia dei dati inattivi per i servizi facciali](../Face/face-encryption-of-data-at-rest.md)
* [QnA Maker la crittografia dei dati inattivi](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Crittografia dei dati inattivi di Language Understanding Service](../LUIS/luis-encryption-of-data-at-rest.md)
* [Content Moderator la crittografia dei dati inattivi](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Crittografia dei dati inattivi di Translator](../translator/translator-encryption-of-data-at-rest.md)
