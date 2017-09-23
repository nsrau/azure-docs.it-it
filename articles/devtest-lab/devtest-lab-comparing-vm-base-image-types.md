---
title: Confronto tra immagini personalizzate e formule nei lab di sviluppo/test | Documentazione Microsoft
description: "Conoscere le differenze tra le immagini personalizzate e le formule come basi per le macchine virtuali consente di decidere quale soluzione è più adatta per il proprio ambiente."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: a3cb259a-7d80-40ec-8ee8-45105704d589
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: ff771abc26c08f0adb977c29739d2f5c91924b21
ms.contentlocale: it-it
ms.lasthandoff: 03/24/2017

---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Confronto tra immagini personalizzate e formule nei lab di sviluppo/test
Sia le [immagini personalizzate](devtest-lab-create-template.md) che le [formule](devtest-lab-manage-formulas.md) possono essere usate come basi per [creare nuove macchine virtuali](devtest-lab-add-vm-with-artifacts.md). La differenza principale tra le immagini personalizzate e le formule è che un'immagine personalizzata è semplicemente un'immagine basata su un disco rigido virtuale, mentre una formula è un'immagine basata su un disco rigido virtuale *con in più* impostazioni preconfigurate, ad esempio dimensioni della macchina virtuale, rete virtuale e subnet, ed elementi. Le impostazioni preconfigurate vengono specificate con valori predefiniti che possono essere sostituiti al momento della creazione della macchina virtuale. Questo articolo illustra alcuni dei vantaggi e degli svantaggi relativi all'uso delle immagini personalizzate e delle formule.

## <a name="custom-image-pros-and-cons"></a>Vantaggi e svantaggi delle immagini personalizzate
Le immagini personalizzate offrono un modo statico e non modificabile per creare macchine virtuali da un ambiente di propria scelta. 

**Vantaggi**

* Il provisioning della macchina virtuale da un'immagine personalizzata è veloce poiché non vengono apportate modifiche dopo che la macchina virtuale viene attivata dall'immagine. In altre parole, non vi sono impostazioni da applicare poiché l'immagine personalizzata è semplicemente un'immagine senza impostazioni. 
* Le macchine virtuali create da un'unica immagine personalizzata sono identiche.

**Svantaggi**

* Se è necessario aggiornare alcuni aspetti dell'immagine personalizzata, si dovrà ricreare l'immagine.  

## <a name="formula-pros-and-cons"></a>Vantaggi e svantaggi delle formule
Le formule offrono un modo dinamico per creare macchine virtuali in base a una configurazione o alle impostazioni desiderate.

**Vantaggi**

* È possibile acquisire in tempo reale le modifiche apportate nell'ambiente in tempo reale usando gli elementi. Ad esempio, per installare una macchina virtuale con i bit più recenti della pipeline di rilascio o integrare il codice più recente dal repository, è possibile specificare semplicemente un elemento che consenta di distribuire i bit più recenti o integri il codice più recente nella formula insieme a un'immagine di base di destinazione. Ogni volta che la formula viene usata per creare macchine virtuali, il codice o i bit più recenti vengono integrati o distribuiti alla macchina virtuale. 
* A differenza delle immagini personalizzate, le formule possono definire impostazioni predefinite come le dimensioni delle macchine virtuali e le impostazioni della rete virtuale. 
* Le impostazioni salvate in una formula vengono visualizzate come valori predefiniti, ma possono essere modificate quando viene creata la macchina virtuale. 

**Svantaggi**

* La creazione di una macchina virtuale da una formula può richiedere più tempo rispetto alla creazione da un'immagine personalizzata.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Post di blog correlati
* [Custom images or formulas? (Immagini personalizzate o formule?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Passaggi successivi
- [Domande frequenti su DevTest Labs](devtest-lab-faq.md)
