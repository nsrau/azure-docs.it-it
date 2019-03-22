---
title: Informazioni sui nomi IDN in Azure Stack | Microsoft Docs
description: Informazioni sui nomi IDN caratteristiche e funzionalità in Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: ab867af76821f90c6a87c08d42affdef8192e201
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258140"
---
# <a name="introducing-idns-for-azure-stack"></a>Introduzione a iDNS per Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

i nomi IDN è una funzionalità di rete di Azure Stack che ti permette di risolvere nomi DNS esterni (ad esempio, https:\//www.bing.com.) Consente inoltre di registrare i nomi di rete virtuale interna. In questo modo, è possibile risolvere le macchine virtuali nella stessa rete virtuale con nome anziché indirizzi IP. Questo approccio elimina la necessità di fornire le voci di server DNS personalizzate. per altre informazioni sul servizio DNS, vedere la [Panoramica di DNS di Azure](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Che cosa fa iDNS?

Con i nomi IDN in Azure Stack, si ottengono le funzionalità seguenti, senza dover specificare voci dei server DNS personalizzate:

- Servizi di risoluzione nome DNS per carichi di lavoro tenant condivisi.
- Servizio DNS autorevole per la risoluzione dei nomi e la registrazione DNS nella rete virtuale tenant.
- Servizio Recursive DNS per la risoluzione dei nomi Internet dalla VM tenant. I tenant non è più necessario specificare le voci DNS personalizzate per la risoluzione dei nomi Internet (ad esempio, www.bing.com.)

È comunque possibile portare il proprio DNS e usare server DNS personalizzati. Tuttavia, utilizzando i nomi IDN, è possibile risolvere i nomi DNS Internet e connettersi ad altre macchine virtuali nella stessa rete virtuale, non è necessario creare voci DNS personalizzate.

## <a name="what-doesnt-idns-do"></a>Che cosa non iDNS?

I nomi IDN non consente di eseguire l'operazione, consiste nel creare un record DNS per un nome che può essere risolto dall'esterno della rete virtuale.

In Azure, hai la possibilità di specificare un'etichetta del nome DNS associato a un indirizzo IP pubblico. È possibile scegliere l'etichetta (prefisso), ma sceglie il suffisso, che è basato sull'area in cui si crea l'indirizzo IP pubblico di Azure.

![Esempio di etichetta del nome DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Come illustrato nella figura precedente, Azure creerà un record "A" in DNS per l'etichetta del nome DNS specificato nella zona **westus.cloudapp.azure.com**. Il prefisso e suffisso vengono combinati per comporre un [nome di dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) che può essere risolto da un punto qualsiasi sulla rete Internet pubblica.

Azure Stack supporta solo i nomi IDN per la registrazione del nome interno, in modo che non è possibile eseguire le operazioni seguenti:

- Creare un record DNS in una zona DNS ospitata esistente (ad esempio, local.azurestack.external.)
- Creare una zona DNS (ad esempio, Contoso.com).
- Creare un record nella zona DNS personalizzato.
- Supporta l'acquisto di nomi di dominio.

## <a name="next-steps"></a>Passaggi successivi

[Uso di DNS in Azure Stack](azure-stack-dns.md)
