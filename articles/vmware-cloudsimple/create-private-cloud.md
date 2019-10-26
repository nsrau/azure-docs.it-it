---
title: 'Soluzione VMware di Azure di CloudSimple: creare un cloud privato CloudSimple'
description: Viene descritto come creare un cloud privato CloudSimple per estendere i carichi di lavoro VMware al cloud con flessibilità operativa e continuità
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893929"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Creare un cloud privato CloudSimple

Un cloud privato è uno stack VMware isolato che supporta host ESXi, vCenter, rete VSAN e NSX. I cloud privati vengono gestiti tramite il portale CloudSimple. Hanno il proprio server vCenter nel proprio dominio di gestione. Lo stack viene eseguito su nodi dedicati e nodi hardware bare metal isolati.

La creazione di un cloud privato ti aiuta a soddisfare diverse esigenze comuni per l'infrastruttura di rete:

* **Crescita**. Se è stato raggiunto un punto di aggiornamento hardware per l'infrastruttura esistente, un cloud privato consente di espandersi senza nuovi investimenti hardware necessari.

* **Espansione rapida**. Se si verificano esigenze di capacità temporanee o non pianificate, un cloud privato consente di creare la capacità aggiuntiva senza alcun ritardo.

* **Maggiore protezione**. Con un cloud privato di tre o più nodi, si ottengono la ridondanza automatica e la protezione della disponibilità elevata.

* **Esigenze dell'infrastruttura a lungo termine**. Se i Data Center sono alla capacità o si vuole ristrutturare per ridurre i costi, un cloud privato consente di ritirare i Data Center ed eseguire la migrazione a una soluzione basata sul cloud, pur rimanendo compatibile con le operazioni aziendali.

Quando si crea un cloud privato, si ottiene un singolo cluster vSphere e tutte le macchine virtuali di gestione create in tale cluster.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di poter creare il cloud privato, è necessario effettuare il provisioning dei nodi. Per altre informazioni sul provisioning dei nodi, vedere effettuare il provisioning [di nodi per la soluzione VMware di Azure con CloudSimple](create-nodes.md).

Allocare un intervallo CIDR per le subnet vSphere/rete VSAN per il cloud privato. Un cloud privato viene creato come un ambiente di stack VMware isolato (con host ESXi, vCenter, rete VSAN e NSX) gestito da un server vCenter. I componenti di gestione vengono distribuiti nella rete selezionata per la CIDR delle subnet vSphere/rete VSAN. L'intervallo CIDR di rete è diviso in subnet diverse durante la distribuzione. Lo spazio degli indirizzi della subnet vSphere/rete VSAN deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente CloudSimple. Le reti che comunicano con CloudSimple includono reti locali e reti virtuali di Azure. Per altre informazioni sulle subnet vSphere/rete VSAN, vedere Panoramica di VLAN e subnet.

* Prefisso intervallo CIDR per le subnet vSphere/rete VSAN minimo:/24
* Prefisso intervallo CIDR per le subnet vSphere/rete VSAN massimo:/21


## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

Accedere al [portale di CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Creazione di un nuovo cloud privato

1. Selezionare **Tutti i servizi**.
2. Cercare i **Servizi CloudSimple**.
3. Selezionare il servizio CloudSimple in cui si vuole creare il cloud privato.
4. Da **Panoramica**fare clic su **Crea cloud privato** per aprire una nuova scheda del browser per il portale di CloudSimple. Se richiesto, accedere con le credenziali di accesso di Azure.

    ![Creare un cloud privato da Azure](media/create-private-cloud-from-azure.png)

5. Nel portale di CloudSimple specificare un nome per il cloud privato.
6. Selezionare la **località** per il cloud privato.
7. Selezionare il **tipo di nodo**, coerente con quello di cui è stato effettuato il provisioning in Azure.
8. Specificare il **numero di nodi**.  Per creare un cloud privato sono necessari almeno tre nodi.

    ![Creare un cloud privato-informazioni di base](media/create-private-cloud-basic-info.png)

9. Fare clic su **Avanti: opzioni avanzate**.
10. Immettere l'intervallo CIDR per le subnet vSphere/rete VSAN. Verificare che l'intervallo CIDR non si sovrappongano con le subnet di Azure locali o altre (reti virtuali) o con la subnet del gateway.

    **Opzioni di intervallo CIDR:** /24,/23,/22 o/21. Un intervallo CIDR/24 supporta fino a nove nodi, un intervallo CIDR/23 supporta fino a 41 nodi e un intervallo CIDR/22 e/21 supporta fino a 64 nodi (il numero massimo di nodi in un cloud privato).

    > [!IMPORTANT]
    > Gli indirizzi IP nell'intervallo CIDR vSphere/rete VSAN sono riservati per l'uso da parte dell'infrastruttura di cloud privato.  Non usare l'indirizzo IP in questo intervallo in una macchina virtuale.

11. Fare clic su **Avanti: esaminare e creare**.
12. Esaminare le impostazioni. Se è necessario modificare le impostazioni, fare clic su **indietro**.
13. Fare clic su **Create**(Crea).

Viene avviato il processo di provisioning del cloud privato. Il provisioning del cloud privato può richiedere fino a due ore.

Per istruzioni sull'espansione di un cloud privato esistente, vedere [espandere un cloud privato](expand-private-cloud.md).
