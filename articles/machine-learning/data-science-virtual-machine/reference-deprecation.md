---
title: 'Informazioni di riferimento: DSVM image Deprecation'
description: Informazioni dettagliate sulle deprecazioni che interessano la Data Science Virtual Machine di Azure (DSVM)
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 4968ac08b75141ed84994ca287215a34728232c4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333339"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Informazioni di riferimento: deprecazione di immagini DSVM

Di seguito vengono illustrati i suggerimenti per la gestione delle deprecazioni future nel Data Science Virtual Machine di Azure.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: migrazione di dischi dati

Il supporto dell'immagine di Windows 2012 DSVM viene interrotto il 5 novembre 2019. Per eseguire la migrazione di un disco dati da Windows DSVM 2012 esistente a un DSVM Windows 2016, seguire questa procedura:

1. Creare una nuova DSVM di Windows 2016, seguendo le istruzioni riportate [qui](./provision-vm.md#create-your-dsvm).
1. Scollegare i dischi dati esistenti dall'immagine di Windows 2012 seguendo [queste istruzioni](../../virtual-machines/windows/detach-disk.md).
1. Alleghi il disco dal passaggio precedente all'immagine di Windows 2016 usando [queste istruzioni](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
