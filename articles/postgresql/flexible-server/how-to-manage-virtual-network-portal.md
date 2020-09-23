---
title: Gestire reti virtuali-portale di Azure-database di Azure per PostgreSQL-server flessibile
description: Creare e gestire reti virtuali per database di Azure per PostgreSQL-server flessibile usando il portale di Azure
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 46d8fe6427b2a3e7811719792ac4bf67ddbcc3c5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940383"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Creare e gestire reti virtuali per database di Azure per PostgreSQL-server flessibile usando il portale di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Database di Azure per PostgreSQL: il server flessibile supporta due tipi di metodi di connettività di rete che si escludono a vicenda per la connessione al server flessibile. Le due opzioni sono:

* Accesso pubblico (indirizzi IP consentiti)
* Accesso privato (integrazione rete virtuale)

Questo articolo è incentrato sulla creazione di un server PostgreSQL con **accesso privato (VNet Integration)** con portale di Azure. Con l'accesso privato (VNet Integration), è possibile distribuire il server flessibile nella propria [rete virtuale di Azure](../../virtual-network/virtual-networks-overview.md). Le reti virtuali di Azure forniscono comunicazioni di rete private e sicure. Con l'accesso privato, le connessioni al server PostgreSQL sono limitate alla rete virtuale. Per ulteriori informazioni, vedere [accesso privato (integrazione VNet)](./concepts-networking.md#private-access-vnet-integration).

È possibile distribuire il server flessibile in una rete virtuale e in una subnet durante la creazione del server. Dopo aver distribuito il server flessibile, non è possibile spostarlo in un'altra rete virtuale, in una subnet o in un *accesso pubblico (indirizzi IP consentiti)* .

## <a name="prerequisites"></a>Prerequisiti
Per creare un server flessibile in una rete virtuale, è necessario quanto segue:
- Una [rete virtuale](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > La rete virtuale e la subnet devono trovarsi nella stessa area e nella stessa sottoscrizione del server flessibile.

-  Per [delegare una subnet](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) a **Microsoft. DBforPostgreSQL/flexibleServers**. Questa delega indica che solo i server flessibili di database di Azure per PostgreSQL possono usare tale subnet. Gli altri tipi di risorsa di Azure non possono trovarsi nella subnet delegata.

## <a name="create-azure-database-for-postgresql---flexible-server-in-an-already-existing-virtual-network"></a>Creare database di Azure per PostgreSQL-server flessibile in una rete virtuale già esistente

1. Nell'angolo in alto a sinistra del portale selezionare **Crea una risorsa** (+).
2. Selezionare **Database** > **Database di Azure per PostgreSQL**. È anche possibile immettere **PostgreSQL** nella casella di ricerca per trovare il servizio.
3. Selezionare **Flexible server** (Server flessibile) come opzione di distribuzione.
4. Compilare il modulo **nozioni di base** .
5. Passare alla scheda **rete** per configurare il modo in cui si desidera connettersi al server.
6. Nel **metodo di connettività**selezionare **accesso privato (integrazione VNet)**. Passare a **rete virtuale** e selezionare la *rete virtuale* e la *subnet* già esistenti create come parte dei prerequisiti precedenti.
7. Selezionare **Rivedi e crea** per rivedere la configurazione del server flessibile.
8. Selezionare **Crea** per effettuare il provisioning del server. Il provisioning può richiedere alcuni minuti.

>[!Note]
> Dopo che il server flessibile è stato distribuito in una rete virtuale e in una subnet, non è possibile spostarlo in accesso pubblico (indirizzi IP consentiti).
## <a name="next-steps"></a>Passaggi successivi
- [Creare e gestire database di Azure per PostgreSQL: rete virtuale server flessibile usando l'interfaccia della riga di comando di Azure](./how-to-manage-virtual-network-cli.md).
- Altre informazioni sulla [rete in database di Azure per PostgreSQL-server flessibile](./concepts-networking.md)
- Altre informazioni su [database di Azure per PostgreSQL-rete virtuale server flessibile](./concepts-networking.md#private-access-vnet-integration).