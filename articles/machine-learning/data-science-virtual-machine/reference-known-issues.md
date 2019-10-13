---
title: Problemi noti e risoluzione dei problemi
titleSuffix: Azure Data Science Virtual  Machine
description: Ottenere un elenco dei problemi noti, delle soluzioni alternative e della risoluzione dei problemi per Azure Data Science Virtual Machine
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: a0b22326a429edfa2f2b8741453215b42910891c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301919"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Problemi noti e risoluzione dei problemi relativi alla Data Science Virtual Machine di Azure

Questo articolo consente di individuare e correggere gli errori o gli errori riscontrati durante l'uso di Azure Data Science Virtual Machine.

## <a name="python-package-installation-issues"></a>Problemi di installazione dei pacchetti Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>L'installazione di pacchetti con pip interrompe le dipendenze in Linux

Quando si installano i pacchetti, utilizzare `sudo pip install` anziché `pip install`.

## <a name="disk-encryption-issues"></a>Problemi di crittografia del disco

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>La crittografia del disco non riesce in Ubuntu DSVM

Crittografia dischi di Azure (ADE) non è attualmente supportata nella DSVM Ubuntu. Come soluzione alternativa, prendere in considerazione la configurazione della [crittografia di archiviazione di Azure con chiavi gestite dal cliente](../../storage/common/storage-encryption-keys-portal.md).

## <a name="tool-appears-disabled"></a>Lo strumento appare disabilitato

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V non funziona in Windows DSVM

Si tratta di un comportamento previsto, come per le prestazioni di avvio. alcuni servizi sono stati disabilitati. Per abilitare nuovamente, aprire la barra di ricerca in Windows DSVM, digitare "Services", quindi impostare tutti i servizi Hyper-V su "Manual" e impostare "Hyper-V Virtual Machine Management" su "Automatic".

La schermata finale dovrebbe essere simile alla seguente:

   ![Abilitare Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

