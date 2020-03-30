---
title: Che cos'è la funzionalità di registrazione automatica delle zone private DNS di AzureWhat is autoregistration feature of Azure DNS private zones
description: Panoramica della funzionalità di registrazione automatica delle zone private DNS di AzureOverview of autoregistration feature of Azure DNS private zones
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71961249"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Qual è la funzionalità di registrazione automatica delle zone private DNS di AzureWhat is the autoregistration feature of Azure DNS private zones

La funzionalità di registrazione automatica delle zone private delle zone private DNS di Azure elimina il problema della gestione dei record DNS per le macchine virtuali distribuite in una rete virtuale. Quando si collega una [rete virtuale](./private-dns-virtual-network-links.md) a una zona DNS privata e si abilita la registrazione automatica per tutte le macchine virtuali, i record DNS per le macchine virtuali distribuite nella rete virtuale vengono creati automaticamente nella zona DNS privata. Oltre ai record di aspetto avanti (record A), vengono creati automaticamente anche i record di ricerca inversa (record PTR) per le macchine virtuali.
Se si aggiungono altre macchine virtuali alla rete virtuale, i record DNS per queste macchine virtuali vengono creati automaticamente anche nella zona DNS privata collegata.

Quando si elimina una macchina virtuale, i record DNS per la macchina virtuale vengono eliminati automaticamente dalla zona DNS privata.

È possibile abilitare la registrazione automatica selezionando l'opzione "Abilita registrazione automatica" durante la creazione di un collegamento di rete virtuale.

![Abilita registrazione automatica](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Restrizioni

* La registrazione automatica funziona solo per le macchine virtuali. Per tutte le altre risorse, ad esempio i servizi di bilanciamento del carico interni e così via, è possibile creare manualmente i record DNS nella zona DNS privata collegata alla rete virtuale.
* I record DNS vengono creati automaticamente solo per la scheda NIC della macchina virtuale primaria. Se le macchine virtuali dispongono di più schede di interfaccia di rete, è possibile creare manualmente i record DNS per altre interfacce di rete.
* la registrazione automatica per i Record IPv6 (record AAAA) non è supportata.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una zona privata in DNS di Azure tramite [Azure PowerShell](./private-dns-getstarted-powershell.md) o l'[interfaccia della riga di comando di Azure](./private-dns-getstarted-cli.md).

* Consultare alcuni [scenari comuni sulle zone private](./private-dns-scenarios.md) che possono essere realizzati con le zone private in DNS di Azure.

* Per domande e risposte comuni sulle zone private in DNS di Azure, tra cui i comportamenti specifici previsti per determinati tipi di operazioni, vedere [Domande frequenti su DNS privato](./dns-faq-private.md).
