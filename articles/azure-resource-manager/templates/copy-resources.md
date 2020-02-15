---
title: Distribuire più istanze di risorse
description: Usare l'operazione di copia e le matrici in un modello di Azure Resource Manager per distribuire il tipo di risorsa molte volte.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: 38b5bcd38e0dc8ba8c758e9aa8371857541ba55e
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210827"
---
# <a name="resource-iteration-in-azure-resource-manager-templates"></a>Iterazione delle risorse nei modelli di Azure Resource Manager

Questo articolo illustra come creare più di un'istanza di una risorsa nel modello di Azure Resource Manager. Aggiungendo l'elemento **Copy** alla sezione Resources del modello, è possibile impostare dinamicamente il numero di risorse da distribuire. È anche possibile evitare di ripetere la sintassi del modello.

È anche possibile usare copia con [Proprietà](copy-properties.md) e [variabili](copy-variables.md).

Se è necessario specificare se una risorsa viene distribuita, vedere l'[elemento condizionale](conditional-resource-deployment.md).

## <a name="resource-iteration"></a>Iterazione delle risorse

Il formato generale dell'elemento Copy è il seguente:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

La proprietà **Name** è qualsiasi valore che identifica il ciclo. La proprietà **count** specifica il numero di iterazioni desiderate per il tipo di risorsa.

Usare le proprietà **mode** e **BatchSize** per specificare se le risorse vengono distribuite in parallelo o in sequenza. Queste proprietà sono descritte in [serie o in parallelo](#serial-or-parallel).

Nell'esempio seguente viene creato il numero di account di archiviazione specificato nel parametro **storageCount** .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

Si noti che il nome di ogni risorsa include la funzione `copyIndex()` che restituisce l'iterazione corrente nel ciclo. `copyIndex()` è in base zero. Quindi l'esempio seguente:

```json
"name": "[concat('storage', copyIndex())]",
```

Crea questi nomi:

* storage0
* storage1
* storage2

Per eseguire l'offset del valore di indice, è possibile passare un valore nella funzione copyIndex(). Il numero di iterazioni è ancora specificato nell'elemento Copy, ma il valore di copyIndex è offset in base al valore specificato. Quindi l'esempio seguente:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Crea questi nomi:

* storage1
* storage2
* storage3

L'operazione di copia è utile quando si lavora con le matrici in quanto è possibile iterare ogni elemento della matrice. Usare la funzione `length` nella matrice per specificare il conteggio per le iterazioni e `copyIndex` per recuperare l'indice corrente nella matrice.

Nell'esempio seguente viene creato un account di archiviazione per ogni nome specificato nel parametro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="serial-or-parallel"></a>Seriale o parallela

Per impostazione predefinita, Gestione risorse crea le risorse in parallelo. Non viene applicato alcun limite al numero di risorse distribuite in parallelo, oltre al limite totale di 800 risorse nel modello. L'ordine di creazione non è garantito.

Tuttavia è consigliabile specificare che le risorse vengano distribuite in sequenza. Ad esempio, quando si aggiorna un ambiente di produzione, è consigliabile sfalsare gli aggiornamenti per aggiornarne solo un determinato numero in un dato momento. Per distribuire in modo seriale più istanze di una risorsa, impostare `mode` su **serial** e `batchSize` sul numero di istanze da distribuire contemporaneamente. Con la modalità seriale, Resource Manager crea una dipendenza da istanze precedenti nel ciclo in modo un batch venga avviato solo dopo il completamento del batch precedente.

Ad esempio, per distribuire in modo seriale gli account di archiviazione due alla volta, usare:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

La proprietà mode accetta anche **parallel**, che è il valore predefinito.

## <a name="depend-on-resources-in-a-loop"></a>In base alle risorse in un ciclo

L'elemento `dependsOn` consente di specificare che una risorsa sia distribuita dopo un'altra. Per distribuire una risorsa che dipende dalla raccolta di risorse in un ciclo, usare il nome del ciclo di copia nell'elemento dependsOn. Nell'esempio seguente viene illustrato come distribuire tre account di archiviazione prima di distribuire la macchina virtuale. La definizione completa della macchina virtuale non viene visualizzata. Si noti che il nome dell'elemento Copy è impostato su `storagecopy` e anche l'elemento dependsOn per la macchina virtuale è impostato su `storagecopy`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="iteration-for-a-child-resource"></a>Iterazione di una risorsa figlio

Non è possibile usare un ciclo di copia per una risorsa figlio. Per creare più istanze di una risorsa cosiddetta "annidata" all'interno di un'altra risorsa è invece necessario creare tale risorsa come una risorsa di livello superiore. La relazione con la risorsa padre si definisce con le proprietà type e name.

Si supponga, ad esempio, di definire in genere un set di dati come una risorsa figlio all'interno di una data factory.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Per creare più set di dati, spostarlo all'esterno della data factory. Il set di dati deve essere sullo stesso livello della data factory, di cui è comunque una risorsa figlio. La relazione fra set di dati e data factory viene mantenuta con le proprietà type e name. Poiché non è possibile dedurre il tipo dalla sua posizione nel modello, è necessario specificarne il nome completo nel formato: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Per stabilire una relazione padre/figlio con un'istanza della data factory, specificare il nome del set di dati che include il nome della risorsa padre. Usare il formato: `{parent-resource-name}/{child-resource-name}`.

Nell'esempio seguente viene descritta l'implementazione:

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="copy-limits"></a>Limiti di copia

Il conteggio non può essere maggiore di 800.

Il conteggio non può essere un numero negativo. Se si distribuisce un modello con Azure PowerShell 2,6 o versioni successive, l'interfaccia della riga di comando di Azure 2.0.74 o versione successiva o l'API REST versione **2019-05-10** o successiva, è possibile impostare Count su zero. Le versioni precedenti di PowerShell, l'interfaccia della riga di comando e l'API REST non supportano zero per Count.

Prestare attenzione quando si usa la [distribuzione in modalità completa](deployment-modes.md) con Copy. Se si esegue la ridistribuzione con la modalità completa in un gruppo di risorse, tutte le risorse non specificate nel modello dopo la risoluzione del ciclo di copia verranno eliminate.

## <a name="example-templates"></a>Modelli di esempio

Gli esempi seguenti mostrano alcuni scenari comuni per la creazione di più istanze di una risorsa o proprietà.

|Modello  |Descrizione  |
|---------|---------|
|[Copia risorsa di archiviazione](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Distribuisce più account di archiviazione con un numero di indice nel nome. |
|[Copia seriale risorse di archiviazione](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Distribuisce più account di archiviazione uno alla volta. Il nome include il numero di indice. |
|[Copia risorsa di archiviazione con matrice](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Distribuisce più account di archiviazione. Il nome include un valore di una matrice. |
|[Distribuzione VM con un numero variabile di dischi dati](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Distribuisce più dischi dati con una macchina virtuale. |
|[Più regole di sicurezza](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Distribuisce più regole di sicurezza a un gruppo di sicurezza di rete. Costruisce le regole di sicurezza da un parametro. Per il parametro, vedere il [file di parametri NSG multipli](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire un'esercitazione, vedere [Tutorial: create multiple resource instances using Resource Manager templates](template-tutorial-create-multiple-instances.md) (Esercitazione: Creare più istanze di risorse usando i modelli di Resource Manager).
* Per altri usi dell'elemento Copy, vedere [iterazione delle proprietà in modelli Azure Resource Manager](copy-properties.md) e [iterazione delle variabili nei modelli Azure Resource Manager](copy-variables.md).
* Per informazioni sull'uso di Copy con i modelli annidati, vedere [uso di Copy](linked-templates.md#using-copy).
* Per altre informazioni sulle sezioni di un modello, vedere [Authoring Azure Resource Manager Templates](template-syntax.md) (Creazione di modelli di Azure Resource Manager).
* Per altre informazioni sulla distribuzione di modelli, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](deploy-powershell.md).

