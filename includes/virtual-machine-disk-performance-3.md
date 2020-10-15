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
ms.openlocfilehash: 14e74bfbcd087ccc1d8c5f2f10a8e44ed37cce84
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016547"
---
![Menu metrica](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Tuttavia, il Standard_D8s_v3 può ottenere un totale di 28.600 IOPs, l'uso delle metriche consente di esaminare cosa sta succedendo e identificare il collo di bottiglia di i/o di archiviazione. Per prima cosa, individuare il menu a sinistra del pulsante metriche e selezionarlo:

![Menu metrica](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Esaminiamo prima di tutto la metrica della **percentuale di IOPS utilizzata nella cache della macchina virtuale** :

![Percentuale di IOPS utilizzata nella cache della VM](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Questa metrica indica i 16.000 IOPs assegnati ai IOPs memorizzati nella cache nella macchina virtuale, 61% viene usato. Questo significa che il collo di bottiglia di i/o di archiviazione non è con i dischi memorizzati nella cache perché non è al 100%. Diamo ora un'occhiata alla metrica della percentuale di operazioni di i/o al secondo **della macchina virtuale** :

![Percentuale di IOPS utilizzata della VM non memorizzata nella cache](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Questa metrica è al 100%, indicando che vengono usati tutti gli IOPs 12.800 assegnati alle operazioni di i/o non memorizzate nella cache nella macchina virtuale. Un modo per correggere questo problema consiste nel modificare le dimensioni della macchina virtuale a una dimensione maggiore in grado di gestire i/o aggiuntivi. Tuttavia, prima di procedere, esaminiamo il disco collegato per vedere il numero di IOPs che vengono visualizzati. Esaminiamo prima di tutto il disco del sistema operativo esaminando la **percentuale di IOPS utilizzata del disco del sistema operativo**:

![Percentuale di IOPS del disco del sistema operativo utilizzata](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Questa metrica indica che è in uso il provisioning di 5.000 IOPs per questo disco del sistema operativo P30, circa il 90%. Questo significa che non vi è alcun collo di bottiglia sul disco del sistema operativo. Esaminiamo ora i dischi dati collegati alla VM esaminando la **percentuale di IOPS utilizzata del disco dati**:

![Percentuale di IOPS del disco dati utilizzata](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Questa metrica indica che la percentuale di IOPs media utilizzata in tutti i dischi collegati è intorno al 42%. Questa percentuale viene calcolata in base ai IOPs usati dai dischi e non vengono serviti dalla cache dell'host. Si esaminerà in dettaglio questa metrica per visualizzare il applicando la **suddivisione** in base a queste metriche e suddividendo il valore lun:

![Percentuale di IOPS del disco dati utilizzata con suddivisione](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Questa metrica indica che i dischi dati collegati in LUN 3 e 2 stanno usando circa il 85% delle operazioni di i/o al secondo di cui è stato effettuato il provisioning. Di seguito è riportato un diagramma dell'architettura dei dischi e delle macchine virtuali:

![Diagramma di esempio di metriche di i/o di archiviazione](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
