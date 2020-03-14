---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/12/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 64aa01995460837c820c90010d7c4e3f3d78e6a2
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300232"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Disponibilità internazionale
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portal

Per creare e configurare snapshot incrementali nel portale di Azure globale, è necessario usare il collegamento seguente: [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots). La creazione di snapshot incrementali non è ancora disponibile nel portale di Azure globale.

1. Accedere al [portale di Azure](https://aka.ms/incrementalsnapshots) con il collegamento fornito e passare al disco per cui si vuole eseguire lo snapshot.
1. Sul disco, selezionare **crea uno snapshot**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text=" ":::

1. Selezionare il gruppo di risorse che si vuole usare e immettere un nome.
1. Selezionare **incrementale** e selezionare **Verifica + crea**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text=" ":::

1. Selezionare **Crea**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text=" ":::

## <a name="next-steps"></a>Passaggi successivi

Se si vuole vedere il codice di esempio che illustra la funzionalità differenziale degli snapshot incrementali, usando .NET, vedere [copiare i backup di Azure Managed disks in un'altra area con funzionalità differenziali di snapshot incrementali](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
