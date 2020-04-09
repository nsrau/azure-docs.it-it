---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/29/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 84736b7f1dcdf8b186fddbced5dd773e008c0dd2
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887418"
---
Il burst del disco è supportato per gli sSD premium. Il bursting è supportato su qualsiasi dimensione di disco SSD premium <512 GiB (P20 o inferiore). Queste dimensioni del disco supportano il bursting nel miglior sforzo e utilizzano un sistema di credito per gestire l'bursting. I crediti si accumulano in un bucket ogni volta che il traffico su disco è inferiore all'obiettivo di prestazioni di cui è stato eseguito il provisioning per le dimensioni del disco e consumano crediti quando il traffico supera l'obiettivo. Il traffico su disco viene monitorato sia per le operazioni di I/O al secondo che per la larghezza di banda nella destinazione di cui è stato eseguito il provisioning. Il burst del disco non ignorerà le limitazioni delle dimensioni della macchina virtuale (VM) su IOPS o velocità effettiva.

Il burst del disco è abilitato per impostazione predefinita nelle nuove distribuzioni delle dimensioni del disco che lo supportano. Le dimensioni dei dischi esistenti, se supportano il burst del disco, possono consentire il bursting tramite uno dei metodi seguenti:

- Scollegare e ricollegare il disco.
- Arrestare e avviare la macchina virtuale.

## <a name="burst-states"></a>Stati burst

Tutte le dimensioni del disco applicabili del burst inizieranno con un bucket di credito burst completo quando il disco è collegato a una macchina virtuale. La durata massima di bursting è determinata dalle dimensioni del bucket di credito burst. È possibile accumulare crediti inutilizzati fino alle dimensioni del bucket di credito. In qualsiasi momento, il bucket di credito del burst del disco può trovarsi in uno dei tre stati seguenti: 

- Accumulo, quando il traffico del disco utilizza meno dell'obiettivo di prestazioni di cui è stato eseguito il provisioning. È possibile accumulare credito se il traffico del disco supera le operazioni di I/O al secondo o gli obiettivi di larghezza di banda o entrambi. È comunque possibile accumulare crediti I/O quando si utilizza la larghezza di banda del disco completa, viceversa.  

- In diminuzione, quando il traffico su disco utilizza più dell'obiettivo di prestazioni di cui è stato eseguito il provisioning. Il traffico burst utilizzerà in modo indipendente i crediti da Operazioni di I/O al secondo o dalla larghezza di banda. 

- Costante rimanente, quando il traffico del disco è esattamente all'obiettivo di prestazioni di cui è stato eseguito il provisioning. 

Le dimensioni del disco che forniscono il supporto per l'esplosione insieme alle specifiche burst sono riepilogate nella tabella seguente.

## <a name="regional-availability"></a>Disponibilità a livello di area

Il burst del disco è disponibile in tutte le aree del cloud pubblico.

## <a name="disk-sizes"></a>Dimensione disco

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Scenari di esempio

Per darti un'idea migliore di come funziona, ecco alcuni scenari di esempio:

- Uno scenario comune che può trarre vantaggio dall'burst del disco è l'avvio più rapido della macchina virtuale e l'avvio dell'applicazione nei dischi del sistema operativo. Prendiamo come esempio una macchina virtuale Linux con un'immagine oS GiB 8.Take a Linux VM with an 8 GiB OS image as an example. Se si usa un disco P2 come disco del sistema operativo, la destinazione di cui è stato eseguito il provisioning è 120 IOPS e 25 MiB. All'avvio della macchina virtuale, si verifica un picco di lettura per il disco del sistema operativo che carica i file di avvio. Con l'introduzione del bursting, è possibile leggere alla velocità massima burst di 3500 IOPS e 170 MiB, accelerando il tempo di caricamento di almeno 6x. Dopo l'avvio della macchina virtuale, il livello di traffico sul disco del sistema operativo è in genere basso, poiché la maggior parte delle operazioni sui dati da parte dell'applicazione sarà sui dischi dati collegati. Se il traffico è inferiore alla destinazione di cui è stato eseguito il provisioning, si accumulano crediti.

- Se si ospita un ambiente Desktop virtuale remoto, ogni volta che un utente attivo avvia un'applicazione come AutoCAD, il traffico di lettura verso il disco del sistema operativo aumenta in modo significativo. In questo caso, il traffico burst consumerà i crediti accumulati, consentendo di andare oltre la destinazione di cui è stato eseguito il provisioning e avviando l'applicazione molto più velocemente.

- Un disco P1 ha una destinazione di cui è stato eseguito il provisioning di 120 IOPS e 25 MiB. Se il traffico effettivo sul disco era di 100 operazioni di I/O al secondo e 20 MiB nell'intervallo di 1 secondo, i 20 I/O inutilizzati e i 5 MB vengono accreditati nel bucket burst del disco. I crediti nel bucket burst possono essere usati in un secondo momento quando il traffico supera la destinazione di cui è stato eseguito il provisioning, fino al limite massimo del burst. Il limite massimo di burst definisce il limite massimo di traffico su disco anche se si dispone di crediti burst da cui consumare. In questo caso, anche se nel bucket di credito sono in grado di avere 10.000 I/O, un disco P1 non può emettere più del burst massimo di 3.500 I/O al secondo.  
