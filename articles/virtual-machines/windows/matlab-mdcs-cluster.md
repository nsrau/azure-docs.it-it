---
title: Cluster MATLAB su macchine virtuali | Microsoft Docs
description: Usare macchine virtuali di Microsoft Azure per creare cluster MATLAB Distributed Computing Server per eseguire carichi di lavoro MATLAB paralleli a elevato utilizzo di calcolo
services: virtual-machines-windows
documentationcenter: 
author: mscurrell
manager: timlt
editor: 
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: b302c6b3c6acbb8552796e7fb1bfd153d23dceb3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Creare cluster MATLAB Distributed Computing Server in VM di Azure
Usare macchine virtuali di Microsoft Azure per creare uno o più cluster MATLAB Distributed Computing Server per eseguire carichi di lavoro MATLAB paralleli a elevato utilizzo di calcolo. Installare il software MATLAB Distributed Computing Server in una VM da usare come un'immagine di base e usare un modello di avvio rapido di Azure o uno script Azure PowerShell, disponibile in [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster), per distribuire e gestire il cluster. Dopo la distribuzione, connettersi al cluster per eseguire i carichi di lavoro.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Informazioni su MATLAB e MATLAB Distributed Computing Server
La piattaforma [MATLAB](http://www.mathworks.com/products/matlab/) è ottimizzata per la risoluzione di problemi di progettazione e scientifici. Gli utenti di MATLAB con simulazioni su larga scala e attività di elaborazione dei dati possono usare i prodotti di elaborazione parallela MathWorks per velocizzare i carichi di lavoro a elevato utilizzo di calcolo sfruttando cluster di elaborazione e servizi griglia. [Parallel Computing Toolbox](http://www.mathworks.com/products/parallel-computing/) consente agli utenti di MATLAB di parallelizzare le applicazioni e sfruttare i vantaggi di processori multicore, GPU e cluster di elaborazione. [MATLAB Distributed Computing Server](http://www.mathworks.com/products/distriben/) consente agli utenti di MATLAB di utilizzare molti computer in un cluster di elaborazione.

Con le macchine virtuali di Azure, è possibile creare cluster MATLAB Distributed Computing Server che presentano gli stessi meccanismi disponibili per inviare un lavoro parallelo come cluster locale, ad esempio processi interattivi, processi batch, attività indipendenti e attività di comunicazione. L'uso di Azure in combinazione con la piattaforma MATLAB presenta numerosi vantaggi rispetto al provisioning e all'uso di hardware locale tradizionale: una gamma di dimensioni di macchina virtuale, la creazione di cluster su richiesta per pagare solo per le risorse di calcolo usate e la possibilità di testare i modelli su larga scala.  

## <a name="prerequisites"></a>Prerequisiti
* **Computer client** : è necessario un computer client basato su Windows per comunicare con Azure e il cluster MATLAB Distributed Computing Server dopo la distribuzione.
* **Azure PowerShell** : per l'installazione nel computer client, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) .
* **Sottoscrizione di Azure** : se non è disponibile una sottoscrizione, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti. Per cluster di maggiori dimensioni, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto.
* **Quota di core** : potrebbe essere necessario aumentare la quota di core per distribuire un cluster di grandi dimensioni o più cluster MATLAB Distributed Computing Server. Per aumentare una quota, è possibile [aprire una richiesta di assistenza clienti online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) senza alcun addebito.
* **Licenze per MATLAB, Parallel Computing Toolbox e MATLAB Distributed Computing Server** : negli script si presuppone che [MathWorks Hosted License Manager](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) venga usato per tutte le licenze.  
* **Software MATLAB Distributed Computing Server** : verrà installato in una VM che sarà usata come immagine di VM di base per le VM del cluster.

## <a name="high-level-steps"></a>Passaggi di livello elevato
Per usare macchine virtuali di Azure per i cluster MATLAB Distributed Computing Server, sono necessari i passaggi generali seguenti. Istruzioni dettagliate sono disponibili nella documentazione che accompagna il modello di avvio rapido e gli script in [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Creare un'immagine di VM di base**  

   * Scaricare e installare il software MATLAB Distributed Computing Server in questa VM.

     > [!NOTE]
     > Questo processo può richiedere un paio d'ore, ma è necessario eseguire questa operazione una sola volta per ogni versione di MATLAB in uso.   
     >
     >
2. **Creare uno o più cluster**  

   * Usare lo script PowerShell o il modello di avvio rapido per creare un cluster dall'immagine di VM di base.   
   * Gestire i cluster con lo script PowerShell fornito che consente di elencare, sospendere, riprendere ed eliminare i cluster.

## <a name="cluster-configurations"></a>Configurazioni dei cluster
Attualmente, il modello e lo script per la creazione di cluster consentono di creare una topologia MATLAB Distributed Computing Server singola. È possibile creare uno o più cluster aggiuntivi e ogni cluster può avere un numero diverso di VM di lavoro, con dimensioni di VM diverse e così via.

### <a name="matlab-client-and-cluster-in-azure"></a>Client e cluster MATLAB in Azure
Il nodo client MATLAB, il nodo MATLAB Job Scheduler e nodi di "lavoro" MATLAB Distributed Computing Server vengono tutti configurati come VM di Azure in una rete virtuale, come illustrato nella figura seguente.


* Per usare il cluster, connettersi con Desktop remoto al nodo client. Il nodo client esegue il client MATLAB.
* Il nodo client ha una condivisione file accessibile a tutti i ruoli di lavoro.
* MathWorks Hosted License Manager viene usato per i controlli relativi alle licenze per tutto il software MATLAB.
* Per impostazione predefinita, viene creato un ruolo di lavoro MATLAB Distributed Computing Server per ogni core delle VM ruolo di lavoro, ma è possibile specificare il numero desiderato.

## <a name="use-an-azure-based-cluster"></a>Usare un cluster basato su Azure
Come per altri tipi di cluster MATLAB Distributed Computing Server, è necessario usare Cluster Profile Manager nel client MATLAB (nella VM client) per creare un profilo di cluster MATLAB Job Scheduler.

![Cluster Profile Manager](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Passaggi successivi
* Per istruzioni dettagliate sulla distribuzione e gestione di cluster MATLAB Distributed Computing Server in Azure, vedere il repository [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) contenente i modelli e gli script.
* Visitare il [sito MathWorks](http://www.mathworks.com/) per la documentazione dettagliata relativa a MATLAB e MATLAB Distributed Computing Server.
