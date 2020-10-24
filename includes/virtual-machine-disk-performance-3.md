---
title: includere file
description: includere file
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 8eff9da82fdfa5749fd1c2bc04652d5c8ce8dfd2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518067"
---
![Screenshot dell'output f i o che Mostra r = 22.8 k evidenziato.](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Il Standard_D8s_v3 può ottenere un totale di 28.600 IOPS. Usando le metriche, analizziamo cosa sta succedendo e identifichiamo il collo di bottiglia di i/o di archiviazione. Nel riquadro sinistro selezionare **metrica**:

![Screenshot che mostra le metriche evidenziate nel riquadro sinistro.](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Esaminiamo prima di tutto la metrica della **percentuale di IOPS utilizzata nella cache della macchina virtuale** :

![Screenshot che mostra la percentuale di utilizzo di V M memorizzato nella cache I/O.](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Questa metrica indica che viene usato il 61% degli IOPS 16.000 assegnati ai IOPS memorizzati nella cache nella macchina virtuale. Questa percentuale indica che il collo di bottiglia di i/o di archiviazione non è con i dischi memorizzati nella cache perché non è al 100%. Si osserverà ora la metrica **della percentuale** di operazioni di i/o al secondo utilizzata per la macchina virtuale:

![Screenshot che mostra la percentuale di utilizzo della V M non memorizzata nella cache.](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Questa metrica è al 100%. Indica che vengono utilizzati tutti gli IOPS 12.800 assegnati alle operazioni di i/o non memorizzate nella cache nella macchina virtuale. Un modo per correggere questo problema consiste nel modificare le dimensioni della macchina virtuale con una dimensione maggiore in grado di gestire i/o aggiuntivi. Ma prima di procedere, esaminiamo il disco collegato per individuare il numero di IOPS che vengono visualizzati. Controllare il disco del sistema operativo esaminando la **percentuale di IOPS utilizzata del disco del sistema operativo**:

![Screenshot che mostra la percentuale di utilizzo del disco I/o.](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Questa metrica indica che è in uso circa il 90% del 5.000 IOPS di cui è stato effettuato il provisioning per questo disco del sistema operativo P30. Questa percentuale indica che non è presente alcun collo di bottiglia sul disco del sistema operativo. Controllare ora i dischi dati collegati alla VM esaminando la **percentuale di IOPS del disco dati utilizzata**:

![Screenshot che mostra la percentuale di utilizzo di I/O del disco dati.](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Questa metrica indica che la percentuale di IOPS media utilizzata in tutti i dischi collegati è intorno al 42%. Questa percentuale viene calcolata in base ai IOPS usati dai dischi e che non vengono serviti dalla cache dell'host. Si esaminerà in dettaglio questa metrica applicando la *suddivisione* in base a queste metriche e suddividendo il valore lun:

![Screenshot che mostra la percentuale di utilizzo dei dischi dati con suddivisione.](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Questa metrica indica che i dischi dati collegati in LUN 3 e 2 usano circa il 85% delle operazioni di i/o al secondo di cui è stato effettuato il provisioning. Di seguito è riportato un diagramma di come appare l'i/o dell'architettura di VM e dischi:

![Diagramma dell'esempio di metrica di archiviazione O di archiviazione.](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
