---
title: Che cos'è una sottorisorsa di collegamento di rete virtuale delle zone private DNS di AzureWhat is a virtual network link subresource of Azure DNS private zones
description: Panoramica della sottorisorsa di collegamento di rete virtuale una zona privata DNS di AzureOverview of virtual network link sub resource a Azure DNS private zone
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646762"
---
# <a name="what-is-a-virtual-network-link"></a>Che cos'è un collegamento di rete virtuale?

Dopo aver creato una zona DNS privata in Azure, non è immediatamente accessibile da alcuna rete virtuale. È necessario collegarlo a una rete virtuale prima che una macchina virtuale ospitata in tale rete possa accedere alla zona DNS privata.
Per collegare una zona DNS privata a una rete virtuale, è necessario creare un collegamento di rete virtuale nella zona DNS privata. Ogni zona DNS privata dispone di una raccolta di risorse figlio di collegamento di rete virtuale. Ognuna di queste risorse rappresenta una connessione a una rete virtuale.

È possibile collegare una rete virtuale a una zona DNS privata come rete virtuale di registrazione o come rete virtuale di risoluzione.

## <a name="registration-virtual-network"></a>Rete virtuale di registrazione

Quando si [crea un collegamento](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) tra una zona DNS privata e una rete virtuale, è possibile attivare la registrazione [automatica](./private-dns-autoregistration.md) dei record DNS per le macchine virtuali. Se si sceglie questa opzione, la rete virtuale diventa una rete virtuale di registrazione per la zona DNS privata. Viene creato automaticamente un record DNS per le macchine virtuali distribuite nella rete. I record DNS vengono creati per le macchine virtuali già distribuite nella rete virtuale. Dal punto di vista della rete virtuale, la zona DNS privata diventa la zona di registrazione per tale rete virtuale.
Una zona DNS privata può avere più reti virtuali di registrazione, tuttavia a ogni rete virtuale può essere associata esattamente una zona di registrazione.

## <a name="resolution-virtual-network"></a>Risoluzione della rete virtuale

Quando si crea un collegamento di rete virtuale in una zona DNS privata e si sceglie di non abilitare la registrazione automatica dei record DNS, la rete virtuale viene considerata come una rete virtuale solo risoluzione. I record DNS per le macchine virtuali distribuite in tali reti non verranno creati automaticamente nella zona DNS privata collegata. Tuttavia, le macchine virtuali distribuite in una rete di questo tipo possono eseguire correttamente query sui record DNS dalla zona DNS privata. Questi record possono essere creati manualmente dall'utente o possono essere popolati da altre reti virtuali che sono state collegate come reti di registrazione con la zona DNS privata.
A una zona DNS privata possono essere associate più reti virtuali con risoluzione e a una rete virtuale possono essere associate più zone di risoluzione.

## <a name="limits"></a>Limiti

Per comprendere il numero di reti di registrazione e risoluzione, è possibile creare collegamenti a zone DNS private, vedere Limiti DNS di AzureTo understand how how many registration and resolution [networks,](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) you can link to private DNS zones see Azure DNS Limits

## <a name="other-considerations"></a>Altre considerazioni

* Le reti virtuali distribuite tramite il modello di distribuzione classica non sono supportate.

* È possibile creare un solo collegamento tra una zona DNS privata e una rete virtuale.

* Ogni collegamento di rete virtuale in una zona DNS privata deve avere un nome univoco nel contesto della zona DNS privata. È possibile avere collegamenti con lo stesso nome in diverse zone DNS private.

* Dopo aver creato un collegamento di rete virtuale, controllare il campo "Stato collegamento" della risorsa collegamento di rete virtuale. A seconda delle dimensioni della rete virtuale, potrebbero essere stati alcuni minuti prima che il collegamento venga eseguito e lo stato del collegamento venga modificato in *Completato*.

* Quando si elimina una rete virtuale, tutti i collegamenti di rete virtuale e i record DNS registrati automaticamente associati in diverse zone DNS private vengono eliminati automaticamente.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come collegare una rete virtuale a una zona DNS privata tramite il portale di [AzureLearn](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) how to link a virtual network to a private DNS zone using Azure portal

* Informazioni su come creare una zona privata nel DNS di Azure tramite [Azure PowerShell](./private-dns-getstarted-powershell.md) o l'[interfaccia della riga di comando di Azure](./private-dns-getstarted-cli.md).

* Consultare alcuni [scenari comuni sulle zone private](./private-dns-scenarios.md) che possono essere realizzati con le zone private in DNS di Azure.

* Per domande e risposte comuni sulle zone private in DNS di Azure, tra cui i comportamenti specifici previsti per determinati tipi di operazioni, vedere [Domande frequenti su DNS privato](./dns-faq-private.md).
