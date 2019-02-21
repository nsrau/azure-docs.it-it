---
title: File di inclusione
description: File di inclusione
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3d100ec08b7b6d70366e605daf9c67edc6ab3bf3
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331069"
---
Obiettivi di scalabilità degli account di archiviazione Premium:

| Capacità account totale | Larghezza di banda totale per un account di archiviazione con ridondanza locale |
| --- | --- | 
| Capacità disco: 35 TB <br>Capacità snapshot: 10 TB | Fino a 50 gigabit al secondo per dati in ingresso<sup>1</sup> e in uscita<sup>2</sup> |

<sup>1</sup> Tutti i dati (richieste) inviati a un account di archiviazione

<sup>2</sup> Tutti i dati (risposte) ricevuti da un account di archiviazione

Se si usano account di archiviazione Premium per dischi non gestiti e l'applicazione supera gli obiettivi di scalabilità di un singolo account di archiviazione, si potrebbe prendere in considerazione la migrazione a Managed Disks. Se non si vuole eseguire la migrazione a Managed Disks, compilare l'applicazione per l'uso di più account di archiviazione. Quindi partizionare i dati tra tali account di archiviazione. Ad esempio, se si desidera collegare dischi da 51 TB tra più VM, distribuirli in due account di archiviazione. Il limite per un account di Archiviazione Premium singolo è 35 TB. Accertarsi che i dischi di cui viene effettuato il provisioning in un singolo account di archiviazione Premium non superino mai i 35 TB.