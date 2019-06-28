---
title: Gestire Azure soluzione VMware da CloudSimple Private Cloud
description: Descrive le funzionalità disponibili per la gestione di risorse del Cloud privato CloudSimple e dell'attività
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 05a2fb451b3acce1011c1d5f4cf17f0a865d57d0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333115"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Gestisce le attività e le risorse del Cloud privato Microsoft

I cloud privati sono gestiti dal portale CloudSimple.  Controllare lo stato, le risorse disponibili, l'attività sul cloud privato e altre impostazioni dal portale CloudSimple.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

Accesso di [CloudSimple portale](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Visualizzare l'elenco dei cloud privati

Il **cloud privati** scheda le **risorse** pagina vengono elencati tutti i cloud privati nella sottoscrizione. Le informazioni includono il nome, numero di vSphere di cluster, località, stato corrente della finestra di private cloud, le informazioni e risorse.

![Pagina del Cloud privato Microsoft](media/manage-private-cloud.png)

Selezionare un Cloud privato per le azioni e informazioni aggiuntive.

## <a name="private-cloud-summary"></a>Riepilogo del Cloud privato Microsoft

Visualizzare un riepilogo completo del Cloud privato selezionato.  Pagina di riepilogo include i server DNS distribuiti sul Cloud privato.  È possibile impostare il servizio DNS di inoltro dal server DNS locali per i server DNS di Cloud privato.  Per ulteriori informazioni sull'inoltro di DNS, vedere [configurare DNS per la risoluzione dei nomi per un Cloud privato vCenter locale](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Riepilogo di Cloud privato](media/private-cloud-summary.png)

### <a name="available-actions"></a>Azioni disponibili

* [Avviare il client di vSphere](https://docs.azure.cloudsimple.com/vsphere-access/). Accesso di vCenter per questo Cloud privato.
* [Acquisto nodi](create-nodes.md). Aggiungere nodi a questo Cloud privato.
* [Espandere](expand-private-cloud.md). Aggiungere nodi a questo Cloud privato.
* **Aggiorna**. Aggiornare le informazioni in questa pagina.
* **Delete**. È possibile eliminare il Cloud privato in qualsiasi momento. **Prima dell'eliminazione, assicurarsi che è stato eseguito il backup di tutti i sistemi e dati.** L'eliminazione di un Cloud privato consente di eliminare tutte le macchine virtuali, configurazione di vCenter e dati. Fare clic su **eliminare** nella sezione di riepilogo per il Cloud privato selezionato. Dopo l'eliminazione, vengono cancellati tutti i dati di Cloud privato in un processo di cancellazione sicura, altamente compatibile.
* [Modificare i diritti di vSphere](escalate-private-cloud-privileges.md).  Eseguire l'escalation dei privilegi in questo Cloud privato.

## <a name="private-cloud-vlanssubnets"></a>Subnet/VLAN Private Cloud

Visualizzare l'elenco di VLAN/subnet definita per il Cloud privato selezionato.  L'elenco include la gestione VLAN/subnet creata quando è stato creato il cloud privato.

![Cloud privato - VLAN/subnet](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Azioni disponibili

* [Aggiungere VLAN/subnet](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Aggiungere un VLAN/subset in questo Cloud privato.

Selezionare una Subnet/VLAN per le azioni seguenti
* [Collegare tabella firewall](https://docs.azure.cloudsimple.com/firewall/). Collegare una tabella di firewall per questo Cloud privato.
* **Modifica**
* **Eliminare** (solo dall'utente VLAN/subnet)

## <a name="private-cloud-activity"></a>Attività di Cloud privato

Visualizzare le informazioni seguenti per il Cloud privato selezionato.  Le informazioni sull'attività è un elenco filtrato di tutte le attività per il Cloud privato selezionato.  Questa pagina illustra le attività recenti fino a 25.

* Avvisi recenti
* Eventi recenti
* Attività recente
* Controllo recenti

![Cloud privato - attività](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Rack di cloud

Cloud rack sono i blocchi predefiniti di Cloud privato. Ogni rack fornisce un'unità di capacità. CloudSimple configura automaticamente rack cloud sulla base delle selezioni quando si crea o espansione di un Cloud privato.  Visualizzare l'elenco completo dei rack cloud, tra cui il Cloud privato da ciascuna è assegnato a.

![Cloud privato - Cloud rack](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere rete di gestione

Elenco di macchine virtuali attualmente configurate sul Cloud privato e le risorse di gestione di VMware. Le informazioni includono la versione del software, un nome di dominio completo (FQDN) e un indirizzo IP delle risorse.

![Cloud privato - vSphere rete di gestione](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare le macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Altre informazioni su [cloud privati](cloudsimple-private-cloud.md)