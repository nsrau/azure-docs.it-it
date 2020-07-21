---
title: Gestire il cloud privato della soluzione Azure VMware di CloudSimple
description: Descrive le funzionalità disponibili per gestire risorse e attività di un cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b0aff8298ac2335e5216252eb2cbd5a393cc37d5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524997"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Gestire risorse e attività del cloud privato

I cloud privati vengono gestiti dal portale di CloudSimple.  Verificare lo stato, le risorse disponibili, l'attività nel cloud privato e altre impostazioni dal portale di CloudSimple.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

Accedere al [portale di CloudSimple](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Visualizzare l'elenco di cloud privati

La scheda **Cloud privati** nella pagina **Risorse** elenca tutti i cloud privati della sottoscrizione. Le informazioni includono il nome, il numero di cluster vSphere, la posizione, lo stato corrente del cloud privato e le informazioni sulle risorse.

![Pagina del cloud privato](media/manage-private-cloud.png)

Selezionare un cloud privato per altre informazioni e azioni.

## <a name="private-cloud-summary"></a>Riepilogo del cloud privato

Visualizzare un riepilogo completo del cloud privato selezionato.  La pagina Riepilogo include i server DNS distribuiti nel cloud privato.  È possibile configurare l'inoltro DNS da server DNS locali ai server DNS del cloud privato.  Per altre informazioni sull'inoltro DNS, vedere [Configurare il DNS per la risoluzione dei nomi per vCenter del cloud privato da workstation locali](./on-premises-dns-setup.md).

![Riepilogo del cloud privato](media/private-cloud-summary.png)

### <a name="available-actions"></a>Azioni disponibili

* [Avvio del client vSphere](./vcenter-access.md). Accedere a vCenter per il cloud privato.
* [Acquisto di nodi](create-nodes.md). Aggiungere nodi al cloud privato.
* [Espansione](expand-private-cloud.md). Aggiungere nodi al cloud privato.
* **Aggiornamento**. Aggiornare le informazioni contenute in questa pagina.
* **Delete**. È possibile eliminare il cloud privato in qualsiasi momento. **Prima dell'eliminazione, verificare di aver eseguito il backup di tutti i sistemi e i dati.** L'eliminazione di un cloud privato comporta l'eliminazione di tutte le macchine virtuali, della configurazione di vCenter e dei dati. Fare clic su **Elimina** nella sezione relativa al riepilogo per il cloud privato selezionato. Dopo l'eliminazione, tutti i dati del cloud privato vengono cancellati in un processo di cancellazione sicuro e perfettamente conforme.
* [Modifica dei privilegi vSphere](escalate-private-cloud-privileges.md).  Eseguire l'escalation dei privilegi nel cloud privato.

## <a name="private-cloud-vlanssubnets"></a>VLAN/subnet del cloud privato

Visualizzare l'elenco di VLAN/subnet definite per il cloud privato selezionato.  L'elenco include le VLAN/subnet di gestione create durante la creazione del cloud privato.

![Cloud privato - VLAN/subnet](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Azioni disponibili

* [Aggiunta di VLAN/subnet](./create-vlan-subnet.md). Aggiungere una VLAN/subset al cloud privato.

Selezionare una VLAN/subnet per le azioni seguenti
* [Collegamento di una tabella firewall](./firewall.md). Collegare una tabella firewall al cloud privato.
* **Modifica**
* **Aggiunta** (solo VLAN/subnet definite dall'utente)

## <a name="private-cloud-activity"></a>Attività del cloud privato

Visualizzare le informazioni seguenti per il cloud privato selezionato.  Le informazioni relative alle attività sono costituite da un elenco filtrato di tutte le attività per il cloud privato selezionato.  Questa pagina mostra fino a 25 attività recenti.

* Avvisi recenti
* Eventi recenti
* Attività recenti
* Controlli recenti

![Cloud privato - Attività](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Rack cloud

I rack cloud sono gli elementi costitutivi del cloud privato. Ogni rack fornisce un'unità di capacità. CloudSimple configura automaticamente i rack cloud in base alle selezioni effettuate durante la creazione o l'espansione di un cloud privato.  Visualizzare l'elenco completo dei rack cloud, incluso il cloud privato a cui è assegnato ciascuno di essi.

![Rack cloud - Cloud privato](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Rete di gestione vSphere

Elenco delle macchine virtuali e delle risorse di gestione VMware attualmente configurate nel cloud privato. Le informazioni includono la versione del software, il nome di dominio completo (FQDN) e l'indirizzo IP delle risorse.

![Cloud privato - Rete di gestione vSphere](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Passaggi successivi

* [Usare VM di VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Vedere le informazioni sui [cloud privati](cloudsimple-private-cloud.md)
