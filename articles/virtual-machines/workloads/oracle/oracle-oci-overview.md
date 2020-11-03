---
title: Integrare Microsoft Azure con Oracle Cloud Infrastructure | Microsoft Docs
description: Informazioni sulle soluzioni per l'integrazione di app Oracle in esecuzione in Microsoft Azure con i database di Oracle Cloud Infrastructure (OCI).
author: dbakevlar
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/01/2020
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: c8d0f32821bbb3957d7787b7c3bfd733a880325b
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233917"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Soluzioni per l'integrazione di Microsoft Azure e Oracle Cloud Infrastructure per le applicazioni Oracle

Microsoft e Oracle hanno collaborato alla realizzazione di soluzioni in grado di offrire connettività tra cloud a bassa latenza e velocità effettiva elevata per consentire di sfruttare al meglio entrambi i cloud. 

Grazie a questa connettività tra cloud, è possibile partizionare un'applicazione multilivello in modo da eseguire il livello database in Oracle Cloud Infrastructure (OCI) e il livello applicazione e gli altri livelli in Microsoft Azure. L'esperienza è simile all'esecuzione dell'intero stack della soluzione in un unico cloud. 

Se si è interessati all'esecuzione del middleware, incluso WebLogic Server, nell'infrastruttura di Azure, ma il database Oracle viene eseguito all'interno dell'infrastruttura OCI, vedere [Applicazioni Oracle WebLogic Server in Azure](oracle-weblogic.md).

Se si è interessati alla distribuzione di soluzioni Oracle complete nell'infrastruttura di Azure, vedere [Immagini delle macchine virtuali Oracle e rispettiva distribuzione in Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Panoramica dello scenario

La connettività tra cloud offre una soluzione per l'esecuzione di applicazioni Oracle leader del settore e di applicazioni personalizzate nelle macchine virtuali di Azure, sfruttando al tempo stesso i vantaggi offerti dai servizi di database ospitati nell'infrastruttura OCI. 

A partire dal 2020 maggio, le applicazioni seguenti sono certificate in una configurazione tra cloud:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Applicazioni Oracle Retail
* Oracle Hyperion Financial Management

Il diagramma seguente offre una panoramica generale della soluzione connessa. Per semplicità, il diagramma mostra solo un livello applicazione e un livello dati. A seconda dell'architettura dell'applicazione, la soluzione potrebbe includere livelli aggiuntivi, ad esempio un cluster WebLogic Server o un livello Web in Azure. Per altre informazioni, vedere le sezioni seguenti.

![Panoramica della soluzione Azure OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Disponibilità a livello di area 

La connettività tra cloud è limitata alle aree seguenti:
* Stati Uniti orientali di Azure (Eastus) e OCI Ashburn, VA (Stati Uniti orientali)
* Regno Unito meridionale di Azure (UKSouth) e OCI London (Regno Unito meridionale)
* Canada centrale di Azure (CanadaCentral) e OCI Toronto (Canada sudorientale)
* Europa occidentale di Azure (WestEurope) e OCI Amsterdam (Paesi Bassi Nord-ovest)
* Giappone orientale di Azure (JapanEast) e OCI Tokyo (Giappone orientale)
* Stati Uniti occidentali di Azure (Westus) & OCI San Jose (Stati Uniti occidentali)

## <a name="networking"></a>Rete

I clienti aziendali spesso scelgono di diversificare e distribuire carichi di lavoro su più cloud per diversi motivi legati a esigenze aziendali e operative. Per diversificare, i clienti interconnettono le reti cloud tramite Internet o VPN IPSec oppure usando la soluzione di connettività diretta del provider di servizi cloud tramite la rete locale. L'interconnessione di reti cloud può richiedere investimenti significativi in termini di tempo, denaro, progettazione, approvvigionamento, installazione, test e operazioni. 

Per rispondere a queste esigenze dei clienti, Oracle e Microsoft hanno realizzato un'esperienza integrata di più cloud. L'interconnessione tra cloud viene stabilita connettendo un circuito [ExpressRoute](../../../expressroute/expressroute-introduction.md) in Microsoft Azure con un circuito [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) nell'infrastruttura OCI. Questa connettività è possibile quando una località di peering di Azure ExpressRoute si trova in prossimità o nella stessa località di peering del circuito FastConnect di OCI. Questa configurazione consente una connettività sicura e veloce tra i due cloud senza la necessità di un provider di servizi intermedio.

Con ExpressRoute e FastConnect, i clienti possono eseguire il peering di una rete virtuale in Azure con una rete cloud virtuale in OCI, purché lo spazio di indirizzi IP privato non si sovrapponga. Il peering delle due reti consente a una risorsa nella rete virtuale di comunicare con una risorsa nella rete cloud virtuale di OCI, come se si trovassero nella stessa rete.

## <a name="network-security"></a>Sicurezza di rete

La sicurezza di rete è un componente fondamentale di qualsiasi applicazione aziendale ed è fondamentale per questa soluzione multi-cloud. Il traffico su ExpressRoute e FastConnect passa attraverso una rete privata. Questa configurazione consente la comunicazione sicura tra una rete virtuale di Azure e una rete cloud virtuale Oracle. Non è necessario fornire un indirizzo IP pubblico ad alcuna macchina virtuale in Azure. Analogamente, non è necessario un gateway Internet in OCI. Tutte le comunicazioni avvengono tramite l'indirizzo IP privato delle macchine.

È inoltre possibile configurare [elenchi di sicurezza](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) nella rete cloud virtuale di OCI e regole di sicurezza (collegate ai [gruppi di sicurezza di rete](../../../virtual-network/network-security-groups-overview.md) di Azure). Usare queste regole per controllare il flusso di traffico tra le macchine nelle reti virtuali. È possibile aggiungere regole di sicurezza di rete a livello di macchina, di subnet e di rete virtuale.

Le [applicazioni WebLogic Server in Azure](oracle-weblogic.md) creano ciascuna un gruppo di sicurezza di rete preconfigurato per l'uso con le configurazioni delle porte di WebLogic Server.
 
## <a name="identity"></a>Identità

L'identità è uno dei principali pilastri della partnership tra Microsoft e Oracle. È stato dedicato un impegno notevole all'integrazione di [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDC) con [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Azure AD è il servizio Microsoft basato sul cloud per la gestione delle identità e dell'accesso. Con il supporto di Azure AD gli utenti possono accedere a diverse risorse. Azure AD consente inoltre di gestire gli utenti e le relative autorizzazioni.

Attualmente questa integrazione consente la gestione in un'unica posizione centrale, ovvero Azure Active Directory. Il servizio Azure AD sincronizza tutte le modifiche apportate alla directory con la directory Oracle corrispondente e viene usato per l'accesso Single Sign-On alle soluzioni Oracle tra cloud.

## <a name="next-steps"></a>Passaggi successivi

Eseguire i passaggi preliminari per un'[interconnessione di rete](configure-azure-oci-networking.md) tra Azure e OCI. 

Per altre informazioni e white paper su OCI, vedere la documentazione di [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm).
