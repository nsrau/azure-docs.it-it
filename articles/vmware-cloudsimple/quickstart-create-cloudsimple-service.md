---
title: Guida introduttiva alla soluzione VMware di Azure di CloudSimple-Crea servizio
description: Informazioni su come creare il servizio CloudSimple, i nodi di acquisto e i nodi di riserva
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 228ca0f43322c271b1a6db55a37c852ca1f66799
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828912"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Guida introduttiva-creare una soluzione VMware di Azure tramite il servizio CloudSimple

Per iniziare, creare la soluzione VMware di Azure da CloudSimple nel portale di Azure.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Soluzione VMware per CloudSimple-Panoramica dei servizi

Il servizio CloudSimple consente di usare la soluzione VMware di Azure con CloudSimple.  La creazione del servizio consente di effettuare il provisioning di nodi, riservare nodi e creare cloud privati.  È possibile aggiungere il servizio CloudSimple in ogni area di Azure in cui è disponibile il servizio CloudSimple.  Il servizio definisce la rete perimetrale della soluzione VMware di Azure tramite CloudSimple.  Questa rete perimetrale viene usata per i servizi che includono VPN, ExpressRoute e connettività Internet ai cloud privati.

Per aggiungere il servizio CloudSimple, è necessario creare una subnet del gateway. La subnet del gateway viene usata quando si crea la rete perimetrale e richiede un blocco CIDR/28. Lo spazio degli indirizzi della subnet del gateway deve essere univoco. Non può sovrapporsi ad alcuno degli spazi di indirizzi della rete locale o dello spazio di indirizzi della rete virtuale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Allocare un blocco CIDR/28 per la subnet del gateway.  È necessaria una subnet del gateway per ogni servizio CloudSimple ed è univoca per l'area in cui è stata creata. La subnet del gateway viene usata per la soluzione VMware di Azure dai servizi di rete CloudSimple Edge e richiede un blocco CIDR/28. Lo spazio degli indirizzi della subnet del gateway deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente CloudSimple.  Le reti che comunicano con CloudSimple includono reti locali e reti virtuali di Azure.

Verificare i [prerequisiti di rete](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Creare il servizio

1. Selezionare **Tutti i servizi**.
2. Cercare il **servizio CloudSimple**.

    ![Cerca servizio CloudSimple](media/create-cloudsimple-service-search.png)

3. Selezionare **Servizi CloudSimple**.
4. Fare clic su **Aggiungi** per creare un nuovo servizio.

    ![Aggiungere il servizio CloudSimple](media/create-cloudsimple-service-add.png)

5. Selezionare la sottoscrizione in cui si vuole creare il servizio CloudSimple.
6. Selezionare il gruppo di risorse per il servizio. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il nome per identificare il servizio.
8. Immettere il CIDR per il gateway del servizio. Specificare una subnet/28 che non si sovrappone ad alcuna subnet locale, subnet di Azure o subnet CloudSimple pianificate. Non è possibile modificare il CIDR dopo che il servizio è stato creato.

    ![Creazione del servizio CloudSimple](media/create-cloudsimple-service.png)

9. Fare clic su **OK**.

Il servizio viene creato e aggiunto all'elenco dei servizi.

## <a name="provision-nodes"></a>Effettuare il provisioning dei nodi

Per configurare la capacità con pagamento in base al consumo per un ambiente cloud privato CloudSimple, eseguire prima il provisioning dei nodi nel portale di Azure.

1. Selezionare **Tutti i servizi**.
2. Cercare i **nodi CloudSimple**.

    ![Cerca nodi CloudSimple](media/create-cloudsimple-node-search.png)

3. Selezionare i **nodi CloudSimple**.
4. Fare clic su **Aggiungi** per creare i nodi.

    ![Aggiungi nodi CloudSimple](media/create-cloudsimple-node-add.png)

5. Selezionare la sottoscrizione in cui si vuole eseguire il provisioning dei nodi CloudSimple.
6. Selezionare il gruppo di risorse per i nodi. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il prefisso per identificare i nodi.
8. Selezionare il percorso per le risorse del nodo.
9. Selezionare il percorso dedicato per ospitare le risorse del nodo.
10. Selezionare il [tipo di nodo](cloudsimple-node.md).
11. Selezionare il numero di nodi di cui eseguire il provisioning.
12. Selezionare **Rivedi e crea**.
13. Esaminare le impostazioni. Per modificare le impostazioni, fare clic su **indietro**.
14. Selezionare **Create**.

## <a name="next-steps"></a>Passaggi successivi

* [Creazione di un cloud privato e configurazione dell'ambiente](quickstart-create-private-cloud.md)
* Altre informazioni sul [servizio CloudSimple](https://docs.azure.cloudsimple.com/cloudsimple-service)
