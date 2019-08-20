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
ms.openlocfilehash: b20ff261939dd97a74d27f5ec7f21eae2665f474
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574523"
---
# <a name="quickstart---create-cloudsimple-service"></a>Guida introduttiva-creare un servizio CloudSimple

Per iniziare, creare la soluzione VMware di Azure da CloudSimple nel portale di Azure.

Il servizio CloudSimple consente di usare la soluzione VMware di Azure con CloudSimple.  La creazione del servizio consente di acquistare nodi, riservare nodi e creare cloud privati.  È possibile aggiungere il servizio CloudSimple in ogni area di Azure in cui è disponibile il servizio CloudSimple.  Il servizio definisce la rete perimetrale della soluzione VMware di Azure tramite CloudSimple.  Questa rete perimetrale viene usata per i servizi che includono VPN, ExpressRoute e connettività Internet ai cloud privati.

Per aggiungere il servizio CloudSimple, è necessario creare una subnet del gateway. La subnet del gateway viene usata quando si crea la rete perimetrale e richiede un blocco CIDR/28. Lo spazio degli indirizzi della subnet del gateway deve essere univoco. Non può sovrapporsi ad alcuno degli spazi di indirizzi della rete locale o dello spazio di indirizzi della rete virtuale di Azure.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Abilita provider di risorse Microsoft. VMwareCloudSimple

Attenersi alla procedura seguente per abilitare il provider di risorse per il servizio CloudSimple.

1. Selezionare **Tutti i servizi**.
2. Cercare e selezionare le **sottoscrizioni**.

    ![Seleziona sottoscrizioni](media/cloudsimple-service-select-subscriptions.png)

3. Selezionare la sottoscrizione in cui si vuole abilitare il servizio CloudSimple.
4. Fare clic su **provider di risorse** per la sottoscrizione.
5. Utilizzare **Microsoft. VMwareCloudSimple** per filtrare il provider di risorse.
6. Selezionare il provider di risorse **Microsoft. VMwareCloudSimple** e fare clic su **registra**.

    ![Registrare il provider di risorse](media/cloudsimple-service-enable-resource-provider.png)

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

## <a name="purchase-nodes"></a>Acquistare nodi

Per configurare la capacità con pagamento in base al consumo per un ambiente cloud privato CloudSimple, eseguire prima il provisioning dei nodi nel portale di Azure.

1. Selezionare **Tutti i servizi**.
2. Cercare i **nodi CloudSimple**.

    ![Cerca nodi CloudSimple](media/create-cloudsimple-node-search.png)

3. Selezionare i **nodi CloudSimple**.
4. Fare clic su **Aggiungi** per creare i nodi.

    ![Aggiungi nodi CloudSimple](media/create-cloudsimple-node-add.png)

5. Selezionare la sottoscrizione in cui si vogliono acquistare i nodi di CloudSimple.
6. Selezionare il gruppo di risorse per i nodi. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il prefisso per identificare i nodi.
8. Selezionare il percorso per le risorse del nodo.
9. Selezionare il percorso dedicato per ospitare le risorse del nodo.
10. Selezionare il tipo di nodo. È possibile scegliere l' [opzione CS28 o CS36](cloudsimple-node.md). La seconda opzione include la capacità di calcolo e di memoria massima.
11. Selezionare il numero di nodi di cui eseguire il provisioning.
12. Selezionare **Rivedi e crea**.
13. Esaminare le impostazioni. Per modificare le impostazioni, fare clic su **indietro**.
14. Selezionare **Create**.

## <a name="next-steps"></a>Passaggi successivi

* [Creazione di un cloud privato e configurazione dell'ambiente](quickstart-create-private-cloud.md)
* Altre informazioni sul [servizio CloudSimple](cloudsimple-service.md)
