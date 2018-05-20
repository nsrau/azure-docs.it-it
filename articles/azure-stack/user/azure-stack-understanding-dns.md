---
title: Informazioni sui nomi IDN nello Stack di Azure | Documenti Microsoft
description: Informazioni sui nomi IDN caratteristiche e funzionalità in Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: df9c22877eeac381d936f2fb86f5720c9cc9c930
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
---
# <a name="introducing-idns-for-azure-stack"></a>Introduzione a nomi IDN per Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

i nomi IDN è una funzionalità di rete dello Stack di Azure che consente di risolvere i nomi DNS esterni (ad esempio, http://www.bing.com.) consente di registrare i nomi di rete virtuale interna. In questo modo, è possibile risolvere le macchine virtuali nella stessa rete virtuale con nome anziché indirizzo IP. Questo approccio elimina la necessità di fornire voci dei server DNS personalizzate. Per ulteriori informazioni su DNS, vedere la [Panoramica di DNS di Azure](https://docs.microsoft.com/en-us/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Scopo di nomi IDN

Con nomi IDN nello Stack di Azure, sono disponibili le seguenti funzionalità, senza dover specificare voci dei server DNS personalizzate:

* Condiviso di servizi di risoluzione DNS per i carichi di lavoro tenant.
* Servizio DNS autorevole per la risoluzione dei nomi e la registrazione DNS nella rete virtuale del tenant.
* Servizio Recursive DNS per la risoluzione dei nomi Internet da macchine virtuali tenant. I tenant non è più necessario specificare le voci DNS personalizzate per risolvere i nomi Internet (ad esempio, www.bing.com.)

È comunque possibile portare il propria DNS e utilizzare i server DNS personalizzati. Tuttavia, tramite nomi IDN, è possibile risolvere i nomi DNS Internet e connettersi ad altre macchine virtuali nella stessa rete virtuale, non è necessario creare voci DNS personalizzate.

## <a name="what-doesnt-idns-do"></a>Non i nomi IDN deve procedere?

I nomi IDN non consente di eseguire l'operazione, la creazione di un record DNS per un nome che può essere risolto dall'esterno della rete virtuale.

In Azure, è possibile specificare un'etichetta del nome DNS associato a un indirizzo IP pubblico. È possibile scegliere l'etichetta (prefisso), ma Azure sceglie il suffisso è basato sull'area in cui creare l'indirizzo IP pubblico.

![Esempio di un'etichetta del nome DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Come mostra l'immagine precedente, Azure creerà un record "A" in DNS per l'etichetta del nome DNS specificato nella zona **westus.cloudapp.azure.com**. Il prefisso e il suffisso vengono combinate per comporre un [nome di dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) che possono essere risolti da un punto qualsiasi nella rete Internet pubblica.

Stack di Azure supporta solo i nomi IDN per la registrazione del nome interno, pertanto non è possibile eseguire le operazioni seguenti:

* Creare un record DNS in una zona DNS ospitato esistente (ad esempio, local.azurestack.external.)
* Creare una zona DNS (ad esempio, Contoso.com).
* Creare un record nella zona DNS personalizzato.
* Supporta l'acquisto di nomi di dominio.

## <a name="next-steps"></a>Passaggi successivi

[Tramite DNS nello Stack di Azure](azure-stack-dns.md)