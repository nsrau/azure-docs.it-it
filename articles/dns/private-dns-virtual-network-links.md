---
title: Che cos'è una sottorisorsa collegamento rete virtuale di zone private di DNS di Azure
description: Panoramica della sottorisorsa collegamento rete virtuale una zona privata di DNS di Azure
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: c5675dea2676122d96276b7cdb90b4c3c5340c7a
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961183"
---
# <a name="what-is-a-virtual-network-link"></a>Che cos'è un collegamento di rete virtuale?

Dopo aver creato una zona DNS privata in Azure, non è immediatamente accessibile da alcuna rete virtuale. È necessario collegarlo a una rete virtuale prima che una macchina virtuale ospitata in tale rete possa accedere alla zona DNS privata.
Per collegare una zona DNS privata a una rete virtuale, è necessario creare un collegamento di rete virtuale nella zona DNS privata. Ogni zona DNS privata dispone di una raccolta di risorse figlio collegamento rete virtuale. Ognuna di queste risorse rappresenta una connessione a una rete virtuale.

È possibile collegare una rete virtuale a una zona DNS privata come rete virtuale di registrazione o come rete virtuale di risoluzione.

## <a name="registration-virtual-network"></a>Rete virtuale di registrazione

Quando si [Crea un collegamento](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) tra una zona DNS privata e una rete virtuale, è possibile attivare la [registrazione](./private-dns-autoregistration.md) automatici dei record DNS per le macchine virtuali. Se si sceglie questa opzione, la rete virtuale diventa una rete virtuale di registrazione per la zona DNS privata. Viene creato automaticamente un record DNS per le macchine virtuali distribuite nella rete. I record DNS vengono creati per le macchine virtuali già distribuite nella rete virtuale. Dal punto di vista della rete virtuale, la zona DNS privata diventa l'area di registrazione per la rete virtuale.
Una zona DNS privata può avere più reti virtuali di registrazione, tuttavia a ogni rete virtuale può essere associata esattamente una zona di registrazione.

## <a name="resolution-virtual-network"></a>Rete virtuale di risoluzione

Quando si crea un collegamento di rete virtuale in una zona DNS privata e si sceglie di non abilitare la registrazione dei record DNS, la rete virtuale viene considerata come una rete virtuale di sola risoluzione. I record DNS per le macchine virtuali distribuite in tali reti non verranno creati automaticamente nella zona DNS privata collegata. Tuttavia, le macchine virtuali distribuite in una rete di questo tipo possono eseguire correttamente query sui record DNS dalla zona DNS privata. Questi record possono essere creati manualmente dall'utente o possono essere popolati da altre reti virtuali collegate come reti di registrazione con la zona DNS privata.
Una zona DNS privata può avere più reti virtuali di risoluzione e a una rete virtuale possono essere associate più zone di risoluzione.

## <a name="limits"></a>Limiti

Per comprendere il numero di reti di registrazione e di risoluzione, è possibile collegarsi a zone DNS private, vedere [limiti di DNS di Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)

## <a name="other-considerations"></a>Altre considerazioni

* Le reti virtuali distribuite con il modello di distribuzione classica non sono supportate.

* È possibile creare un solo collegamento tra una zona DNS privata e una rete virtuale.

* Ogni collegamento di rete virtuale in una zona DNS privata deve avere un nome univoco all'interno del contesto della zona DNS privata. È possibile avere collegamenti con lo stesso nome in zone DNS private diverse.

* Dopo aver creato un collegamento di rete virtuale, selezionare il campo "stato collegamento" della risorsa collegamento rete virtuale. A seconda delle dimensioni della rete virtuale, possono essere necessari alcuni minuti prima che il collegamento sia operativo e lo stato del collegamento venga modificato in *completato*.

* Quando si elimina una rete virtuale, tutti i collegamenti alla rete virtuale e i record DNS registrati automaticamente associati in zone DNS private diverse vengono eliminati automaticamente.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come collegare una rete virtuale a una zona DNS privata usando [portale di Azure](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)

* Informazioni su come creare una zona privata nel DNS di Azure tramite [Azure PowerShell](./private-dns-getstarted-powershell.md) o l'[interfaccia della riga di comando di Azure](./private-dns-getstarted-cli.md).

* Consultare alcuni [scenari comuni sulle zone private](./private-dns-scenarios.md) che possono essere realizzati con le zone private in DNS di Azure.

* Per domande e risposte comuni sulle zone private in DNS di Azure, tra cui i comportamenti specifici previsti per determinati tipi di operazioni, vedere [Domande frequenti su DNS privato](./dns-faq-private.md).
