---
title: Introduzione alla macchina virtuale per l'apprendimento avanzato - Azure | Microsoft Docs
description: Gli scenari analitici e i componenti chiave per le macchine virtuali per l'apprendimento avanzato.
keywords: apprendimento avanzato, AI, strumenti di data science, macchina virtuale per data science, strumenti per data science, data science per Linux
services: machine-learning
documentationcenter: 
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: gokuma
ms.openlocfilehash: a3e4c989c1dbb31b237115acfcc032aa2dee4f2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Introduzione alla macchina virtuale per l'apprendimento avanzato - Azure

## <a name="why-deep-learning-virtual-machine"></a>Perché la macchina virtuale per l'apprendimento avanzato? 

Gli algoritmi di apprendimento avanzato e le reti neurali profonde compaiono sempre più spesso fra i metodi più diffusi utilizzati in molti problemi di machine learning. Sono particolarmente efficaci per le attività di machine cognition come la comprensione di immagini, testo, audio e video spesso simile a livelli cognitivi umani in alcuni domini specifici con architetture di rete neurali profonde avanzate e l'accesso ad estesi set di dati per l'addestramento dei modelli. L'apprendimento avanzato richiede una grande quantità di potenza di calcolo per addestrare i modelli con questi set di dati di grandi dimensioni. Con il cloud e la disponibilità di unità di elaborazione grafica (GPU) sta diventando possibile creare architetture neurali profonde sofisticate e addestrarle con un set di dati di grandi dimensioni in potenti infrastruttura di calcolo su cloud.  La [macchina virtuale per data science](overview.md) ha fornito un ampio set di strumenti ed esempi per la preparazione dei dati, il machine learning e la formazione avanzata. Ma una delle sfide riscontrate dagli utenti consiste nell'individuare facilmente gli strumenti e gli esempi per scenari specifici, come l'apprendimento avanzato, e anche eseguire più facilmente il provisioning delle istanze di VM basate su GPU. Questa macchina virtuale per l'apprendimento avanzato (DLVM) affronta queste sfide. 

## <a name="what-is-deep-learning-virtual-machine"></a>Cos'è la macchina virtuale per l'apprendimento avanzato? 
La macchina virtuale per l'apprendimento avanzato è una variante configurata in modo particolare della [macchina virtuale per data science](overview.md) (DSVM) che semplifica l'utilizzo delle istanze di VM basate su GPU per l'addestramento dei modelli di apprendimento avanzato. È supportata in Windows 2016 e nella macchina virtuale per data science Ubuntu.  Condivide le stesse immagini di VM di base (e pertanto tutto il ricco set di strumenti) della DSVM ma è configurata in modo da semplificare l'apprendimento avanzato. Sono forniti anche esempi end-to-end per la comprensione di immagini e testo, che sono ampiamente applicabili a molti scenari AI reali. La macchina virtuale per l'apprendimento avanzato tenta inoltre di facilitare l'individuazione della vasta gamma di strumenti ed esempi della DSVM presentando un catalogo degli strumenti ed esempi nella macchina virtuale. In termini di strumenti, la macchina virtuale per l'apprendimento avanzato fornisce numerosi framework di apprendimento diffusi, strumenti per acquisire e pre-elaborare immagini e dati di testo. Per un elenco completo degli strumenti, vedere la [pagina della panoramica relativa alla macchina virtuale per data science](overview.md#whats-included-in-the-data-science-vm). 

## <a name="next-steps"></a>Passaggi successivi

Per iniziare con la macchina virtuale per l'apprendimento avanzato, eseguire i passaggi seguenti:

* [Eseguire il provisioning di una macchina virtuale per l'apprendimento avanzato](provision-deep-learning-dsvm.md)
* [Usare la macchina virtuale per l'apprendimento avanzato](use-deep-learning-dsvm.md)
* [Riferimento agli strumenti](dsvm-deep-learning-ai-frameworks.md)
* [Esempi](dsvm-samples-and-walkthroughs.md)
