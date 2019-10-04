---
title: Che cos'è una zona privata di DNS di Azure
description: Panoramica di una zona DNS privata
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 462c873221a4bdc622a9b118b6699a9719a5f88d
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961223"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Che cos'è una zona DNS di Azure privata

DNS privato di Azure fornisce un servizio DNS protetto e affidabile per gestire e risolvere i nomi di dominio in una rete virtuale senza la necessità di aggiungere una soluzione DNS personalizzata. Usando le zone DNS private è possibile usare i nomi di dominio personalizzati anziché i nomi forniti da Azure attualmente disponibili. 

I record contenuti in una zona DNS privata non sono risolvibili da Internet. La risoluzione DNS in una zona DNS privata funziona solo dalle reti virtuali collegate.

È possibile collegare una zona DNS privata a una o più reti virtuali creando [collegamenti alla rete virtuale](./private-dns-virtual-network-links.md).
È anche possibile abilitare la funzionalità di [registrazione automatica](./private-dns-autoregistration.md) per gestire automaticamente il ciclo di vita dei record DNS per le macchine virtuali distribuite in una rete virtuale.

## <a name="limits"></a>Limiti

Per comprendere il numero di zone DNS private che è possibile creare in una sottoscrizione e il numero di set di record supportati in una zona DNS privata, vedere [limiti di DNS di Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)

## <a name="restrictions"></a>Restrizioni

* Le zone DNS private con etichetta singola non sono supportate. La zona DNS privata deve avere due o più etichette. Ad esempio, contoso.com ha due etichette separate da un punto. Una zona DNS privata può avere un massimo di 34 etichette.
* Non è possibile creare deleghe di zona (record NS) in una zona DNS privata. Se si intende usare un dominio figlio, è possibile creare direttamente il dominio come zona DNS privata e collegarlo alla rete virtuale senza configurare una delega del server dei nomi dalla zona padre.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una zona privata nel DNS di Azure tramite [Azure PowerShell](./private-dns-getstarted-powershell.md) o l'[interfaccia della riga di comando di Azure](./private-dns-getstarted-cli.md).

* Consultare alcuni [scenari comuni sulle zone private](./private-dns-scenarios.md) che possono essere realizzati con le zone private in DNS di Azure.

* Per domande e risposte comuni sulle zone private in DNS di Azure, tra cui i comportamenti specifici previsti per determinati tipi di operazioni, vedere [Domande frequenti su DNS privato](./dns-faq-private.md).
