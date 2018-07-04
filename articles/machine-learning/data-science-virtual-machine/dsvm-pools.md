---
title: Pool di Data Science Virtual Machine in Azure | Microsoft Docs
description: Distribuzione di pool di Data Science VM come risorsa condivisa per un team
keywords: apprendimento avanzato, AI, strumenti di data science, macchina virtuale per data science, analisi geospaziale, processo di data science del team
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 0740ff7542d066442146b8e80e188ad5ba49a2b5
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309399"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Creare un pool condiviso di Data Science Virtual Machine

Questo articolo illustra come è possibile creare un pool condiviso di Data Science Virtual Machine (DSVM) per l'uso da parte di un team. I vantaggi di usare un pool condiviso sono un migliore utilizzo delle risorse, facilità di condivisione e collaborazione e gestione più efficace delle risorse DSVM. 

È possibile usare numerosi metodi e tecnologie per creare un pool di DSVM. Questo articolo illustra i pool per l'elaborazione batch e le VM interattive.

## <a name="batch-processing-pool"></a>Pool di elaborazione batch
Per configurare un pool di DSVM principalmente per eseguire i processi in un batch offline, è possibile usare il servizio [Azure Batch per intelligenza artificiale](https://docs.microsoft.com/azure/batch-ai/) oppure [Azure Batch](https://docs.microsoft.com/azure/batch/). Questo articolo è incentrato su Azure Batch per intelligenza artificiale.

L'edizione Ubuntu della DSVM è supportata come una delle immagini in Azure Batch per intelligenza artificiale. Nell'interfaccia della riga di comando di Azure o in Python SDK, in cui si crea il cluster Azure Batch per intelligenza artificiale, si può specificare il parametro `image` e impostarlo su `UbuntuDSVM`. È possibile scegliere il tipo di nodi di elaborazione desiderato, ovvero istanze basate su GPU o istanze solo CPU, il numero di CPU e la memoria, dall'[ampia scelta di istanze di VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) disponibile in Azure. 

Quando si usa l'immagine Ubuntu di DSVM in Azure Batch per intelligenza artificiale con nodi basati su GPU, tutti i driver GPU e i framework di apprendimento avanzato necessari sono preinstallati. La preinstallazione consente di risparmiare molto tempo nella preparazione dei nodi batch. Se infatti si sviluppa in una DSVM Ubuntu in modo interattivo, si noterà che i nodi Azure Batch per intelligenza artificiale hanno esattamente la stessa configurazione dell'ambiente. 

In genere quando, si crea un cluster Azure Batch per intelligenza artificiale, si crea anche una condivisione file montata da tutti i nodi. La condivisione file viene usata per l'input e l'output dei dati, oltre che per l'archiviazione del codice e degli script dei processi batch. 

Dopo avere creato un cluster Azure Batch per intelligenza artificiale, è possibile usare la stessa interfaccia della riga di comando o Python SDK per inviare i processi da eseguire. Si paga solo per il tempo impiegato per eseguire i processi batch. 

Per altre informazioni, vedere:
* Procedura dettagliata per l'uso dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) per gestire Azure Batch per intelligenza artificiale
* Procedura dettagliata per l'uso di [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) per gestire Azure Batch per intelligenza artificiale
* [File recipe di Azure Batch per intelligenza artificiale](https://github.com/Azure/BatchAI) che illustrano come usare vari framework di apprendimento avanzato e intelligenza artificiale con Azure Batch per intelligenza artificiale

## <a name="interactive-vm-pool"></a>Pool di VM interattive

Un pool di VM interattive che sono condivise dall'intero team di intelligenza artificiale/data science consente agli utenti di accedere a un'istanza disponibile della DSVM anziché avere un'istanza dedicata per ogni set di utenti. Questa configurazione garantisce una migliore disponibilità e un uso più efficace delle risorse. 

La tecnologia usata per creare un pool di VM interattive si basa sui [set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). È possibile usare i set di scalabilità per creare e gestire un gruppo di VM identiche con bilanciamento del carico e scalabilità automatica. 

L'utente accede all'indirizzo IP o DNS del pool principale. Il set di scalabilità esegue automaticamente il routing della sessione su una DSVM disponibile nel set di scalabilità. Poiché gli utenti vogliono un ambiente simile, indipendentemente dalla VM a cui eseguono l'accesso, tutte le istanze della VM nel set di scalabilità montano un'unità di rete condivisa, ad esempio una condivisione di File di Azure o una condivisione NFS. L'area di lavoro condivisa dell'utente in genere viene mantenuta nell'archivio di file condiviso montato in ognuna delle istanze. 

È possibile trovare un esempio di modello di Azure Resource Manager che crea un set di scalabilità con istanze di DSVM Ubuntu in [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Nello stesso percorso si trova anche un esempio del [file dei parametri](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) per il modello di Azure Resource Manager. 

È possibile creare il set di scalabilità dal modello di Azure Resource Manager specificando i valori per il file dei parametri nell'interfaccia della riga di comando di Azure. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
I comandi precedenti presuppongono che si abbia:
* Una copia del file dei parametri con i valori specificati per l'istanza del set di scalabilità.
* Il numero di istanze di VM.
* Puntatori alla condivisione di File di Azure.
* Credenziali per l'account di archiviazione che verrà montato in ogni VM. 

Nei comandi si fa riferimento in locale al file dei parametri. È anche possibile passare parametri inline o richiedere i parametri nello script.  

Il modello precedente abilita la porta JupyterHub e SSH dal set di scalabilità front-end al pool back-end delle DSVM Ubuntu. Come utente, si esegue l'accesso semplicemente alla VM in JupyterHub o in SSH in modo normale. Poiché è possibile aumentare o ridurre in modo dinamico le prestazioni delle istanze di VM, ogni stato deve essere salvato nella condivisione di File di Azure montata. È possibile usare lo stesso approccio per creare un pool di DSVM Windows. 

Anche lo [script che monta la condivisione di File di Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) è disponibile nel repository Azure DataScienceVM in GitHub. Lo script monta la condivisione di File di Azure nel punto di montaggio specificato nel file dei parametri. Lo script crea anche collegamenti simbolici all'unità montata nella home directory dell'utente iniziale. Una directory notebook specifica dell'utente nella condivisione di File di Azure viene collegata in modo simbolico alla directory `$HOME/notebooks/remote` in modo che l'utente possa accedere, eseguire e salvare i notebook di Jupyter. È possibile usare la stessa convenzione quando si creano altri utenti nella VM per puntare all'area di lavoro Jupyter di ogni utente nella condivisione di File di Azure. 

I set di scalabilità di macchine virtuali supportano la scalabilità automatica. È possibile impostare regole che determinano quando creare altre istanze e quando ridurre il numero delle istanze. È ad esempio possibile ridurre il numero di istanze a zero per risparmiare sui costi di utilizzo dell'hardware cloud quando le VM non vengono usate. Le pagine di documentazione sui set di scalabilità di macchine virtuali riportano i passaggi dettagliati per la [scalabilità automatica](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare un'identità comune](dsvm-common-identity.md)
* [Archiviare le credenziali in modo sicuro per accedere alle risorse cloud](dsvm-secure-access-keys.md)















