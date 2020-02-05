---
title: Azure VMware Solutions (AVS)-creare un cloud privato AVS
description: Viene descritto come creare un cloud privato AVS per estendere i carichi di lavoro VMware al cloud con flessibilità operativa e continuità
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 128a0a1eec03878d0deba93048c54324aab7d888
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024790"
---
# <a name="create-an-avs-private-cloud"></a>Creare un cloud privato AVS

Un cloud privato AVS è uno stack VMware isolato che supporta host ESXi, vCenter, rete VSAN e NSX. I cloud privati AVS vengono gestiti tramite il portale AVS. Hanno il proprio server vCenter nel proprio dominio di gestione. Lo stack viene eseguito su nodi dedicati e nodi hardware bare metal isolati.

La creazione di un cloud privato AVS ti aiuta a soddisfare diverse esigenze comuni per l'infrastruttura di rete:

* **Crescita**. Se è stato raggiunto un punto di aggiornamento hardware per l'infrastruttura esistente, un cloud privato AVS consente di espandersi senza nuovi investimenti hardware necessari.

* **Espansione rapida**. Se si verificano esigenze di capacità temporanee o non pianificate, un cloud privato AVS consente di creare la capacità aggiuntiva senza alcun ritardo.

* **Maggiore protezione**. Con un cloud privato AVS di tre o più nodi, si ottengono la ridondanza automatica e la protezione della disponibilità elevata.

* **Esigenze dell'infrastruttura a lungo termine**. Se i Data Center sono alla capacità o si vuole ristrutturare per ridurre i costi, un cloud privato AVS consente di ritirare i Data Center ed eseguire la migrazione a una soluzione basata sul cloud, pur rimanendo compatibile con le operazioni aziendali.

Quando si crea un cloud privato AVS, si ottiene un singolo cluster vSphere e tutte le macchine virtuali di gestione create in tale cluster.

## <a name="before-you-begin"></a>Prima di iniziare

Per poter creare il cloud privato AVS, è necessario effettuare il provisioning dei nodi. Per altre informazioni sul provisioning dei nodi, vedere Provisioning [di nodi per le soluzioni VMware di Azure (AVS)](create-nodes.md).

Allocare un intervallo CIDR per le subnet vSphere/rete VSAN per il cloud privato AVS. Un cloud privato AVS viene creato come ambiente di stack VMware isolato (con host ESXi, vCenter, rete VSAN e NSX) gestito da un server vCenter. I componenti di gestione vengono distribuiti nella rete selezionata per la CIDR delle subnet vSphere/rete VSAN. L'intervallo CIDR di rete è diviso in subnet diverse durante la distribuzione. Lo spazio degli indirizzi della subnet vSphere/rete VSAN deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente AVS. Le reti che comunicano con AVS includono le reti locali e le reti virtuali di Azure. Per altre informazioni sulle subnet vSphere/rete VSAN, vedere Panoramica di VLAN e subnet.

* Prefisso intervallo CIDR per le subnet vSphere/rete VSAN minimo:/24
* Prefisso intervallo CIDR per le subnet vSphere/rete VSAN massimo:/21


## <a name="access-the-avs-portal"></a>Accedere al portale AVS

Accedere al [portale AVS](access-cloudsimple-portal.md).

## <a name="create-a-new-avs-private-cloud"></a>Creare un nuovo cloud privato AVS

1. Selezionare **Tutti i servizi**.
2. Cercare **AVS Services**.
3. Selezionare il servizio AVS in cui si vuole creare il cloud privato AVS.
4. Da **Panoramica**fare clic su **Crea AVS private cloud** per aprire una nuova scheda del browser per AVS Portal. Se richiesto, accedere con le credenziali di accesso di Azure.

    ![Creare un cloud privato AVS da Azure](media/create-private-cloud-from-azure.png)

5. Nel portale AVS specificare un nome per il cloud privato AVS.
6. Selezionare la **località** per il cloud privato AVS.
7. Selezionare il **tipo di nodo**, coerente con quello acquistato in Azure.
8. Specificare il **numero di nodi**. Per creare un cloud privato AVS sono necessari almeno tre nodi.
5. Nel portale di CloudSimple specificare un nome per il cloud privato.
6. Selezionare la **località** per il cloud privato.
7. Selezionare il **tipo di nodo**, coerente con quello di cui è stato effettuato il provisioning in Azure.
8. Specificare il **numero di nodi**.  Per creare un cloud privato sono necessari almeno tre nodi.

    ![Creazione di un cloud privato AVS-informazioni di base](media/create-private-cloud-basic-info.png)

9. Fare clic su **Avanti: opzioni avanzate**.
10. Immettere l'intervallo CIDR per le subnet vSphere/rete VSAN. Verificare che l'intervallo CIDR non si sovrappongano con le subnet di Azure locali o altre (reti virtuali) o con la subnet del gateway.

    **Opzioni di intervallo CIDR:** /24,/23,/22 o/21. Un intervallo CIDR/24 supporta fino a nove nodi, un intervallo CIDR/23 supporta fino a 41 nodi e un intervallo CIDR/22 e/21 supporta fino a 64 nodi (il numero massimo di nodi in un cloud privato AVS).

    > [!IMPORTANT]
    > Gli indirizzi IP nell'intervallo CIDR vSphere/rete VSAN sono riservati per l'uso da parte dell'infrastruttura di cloud privato AVS. Non usare l'indirizzo IP in questo intervallo in una macchina virtuale.

11. Fare clic su **Avanti: esaminare e creare**.
12. Esaminare le impostazioni. Se è necessario modificare le impostazioni, fare clic su **indietro**.
13. Fare clic su **Crea**.

Viene avviato il processo di provisioning del cloud privato AVS. Il provisioning del cloud privato AVS può richiedere fino a due ore.

Per istruzioni sull'espansione di un cloud privato AVS esistente, vedere [espandere un cloud privato AVS](expand-private-cloud.md).
