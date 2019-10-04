---
title: Che cos'è la funzionalità autoregistration delle zone private di DNS di Azure
description: Panoramica della funzionalità di registrazione dell'autoregistrazione delle zone private di DNS di Azure
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961249"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Qual è la funzionalità di registrazione autoregistrazione delle zone private di DNS di Azure

La funzionalità di registrazione automatica di zone private di DNS di Azure Elimina la gestione dei record DNS per le macchine virtuali distribuite in una rete virtuale. Quando si [collega una rete virtuale](./private-dns-virtual-network-links.md) a una zona DNS privata e si Abilita la registrazione automatica per tutte le macchine virtuali, i record DNS per le macchine virtuali distribuite nella rete virtuale vengono creati automaticamente nella zona DNS privata. Oltre ai record di aspetto (record A), vengono creati automaticamente anche i record di ricerca inversa (record PTR) per le macchine virtuali.
Se si aggiungono altre macchine virtuali alla rete virtuale, anche i record DNS per queste macchine virtuali vengono creati automaticamente nella zona DNS privata collegata.

Quando si elimina una macchina virtuale, i record DNS per la macchina virtuale vengono eliminati automaticamente dalla zona DNS privata.

È possibile abilitare la registrazione automatica selezionando l'opzione "Abilita registrazione automatica" durante la creazione di un collegamento di rete virtuale.

![Abilita registrazione automatica](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Restrizioni

* La registrazione automatici funziona solo per le macchine virtuali. Per tutte le altre risorse, ad esempio i bilanciamento del carico interno e così via, è possibile creare manualmente i record DNS nella zona DNS privata collegata alla rete virtuale.
* I record DNS vengono creati automaticamente solo per la scheda di interfaccia di rete primaria della macchina virtuale. Se le macchine virtuali hanno più di una scheda di interfaccia di rete, è possibile creare manualmente i record DNS per altre interfacce di rete.
* la registrazione autoregistrazione per IPv6 (record AAAA) non è supportata.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una zona privata in DNS di Azure tramite [Azure PowerShell](./private-dns-getstarted-powershell.md) o l'[interfaccia della riga di comando di Azure](./private-dns-getstarted-cli.md).

* Consultare alcuni [scenari comuni sulle zone private](./private-dns-scenarios.md) che possono essere realizzati con le zone private in DNS di Azure.

* Per domande e risposte comuni sulle zone private in DNS di Azure, tra cui i comportamenti specifici previsti per determinati tipi di operazioni, vedere [Domande frequenti su DNS privato](./dns-faq-private.md).
