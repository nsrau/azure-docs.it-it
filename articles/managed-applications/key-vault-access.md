---
title: Uso di Azure Key Vault con le applicazioni gestite di Azure | Microsoft Docs
description: Illustra come usare i segreti di accesso in Azure Key Vault durante la distribuzione delle applicazioni gestite
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 55410250ccd4dfceac8ac9ae5b81d4736de0d91a
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492675"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Segreto di accesso di Key Vault quando si distribuiscono le Applicazioni gestite di Azure

Quando è necessario passare un valore protetto (ad esempio una password) come parametro durante la distribuzione, è possibile recuperare il valore da [Azure Key Vault](../key-vault/key-vault-whatis.md). Per accedere a Key Vault durante la distribuzione delle applicazione gestite è necessario concedere l'accesso all'entità servizio **Provider di risorse di Appliance**. Il servizio Applicazioni gestite usa questa identità per eseguire le operazioni. Per recuperare correttamente un valore da un insieme di credenziali delle chiavi durante la distribuzione, l'entità servizio deve essere in grado di accedere all'insieme di credenziali delle chiavi.

Questo articolo descrive come configurare Key Vault per lavorare con le applicazioni gestite.

## <a name="enable-template-deployment"></a>Abilitare la distribuzione di modelli

1. Nel portale selezionare Key Vault.

1. Selezionare **Criteri di accesso**.   

   ![Selezionare i criteri di accesso](./media/key-vault-access/select-access-policies.png)

1. Selezionare **Fare clic per visualizzare i criteri di accesso avanzati**.

   ![Mostrare i criteri di accesso avanzati](./media/key-vault-access/advanced.png)

1. Selezionare **Abilita l'accesso ad Azure Resource Manager per la distribuzione dei modelli**. Selezionare quindi **Salva**.

   ![Abilitare la distribuzione di modelli](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Aggiungere il servizio come collaboratore

1. Selezionare **Controllo di accesso (IAM)**.

   ![Selezionare il controllo di accesso](./media/key-vault-access/access-control.png)

1. Selezionare **Aggiungi assegnazione ruolo**.

   ![Selezionare Aggiungi](./media/key-vault-access/add-access-control.png)

1. Selezionare **Collaboratore** per il ruolo. Cercare **Provider di risorse di Appliance** e selezionarlo dalle opzioni disponibili.

   ![Cercare il provider](./media/key-vault-access/search-provider.png)

1. Selezionare **Salva**.

## <a name="reference-key-vault-secret"></a>Fare riferimento al segreto di Key Vault

Per passare un segreto da un Key Vault a un modello nell'applicazione gestita, è necessario usare un [modello collegato](../azure-resource-manager/resource-group-linked-templates.md) e fare riferimento a Key Vault nei parametri per il modello collegato. Fornire l'ID risorsa di Key Vault e il nome del segreto.

```json
"resources": [{
  "apiVersion": "2015-01-01",
  "name": "linkedTemplate",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"
          },
          "secretName": "<secret-name>"
        }
      },
      "adminLogin": { "value": "[parameters('adminLogin')]" },
      "sqlServerName": {"value": "[parameters('sqlServerName')]"}
    }
  }
}],
```

## <a name="next-steps"></a>Passaggi successivi

Key Vault è stato configurato per essere accessibile durante la distribuzione di un'applicazione gestita.

* Per informazioni su come passare un valore da un Key Vault come parametro del modello, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](../azure-resource-manager/resource-manager-keyvault-parameter.md).
* Per esempi di applicazioni gestite, vedere [Progetti di esempio per applicazioni gestite di Azure](sample-projects.md).
* Per informazioni sulla creazione di un file di definizione dell'interfaccia utente per un'applicazione gestita, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).