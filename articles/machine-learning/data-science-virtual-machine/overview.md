---
title: Informazioni su Azure Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: 'Panoramica di Azure Data Science Virtual Machine: una macchina virtuale semplice da usare nella piattaforma cloud di Azure con librerie e strumenti preinstallati e configurati per scenari di data science.'
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: 7bcee0f571fe42e4c00499f188e35d0e0ff2ebe7
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322502"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Cos'è la Azure Data Science Virtual Machine per Linux e Windows?

La Data Science Virtual Machine (DSVM) è un'immagine di VM personalizzata sulla piattaforma cloud di Azure creata specificamente per le attività di data science. Include diversi strumenti comuni per data science e strumenti preinstallati e preconfigurati per implementare rapidamente la creazione di applicazioni intelligenti per l'analisi avanzata.

Data Science Virtual Machine è disponibile in:

+ Windows Server 2019
+ Ubuntu 18.04 LTS

## <a name="comparison-with-azure-machine-learning"></a>Confronto con Azure Machine Learning

Data Science Virtual Machine è un'immagine VM per data science ma [Azure Machine Learning](../overview-what-is-azure-ml.md) (AzureML) è una piattaforma end-to-end che include:

+ Risorse di calcolo completamente gestite
  + Compute Instances (Istanze di calcolo)
  + Cluster di elaborazione per attività di Machine Learning distribuito
  + Cluster di inferenza per assegnazione di punteggi in tempo reale
+ Archivi dati (ad esempio BLOB, Azure Data Lake Storage Gen2, database SQL)
+ Verifica degli esperimenti
+ Gestione di modelli
+ Notebook
+ Ambienti (gestione delle dipendenze di Conda e R)
+ Etichettatura
+ Pipeline (automatizzazione dei flussi di lavoro di data science end-to-end)

### <a name="comparison-with-azureml-compute-instances"></a>Confronto con le istanze di ambiente di calcolo di AzureML

Le [istanze di ambiente di calcolo di Azure Machine Learning](../concept-compute-instance.md) sono un'immagine VM completamente configurata e __gestita__ , mentre Data Science Virtual Machine è una VM __non gestita__.

Le differenze essenziali tra queste due offerte di prodotti sono illustrate di seguito:


|Funzionalità |Data science<br>VM |AzureML<br>Istanza di ambiente di calcolo  | 
|---------|---------|---------|
| Completamente gestita | No        | Sì        |
|Supporto delle lingue     |  Python, R, Julia, SQL, C#,<br> Java, Node.js, F#       | Python e R        |
|Sistema operativo     | Ubuntu<br>Windows         |    Ubuntu     |
|Opzione per GPU preconfigurata     |  Sì       |    Sì     |
|Opzione per aumento delle prestazioni | Sì | Sì |
|Accesso SSH    | Sì        |    Sì     |
|Accesso RDP    | Sì        |     No    |
|Predefinito<br>Notebook ospitati     |   No<br>(richiede configurazione aggiuntiva)      |      Sì   |
|Accesso Single Sign-On predefinito     | No <br>(richiede configurazione aggiuntiva)         |    Sì     |
|Collaborazione predefinita     | No         | Sì        |
|Strumenti preinstallati     |  Jupyter(lab), RStudio Server, VSCode,<br> Visual Studio, PyCharm, Juno,<br>Power BI Desktop, SSMS, <br>Microsoft Office 365, Apache Drill       |     Jupyter(lab)<br> RStudio Server   |

## <a name="sample-use-cases"></a>Casi d'uso di esempio

Di seguito vengono illustrati alcuni casi d'uso comuni per i clienti della DSVM.

### <a name="short-term-experimentation-and-evaluation"></a>Valutazione e sperimentazione a breve termine

È possibile usare la DSVM per valutare o apprendere nuovi [strumenti](./tools-included.md) di data science, in particolare attraverso alcuni degli [esempi e delle procedure dettagliate](./dsvm-samples-and-walkthroughs.md) pubblicati.

### <a name="deep-learning-with-gpus"></a>Deep Learning con GPU

Nella DSVM i modelli di training possono usare algoritmi di Deep Learning su hardware basato su GPU (Graphics Processing Unit). Sfruttando il vantaggio della scalabilità delle VM della piattaforma Azure, la DSVM consente di usare hardware basato su GPU nel cloud secondo specifiche esigenze. È possibile passare a una VM basata su GPU durante il training di modelli di grandi dimensioni o quando sono necessari calcoli ad alta velocità, mantenendo lo stesso disco del sistema operativo. È possibile scegliere uno degli SKU di macchine virtuali abilitate per la GPU della serie N con la DSVM. Si noti che gli SKU di macchine virtuali abilitate per la GPU non sono supportati negli account Azure gratuiti.

Nelle edizioni per Windows della DSVM sono preinstallati i driver di GPU, i framework e le versioni per GPU dei framework di Deep Learning. Nelle edizioni per Linux, il Deep Learning su GPU è abilitato sulle DSVM Ubuntu. 

È anche possibile distribuire l'edizione per Ubuntu o Windows della DSVM in una macchina virtuale di Azure non basata su GPU. In questo caso tutti i framework di Deep Learning eseguiranno il fallback alla modalità CPU.

[Altre informazioni sui framework di Deep Learning e IA disponibili](dsvm-tools-deep-learning-frameworks.md).

### <a name="data-science-training-and-education"></a>Preparazione e formazione sull'analisi scientifica dei dati

Gli istruttori e i formatori aziendali che tengono corsi di data science in genere forniscono un'immagine di macchina virtuale per garantire che gli studenti abbiano una configurazione coerente e che gli esempi abbiano un comportamento prevedibile.

La DSVM consente di creare un ambiente su richiesta con una configurazione coerente che semplifica i problemi relativi a incompatibilità e supporto. Esistono vantaggi sostanziali per i casi in cui tali ambienti devono essere compilati di frequente, in particolare per i corsi di formazione più brevi.


## <a name="whats-included-on-the-dsvm"></a>Quali funzionalità sono incluse nella DSVM?

Vedere un elenco completo di strumenti sulle DVSM di Windows e Linux [qui](tools-included.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

+ Windows:
  + [Configurare una Data Science Virtual Machine Windows](provision-vm.md)
  + [Data science in una Data Science Virtual Machine Windows](vm-do-ten-things.md)

+ Linux:
  + [Configurare una Data Science Virtual Machine Linux (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Data science in una Data Science Virtual Machine Linux](linux-dsvm-walkthrough.md)