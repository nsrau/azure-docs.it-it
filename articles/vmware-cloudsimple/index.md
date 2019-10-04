---
title: Soluzione Azure VMware di CloudSimple
description: Portale della documentazione per la soluzione Azure VMware di CloudSimple.
author: sharaths-cs
ms.author: b-mashar
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 936a9eb4d9b33b45800fab213fc72a306a04146e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972748"
---
# <a name="azure-vmware-solution-by-cloudsimple"></a>Soluzione Azure VMware di CloudSimple

Benvenuti nel portale unico che contiene informazioni sulla soluzione Azure VMware di CloudSimple.
Nel sito della documentazione è possibile ottenere informazioni sugli argomenti seguenti:

## <a name="overview"></a>Panoramica

Altre informazioni sulla soluzione Azure VMware di CloudSimple

* Per informazioni su funzionalità, vantaggi e scenari di utilizzo, vedere [Informazioni sulla soluzione Azure VMware di CloudSimple](cloudsimple-vmware-solutions-overview.md)
* Esaminare i [concetti chiave per l'amministrazione](key-concepts.md)

## <a name="quickstart"></a>Guida introduttiva

Informazioni su come iniziare a usare la soluzione

* Informazioni su come [inizializzare il servizio e acquistare capacità](quickstart-create-cloudsimple-service.md)
* Per informazioni su come creare un nuovo ambiente VMware, vedere [Configurare un ambiente cloud privato](quickstart-create-private-cloud.md)
* Per informazioni su come unificare la gestione in VMware e Azure, vedere l'articolo [Utilizzare VM di VMware in Azure](quickstart-create-vmware-virtual-machine.md)

## <a name="concepts"></a>Concetti

Informazioni sui concetti seguenti

* Un [servizio CloudSimple](cloudsimple-service.md) (noto anche come "Soluzione Azure VMware di CloudSimple - Servizio"). Questa risorsa deve essere creata una sola volta per ogni area.
* Acquistare capacità per l'ambiente creando una o più risorse di tipo [Nodo CloudSimple](cloudsimple-node.md). Queste risorse vengono definite anche come "Soluzione Azure VMware Solution di CloudSimple - Nodo".
* Inizializzare e configurare l'ambiente VMware usando i [cloud privati](cloudsimple-private-cloud.md).
* Unificare la gestione usando [macchine virtuali CloudSimple](cloudsimple-virtual-machines.md) (note anche come "Soluzione Azure VMware di CloudSimple - Macchina virtuale").
* Progettare la rete sottostante usando [VLAN/subnet](cloudsimple-vlans-subnets.md).
* Segmentare e proteggere la rete sottostante usando la risorsa [Tabella firewall](cloudsimple-firewall-tables.md).
* Ottenere accesso sicuro agli ambienti VMware tramite la rete WAN usando i [gateway VPN](cloudsimple-vpn-gateways.md).
* Abilitare l'accesso pubblico per i carichi di lavoro usando [IP pubblico](cloudsimple-public-ip-address.md).
* Stabilire la connettività a reti virtuali di Azure e a reti locali usando la [connessione di rete di Azure](cloudsimple-azure-network-connection.md).
* Configurare le destinazioni di posta elettronica degli avvisi con [Gestione account](cloudsimple-account.md).
* Visualizzare i log dell'attività utente e del sistema con le schermate di [gestione delle attività](cloudsimple-activity.md).
* Riconoscere i diversi [componenti di VMware](vmware-components.md).

## <a name="tutorials"></a>Esercitazioni

Informazioni su come eseguire attività comuni, ad esempio:

* [Creare un servizio CloudSimple](create-cloudsimple-service.md), una sola volta per ogni area in cui si intende distribuire ambienti VMware.
* Gestire la funzionalità di base del servizio nel [portale di CloudSimple](access-cloudsimple-portal.md).
* Abilitare la capacità e ottimizzare la fatturazione per l'infrastruttura [acquistando nodi CloudSimple](create-nodes.md).
* Gestire le configurazioni dell'ambiente VMware con cloud privati. È possibile [creare](create-private-cloud.md), [gestire](manage-private-cloud.md), [espandere](expand-private-cloud.md) o [ridurre](shrink-private-cloud.md) i cloud privati.
* Abilitare la gestione unificata eseguendo il [mapping delle sottoscrizioni di Azure](azure-subscription-mapping.md).
* Monitorare attività utente e del sistema usando le [pagine attività](monitor-activity.md).
* Configurare la rete per gli ambienti [creando e gestendo le subnet](create-vlan-subnet.md).
* Segmentare e proteggere l'ambiente usando [tabelle e regole del firewall](firewall.md).
* Abilitare l'accesso a Internet in ingresso per i carichi di lavoro [allocando IP pubblici](public-ips.md).
* Abilitare la connettività da reti interne o workstation client [configurando la VPN](vpn-gateway.md).
* Abilitare le comunicazioni da [ambienti locali](on-premises-connection.md) anche a [reti virtuali di Azure](virtual-network-connection.md).
* Configurare le destinazioni degli avvisi e visualizzare la capacità totale acquistata nel [riepilogo dell'account](account.md)
* Visualizzare gli [utenti](users.md) che hanno eseguito l'accesso al portale di CloudSimple.
* Gestire le macchine virtuali VMware dal portale di Azure:
    * [Creare macchine virtuali](azure-create-vm.md) nel portale di Azure.
    * [Gestire le macchine virtuali](azure-manage-vm.md) create.

## <a name="how-to-guides"></a>Guide pratiche

Queste guide descrivono le soluzioni a obiettivi, ad esempio:

* [Protezione dell'ambiente](private-cloud-secure.md)
* Installare strumenti di terze parti, abilitare utenti aggiuntivi e l'origine di autenticazione esterna in vSphere usando l'[escalation dei privilegi](escalate-privileges.md).
* Configurare l'accesso a vari servizi VMware [configurando il DNS locale](on-premises-dns-setup.md).
* Abilitare l'allocazione di nomi e indirizzi per i carichi di lavoro [configurando DNS e DHCP dei carichi di lavoro](dns-dhcp-setup.md).
* Comprendere in che modo il servizio garantisce sicurezza e funzionalità nella piattaforma tramite gli [aggiornamenti](vmware-components.md#updates-and-upgrades) dei servizi.
* Ridurre il costo totale di proprietà relativo al backup creando un'architettura di backup di esempio con un [software di backup di terze parti come Veeam](backup-workloads-veeam.md).
* Creare un ambiente sicuro abilitando la crittografia dei servizi inattivi con un [software di crittografia del server di gestione delle chiavi di terze parti](vsan-encryption.md).
* Estendere la gestione di Azure Active Directory (Azure AD) in VMware configurando l'[origine identità Azure AD](azure-ad.md).
