---
title: Sistemi operativi guest supportati per Azure Stack | Microsoft Docs
description: Questi sistemi operativi Guest può essere utilizzati in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 67022b1d00c8982d7b6e6eff77ffb648bfc557ec
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301282"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Sistemi operativi guest supportati in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

## <a name="windows"></a> Windows

Azure Stack supporta i sistemi operativi guest di Windows elencati nella tabella seguente:

| Sistema operativo | DESCRIZIONE | Disponibile nel Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server, versione 1709 | 64 bit | Core con i contenitori |
| Windows Server 2016 | 64 bit |  Datacenter, Datacenter Core, Datacenter con contenitori |
| Windows Server 2012 R2 | 64 bit |  Data center |
| Windows Server 2012 | 64 bit |  Data center |
| Windows Server 2008 R2 SP1 | 64 bit |  Data center |
| Windows Server 2008 SP2 | 64 bit |  Portare la propria immagine |
| Windows 10 *(vedere nota 1)* | 64 bit, Pro ed Enterprise | Portare la propria immagine |

> [!NOTE]
> Per distribuire sistemi operativi client di Windows 10 in Azure Stack, è necessario disporre [Windows licenze per utente](https://www.microsoft.com/en-us/Licensing/product-licensing/windows10.aspx) o l'acquisto tramite un provider di multi-tenant qualificati ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).

Le immagini del Marketplace sono disponibili per come è a pagamento o licenze BYOL (con contratto Enterprise Agreement o SPLA). Non è supportato l'uso di entrambi gli elementi di una singola istanza di Azure Stack. Durante la distribuzione di Azure Stack inserisce una versione appropriata dell'agente guest nell'immagine.

Datacenter Edition sono disponibili nel marketplace per il download; i clienti possono trasferire le proprie immagini server, incluse le edizioni a altri. Le immagini client Windows non sono disponibili nel Marketplace.

## <a name="linux"></a>Linux

Distribuzioni di Linux indicate come disponibile nel Marketplace sono necessari Windows Azure Linux Agent (WALA). Se invece la propria immagine in Azure Stack, seguire le istruzioni in [immagini Linux di aggiungere ad Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Le immagini personalizzate devono essere compilate con la versione più recente di WALA pubblica o con la versione 2.2.20. Le versioni precedenti rispetto a 2.2.20 e tra 2.2.20 e 2.2.35 (esclusivo) potrebbero non funzionare correttamente in Azure Stack. 
>
> [cloud-init](https://cloud-init.io/) , in questo momento, non è supportato in Azure Stack.

| Distribuzione | DESCRIZIONE | Editore | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Basata su centOS 6.9 | 64 bit | Rogue Wave | Sì |
| Basata su centOS 7.4 | 64 bit | Rogue Wave | Sì |
| ClearLinux | 64 bit | ClearLinux.org | Sì |
| Contenitore Linux |  64 bit | CoreOS | Stabile |
| Debian 8 "Jessie" | 64 bit | credativ |  Sì |
| Debian 9 "Stretch" | 64 bit | credativ | Sì |
| Red Hat Enterprise Linux 7.x | 64 bit | Red Hat |Portare la propria immagine |
| SLES 11SP4 | 64 bit | SUSE | Sì |
| SLES 12SP3 | 64 bit | SUSE | Sì |
| Ubuntu 14.04-LTS | 64 bit | Canonical | Sì |
| Ubuntu 16.04-LTS | 64 bit | Canonical | Sì |
| Ubuntu 18.04-LTS | 64 bit | Canonical | Sì |

Per informazioni sul supporto di Red Hat Enterprise Linux, vedere [Red Hat e Azure Stack: Domande frequenti](https://access.redhat.com/articles/3413531).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Azure Stack Marketplace, vedere gli articoli seguenti:

- [Scaricare elementi di Marketplace](azure-stack-download-azure-marketplace-item.md)  
- [Creare e pubblicare un elemento del Marketplace](azure-stack-create-and-publish-marketplace-item.md)
