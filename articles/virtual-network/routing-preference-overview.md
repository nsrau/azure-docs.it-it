---
title: Preferenza di routing in Azure
description: Informazioni su come scegliere il modo in cui viene instradato il traffico tra Azure e Internet con la preferenza di routing.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about routing choices for my internet egress traffic.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 3f5653e4c184e88bce86026681dde1aee9e14d49
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491418"
---
# <a name="what-is-routing-preference-preview"></a>Che cos'è la preferenza di routing (anteprima)?

La preferenza di routing di Azure consente di scegliere come instradare il traffico tra Azure e Internet. È possibile scegliere di instradare il traffico tramite la rete Microsoft o tramite la rete dell'ISP (Internet pubblico). Queste opzioni sono anche dette rispettivamente *routing cold potato* e *routing hot potato*. Il prezzo per il trasferimento dei dati in uscita varia in base alla selezione del routing. È possibile scegliere l'opzione di routing durante la creazione di un indirizzo IP pubblico. L'indirizzo IP pubblico può essere associato a risorse come macchine virtuali, set di scalabilità di macchine virtuali, servizio di bilanciamento del carico con connessione Internet e così via. È anche possibile impostare la preferenza di routing per le risorse di archiviazione di Azure, come BLOB, file, Web e Azure DataLake. Per impostazione predefinita, il traffico viene instradato tramite la rete globale Microsoft per tutti i servizi di Azure.

> [!IMPORTANT]
> La preferenza di routing è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="routing-via-microsoft-global-network"></a>Routing tramite la rete globale Microsoft

Se instradato tramite la *rete globale Microsoft*, il traffico viene distribuito su una delle reti più grandi del mondo, che si estende su più di 250.000 chilometri di fibra con oltre 165 POP (Point of Presence) perimetrali. La rete è dotata di più percorsi in fibra ridondanti per garantire livelli eccellenti di affidabilità e disponibilità. La progettazione del traffico viene gestita da un controller di rete WAN definito dal software che assicura la selezione del percorso a bassa latenza per il traffico e offre prestazioni di rete ottimali.

![Routing tramite la rete globale Microsoft](media/routing-preference-overview/route-via-microsoft-global-network.png)

**Traffico in ingresso:** l'annuncio di Anycast BGP globale garantisce che il traffico in ingresso entri nella rete Microsoft più vicina all'utente. Se ad esempio un utente di Singapore accede a risorse di Azure ospitate a Chicago, USA, il traffico entra nella rete globale Microsoft nel POP perimetrale di Singapore e viaggia sulla rete Microsoft fino al servizio ospitato a Chicago.

**Traffico in uscita:** il traffico in uscita segue lo stesso principio. Il traffico percorre la maggior parte del viaggio sulla rete globale Microsoft ed esce nel punto più vicino all'utente. Se ad esempio il traffico proveniente dal servizio di Azure a Chicago è destinato a un utente di Singapore, viaggia sulla rete Microsoft da Chicago a Singapore ed esce dalla rete Microsoft nel POP perimetrale di Singapore.

Il traffico in ingresso e in uscita rimane per la maggior parte del percorso sulla rete globale Microsoft. Questo scenario è anche noto come *routing cold potato*.


## <a name="routing-over-public-internet-isp-network"></a>Routing su Internet pubblico (rete dell'ISP)

La nuova scelta di routing, il *routing Internet*, riduce al minimo il percorso sulla rete globale Microsoft e usa la rete dell'ISP di transito per instradare il traffico. Questa opzione di routing ottimizzata per i costi offre prestazioni di rete paragonabili a quelle di altri provider di servizi cloud.

![Routing su Internet pubblico](media/routing-preference-overview/route-via-isp-network.png)

**Traffico in ingresso:** il percorso in ingresso usa il *routing hot potato*, il che significa che il traffico entra nella rete Microsoft più vicina all'area in cui è ospitato il servizio. Se ad esempio un utente di Singapore accede alle risorse di Azure ospitate a Chicago, il traffico viaggia su Internet pubblico ed entra nella rete globale Microsoft a Chicago.

**Traffico in uscita:** il traffico in uscita segue lo stesso principio. Il traffico esce dalla rete Microsoft nella stessa area in cui è ospitato il servizio. Se ad esempio il traffico proveniente dal servizio di Azure a Chicago è destinato a un utente di Singapore, esce dalla rete Microsoft a Chicago e viaggia su Internet pubblico fino all'utente di Singapore.

## <a name="supported-services"></a>Servizi supportati

L'indirizzo IP pubblico con la preferenza di routing impostata sulla rete globale Microsoft può essere associato a qualsiasi servizio di Azure. L'indirizzo IP pubblico con la preferenza di routing impostata su **Internet**, invece, può essere associato alle risorse di Azure seguenti:

* Macchina virtuale
* Set di scalabilità di macchine virtuali
* Servizio Azure Kubernetes
* Servizio di bilanciamento del carico Internet
* Gateway applicazione
* Firewall di Azure

Per l'archiviazione, gli endpoint primari usano sempre la **rete globale Microsoft**. È possibile abilitare endpoint secondari con **Internet** come scelta per il routing del traffico. I servizi di archiviazione supportati sono:

* BLOB
* File
* Web
* Azure DataLake

## <a name="pricing"></a>Prezzi
La differenza di prezzo tra le due opzioni si riflette nei prezzi del trasferimento dei dati in uscita su Internet. Il prezzo del trasferimento dei dati per il routing tramite **rete globale Microsoft** è uguale all'attuale prezzo in uscita su Internet. Per le informazioni più aggiornate sui prezzi, vedere la [pagina dei prezzi della larghezza di banda di Azure](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="availability"></a>Disponibilità

Il supporto per la preferenza di routing è disponibile nelle aree seguenti per servizi come macchine virtuali e servizio di bilanciamento del carico con connessione Internet che usano un indirizzo IP pubblico per l'uscita da Internet, ovvero Europa settentrionale, Europa occidentale, Francia meridionale, Regno Unito meridionale, Stati Uniti orientali, Sta, Stati Uniti centro-meridionali, Stati Uniti occidentali, Stati Uniti centro-occidentali, Asia sud-orientale, Germania centro-occidentale, Svizzera occidentale, Giappone orientale e Giappone occidentale.

Il supporto delle preferenze di routing per l'account di archiviazione è disponibile nelle aree di Azure seguenti: Stati Uniti centro-settentrionali, Stati Uniti centro-occidentali, Stati Uniti centro-meridionali, Stati Uniti orientali, Stati Uniti occidentali, Europa settentrionale, Francia meridionale, Germania centro-occidentale, Svizzera occidentale, Asia orientale sud, Giappone orientale e Giappone occidentale.
## <a name="limitations"></a>Limitazioni

* La preferenza di routing è compatibile solo con lo SKU Standard dell'indirizzo IP pubblico. Lo SKU Basic dell'indirizzo IP pubblico non è supportato.
* Attualmente, la preferenza di routing supporta solo indirizzi IP pubblici IPv4. Gli indirizzi IP pubblici IPv6 non sono supportati.
* Le macchine virtuali con più schede di interfaccia di rete possono avere un solo tipo di preferenza di routing.


## <a name="next-steps"></a>Passaggi successivi

* [Configurare la preferenza di routing per una macchina virtuale con Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Configurare la preferenza di routing per una macchina virtuale con l'interfaccia della riga di comando di Azure](configure-routing-preference-virtual-machine-cli.md)
