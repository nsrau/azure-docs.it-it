---
title: Impostare l&quot;ordine di distribuzione per le risorse di Azure | Documentazione Microsoft
description: Descrive come impostare una risorsa come dipendente da un&quot;altra risorsa durante la distribuzione per garantire che le risorse vengano distribuite nell&quot;ordine corretto.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 5254ea3f072402e2fa4cffcdc23b2e9eae2aad57


---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Definire l'ordine per la distribuzione delle risorse nei modelli di Azure Resource Manager
Perché una risorsa possa essere distribuita, potrebbe essere necessario che prima di essa esistano altre risorse specifiche. Ad esempio, un server SQL deve esistere prima che si tenti di distribuire un database SQL. Per definire questa relazione, si contrassegna una risorsa come dipendente dall'altra risorsa. Una dipendenza viene definita con l'elemento **dependsOn** oppure con la funzione **reference**. 

Resource Manager valuta le dipendenze tra le risorse e le distribuisce in base all'ordine di dipendenza. Quando le risorse non sono interdipendenti, Resource Manager le distribuisce in parallelo. La definizione delle dipendenze è necessaria solo per le risorse distribuite nello stesso modello. 

## <a name="dependson"></a>dependsOn
All'interno del modello, l'elemento dependsOn consente di definire una risorsa come dipendente da una o più risorse. Il valore può essere un elenco delimitato da virgole di nomi di risorse. 

L'esempio seguente illustra un set di scalabilità di macchine virtuali dipendente da un servizio di bilanciamento del carico e da una rete virtuale. L'esempio illustra anche un ciclo che crea più account di archiviazione. Queste altre risorse non sono visualizzate nell'esempio seguente, ma devono esistere in un altro punto del modello.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

Nell'esempio precedente è inclusa una dipendenza per le risorse create tramite il ciclo di copia denominato **storageLoop**. Per avere un esempio, vedere [Creare più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).

Quando si definiscono le dipendenze, per evitare ambiguità è possibile includere lo spazio dei nomi del provider di risorse e il tipo di risorsa. Ad esempio, per distinguere un bilanciamento del carico e una rete virtuale che hanno lo stesso nome di altre risorse, usare il formato seguente:

```json
"dependsOn": [
  "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
]
``` 

Anche se si potrebbe essere propensi a usare dependsOn per mappare le relazioni tra le risorse, è importante comprendere il motivo per cui si esegue tale operazione. Ad esempio, per documentare come le risorse sono interconnesse, dependsOn non è l'approccio giusto. Non è possibile eseguire query su quali risorse sono state definite nell'elemento dependsOn dopo la distribuzione. L'uso di dependsOn potrebbe influire sul tempo necessario per la distribuzione perché Resource Manager non esegue la distribuzione simultanea di due risorse con dipendenza. Per documentare le relazioni tra le risorse, usare il [collegamento di risorse](resource-group-link-resources.md).

## <a name="child-resources"></a>Risorse figlio
La proprietà delle risorse consente di specificare le risorse figlio correlate alla risorsa definita. Le risorse figlio possono essere solo definite da cinque livelli. È importante notare che una relazione implicita non viene creata tra una risorsa figlio e la risorsa padre. Se è necessario che la risorsa figlio sia distribuita dopo la risorsa padre, è necessario dichiarare in modo esplicito tale dipendenza con la proprietà dependsOn. 

Ogni risorsa padre accetta solo determinati tipi di risorse come risorse figlio. I tipi di risorse accettate sono specificati nel [schema del modello](https://github.com/Azure/azure-resource-manager-schemas) della risorsa padre. Il nome del tipo di risorsa figlio include il nome del tipo di risorsa padre, ad esempio **Microsoft.Web/sites/config** e **Microsoft.Web/sites/extensions** sono entrambi risorse figlio di **Microsoft.Web/sites**.

Nell'esempio seguente sono illustrati un server SQL e un database SQL. Si noti che viene definita una dipendenza esplicita tra il database SQL e il server SQL, anche se il database è un elemento figlio del server.

```json
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
```

## <a name="reference-function"></a>funzione di riferimento
La [funzione di riferimento](resource-group-template-functions.md#reference) consente un'espressione per derivare il valore da altri nomi JSON e coppie valore o risorse di runtime. Le Espressioni di riferimento in modo implicito dichiarano che una risorsa dipende da un altro. Il formato generale è il seguente:

```json
reference('resourceName').propertyPath
```

Nell'esempio seguente, un endpoint della rete CDN dipende in modo esplicito dal profilo CDN e in modo implicito da un'app Web.

```json
{
    "name": "[variables('endpointName')]",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

È possibile utilizzare questo elemento o l'elemento dependsOn per specificare le dipendenze, ma non è necessario utilizzare entrambi per la stessa risorsa dipendente. Quando possibile, usare un riferimento implicito per evitare di aggiungere una dipendenza non necessaria.

Per altre informazioni, vedere la [funzione del riferimento](resource-group-template-functions.md#reference).

## <a name="recommendations-for-setting-dependencies"></a>Raccomandazioni per l'impostazione delle dipendenze

Quando si decidono le dipendenze da impostare, usare le linee guida seguenti:

* Impostare il minor numero possibile di dipendenze.
* Impostare una risorsa figlio come dipendente dalla risorsa padre.
* Usare la funzione **reference** per impostare dipendenze implicite tra le risorse che devono condividere una proprietà. Non aggiungere una dipendenza esplicita (**dependsOn**) quando è già stata definita una dipendenza implicita. Questo approccio riduce il rischio di creare dipendenze non necessarie. 
* Impostare una dipendenza quando una risorsa non può essere **creata** senza le funzionalità di un'altra risorsa. Non impostare una dipendenza se le risorse interagiscono solo dopo la distribuzione.
* Consentire la propagazione a catena delle dipendenze senza impostarle esplicitamente. Ad esempio, una macchina virtuale dipende da un'interfaccia di rete virtuale e tale interfaccia dipende da una rete virtuale e indirizzi IP pubblici. La macchina virtuale viene quindi distribuita dopo tutte e tre le risorse, ma non deve essere impostata esplicitamente come dipendente da esse. Questo approccio offre chiarezza nell'ordine delle dipendenze e semplifica la successiva modifica del modello.
* Se un valore può essere determinato prima della distribuzione, provare a distribuire le risorse senza una dipendenza. Se un valore di configurazione richiede il nome di un'altra risorsa, ad esempio, potrebbe non essere necessaria una dipendenza. Questa indicazione non è sempre valida perché alcune risorse verificano l'esistenza dell'altra risorsa. Se viene visualizzato un errore, aggiungere una dipendenza. 

Resource Manager identifica le dipendenze circolari durante la convalida del modello. Se viene visualizzato un errore che indica la presenza di una dipendenza circolare, valutare il modello per verificare se esistono dipendenze non necessarie che possono essere rimosse. Se la rimozione delle dipendenze non è applicabile, è possibile evitare le dipendenze circolari spostando alcune operazioni di distribuzione in risorse figlio distribuite dopo le risorse che hanno la dipendenza circolare. Si supponga, ad esempio, di distribuire due macchine virtuali e che sia necessario impostare in ognuna proprietà che fanno riferimento all'altra. È possibile eseguire la distribuzione nell'ordine seguente:

1. VM&1;
2. VM&2;
3. L'estensione in VM&1; dipende da VM&1; e VM&2;. L'estensione imposta in VM&1; valori ottenuti da VM&2;.
4. L'estensione in VM&2; dipende da VM&1; e VM&2;. L'estensione imposta in VM&2; valori ottenuti da VM&1;.

Per informazioni sulla valutazione dell'ordine di distribuzione e la risoluzione degli errori relativi alle dipendenze, vedere [Controllare la sequenza di distribuzione](resource-manager-common-deployment-errors.md#check-deployment-sequence).

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulla risoluzione dei problemi relativi alle dipendenze durante la distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Per informazioni sulla creazione di modelli di Gestione risorse di Azure, vedere [Creazione di modelli](resource-group-authoring-templates.md). 
* Per un elenco delle funzioni disponibili in un modello, vedere [Funzioni di modelli](resource-group-template-functions.md).




<!--HONumber=Jan17_HO4-->


