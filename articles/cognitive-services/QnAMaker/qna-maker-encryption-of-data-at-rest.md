---
title: QnA Maker la crittografia dei dati inattivi
titleSuffix: Azure Cognitive Services
description: QnA Maker la crittografia dei dati inattivi.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372095"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker la crittografia dei dati inattivi

QnA Maker crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud, contribuendo a soddisfare gli obiettivi di sicurezza e conformità dell'organizzazione.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Per impostazione predefinita, la sottoscrizione USA chiavi di crittografia gestite da Microsoft. È anche possibile gestire la sottoscrizione con le proprie chiavi. Le chiavi gestite dal cliente (CMK) offrono maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere i dati.

QnA Maker usa il supporto CMK da ricerca di Azure. È necessario creare [CMK in ricerca di Azure usando Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Questa istanza di Azure deve essere associata al servizio QnA Maker per renderla CMK abilitata.

> [!IMPORTANT]
> La risorsa del servizio ricerca di Azure deve essere stata creata dopo il 2019 gennaio e non può trovarsi nel livello gratuito (condiviso). Non è disponibile alcun supporto per la configurazione delle chiavi gestite dal cliente nel portale di Azure.

## <a name="enable-customer-managed-keys"></a>Abilita chiavi gestite dal cliente

Il servizio QnA Maker USA CMK dal servizio ricerca di Azure. Per abilitare CMK, attenersi alla procedura seguente:

1. Creare una nuova istanza di ricerca di Azure e abilitare i prerequisiti indicati nei [prerequisiti della chiave gestita dal cliente per Azure ricerca cognitiva](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Visualizzare le impostazioni di crittografia](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Quando si crea una risorsa di QnA Maker, questa viene associata automaticamente a un'istanza di ricerca di Azure. Non può essere usato con CMK. Per usare CMK, è necessario associare l'istanza appena creata di ricerca di Azure creata nel passaggio 1. In particolare, è necessario aggiornare `AzureSearchAdminKey` e `AzureSearchName` nella risorsa QnA Maker.

   ![Visualizzare le impostazioni di crittografia](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Successivamente, creare una nuova impostazione dell'applicazione:
   * **Nome**: impostare questa impostazione su`CustomerManagedEncryptionKeyUrl`
   * **Valore**: questo è il valore ottenuto nel passaggio 1 durante la creazione dell'istanza di ricerca di Azure.

   ![Visualizzare le impostazioni di crittografia](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Al termine, riavviare il Runtime. Il servizio QnA Maker è ora abilitato per CMK.

## <a name="regional-availability"></a>Disponibilità a livello di area

Le chiavi gestite dal cliente sono disponibili in tutte le aree di ricerca di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Crittografia in ricerca di Azure con CMK in Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Crittografia dei dati inattivi](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Altre informazioni su Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
