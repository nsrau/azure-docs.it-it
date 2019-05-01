---
title: La soluzione Azure VMware da CloudSimple Quickstart - Crea servizio
description: Informazioni su come creare il servizio CloudSimple, l'acquisto nodi e riserva nodi
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b3b95db24f4b0f9a0cf8f5102dfeea5dc51e29f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577571"
---
# <a name="quickstart---create-service"></a>Guida introduttiva - creare servizio

Per iniziare, creare la soluzione VMware di Azure da CloudSimple nel portale di Azure.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Soluzione VMware da CloudSimple - Panoramica del servizio

Il servizio CloudSimple consente all'utente di Azure VMware Solution by CloudSimple.  Creazione del servizio consente di acquistare i nodi, di riservarlo nodi e creare cloud privati.  Si aggiunge il servizio CloudSimple in ogni area di Azure in cui il servizio CloudSimple è disponibile.  Il servizio definisce la rete perimetrale di Azure VMware Solution by CloudSimple.  Questa rete perimetrale viene usata per i servizi che includono la connettività VPN, ExpressRoute e Internet dei cloud privati.

Per aggiungere il servizio CloudSimple, è necessario creare una subnet del gateway. La subnet del gateway viene usata quando si crea la rete perimetrale e richiede / 28 blocco CIDR. Lo spazio degli indirizzi di subnet del gateway deve essere univoco. Non può sovrapporsi con qualsiasi spazio degli indirizzi di rete virtuale di Azure o spazi di indirizzi della rete locale.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Abilitare il provider di risorse Microsoft.VMwareCloudSimple

Attenersi alla procedura seguente per abilitare il provider di risorse per il servizio CloudSimple.

1. Selezionare **Tutti i servizi**.
2. Cercare e selezionare **sottoscrizioni**.

    ![Selezionare le sottoscrizioni](media/cloudsimple-service-select-subscriptions.png)

3. Selezionare la sottoscrizione in cui si desidera abilitare il servizio CloudSimple
4. Fare clic su **provider di risorse** per la sottoscrizione
5. Uso **Microsoft.VMwareCloudSimple** per filtrare il provider di risorse
6. Selezionare il **Microsoft.VMwareCloudSimple** provider di risorse e fare clic su **registrare**

    ![Registrare il provider di risorse](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>Creare il servizio

>[!NOTE]
> Servizio CloudSimple deve essere abilitato per la sottoscrizione. Se la sottoscrizione non è abilitato, si riceverà un errore quando si prova a creare il servizio.  Seguire i passaggi descritti in [CloudSimple Abilita servizio](https://docs.azure.cloudsimple.com/enable-cloudsimple-service) articolo per abilitare il servizio.

1. Selezionare **Tutti i servizi**.
2. Cercare **CloudSimple servizio**.

    ![Servizio di ricerca CloudSimple](media/create-cloudsimple-service-search.png)

3. Selezionare **CloudSimple servizi**.
4. Fare clic su **Add** per creare un nuovo servizio.

    ![Aggiungere servizio CloudSimple](media/create-cloudsimple-service-add.png)

5. Selezionare la sottoscrizione in cui si desidera creare il servizio CloudSimple.
6. Selezionare il gruppo di risorse per il servizio. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il nome per identificare il servizio.
8. Immettere il CIDR per il gateway del servizio. Specificare/28 subnet che non si sovrapponga con qualsiasi subnet locale, le subnet di Azure o subnet CloudSimple pianificata. È possibile modificare il CIDR dopo la creazione del servizio.

    ![Creazione del servizio CloudSimple](media/create-cloudsimple-service.png)

9. Fare clic su **OK**.

Il servizio viene creato e aggiunto all'elenco dei servizi.

## <a name="purchase-nodes"></a>Acquisto nodi

Per impostare il pagamento in base al capacità di passare la capacità per un ambiente Cloud privato CloudSimple, prima di tutto eseguire il provisioning di nodi nel portale di Azure.

1. Selezionare **Tutti i servizi**.
2. Cercare **CloudSimple nodi**.

    ![Ricerca CloudSimple nodi](media/create-cloudsimple-node-search.png)

3. Selezionare **CloudSimple nodi**.
4. Fare clic su **Add** per creare nodi.

    ![Aggiungere nodi CloudSimple](media/create-cloudsimple-node-add.png)

5. Selezionare la sottoscrizione in cui si desidera acquistare CloudSimple nodi.
6. Selezionare il gruppo di risorse per i nodi. Per aggiungere un nuovo gruppo di risorse, fare clic su **Crea nuovo**.
7. Immettere il prefisso per identificare i nodi.
8. Selezionare il percorso per le risorse di nodo.
9. Selezionare la posizione dedicata per ospitare le risorse di nodo.
10. Selezionare il tipo di nodo. È possibile scegliere il [opzione CS28 o CS36](cloudsimple-node.md). L'opzione di quest'ultima include la capacità di calcolo e memoria massima.
11. Selezionare il numero di nodi per eseguire il provisioning.
12. Selezionare **Rivedi e crea**.
13. Rivedere le impostazioni. Per modificare le impostazioni, fare clic su **Previous**.
14. Selezionare **Create**.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un Cloud privato e configurare l'ambiente](quickstart-create-private-cloud.md)
* Altre informazioni su [CloudSimple servizio](https://docs.azure.cloudsimple.com/cloudsimple-service)
