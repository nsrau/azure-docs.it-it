---
title: Sistemi operativi guest supportati per lo Stack di Azure | Documenti Microsoft
description: Questi sistemi operativi Guest può essere utilizzati nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: Brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 819d8a9580de435536bf184b10ffbc23c32b247b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Sistemi operativi guest supportata nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

## <a name="windows"></a>Windows

Stack di Azure supporta i sistemi operativi guest di Windows elencati nella tabella seguente:

| Sistema operativo | DESCRIZIONE | Disponibili in Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server, versione 1709 | 64 bit | Core con i contenitori |
| Windows Server 2016 | 64 bit |  Datacenter, Datacenter Core, Datacenter con i contenitori |
| Windows Server 2012 R2 | 64 bit |  Data center |
| Windows Server 2012 | 64 bit |  Data center |
| Windows Server 2008 R2 SP1 | 64 bit |  Data center |
| Windows Server 2008 SP2 | 64 bit |  Portare la propria immagine |
| Windows 10 *(vedere nota 1)* | 64 bit, Pro ed Enterprise | Portare la propria immagine |

***Nota 1:*** *per distribuire i sistemi operativi client Windows 10 nello Stack di Azure, è necessario disporre [Windows per ogni utente licenze](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) o si acquista tramite un provider di multi-tenant qualificato ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).*

Le immagini di Marketplace sono disponibili per pagare-come--l'utilizzo o licenze BYOL EA/SPLA (). Utilizzo di entrambi gli elementi in una singola istanza di Azure Stack non è supportato. Durante la distribuzione, Azure Stack inserisce una versione appropriata dell'agente guest nell'immagine.

 Edizioni datacenter sono disponibili in marketplace per il download; i clienti possono portare le proprie immagini server, incluse le altre edizioni. Le immagini client Windows non sono disponibili in Marketplace.

## <a name="linux"></a>Linux

Distribuzioni di Linux indicate come disponibile nel Marketplace includono l'agente Azure Linux di Windows necessarie (WALA). Se si introducono la propria immagine allo Stack di Azure, seguire le istruzioni in [immagini Linux aggiungere allo Stack Azure](azure-stack-linux.md).

> [!NOTE]
> Immagini personalizzate devono essere compilate con l'ultima versione WALA pubblica. Le versioni precedenti a 2.2.18 potrebbero non funzionare correttamente nello Stack di Azure.
>
> [cloud-init](https://cloud-init.io/) non è supportata nello Stack di Azure in questo momento.

| Distribuzione | DESCRIZIONE | Editore | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Basato su centOS 6,9 | 64 bit | Rogue Wave | Sì |
| 7.4 basato su centOS | 64 bit | Rogue Wave | Sì |
| ClearLinux | 64 bit | ClearLinux.org | Sì |
| Contenitore Linux |  64 bit | CoreOS | Stabile |
| Debian 8 "Jessie" | 64 bit | credativ |  Sì |
| Debian 9 "estensione" | 64 bit | credativ | Sì |
| Red Hat Enterprise Linux 7. x | 64 bit | Red Hat |Portare la propria immagine |
| SLES 11SP4 | 64 bit | SUSE | Sì |
| SLES 12SP3 | 64 bit | SUSE | Sì |
| Ubuntu 14.04-LTS | 64 bit | Canonical | Sì |
| Ubuntu 16.04-LTS | 64 bit | Canonical | Sì |
| 18.04-LTS Ubuntu | 64 bit | Canonical | Sì |

Altre distribuzioni Linux potrebbero essere supportati in futuro.
