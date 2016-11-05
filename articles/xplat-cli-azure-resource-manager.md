
---
title: Gestire le risorse con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Usare l'interfaccia della riga di comando di Azure per gestire le risorse e i gruppi di Azure
editor: ''
manager: timlt
documentationcenter: ''
author: dlepow
services: azure-resource-manager

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: danlep

---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Usare l'interfaccia della riga di comando di Azure per gestire risorse e gruppi di risorse
> [!div class="op_single_selector"]
> * [Portale](azure-portal/resource-group-portal.md) 
> * [Interfaccia della riga di comando di Azure](xplat-cli-azure-resource-manager.md)
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [API REST](resource-manager-rest-api.md)
> 
> 

L'interfaccia della riga di comando di Azure è uno degli strumenti che è possibile usare per distribuire e gestire le risorse con Resource Manager. Questo articolo illustra i metodi comuni per gestire risorse e gruppi di risorse di Azure usando l'interfaccia della riga di comando di Azure in modalità Resource Manager. Per informazioni sull'uso dell'interfaccia della riga di comando per distribuire le risorse, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](resource-group-template-deploy-cli.md). Per informazioni sulle risorse di Azure e su Resource Manager, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md).

> [!NOTE]
> Per gestire le risorse di Azure con l'interfaccia della riga di comando di Azure, è necessario [installare l'interfaccia della riga di comando di Azure](xplat-cli-install.md) e [accedere ad Azure](xplat-cli-connect.md) usando il comando `azure login`. Verificare che l'interfaccia della riga di comando sia in modalità Resource Manager eseguendo `azure config mode arm`. Se queste operazioni sono già state eseguite, si è pronti per proseguire.
> 
> 

## <a name="get-resource-groups-and-resources"></a>Ottenere le risorse e i gruppi di risorse
### <a name="resource-groups"></a>Gruppi di risorse
Per ottenere un elenco di tutti i gruppi di risorse della sottoscrizione e delle rispettive posizioni, eseguire questo comando.

    azure group list


### <a name="resources"></a>Risorse
 Per elencare tutte le risorse in un gruppo, ad esempio quello con il nome *testRG*, usare il comando seguente.

    azure resource list testRG

Per visualizzare una singola risorsa nel gruppo, ad esempio una macchina virtuale denominata *MyUbuntuVM*, usare un comando come il seguente.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Notare il parametro **Microsoft.Compute/virtualMachines**. Questo parametro indica il tipo di risorsa per il quale si stanno richiedendo informazioni.

> [!NOTE]
> Quando si usano i comandi **azure resource**, a eccezione di **list**, è necessario specificare la versione dell'API della risorsa con il parametro **-o**. Se non si è certi della versione dell'API, consultare il file modello e trovare il campo apiVersion relativo alla risorsa. Per altre informazioni sulle versioni dell'API in Resource Manager, vedere [Provider, aree, versioni API e schemi di Gestione risorse](resource-manager-supported-services.md).
> 
> 

Quando si visualizzano i dettagli su una risorsa, è spesso utile utilizzare il parametro`--json`. Questo parametro rende l'output più leggibile perché alcuni valori sono strutture annidate o raccolte. L'esempio seguente illustra come restituire i risultati del comando **show** come un documento JSON.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

> [!NOTE]
> Se necessario, salvare i dati JSON nel file usando il carattere &gt; per indirizzare l'output a un file. Ad esempio:
> 
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`
> 
> 

### <a name="tags"></a>Tag
[!INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="manage-resources"></a>Gestire risorse
Per aggiungere una risorsa, ad esempio un account di archiviazione, a un gruppo di risorse, eseguire un comando simile a:

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{\"accountType\": \"Standard_LRS\"}"

Oltre a specificare la versione dell'API della risorsa con il parametro **-o**, usare il parametro **-p** per passare una stringa in formato JSON con le proprietà obbligatorie o aggiuntive.

Per eliminare una risorsa esistente, ad esempio una macchina virtuale, usare un comando simile al seguente.

    azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Per spostare le risorse esistenti in un gruppo di risorse o una sottoscrizione diversa, usare il comando **azure resource move** . Il seguente esempio illustra come spostare una cache Redis in un nuovo gruppo di risorse. Nel parametro **-i** , fornire un elenco delimitato da virgole di id di risorsa da spostare.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## <a name="control-access-to-resources"></a>Controllare l'accesso alle risorse
È possibile usare l'interfaccia della riga di comando di Azure per creare e gestire i criteri per controllare l'accesso alle risorse di Azure. Per informazioni sulle definizioni dei criteri e sull'assegnazione di criteri alle risorse, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).

Ad esempio, definire i criteri seguenti per rifiutare tutte le richieste in cui la località non è Stati Uniti occidentali o Stati Uniti centro-settentrionali e salvarli nel file di definizione dei criteri policy.json:

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

Eseguire quindi il comando **policy definition create**:

    azure policy definition create MyPolicy -p c:\temp\policy.json

Questo comando ha un output simile al seguente.

    + Creating policy definition MyPolicy data:    PolicyName:             MyPolicy data:    PolicyDefinitionId:     /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    data:    PolicyType:             Custom data:    DisplayName:            undefined data:    Description:            undefined data:    PolicyRule:             field=location, in=[westus, northcentralus], effect=deny

 Per assegnare un criterio nell'ambito desiderato, usare il valore di **PolicyDefinitionId** restituito dal comando precedente. Nell'esempio seguente, questo ambito è la sottoscrizione, ma è possibile impostare come ambito gruppi di risorse o singole risorse:

    azure policy assignment create MyPolicyAssignment -p /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/MyPolicy -s /subscriptions/########-####-####-####-############/

È possibile ottenere, modificare o rimuovere le definizioni dei criteri usando i comandi **policy definition show**, **policy definition set** e **policy definition delete**.

Analogamente, è possibile ottenere, modificare o rimuovere le assegnazioni dei criteri usando i comandi **policy assignment show**, **policy assignment set** e **policy assignment delete**.

## <a name="export-a-resource-group-as-a-template"></a>Esportare un gruppo di risorse come modello
È possibile visualizzare il modello di Resource Manager per un gruppo di risorse esistente. L'esportazione del modello offre due vantaggi:

1. È possibile automatizzare le distribuzioni future della soluzione, perché tutti gli elementi dell'infrastruttura sono definiti nel modello.
2. Per acquisire familiarità con la sintassi del modello, esaminare il codice JSON che rappresenta la soluzione.

Usando l'interfaccia della riga di comando di Azure, è possibile esportare un modello che rappresenta lo stato corrente del gruppo di risorse oppure scaricare il modello usato per una distribuzione specifica.

* **Esportare il modello per un gruppo di risorse**: questa operazione è utile quando sono state apportate modifiche a un gruppo di risorse ed è necessario recuperare la rappresentazione JSON del rispettivo stato corrente. Il modello generato, tuttavia, contiene solo un numero minimo di parametri e nessuna variabile. La maggior parte dei valori del modello è hardcoded. Prima di distribuire il modello generato, è possibile che si voglia convertire altri valori in parametri, per potere personalizzare la distribuzione per diversi ambienti.
  
    Per esportare il modello per un gruppo di risorse in una directory locale, eseguire il comando `azure group export`, come illustrato nell'esempio seguente. Usare una directory locale appropriata per l'ambiente del sistema operativo in uso.
  
        azure group export testRG ~/azure/templates/
* **Scaricare il modello per una distribuzione specifica**: questa operazione è utile quando è necessario visualizzare il modello effettivo usato per distribuire le risorse. Il modello include tutte le variabili e tutti i parametri definiti per la distribuzione originale. Se, tuttavia, un utente dell'organizzazione ha modificato il gruppo di risorse in modo diverso dalla definizione nel modello, questo modello non rappresenta lo stato corrente del gruppo di risorse.
  
    Per scaricare in una directory locale il modello usato per una distribuzione specifica, eseguire il comando `azure group deployment template download`. ad esempio:
  
        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/

> [!NOTE]
> La funzionalità di esportazione del modello è disponibile in anteprima e non tutti i tipi di risorse supportano attualmente l'esportazione di un modello. Quando si prova a esportare un modello, è possibile che venga visualizzato un errore che indica che alcune risorse non sono state esportate. Se necessario, definire manualmente queste risorse nel modello dopo averlo scaricato.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* Per ottenere i dettagli delle operazioni di distribuzione e risolvere i problemi relativi agli errori di distribuzione con l'interfaccia della riga di comando di Azure, vedere [Visualizzare le operazioni di distribuzione con l'interfaccia della riga di comando di Azure](resource-manager-troubleshoot-deployments-cli.md).
* Per usare l'interfaccia della riga di comando per configurare un'applicazione o uno script per accedere alle risorse, vedere [Usare l'interfaccia della riga di comando di Azure per creare un'entità servizio per accedere alle risorse](resource-group-authenticate-service-principal-cli.md).

<!--HONumber=Oct16_HO2-->


