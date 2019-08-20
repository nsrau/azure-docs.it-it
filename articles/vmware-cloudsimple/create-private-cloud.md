---
title: Creare una soluzione VMware di Azure con il cloud privato CloudSimple
description: Viene descritto come creare un cloud privato CloudSimple per estendere i carichi di lavoro VMware al cloud con flessibilità operativa e continuità
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 02a2bd311ea1e89a49eb12ef57a167a08eea5f98
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812261"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Creare un cloud privato CloudSimple

La creazione di un cloud privato ti aiuta a soddisfare diverse esigenze comuni per l'infrastruttura di rete:

* **Crescita**. Se è stato raggiunto un punto di aggiornamento hardware per l'infrastruttura esistente, un cloud privato consente di espandersi senza nuovi investimenti hardware necessari.

* **Espansione rapida**. Se si verificano esigenze di capacità temporanee o non pianificate, un cloud privato consente di creare la capacità aggiuntiva senza alcun ritardo.

* **Maggiore protezione**. Con un cloud privato di tre o più nodi, si ottengono la ridondanza automatica e la protezione della disponibilità elevata.

* **Esigenze dell'infrastruttura a lungo termine**. Se i Data Center sono alla capacità o si vuole ristrutturare per ridurre i costi, un cloud privato consente di ritirare i Data Center ed eseguire la migrazione a una soluzione basata sul cloud, pur rimanendo compatibile con le operazioni aziendali.

Quando si crea un cloud privato, si ottiene un singolo cluster vSphere e tutte le macchine virtuali di gestione create in tale cluster.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di poter creare il cloud privato, è necessario effettuare il provisioning dei nodi.  Per altre informazioni sul provisioning dei nodi, vedere l'articolo relativo al provisioning di [nodi per la soluzione VMware con CloudSimple-Azure](create-nodes.md) .

Allocare un intervallo CIDR per le subnet vSphere/rete VSAN per il cloud privato. Un cloud privato viene creato come ambiente VMware stack isolato (host ESXi, vCenter, rete VSAN e NSX) gestito da un server vCenter. I componenti di gestione vengono distribuiti nella rete selezionata per la CIDR delle subnet vSphere/rete VSAN. L'intervallo CIDR di rete è diviso in subnet diverse durante la distribuzione.  Lo spazio degli indirizzi della subnet vSphere/rete VSAN deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente CloudSimple.  Le reti che comunicano con CloudSimple includono reti locali e reti virtuali di Azure.  Per altre informazioni sulle subnet vSphere/rete VSAN, vedere [Panoramica di VLAN e subnet](cloudsimple-vlans-subnets.md).

* Prefisso intervallo CIDR per le subnet vSphere/rete VSAN minimo:/24 
* Prefisso intervallo CIDR per le subnet vSphere/rete VSAN massimo:/21

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

Accedere al [portale di CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Creazione di un nuovo cloud privato

1. Nella pagina **risorse** fare clic su **nuovo cloud privato**.

    ![Creazione di un cloud privato-come iniziare](media/create-pc-button.png)

2. Selezionare la località in cui ospitare le risorse del cloud privato.

3. Scegliere il tipo di nodo CS28 o CS36 di cui è stato effettuato il provisioning you'ev per il cloud privato. La seconda opzione include la capacità di calcolo e di memoria massima.

4. Selezionare il numero di nodi per il cloud privato. È possibile selezionare al massimo il numero di nodi disponibili di cui [è stato effettuato il provisioning di you'ev](create-nodes.md).

    ![Creare un cloud privato-impostazioni di base](media/create-private-cloud-basic-info.png)

5. Fare clic su **Avanti: Opzioni**avanzate.

6. Immettere l'intervallo CIDR per le subnet vSphere/rete VSAN. Verificare che l'intervallo CIDR non si sovrappongano con le subnet di Azure locali o altre (reti virtuali) o con la subnet del gateway.  Non usare alcun intervallo CIDR definito nelle reti virtuali di Azure.
    
    **Opzioni di intervallo CIDR:** /24,/23,/22 o/21. Un intervallo CIDR/24 supporta fino a nove nodi, un intervallo CIDR/23 supporta fino a 41 nodi e un intervallo CIDR/22 e/21 supporta fino a 64 nodi (il numero massimo di nodi in un cloud privato).

    > [!CAUTION]
    > Gli indirizzi IP nell'intervallo CIDR vSphere/rete VSAN sono riservati per l'uso da un'infrastruttura di cloud privato.  Non usare l'indirizzo IP in questo intervallo in una macchina virtuale.

7. Fare clic su **Avanti: Esaminare e creare**.

8. Esaminare le impostazioni. Se è necessario modificare le impostazioni, fare clic su **indietro**.

9. Fare clic su **Create**(Crea).

Il provisioning del cloud privato si avvierà quando si fa clic su Crea.  È possibile monitorare lo stato di avanzamento dalla pagina [attività](https://docs.azure.cloudsimple.com/activity/#tasks) nel portale di CloudSimple.  Il provisioning può richiedere da 30 minuti a due ore.  Al termine del provisioning, si riceverà un messaggio di posta elettronica.

## <a name="next-steps"></a>Passaggi successivi

* [Espandi cloud privato](expand-private-cloud.md)