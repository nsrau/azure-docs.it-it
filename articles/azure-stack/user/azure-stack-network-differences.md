---
title: Azure Stack le differenze e considerazioni sulla rete | Microsoft Docs
description: Informazioni sulle differenze e considerazioni quando si lavora con tecnologie di rete in Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.date: 01/25/2019
ms.topic: article
ms.service: azure-stack
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: f0e3eca0c38a47e7107e52464e889580dd0b1b8a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243573"
---
# <a name="considerations-for-azure-stack-networking"></a>Considerazioni per la rete di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

La rete di Azure Stack ha molte delle funzionalità fornite dalla rete di Azure. Tuttavia, esistono alcune differenze fondamentali che è necessario comprendere prima di distribuire una rete di Azure Stack.

Questo articolo offre una panoramica delle considerazioni univoche per la rete di Azure Stack e le relative funzionalità. Per altre informazioni sulle differenze generali tra Azure e Azure Stack, vedere la [considerazioni chiave](azure-stack-considerations.md) articolo.

## <a name="cheat-sheet-networking-differences"></a>Foglio informativo: Differenze di funzionalità di rete

| Service | Funzionalità | Azure (globale) | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | DNS di multi-tenant | Supportato | Non è ancora supportata |
|  | Record AAAA DNS | Supportato | Non supportate |
|  | Zone DNS per ogni sottoscrizione | 100 (valore predefinito)<br>Può essere aumentato su richiesta. | 100 |
|  | Set di record DNS per ogni zona | 5000 (impostazione predefinita)<br>Può essere aumentato su richiesta. | 5000 |
|  | Server dei nomi per la delega delle zone | Azure offre quattro server dei nomi per ogni zona di utente (tenant) che viene creato. | Azure Stack offre due server dei nomi per ogni zona di utente (tenant) che viene creato. |
| Rete virtuale | Peering di rete virtuale | Connettere due reti virtuali nella stessa area tramite la rete backbone di Azure. | Non è ancora supportata |
|  | Indirizzi IPv6 | È possibile assegnare un indirizzo IPv6 come parte di [configurazione dell'interfaccia di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions). | È supportato solo il protocollo IPv4. |
|  | Piano di protezione DDoS | Supportato | Non è ancora supportata. |
|  | Set di scalabilità di configurazioni IP | Supportato | Non è ancora supportata. |
|  | Servizi di accesso privata (Subnet) | Supportato | Non è ancora supportata. |
|  | Endpoint servizio | Supportato per il collegamento interno (non Internet) in servizi di Azure. | Non è ancora supportata. |
| È supportato solo il protocollo IPv4. | Criteri degli endpoint di servizio | Supportato | Non è ancora supportata. |
|  | Esegue il servizio | Supportato | Non è ancora supportata.  |
| Gruppi di sicurezza di rete | Regole di sicurezza ottimizzate | Supportato | Non è ancora supportata. |
|  | Regole di sicurezza valide | Supportato | Non è ancora supportata. |
|  | Gruppi di sicurezza delle applicazioni | Supportato | Non è ancora supportata. |
| Gateway di rete virtuale | Gateway VPN da punto a sito | Supportato | Non è ancora supportata. |
|  | Gateway di rete virtuale a rete virtuale | Supportato | Non è ancora supportata. |
|  | Tipo di Gateway di rete virtuale | Azure supporta VPN<br> Express Route <br> Net Hyper | Azure Stack supporta solo tipo di VPN in questo momento. |
|  | SKU del Gateway VPN | Supporto per Basic, GW1, GW2, GW3, ad alte prestazioni Standard, prestazioni estremamente elevate. | Supporto per SKU Basic, Standard e ad alte prestazioni. |
|  | Tipo di VPN | Azure supporta sia basata su criteri e basati su Route. | Azure Stack supporta basato su Route solo. |
|  | Impostazioni BGP | Azure supporta la configurazione dell'indirizzo di Peering BGP e il peso Peer. | Indirizzo di Peering BGP e il peso Peer vengono configurate automaticamente in Azure Stack. Non è possibile per l'utente debba configurare queste impostazioni con i propri valori. |
|  | Sito Gateway predefinito | Azure supporta la configurazione di un sito predefinito per il tunneling forzato. | Non è ancora supportata. |
|  | Ridimensionamento di gateway | Azure supporta il ridimensionamento del gateway dopo la distribuzione. | Modifica delle dimensioni non supportato. |
|  | Configurazione attiva/attiva | Supportato | Non è ancora supportata. |
|  | UsePolicyBasedTrafficSelectors | Azure supporta l'uso dei selettori di traffico basata su criteri con le connessioni del gateway basato su route. | Non è ancora supportata. |
| Bilanciamento del carico | SKU | Base e Load Balancer Standard sono supportate | È supportato solo il servizio Load Balancer Basic.  La proprietà SKU non è supportata. |
|  | Zone | Sono supportate le zone di disponibilità. | Non è ancora supportata |
|  | Regole NAT in ingresso supportano gli endpoint del servizio | Azure supporta specificando gli endpoint di servizio per le regole NAT in ingresso. | Azure Stack non supporta ancora gli endpoint di servizio, pertanto questi possono essere specificati. |
|  | Protocollo | Azure supporta la specifica di GRE o ESP. | Classe di protocollo non è supportato in Azure Stack. |
| Indirizzo IP pubblico | Versione dell'indirizzo IP pubblico | Azure supporta sia IPv4 che IPv6 | È supportato solo il protocollo IPv4. |
| Interfaccia di rete | Ottenere la tabella di Route valida | Supportato | Non è ancora supportata. |
|  | Ottenere gli ACL efficaci | Supportato | Non è ancora supportata. |
|  | Abilitare Rete accelerata | Supportato | Non è ancora supportata. |
|  | Inoltro IP | Disattivata per impostazione predefinita.  Può essere abilitata. | Attivare e disattivare questa impostazione non è supportato.  In per impostazione predefinita. |
|  | Gruppi di sicurezza delle applicazioni | Supportato | Non è ancora supportata. |
|  | Etichetta del nome DNS interno | Supportato | Non è ancora supportata. |
|  | Versione indirizzo IP privato | Sono supportati sia IPv4 che IPv6. | È supportato solo il protocollo IPv4. |
| Network Watcher | Le funzionalità di monitoraggio della rete tenant Watcher di rete | Supportato | Non è ancora supportata. |
| RETE CDN | Profili di rete per la distribuzione del contenuto | Supportato | Non è ancora supportata. |
| gateway applicazione | Bilanciamento del carico di livello 7 | Supportato | Non è ancora supportata. |
| Gestione traffico | Instradare il traffico in arrivo per prestazioni ottimali delle applicazioni e l'affidabilità. | Supportato | Non è ancora supportata. |
| Express Route | Configurare una connessione privata veloce a servizi cloud Microsoft dalla struttura dell'infrastruttura o con più sedi locali. | Supportato | Supporto per la connessione di Azure Stack per un circuito Expressroute. |

## <a name="next-steps"></a>Passaggi successivi

[DNS in Azure Stack](azure-stack-dns.md)
