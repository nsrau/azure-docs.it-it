---
title: Integrazione di Microsoft Azure con l'infrastruttura cloud Oracle Documenti Microsoft
description: Informazioni sulle soluzioni che integrano app Oracle in esecuzione in Microsoft Azure con database in Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: e1249913300be532cc6514f1478bbc6f4183c001
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300554"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Soluzioni applicative Oracle che integrano Microsoft Azure e Oracle Cloud Infrastructure (anteprima)Oracle application solutions integrating Microsoft Azure and Oracle Cloud Infrastructure (preview)

Microsoft e Oracle hanno collaborato per fornire connettività cross-cloud a bassa latenza e velocità effettiva, consentendo di sfruttare al meglio entrambi i cloud. 

Utilizzando questa connettività cross-cloud, è possibile partizionare un'applicazione multilivello per eseguire il livello di database in Oracle Cloud Infrastructure (OCI) e l'applicazione e altri livelli in Microsoft Azure. L'esperienza è simile all'esecuzione dell'intero stack di soluzioni in un singolo cloud. 

> [!IMPORTANT]
> Questa funzionalità cross-cloud è attualmente in anteprima e [si applicano limitazioni.](#region-availability) Per stabilire la connettività a bassa latenza tra Azure e OCI, la sottoscrizione di Azure deve prima essere abilitata per questa funzionalità. È necessario iscriversi all'anteprima compilando questo breve [modulo di indagine](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu). Si riceverà un messaggio di posta elettronica dopo la registrazione della sottoscrizione. Non è possibile usare la funzionalità finché non si riceve un messaggio di posta elettronica di conferma. È inoltre possibile contattare il rappresentante Microsoft per essere abilitato per questa anteprima. L'accesso alla funzionalità di anteprima è soggetto a disponibilità e limitato da Microsoft a sua esclusiva discrezione. Il completamento dell'indagine non garantisce l'accesso. Questa anteprima viene fornita senza un contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Per informazioni dettagliate, vedere [Le Condizioni supplementari per l'utilizzo delle](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) anteprime di Microsoft Azure.See the Supplemental Terms of Use for Microsoft Azure Previews for details. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

Se si è interessati a distribuire soluzioni Oracle interamente nell'infrastruttura di Azure, vedere [Immagini di macchine virtuali Oracle e relativa distribuzione in Microsoft Azure.](oracle-vm-solutions.md)

## <a name="scenario-overview"></a>Panoramica dello scenario

La connettività cross-cloud offre una soluzione per eseguire le applicazioni leader del settore Oracle e le proprie applicazioni personalizzate nelle macchine virtuali di Azure, godendone i vantaggi dei servizi di database ospitati in OCI. 

Le applicazioni che è possibile eseguire in una configurazione cross-cloud includono:Applications you can run in a cross-cloud configuration include:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Applicazioni Oracle Retail
* Oracle Hyperion Financial Management

Il diagramma seguente è una panoramica generale della soluzione connessa. Per semplicità, il diagramma mostra solo un livello applicazione e un livello dati. A seconda dell'architettura dell'applicazione, la soluzione potrebbe includere livelli aggiuntivi, ad esempio un livello Web in Azure.Depending on the application architecture, your solution could include additional tiers such as a web tier in Azure. Per altre informazioni, vedere le sezioni seguenti.

![Panoramica della soluzione OCI di AzureAzure OCI solution overview](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Disponibilità della regione 

La connettività cross-cloud è limitata alle seguenti aree:
* Azure East US (eastus) & OCI Ashburn (Stati Uniti orientali)
* Azure Italia Sud (uksouth) & OCI London (Regno Unito sud)
* Azure Canada Central (canadacentral) & OCI Toronto (Canada sud-est)
* Azure West Europe (westeurope) & OCI Amsterdam (Paesi Bassi nord-occidentali)

## <a name="networking"></a>Rete

I clienti aziendali spesso scelgono di diversificare e distribuire i carichi di lavoro su più cloud per vari motivi aziendali e operativi. Per diversificare, i clienti interagiscono tra le reti cloud utilizzando Internet, IPSec VPN o la soluzione di connettività diretta del provider cloud tramite la rete locale. L'interconnessione delle reti cloud può richiedere investimenti significativi in tempo, denaro, progettazione, approvvigionamento, installazione, test e operazioni. 

Per affrontare queste sfide con i clienti, Oracle e Microsoft hanno consentito un'esperienza multi-cloud integrata. La rete cross-cloud viene stabilita connettendo un circuito [ExpressRoute](../../../expressroute/expressroute-introduction.md) in Microsoft Azure con un circuito [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) in OCI. Questa connettività è possibile quando una posizione di peering di Azure ExpressRoute si trova in prossimità o nella stessa posizione di peering di OCI FastConnect.This connectivity is possible where an Azure ExpressRoute peering location is in proximity to or in the same peering location as the OCI FastConnect. Questa configurazione consente una connettività sicura e veloce tra i due cloud senza la necessità di un provider di servizi intermedi.

Usando ExpressRoute e FastConnect, i clienti possono eseguire il peer network virtuale in Azure con una rete cloud virtuale in OCI, a condizione che lo spazio di indirizzi IP privati non si sovrapponga. Il peering delle due reti consente a una risorsa nella rete virtuale di comunicare con una risorsa nella rete cloud virtuale OCI come se entrambe si trovassero nella stessa rete.

## <a name="network-security"></a>Sicurezza di rete

La sicurezza di rete è un componente fondamentale di qualsiasi applicazione aziendale ed è fondamentale per questa soluzione multi-cloud. Qualsiasi traffico che passa su ExpressRoute e FastConnect passa su una rete privata. Questa configurazione consente una comunicazione sicura tra una rete virtuale di Azure e una rete cloud virtuale Oracle.This configuration allows for secure communication between an Azure virtual network and an Oracle virtual cloud network. Non è necessario fornire un indirizzo IP pubblico alle macchine virtuali in Azure.You don't need to provide a public IP address to any virtual machines in Azure. Allo stesso modo, non è necessario un gateway Internet in OCI. Tutte le comunicazioni avvengono tramite l'indirizzo IP privato delle macchine.

Inoltre, è possibile configurare elenchi di [sicurezza](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) nella rete cloud virtuale OCI e nelle regole di sicurezza (collegate ai gruppi di sicurezza di rete di [Azure).](../../../virtual-network/security-overview.md) Usare queste regole per controllare il traffico che passa tra le macchine nelle reti virtuali. Le regole di sicurezza di rete possono essere aggiunte a livello di computer, a livello di subnet e a livello di rete virtuale.Network security rules can be added at a machine level, at a subnet level, as and at the virtual network level.
 
## <a name="identity"></a>Identità

L'identità è uno dei pilastri fondamentali della partnership tra Microsoft e Oracle. È stato fatto un lavoro significativo per integrare [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) con Azure Active [Directory](../../../active-directory/index.yml) (Azure AD). Azure AD è il servizio di gestione di identità e accessi basato su cloud di Microsoft.Azure AD is Microsoft's cloud-based identity and access management service. Consente agli utenti di accedere a varie risorse. Azure AD consente inoltre di gestire gli utenti e le relative autorizzazioni.

Attualmente, questa integrazione consente di gestire in un'unica posizione centrale, ovvero Azure Active Directory.Currently, this integration allows you to manage in one central location, which is Azure Active Directory. Azure AD sincronizza tutte le modifiche nella directory con la directory Oracle corrispondente e viene usato per l'accesso Single Sign-On alle soluzioni Oracle cross-cloud.

## <a name="next-steps"></a>Passaggi successivi

Introduzione a una [rete cross-cloud](configure-azure-oci-networking.md) tra Azure e OCI. 

Per altre informazioni e white paper su OCI, vedere la documentazione di [Oracle Cloud.](https://docs.cloud.oracle.com/iaas/Content/home.htm)
