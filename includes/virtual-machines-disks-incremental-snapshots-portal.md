---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628404"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Disponibilità a livello di area
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portale


1. Accedere al portale di [Azure](https://portal.azure.com/) e passare al disco da creare per l'istantanea.
1. Sul disco, selezionare **Crea un'istantanea**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Screenshot. Il blade del disco, con l'opzione di creazione di un'istantanea evidenziata, in quanto è ciò che è necessario selezionare.":::

1. Selezionare il gruppo di risorse da usare e immettere un nome.
1. Selezionare **Incrementale (Incrementale)** e selezionare **Revisione ( Crea )**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Screenshot. Creare un pannello snapshot, immettere il nome e selezionare incrementale, quindi creare lo snapshot.":::

1. Selezionare **Crea**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Screenshot. La pagina di convalida per lo snapshot, confermare le selezioni e quindi creare lo snapshot.":::

## <a name="next-steps"></a>Passaggi successivi

Se si vuole vedere codice di esempio che illustra la funzionalità differenziale degli snapshot incrementali tramite .NET, vedere Copiare backup di dischi gestiti di Azure in [un'altra area con funzionalità differenziali di snapshot incrementali.](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)
