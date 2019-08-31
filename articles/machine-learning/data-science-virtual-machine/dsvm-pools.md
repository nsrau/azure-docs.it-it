---
title: Pool condivisi
titleSuffix: Azure Data Science Virtual Machine
description: Distribuzione di pool di Dsvm come risorsa condivisa per un team
keywords: apprendimento avanzato, AI, strumenti di data science, macchina virtuale per data science, analisi geospaziale, processo di data science del team
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 929040b5dc8650a757fb9c0da58cb82a76a72b5b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195622"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Creare un pool condiviso di Data Science Virtual Machine

In questo articolo si apprenderà come creare un pool condiviso di macchine virtuali di Data Science (Dsvm) per un team. I vantaggi derivanti dall'utilizzo di un pool condiviso includono un migliore utilizzo delle risorse, una condivisione e una collaborazione più semplici e una gestione più efficace delle risorse DSVM.

È possibile usare numerosi metodi e tecnologie per creare un pool di DSVM. Questo articolo è incentrato sui pool di macchine virtuali interattive (VM). Un'infrastruttura di calcolo gestita alternativa è Ambiente di calcolo di Azure Machine Learning. Per altre informazioni, vedere [configurare le destinazioni di calcolo](../service/how-to-set-up-training-targets.md#amlcompute).

## <a name="interactive-vm-pool"></a>Pool di VM interattive

Un pool di VM interattive che sono condivise dall'intero team di intelligenza artificiale/data science consente agli utenti di accedere a un'istanza disponibile della DSVM anziché avere un'istanza dedicata per ogni set di utenti. Questa configurazione consente una maggiore disponibilità e un uso più efficiente delle risorse.

Usare la tecnologia di set di scalabilità di [macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) per creare un pool di macchine virtuali interattivo. È possibile usare i set di scalabilità per creare e gestire un gruppo di VM identiche con bilanciamento del carico e scalabilità automatica.

L'utente accede all'indirizzo IP o DNS del pool principale. Il set di scalabilità esegue automaticamente il routing della sessione su una DSVM disponibile nel set di scalabilità. Poiché gli utenti vogliono un ambiente coerente e familiare indipendentemente dalla macchina virtuale a cui accedono, tutte le istanze della macchina virtuale nel set di scalabilità montano un'unità di rete condivisa, ad esempio una condivisione File di Azure o una condivisione NFS (Network File System). L'area di lavoro condivisa dell'utente in genere viene mantenuta nell'archivio di file condiviso montato in ognuna delle istanze.

È possibile trovare un esempio di modello di Azure Resource Manager che crea un set di scalabilità con istanze di DSVM Ubuntu in [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). È possibile trovare un esempio del [file di parametri](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) per il modello di Azure Resource Manager nello stesso percorso.

È possibile creare il set di scalabilità dal modello di Azure Resource Manager specificando i valori per il file dei parametri nell'interfaccia della riga di comando di Azure:

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

Il modello precedente abilita la porta JupyterHub e SSH dal set di scalabilità front-end al pool back-end delle DSVM Ubuntu. L'utente accede alla macchina virtuale in un Secure Shell (SSH) o in JupyterHub in modo normale. Poiché le istanze di macchina virtuale possono essere aumentate o ridotte in modo dinamico, qualsiasi stato deve essere salvato nella condivisione File di Azure montata. È possibile usare lo stesso approccio per creare un pool di DSVM Windows.

Anche lo [script che monta la condivisione di File di Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) è disponibile nel repository Azure DataScienceVM in GitHub. Lo script monta la condivisione di File di Azure nel punto di montaggio specificato nel file dei parametri. Lo script crea anche collegamenti simbolici all'unità montata nella home directory dell'utente iniziale. Una directory del notebook specifica dell'utente nella condivisione file di Azure è collegata temporaneamente alla `$HOME/notebooks/remote` directory in modo che gli utenti possano accedere, eseguire e salvare i notebook di Jupyter. È possibile usare la stessa convenzione quando si creano altri utenti nella VM per puntare all'area di lavoro Jupyter di ogni utente nella condivisione di File di Azure.

I set di scalabilità di macchine virtuali supportano la scalabilità automatica. È possibile impostare regole su quando creare istanze aggiuntive e quando ridimensionare le istanze. È ad esempio possibile ridurre il numero di istanze a zero per risparmiare sui costi di utilizzo dell'hardware cloud quando le VM non vengono usate. Le pagine di documentazione sui set di scalabilità di macchine virtuali riportano i passaggi dettagliati per la [scalabilità automatica](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare un'identità comune](dsvm-common-identity.md)
* [Archiviare le credenziali in modo sicuro per accedere alle risorse cloud](dsvm-secure-access-keys.md)















