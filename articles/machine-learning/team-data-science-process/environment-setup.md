---
title: Configurare ambienti di analisi scientifica dei dati in Azure | Microsoft Docs
description: Configurare gli ambienti per l'analisi scientifica dei dati in Azure da usare nel processo di analisi scientifica dei dati eseguito dai team.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 481cfa6a-7ea3-46ac-b0f9-2e3982c37153
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: deguhath
ms.openlocfilehash: 6c28a64830afeb19c6a9264888b296c3b99990d1
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39262566"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Impostare gli ambienti per la scienza dei dati per l'uso nel Processo di analisi scientifica dei dati per i team
Il Processo di analisi scientifica dei dati per i team usa diversi ambienti per la scienza dei dati per l'archiviazione, l'elaborazione e l'analisi dei dati. Tra questi rientrano l'archiviazione BLOB di Azure, diversi tipi di macchine virtuali di Azure, i cluster HDInsight (Hadoop) e le aree di lavoro di Azure Machine Learning. La scelta riguardante quale ambiente usare dipende dal tipo e dalla quantità di dati da modellare e dalla destinazione di quei dati nel cloud. 

* Per indicazioni sulle questioni da prendere in considerazione per prendere questa decisione, vedere [Pianificazione dell'ambiente di analisi scientifica dei dati di Azure Machine Learning](plan-your-environment.md). 
* Per un catalogo di alcuni degli scenari che potrebbero verificarsi quando si esegue l'analisi avanzata, vedere [Scenarios for the Team Data Science Process](plan-sample-scenarios.md)

Questo menu include collegamenti ad argomenti che descrivono come configurare i diversi ambienti di analisi scientifica dei dati usati dal processo di analisi scientifica dei dati per i team.

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

La **macchina virtuale per l'analisi scientifica dei dati di Microsoft** è disponibile anche come immagine di macchina virtuale di (VM) Azure. Questa macchina virtuale è pre-installata e configurata con diversi strumenti diffusi, usati comunemente per l'analisi dei dati e l'apprendimento automatico. La macchina virtuale per l'analisi scientifica dei dati è disponibile in Windows e Linux. Per altre informazioni, vedere [Introduzione alla Data Science Virtual Machine basata su cloud per Linux e Windows](../data-science-virtual-machine/overview.md).

Scopri come creare:

- [Data Science Virtual Machine di Windows](../data-science-virtual-machine/provision-vm.md)
- [Data Science Virtual Machine di Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [Data Science Virtual Machine di CentOS](../data-science-virtual-machine/linux-dsvm-intro.md)
- [Macchina virtuale per l'apprendimento avanzato](../data-science-virtual-machine/provision-deep-learning-dsvm.md)
