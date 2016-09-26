<properties
   pageTitle="Dipendenze nei modelli di Resource Manager | Microsoft Azure"
   description="Descrive come impostare una risorsa come dipendente da un'altra risorsa durante la distribuzione per garantire che le risorse vengano distribuite nell'ordine corretto."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# Definizione delle dipendenze nei modelli di gestione risorse di Azure

Perché una risorsa possa essere distribuita, potrebbe essere necessario che prima di essa esistano altre risorse specifiche. Ad esempio, un server SQL deve esistere prima che si tenti di distribuire un database SQL. Per definire questa relazione, si contrassegna una risorsa come dipendente dall'altra risorsa. In genere, una dipendenza viene definita con l'elemento **dependsOn**, ma è anche possibile definirla tramite la funzione **reference**.

Resource Manager valuta le dipendenze tra le risorse e le distribuisce in base all'ordine di dipendenza. Quando le risorse non sono interdipendenti, Resource Manager le distribuisce in parallelo.

## dependsOn

All'interno del modello, l'elemento dependsOn consente di definire una risorsa come dipendente da una o più risorse. Il valore può essere un elenco delimitato da virgole di nomi di risorse.

L'esempio seguente illustra un set di scalabilità di macchine virtuali dipendente da un servizio di bilanciamento del carico e da una rete virtuale. L'esempio illustra anche un ciclo che crea più account di archiviazione. Queste altre risorse non sono visualizzate nell'esempio seguente, ma devono esistere in un altro punto del modello.

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

Per definire una dipendenza tra una risorsa e le risorse che vengono create tramite un ciclo di copia, impostare l'elemento dependsOn sul nome del ciclo. Per avere un esempio, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).

Mentre si potrebbe pensare di usare dependsOn per mappare le relazioni tra le risorse, è importante comprendere il motivo per cui si esegue tale operazione perché può ridurre le prestazioni della distribuzione. Ad esempio, per documentare come le risorse sono interconnesse, dependsOn non è l'approccio giusto. Non è possibile eseguire query su quali risorse sono state definite nell'elemento dependsOn dopo la distribuzione. L'uso di dependsOn potrebbe influire sul tempo necessario per la distribuzione perché Resource Manager non esegue la distribuzione simultanea di due risorse con dipendenza. Per documentare le relazioni tra le risorse, usare il [collegamento di risorse](resource-group-link-resources.md).

## Risorse figlio

La proprietà delle risorse consente di specificare le risorse figlio correlate alla risorsa definita. Le risorse figlio possono essere solo definite da cinque livelli. È importante notare che una relazione implicita non viene creata tra una risorsa figlio e la risorsa padre. Se è necessario che la risorsa figlio sia distribuita dopo la risorsa padre, è necessario dichiarare in modo esplicito tale dipendenza con la proprietà dependsOn.

Ogni risorsa padre accetta solo determinati tipi di risorse come risorse figlio. I tipi di risorse accettate sono specificati nel [schema del modello](https://github.com/Azure/azure-resource-manager-schemas) della risorsa padre. Il nome del tipo di risorsa figlio include il nome del tipo di risorsa padre, ad esempio **Microsoft.Web/sites/config** e **Microsoft.Web/sites/extensions** sono entrambi risorse figlio di **Microsoft.Web/sites**.

Nell'esempio seguente sono illustrati un server SQL e un database SQL. Si noti che viene definita una dipendenza esplicita tra il database SQL e il server SQL, anche se il database è un elemento figlio del server.

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## funzione di riferimento

La [funzione di riferimento](resource-group-template-functions.md#reference) consente un'espressione per derivare il valore da altri nomi JSON e coppie valore o risorse di runtime. Le Espressioni di riferimento in modo implicito dichiarano che una risorsa dipende da un altro.

    reference('resourceName').propertyPath

È possibile utilizzare questo elemento o l'elemento dependsOn per specificare le dipendenze, ma non è necessario utilizzare entrambi per la stessa risorsa dipendente. Quando possibile, usare un riferimento implicito per evitare di aggiungere inavvertitamente una dipendenza non necessaria.

Per altre informazioni, vedere la [funzione del riferimento](resource-group-template-functions.md#reference).

## Passaggi successivi

- Per informazioni sulla creazione di modelli di Gestione risorse di Azure, vedere [Creazione di modelli](resource-group-authoring-templates.md).
- Per un elenco delle funzioni disponibili in un modello, vedere [Funzioni di modelli](resource-group-template-functions.md).

<!---HONumber=AcomDC_0914_2016-->