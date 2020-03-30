---
title: "Riferimento: Deprecazione dell'immagine dell'immagine della macchina virtuale di data scienceReference: Data Science Virtual Machine Image Deprecation"
titleSuffix: Azure Data Science Virtual Machine
description: Dettagli sulle deprecazioni che interessano la macchina virtuale di analisi scientifica dei dati di AzureDetails on deprecations affecting the Azure Data Science Virtual Machine
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525788"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Riferimento: Deprecazione delle immagini DSVMReference: Deprecation of DSVM Images

Di seguito vengono illustrati i suggerimenti per gestire le prossime deprecazioni nella macchina virtuale di analisi scientifica dei dati di Azure.Below we discuss suggestions for dealing ing imminent deprecations on the Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Migrazione dei dischi dati

Smetteremo di supportare l'immagine DSVM di Windows 2012 il 31 dicembre 2019. Per eseguire la migrazione di un disco dati dalla dSVM di Windows 2012 esistente a una DSVM di Windows 2016, attenersi alla seguente procedura:

1. Creare una nuova DSVM di Windows 2016, seguendo le istruzioni illustrate [di seguito](./provision-vm.md#create-your-dsvm).
1. Scollegare i dischi dati esistenti dall'immagine di Windows 2012 utilizzando [queste istruzioni.](../../virtual-machines/windows/detach-disk.md)
1. Collegare il disco del passaggio precedente all'immagine di Windows 2016 seguendo [queste istruzioni.](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)
