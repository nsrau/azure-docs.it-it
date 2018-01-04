---
title: Struttura e sintassi del modello di Azure Resource Manger | Documentazione Microsoft
description: "Descrive la struttura e le proprietà dei modelli di Azure Resource Manager con la sintassi dichiarativa JSON."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2017
ms.author: tomfitz
ms.openlocfilehash: 89e4b52e7d306bd495c426bcf775f59d0f30eb55
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Sezione relativa alle risorse dei modelli di gestione risorse di Azure

Nella sezione risorse è possibile definire le risorse da distribuire o aggiornare. Questa sezione può risultare complicata perché per specificare i valori corretti è necessario conoscere i tipi da distribuire.

## <a name="available-properties"></a>Proprietà disponibili

Le risorse vengono definite con la struttura seguente:

```json
"resources": [
  {
      "condition": "<boolean-value-whether-to-deploy>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": "<number-of-iterations>",
          "mode": "<serial-or-parallel>",
          "batchSize": "<number-to-deploy-serially>"
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nome dell'elemento | Obbligatoria | DESCRIZIONE |
|:--- |:--- |:--- |
| condition | No  | Valore booleano che indica se la risorsa viene distribuita. |
| apiVersion |Sì |Versione dell'API REST da utilizzare per la creazione della risorsa. |
| type |Sì |Tipo di risorsa. Questo valore è una combinazione dello spazio dei nomi del provider di risorse e del tipo di risorsa, ad esempio **Microsoft.Storage/storageAccounts**. |
| name |Sì |Nome della risorsa. Il nome deve rispettare le restrizioni dei componenti URI definite dallo standard RFC3986. I servizi Azure che rendono visibile il nome della risorsa a terze parti convalidano anche il nome, per garantire che non si tratti di un tentativo di spoofing per un'identità alternativa. |
| location |Variabile |Aree geografiche supportate della risorsa specificata. È possibile selezionare qualsiasi località disponibile, ma è in genere opportuno sceglierne una vicina agli utenti. Di solito è anche opportuno inserire le risorse che interagiscono tra loro nella stessa area. La maggior parte dei tipi di risorsa richiede una posizione, ma alcuni tipi (ad esempio un'assegnazione di ruolo) non la richiedono. |
| tags |No  |Tag associati alla risorsa. Applicare i tag per organizzare in modo logico le risorse tra la sottoscrizione. |
| commenti |No  |Le note per documentare le risorse nel modello |
| copy |No  |Numero di risorse da creare, se sono necessarie più istanze. La modalità predefinita è parallela. Specificare la modalità seriale quando si desidera che non tutte le risorse vengano distribuite contemporaneamente. Per altre informazioni, vedere [Creare più istanze di risorse in Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |No  |Risorse da distribuire prima della distribuzione di questa risorsa. Resource Manager valuta le dipendenze tra le risorse e le distribuisce nell'ordine corretto. Quando le risorse non sono interdipendenti, vengono distribuite in parallelo. Il valore può essere un elenco delimitato da virgole di nomi o identificatori univoci di risorse. Elencare solo le risorse distribuite in questo modello. Le risorse non definite in questo modello devono essere già esistenti. Evitare di aggiungere dipendenze non necessarie perché possono rallentare la distribuzione e creare dipendenze circolari. Per indicazioni sull'impostazione delle dipendenze, vedere l'articolo relativo alla [definizione delle dipendenze nei modelli di Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |No  |Impostazioni di configurazione specifiche delle risorse. I valori per l'elemento properties corrispondono esattamente a quelli specificati nel corpo della richiesta per l'operazione API REST (metodo PUT) per creare la risorsa. È inoltre possibile specificare una matrice di copia per creare più istanze di una proprietà. |
| resources |No  |Risorse figlio che dipendono dalla risorsa in via di definizione. Specificare solo tipi di risorsa consentiti dallo schema della risorsa padre. Il nome di tipo completo della risorsa figlio include il tipo della risorsa padre, ad esempio **Microsoft.Web/sites/extensions**. La dipendenza dalla risorsa padre non è implicita. È necessario definirla in modo esplicito. |

## <a name="resource-specific-values"></a>Valori specifici delle risorse

Il **apiVersion**, **tipo**, e **proprietà** sono diverse per ogni tipo di risorsa. Per determinare i valori per queste proprietà, vedere [riferimento a un modello](/azure/templates/).

## <a name="resource-names"></a>Nomi di risorse
In genere vengono usati tre tipi di nomi di risorse in Resource Manager:

* Nomi di risorse che devono essere univoci.
* I nomi delle risorse che non devono essere univoci, ma si sceglie di fornire un nome che è possibile identificare la risorsa.
* Nomi di risorse che possono essere generici.

### <a name="unique-resource-names"></a>Nomi di risorse univoci
È necessario fornire un nome univoco per qualsiasi tipo di risorsa con un endpoint di accesso ai dati. Alcuni tipi di risorse comuni che richiedono un nome univoco includono:

* Archiviazione di Azure<sup>1</sup> 
* Funzionalità app Web del servizio app di Azure
* SQL Server
* Azure Key Vault
* Cache Redis di Azure
* Azure Batch
* Gestione traffico di Azure
* Ricerca di Azure
* HDInsight di Azure

<sup>1</sup> I nomi di account di archiviazione devono essere formati da lettere minuscole, un massimo di 24 caratteri e non devono includere alcun segno meno.

Quando si imposta il nome, è possibile manualmente creare un nome univoco o utilizzare il [uniqueString()](resource-group-template-functions-string.md#uniquestring) funzione per generare un nome. È spesso opportuno aggiungere un prefisso o un suffisso al risultato di **uniqueString**. La modifica del nome univoco consente di identificare più facilmente il tipo di risorsa in base al nome. Ad esempio, è possibile generare un nome univoco per un account di archiviazione usando la variabile seguente:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Nomi di risorse per l'identificazione
Si tratta di risorse cui si desidera attribuire un nome, ma di cui non è necessario garantire l'univocità. Per questi tipi di risorse, è sufficiente indicare un nome che identifichi il contesto e il tipo di risorsa.

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

### <a name="generic-resource-names"></a>Nomi di risorse generici
Per i tipi di risorse in gran parte accessibili tramite un'altra risorsa, è possibile usare un nome generico che sia hardcoded nel modello. Ad esempio, è possibile impostare un nome generico e standard per le regole del firewall in SQL server:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>Località
Quando si distribuisce un modello, è necessario fornire la posizione per ogni risorsa. Diversi tipi di risorse sono supportati in posizioni diverse. Per visualizzare un elenco di ubicazioni disponibili per la sottoscrizione per un determinato tipo di risorsa, usare Azure PowerShell o l'interfaccia CLI di Azure. 

Nell'esempio seguente viene usato PowerShell per ottenere la posizione per il tipo di risorsa `Microsoft.Web\sites`:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Nell'esempio seguente viene usata l'interfaccia della riga di comando di Azure 2.0 per ottenere la posizione per il tipo di risorsa `Microsoft.Web\sites`:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

Dopo aver determinato i percorsi supportati per le risorse, impostare tale posizione nel modello. Il modo più semplice per impostare questo valore consiste nel creare un gruppo di risorse in una posizione che supporti i tipi di risorsa e impostare ogni posizione su `[resourceGroup().location]`. È possibile ridistribuire il modello a gruppi di risorse in posizioni diverse senza modificare i valori o i parametri del modello. 

Nell'esempio seguente viene illustrato un account di archiviazione che viene distribuito nella stessa posizione del gruppo di risorse:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Se è necessario impostare come hardcoded la posizione del modello, specificare il nome di una delle aree supportate. Nell'esempio seguente viene illustrato un account di archiviazione che viene sempre distribuito negli Stati Uniti centro-settentrionali:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="tags"></a>Tag
[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

### <a name="add-tags-to-your-template"></a>Aggiungere i tag al modello

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>Risorse figlio

All'interno di alcuni tipi di risorsa, è inoltre possibile definire una matrice di risorse figlio. Risorse figlio sono presenti solo all'interno del contesto di un'altra risorsa. Ad esempio, un database SQL non può esistere senza un server SQL in modo il database è un figlio del server. È possibile definire il database all'interno della definizione per il server.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Quando annidati, il tipo è impostato su `databases` ma il tipo di risorsa completo `Microsoft.Sql/servers/databases`. Non si specifica `Microsoft.Sql/servers/` perché viene ottenuto dal tipo della risorsa padre. Il nome della risorsa figlio è impostato su `exampledatabase`, ma il nome completo include il nome della risorsa padre. Non si specifica `exampleserver` perché viene ottenuto dalla risorsa padre.

Il formato del tipo della risorsa figlio è: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

Il formato del nome della risorsa figlio è: `{parent-resource-name}/{child-resource-name}`

Tuttavia, non è necessario definire il database all'interno del server. È possibile definire la risorsa figlio al primo livello. Questo approccio potrebbe essere usato se la risorsa padre non viene distribuita nello stesso modello o se si vuole usare `copy` per creare più risorse figlio. Con questo approccio, è necessario specificare il tipo di risorsa completo e includere il nome della risorsa padre nel nome della risorsa figlio.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Quando si crea un riferimento completo a una risorsa, l'ordine di combinazione dei segmenti dal tipo e dal nome non è semplicemente una concatenazione dei due elementi.  Dopo lo spazio dei nomi, usare invece una sequenza di coppie *tipo/nome* dal meno specifico al più specifico:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Ad esempio: 

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` è corretto `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` non è corretto

## <a name="recommendations"></a>Raccomandazioni
Le informazioni seguenti possono essere utili quando si usano le risorse:

* Specificare **comments** per ogni risorsa nel modello per consentire ad altri collaboratori di comprendere lo scopo della risorsa:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Se si usa un *endpoint pubblico* nel modello, come ad esempio un endpoint pubblico di archiviazione BLOB di Azure, *non impostare come hardcoded* lo spazio dei nomi. Usare la funzione **reference** per recuperare lo spazio dei nomi in modo dinamico. Questo approccio consente di distribuire il modello in ambienti diversi dello spazio dei nomi pubblico senza dover modificare manualmente l'endpoint nel modello. Impostare la versione dell'API sulla stessa versione in uso per l'account di archiviazione nel modello:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Se l'account di archiviazione viene distribuito nello stesso modello creato, non è necessario specificare lo spazio dei nomi del provider quando si fa riferimento alla risorsa. L'esempio seguente mostra la sintassi semplificata:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Se nel modello sono presenti altri valori configurati per usare uno spazio dei nomi pubblico, modificarli in modo da riflettere la stessa funzione **reference**. Ad esempio, è possibile impostare la proprietà **storageUri** del profilo di diagnostica della macchina virtuale:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   È anche possibile fare riferimento a un account di archiviazione in un gruppo di risorse diverso:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Assegnare indirizzi IP pubblici a una macchina virtuale solo se richiesto per un'applicazione. Per connettersi a una macchina virtuale (VM) per il debug o per la gestione o a scopi amministrativi, usare le regole NAT in ingresso, un gateway di rete virtuale o un jumpbox.
   
     Per altre informazioni sulla connessione alle macchine virtuali, vedere:
   
   * [Eseguire macchine virtuali per un'architettura a più livelli in Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Configurare l'accesso WinRM per le macchine virtuali in Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Consentire l'accesso esterno alla macchina virtuale tramite il portale di Azure](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Consentire l'accesso esterno alla macchina virtuale tramite PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Consentire l'accesso esterno alla macchina virtuale Linux tramite l'interfaccia della riga di comando di Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* La proprietà **domainNameLabel** deve essere univoca per gli indirizzi IP pubblici. Il valore **domainNameLabel** deve avere una lunghezza compresa tra 3 e 63 caratteri e seguire le regole specificate dall'espressione regolare seguente: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Dato che la funzione **uniqueString** genera una stringa lunga 13 caratteri, il parametro **dnsPrefixString** non deve superare 50 caratteri:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Quando si aggiunge una password a un'estensione di script personalizzato, usare la proprietà **commandToExecute** nella proprietà **protectedSettings**:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Per garantire che i segreti passati come parametri a macchine virtuali ed estensioni siano crittografati, usare la proprietà **protectedSettings** delle estensioni pertinenti.
   > 
   > 


## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare modelli completi per molti tipi diversi di soluzioni, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
* Per informazioni dettagliate sulle funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
* Per unire più modelli durante la distribuzione, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Potrebbe essere necessario usare le risorse esistenti all'interno di un gruppo di risorse diverso. Questo scenario è comune quando si usano account di archiviazione o reti virtuali condivisi tra più gruppi di risorse. Per altre informazioni, vedere la [funzione resourceId](resource-group-template-functions-resource.md#resourceid).
* Per informazioni sulle restrizioni relative ai nomi di risorse, vedere [Recommended naming conventions for Azure resources](../guidance/guidance-naming-conventions.md)(Convenzioni di denominazione consigliate per le risorse di Azure).