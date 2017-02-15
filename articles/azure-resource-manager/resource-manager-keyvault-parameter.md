---
title: Segreto dell&quot;insieme di credenziali delle chiavi con il modello di Resource Manager | Microsoft Docs
description: Viene illustrato come passare una chiave privata da un insieme di credenziali chiave come parametro durante la distribuzione.
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c582c144-4760-49d3-b793-a3e1e89128e2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 23fb716997145152bd09d177b75973ad0b4ca9f3
ms.openlocfilehash: 171cfe2a8750025914545701fa3423c7b9baa5f2


---
# <a name="use-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Usare Key Vault per passare un valore del parametro protetto durante la distribuzione

Quando è necessario passare un valore protetto (ad esempio una password) come parametro durante la distribuzione, è possibile recuperare il valore da [Azure Key Vault](../key-vault/key-vault-whatis.md). Il valore viene recuperato facendo riferimento all'insieme di credenziali delle chiavi e alla chiave privata nel file dei parametri. Il valore non viene mai esposto, in quanto si fa riferimento solo all'ID dell'insieme di credenziali chiave. Non è necessario immettere manualmente il valore segreto ogni volta che si distribuisce le risorse. L'insieme di credenziali delle chiavi può essere presente in una sottoscrizione diversa rispetto al gruppo di risorse in cui si sta eseguendo la distribuzione. Quando si fa riferimento all'insieme di credenziali delle chiavi, includere l'ID sottoscrizione.

Questo argomento illustra come creare un insieme di credenziali delle chiavi e una chiave privata, configurare l'accesso alla chiave privata per un modello di Resource Manager e passare la chiave privata come parametro. Se si dispone già di un insieme di credenziali delle chiavi e di una chiave privata, ma è necessario controllare il modello e l'accesso utente, andare alla sezione [Abilitare l'accesso alla chiave privata](#enable-access-to-the-secret). Se si dispone già di un insieme di credenziali delle chiavi e di una chiave privata e si è certi che siano stati configurati per il modello e l'accesso utente, andare alla sezione [Fare riferimento a un segreto con un ID statico](#reference-a-secret-with-static-id). 

## <a name="deploy-a-key-vault-and-secret"></a>Distribuire un insieme di credenziali chiave e una chiave privata

È possibile distribuire un insieme di credenziali delle chiavi e una chiave privata tramite un modello di Resource Manager. Per un esempio, vedere [Key vault template](resource-manager-template-keyvault.md) (Schema del modello di insieme di credenziali chiave) e [Schema del modello di chiave privata nell'insieme di credenziali chiave](resource-manager-template-keyvault-secret.md). Quando si crea l'insieme di credenziali delle chiavi, impostare la proprietà **enabledForTemplateDeployment** su **true**, in modo che sia possibile farvi riferimento da altri modelli di Resource Manager. 

In alternativa, è possibile creare l'insieme di credenziali delle chiavi e la chiave privata tramite il portale di Azure. 

1. Selezionare **Nuovo** -> **Sicurezza e identità** -> **Insieme di credenziali delle chiavi**.

   ![creare un nuovo insieme di credenziali delle chiavi](./media/resource-manager-keyvault-parameter/new-key-vault.png)

2. Specificare i valori per l'insieme di credenziali delle chiavi. Per il momento è possibile ignorare le impostazioni di **Criteri di accesso** e **Criteri di accesso avanzati**. Queste impostazioni sono descritte più avanti nella sezione. Selezionare **Create**.

   ![impostare l'insieme di credenziali delle chiavi](./media/resource-manager-keyvault-parameter/create-key-vault.png)

3. È ora disponibile un insieme di credenziali delle chiavi. Selezionarlo.

4. Nel pannello dell'insieme di credenziali delle chiavi, selezionare **Segreti**.

   ![selezionare le chiavi private](./media/resource-manager-keyvault-parameter/select-secret.png)

5. Selezionare **Aggiungi**.

   ![selezionare aggiungi](./media/resource-manager-keyvault-parameter/add-secret.png)

6. Selezionare **Manuale** per le opzioni di caricamento. Specificare un nome e valore per la chiave privata. Selezionare **Create**.

   ![specificare una chiave privata](./media/resource-manager-keyvault-parameter/provide-secret.png)

Sono stati creati l'insieme di credenziali delle chiavi e la chiave privata.

## <a name="enable-access-to-the-secret"></a>Abilitare l'accesso alla chiave privata

Indipendentemente dal fatto che si usi un insieme di credenziali delle chiavi nuovo o già esistente, assicurarsi che l'utente che distribuisce il modello possa accedere alla chiave privata. L'utente che distribuisce un modello che fa riferimento a una chiave privata deve disporre dell'autorizzazione `Microsoft.KeyVault/vaults/deploy/action` per l'insieme di credenziali delle chiavi. Entrambi i ruoli [Proprietario](../active-directory/role-based-access-built-in-roles.md#owner) e [Collaboratore](../active-directory/role-based-access-built-in-roles.md#contributor) possono concedere l'accesso. È inoltre possibile creare un [ruolo personalizzato](../active-directory/role-based-access-control-custom-roles.md) che conceda l'autorizzazione e aggiunga l'utente a questo ruolo. Inoltre, è necessario concedere a Resource Manager la possibilità di accedere all'insieme di credenziali delle chiavi durante la distribuzione.

È possibile verificare o eseguire questi passaggi tramite il portale.

1. Selezionare **Controllo di accesso (IAM)**.

   ![selezionare il controllo dell'accesso](./media/resource-manager-keyvault-parameter/select-access-control.png)

2. Se l'account che si intende usare per la distribuzione dei modelli non è già Proprietario o Collaboratore (o aggiunto a un ruolo personalizzato che conceda l'autorizzazione `Microsoft.KeyVault/vaults/deploy/action`), selezionare **Aggiungi**.

   ![Aggiungi utente](./media/resource-manager-keyvault-parameter/add-user.png)

3. Selezionare il ruolo Collaboratore o Proprietario e cercare l'identità da assegnare a tale ruolo. Selezionare **OK** per completare l'aggiunta dell'identità al ruolo.

   ![Aggiungi utente](./media/resource-manager-keyvault-parameter/search-user.png)

4. Per abilitare l'accesso da un modello durante la distribuzione, selezionare **Criteri di accesso avanzati**. Selezionare l'opzione **Abilita l'accesso ad Azure Resource Manager per la distribuzione dei modelli**.

   ![abilitare l'accesso ai modelli](./media/resource-manager-keyvault-parameter/select-template-access.png)

## <a name="reference-a-secret-with-static-id"></a>Fare riferimento a un segreto con un ID statico

Si fa riferimento alla chiave privata all'interno di un **file dei parametri (non il modello)** che passa i valori al modello. Si fa riferimento alla chiave privata passando l'identificatore della risorsa dell'insieme di credenziali chiave e il nome della chiave privata. Nell'esempio seguente il segreto dell'insieme di credenziali delle chiavi deve esistere già e si deve fornire un valore statico per il relativo ID risorsa.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "sqlsvrAdminLoginPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          },
          "secretName": "adminPassword"
        }
      },
      "sqlsvrAdminLogin": {
        "value": "exampleadmin"
      }
    }
}
```

Nel modello il parametro che accetta la chiave privata deve essere di tipo **securestring**. Nell'esempio seguente sono illustrate le sezioni pertinenti di un modello che consente di distribuire un server SQL che richiede una password di amministratore.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlsvrAdminLogin": {
            "type": "string",
            "minLength": 4
        },
        "sqlsvrAdminLoginPassword": {
            "type": "securestring"
        },
        ...
    },
    "resources": [
        {
          "name": "[variables('sqlsvrName')]",
          "type": "Microsoft.Sql/servers",
          "location": "[resourceGroup().location]",
          "apiVersion": "2014-04-01-preview",
          "properties": {
              "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
              "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
          },
          ...
        }
    ],
    "variables": {
        "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
    },
    "outputs": { }
}
```



## <a name="reference-a-secret-with-dynamic-id"></a>Fare riferimento a un segreto con un ID dinamico

La sezione precedente ha illustrato come passare un ID risorsa statico per il segreto dell'insieme di credenziali delle chiavi. In alcuni scenari, tuttavia, è necessario fare riferimento a un segreto dell'insieme di credenziali delle chiavi che varia a seconda della distribuzione corrente. In questo caso non è possibile impostare come hardcoded l'ID risorsa nel file dei parametri. Non è sfortunatamente possibile generare in modo dinamico l'ID risorsa nel file dei parametri, perché le espressioni del modello non sono consentite nel file dei parametri.

Per generare in modo dinamico l'ID risorsa per un segreto dell'insieme di credenziali delle chiavi, è necessario spostare la risorsa che necessita del segreto in un modello annidato. Nel modello principale aggiungere il modello annidato e passare un parametro che include l'ID risorsa generato in modo dinamico.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }],
    "outputs": {}
}
```

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni generali sugli insiemi di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md).
* Per informazioni sull'uso di un insieme di credenziali delle chiavi con una macchina virtuale, vedere [Considerazioni sulla sicurezza per Azure Resource Manager](best-practices-resource-manager-security.md).
* Per esempi completi di segreti di riferimento alle chiavi private, vedere [Esempi di insiemi di credenziali chiave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).




<!--HONumber=Dec16_HO2-->


