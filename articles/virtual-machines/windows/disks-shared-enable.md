---
title: Abilitare i dischi condivisi per i dischi gestiti di AzureEnable shared disks for Azure managed disks
description: Configurare un disco gestito di Azure con dischi condivisi (anteprima) in modo da poterlo condividere tra più macchine virtualiConfigure an Azure managed disk with shared disks (preview) so that you can share it across multiple VMs
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e7ada64a50d6ace6ea4d34db87e0501d0311071d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471695"
---
# <a name="enable-shared-disk"></a>Abilita disco condiviso

Questo articolo illustra come abilitare la funzionalità dischi condivisi (anteprima) per i dischi gestiti di Azure.This article covers how to enable the shared disks (preview) feature for Azure managed disks. Dischi condivisi di Azure (anteprima) è una nuova funzionalità per i dischi gestiti di Azure che consente di collegare un disco gestito a più macchine virtuali (VM) contemporaneamente. La connessione di un disco gestito a più macchine virtuali consente di distribuire nuove applicazioni cluster o di eseguire la migrazione di applicazioni cluster esistenti in Azure.Attaching a managed disk to multiple VMs allows you to either deploy new or migrate existing clustered applications to Azure. 

Se si cercano informazioni concettuali sui dischi gestiti con dischi condivisi abilitati, vedere [Dischi condivisi](disks-shared.md)di Azure .
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]