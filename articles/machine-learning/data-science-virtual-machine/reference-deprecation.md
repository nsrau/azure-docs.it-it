---
title: "Riferimento: Deprecazione dell'immagine dell'immagine della macchina virtuale di data scienceReference: Data Science Virtual Machine Image Deprecation"
titleSuffix: Azure Data Science Virtual Machine
description: Dettagli sulle deprecazioni che interessano la macchina virtuale di analisi scientifica dei dati di AzureDetails on deprecations affecting the Azure Data Science Virtual Machine
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754761"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Riferimento: Deprecazione delle immagini DSVMReference: Deprecation of DSVM Images

Di seguito vengono illustrati i suggerimenti per gestire le prossime deprecazioni nella macchina virtuale di analisi scientifica dei dati di Azure.Below we discuss suggestions for dealing ing imminent deprecations on the Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Migrazione dei dischi dati

Smetteremo di supportare l'immagine DSVM di Windows 2012 il 31 dicembre 2019. Per eseguire la migrazione di un disco dati dalla dSVM di Windows 2012 esistente a una DSVM di Windows 2016, attenersi alla seguente procedura:

1. Creare una nuova DSVM di Windows 2016, seguendo le istruzioni illustrate [di seguito](./provision-vm.md#create-your-dsvm).
1. Scollegare i dischi dati esistenti dall'immagine di Windows 2012 utilizzando [queste istruzioni.](../../virtual-machines/windows/detach-disk.md)
1. Collegare il disco del passaggio precedente all'immagine di Windows 2016 seguendo [queste istruzioni.](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)

## <a name="centos"></a>CentOS

I nuovi utenti devono utilizzare le immagini Ubuntu o Windows più recenti. CentOS rimarrà disponibile per l'utilizzo con i modelli di soluzione esistenti.