---
title: Gestire reti virtuali-portale di Azure-database di Azure per MySQL-server flessibile
description: Creare e gestire reti virtuali per database di Azure per MySQL-server flessibile usando il portale di Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 28506ea5afe085e9f52e40495d9931ae0aeddf32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937314"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Creare e gestire reti virtuali per database di Azure per MySQL-server flessibile usando il portale di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Il server flessibile del Database di Azure per MySQL supporta due tipi di metodi di connettività di rete che si escludono a vicenda per la connessione al server flessibile. Le due opzioni sono:

- Accesso pubblico (indirizzi IP consentiti)
- Accesso privato (integrazione rete virtuale)

Questo articolo è incentrato sulla creazione di un server MySQL con **accesso privato (VNet Integration)** con portale di Azure. Con l'accesso privato (VNet Integration), è possibile distribuire il server flessibile nella propria [rete virtuale di Azure](../../virtual-network/virtual-networks-overview.md). Le reti virtuali di Azure forniscono comunicazioni di rete private e sicure. Con l'accesso privato, le connessioni al server MySQL sono limitate alla rete virtuale. Per ulteriori informazioni, vedere [accesso privato (integrazione VNet)](./concepts-networking.md#private-access-vnet-integration).

È possibile distribuire il server flessibile in una rete virtuale e in una subnet durante la creazione del server. Dopo aver distribuito il server flessibile, non è possibile spostarlo in un'altra rete virtuale, in una subnet o in un *accesso pubblico (indirizzi IP consentiti)* .

## <a name="prerequisites"></a>Prerequisiti
Per creare un server flessibile in una rete virtuale, è necessario quanto segue:
- Una [rete virtuale](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > La rete virtuale e la subnet devono trovarsi nella stessa area e nella stessa sottoscrizione del server flessibile.

-  [Delegare una subnet](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) a **Microsoft.DBforMySQL/flexibleServers**. Con questa delega solo i server flessibili di Database di Azure per MySQL possono usare tale subnet. Gli altri tipi di risorsa di Azure non possono trovarsi nella subnet delegata.

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>Creare un server flessibile per database di Azure per MySQL in una rete virtuale già esistente

1. Nell'angolo in alto a sinistra del portale selezionare **Crea una risorsa** (+).
2. Selezionare **Database** > **Database di Azure per MySQL**. È anche possibile immettere **MySQL** nella casella di ricerca per trovare il servizio.
3. Selezionare **Flexible server** (Server flessibile) come opzione di distribuzione.
4. Compilare il modulo **nozioni di base** .
5. Passare alla scheda **rete** per configurare il modo in cui si desidera connettersi al server.
6. Nel **metodo di connettività**selezionare **accesso privato (integrazione VNet)**. Passare a **rete virtuale** e selezionare la *rete virtuale* e la *subnet* già esistenti create come parte dei prerequisiti precedenti.
7. Selezionare **Rivedi e crea** per rivedere la configurazione del server flessibile.
8. Selezionare **Crea** per effettuare il provisioning del server. Il provisioning può richiedere alcuni minuti.

>[!Note]
> Dopo che il server flessibile è stato distribuito in una rete virtuale e in una subnet, non è possibile spostarlo in accesso pubblico (indirizzi IP consentiti).

## <a name="next-steps"></a>Passaggi successivi
- [Creare e gestire una rete virtuale di server flessibili per database di Azure per MySQL usando l'interfaccia della riga di](./how-to-manage-virtual-network-cli.md)comando
- Altre informazioni sulla [rete nel server flessibile database di Azure per MySQL](./concepts-networking.md)
- Ulteriori informazioni sulla [rete virtuale di server flessibili per database di Azure per MySQL](./concepts-networking.md#private-access-vnet-integration).
