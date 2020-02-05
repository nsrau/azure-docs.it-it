---
title: Gestire il cloud privato di Azure VMware Solutions (AVS)
description: Descrive le funzionalità disponibili per gestire le risorse e le attività del cloud privato AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 47bf2251f71204b99245c1a9d55ef87157c41dd8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014828"
---
# <a name="manage-avs-private-cloud-resources-and-activities"></a>Gestire le risorse e le attività del cloud privato AVS

I cloud privati AVS sono gestiti dal portale di AVS. Verificare lo stato, le risorse disponibili, le attività nel cloud privato AVS e altre impostazioni dal portale AVS.

## <a name="sign-in-to-azure"></a>Accedere a Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Accedere al portale AVS

Accedere al [portale AVS](access-cloudsimple-portal.md).

## <a name="view-the-list-of-avs-private-clouds"></a>Visualizzare l'elenco dei cloud privati AVS

La scheda **AVS private cloud** nella pagina **risorse** elenca tutti i cloud privati AVS nella sottoscrizione. Le informazioni includono il nome, il numero di cluster vSphere, la posizione, lo stato corrente del cloud privato AVS e le informazioni sulle risorse.

![Pagina del cloud privato AVS](media/manage-private-cloud.png)

Selezionare un cloud privato AVS per ulteriori informazioni e azioni.

## <a name="avs-private-cloud-summary"></a>Riepilogo del cloud privato AVS

Visualizza un riepilogo completo del cloud privato AVS selezionato. La pagina di riepilogo include i server DNS distribuiti nel cloud privato AVS. È possibile configurare l'invio DNS da server DNS locali ai server DNS del cloud privato AVS. Per altre informazioni sull'invio DNS, vedere [configurare DNS per la risoluzione dei nomi per AVS private cloud vCenter da locale](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Riepilogo del cloud privato AVS](media/private-cloud-summary.png)

### <a name="available-actions"></a>Azioni disponibili

* [Avviare vSphere client](https://docs.azure.cloudsimple.com/vsphere-access/). Accedere a vCenter per questo cloud privato AVS.
* [Acquistare i nodi](create-nodes.md). Aggiungere nodi a questo cloud privato AVS.
* [Espandi](expand-private-cloud.md). Aggiungere nodi a questo cloud privato AVS.
* **Aggiornamento**. Aggiornare le informazioni in questa pagina.
* **Delete**. È possibile eliminare il cloud privato AVS in qualsiasi momento. **Prima di eliminare, verificare di aver eseguito il backup di tutti i sistemi e i dati.** L'eliminazione di un cloud privato AVS Elimina tutte le VM, la configurazione di vCenter e i dati. Fare clic su **Elimina** nella sezione Riepilogo per il cloud privato AVS selezionato. Dopo l'eliminazione, tutti i dati del cloud privato AVS vengono cancellati in un processo di cancellazione sicuro e altamente conforme.
* [Modificare i privilegi vSphere](escalate-private-cloud-privileges.md). Escalation dei privilegi in questo cloud privato AVS.

## <a name="avs-private-cloud-vlanssubnets"></a>VLAN/subnet del cloud privato AVS

Visualizzare l'elenco di VLAN/subnet definite per il cloud privato AVS selezionato. L'elenco include le VLAN di gestione/subnet create durante la creazione del cloud privato AVS.

![Cloud privato AVS-VLAN/subnet](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Azioni disponibili

* [Aggiungere VLAN/subnet](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Aggiungere una VLAN/subset al cloud privato AVS.

Selezionare una VLAN/subnet per le azioni seguenti
* [Connettersi alla tabella del firewall](https://docs.azure.cloudsimple.com/firewall/). Alleghi una tabella del firewall a questo cloud privato AVS.
* **Modifica**
* **Elimina** (solo VLAN/subnet definite dall'utente)

## <a name="avs-private-cloud-activity"></a>Attività del cloud privato AVS

Visualizzare le informazioni seguenti per il cloud privato AVS selezionato. Le informazioni sulle attività sono un elenco filtrato di tutte le attività per il cloud privato AVS selezionato. Questa pagina mostra fino a 25 attività recenti.

* Avvisi recenti
* Eventi recenti
* Attività recenti
* Controllo recente

![Cloud privato AVS-attività](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Rack cloud

I rack cloud sono i componenti costitutivi del cloud privato AVS. Ogni rack fornisce un'unità di capacità. AVS configura automaticamente i rack cloud in base alle selezioni effettuate durante la creazione o l'espansione di un cloud privato AVS. Consente di visualizzare l'elenco completo dei rack cloud, incluso il cloud privato AVS a cui è assegnato ogni.

![Cloud privato AVS-rack cloud](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Rete di gestione vSphere

Elenco delle risorse di gestione VMware e delle macchine virtuali attualmente configurate nel cloud privato AVS. Le informazioni includono la versione del software, il nome di dominio completo (FQDN) e l'indirizzo IP delle risorse.

![AVS private cloud-vSphere Management Network](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Altre informazioni sui [cloud privati AVS](cloudsimple-private-cloud.md)