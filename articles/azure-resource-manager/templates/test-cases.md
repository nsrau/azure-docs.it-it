---
title: Test case per test Toolkit
description: Descrive i test eseguiti da ARM template test Toolkit.
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: dda8e92c17029126e7f473a6aee03acfc970e04b
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378118"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Test case predefiniti per ARM template test Toolkit

Questo articolo descrive i test predefiniti eseguiti con il Toolkit di [test del modello](test-toolkit.md). Fornisce esempi che superano o non superano il test. Include il nome di ogni test.

## <a name="use-correct-schema"></a>Usa schema corretto

Nome test: lo **schema DeploymentTemplate è corretto**

Nel modello è necessario specificare un valore di schema valido.

Nell'esempio seguente viene **passato** questo test.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

È necessario impostare la proprietà dello schema nel modello su uno degli schemi seguenti:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>I parametri devono esistere

Nome test: la **Proprietà Parameters deve esistere**

Il modello deve avere un elemento Parameters. I parametri sono essenziali per rendere i modelli riutilizzabili in ambienti diversi. Aggiungere parametri al modello per i valori che cambiano quando si esegue la distribuzione in ambienti diversi.

L'esempio seguente **supera** il test:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>È necessario usare i parametri dichiarati

Nome test: **è necessario fare riferimento ai parametri**

Per ridurre la confusione nel modello, eliminare tutti i parametri definiti ma non utilizzati. Questo test trova tutti i parametri che non vengono usati in alcun punto del modello. L'eliminazione di parametri inutilizzati facilita anche la distribuzione del modello perché non è necessario fornire valori non necessari.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>I parametri sicuri non possono avere un valore predefinito hardcoded

Nome test: i **parametri della stringa sicura non possono avere valori predefiniti**

Non specificare un valore predefinito hardcoded per un parametro sicuro nel modello. Una stringa vuota è corretta per il valore predefinito.

Usare i tipi **SecureString** o **SecureObject** nei parametri che contengono valori sensibili, ad esempio le password. Quando un parametro utilizza un tipo protetto, il valore del parametro non viene registrato né archiviato nella cronologia di distribuzione. Questa azione impedisce a un utente malintenzionato di individuare il valore sensibile.

Tuttavia, quando si specifica un valore predefinito, tale valore può essere individuato da chiunque possa accedere al modello o alla cronologia di distribuzione.

Nell'esempio seguente viene **superato** il test:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

L'esempio seguente **supera** il test:

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>Gli URL dell'ambiente non possono essere hardcoded

Nome test: **DeploymentTemplate non deve contenere URI hardcoded**

Non impostare come hardcoded gli URL di ambiente nel modello. Usare invece la [funzione Environment](template-functions-deployment.md#environment) per ottenere in modo dinamico questi URL durante la distribuzione. Per un elenco degli host URL bloccati, vedere la [test case](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1).

Nell'esempio seguente il test **non viene superato** perché l'URL è hardcoded.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

Anche il test ha **esito negativo** se usato con [Concat](template-functions-string.md#concat) o [URI](template-functions-string.md#uri).

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

Nell'esempio seguente viene **passato** questo test.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>Il percorso usa il parametro

Nome test: il **percorso non deve essere hardcoded**

È possibile che agli utenti del modello siano disponibili aree limitate. Quando si imposta il percorso della risorsa su `"[resourceGroup().location]"` , il gruppo di risorse potrebbe essere stato creato in un'area a cui gli altri utenti non possono accedere. A questi utenti viene impedito l'uso del modello.

Quando si definisce il percorso per ogni risorsa, usare un parametro che per impostazione predefinita è il percorso del gruppo di risorse. Specificando questo parametro, gli utenti possono usare il valore predefinito quando è utile, ma anche specificare un percorso diverso.

Nell'esempio seguente il test **non viene superato** perché il percorso della risorsa è impostato su `resourceGroup().location` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

Nell'esempio seguente viene usato un parametro location, ma il test **non riesce** perché il parametro location è impostato su una posizione hardcoded.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

In alternativa, creare un parametro che per impostazione predefinita è il percorso del gruppo di risorse, ma consente agli utenti di fornire un valore diverso. Nell'esempio seguente viene **passato** questo test.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="resources-should-have-location"></a>Le risorse devono avere un percorso

Nome test: **le risorse devono avere il percorso**

Il percorso di una risorsa deve essere impostato su un' [espressione di modello](template-expressions.md) o `global` . L'espressione modello USA in genere il parametro location descritto nel test precedente.

Nell'esempio seguente il test **non viene superato** perché il percorso non è un'espressione o `global` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Nell'esempio seguente viene **passato** questo test.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

L'esempio successivo **supera** anche questo test.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>Dimensioni macchina virtuale usa parametro

Nome test: le **dimensioni della macchina virtuale devono essere un parametro**

Non impostare come hardcoded le dimensioni della macchina virtuale. Fornire un parametro in modo che gli utenti del modello possano modificare le dimensioni della macchina virtuale distribuita.

Nell'esempio seguente il test **non viene superato** .

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

Specificare invece un parametro.

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

Quindi, impostare le dimensioni della macchina virtuale su tale parametro.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>I valori min e Max sono numeri

Nome test: il **valore minimo e massimo sono numeri**

Se si definiscono i valori min e Max per un parametro, specificarli come numeri.

Nell'esempio seguente viene **superato** il test:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Specificare invece i valori come numeri. L'esempio seguente **supera** il test:

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

Questo avviso viene visualizzato anche se si specifica un valore minimo o massimo, ma non l'altro.

## <a name="artifacts-parameter-defined-correctly"></a>Parametro degli artefatti definito correttamente

Nome test: **parametro artefatti**

Quando si includono parametri per `_artifactsLocation` e `_artifactsLocationSasToken` , utilizzare i tipi e le impostazioni predefinite corretti. Per superare il test, è necessario che siano soddisfatte le condizioni seguenti:

* Se si specifica un parametro, è necessario fornire l'altro
* `_artifactsLocation` deve essere una **stringa**
* `_artifactsLocation` deve avere un valore predefinito nel modello principale
* `_artifactsLocation` non è possibile avere un valore predefinito in un modello annidato 
* `_artifactsLocation` deve avere `"[deployment().properties.templateLink.uri]"` o l'URL del repository non elaborato per il valore predefinito
* `_artifactsLocationSasToken` deve essere un oggetto **secureString**
* `_artifactsLocationSasToken` può avere solo una stringa vuota per il valore predefinito
* `_artifactsLocationSasToken` non è possibile avere un valore predefinito in un modello annidato 

## <a name="declared-variables-must-be-used"></a>È necessario usare le variabili dichiarate

Nome test: **è necessario fare riferimento alle variabili**

Per ridurre la confusione nel modello, eliminare tutte le variabili definite ma non utilizzate. Questo test trova le variabili che non vengono usate in alcun punto del modello.

## <a name="dynamic-variable-should-not-use-concat"></a>La variabile dinamica non deve usare Concat

Nome test: i **riferimenti a variabili dinamiche non devono usare Concat**

In alcuni casi è necessario costruire dinamicamente una variabile in base al valore di un'altra variabile o parametro. Non usare la funzione [Concat](template-functions-string.md#concat) durante l'impostazione del valore. Usare invece un oggetto che include le opzioni disponibili e ottenere dinamicamente una delle proprietà dall'oggetto durante la distribuzione.

Nell'esempio seguente viene **passato** questo test. La variabile **currentImage** viene impostata in modo dinamico durante la distribuzione.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>USA versione API recente

Nome test: **ApiVersions dovrebbe essere recente**

La versione dell'API per ogni risorsa deve usare una versione recente. Il test valuta la versione usata per le versioni disponibili per quel tipo di risorsa.

## <a name="use-hardcoded-api-version"></a>Usa la versione dell'API hardcoded

Nome del test: i **provider ApiVersions non sono consentiti**

La versione dell'API per un tipo di risorsa determina quali proprietà sono disponibili. Specificare una versione dell'API hardcoded nel modello. Non recuperare una versione dell'API che viene determinata durante la distribuzione. Non si sa quali proprietà sono disponibili.

Nell'esempio seguente il test **non viene superato** .

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

Nell'esempio seguente viene **passato** questo test.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>Le proprietà non possono essere vuote

Nome test: il **modello non deve contenere spazi vuoti**

Non impostare come hardcoded le proprietà su un valore vuoto. I valori vuoti includono stringhe null e vuote, oggetti o matrici. Se è stata impostata una proprietà su un valore vuoto, rimuovere tale proprietà dal modello. Tuttavia, è accettabile impostare una proprietà su un valore vuoto durante la distribuzione, ad esempio tramite un parametro.

## <a name="use-resource-id-functions"></a>Usare le funzioni di ID risorsa

Nome test: gli **ID devono essere derivati da ResourceIDs**

Quando si specifica un ID risorsa, usare una delle funzioni ID risorsa. Le funzioni consentite sono:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Non usare la funzione concat per creare un ID risorsa. Nell'esempio seguente il test **non viene superato** .

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

L'esempio successivo **supera** questo test.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>La funzione ResourceId presenta parametri corretti

Nome del test: **ResourceIds non deve contenere**

Quando si generano gli ID risorsa, non usare funzioni superflue per i parametri facoltativi. Per impostazione predefinita, la funzione [resourceId](template-functions-resource.md#resourceid) usa la sottoscrizione e il gruppo di risorse correnti. Non è necessario specificare tali valori.  

Nell'esempio seguente il test **non viene superato** perché non è necessario fornire l'ID sottoscrizione corrente e il nome del gruppo di risorse.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

L'esempio successivo **supera** questo test.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Questo test si applica a:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [reference](template-functions-resource.md#reference)
* [list*](template-functions-resource.md#list)

Per `reference` e `list*` , il test ha **esito negativo** quando si usa `concat` per costruire l'ID risorsa.

## <a name="dependson-best-practices"></a>procedure consigliate per dependsOn

Nome test: **DependsOn Best Practices**

Quando si impostano le dipendenze di distribuzione, non usare la funzione [if](template-functions-logical.md#if) per testare una condizione. Se una risorsa dipende da una risorsa [distribuita in modo condizionale](conditional-resource-deployment.md), impostare la dipendenza come si farebbe con qualsiasi risorsa. Quando una risorsa condizionale non viene distribuita, Azure Resource Manager la rimuove automaticamente dalle dipendenze richieste.

Nell'esempio seguente il test **non viene superato** .

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

L'esempio successivo **supera** questo test.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Le distribuzioni nidificate o collegate non possono usare il debug

Nome test: **le risorse di distribuzione non devono essere debug**

Quando si definisce un [modello annidato o collegato](linked-templates.md) con il tipo di risorsa **Microsoft. resources/Deployments** , è possibile abilitare il debug per quel modello. Il debug è corretto quando è necessario eseguire il test del modello, ma deve essere attivato quando si è pronti a usare il modello nell'ambiente di produzione.

## <a name="admin-user-names-cant-be-literal-value"></a>I nomi utente amministratore non possono essere valori letterali

Nome test: **con valori AdminUsername non deve essere un valore letterale**

Quando si imposta un nome utente amministratore, non usare un valore letterale.

Nell'esempio seguente viene **superato** il test:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Usare invece un parametro. L'esempio seguente **supera** il test:

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Usa l'immagine di macchina virtuale più recente

Nome test: le **Immagini di macchina virtuale devono usare la versione più recente**

Se il modello include una macchina virtuale con un'immagine, assicurarsi che usi la versione più recente dell'immagine.

## <a name="use-stable-vm-images"></a>Usare immagini di VM stabili

Nome test: le **macchine virtuali non devono essere in anteprima**

Le macchine virtuali non devono usare immagini di anteprima.

Nell'esempio seguente il test **non viene superato** .

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

Nell'esempio seguente viene **passato** questo test.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Non usare l'estensione ManagedIdentity

Nome del test: **ManagedIdentityExtension non deve essere usato**

Non applicare l'estensione ManagedIdentity a una macchina virtuale. Per altre informazioni, vedere [come interrompere l'uso dell'estensione identità gestite della macchina virtuale e iniziare a usare il servizio metadati dell'istanza di Azure](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md).

## <a name="outputs-cant-include-secrets"></a>Gli output non possono includere segreti

Nome test: gli **output non devono contenere segreti**

Non includere alcun valore nella sezione outputs che potenzialmente esponga segreti. L'output di un modello viene archiviato nella cronologia di distribuzione, quindi un utente malintenzionato potrebbe trovare le informazioni.

Nell'esempio seguente viene **superato** il test perché include un parametro sicuro in un valore di output.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

L'esempio seguente ha **esito negativo** perché usa una funzione [List *](template-functions-resource.md#list) negli output.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'esecuzione del Toolkit di test, vedere [use ARM template test Toolkit](test-toolkit.md).