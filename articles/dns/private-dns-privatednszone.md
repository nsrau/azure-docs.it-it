---
title: Che cos'è una zona privata DNS di AzureWhat is an Azure DNS private zone
description: Panoramica di una zona DNS privata
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: a951bc07c4a8ed42b1c116332d13674656bbaafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646796"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Che cos'è una zona DNS di Azure privata

DNS privato di Azure fornisce un servizio DNS protetto e affidabile per gestire e risolvere i nomi di dominio in una rete virtuale senza la necessità di aggiungere una soluzione DNS personalizzata. Usando le zone DNS private è possibile usare i nomi di dominio personalizzati anziché i nomi forniti da Azure attualmente disponibili. 

I record contenuti in una zona DNS privata non sono risolvibili da Internet. La risoluzione DNS rispetto a una zona DNS privata funziona solo da reti virtuali ad essa collegate.

È possibile collegare una zona DNS privata a una o più reti virtuali creando collegamenti di [rete virtuale.](./private-dns-virtual-network-links.md)
È inoltre possibile abilitare la funzionalità [di registrazione automatica](./private-dns-autoregistration.md) per gestire automaticamente il ciclo di vita dei record DNS per le macchine virtuali distribuite in una rete virtuale.

## <a name="limits"></a>Limiti

Per comprendere il numero di zone DNS private che è possibile creare in una sottoscrizione e il numero di set di record supportati in una zona DNS privata, vedere Limiti DNS di [AzureTo](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) understand how many private DNS zones you can create in a subscription and how many record sets are supported in a private DNS zone see Azure DNS limits

## <a name="restrictions"></a>Restrizioni

* Le zone DNS private con etichetta singola non sono supportate. La zona DNS privata deve avere due o più etichette. Ad esempio, contoso.com dispone di due etichette separate da un punto. Una zona DNS privata può avere un massimo di 34 etichette.
* Non è possibile creare deleghe di zona (record NS) in una zona DNS privata. Se si intende utilizzare un dominio figlio, è possibile creare direttamente il dominio come zona DNS privata e collegarlo alla rete virtuale senza impostare una delega del server dei nomi dalla zona padre.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una zona privata nel DNS di Azure tramite [Azure PowerShell](./private-dns-getstarted-powershell.md) o l'[interfaccia della riga di comando di Azure](./private-dns-getstarted-cli.md).

* Consultare alcuni [scenari comuni sulle zone private](./private-dns-scenarios.md) che possono essere realizzati con le zone private in DNS di Azure.

* Per domande e risposte comuni sulle zone private in DNS di Azure, tra cui i comportamenti specifici previsti per determinati tipi di operazioni, vedere [Domande frequenti su DNS privato](./dns-faq-private.md).
