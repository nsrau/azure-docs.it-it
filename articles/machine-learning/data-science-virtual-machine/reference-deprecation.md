---
title: 'Informazioni di riferimento: DSVM image Deprecation'
description: Informazioni dettagliate sulle deprecazioni che interessano la Data Science Virtual Machine di Azure (DSVM)
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 252f9c66034dbadaf7a2e9e6f78665c26d414deb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456277"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Informazioni di riferimento: deprecazione di immagini DSVM

Di seguito vengono illustrati i suggerimenti per la gestione delle deprecazioni future nel Data Science Virtual Machine di Azure.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: migrazione di dischi dati

Il 31 dicembre 2019 si arresterà il supporto dell'immagine di Windows 2012 DSVM. Per eseguire la migrazione di un disco dati da Windows DSVM 2012 esistente a un DSVM Windows 2016, seguire questa procedura:

1. Creare una nuova DSVM di Windows 2016, seguendo le istruzioni riportate [qui](./provision-vm.md#create-your-dsvm).
1. Scollegare i dischi dati esistenti dall'immagine di Windows 2012 seguendo [queste istruzioni](../../virtual-machines/windows/detach-disk.md).
1. Alleghi il disco dal passaggio precedente all'immagine di Windows 2016 usando [queste istruzioni](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
