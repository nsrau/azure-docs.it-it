---
title: Informazioni su Azure Data Science Virtual Machine
description: Scenari e componenti chiave dell'analisi per le macchine virtuali data science per Windows e Linux.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 02/22/2019
ms.openlocfilehash: 8359a8407e93a2b31466342b82539cef04b0fe01
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802188"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Cos'è la Azure Data Science Virtual Machine per Linux e Windows?

La Data Science Virtual Machine (DSVM) è un'immagine di VM personalizzata sulla piattaforma cloud di Azure creata specificamente per le attività di data science. Include diversi strumenti comuni per l'analisi scientifica dei dati e strumenti preinstallati e preconfigurati per implementare rapidamente la creazione di applicazioni intelligenti per l'analisi avanzata. 

Data Science Virtual Machine è disponibile in:
+ Windows Server 2016
+ Ubuntu 16.04 LTS e CentOS 7.4

> [!NOTE]
> Nella Data Science Virtual Machine sono stati raggruppati tutti gli strumenti delle VM per Deep Learning. 


## <a name="why-choose-the-dsvm"></a>Perché scegliere la DSVM?
L'obiettivo della Data Science Virtual Machine (DSVM) è offrire ai professionisti dei dati, con tutti i livelli di competenze e in tutti i settori, un ambiente di data science privo di problemi e preconfigurato. Invece di implementare un'area di lavoro paragonabile autonomamente, è possibile eseguire il provisioning di una DSVM. Questa scelta consente di risparmiare diversi giorni o persino _settimane_ nelle procedure di installazione, configurazione e gestione dei pacchetti. Dopo aver allocato la macchina virtuale per l'analisi scientifica, è possibile iniziare immediatamente a lavorare al progetto di analisi scientifica dei dati.

## <a name="sample-use-cases"></a>Casi d'uso di esempio

Di seguito vengono illustrati alcuni casi d'uso comuni per i clienti della DSVM.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Trasferimento dei carichi di lavoro di data science nel cloud

La DSVM offre una configurazione di base per i team di data science che vogliono sostituire i desktop locali con un desktop cloud gestito, per assicurarsi che tutti i data scientist di un team abbiano a disposizione una configurazione coerente con cui verificare gli esperimenti e promuovere la collaborazione. Anche i costi diminuiscono, grazie alla riduzione del carico lavorativo per gli amministratori di sistema e del tempo necessario per valutare, installare e gestire i vari pacchetti software per l'analisi avanzata.

### <a name="data-science-training-and-education"></a>Preparazione e formazione sull'analisi scientifica dei dati
Gli istruttori e i formatori aziendali che tengono corsi di data science in genere forniscono un'immagine di macchina virtuale per garantire che gli studenti abbiano una configurazione coerente e che gli esempi abbiano un comportamento prevedibile. 

La DSVM consente di creare un ambiente su richiesta con una configurazione coerente che semplifica i problemi relativi a incompatibilità e supporto. Esistono vantaggi sostanziali per i casi in cui tali ambienti devono essere compilati di frequente, in particolare per i corsi di formazione più brevi.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacità elastica su richiesta per progetti su larga scala
Gli hackathon e i concorsi di data science o la modellazione e l'esplorazione di dati su larga scala richiedono una maggiore capacità hardware, in genere per brevi periodi di tempo. La DSVM consente di replicare rapidamente e su richiesta l'ambiente di data science su server con scalabilità orizzontale, che consentono di eseguire esperimenti con risorse di calcolo con potenza elevata.

### <a name="custom-compute-power-for-azure-notebooks"></a>Potenza di calcolo personalizzata per Azure Notebooks
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) è un servizio ospitato gratuito che consente di sviluppare, eseguire e condividere notebook di Jupyter nel cloud senza alcuna installazione. Il livello di servizio gratuito è limitato a 4 GB di memoria e 1 GB di dati. 

Per eliminare tutti i limiti, è possibile collegare un progetto di Notebooks a una DSVM o a qualsiasi altra macchina virtuale in esecuzione in un server Jupyter. Se si accede ad Azure Notebooks con un account tramite Azure Active Directory (ad esempio un account aziendale), Notebooks mostrerà automaticamente le DSVM incluse in qualsiasi sottoscrizione associata a tale account. È possibile [collegare una DSVM ad Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) per ampliare la potenza di calcolo disponibile.

### <a name="short-term-experimentation-and-evaluation"></a>Valutazione e sperimentazione a breve termine
È possibile usare la DSVM per valutare o apprendere nuovi [strumenti](./tools-included.md) di data science, in particolare attraverso alcuni degli [esempi e delle procedure dettagliate](./dsvm-samples-and-walkthroughs.md) pubblicati.


### <a name="deep-learning-with-gpus"></a>Deep Learning con GPU
Nella DSVM i modelli di training possono usare algoritmi di Deep Learning su hardware basato su GPU (Graphics Processing Unit). Sfruttando il vantaggio della scalabilità delle VM della piattaforma Azure, la DSVM consente di usare hardware basato su GPU nel cloud secondo specifiche esigenze. È possibile passare a una VM basata su GPU durante il training di modelli di grandi dimensioni o quando sono necessari calcoli ad alta velocità, mantenendo lo stesso disco del sistema operativo. È possibile scegliere uno degli SKU di macchine virtuali abilitate per la GPU della serie N con la DSVM. Si noti che gli account gratuiti di Azure non supportano gli SKU delle macchine virtuali abilitate per GPU.

Nell'edizione per Windows Server 2016 della DSVM sono preinstallati i driver GPU, i framework e la versione GPU dei framework di Deep Learning. Nell'edizione per Linux, il Deep Learning su GPU è abilitato sulle DSVM CentOS e Ubuntu. 

È anche possibile distribuire l'edizione Ubuntu, CentOS o Windows 2016 della DSVM in una macchina virtuale di Azure non basata su GPU. In questo caso tutti i framework di Deep Learning eseguiranno il fallback alla modalità CPU.
 
[Altre informazioni sui framework di Deep Learning e IA disponibili](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Quali funzionalità sono incluse nella DSVM?

Vedere un elenco completo di strumenti sulle DVSM di Windows e Linux [qui](tools-included.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

+ Windows:
  + [Configurare una Data Science Virtual Machine Windows](provision-vm.md)
  + [Dieci cose da fare con una Data Science Virtual Machine Windows](vm-do-ten-things.md)

+ Linux:
  + [Configurare una Data Science Virtual Machine Linux (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Configurare una Data Science Virtual Machine Linux (CentOS)](linux-dsvm-intro.md)
  + [Data science in una Data Science Virtual Machine Linux](linux-dsvm-walkthrough.md)
