---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c2ce39c40ad535b4a66349fcbb4529333218555a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512665"
---
Il supporto delle unità SSD Premium in qualsiasi dimensione di disco < = 512 GiB (P20 o di seguito). Queste dimensioni del disco supportano il proporziono al massimo sforzo e utilizzano un sistema di credito per gestire il picchio. I crediti si accumulano in un bucket di espansione ogni volta che il traffico del disco è al di sotto dell'obiettivo di prestazioni con provisioning per le dimensioni del disco e utilizza crediti quando il traffico supera la destinazione. Il traffico su disco viene monitorato in base a IOPS e larghezza di banda nella destinazione sottoposta a provisioning.

Per impostazione predefinita, l'espansione del disco è abilitata nelle nuove distribuzioni delle dimensioni dei dischi che la supportano. Le dimensioni dei dischi esistenti, se supportano la modalità di espansione del disco, possono consentire l'espansione tramite uno dei metodi seguenti:

- Scollegare e riconnettere il disco.
- Arrestare e avviare la macchina virtuale.

## <a name="burst-states"></a>Stati di espansione

Tutte le dimensioni del disco applicabili a impulsi iniziano con un bucket di credito a espansione completa quando il disco è collegato a una macchina virtuale. La durata massima dell'espansione è determinata dalla dimensione del bucket di credito di espansione. È possibile accumulare solo crediti inutilizzati fino alle dimensioni del bucket di credito. In qualsiasi momento, il bucket di credito con picchi di disco può trovarsi in uno dei tre stati seguenti: 

- Accumulo, quando il traffico del disco sta utilizzando meno della destinazione di prestazioni con provisioning. È possibile accumulare il credito se il traffico su disco è superiore a IOPS o destinazioni della larghezza di banda o entrambi. È comunque possibile accumulare crediti di i/o quando si utilizza una larghezza di banda completa del disco, viceversa.  

- In declino, quando il traffico del disco utilizza un numero di volte superiore rispetto alla destinazione di prestazioni con provisioning. Il traffico di espansione utilizzerà in modo indipendente i crediti da IOPS o larghezza di banda. 

- Costante rimanente, quando il traffico del disco è esattamente in corrispondenza della destinazione di prestazioni con provisioning. 

Nella tabella riportata di seguito vengono riepilogate le dimensioni dei dischi che forniscono il supporto per l'espansione in sequenza con le specifiche di espansione.

## <a name="regional-availability"></a>Disponibilità internazionale

Attualmente, l'espansione del disco è disponibile solo nell'area Stati Uniti centro-occidentali.

## <a name="disk-sizes"></a>Dimensione disco

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Scenari di esempio

Per fornire un'idea più approfondita del funzionamento, ecco alcuni scenari di esempio:

- Uno scenario comune che può trarre vantaggio dall'espansione del disco è l'avvio più veloce delle macchine virtuali e l'avvio dell'applicazione nei dischi del sistema operativo. Come esempio, usare una VM Linux con un'immagine del sistema operativo 8 GiB. Se si usa un disco P2 come disco del sistema operativo, la destinazione di cui è stato effettuato il provisioning è 120 IOPS e 25 MBps. Quando viene avviata la macchina virtuale, il disco del sistema operativo carica i file di avvio. Con l'introduzione dell'espansione, è possibile leggere con la massima velocità di espansione di 3500 IOPS e 170 MBps, accelerando il tempo di caricamento di almeno 6x. Dopo l'avvio della macchina virtuale, il livello di traffico sul disco del sistema operativo è in genere basso, perché la maggior parte delle operazioni sui dati da parte dell'applicazione avverrà sui dischi dati collegati. Se il traffico è inferiore alla destinazione di cui è stato effettuato il provisioning, si accumuleranno crediti.

- Se si ospita un ambiente desktop virtuale remoto, ogni volta che un utente attivo avvia un'applicazione come AutoCAD, il traffico verso il disco del sistema operativo aumenta in modo significativo. In questo caso, il traffico di espansione utilizzerà crediti accumulati, consentendo di superare la destinazione di cui è stato effettuato il provisioning e di avviare l'applicazione molto più velocemente.

- Un disco P1 ha una destinazione con provisioning di 120 IOPS e 25 MBps. Se il traffico effettivo sul disco era 100 IOPS e 20 MBps nell'intervallo di 1 secondo precedente, i 20 IOs e 5 MB non usati vengono accreditati sul bucket di espansione del disco. I crediti nel bucket di espansione possono essere usati in un secondo momento quando il traffico supera la destinazione di cui è stato effettuato il provisioning, fino al limite massimo di picchi. Il limite massimo di impulsi definisce il limite di traffico del disco anche se sono presenti crediti di espansione da utilizzare. In questo caso, anche se si dispone di 10.000 IOs nel bucket di credito, un disco P1 non può emettere più del picco massimo di 3.500 IO al secondo.  
