---
title: Risolvere i problemi relativi ai riavvii imprevisti delle macchine virtuali con dischi rigidi virtuali collegati nelle macchine virtuali di Azure | Microsoft Docs
description: Come risolvere i problemi relativi ai riavvii imprevisti delle macchine virtuali.
keywords: connessione SSH rifiutata, errore SSH, SSH Azure, connessione SSH non riuscita
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: d3b54941d38424e71ac800d2e750ac9bbc96cde9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086874"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Risolvere i problemi relativi a riavvii imprevisti di VM con dischi rigidi virtuali collegati

Se una macchina virtuale di Azure contiene numerosi dischi rigidi virtuali (VHD, Virtual Hard Disk) collegati nello stesso account di archiviazione, è possibile che vengano superati gli obiettivi di scalabilità dell'account di archiviazione determinando un riavvio imprevisto della macchina virtuale. Controllare le metriche al minuto per l'account di archiviazione (**TotalRequests**/**TotalIngress**/**TotalEgress**) per verificare se esistono picchi che superano gli obiettivi di scalabilità per un account di archiviazione. Vedere [Le metriche indicano un aumento di PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) per sapere come si stabilisce se si è verificata una limitazione nell'account di archiviazione.

In generale, ogni singola operazione di input o output eseguita su un disco rigido virtuale di una macchina virtuale viene trasformata in operazione **Get Page** o **Put Page** nel BLOB di pagine sottostante. Di conseguenza, è possibile utilizzare le IOPS previste per l'ambiente per stabilire quanti VHD si possono avere in un singolo account di archiviazione in base al comportamento specifico dell'applicazione. Microsoft consiglia di includere al massimo 40 dischi in un singolo account di archiviazione. Vedere [Obiettivi di scalabilità e prestazioni di Archiviazione di Azure](../../storage/common/storage-scalability-targets.md) per i dettagli relativi agli attuali obiettivi di scalabilità per gli account di archiviazione, in particolare la velocità totale delle richieste e la larghezza di banda totale per il tipo di account di archiviazione in uso.

Se si superano gli obiettivi di scalabilità per il proprio account di archiviazione, inserire i dischi rigidi virtuali in più account per ridurre l'attività in ogni singolo account.
