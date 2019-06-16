---
title: Panoramica di IPv6 per la rete virtuale di Azure (anteprima)
titlesuffix: Azure Virtual Network
description: Descrizione di IPv6 dei percorsi di dati e gli endpoint IPv6 nella rete virtuale di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 0ec650880a45f6383b24b5ac810fc2ee745806b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130934"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Che cos'è IPv6 per la rete virtuale di Azure? (Anteprima)

IPv6 per Azure rete virtuale (VNET) consente di ospitare applicazioni in Azure con connettività IPv6 e IPv4 sia all'interno di una rete virtuale e da e verso Internet. A causa dell'esaurimento di indirizzi IPv4 pubblici, alle nuove reti per la mobilità e Internet delle cose (IoT) sono spesso basate su IPv6. Prolungata anche stabilita ISP e reti mobili sono in corso di trasformazione a IPv6. Solo IPv4 i servizi possono trovare autonomamente svantaggiate reali nei mercati emergenti ed esistenti. Dual-stack connettività IPv4 e IPv6 consente ai servizi ospitati in Azure attraversare questo gap di tecnologia con i servizi disponibili a livello globale, dual stack che si connettono facilmente con IPv4 esistenti e questi nuovi dispositivi di IPv6 e reti.

Connettività IPv6 originale di Azure rende più semplice fornire dual-stack connettività a Internet (IPv4 e IPv6) per le applicazioni ospitate in Azure. Consente di semplice distribuzione di macchine virtuali con connettività IPv6 con bilanciata del carico per le connessioni in entrata e in uscita avviate. Questa funzionalità è ancora disponibile e altre informazioni sono disponibili [qui](../load-balancer/load-balancer-ipv6-overview.md).
IPv6 per la rete virtuale di Azure è molto più complete in primo piano-abilitazione completi architetture delle soluzioni di IPv6 per la distribuzione in Azure.

> [!Important]
> IPv6 rete virtuale di Azure è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il diagramma seguente illustra una distribuzione semplice dual-stack (IPv4 e IPv6) in Azure:

![Diagramma di distribuzione di rete IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Vantaggi

Vantaggi di IPv6 di rete virtuale di Azure:

- Consente di espandere la copertura di applicazioni ospitate in Azure in di crescita per dispositivi mobili e Internet delle cose mercati.
- Due macchine virtuali di IPv4 e IPv6 in pila offrono flessibilità nella distribuzione massimi del servizio. Una singola istanza del servizio possa connettersi con i client di IPv4 e Internet in grado di supportare IPv6.
- Si basa su stabile, lunga Azure macchine Virtuali a Internet connettività IPv6.
- Protezione per impostazione predefinita perché la connettività IPv6 a Internet viene stabilita solo quando l'utente lo richiede in modo esplicito nella distribuzione.

## <a name="capabilities"></a>Capabilities

Il supporto IPv6 per le macchine virtuali include le funzionalità seguenti:

- I clienti di Azure è possono definire i propri spazio degli indirizzi di rete virtuale IPv6 per soddisfare le esigenze delle applicazioni, i clienti, o essere integrato nel proprio spazio IP in locale.
- Dual-stack (IPv4 e IPv6) le reti virtuali con subnet dual-stack consentono alle applicazioni di connettersi alle risorse IPv4 e IPv6 nella rete virtuale oppure - l'Internet.
- Proteggere le risorse con le regole di IPv6 per i gruppi di sicurezza di rete
- Personalizzare il routing del traffico IPv6 nella rete virtuale con le route definite dall'utente - soprattutto quando si usa Appliance virtuali di rete per aumentare l'applicazione.
- Supporto di bilanciamento del carico IPv6 per creare applicazioni scalabili, resilienti che include il supporto di DNS di Azure per i record AAAA per gli indirizzi IP pubblici IPv6.
- Aggiungere facilmente la connettività IPv6 per le distribuzioni esistenti solo IPv4 con aggiornamento in loco.
- Creare applicazioni dual-stack che si adattano automaticamente al carico con set di scalabilità di macchine virtuali.

## <a name="limitations"></a>Limitazioni
La versione di anteprima di IPv6 per la rete virtuale di Azure presenta le limitazioni seguenti:
- IPv6 per la rete virtuale di Azure (anteprima) è disponibile in tutte le aree globali di Azure, ma solo nella Global Azure - non i cloud per enti pubblici.   
- Supporto del portale per l'anteprima è limitato per visualizzare solo per la configurazione IPv6 molto, ma non tutti, ma IPv6 per la rete virtuale dispone di documentazione (con esempi) e il supporto completo per la distribuzione di IPv6 tramite Azure Powershell e interfaccia della riga di comando (CLI).
- Supporto di Network Watcher per l'anteprima è limitato a log di flusso e acquisizioni di pacchetti di rete.
- Supporto di bilanciamento del carico per l'anteprima sarà inizialmente limitato a Load Balancer Basic.
- Gli indirizzi IP pubblici a livello di istanza (indirizzi IP pubblici direttamente in una macchina virtuale) non è supportato in anteprima.  
- Peering reti virtuali (a livello di area o a livello globale) non è supportato in anteprima. 

## <a name="pricing"></a>Prezzi

Larghezza di banda e le risorse di Azure di IPv6 vengono addebitati alle stesse tariffe IPv4. Non sono previsti addebiti aggiuntivi o diversi per IPv6. È possibile trovare i dettagli sui prezzi relativi [gli indirizzi IP pubblici](https://azure.microsoft.com/pricing/details/ip-addresses/), [larghezza di banda di rete](https://azure.microsoft.com/pricing/details/bandwidth/), o [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire un'applicazione di dual-stack IPv6 usando Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Informazioni su come [distribuire un'applicazione di dual-stack IPv6 usando Azure CLI](virtual-network-ipv4-ipv6-dual-stack-cli.md).
