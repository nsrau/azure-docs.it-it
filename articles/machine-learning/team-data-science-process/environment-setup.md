---
title: Configurare gli ambienti di data science in Azure - Processo di data science per i team
description: Configurare gli ambienti di data science in Azure da usare nel processo di data science eseguito dai team.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2901c4dd352ff251d6c73c3fb847ee49d542d1f4
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53132483"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Configurare gli ambienti di data science per l'uso nel processo di data science per i team
Il processo di data science per i team usa diversi ambienti di data science per l'archiviazione, l'elaborazione e l'analisi dei dati. Tra questi rientrano l'archiviazione BLOB di Azure, diversi tipi di macchine virtuali di Azure, i cluster HDInsight (Hadoop) e le aree di lavoro di Azure Machine Learning. La scelta riguardante quale ambiente usare dipende dal tipo e dalla quantità di dati da modellare e dalla destinazione di quei dati nel cloud. 

* Per indicazioni sulle questioni da prendere in considerazione per prendere questa decisione, vedere [Pianificare l'ambiente di data science di Azure Machine Learning](plan-your-environment.md). 
* Per un catalogo di alcuni degli scenari che potrebbero verificarsi quando si esegue l'analisi avanzata, vedere [Scenari per il processo di data science per i team](plan-sample-scenarios.md).

Gli articoli seguenti descrivono come configurare i diversi ambienti di data science usati dal processo di data science per i team.

* [Account di archiviazione di Azure](../../storage/common/storage-quickstart-create-account.md)
* [Cluster HDInsight (Hadoop)](customize-hadoop-cluster.md)
* [Area di lavoro di Azure Machine Learning Studio](../studio/create-workspace.md)

**Microsoft Data Science Virtual Machine (DSVM)** è disponibile anche come immagine di macchina virtuale di Azure. Questa macchina virtuale è pre-installata e configurata con diversi strumenti diffusi, usati comunemente per l'analisi dei dati e l'apprendimento automatico. Data Science Virtual Machine è disponibile in Windows e Linux. Per altre informazioni, vedere [Introduzione alla Data Science Virtual Machine basata su cloud per Linux e Windows](../data-science-virtual-machine/overview.md).

Scopri come creare:

- [DSVM di Windows](../data-science-virtual-machine/provision-vm.md)
- [Data Science Virtual Machine di Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [Data Science Virtual Machine di CentOS](../data-science-virtual-machine/linux-dsvm-intro.md)
- [Macchina virtuale per il Deep Learning](../data-science-virtual-machine/provision-deep-learning-dsvm.md)
