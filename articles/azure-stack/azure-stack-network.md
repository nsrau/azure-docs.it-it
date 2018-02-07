---
title: Considerazioni relative all'integrazione per Azure Stack integrati sistemi di rete | Documenti Microsoft
description: "Informazioni sulle operazioni per pianificare l'integrazione di rete di Data Center con lo Stack di Azure a più nodi."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: a198ff5fe7135e17301025d6a712236b76be0ede
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="network-connectivity"></a>Connettività di rete
Questo articolo fornisce informazioni sull'infrastruttura di rete di Azure Stack utili per decidere come integrare più Stack di Azure nell'ambiente di rete esistente. 

> [!NOTE]
> Per risolvere i nomi DNS esterni dallo Stack di Azure (ad esempio, www.bing.com), è necessario fornire i server DNS per inoltrare le richieste DNS. Per ulteriori informazioni sui requisiti di Azure Stack DNS, vedere [integrazione con Data Center di Azure Stack - DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Progettazione di rete fisica
La soluzione Azure Stack richiede un'infrastruttura fisica resiliente e a disponibilità elevata per supportare il funzionamento e i servizi. Nel diagramma seguente rappresenta la progettazione consigliata:

![Progettazione di rete consigliato Azure Stack](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Reti logiche
Le reti logiche rappresentano un'astrazione dell'infrastruttura di rete fisica sottostante. Vengono utilizzati per organizzare e semplificare le assegnazioni di rete per host, macchine virtuali e servizi. Come parte della creazione di reti logiche, siti di rete vengono creati per definire le reti locali virtuali (VLAN), subnet IP e coppie subnet IP/VLAN associate alla rete logica in ogni posizione fisica.

La tabella seguente illustra le reti logiche e associare gli intervalli di subnet IPv4 che è necessario pianificare:

| Rete logica | DESCRIZIONE | Dimensione | 
| -------- | ------------- | ------------ | 
| VIP pubblico | Indirizzi IP pubblici per un piccolo set di servizi di Azure Stack, con il resto venga utilizzato dalle macchine virtuali tenant. L'infrastruttura di Azure Stack utilizza 32 indirizzi da questa rete. Se si prevede di utilizzare il provider di risorse SQL e di servizio App, vengono utilizzati 7 più indirizzi. | / 26 (62 host) - /22 (1022 host)<br><br>Consigliato = /24 (254 host) | 
| Infrastruttura switch | Gli indirizzi IP Point to Point a scopo di routing, dedicato passare le interfacce di gestione e gli indirizzi di loopback assegnati al commutatore. | /26 | 
| Infrastruttura | Utilizzato per i componenti interni dello Stack di Azure per comunicare. | /24 |
| Privato | Utilizzato per la rete di archiviazione e gli indirizzi VIP privato. | /24 | 
| BMC | Utilizzato per comunicare con il BMC negli host fisici. | /27 | 
| | | |

## <a name="network-infrastructure"></a>Infrastruttura di rete
L'infrastruttura di rete per lo Stack di Azure è costituita da più reti logiche configurate nei commutatori. Il diagramma seguente mostra le reti logiche e come integrarlo di top-of-rack (TOR), baseboard management controller (BMC) e il bordo switch (rete cliente).

![Connessioni di diagramma e il commutatore di rete logica](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Rete BMC
Questa rete è dedicata per tutti i baseboard management controller (noto anche come servizio processori, ad esempio, iDRAC, iLO, iBMC, ecc.) la connessione alla rete di gestione. Se presente, l'host del ciclo di vita di hardware (HLH) si trova nella rete e può costituire software specifico OEM per la manutenzione dell'hardware e/o di monitoraggio. 

### <a name="private-network"></a>Rete privata
Questo /24 254 host della rete IP) (è privato per l'area dello Stack di Azure (non superare i dispositivi a sensore bordo dell'area dello Stack di Azure) ed è suddivisa in due subnet:

- **Rete di archiviazione**. Migrazione in tempo reale un /25 126 host della rete IP) (utilizzata per supportare l'utilizzo di traffico di archiviazione diretta di spazi e Server Message Block (SMB) e la macchina virtuale. 
- **Rete IP virtuale interna**. A/25 dedicata all'interno solo per gli indirizzi VIP per il bilanciamento del carico software di rete.

### <a name="azure-stack-infrastructure-network"></a>Rete infrastruttura di Azure Stack
Questo/24 rete dedicato per i componenti interni dello Stack di Azure in modo che possano comunicare e scambiare dati tra loro. Questa subnet richiede gli indirizzi IP instradabili, ma è privata per la soluzione tramite elenchi di controllo di accesso (ACL). Non è previsto l'indirizzamento oltre le opzioni di bordo, ad eccezione di un intervallo di piccole dimensioni uguali a quelle di un /27 rete usata da alcuni di questi servizi quando richiedono l'accesso alle risorse esterne e/o internet. 

### <a name="public-infrastructure-network"></a>Rete pubblica infrastruttura
Questo/27 rete è compreso nell'intervallo piccola tra la subnet dell'infrastruttura Azure Stack indicata in precedenza, non richiede indirizzi IP pubblici, ma richiede l'accesso a internet tramite un dispositivo NAT o un Proxy trasparente. Questa rete verrà allocata per l'emergenza ripristino Console di sistema (ERCS), la VM ERCS richiede l'accesso a internet durante la registrazione in Azure e deve essere instradabile alla rete di gestione per la risoluzione dei problemi.

### <a name="public-vip-network"></a>Rete pubblica di indirizzi VIP
Rete pubblica VIP viene assegnata al controller di rete nello Stack di Azure. Non è una rete logica del commutatore. Il SLB utilizza il pool di indirizzi e assegna/32 di reti per carichi di lavoro tenant. Nella tabella di routing di commutatore, questi indirizzi IP di 32 vengono annunciate come non disponibili tramite il protocollo BGP. Questa rete contiene gli indirizzi IP pubblici o esterni accessibili. L'infrastruttura di Azure Stack utilizza almeno 8 indirizzi da questa rete VIP pubblico, mentre il resto viene utilizzato da macchine virtuali tenant. Le dimensioni della subnet di rete possono variare da un minimo di /26 (64 host) a un massimo di /22 (1022 host), è consigliabile pianificare un /24 rete.

### <a name="switch-infrastructure-network"></a>Rete infrastruttura commutatore
Questo/26 rete è la subnet contenente subnet di /30 IP instradabile Point to Point (IP host 2) e i loopback di cui dedicati/32 subnet per in banda passare gestione e l'ID del router BGP. Questo intervallo di indirizzi IP deve essere instradabile dall'esterno della soluzione dello Stack di Azure al Data Center, possono essere indirizzi IP pubblici o privati.

### <a name="switch-management-network"></a>Rete di gestione commutatore
Questo /29 (host 6 indirizzi IP) rete dedicata per le porte di gestione delle opzioni di connessione. Consente l'accesso fuori banda per la distribuzione, gestione e risoluzione dei problemi. Viene calcolato dalla rete dell'infrastruttura commutatore indicata in precedenza.

## <a name="publish-azure-stack-services"></a>La pubblicazione dei servizi Azure Stack
È necessario rendere disponibili i servizi di Azure Stack agli utenti dallo Stack all'esterno di Azure. Stack di Azure consente di impostare vari endpoint per i ruoli di infrastruttura. Questi endpoint vengono assegnati indirizzi IP virtuali dal pool di indirizzi IP pubblici. Una voce DNS viene creata per ogni endpoint nella zona DNS esterna, che è stata specificata in fase di distribuzione. Ad esempio, il portale per gli utenti viene assegnato alla voce host DNS del portale.  *&lt;area >.&lt; nome di dominio completo >*.

### <a name="ports-and-urls"></a>Porte e gli URL
Per rendere i servizi di Azure Stack (ad esempio i portali, Gestione risorse di Azure, DNS e così via) disponibile per le reti esterne, è necessario consentire il traffico in ingresso con questi endpoint per gli URL, porte e protocolli specifici.
 
In una distribuzione in cui un uplink proxy trasparente per un server proxy tradizionali, è necessario consentire determinate porte e gli URL per entrambi [in ingresso](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound) e [in uscita](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound) comunicazione. Tra le porte e gli URL per identità, diffusione di marketplace, patch e aggiornamento, registrazione e dati di utilizzo.

## <a name="next-steps"></a>Passaggi successivi
[Connettività di bordo](azure-stack-border-connectivity.md)