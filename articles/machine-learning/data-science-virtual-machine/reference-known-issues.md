---
title: 'Riferimento: Problemi noti & risoluzione dei problemi'
titleSuffix: Azure Data Science Virtual  Machine
description: Ottenere un elenco dei problemi noti, soluzioni alternative e risoluzione dei problemi per Azure Data Science Virtual MachineGet a list of the known issues, workarounds, and troubleshooting for Azure Data Science Virtual Machine
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206521"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Problemi noti e risoluzione dei problemi della macchina virtuale di analisi scientifica dei dati di AzureKnown issues and troubleshooting the Azure Data Science Virtual Machine

Questo articolo consente di trovare e correggere errori o errori che potrebbero verificarsi quando si usa la macchina virtuale di analisi scientifica dei dati di Azure.This article helps you find and correct errors or failures you might come in the us when using the Azure Data Science Virtual Machine.

## <a name="python-package-installation-issues"></a>Problemi di installazione dei pacchetti Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Installazione di pacchetti con interruzioni di pip dipendenze su LinuxInstalling packages with pip breaks dependencies on Linux

Utilizzare `sudo pip install` invece di `pip install` installare i pacchetti.

## <a name="disk-encryption-issues"></a>Problemi di crittografia dischi

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Crittografia del disco non riesce nella DSVM di Ubuntu

Azure Disk Encryption (ADE) isn't currently supported on the Ubuntu DSVM. Per risolvere il problema, è consigliabile configurare la [crittografia lato server dei dischi gestiti di Azure.](../../virtual-machines/windows/disk-encryption.md)

## <a name="tool-appears-disabled"></a>Lo strumento appare disabilitato

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V non funziona in Windows DSVM

Che Hyper-V inizialmente non funziona su Windows è il comportamento previsto. Per le prestazioni di avvio, abbiamo disabilitato alcuni servizi. Per abilitare Hyper-V:

1. Aprire la barra di ricerca in Windows DSVM
1. Digitare "Servizi",
1. Impostare tutti i servizi Hyper-V su "Manuale"
1. Impostare "Gestione macchina virtuale Hyper-V" su "Automatico"

La schermata finale dovrebbe essere simile alla seguente:Your final screen should look like this:

   ![Abilitare Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

