---
title: Sistemi operativi guest supportati per lo Stack di Azure | Documenti Microsoft
description: "Questi sistemi operativi Guest può essere utilizzati nello Stack di Azure."
services: azure-stack
documentationcenter: 
author: Brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 3eceb740b8115d2eaca517017f6158744d6e8e58
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Sistemi operativi guest supportata nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

## <a name="windows"></a>Windows
Stack di Azure supporta i sistemi operativi guest Windows sono elencati nella tabella seguente: le immagini in Marketplace sono disponibili per il download allo Stack di Azure. Le immagini client Windows non sono disponibili in Marketplace.

Durante la distribuzione, Azure Stack inserisce una versione appropriata dell'agente guest nell'immagine.

| Sistema operativo | DESCRIZIONE | Editore | OS Type (Tipo di sistema operativo) | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 SP1 | 64 bit | Microsoft | Windows | Data center |
| Windows Server 2012 | 64 bit | Microsoft | Windows | Data center |
| Windows Server 2012 R2 | 64 bit | Microsoft | Windows | Data center |
| Windows Server 2016 | 64 bit | Microsoft | Windows | Datacenter, Datacenter Core, Datacenter con i contenitori |
| Windows 10 *(vedere nota 1)* | 64 bit, Pro ed Enterprise | Microsoft | Windows | No  |

***Nota 1:****per distribuire sistemi operativi di client Windows 10 nello Stack di Azure, è necessario disporre [Windows licenze per utente](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) o tramite un provider di multi-tenant completo di acquisto ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).* 


## <a name="linux"></a>Linux

Distribuzioni Linux elencate di seguito sono necessari Windows Azure Linux agente (WALA).

> [!NOTE]   
> Immagini create con versioni WALA precedente rispetto al 2.2.3 *non* supportate e sono difficilmente distribuire. Alcune versioni di agente WALA sono noti inclusa nelle macchine virtuali di Azure Stack, incluse le versioni 2.2.12 e 2.2.13.
>
> [cloud init](https://cloud-init.io/) è supportato solo nelle distribuzioni Ubuntu nello Stack di Azure.

| Distribuzione | DESCRIZIONE | Editore | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Contenitore Linux |  64 bit | CoreOS | Stabile |
| Basato su centOS 6,9 | 64 bit | Rogue Wave | Sì |
| 7.4 basato su centOS | 64 bit | Rogue Wave | Sì |
| Debian 8 "Jessie" | 64 bit | credativ |  Sì |
| Debian 9 "estensione" | 64 bit | credativ | Sì |
| Red Hat Enterprise Linux 7. x (in sospeso) | 64 bit | Red Hat | No  |
| SLES 11SP4 | 64 bit | SUSE | Sì |
| SLES 12SP3 | 64 bit | SUSE | Sì |
| Ubuntu 14.04-LTS | 64 bit | Canonical | Sì |
| Ubuntu 16.04-LTS | 64 bit | Canonical | Sì |

Altre distribuzioni Linux potrebbero essere supportati in futuro.
