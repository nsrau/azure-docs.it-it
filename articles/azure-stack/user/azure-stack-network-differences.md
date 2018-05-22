---
title: 'Rete di Azure dello Stack: Considerazioni e le differenze'
description: Informazioni sulle considerazioni e le differenze quando si lavora con la rete nello Stack di Azure.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.author: mabrigg
ms.date: 05/14/2018
ms.topic: article
ms.service: azure-stack
ms.openlocfilehash: 2a4c5bce072970f158a89763ebdf4132eafe9cbe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
---
# <a name="considerations-for-azure-stack-networking"></a>Considerazioni per la rete di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Azure Stack di rete ha molte funzionalità fornite dalla funzionalità di rete di Azure. Tuttavia, esistono alcune differenze fondamentali che è necessario comprendere prima di distribuire una rete di Azure Stack.

In questo articolo viene fornita una panoramica delle considerazioni univoche per Azure Stack di rete e delle relative funzionalità. Per ulteriori informazioni sulle differenze generali tra Stack di Azure e Azure, vedere il [considerazioni chiave](azure-stack-considerations.md) argomento.

## <a name="cheat-sheet-networking-differences"></a>Schede di riferimento rapido: differenze di funzionalità di rete

|Service | Funzionalità | Azure (globale) | Azure Stack |
| --- | --- | --- | --- |
| DNS | DNS multi-tenant | Supportato| Non è ancora supportato|
| |Record AAAA DNS|Supportato|Non supportate|
| |Zone DNS per ogni sottoscrizione|100 (impostazione predefinita)<br>Può essere aumentato su richiesta.|100|
| |I set di record DNS per ogni zona|5000 (impostazione predefinita)<br>Può essere aumentato su richiesta.|5000|
||Server dei nomi per la delega delle zone|Azure offre quattro server dei nomi per ogni zona utente (tenant) che viene creato.|Stack di Azure fornisce due server dei nomi per ogni zona utente (tenant) che viene creato.|
| Rete virtuale|Peering di rete virtuale|Connettere due reti virtuali nella stessa area tramite la backbone di Azure.|Non è ancora supportato|
| |Indirizzi IPv6|È possibile assegnare un indirizzo IPv6 come parte di [configurazione di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions).|È supportato solo il protocollo IPv4.|
|Gateway VPN|Gateway VPN Point-to-Site|Supportato|Non è ancora supportato|
| |Gateway di rete virtuale a|Supportato|Non è ancora supportato|
| |SKU di Gateway VPN|Supporto per Basic, GW1, GW2, GW3, Standard ad alte prestazioni, prestazioni estremamente elevate. |Supporto per Basic, Standard e ad alte prestazioni SKU.|
|Bilanciamento del carico|Indirizzi IP pubblici IPv6|Supporto per l'assegnazione di un indirizzo IP pubblico IPv6 a un bilanciamento del carico.|È supportato solo il protocollo IPv4.|
|Network Watcher|Funzionalità di monitoraggio della rete tenant Watcher di rete|Supportato|Non è ancora supportato|
|RETE CDN|Profili di rete per la distribuzione del contenuto|Supportato|Non è ancora supportato|
|gateway applicazione|Bilanciamento del carico di livello 7|Supportato|Non è ancora supportato|
|Gestione traffico|Instradare il traffico in ingresso per affidabilità e prestazioni ottimali delle applicazioni.|Supportato|Non è ancora supportato|
|Express Route|Configurare una connessione veloce e privata per servizi cloud Microsoft dalle funzionalità di infrastruttura o la condivisione percorso locale.|Supportato|Supporto per la connessione Azure Stack a un circuito Express Route.|

## <a name="next-steps"></a>Passaggi successivi

[DNS in Azure Stack](azure-stack-dns.md)
