---
title: Manutenzione pianificata per macchine virtuali Windows | Microsoft Docs
description: "Informazioni sulla manutenzione pianificata di Azure e su come può influire sulle macchine virtuali Windows eseguite in Azure."
services: virtual-machines-windows
documentationcenter: 
author: drewm
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: drewm
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: fecc7666f80f7a9a5b2f8daaa65fd1766616fc85
ms.lasthandoff: 03/03/2017


---
# <a name="planned-maintenance-for-virtual-machines-in-azure"></a>Manutenzione pianificata per macchine virtuali in Azure
Informazioni sulla manutenzione pianificata di Azure e su come può influire sulla disponibilità delle macchine virtuali Windows. Questo articolo è disponibile anche per le [macchine virtuali Linux](virtual-machines-linux-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Questo articolo offre informazioni di background relative al processo di manutenzione pianificata di Azure. Se si vogliono risolvere eventuali problemi di riavvio indesiderato delle macchine virtuali, è possibile [leggere il post di blog post che illustra in dettaglio i log di riavvio delle VM](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="why-azure-performs-planned-maintenance"></a>Perché Azure esegue la manutenzione pianificata
Microsoft Azure esegue periodicamente aggiornamenti a livello globale per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host sottostante alle macchine virtuali. Molti di questi aggiornamenti vengono eseguiti senza alcun impatto sulle macchine virtuali o sui servizi cloud, inclusi gli aggiornamenti con mantenimento della memoria.

Tuttavia, alcuni aggiornamenti richiedono un riavvio delle macchine virtuali per applicare gli aggiornamenti necessari all'infrastruttura. Le macchine virtuali vengono arrestate durante l'applicazione delle patch all'infrastruttura e quindi riavviate.

Si noti che esistono due tipi di manutenzione che possono influire sulla disponibilità delle macchine virtuali: pianificata e non pianificata. Questa pagina descrive come Microsoft Azure esegue la manutenzione pianificata. Per altre informazioni sulla manutenzione non pianificata, vedere la pagina relativa al [confronto tra manutenzione pianificata e non pianificata](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

