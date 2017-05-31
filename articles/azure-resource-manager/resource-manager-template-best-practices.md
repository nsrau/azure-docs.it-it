---
title: Procedure consigliate per la creazione di modelli di Resource Manager| Microsoft Docs
description: Linee guida per la semplificazione dei modelli di Azure Resource Manager.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 31b10deb-0183-47ce-a5ba-6d0ff2ae8ab3
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 24c2b27948e9373bc3957f706ed802cc36c04148
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="best-practices-for-creating-azure-resource-manager-templates"></a>Procedure consigliate per la creazione di modelli di Azure Resource Manager
Le linee guida seguenti consentono di creare modelli di Azure Resource Manager affidabili e facili da usare; sono quindi da considerare solo come suggerimenti e non come requisiti assoluti, salvo diversa indicazione. I diversi scenari potrebbero richiedere una variazione rispetto agli approcci o esempi seguenti.

## <a name="resource-names"></a>Nomi di risorse
In genere vengono usati tre tipi di nomi di risorse in Resource Manager:

* Nomi di risorse che devono essere univoci.
* Nomi di risorse che non devono necessariamente essere univoci, ma che si desidera rendano possibile l'identificazione di una risorsa in base al contesto.
* Nomi di risorse che possono essere generici.

Per informazioni sulla creazione di una convenzione di denominazione, vedere le [linee guida sulla denominazione di un'infrastruttura di Azure](../virtual-machines/windows/infrastructure-naming-guidelines.md). Per informazioni sulle restrizioni relative ai nomi di risorse, vedere [Recommended naming conventions for Azure resources](../guidance/guidance-naming-conventions.md)(Convenzioni di denominazione consigliate per le risorse di Azure).

### <a name="unique-resource-names"></a>Nomi di risorse univoci
È necessario fornire un nome univoco per qualsiasi tipo di risorsa con un endpoint di accesso ai dati. Alcuni tipi di risorse comuni che richiedono un nome univoco includono:

* Archiviazione di Azure<sup>1</sup> 
* Funzionalità app Web del servizio app di Azure
* SQL Server
* Insieme di credenziali chiave Azure
* Cache Redis di Azure
* Azure Batch
* Gestione traffico di Azure
* Ricerca di Azure
* HDInsight di Azure

<sup>1</sup> I nomi di account di archiviazione devono essere formati da lettere minuscole, un massimo di 24 caratteri e non devono includere alcun segno meno.

Se si specifica un parametro per un nome di risorsa, è necessario indicare un nome univoco durante la distribuzione. In alternativa, è possibile creare una variabile che usi la funzione [uniqueString()](resource-group-template-functions-string.md#uniquestring) per generare un nome. 

È spesso opportuno aggiungere un prefisso o un suffisso al risultato di **uniqueString**. La modifica del nome univoco consente di identificare più facilmente il tipo di risorsa in base al nome. Ad esempio, è possibile generare un nome univoco per un account di archiviazione usando la variabile seguente:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Nomi di risorse per l'identificazione
Si tratta di risorse cui si desidera attribuire un nome, ma di cui non è necessario garantire l'univocità. Per questi tipi di risorse, è sufficiente indicare un nome che identifichi il contesto e il tipo di risorsa. È opportuno attribuire un nome descrittivo che ne faciliti il riconoscimento in un elenco di risorse. Se è necessario modificare il nome della risorsa durante le distribuzioni, usare un parametro per il nome:

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

Se non è necessario modificare il nome durante la distribuzione, usare una variabile: 

```json
"variables": {
    "vmName": "demoLinuxVM"
}
```

In alternativa si può usare un valore hardcoded:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "demoLinuxVM",
  ...
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

## <a name="parameters"></a>parameters
Le informazioni seguenti possono essere utili quando si usano parametri:

* Ridurre al minimo l'uso di parametri. Se possibile, usare una variabile o un valore letterale. Specificare parametri solo per questi scenari:
   
   * Impostazioni da variare in base all'ambiente (ad esempio SKU, dimensioni o capacità).
   * Nomi di risorse da specificare per facilitare l'identificazione.
   * Valori usati spesso per completare altre attività (ad esempio nome utente amministratore).
   * Segreti (ad esempio password).
   * Il numero o la matrice di valori da usare durante la creazione di più istanze di un tipo di risorsa.
* Usare la notazione Camel per i nomi dei parametri.
* Indicare una descrizione nei metadati per ogni parametro:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Definire i valori predefiniti per i parametri (ad eccezione delle password e delle chiavi SSH):
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Usare **SecureString** per tutte le password e i segreti: 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Quando possibile, evitare di usare un parametro per specificare la posizione. Usare invece la proprietà **location** del gruppo di risorse. Usando l'espressione **resourceGroup().location** per tutte le risorse, le risorse nel modello verranno distribuite nella stessa posizione del gruppo di risorse:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   Se un tipo di risorsa è supportato solo in un numero limitato di posizioni, provare a specificare una posizione valida direttamente nel modello. Se è necessario usare un parametro **location**, condividere per quanto possibile il relativo valore con le risorse che potrebbero essere nella stessa posizione. Questo approccio permette di ridurre al minimo il numero di volte in cui gli utenti devono dare informazioni sulla posizione.
* Evitare di usare un parametro o una variabile per la versione dell'API per un tipo di risorsa. I valori e le proprietà delle risorse possono variare in base al numero di versione. Quando la versione dell'API è impostata su un parametro o una variabile, IntelliSense negli editor di codice non può determinare lo schema corretto. Impostare invece la versione dell'API come hardcoded nel modello.

## <a name="variables"></a>variables
Le informazioni seguenti possono essere utili quando si usano variabili:

* Usare le variabili per i valori da usare più volte in un modello. Se un valore viene usato una sola volta, un valore hardcoded facilita la lettura del modello.
* Non è possibile usare la funzione [reference](resource-group-template-functions-resource.md#reference) nella sezione **variables** del modello. La funzione **reference** deriva il proprio valore dallo stato di runtime della risorsa, ma le variabili vengono risolte durante l'analisi iniziale del modello. Costruire invece valori che richiedono la funzione **reference** direttamente nella sezione **resources** o **outputs** del modello.
* Includere le variabili per i nomi di risorse che devono essere univoci, come illustrato in [Nomi di risorse](#resource-names).
* È possibile raggruppare le variabili in oggetti complessi. È possibile fare riferimento a un valore da un oggetto complesso nel formato **variable.subentry**. Il raggruppamento delle variabili consente di tenere traccia delle variabili correlate e migliora la leggibilità del modello. Ad esempio:
   
   ```json
   "variables": {
       "storage": {
           "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
           "type": "Standard_LRS"
       }
   },
   "resources": [
     {
         "type": "Microsoft.Storage/storageAccounts",
         "name": "[variables('storage').name]",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "sku": {
             "name": "[variables('storage').type]"
         },
         ...
     }
   ]
   ```
   
   > [!NOTE]
   > Un oggetto complesso non può contenere un'espressione che fa riferimento a un valore da un oggetto complesso. A questo scopo, definire una variabile separata.
   > 
   > 
   
     Per esempi avanzati di uso di oggetti complessi come variabili, vedere [Condividere lo stato tra modelli di Azure Resource Manager](best-practices-resource-manager-state.md).

## <a name="resources"></a>Risorse
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

* È possibile usare i tag per aggiungere metadati alle risorse. Usare i metadati per aumentare le informazioni sulle risorse. Ad esempio, è possibile aggiungere metadati per registrare i dettagli di fatturazione di una risorsa. Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](resource-group-using-tags.md).
* Se si usa un *endpoint pubblico* nel modello, come ad esempio un endpoint pubblico di archiviazione BLOB di Azure, *non impostare come hardcoded* lo spazio dei nomi. Usare la funzione **reference** per recuperare lo spazio dei nomi in modo dinamico. Questo approccio consente di distribuire il modello in ambienti diversi dello spazio dei nomi pubblico senza dover modificare manualmente l'endpoint nel modello. Impostare la versione dell'API sulla stessa versione in uso per l'account di archiviazione nel modello:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Se l'account di archiviazione viene distribuito nello stesso modello creato, non è necessario specificare lo spazio dei nomi del provider quando si fa riferimento alla risorsa. La sintassi semplificata è:
   
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

## <a name="outputs"></a>outputs
Se viene usato un modello per creare indirizzi IP pubblici, deve includere una sezione **outputs** che restituisca i dettagli dell'indirizzo IP e del nome di dominio completo (FQDN). Questi valori di output consentiranno di recuperare facilmente i dettagli sugli indirizzi IP pubblici e sugli FQDN dopo la distribuzione. Quando si fa riferimento alla risorsa, usare la versione dell'API impiegata per crearla: 

```json
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## <a name="single-template-vs-nested-templates"></a>Modello singolo o modelli annidati
Per distribuire la soluzione, è possibile usare un modello singolo o un modello principale con più modelli annidati. I modelli annidati sono comuni per scenari più avanzati. L'uso di un modello annidato presenta i vantaggi seguenti:

* È possibile scomporre la soluzione in componenti di destinazione.
* È possibile riusare i modelli annidati con modelli principali diversi.

Quando si decide di scomporre la progettazione del modello in più modelli annidati, le linee guida seguenti ne consentono la standardizzazione. Queste linee guida si basano sui [criteri di progettazione per modelli di Azure Resource Manager](best-practices-resource-manager-design-templates.md). La progettazione consigliata include i modelli seguenti:

* **Modello principale** (azuredeploy.json). Da usare per i parametri di input.
* **Modello di risorse condivise**. Da usare per distribuire le risorse condivise da tutte le altre risorse (ad esempio, rete virtuale e set di disponibilità). L'espressione **dependsOn** deve essere usata per assicurarsi che questo modello venga distribuito prima degli altri.
* **Modello di risorse facoltative**. Da usare per distribuire risorse in modo condizionale in base a un parametro, ad esempio un jumpbox.
* **Modello di risorse membro**. Ogni tipo di istanza all'interno di un livello di applicazione prevede una propria configurazione. All'interno di un livello, è possibile definire diversi tipi di istanza. Ad esempio, la prima istanza crea un cluster mentre le altre vengono aggiunte al cluster esistente. Ogni tipo di istanza avrà un proprio modello di distribuzione.
* **Script**. Per ogni tipo di istanza sono applicabili script ampiamente riutilizzabili, come ad esempio quelli di inizializzazione e formattazione di dischi aggiuntivi. Gli script personalizzati vengono creati per scopi di personalizzazione specifici e variano in base al tipo di istanza.

![Modello annidato](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

Per altre informazioni, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).

## <a name="conditionally-link-to-nested-templates"></a>Collegarsi in modo condizionale al modello annidato
È possibile collegarsi in modo condizionale ai modelli annidati usando un parametro che diventa parte dell'URI per il modello:

```json
"parameters": {
    "newOrExisting": {
        "type": "String",
        "allowedValues": [
            "new",
            "existing"
        ]
    }
},
"variables": {
    "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
},
"resources": [
    {
        "apiVersion": "2015-01-01",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "[variables('templatelink')]",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
            }
        }
    }
]
```

## <a name="template-format"></a>Formato del modello
È consigliabile passare il modello tramite un validator JSON. Un validator può aiutare a rimuovere virgole, parentesi e parentesi quadre estranee che potrebbero causare un errore durante la distribuzione. Provare [JSONlint](http://jsonlint.com/) o un pacchetto linter per l'ambiente di modifica preferito (Visual Studio Code, Atom, Sublime Text, Visual Studio).

È anche consigliabile formattare il codice JSON per una migliore leggibilità. È possibile usare un pacchetto formattatore JSON per l'editor locale. In Visual Studio formattare il documento con **Ctrl+K, Ctrl+D**. In Visual Studio Code, usare **Alt+Shift+F**. Se l'editor locale non formatta il documento, è possibile usare un [formattatore online](https://www.bing.com/search?q=json+formatter).

## <a name="next-steps"></a>Passaggi successivi
* Per indicazioni sull'architettura della soluzione per le macchine virtuali, vedere [Run a Windows VM in Azure](../guidance/guidance-compute-single-vm.md) (Eseguire una macchina virtuale Windows in Azure) e [Run a Linux VM in Azure](../guidance/guidance-compute-single-vm-linux.md) (Eseguire una macchina virtuale Linux in Azure).
* Per indicazioni sulla configurazione di un account di archiviazione, vedere l'[elenco di controllo di prestazioni e scalabilità per Archiviazione di Azure](../storage/storage-performance-checklist.md).
* Per assistenza con le reti virtuali, vedere le [linee guida sull'infrastruttura di rete](../virtual-machines/windows/infrastructure-networking-guidelines.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Scaffold Azure enterprise: governance prescrittiva per le sottoscrizioni](resource-manager-subscription-governance.md).


