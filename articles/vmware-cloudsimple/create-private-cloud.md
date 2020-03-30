---
title: Soluzione Azure VMware di CloudSimple - Creare cloudSimple Private Cloud
description: Descrive come creare un cloud privato CloudSimple per estendere i carichi di lavoro VMware al cloud con flessibilità e continuità operative
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024790"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Creare un cloud privato semplice

Un cloud privato è uno stack VMware isolato che supporta host ESXi, vCenter, vSAN e NSX. I cloud privati vengono gestiti tramite il portale CloudSimple. Hanno il proprio server vCenter nel proprio dominio di gestione. Lo stack viene eseguito su nodi dedicati e nodi hardware bare metal isolati.

La creazione di un cloud privato consente di soddisfare una serie di esigenze comuni per l'infrastruttura di rete:Creating a Private Cloud helps you address a variety of common needs for network infrastructure:

* **Crescita**. Se hai raggiunto un punto di aggiornamento hardware per l'infrastruttura esistente, un cloud privato consente di espanderti senza richiedere alcun nuovo investimento hardware.

* **Espansione rapida**. Se si verificano esigenze di capacità temporanea o non pianificata, un cloud privato consente di creare la capacità aggiuntiva senza ritardi.

* **Maggiore protezione**. Con un cloud privato di tre o più nodi, si ottiene la ridondanza automatica e la protezione della disponibilità elevata.

* **Esigenze infrastrutturali a lungo termine**. Se i data center sono in grado o si desidera ristrutturare per ridurre i costi, un cloud privato consente di ritirare i data center ed eseguire la migrazione a una soluzione basata su cloud, pur rimanendo compatibile con le operazioni aziendali.

Quando si crea un cloud privato, si ottiene un singolo cluster vSphere e tutte le macchine virtuali di gestione create in tale cluster.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario eseguire il provisioning dei nodi prima di poter creare il cloud privato. Per altre informazioni sul provisioning dei nodi, vedere [Provisioning dei nodi per la soluzione VMware di Azure da CloudSimple.](create-nodes.md)

Allocare un intervallo CIDR per le subnet vSphere/vSAN per il cloud privato. Un cloud privato viene creato come ambiente stack VMware isolato (con host ESXi, vCenter, vSAN e NSX) gestito da un server vCenter. I componenti di gestione vengono distribuiti nella rete selezionata per le subnet CIDR vSphere/vSAN. L'intervallo CIDR di rete è suddiviso in diverse subnet durante la distribuzione. Lo spazio di indirizzi della subnet vSphere/vSAN deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente CloudSimple. Le reti che comunicano con CloudSimple includono reti locali e reti virtuali di Azure.The networks that communicate with CloudSimple include on-premises networks and Azure virtual networks. Per altre informazioni sulle subnet vSphere/vSAN, vedere Cenni preliminari sulle VLAN e sulle subnet.

* Prefisso intervallo CIDR delle subnet minime vSphere/vSAN: /24
* Prefisso intervallo CIDR delle subnet massime vSphere/vSAN: /21Maximum vSphere/vSAN subnets CIDR range prefix: /21


## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

Accedere al [portale CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Creare un nuovo cloud privatoCreate a New Private Cloud

1. Selezionare **Tutti i servizi**.
2. Cercare **CloudSimple Services**.
3. Selezionare il servizio CloudSimple in cui si desidera creare il cloud privato.
4. In **Panoramica**fare clic su **Crea cloud privato** per aprire una nuova scheda del browser per il portale CloudSimple. Se richiesto, accedere con le credenziali di accesso di Azure.If prompted, sign in with your Azure sign in credentials.

    ![Creare un cloud privato da AzureCreate Private Cloud from Azure](media/create-private-cloud-from-azure.png)

5. Nel portale CloudSimple specificare un nome per il cloud privato.
6. Selezionare **Posizione** per il cloud privato.
7. Selezionare **Tipo di nodo**, coerente con il provisioning in Azure.Select Node type , consistent with what you provisioned on Azure.
8. Specificare **il numero di nodi**.  Per creare un cloud privato sono necessari almeno tre nodi.

    ![Creare un cloud privato - Informazioni di base](media/create-private-cloud-basic-info.png)

9. Fare clic su **Successivo: Opzioni avanzate**.
10. Immettere l'intervallo CIDR per le subnet vSphere/vSAN. Assicurarsi che l'intervallo CIDR non si sovrapponga a nessuna delle subnet di Azure (reti virtuali) locali o di altre subnet di Azure o con la subnet del gateway.

    **Opzioni dell'intervallo CIDR:** /24, /23, /22 o /21. Un intervallo CIDR /24 supporta fino a nove nodi, un intervallo /23 CIDR supporta fino a 41 nodi e un intervallo CIDR /22 e /21 supporta fino a 64 nodi (il numero massimo di nodi in un cloud privato).

    > [!IMPORTANT]
    > Gli indirizzi IP nell'intervallo CIDR vSphere/vSAN sono riservati per l'utilizzo da parte dell'infrastruttura Private Cloud.  Non usare l'indirizzo IP in questo intervallo in qualsiasi macchina virtuale.

11. Fare clic su **Avanti: Revisione e creazione**.
12. Rivedere le impostazioni. Se è necessario modificare le impostazioni, fare clic su **Indietro**.
13. Fare clic su **Crea**.

Viene avviato il processo di provisioning del cloud privato. Il provisioning del cloud privato può richiedere fino a due ore.

Per istruzioni sull'espansione di un cloud privato esistente, consultate [Espandere un cloud privato.](expand-private-cloud.md)
