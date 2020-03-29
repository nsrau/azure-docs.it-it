---
title: Crittografia QnA Maker dei dati inattivi
titleSuffix: Azure Cognitive Services
description: QnA Maker crittografia dei dati a riposo .
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372095"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>Crittografia QnA Maker dei dati inattivi

QnA Maker crittografa automaticamente i dati quando vengono resi persistenti nel cloud, consentendo di raggiungere gli obiettivi di sicurezza e conformità dell'organizzazione.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Per impostazione predefinita, la sottoscrizione usa chiavi di crittografia gestite da Microsoft.By default, your subscription uses Microsoft-managed encryption keys. C'è anche un'opzione per gestire l'abbonamento con le proprie chiavi. I tasti gestiti dal cliente (CMK) offrono una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È inoltre possibile controllare le chiavi di crittografia utilizzate per proteggere i dati.

QnA Maker usa il supporto CMK dalla ricerca di Azure.QnA Maker uses CMK support from Azure search. È necessario creare CMK in Ricerca di Azure usando L'insieme di credenziali delle chiavi di Azure.You need to create [CMK in Azure Search using Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Questa istanza di Azure deve essere associata al servizio QnA Maker per abilitarla a CMK.

> [!IMPORTANT]
> La risorsa del servizio Ricerca di Azure deve essere stata creata dopo gennaio 2019 e non può essere nel livello gratuito (condiviso). Non è disponibile alcun supporto per configurare le chiavi gestite dal cliente nel portale di Azure.There is no support to configure customer-managed keys in the Azure portal.

## <a name="enable-customer-managed-keys"></a>Abilitare le chiavi gestite dal clienteEnable customer-managed keys

Il servizio QnA Maker usa CMK dal servizio Ricerca di Azure.The QnA Maker service uses CMK from the Azure Search service. Attenersi alla seguente procedura per abilitare le CPK:

1. Creare una nuova istanza di Ricerca di Azure e abilitare i prerequisiti menzionati nei prerequisiti chiave gestiti dal cliente per Ricerca cognitiva di Azure.Create a new Azure Search instance and enable the prerequisites mentioned in the [customer-managed key prerequisites for Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Visualizzare le impostazioni di crittografia](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Quando si crea una risorsa QnA Maker, questa viene associata automaticamente a un'istanza di Ricerca di Azure.When you create a QnA Maker resource, it's automatically associated with an Azure Search instance. Questo non può essere utilizzato con CMK. Per usare CMK, è necessario associare l'istanza appena creata di Ricerca di Azure creata nel passaggio 1.To use CMK, you'll need to associate your newly created instance of Azure Search that was created in step 1. In particolare, è necessario `AzureSearchAdminKey` aggiornare la risorsa e `AzureSearchName` nella risorsa QnA Maker.

   ![Visualizzare le impostazioni di crittografia](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Successivamente, creare una nuova impostazione dell'applicazione:Next, create a new application setting:
   * **Nome**: Impostare questa opzione su`CustomerManagedEncryptionKeyUrl`
   * **Valore**: Questo è il valore ottenuto nel passaggio 1 durante la creazione dell'istanza di Ricerca di Azure.Value : This is the value that you got in Step 1 when creating your Azure Search instance.

   ![Visualizzare le impostazioni di crittografia](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Al termine, riavviare il runtime. Ora il servizio QnA Maker è abilitato per CMK.

## <a name="regional-availability"></a>Disponibilità a livello di area

Le chiavi gestite dal cliente sono disponibili in tutte le aree di Ricerca di Azure.Customer-managed keys are available in all Azure Search regions.

## <a name="next-steps"></a>Passaggi successivi

* [Crittografia in Ricerca di Azure usando le chiavi CME nel Vault delle chiavi di AzureEncryption in Azure Search using CKs in Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Crittografia dei dati inattivi](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Altre informazioni su Azure Key VaultLearn more about Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
