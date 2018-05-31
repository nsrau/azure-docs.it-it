---
title: Risolvere i problemi relativi a riavvii imprevisti di VM con dischi rigidi virtuali collegati nelle VM di Azure Linux | Microsoft Docs
description: Risoluzione dei problemi relativi a riavvii imprevisti di VM di Linux.
keywords: connessione SSH rifiutata, errore SSH, SSH Azure, connessione SSH non riuscita
services: virtual-machines-linux
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 29aecf748ddda280e6f42be0bb3c986d90a3d7f9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364421"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Risolvere i problemi relativi a riavvii imprevisti di VM con dischi rigidi virtuali collegati

Se una macchina virtuale di Azure contiene numerosi dischi rigidi virtuali (VHD, Virtual Hard Disk) collegati nello stesso account di archiviazione, è possibile che vengano superati gli obiettivi di scalabilità dell'account di archiviazione determinando un riavvio imprevisto della macchina virtuale. Controllare le metriche al minuto per l'account di archiviazione (**TotalRequests**/**TotalIngress**/**TotalEgress**) per verificare se esistono picchi che superano gli obiettivi di scalabilità per un account di archiviazione. Vedere [Le metriche indicano un aumento di PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) per sapere come si stabilisce se si è verificata una limitazione nell'account di archiviazione.

In generale, ogni singola operazione di input o output eseguita su un disco rigido virtuale di una macchina virtuale viene trasformata in operazione **Get Page** o **Put Page** nel BLOB di pagine sottostante. Di conseguenza, è possibile utilizzare le IOPS previste per l'ambiente per stabilire quanti VHD si possono avere in un singolo account di archiviazione in base al comportamento specifico dell'applicazione. Microsoft consiglia di includere al massimo 40 dischi in un singolo account di archiviazione. Vedere [Obiettivi di scalabilità e prestazioni di Archiviazione di Azure](../../storage/common/storage-scalability-targets.md) per i dettagli relativi agli attuali obiettivi di scalabilità per gli account di archiviazione, in particolare la velocità totale delle richieste e la larghezza di banda totale per il tipo di account di archiviazione in uso.

Se si superano gli obiettivi di scalabilità per il proprio account di archiviazione, inserire i dischi rigidi virtuali in più account per ridurre l'attività in ogni singolo account.
