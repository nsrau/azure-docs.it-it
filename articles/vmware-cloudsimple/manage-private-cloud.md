---
title: Gestire la soluzione Azure VMware dal cloudSimple Private Cloud
description: Descrive le funzionalità disponibili per gestire le risorse e l'attività del cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 13496a18f4c99b69a5b8095caf5b74a04d1bba88
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869293"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Gestire le risorse e le attività del cloud privato

I cloud privati sono gestiti dal portale CloudSimple.Private clouds are managed from CloudSimple portal.  Controllare lo stato, le risorse disponibili, l'attività nel cloud privato e altre impostazioni dal portale CloudSimple.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

Accedere al [portale CloudSimple](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Visualizza l'elenco dei cloud privati

La scheda **Cloud privati** nella pagina **Risorse** elenca tutti i cloud privati nell'abbonamento. Le informazioni includono il nome, il numero di cluster vSphere, la posizione, lo stato corrente del cloud privato e le informazioni sulle risorse.

![Pagina Cloud privato](media/manage-private-cloud.png)

Selezionare un cloud privato per ulteriori informazioni e azioni.

## <a name="private-cloud-summary"></a>Riepilogo del cloud privato

Visualizza un riepilogo completo del cloud privato selezionato.  La pagina di riepilogo include i server DNS distribuiti nel cloud privato.  È possibile configurare l'inoltro DNS dai server DNS locali ai server DNS private cloud.  Per ulteriori informazioni sull'inoltro DNS, vedere Configurare DNS per la [risoluzione dei nomi per Private Cloud vCenter da locale.](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-dns-setup/)

![Riepilogo del cloud privato](media/private-cloud-summary.png)

### <a name="available-actions"></a>Azioni disponibili

* [Avviare il client vSphere](https://docs.microsoft.com/azure/vmware-cloudsimple/vcenter-access). Accedi al vCenter per questo cloud privato.
* [Nodi di acquisto](create-nodes.md). Aggiungere nodi a questo cloud privato.
* [Espandere](expand-private-cloud.md). Aggiungere nodi a questo cloud privato.
* **Aggiorna**. Aggiornare le informazioni in questa pagina.
* **Elimina**. È possibile eliminare il cloud privato in qualsiasi momento. **Prima di eliminarli, assicurarsi di aver eseguito il backup di tutti i sistemi e i dati.** L'eliminazione di un cloud privato comporta l'eliminazione di tutte le macchine virtuali, la configurazione di vCenter e i dati. Fare clic su **Elimina** nella sezione di riepilogo per il cloud privato selezionato. Dopo l'eliminazione, tutti i dati del cloud privato vengono cancellati in un processo di cancellazione sicuro e altamente conforme.
* [Modificare i privilegi di vSphere](escalate-private-cloud-privileges.md).  Aumenta i tuoi privilegi su questo cloud privato.

## <a name="private-cloud-vlanssubnets"></a>VLANS/subnet del cloud privato

Visualizzare l'elenco delle VLAN/subnet definite per il cloud privato selezionato.  L'elenco include le VLAN/subnet di gestione create al momento della creazione del cloud privato.

![Cloud privato - VLAN/subnet](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Azioni disponibili

* [Aggiungere VLANS/Subnets](https://docs.microsoft.com/azure/vmware-cloudsimple/create-vlan-subnet/). Aggiungere una VLAN/subset a questo cloud privato.

Selezionare una VLAN/Subnet per le seguenti azioni
* [Collegare la tabella del firewall](https://docs.microsoft.com/azure/vmware-cloudsimple/firewall/). Collegare una tabella firewall a questo cloud privato.
* **Modifica**
* **Elimina** (solo VLAN/subnet definite dall'utente)

## <a name="private-cloud-activity"></a>Attività del cloud privato

Visualizzare le seguenti informazioni per il cloud privato selezionato.  Le informazioni sull'attività sono un elenco filtrato di tutte le attività per il cloud privato selezionato.  Questa pagina mostra fino a 25 attività recenti.

* Avvisi recenti
* Eventi recenti
* Attività recenti
* Audit recente

![Private Cloud - Attività](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Rack cloud

I cloud rack sono gli elementi costitutivi del cloud privato. Ogni rack fornisce un'unità di capacità. CloudSimple configura automaticamente i cloud rack in base alle selezioni effettuate durante la creazione o l'espansione di un cloud privato.  Visualizzare l'elenco completo dei rack cloud, incluso il cloud privato a cui ognuno è assegnato.

![Cloud privato - Rack cloud](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Rete di gestione vSphere

Elenco delle risorse di gestione di VMware e delle macchine virtuali attualmente configurate nel cloud privato. Le informazioni includono la versione del software, il nome di dominio completo (FQDN) e l'indirizzo IP delle risorse.

![Cloud privato - vSphere Management Network](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare VM di VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Ulteriori informazioni sui [cloud privati](cloudsimple-private-cloud.md)