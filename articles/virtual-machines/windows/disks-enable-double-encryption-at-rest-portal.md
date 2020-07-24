---
title: Abilitare la crittografia doppia su REST-Managed disks portale di Azure
description: Abilitare la crittografia doppia per i dati dei dischi gestiti usando il portale di Azure.
author: roygara
ms.date: 07/23/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b244ef36e4e1a15327c053402b28ee2272fc71f0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136216"
---
# <a name="azure-portal---enable-double-encryption-at-rest-on-your-managed-disks"></a>portale di Azure abilitare la crittografia doppia sui dischi gestiti

Archiviazione su disco di Azure supporta la crittografia doppia per i dischi gestiti. Per informazioni di carattere generale sulla crittografia dei dati inattivi, oltre ad altri tipi di crittografia del disco gestito, vedere la sezione relativa alla crittografia [a doppio](disk-encryption.md#double-encryption-at-rest) inattivo dell'articolo crittografia del disco.

[!INCLUDE [virtual-machines-double-encryption-at-rest-portal](../../../includes/virtual-machines-disks-double-encryption-at-rest-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Azure PowerShell-abilitare le chiavi gestite dal cliente con la crittografia lato server-Managed Disks](disks-enable-customer-managed-keys-powershell.md)
- [Esempi di modelli di Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)