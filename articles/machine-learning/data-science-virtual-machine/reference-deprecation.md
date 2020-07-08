---
title: "Riferimento: Data Science Virtual Machine deprecazione dell'immagine"
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni dettagliate sulle deprecazioni che interessano la Data Science Virtual Machine di Azure
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80754761"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Informazioni di riferimento: deprecazione di immagini DSVM

Di seguito vengono illustrati i suggerimenti per la gestione delle deprecazioni future nel Data Science Virtual Machine di Azure.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: migrazione di dischi dati

Il 31 dicembre 2019 si arresterà il supporto dell'immagine di Windows 2012 DSVM. Per eseguire la migrazione di un disco dati da Windows DSVM 2012 esistente a un DSVM Windows 2016, seguire questa procedura:

1. Creare una nuova DSVM di Windows 2016, seguendo le istruzioni riportate [qui](./provision-vm.md#create-your-dsvm).
1. Scollegare i dischi dati esistenti dall'immagine di Windows 2012 seguendo [queste istruzioni](../../virtual-machines/windows/detach-disk.md).
1. Alleghi il disco dal passaggio precedente all'immagine di Windows 2016 usando [queste istruzioni](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

## <a name="centos"></a>CentOS

I nuovi utenti devono usare le immagini Ubuntu o Windows più recenti. CentOS rimarrà disponibile per l'uso con i modelli di soluzione esistenti.