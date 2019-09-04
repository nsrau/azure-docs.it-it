---
title: Procedure consigliate per i modelli di Azure Resource Manager
description: Descrive gli approcci consigliati per la creazione di modelli di Azure Resource Manager. Offre suggerimenti per evitare problemi comuni quando si usano i modelli.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2019
ms.author: tomfitz
ms.openlocfilehash: 161539aaec4d3b7162405f437b7fb3dd1f6a00e6
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258840"
---
# <a name="azure-resource-manager-template-best-practices"></a>Procedure consigliate per i modelli di Azure Resource Manager

Questo articolo offre indicazioni su come costruire il modello di Resource Manager. Queste indicazioni consentono di evitare i problemi comuni quando si usa un modello per distribuire una soluzione.

Per indicazioni su come gestire le sottoscrizioni di Azure, vedere [Scaffold Azure enterprise: governance prescrittiva per le sottoscrizioni](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Per indicazioni su come compilare modelli funzionanti in tutti gli ambienti cloud di Azure, vedere [Sviluppare modelli di Azure Resource Manager per la coerenza del cloud](templates-cloud-consistency.md).

## <a name="template-limits"></a>Limiti del modello

Limitare le dimensioni del modello a 4 MB e ogni file di parametri a 64 KB. Il limite di 4 MB si applica allo stato finale del modello dopo che è stato espanso con le definizioni di risorse iterative e i valori per variabili e parametri. 

Esistono anche i limiti seguenti:

* 256 parametri
* 256 variabili
* 800 risorse (incluso il conteggio copie)
* 64 valori di output
* 24.576 caratteri in un'espressione di modello

È possibile superare alcuni limiti del modello usando un modello annidato. Per altre informazioni, vedere [Uso di modelli collegati nella distribuzione di risorse di Azure](resource-group-linked-templates.md). Per ridurre il numero di parametri, variabili o output, è possibile combinare più valori in un oggetto. Per altre informazioni, vedere [Oggetti come parametri](resource-manager-objects-as-parameters.md).

## <a name="resource-group"></a>Gruppo di risorse

Quando si distribuiscono le risorse in un gruppo di risorse, il gruppo di risorse archivia i metadati relativi alle risorse. I metadati vengono archiviati nella posizione del gruppo di risorse.

Se l'area del gruppo di risorse è temporaneamente non disponibile, non è possibile aggiornare le risorse nel gruppo di risorse perché i metadati non sono disponibili. Le risorse in altre aree continueranno a funzionare come previsto, ma non è possibile aggiornarle. Per ridurre il rischio, collocare il gruppo di risorse e le risorse nella stessa area.

## <a name="parameters"></a>Parametri
Le informazioni di questa sezione possono essere utili quando si usano i [parametri](resource-group-authoring-templates.md#parameters).

### <a name="general-recommendations-for-parameters"></a>Raccomandazioni generali per i parametri

* Ridurre al minimo l'uso di parametri. Usare invece i valori letterali o le variabili per le proprietà che non è necessario specificare durante la distribuzione.

* Usare la notazione Camel per i nomi dei parametri.

* Usare i parametri per le impostazioni che variano in base all'ambiente, ad esempio lo SKU, la dimensione o la capacità.

* Usare i parametri per i nomi di risorse da specificare per facilitare l'identificazione.

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

* Definire i valori predefiniti per i parametri non riservati. Specificando un valore predefinito, è più semplice distribuire il modello e gli utenti del modello vedono un esempio di un valore appropriato. Qualsiasi valore predefinito per un parametro deve essere valido per tutti gli utenti nella configurazione della distribuzione predefinita. 
   
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

* Per specificare un parametro facoltativo, non usare una stringa vuota come valore predefinito. Usare invece un valore letterale o un'espressione del linguaggio per costruire un valore.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Non usare un parametro per la versione dell'API per un tipo di risorsa. I valori e le proprietà delle risorse possono variare in base al numero di versione. Quando la versione dell'API è impostata su un parametro, IntelliSense negli editor di codice non può determinare lo schema corretto. Impostare invece la versione dell'API come hardcoded nel modello.

* Usare `allowedValues` solo in casi limitati. Usarlo solo quando è necessario assicurarsi che alcuni valori non vengano inclusi nelle opzioni consentite. Se si usa `allowedValues` troppo diffusamente, si potrebbero bloccare le distribuzioni valide non tenendo aggiornato l'elenco.

* Quando il nome di un parametro nel modello corrisponde a un parametro nel comando di distribuzione di PowerShell, Resource Manager risolve questo conflitto di denominazione aggiungendo il suffisso **FromTemplate** al parametro del modello. Se, ad esempio, si include un parametro denominato **ResourceGroupName** nel modello, si crea un conflitto con il parametro **ResourceGroupName** nel cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Durante la distribuzione verrà quindi richiesto di specificare un valore per **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Raccomandazioni sulla sicurezza per i parametri

* Usare sempre i parametri per i nomi utente e le password (o i segreti).

* Usare `securestring` per tutte le password e i segreti. Se si passano dati sensibili in un oggetto JSON, usare il tipo `secureObject`. Non è possibile leggere i parametri di modello di tipo stringa sicura o oggetto sicuro dopo la distribuzione delle risorse. 
   
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

* Non fornire valori predefiniti per nomi utente, password o valori che richiedono un tipo `secureString`.

* Non fornire valori predefiniti per le proprietà che aumentano la superficie di attacco dell'applicazione.

### <a name="location-recommendations-for-parameters"></a>Raccomandazioni sulla posizione per i parametri

* Usare un parametro per specificare la posizione delle risorse e impostare il valore predefinito su `resourceGroup().location`. Fornire un parametro location consente agli utenti del modello di specificare una posizione in cui sono autorizzati a eseguire la distribuzione.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* Non specificare `allowedValues` per il parametro location. Le posizioni specificate potrebbero non essere disponibili in tutti i cloud.

* Usare il valore del parametro location per le risorse che potrebbero essere nella stessa posizione. Questo approccio permette di ridurre al minimo il numero di volte in cui gli utenti devono dare informazioni sulla posizione.

* Per le risorse che non sono disponibili in tutte le posizioni, usare un parametro distinto oppure specificare un valore letterale per location.

## <a name="variables"></a>Variabili

Le informazioni seguenti possono essere utili quando si usano le [variabili](resource-group-authoring-templates.md#variables):

* Usare il caso Camel per i nomi delle variabili.

* Usare le variabili per i valori da usare più volte in un modello. Se un valore viene usato una sola volta, un valore hardcoded facilita la lettura del modello.

* Usare le variabili per i valori costruiti da una disposizione complessa delle funzioni del modello. È più facile leggere il modello quando l'espressione complessa viene visualizzata solo nelle variabili.

* Non usare le variabili per `apiVersion` in una risorsa. La versione dell'API determina lo schema della risorsa. Spesso non è possibile modificare la versione senza modificare le proprietà della risorsa.

* Non è possibile usare la funzione [reference](resource-group-template-functions-resource.md#reference) nella sezione **variables** del modello. La funzione **reference** deriva il proprio valore dallo stato di runtime della risorsa, ma le variabili vengono risolte durante l'analisi iniziale del modello. Costruire invece valori che richiedono la funzione **reference** direttamente nella sezione **resources** o **outputs** del modello.

* Includere le variabili per i nomi di risorse che devono essere univoci.

* Usare un [ciclo di copia nelle variabili](resource-group-create-multiple.md#variable-iteration) per creare un modello ripetitivo di oggetti JSON.

* Rimuovere le variabili non usate.

## <a name="resource-dependencies"></a>Dipendenze delle risorse

Quando si decidono le [dipendenze](resource-group-define-dependencies.md) da impostare, usare le linee guida seguenti:

* Usare la funzione **reference** passando il nome della risorsa per impostare una dipendenza implicita tra risorse che devono condividere una proprietà. Non aggiungere un elemento `dependsOn` esplicito quando è già stata definita una dipendenza implicita. Questo approccio riduce il rischio di creare dipendenze non necessarie.

* Impostare una risorsa figlio come dipendente dalla risorsa padre.

* Le risorse con l'[elemento condition](conditional-resource-deployment.md) impostato su false vengono automaticamente rimosse dall'ordine di dipendenza. Impostare le dipendenze come se la risorsa venisse sempre distribuita.

* Consentire la propagazione a catena delle dipendenze senza impostarle esplicitamente. Ad esempio, una macchina virtuale dipende da un'interfaccia di rete virtuale e tale interfaccia dipende da una rete virtuale e indirizzi IP pubblici. La macchina virtuale viene quindi distribuita dopo tutte e tre le risorse, ma non deve essere impostata esplicitamente come dipendente da tutte e tre. Questo approccio offre chiarezza nell'ordine delle dipendenze e semplifica la successiva modifica del modello.

* Se un valore può essere determinato prima della distribuzione, provare a distribuire le risorse senza una dipendenza. Se un valore di configurazione richiede il nome di un'altra risorsa, ad esempio, potrebbe non essere necessaria una dipendenza. Questa indicazione non è sempre valida perché alcune risorse verificano l'esistenza dell'altra risorsa. Se viene visualizzato un errore, aggiungere una dipendenza.

## <a name="resources"></a>Risorse

Le informazioni seguenti possono essere utili quando si usano le [risorse](resource-group-authoring-templates.md#resources):

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
   
   Se l'account di archiviazione viene distribuito nello stesso modello creato, non è necessario specificare lo spazio dei nomi del provider quando si fa riferimento alla risorsa. L'esempio seguente viene descritta la sintassi semplificata:
   
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

## <a name="outputs"></a>Output

Se viene usato un modello per creare indirizzi IP pubblici, deve includere una [sezione outputs](resource-group-authoring-templates.md#outputs) che restituisca i dettagli dell'indirizzo IP e del nome di dominio completo (FQDN). Questi valori di output consentiranno di recuperare facilmente i dettagli sugli indirizzi IP pubblici e sugli FQDN dopo la distribuzione.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla struttura del file modello di Resource Manager, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per indicazioni su come compilare modelli funzionanti in tutti gli ambienti cloud di Azure, vedere [Sviluppare modelli di Azure Resource Manager per la coerenza del cloud](templates-cloud-consistency.md).
