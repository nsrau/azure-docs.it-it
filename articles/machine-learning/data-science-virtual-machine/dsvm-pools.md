---
title: Pool di macchine virtuali per data science in Azure | Microsoft Docs
description: Distribuzione di pool di macchine virtuale di data science come risorsa condivisa per il team
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
ms.openlocfilehash: c7aab0435ecbd0aee57a15008ac0270159ec2eb3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837083"
---
# <a name="creating-a-shared-pool-of-data-science-virtual-machines"></a>Creazione di un pool condiviso di macchine virtuali per data science

Questo articolo illustra come creare un pool condiviso di macchine virtuali per data science (DSVM) per l'uso da parte del team. Il vantaggio di usare un pool condiviso è un migliore utilizzo delle risorse che facilita la condivisione e la collaborazione e consente al reparto IT di gestire le risorse DSVM in modo più efficace. 

Esistono molti modi e diverse tecnologie che possono essere usate per creare un pool di DSVM.  Gli scenari principali sono i seguenti:

* Pool per l'elaborazione batch
* Pool per le VM interattive

## <a name="batch-processing-pool"></a>Pool di elaborazione batch
Se si desidera configurare un pool di DSVM principalmente per eseguire i processi in un batch non in linea, è possibile usare il servizio [Azure Batch per intelligenza artificiale](https://docs.microsoft.com/azure/batch-ai/) oppure [Azure Batch](https://docs.microsoft.com/azure/batch/). 

### <a name="azure-batch-ai"></a>Azure Batch per intelligenza artificiale
L'edizione Ubuntu della DSVM è supportata come una delle immagini in Azure Batch per intelligenza artificiale. Nell'interfaccia della riga di comando di Azure o Python SDK in cui si crea il cluster Azure Batch per intelligenza artificiale si può specificare il parametro ```image``` e impostarlo su ```UbuntuDSVM```. È possibile scegliere il tipo di nodi di elaborazione desiderato, ovvero istanze basate su GPU o istanze solo CPU, il numero di CPU e la memoria, dall'[ampia scelta di istanze di VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) disponibile in Azure. Quando si usa l'immagine Ubuntu di DSVM in Azure Batch per intelligenza artificiale con nodi basati su GPU, tutti i driver GPU e i framework di apprendimento avanzato necessari sono preinstallati e ciò consente di risparmiare molto tempo nella preparazione dei nodi batch. Infatti, se si sviluppa in una DSVM Ubuntu in modo interattivo, si noterà che i nodi Azure Batch per intelligenza artificiale hanno esattamente la stessa configurazione dell'ambiente. In genere quando si crea un cluster Azure Batch per intelligenza artificiale si crea anche una condivisione file montata da tutti i nodi che viene usata per l'input e l'output dei dati e anche per l'archiviazione del codice e degli script del processo. 

Dopo la creazione del cluster Azure Batch per intelligenza artificiale, è possibile usare la stessa interfaccia della riga di comando o Python SDK per inviare i processi da eseguire. Si paga solo per il tempo impiegato per eseguire i processi batch. 

#### <a name="more-information"></a>Altre informazioni
* Procedura dettagliata per l'uso dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) per la gestione di Azure Batch per intelligenza artificiale
* Procedura dettagliata per l'uso di [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) per la gestione di Azure Batch per intelligenza artificiale
* Sono disponibili [file recipe di Azure Batch per intelligenza artificiale](https://github.com/Azure/BatchAI) che illustrano come usare vari framework di apprendimento avanzato/intelligenza artificiale con Azure Batch per intelligenza artificiale.

## <a name="interactive-vm-pool"></a>Pool di VM interattive

Un pool di DSVM interattive che sono condivise dall'intero team di intelligenza artificiale/data science consente agli utenti di accedere a un'istanza disponibile della DSVM anziché avere un'istanza dedicata per ogni set di utenti. Questo garantisce una migliore disponibilità e un uso più efficace delle risorse. 

La tecnologia utilizzata per creare un pool di VM interattive è il [set di scalabilità di macchine virtuali di Microsoft Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) (VMSS) che consente di creare e gestire un gruppo di VM identiche dotate di carico bilanciato e scalabilità automatica. L'utente accede all'indirizzo IP o DNS del pool principale. Il set di scalabilità esegue automaticamente il routing della sessione su una DSVM disponibile nel set di scalabilità. Poiché l'utente apprezza un ambiente simile, indipendentemente dalla VM a cui esegue l'accesso, tutte le istanze della VM nel set di scalabilità montano un'unità di rete condivisa, ad esempio una condivisione File di Azure o NFS. L'area di lavoro condivisa dell'utente in genere viene mantenuta nell'archivio di file condiviso montato in ognuna delle istanze. 

Un esempio di modello di Azure Resource Manager che crea un set di scalabilità di VM con istanze di DSVM Ubuntu è disponibile in [github](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Nello stesso percorso è disponibile anche un esempio del [file dei parametri](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) del modello di Azure Resource Manager. 

È possibile creare il set di scalabilità della VM dal modello di Azure Resource Manager specificando i valori adatti per il file dei parametri mediante l'interfaccia della riga di comando di Azure. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
I comandi precedenti si basano sul presupposto che si abbia una copia del file dei parametri con i valori specificati per l'istanza del set di scalabilità della VM, il numero di istanze di VM, i puntatori ai file di Azure e le credenziali per l'account di archiviazione che verrà montato su ogni VM. Nel comando precedente si fa riferimento in locale al file dei parametri. È possibile anche passare parametri inline o richiedere i parametri nello script.  

Il modello precedente abilita la porta Jupyterhub e SSH dal set di scalabilità della VM front-end al pool back-end delle DSVM Ubuntu.  Come utente si esegue l'accesso semplicemente alla VM in JupyterHub o in SSH in modo normale. Poiché le istanze di VM possono essere ridimensionate verso l'alto o verso il basso in modo dinamico, ogni stato deve essere salvato nella condivisione File di Azure montata. Lo stesso approccio può essere usato per creare un pool di DSVM Windows. 

Anche lo [script che monta File di Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) è disponibile in Azure DataScienceVM Github. Oltre a montare File di Azure nel punto di montaggio specificato nel file dei parametri, crea anche altri collegamenti simbolici nell'unità montata nella home directory dell'utente iniziale e una directory notebook specifica per l'utente all'interno di File di Azure è collegata in modo simbolico alla directory ```$HOME/notebooks/remote``` consentendo l'accesso dell'utente, l'esecuzione e il salvataggio dei suoi notebook Jupyter.  Si può usare la stessa convenzione quando si creano altri utenti nella VM per puntare all'area di lavoro Jupyter di ogni utente in File di Azure condiviso. 

I set di scalabilità della VM di Azure supportano la scalabilità automatica in cui è possibile impostare regole sui casi in cui creare istanze aggiuntive e le circostanze in cui ridimensionare verso il basso le istanze, incluso portare a zero le istanze per risparmiare sui costi di utilizzo del cloud quando le VM non vengono usate. Le pagine di documentazione sui set di scalabilità delle VM riportano i passaggi dettagliati per la [scalabilità automatica](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Passaggi successivi

* [Impostare l'identità comune](dsvm-common-identity.md)
* [Archiviare in modo sicuro le credenziali per accedere alle risorse cloud](dsvm-secure-access-keys.md)















