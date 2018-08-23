---
title: Informazioni sulle zone di disponibilità di Azure | Microsoft Docs
description: Per creare applicazioni resilienti e a disponibilità elevata in Azure, le zone di disponibilità offrono posizioni fisicamente separate che è possibile usare per eseguire le risorse.
services: ''
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: iainfou
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 6a4dcc2cd3b196221b881783c79ddb0adaa6f38b
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2018
ms.locfileid: "42140722"
---
# <a name="what-are-availability-zones-in-azure"></a>Informazioni sulle zone di disponibilità di Azure
Le zone di disponibilità offrono una soluzione a disponibilità elevata che consente di proteggere le applicazioni e i dati da eventuali guasti del data center. Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le aree abilitate. La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati da eventuali guasti del data center. I servizi con ridondanza della zona replicano le applicazioni e i dati tra aree di disponibilità per garantire la protezione da singoli punti di errore. Con le zone di disponibilità, Azure offre un contratto di servizio con tempo di attività delle VM del 99,99% tra i migliori del settore. La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) descrive la disponibilità garantita di Azure nel suo complesso.

Una zona di disponibilità in un'area di Azure è una combinazione di un dominio di errore e un dominio di aggiornamento. Ad esempio, se si creano tre o più macchine virtuali in tre aree in un'area di Azure, le macchine virtuali vengono distribuite in modo efficace in tre domini di errore e tre domini di aggiornamento. La piattaforma di Azure riconosce questa distribuzione in domini di aggiornamento per assicurarsi che le macchine virtuali in diverse aree non vengano aggiornate contemporaneamente.

È possibile configurare la disponibilità elevata nell'architettura delle applicazioni includendo le risorse di calcolo, archiviazione, rete e dati all'interno di una zona e replicandole in altre zone. I servizi di Azure che supportano le zone di disponibilità rientrano in due categorie:

- **Servizi di zona**: le risorse, ad esempio macchine virtuali, dischi gestiti o indirizzi IP, vengono associate a una zona specifica.
- **Servizi con ridondanza della zona**: la piattaforma replica automaticamente le applicazioni e i dati tra le zone, usando ad esempio l'archiviazione con ridondanza della zona o il database SQL.

Per garantire la continuità aziendale completa in Azure, creare l'architettura delle applicazioni combinando le zone di disponibilità con coppie di aree di Azure. È possibile replicare in modo sincrono le applicazioni e i dati usando le zone di disponibilità all'interno di un'area di Azure per la disponibilità elevata ed eseguire repliche asincrone tra le aree di Azure per la protezione con ripristino di emergenza.
 
![Visualizzazione concettuale di una zona che diventa indisponibile in un'area](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Aree che supportano la funzionalità Zone di disponibilità

- Stati Uniti centrali
- Francia centrale
- Stati Uniti orientali 2 (anteprima)
- Europa occidentale
- Asia sud-orientale (anteprima)


## <a name="services-that-support-availability-zones"></a>Servizi che supportano la funzionalità Zone di disponibilità
I servizi che supportano la funzionalità Zone di disponibilità sono i seguenti:

- Macchine virtuali Linux
- Macchine virtuali Windows
- Set di scalabilità di macchine virtuali
- Managed Disks
- Load Balancer
- Indirizzo IP pubblico
- Archiviazione con ridondanza della zona
- Database SQL
- Hub eventi
- Bus di servizio
- Gateway VPN
- ExpressRoute


## <a name="pricing"></a>Prezzi
Per le macchine virtuali distribuite in una zona di disponibilità non sono previsti costi aggiuntivi. Il contratto di servizio con tempo di attività delle VM del 99,99% viene offerto quando due o più macchine virtuali vengono distribuite tra due o più zone di disponibilità all'interno di un'area di Azure. Vengono applicati addebiti per il trasferimento dei dati da VM a VM tra diverse zone di disponibilità. Per altre informazioni, vedere la pagina [Dettagli sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Introduzione alle zone di disponibilità
- [Creare una macchina virtuale](../virtual-machines/windows/create-portal-availability-zone.md)
- [Aggiungere un disco gestito usando PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Creare un set di scalabilità di macchine virtuali che usa le zone di disponibilità](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Bilanciare il carico delle macchine virtuali tra zone usando un servizio Load Balancer Standard con un front-end con ridondanza della zona](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Bilanciare il carico delle macchine virtuali all'interno di una zona usando un servizio Load Balancer Standard con un front-end di zona](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Archiviazione con ridondanza della zona](../storage/common/storage-redundancy-zrs.md)
- [Database SQL](../sql-database/sql-database-high-availability.md#zone-redundant-configuration-preview)
- [Ripristino di emergenza geografico di Hub eventi](../event-hubs/event-hubs-geo-dr.md#availability-zones-preview)
- [Ripristino di emergenza geografico del bus di servizio](../service-bus-messaging/service-bus-geo-dr.md#availability-zones-preview)
- [Creare un gateway di rete virtuale con ridondanza della zona](../vpn-gateway/create-zone-redundant-vnet-gateway.md)


## <a name="next-steps"></a>Passaggi successivi
- [Modelli di avvio rapido](http://aka.ms/azqs)
