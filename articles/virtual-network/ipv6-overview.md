---
title: Panoramica di IPv6 per rete virtuale di Azure (anteprima)
titlesuffix: Azure Virtual Network
description: Descrizione IPv6 di endpoint e percorsi di dati IPv6 in una rete virtuale di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 5093b74484cd04a0c0c7afed8e2ebc725af033f5
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249824"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Che cos'è IPv6 per la rete virtuale di Azure? (Anteprima)

IPv6 per rete virtuale di Azure (VNet) consente di ospitare le applicazioni in Azure con connettività IPv6 e IPv4 sia all'interno di una rete virtuale che da e verso Internet. A causa dell'esaurimento degli indirizzi IPv4 pubblici, le nuove reti per la mobilità e la Internet delle cose (tutto) sono spesso basate su IPv6. Anche le reti ISP e mobile stabilite a lungo vengono trasformate in IPv6. I servizi solo IPv4 possono trovarsi in uno svantaggio reale nei mercati esistenti ed emergenti. La connettività IPv4/IPv6 dual stack consente ai servizi ospitati in Azure di attraversare questo gap tecnologico con servizi dual stack disponibili a livello globale che si connettono prontamente con i dispositivi IPv4 e le nuove reti IPv6 esistenti.

La connettività IPv6 originale di Azure consente di fornire facilmente connettività Internet dual stack (IPv4/IPv6) per le applicazioni ospitate in Azure. Consente di distribuire in modo semplice le VM con connettività IPv6 con bilanciamento del carico per le connessioni avviate sia in ingresso che in uscita. Questa funzionalità è ancora disponibile e altre informazioni sono disponibili [qui](../load-balancer/load-balancer-ipv6-overview.md).
IPv6 per la rete virtuale di Azure è molto più completo per l'abilitazione di architetture di soluzioni IPv6 complete da distribuire in Azure.

> [!Important]
> IPv6 per la rete virtuale di Azure è attualmente disponibile in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il diagramma seguente illustra una distribuzione semplice a doppio stack (IPv4/IPv6) in Azure:

![Diagramma di distribuzione della rete IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Vantaggi

Vantaggi IPv6 della rete virtuale di Azure:

- Consente di espandere la portata delle applicazioni ospitate in Azure in mercati mobili e Internet delle cose in crescita.
- Le VM IPv4/IPv6 dual stack offrono la massima flessibilità per la distribuzione del servizio. Una singola istanza del servizio può connettersi con client Internet compatibili con IPv4 e IPv6.
- Si basa su connettività IPv6 da macchina virtuale a Internet di Azure stabile e di lunga durata.
- Sicurezza per impostazione predefinita, poiché la connettività IPv6 a Internet viene stabilita solo quando viene richiesta in modo esplicito nella distribuzione.

## <a name="capabilities"></a>Funzionalità

IPv6 per VNet include le funzionalità seguenti:

- I clienti di Azure possono definire lo spazio di indirizzi della rete virtuale IPv6 per soddisfare le esigenze delle applicazioni, dei clienti o integrarsi facilmente nello spazio IP locale.
- Le reti virtuali dual stack (IPv4 e IPv6) con subnet dual stack consentono alle applicazioni di connettersi alle risorse IPv4 e IPv6 nella propria rete virtuale o in Internet.
- Proteggere le risorse con le regole IPv6 per i gruppi di sicurezza di rete
- Personalizzare il routing del traffico IPv6 nella rete virtuale con le route definite dall'utente, soprattutto quando si usano appliance virtuali di rete per migliorare l'applicazione.
- Consentire ai client Internet di accedere facilmente all'applicazione dual stack usando il protocollo scelto con il supporto DNS di Azure per i record IPv6 (AAAA). 
- Supporto di Load Balancer pubblico IPv6 standard per creare applicazioni resilienti e scalabili che includono:
    - Probe di integrità IPv6 facoltativo per determinare quali istanze del pool back-end sono integrità e quindi possono ricevere nuovi flussi. .  
    - Regole in uscita facoltative che offrono un controllo dichiarativo completo sulla connettività in uscita per scalare e ottimizzare questa capacità in base alle esigenze specifiche.
    - Più configurazioni front-end facoltative che consentono a un singolo servizio di bilanciamento del carico di usare più indirizzi IP pubblici IPv6. lo stesso protocollo e la stessa porta front-end possono essere riutilizzati in tutti gli indirizzi front-end.
- L'IP pubblico a livello di istanza fornisce connettività Internet IPv6 direttamente alle singole macchine virtuali.
- Aggiungere facilmente la connettività IPv6 alle distribuzioni solo IPv4 esistenti con l'aggiornamento sul posto.
- Creare applicazioni dual stack che si adattano automaticamente al carico con i set di scalabilità di macchine virtuali.
- Il supporto del portale per l'anteprima include la creazione/modifica/eliminazione interattiva di reti virtuali e subnet dual stack (IPv4 + IPv6), le regole del gruppo di sicurezza di rete IPv6, le route definite dall'utente IPv6 e gli indirizzi IP pubblici IPv6.  

## <a name="limitations"></a>Limitazioni
La versione di anteprima di IPv6 per la rete virtuale di Azure presenta le limitazioni seguenti:
- IPv6 per rete virtuale di Azure (anteprima) è disponibile in tutte le aree di Azure globali, ma solo in Azure globale, non nei cloud governativi.
- Il supporto del portale per i componenti Load Balancer Standard è di sola visualizzazione.  Sono tuttavia disponibili supporto completo e documentazione (con esempi) per le distribuzioni di Load Balancer Standard con Azure PowerShell e l'interfaccia della riga di comando (CLI).   
- Network Watcher supporto per l'anteprima è limitato ai log di flusso NSG e alle acquisizioni di pacchetti di rete.
- Il peering di rete virtuale (regionalmente o globalmente) non è supportato nella versione di anteprima.
- Quando si usa l'Load Balancer esterno IPv6 standard, si applicano i limiti seguenti: 
  - Le regole in uscita possono fare riferimento a più indirizzi IP pubblici front-end, ma **non** possono fare riferimento a un prefisso pubblico IPv6. Il prefisso IP pubblico supporta solo i prefissi IPv4.
  - Le regole di bilanciamento del carico IPv6 **non** possono usare la funzionalità *IP mobile* . Il riutilizzo delle porte nelle istanze back-end è supportato solo con IPv4.
- La funzionalità di prefisso degli indirizzi IP pubblici di Azure non supporta la riserva di un blocco di indirizzi IPv6 con connessione Internet.

## <a name="pricing"></a>Prezzi

Per le risorse e la larghezza di banda di Azure IPv6 vengono addebitate le stesse tariffe di IPv4. Per IPv6 non sono previsti addebiti aggiuntivi o diversi. È possibile trovare informazioni dettagliate sui prezzi per [gli indirizzi IP pubblici](https://azure.microsoft.com/pricing/details/ip-addresses/), la [larghezza di banda di rete](https://azure.microsoft.com/pricing/details/bandwidth/)o [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire un'applicazione IPv6 dual stack usando Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Informazioni su come [distribuire un'applicazione IPv6 dual stack usando l'interfaccia](virtual-network-ipv4-ipv6-dual-stack-cli.md)della riga di comando di Azure.
