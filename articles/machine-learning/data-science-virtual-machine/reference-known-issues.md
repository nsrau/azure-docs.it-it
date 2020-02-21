---
title: 'Informazioni di riferimento: problemi noti & risoluzione dei problemi'
titleSuffix: Azure Data Science Virtual  Machine
description: Ottenere un elenco dei problemi noti, delle soluzioni alternative e della risoluzione dei problemi per Azure Data Science Virtual Machine
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: d022b1124146a1e506401e6cee257805e3a38fd3
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526549"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Problemi noti e risoluzione dei problemi relativi alla Data Science Virtual Machine di Azure

Questo articolo consente di individuare e correggere gli errori o gli errori riscontrati durante l'uso di Azure Data Science Virtual Machine.

## <a name="python-package-installation-issues"></a>Problemi di installazione dei pacchetti Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>L'installazione di pacchetti con pip interrompe le dipendenze in Linux

Utilizzare `sudo pip install` anziché `pip install` quando si installano i pacchetti.

## <a name="disk-encryption-issues"></a>Problemi di crittografia del disco

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>La crittografia del disco non riesce in Ubuntu DSVM

Crittografia dischi di Azure (ADE) non è attualmente supportata nella DSVM Ubuntu. Come soluzione alternativa, prendere in considerazione la configurazione della [crittografia di archiviazione di Azure con chiavi gestite dal cliente](../../storage/common/storage-encryption-keys-portal.md).

## <a name="tool-appears-disabled"></a>Lo strumento appare disabilitato

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V non funziona in Windows DSVM

Si tratta di un comportamento previsto, come per le prestazioni di avvio. alcuni servizi sono stati disabilitati. Per abilitare nuovamente, aprire la barra di ricerca in Windows DSVM, digitare "Services", quindi impostare tutti i servizi Hyper-V su "Manual" e impostare "Hyper-V Virtual Machine Management" su "Automatic".

La schermata finale dovrebbe essere simile alla seguente:

   ![Abilitare Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

