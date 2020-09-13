---
title: includere file
description: includere file
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 66380e0bab0a83d086ebebb5e595908cecd11643
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664143"
---
Questo articolo consente di chiarire le prestazioni del disco e il relativo funzionamento con quando si combinano macchine virtuali di Azure e dischi di Azure. Viene inoltre descritto come è possibile diagnosticare i colli di bottiglia per l'IO del disco e le modifiche che è possibile apportare per ottimizzare le prestazioni.

## <a name="how-does-disk-performance-work"></a>Come funzionano le prestazioni del disco?
Le macchine virtuali di Azure hanno limiti di prestazioni per IOPS e velocità effettiva in base al tipo e alle dimensioni della macchina virtuale. I dischi del sistema operativo e i dischi dati, che possono essere collegati alle macchine virtuali, hanno limiti di velocità effettiva e IOPs. Quando l'applicazione in esecuzione nelle macchine virtuali richiede più IOPS o velocità effettiva rispetto a quanto assegnato per la macchina virtuale o i dischi collegati, le prestazioni dell'applicazione vengono limitate. Quando si verifica questa situazione, l'applicazione presenta prestazioni non ottimali e può causare alcune conseguenze negative, ad esempio un aumento della latenza. Di seguito sono riportati alcuni esempi per consolidare questa operazione. Per semplificare il following di questi esempi, verranno esaminati solo i IOPs, ma la stessa logica si applica anche alla velocità effettiva.

## <a name="disk-io-capping"></a>Limitazione IO disco
Configurazione:
- Standard_D8s_v3 
    - IOPS non memorizzati nella cache: 12.800
- Disco del sistema operativo E30
    - IOPS: 500 
- 2 dischi dati E30
    - IOPS: 500

![Limitazione a livello di disco](media/vm-disk-performance/disk-level-throttling.jpg)

L'applicazione in esecuzione nella macchina virtuale effettua una richiesta che richiede 10.000 IOPs alla macchina virtuale. Tutti i quali sono consentiti dalla VM perché la macchina virtuale Standard_D8s_v3 può eseguire fino a 12.800 IOPs. Queste richieste di IOPs 10.000 vengono quindi suddivise in tre diverse richieste a dischi diversi. 1.000 IOPs vengono richiesti al disco del sistema operativo e 4.500 IOPs vengono richiesti a ogni disco dati. Poiché tutti i dischi collegati sono dischi E30 e possono gestire solo 500 IOPs, rispondono con 500 IOPs ciascuno. Le prestazioni dell'applicazione vengono quindi limitate dai dischi collegati e possono solo elaborare 1.500 IOPs. Potrebbe funzionare con prestazioni ottimali con 10.000 IOPS se venivano usati dischi migliori, ad esempio SSD Premium dischi P30.

## <a name="virtual-machine-io-capping"></a>Limitazione delle operazioni di i/o della macchina virtuale
Configurazione:
- Standard_D8s_v3 
    - IOPS non memorizzati nella cache: 12.800
- Disco del sistema operativo P30
    - IOPS: 5.000 
- 2 dischi dati P30 
    - IOPS: 5.000

![Limitazione a livello di macchina virtuale](media/vm-disk-performance/vm-level-throttling.jpg)

L'applicazione in esecuzione nella macchina virtuale effettua una richiesta che richiede 15.000 IOPs. Sfortunatamente, viene eseguito il provisioning della macchina virtuale Standard_D8s_v3 per gestire 12.800 IOPs. Da questo, l'applicazione è limitata dai limiti della macchina virtuale e deve quindi allocare gli IOPs 12.800 assegnati. Questi 12.800 IOPs richiesti vengono quindi suddivisi in tre richieste diverse per i diversi dischi. 4.267 IOPs vengono richiesti al disco del sistema operativo e 4.266 IOPs vengono richiesti a ogni disco dati. Poiché tutti i dischi collegati sono dischi P30, che possono gestire 5.000 IOPs, rispondono con gli importi richiesti.