---
title: Gestire la soluzione VMware di Azure con il cloud privato CloudSimple
description: Descrive le funzionalità disponibili per gestire le risorse e le attività del cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 620c0226d3aca907352658ebbe1b94c7673d91cd
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812276"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Gestione delle risorse e delle attività del cloud privato

I cloud privati vengono gestiti dal portale di CloudSimple.  Verificare lo stato, le risorse disponibili, l'attività nel cloud privato e altre impostazioni dal portale di CloudSimple.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

Accedere al [portale di CloudSimple](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Visualizzare l'elenco dei cloud privati

Nella scheda **cloud privati** della pagina **risorse** sono elencati tutti i cloud privati nella sottoscrizione. Le informazioni includono il nome, il numero di cluster vSphere, la posizione, lo stato corrente del cloud privato e le informazioni sulle risorse.

![Pagina del cloud privato](media/manage-private-cloud.png)

Selezionare un cloud privato per ulteriori informazioni e azioni.

## <a name="private-cloud-summary"></a>Riepilogo del cloud privato

Visualizza un riepilogo completo del cloud privato selezionato.  Nella pagina Riepilogo sono inclusi i server DNS distribuiti nel cloud privato.  È possibile configurare l'invio DNS da server DNS locali ai server DNS del cloud privato.  Per altre informazioni sull'invio DNS, vedere [configurare DNS per la risoluzione dei nomi per il cloud privato vCenter da locale](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Riepilogo del cloud privato](media/private-cloud-summary.png)

### <a name="available-actions"></a>Azioni disponibili

* [Avviare vSphere client](https://docs.azure.cloudsimple.com/vsphere-access/). Accedere a vCenter per questo cloud privato.
* [Acquistare i nodi](create-nodes.md). Aggiungere nodi a questo cloud privato.
* [Espandi](expand-private-cloud.md). Aggiungere nodi a questo cloud privato.
* **Aggiornamento**. Aggiornare le informazioni in questa pagina.
* **Delete**. È possibile eliminare il cloud privato in qualsiasi momento. **Prima di eliminare, verificare di aver eseguito il backup di tutti i sistemi e i dati.** L'eliminazione di un cloud privato comporta l'eliminazione di tutte le macchine virtuali, la configurazione di vCenter e i dati. Fare clic su **Elimina** nella sezione Riepilogo per il cloud privato selezionato. Dopo l'eliminazione, tutti i dati del cloud privato vengono cancellati in un processo di cancellazione sicuro e altamente conforme.
* [Modificare i privilegi vSphere](escalate-private-cloud-privileges.md).  Escalation dei privilegi in questo cloud privato.

## <a name="private-cloud-vlanssubnets"></a>VLAN/subnet del cloud privato

Visualizza l'elenco di VLAN/subnet definite per il cloud privato selezionato.  L'elenco include le VLAN di gestione/subnet create durante la creazione del cloud privato.

![Cloud privato-VLAN/subnet](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Azioni disponibili

* [Aggiungere VLAN/subnet](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Aggiungere una VLAN/subset al cloud privato.

Selezionare una VLAN/subnet per le azioni seguenti
* [Connettersi alla tabella del firewall](https://docs.azure.cloudsimple.com/firewall/). Consente di alleghi una tabella del firewall a questo cloud privato.
* **Modifica**
* **Elimina** (solo VLAN/subnet definite dall'utente)

## <a name="private-cloud-activity"></a>Attività del cloud privato

Per il cloud privato selezionato, visualizzare le informazioni seguenti.  Le informazioni sulle attività sono un elenco filtrato di tutte le attività per il cloud privato selezionato.  Questa pagina mostra fino a 25 attività recenti.

* Avvisi recenti
* Eventi recenti
* Attività recenti
* Controllo recente

![Cloud privato-attività](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Rack cloud

I rack cloud sono i componenti costitutivi del cloud privato. Ogni rack fornisce un'unità di capacità. CloudSimple configura automaticamente i rack cloud in base alle selezioni effettuate durante la creazione o l'espansione di un cloud privato.  Consente di visualizzare l'elenco completo dei rack cloud, incluso il cloud privato a cui sono assegnati.

![Cloud privato-rack cloud](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Rete di gestione vSphere

Elenco delle risorse di gestione VMware e delle macchine virtuali attualmente configurate nel cloud privato. Le informazioni includono la versione del software, il nome di dominio completo (FQDN) e l'indirizzo IP delle risorse.

![Cloud privato-rete di gestione vSphere](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Altre informazioni sui [cloud privati](cloudsimple-private-cloud.md)