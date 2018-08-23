---
title: Considerazioni relative all'integrazione per i sistemi integrati di Azure Stack di rete | Microsoft Docs
description: Informazioni su cosa è possibile fare per pianificare l'integrazione di rete di Data Center con Azure Stack con più nodi.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 48e2f85488adfb776da5f52c154028f8aafb167a
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617022"
---
# <a name="network-connectivity"></a>Connettività di rete
Questo articolo fornisce informazioni sull'infrastruttura di rete di Azure Stack per decidere come per integrarsi al meglio Azure Stack nell'ambiente di rete esistente. 

> [!NOTE]
> Per risolvere i nomi DNS esterni dallo Stack di Azure (ad esempio, www.bing.com), è necessario fornire i server DNS per inoltrare le richieste DNS. Per altre informazioni sui requisiti di DNS di Azure Stack, vedere [integrazione di Data Center di Azure Stack - DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Progettazione di rete fisica
La soluzione di Azure Stack richiede un'infrastruttura fisica resiliente e a disponibilità elevata per supportare il funzionamento e i servizi. Uplink da ToR alle opzioni del bordo sono limitati a SFP + o supporti SFP28 e 1 GB, 10 GB o velocità del 25 GB. Rivolgersi al fornitore dell'hardware (OEM) original equipment manufacturer per la disponibilità. Il diagramma seguente illustra la progettazione consigliata:

![Progettazione di rete di Azure Stack consigliato](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Reti logiche
Le reti logiche rappresentano un'astrazione dell'infrastruttura di rete fisica sottostante. Vengono utilizzati per organizzare e semplificare le assegnazioni di rete per host, macchine virtuali e servizi. Durante la creazione di reti logiche, siti di rete vengono creati per definire le reti locali virtuali (VLAN), subnet IP e coppie subnet/VLAN IP associati con la rete logica in ogni posizione fisica.

Nella tabella seguente mostra le reti logiche e intervalli di subnet IPv4 associati che è necessario pianificare:

| Rete logica | DESCRIZIONE | Dimensione | 
| -------- | ------------- | ------------ | 
| Indirizzo VIP pubblico | Azure Stack viene utilizzato un totale di 31 indirizzi da questa rete. Otto indirizzi IP pubblici vengono usati per un piccolo set di servizi di Azure Stack e gli altri vengono usati dalle macchine virtuali tenant. Se si prevede di usare servizio App e i provider di risorse SQL, vengono utilizzati 7 più indirizzi. Gli indirizzi IP 15 rimanenti sono riservati per futuri servizi di Azure. | / 26 (62 host) - /22 (1022 host)<br><br>Consigliato = da/24 (254 host) | 
| Infrastruttura di switch | Gli indirizzi IP da punto a punto per il routing, dedicato switch interfacce di gestione e indirizzo di loopback assegnata al commutatore. | /26 | 
| Infrastruttura | Utilizzato per i componenti interni di Azure Stack per comunicare. | /24 |
| Privato | Utilizzato per la rete di archiviazione e gli indirizzi VIP privati. | /24 | 
| BMC | Utilizzato per comunicare con il BMC negli host fisici. | /27 | 
| | | |

## <a name="network-infrastructure"></a>Infrastruttura di rete
L'infrastruttura di rete per Azure Stack è costituito da diverse reti logiche configurate negli switch. Il diagramma seguente mostra le reti logiche, e come integrare con il top-of-rack (TOR), baseboard management controller (BMC) e border commutatori (rete del cliente).

![Connessioni di diagramma e switch di rete logica](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Rete BMC
Questa rete è dedicata alla connessione di tutti i baseboard management controller (noto anche come processori di servizio, ad esempio, iDRAC, iLO, iBMC, e così via) per la rete di gestione. Se presente, l'Host di ciclo di vita dell'Hardware (HLH) si trova su questa rete e può offrire software specifico dell'OEM per la manutenzione dell'hardware o il monitoraggio. 

Il HLH ospita anche la distribuzione della macchina virtuale DVM (). Di DVM usata durante la distribuzione di Azure Stack e viene rimosso al termine della distribuzione. Di DVM richiede l'accesso a internet negli scenari di distribuzione connessi a test, convalida e accedere a più componenti. Questi componenti possono essere all'interno e all'esterno della rete azienda. ad esempio, NTP, DNS e Azure. Per altre informazioni sui requisiti di connettività, vedere la [sezione NAT nell'integrazione con Azure Stack firewall](azure-stack-firewall.md#network-address-translation). 

### <a name="private-network"></a>Rete privata
Questo da/24 (gli indirizzi IP di 254 host) di rete è privata nell'area di Azure Stack (non espande oltre i dispositivi di commutatore del bordo dell'area di Azure Stack) e viene suddivisa in due subnet:

- **Rete di archiviazione**. Un /25 (host 126 gli indirizzi IP) utilizzata per supportare l'uso di spazi diretti o Server Message Block (SMB) il traffico di archiviazione e migrazione in tempo reale delle macchine virtuali di rete. 
- **Rete IP virtuale interna**. Oggetto/25 rete dedicata all'interno di solo gli indirizzi VIP per bilanciamento del carico software.

### <a name="azure-stack-infrastructure-network"></a>Rete dell'infrastruttura di Azure Stack
In questo/24 rete dedicato ai componenti interni di Azure Stack, in modo che possano comunicare e scambiare dati tra loro. Questa subnet richiede indirizzi IP instradabili, ma verrà mantenuta privata per la soluzione con elenchi di controllo di accesso (ACL). Non deve essere indirizzato oltre le opzioni del bordo, ad eccezione di una gamma ristretta equivalente in dimensioni di/27 rete usata da alcuni di questi servizi quando richiedono l'accesso alle risorse esterne e/o internet. 

### <a name="public-infrastructure-network"></a>Rete pubblica dell'infrastruttura
In questo/rete 27 è l'intervallo di piccole dimensioni dalla subnet dell'infrastruttura di Azure Stack indicata in precedenza, non richiede gli indirizzi IP pubblici, ma richiede l'accesso a internet tramite un dispositivo NAT o un Proxy trasparente. Questa rete verrà allocata per le emergenze ripristino Console System (ERCS), la VM ERCS richiede l'accesso a internet durante la registrazione in Azure e durante i backup dell'infrastruttura. La VM ERCS devono essere instradabile alla rete di gestione per la risoluzione dei problemi.

### <a name="public-vip-network"></a>Rete indirizzi VIP pubblici
La rete VIP pubblico viene assegnata al controller di rete in Azure Stack. Non è una rete logica nel commutatore. Il bilanciamento del carico software Usa il pool di indirizzi e assegna/32 di reti per carichi di lavoro tenant. Nella tabella di routing di commutatore, questi indirizzi IP di 32 vengono annunciati come non disponibili tramite il protocollo BGP. Questa rete contiene gli indirizzi IP pubblici o accessibile dall'esterno. L'infrastruttura di Azure Stack riserva i primi 31 indirizzi da questa rete VIP pubblici mentre il resto viene usato da macchine virtuali tenant. Le dimensioni di rete in questa subnet possono variare da un minimo di /26 (64 host) a un massimo di /22 (1022 host), è consigliabile pianificare/24 rete.

### <a name="switch-infrastructure-network"></a>Cambia rete dell'infrastruttura
In questo/26 rete è la subnet che contiene la subnet di (host 2 indirizzi IP) / 30 di IP da punto a punto instradabili e il loopback, di quali dedicati/32 subnet per Inband switch management e ID del router BGP. Questo intervallo di indirizzi IP deve essere instradabile dall'esterno della soluzione Azure Stack nel tuo Data Center, possono essere indirizzi IP pubblici o privati.

### <a name="switch-management-network"></a>Rete di gestione commutatore
Questo pari a/29 (host 6 indirizzi IP) rete è dedicata alla connessione di porte di gestione delle opzioni. Consente l'accesso fuori banda per la distribuzione, gestione e risoluzione dei problemi. Viene calcolato dalla rete dell'infrastruttura di switch indicato in precedenza.

## <a name="publish-azure-stack-services"></a>Pubblicare i servizi di Azure Stack
È necessario rendere disponibili i servizi di Azure Stack per gli utenti dall'esterno di Azure Stack. Azure Stack consente di configurare gli endpoint diversi per i ruoli di infrastruttura. Questi endpoint vengono assegnati gli indirizzi VIP dal pool di indirizzi IP pubblici. Viene creata una voce DNS per ogni endpoint nella zona DNS esterna, che è stata specificata in fase di distribuzione. Ad esempio, il portale per gli utenti viene assegnato la voce host DNS del portale.  *&lt;area >.&lt; FQDN >*.

### <a name="ports-and-urls"></a>Porte e URL
Per rendere i servizi di Azure Stack (ad esempio i portali, Azure Resource Manager, DNS, e così via) disponibili per le reti esterne, è necessario consentire il traffico in ingresso a questi endpoint per gli URL, porte e protocolli specifici.
 
In una distribuzione in cui un uplink proxy trasparente per un server proxy tradizionali, è necessario consentire determinate porte e URL per entrambi [in ingresso](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound) e [uscita](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound) comunicazione. Tra le porte e URL per i dati di identità, il marketplace, patch e aggiornamenti, registrazione e utilizzo.

## <a name="next-steps"></a>Passaggi successivi
[Connettività del bordo](azure-stack-border-connectivity.md)