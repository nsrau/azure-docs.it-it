<properties
   pageTitle="Definizione delle dipendenze nei modelli di gestione risorse di Azure"
   description="Viene descritto come impostare una risorsa come dipendente da un'altra risorsa durante la distribuzione."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="mmercuri"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2015"
   ms.author="mmercuri"/>

# Definizione delle dipendenze nei modelli di gestione risorse di Azure

Per una determinata risorsa, possono essere presenti diverse dipendenze a monte e dipendenze figlio che sono fondamentali per il successo della topologia. È possibile definire le dipendenze in altre risorse utilizzando**dependsOn**e proprietà**risorse**di una risorsa. È inoltre possibile specificare una dipendenza utilizzando il**riferimento**funzione.

    {
        "name": "<name-of-the-resource>",
        "type": "<resource-provider-namespace/resource-type-name>",
        "apiVersion": "<supported-api-version-of-resource>",
        "location": "<location-of-resource>",
        "tags": { <name-value-pairs-for-resource-tagging> },
        "dependsOn": [ <array-of-related-resource-names> ],
        "properties": { <settings-for-the-resource> },
        "resources": { <dependent-resources> }
    }

 Sono disponibili collegamenti a risorse che è possono definire relazioni tra le risorse ed un supporto per la definizione di queste relazioni tra gruppi di risorse.

## dependsOn

Per una determinata macchina virtuale, potrebbe essere necessario dipendente da una risorsa del database eseguito correttamente il provisioning. In altri casi, potrebbe essere dipendente da più nodi del cluster per essere installato prima di distribuire una macchina virtuale con lo strumento di gestione del cluster.

All'interno del modello, la proprietà dependsOn fornisce la possibilità di definire questa dipendenza per una risorsa. Il valore può essere un elenco delimitato da virgole di nomi di risorse. Le dipendenze tra risorse vengono valutate e le risorse vengono distribuite in base all'ordine di dipendenza. Quando le risorse non sono interdipendenti, si cerca di distribuirle in parallelo.

Mentre si potrebbe pensare di utilizzare dependsOn per mappare le dipendenze tra le risorse, è importante comprendere il motivo per cui si esegue tale operazione perché può ridurre le prestazioni della distribuzione. Ad esempio, se si procede a questa operazione perché si desidera documentare come le risorse sono interconnesse, dependsOn non è l'approccio giusto. Il Ciclo di vita di dependsOn è solo per la distribuzione e non è disponibile post-distribuzione. Una volta distribuita non è possibile eseguire una query di queste dipendenze. Utilizzando dependsOn si corre il rischio di influire sulle prestazioni e si potrebbe inavvertitamente distrarre il motore di distribuzione da cui potrebbero essere altrimenti utilizzando il parallelismo. Per documentare e fornire query capabililty delle relazioni tra le risorse, è necessario utilizzare[collegamento delle risorse](resource-group-link-resources.md).

Questo elemento non è necessario se la funzione di riferimento viene utilizzata per ottenere una rappresentazione di una risorsa in quanto un oggetto di riferimento implica una dipendenza sulla risorsa. Infatti, se è presente un'opzione per utilizzare un riferimento rispetto a dependsOn, fare riferimento alle linee guida consiste per utilizzare la funzione di riferimento e con riferimenti impliciti. La logica di riferimento è nuovamente delle prestazioni. I Riferimenti definiscono le dipendenze implicite che sono note per essere richieste come riferite all'interno del modello. Con la loro presenza, sono rilevanti, evitando nuovamente l’ottimizzazione delle prestazioni e per evitare il rischio potenziale di distrarre il motore di distribuzione dall’evitare inutilmente un parallelismo.

## resources

La proprietà delle risorse consente di specificare le risorse figlio correlate alla risorsa definita. Le Risorse figlio possono essere solo definite da5 livelli. È importante notare che una relazione implicita non viene creata tra una risorsa figlio e la risorsa padre. Se è necessario che la risorsa figlio sia distribuita dopo la risorsa padre, è necessario dichiarare in modo esplicito tale dipendenza con la proprietà dependsOn.

## funzione di riferimento

La funzione di riferimento consente un'espressione per derivare il valore da altri nomi JSON e coppie valore o risorse di runtime. Le Espressioni di riferimento in modo implicito dichiarano che una risorsa dipende da un altro. La proprietà rappresentata dal**propertyPath** allegato di seguito è facoltativa, se non è specificato, il riferimento è alla risorsa.

    reference('resourceName').propertyPath

È possibile utilizzare questo elemento o l'elemento dependsOn per specificare le dipendenze, ma non è necessario utilizzare entrambi per la stessa risorsa dipendente. Le linee guida prevedono l’utilizzo del riferimento implicito per evitare il rischio di avere inavvertitamente un elemento dependsOn che arresti il motore di distribuzione tanto che gli aspetti della distribuzione in parallelo siano poi interrotti.

Per ulteriori informazioni, vedere[riferimento a funzione](../resource-group-template-functions/#reference).

## Passaggi successivi

- Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md). 
- Per un elenco di funzioni disponibili in un modello, vedere[funzioni modello](resource-group-template-functions.md).

<!---HONumber=August15_HO6-->