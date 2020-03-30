---
title: Configurare le chiavi gestite dal cliente tramite il portale di AzureConfigure customer-managed-keys using the Azure portal
description: Questo articolo descrive come configurare la crittografia delle chiavi gestite dal cliente nei dati in Azure Data Explorer.This article describes how to configure customer-managed keys encryption on your data in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: c014ed2c25711677617d3bf8ff5d2f0f968a3b14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301044"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Configurare le chiavi gestite dal cliente tramite il portale di AzureConfigure customer-managed keys using the Azure portal

> [!div class="op_single_selector"]
> * [Portale](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Modello di Azure Resource ManagerAzure Resource Manager template](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Abilitare la crittografia con chiavi gestite dal cliente nel portale di AzureEnable encryption with customer-managed keys in the Azure portal

Questo articolo illustra come abilitare la crittografia delle chiavi gestite dal cliente tramite il portale di Azure.This article shows you how to enable customer-managed keys encryption using the Azure portal. Per impostazione predefinita, la crittografia di Azure Data Explorer usa chiavi gestite da Microsoft.By default, Azure Data Explorer encryption uses Microsoft-managed keys. Configurare il cluster di Azure Data Explorer per l'uso delle chiavi gestite dal cliente e specificare la chiave da associare al cluster.

1. Nel portale di Azure passare alla risorsa cluster di [Azure Data Explorer.In](create-cluster-database-portal.md#create-a-cluster) the [Azure portal,](https://portal.azure.com/)go to your Azure Data Explorer cluster resource. 
1. Selezionare**Crittografia** **impostazioni** > nel riquadro sinistro del portale.
1. Nel riquadro **Crittografia** selezionare **Attivato** per l'impostazione **Chiave gestita dal cliente.**
1. Fare clic su **Seleziona chiave**.

    ![Configurare le chiavi gestite dal cliente](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. Nella finestra **Seleziona chiave dall'insieme di** credenziali delle chiavi di Azure selezionare un insieme di credenziali **delle** chiavi esistente dall'elenco a discesa. Se si seleziona **Crea nuovo** per creare un [nuovo insieme](/azure/key-vault/quick-create-portal#create-a-vault)di credenziali delle chiavi, verrà indirizzato alla schermata Crea **vault** chiave.

1. Selezionare **Tasto**.
1. Selezionare **Versione**.
1. Fare clic su **Seleziona**.

    ![Seleziona chiave dall'insieme di credenziali delle chiavi di AzureSelect key from Azure Key Vault](media/customer-managed-keys-portal/cmk-key-vault.png)

1. Nel riquadro **Crittografia** che contiene la chiave selezionare **Salva**. Quando la creazione di CMK ha esito positivo, verrà visualizzato un messaggio di operazione riuscita in **Notifiche**.

    ![Salva la chiave gestita dal cliente](media/customer-managed-keys-portal/cmk-encryption-setting.png)

Abilitando le chiavi gestite dal cliente per il cluster Azure Data Explorer, si creerà un'identità assegnata al sistema per il cluster, se non ne esiste una. Inoltre, si forniranno le autorizzazioni di visualizzazione necessarie per il cluster di Azure Data Explorer nell'insieme di credenziali delle chiavi selezionato e si otterranno le proprietà dell'insieme di credenziali delle chiavi. 

> [!NOTE]
> Selezionare **Disattivato** per rimuovere la chiave gestita dal cliente dopo che è stata creata.

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i cluster di Azure Data Explorer in AzureSecure Azure Data Explorer clusters in Azure](security.md)
* [Proteggere il cluster in Azure Data Explorer - Portale](manage-cluster-security.md) di Azure abilitando la crittografia inattivi.
* [Configurare le chiavi gestite dal cliente usando il modello di Azure Resource ManagerConfigure customer-managed-keys using the Azure Resource Manager template](customer-managed-keys-resource-manager.md)
* [Configurare le chiavi gestite dal cliente usando CConfigure customer-managed-keys using C #](customer-managed-keys-csharp.md)



