---
title: Informazioni sulle zone di disponibilità di Azure | Microsoft Docs
description: Per creare applicazioni resilienti e a disponibilità elevata in Azure, le zone di disponibilità offrono posizioni fisicamente separate che è possibile usare per eseguire le risorse.
services: ''
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: cynthn
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: d6e53c055f3c15c585aeb806c0c243eabdc0f00d
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60000828"
---
# <a name="what-are-availability-zones-in-azure"></a>Informazioni sulle zone di disponibilità di Azure
Le zone di disponibilità offrono una soluzione a disponibilità elevata che consente di proteggere le applicazioni e i dati da eventuali guasti del data center. Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le aree abilitate. La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati da eventuali guasti del data center. I servizi con ridondanza della zona replicano le applicazioni e i dati tra aree di disponibilità per garantire la protezione da singoli punti di errore. Con le zone di disponibilità, Azure offre un contratto di servizio con tempo di attività delle VM del 99,99% tra i migliori del settore. La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) descrive la disponibilità garantita di Azure nel suo complesso.

Una zona di disponibilità in un'area di Azure è una combinazione di un dominio di errore e un dominio di aggiornamento. Ad esempio, se si creano tre o più macchine virtuali in tre aree in un'area di Azure, le macchine virtuali vengono distribuite in modo efficace in tre domini di errore e tre domini di aggiornamento. La piattaforma di Azure riconosce questa distribuzione in domini di aggiornamento per assicurarsi che le macchine virtuali in diverse aree non vengano aggiornate contemporaneamente.

È possibile configurare la disponibilità elevata nell'architettura delle applicazioni includendo le risorse di calcolo, archiviazione, rete e dati all'interno di una zona e replicandole in altre zone. I servizi di Azure che supportano le zone di disponibilità rientrano in due categorie:

- **Servizi di zona**: le risorse, ad esempio macchine virtuali, dischi gestiti o indirizzi IP, vengono associate a una zona specifica.
- **Servizi con ridondanza della zona**: la piattaforma replica automaticamente le applicazioni e i dati tra le zone, usando ad esempio l'archiviazione con ridondanza della zona o il database SQL.

Per garantire la continuità aziendale completa in Azure, creare l'architettura delle applicazioni combinando le zone di disponibilità con coppie di aree di Azure. È possibile replicare in modo sincrono le applicazioni e i dati usando le zone di disponibilità all'interno di un'area di Azure per la disponibilità elevata ed eseguire repliche asincrone tra le aree di Azure per la protezione con ripristino di emergenza.
 
![Visualizzazione concettuale di una zona che diventa indisponibile in un'area](./media/az-overview/az-graphic-two.png)

## <a name="services-support-by-region"></a>Supporto dei servizi per area

Le combinazioni di servizi di Azure e le aree che supportano le zone di disponibilità sono:


|                                 |Americhe |              |           |           | Europa |              |          |              | Asia/Pacifico |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |Stati Uniti centrali|Stati Uniti orientali|Stati Uniti orientali 2|Stati Uniti occidentali 2|Francia centrale|Europa settentrionale|Regno Unito meridionale|Europa occidentale|Giappone orientale|Asia sud-orientale|
| **Calcolo**                         |            |              |           |           |                |              |          |             |            |                |
| Macchine virtuali Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Macchine virtuali Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Set di scalabilità di macchine virtuali      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Archiviazione**   |            |              |           |           |                |              |          |             |            |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Archiviazione con ridondanza della zona          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Rete**                     |            |              |           |           |                |              |          |             |            |                |
| Indirizzo IP standard        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; &#42;| &#10003;    | &#10003;   | &#10003;       |
| Load Balancer Standard     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; &#42;| &#10003;    | &#10003;   | &#10003;       |
| Gateway VPN                     | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| ExpressRoute                    | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| Gateway applicazione (anteprima)   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| **Database**                     |            |              |           |           |                |              |          |             |            |                |
| Database SQL                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |            | &#10003;       |
| **Analisi**                       |            |              |           |           |                |              |          |             |            |                |
| Hub eventi                      | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| **Integrazione**                     |            |              |           |           |                |              |          |             |            |                |
| Bus di servizio (solo livello Premium) | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |


&#42;Per avere la ridondanza della zona verranno presto convertite le risorse create nel Regno Unito meridionale prima il 25 marzo 2019. Risorse create dopo il 25 marzo 2019 saranno ridondanza della zona immediatamente.

## <a name="services-resiliency"></a>Resilienza di servizi
Tutti i servizi di gestione di Azure sono progettati per essere resilienti dagli errori a livello di area. Nello spettro di errori, uno o più errori di zona di disponibilità all'interno di un'area presentano un raggio di errore più piccolo rispetto a un errore all'intera area. Azure è possibile ripristinare da un errore a livello di zona dei servizi di gestione all'interno dell'area o da un'altra area di Azure. Azure esegue una zona di manutenzione critiche in un momento all'interno di un'area per evitare eventuali errori delle conseguenze per le risorse dei clienti distribuite tra zone di disponibilità all'interno di un'area.

## <a name="pricing"></a>Prezzi
Per le macchine virtuali distribuite in una zona di disponibilità non sono previsti costi aggiuntivi. Il contratto di servizio con tempo di attività delle VM del 99,99% viene offerto quando due o più macchine virtuali vengono distribuite tra due o più zone di disponibilità all'interno di un'area di Azure. Vengono applicati addebiti per il trasferimento dei dati da VM a VM tra diverse zone di disponibilità. Per altre informazioni, vedere la pagina [Dettagli sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Introduzione alle zone di disponibilità
- [Creare una macchina virtuale](../virtual-machines/windows/create-portal-availability-zone.md)
- [Aggiungere un disco gestito usando PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Creare un set di scalabilità di macchine virtuali che usa le zone di disponibilità](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Bilanciare il carico delle macchine virtuali tra zone usando un servizio Load Balancer Standard con un front-end con ridondanza della zona](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Bilanciare il carico delle macchine virtuali all'interno di una zona usando un servizio Load Balancer Standard con un front-end di zona](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Archiviazione con ridondanza della zona](../storage/common/storage-redundancy-zrs.md)
- [Database SQL](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Ripristino di emergenza geografico di Hub eventi](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Ripristino di emergenza geografico del bus di servizio](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Creare un gateway di rete virtuale con ridondanza della zona](../vpn-gateway/create-zone-redundant-vnet-gateway.md)


## <a name="next-steps"></a>Passaggi successivi
- [Modelli di avvio rapido](https://aka.ms/azqs)
