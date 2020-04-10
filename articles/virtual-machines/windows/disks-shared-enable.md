---
title: Abilitare i dischi condivisi per i dischi gestiti di AzureEnable shared disks for Azure managed disks
description: Configurare un disco gestito di Azure con dischi condivisi (anteprima) in modo da poterlo condividere tra più macchine virtualiConfigure an Azure managed disk with shared disks (preview) so that you can share it across multiple VMs
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1cb581f0914dbafff4a46510924115e82e42b990
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011141"
---
# <a name="enable-shared-disk"></a>Abilita disco condiviso

Questo articolo illustra come abilitare la funzionalità dischi condivisi (anteprima) per i dischi gestiti di Azure.This article covers how to enable the shared disks (preview) feature for Azure managed disks. Dischi condivisi di Azure (anteprima) è una nuova funzionalità per i dischi gestiti di Azure che consente di collegare un disco gestito a più macchine virtuali (VM) contemporaneamente. La connessione di un disco gestito a più macchine virtuali consente di distribuire nuove applicazioni cluster o di eseguire la migrazione di applicazioni cluster esistenti in Azure.Attaching a managed disk to multiple VMs allows you to either deploy new or migrate existing clustered applications to Azure. 

Se si cercano informazioni concettuali sui dischi gestiti con dischi condivisi abilitati, vedere [Dischi condivisi](disks-shared.md)di Azure .
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]