---
title: Selezionare immagini di VM Linux con l&quot;interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come determinare l&quot;editore, l&quot;offerta e lo SKU per le immagini durante la creazione di una macchina virtuale Linux con il modello di distribuzione di Resource Manager.
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 732767bcece6d892aee7a8e6976f527fc66bb48d


---
# <a name="select-linux-vm-images-with-the-azure-cli"></a>Selezionare immagini di VM Linux con l'interfaccia della riga di comando di Azure
Questo argomento descrive come trovare editori, offerte, SKU e versioni per ciascuna posizione di distribuzione. Per fare un esempio, alcune immagini di macchine virtuali Linux usate comunemente sono:

**Tabella delle immagini Linux di uso comune**

| PublisherName | Offerta | Sku |
|:--- |:--- |:--- |:--- |
| RedHat |RHEL |7,2 |
| credativ |Debian |8 |
| SUSE |openSUSE |13.2 |
| SUSE |SLES |12-SP1 |
| OpenLogic |CentOS |7.1 |
| Canonical |UbuntuServer |14.04.4-LTS |
| CoreOS |CoreOS |Stabile |

[!INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]




<!--HONumber=Nov16_HO3-->


