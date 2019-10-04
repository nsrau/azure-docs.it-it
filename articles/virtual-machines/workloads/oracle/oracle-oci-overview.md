---
title: Integrare Microsoft Azure con l'infrastruttura cloud Oracle | Microsoft Docs
description: Informazioni sulle soluzioni che integrano le app Oracle in esecuzione in Microsoft Azure con i database in Oracle Cloud Infrastructure (OCI).
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
ms.openlocfilehash: 0f35f22d6bd32d75d5c3b07d8a20b4bb7714e247
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71799554"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Soluzioni di applicazioni Oracle che integrano Microsoft Azure e l'infrastruttura cloud Oracle (anteprima)

Microsoft e Oracle hanno collaborato per offrire connettività tra cloud a bassa latenza e velocità effettiva elevata, consentendo di sfruttare al meglio entrambi i cloud. 

Grazie a questa connettività tra cloud, è possibile partizionare un'applicazione multilivello per eseguire il livello di database in Oracle Cloud Infrastructure (OCI) e l'applicazione e altri livelli in Microsoft Azure. L'esperienza è simile all'esecuzione dell'intero stack della soluzione in un unico Cloud. 

> [!IMPORTANT]
> Questa funzionalità tra cloud è attualmente in anteprima e le [limitazioni si applicano](#preview-limitations). Per stabilire una connettività a bassa latenza tra Azure e OCI, è necessario abilitare prima la sottoscrizione di Azure per questa funzionalità. È necessario iscriversi all'anteprima completando questo breve [modulo di questionario](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu). Si riceverà un messaggio di posta elettronica dopo la registrazione della sottoscrizione. Non è possibile usare la funzionalità finché non si riceve un messaggio di posta elettronica di conferma. È anche possibile contattare il rappresentante Microsoft per abilitare questa versione di anteprima. L'accesso alla funzionalità di anteprima è soggetto alla disponibilità e limitato da Microsoft a sua esclusiva discrezione. Il completamento del sondaggio non garantisce l'accesso. Questa versione di anteprima viene fornita senza un contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Per informazioni dettagliate, vedere le condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per Microsoft Azure anteprime. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

Se si è interessati alla distribuzione di soluzioni Oracle interamente nell'infrastruttura di Azure, vedere [le immagini di VM Oracle e la relativa distribuzione in Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Panoramica dello scenario

La connettività tra cloud offre una soluzione per l'esecuzione di applicazioni leader nel settore Oracle e delle applicazioni personalizzate in macchine virtuali di Azure, sfruttando al contempo i vantaggi offerti dai servizi di database ospitati in OCI. 

Le applicazioni che è possibile eseguire in una configurazione tra cloud includono:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Applicazioni Oracle Retail
* Gestione finanziaria Oracle Hyperion

Il diagramma seguente è una panoramica di alto livello della soluzione connessa. Per semplicità, il diagramma mostra solo un livello applicazione e un livello dati. A seconda dell'architettura dell'applicazione, la soluzione potrebbe includere livelli aggiuntivi, ad esempio un livello Web in Azure. Per altre informazioni, vedere le sezioni seguenti.

![Panoramica della soluzione Azure OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>Limiti di anteprima

* La connettività tra cloud in versione di anteprima è limitata alle aree degli Stati Uniti orientali di Azure (eastus) & Regno Unito meridionale (uksouth) e di Ashburn (Stati Uniti orientali) & Londra (Regno Unito meridionale). Per Regno Unito meridionale, usare il dominio di disponibilità 1 (AD 1) in OCI quando si distribuisce l'interconnessione per le latenze più basse.

## <a name="networking"></a>Rete

I clienti aziendali spesso scelgono di diversificare e distribuire carichi di lavoro su più cloud per diversi motivi aziendali e operativi. Per diversificare, i clienti interconnettono le reti cloud tramite Internet, VPN IPSec o usando la soluzione di connettività diretta del provider di servizi cloud tramite la rete locale. L'interconnessione di reti cloud può richiedere investimenti significativi in termini di tempo, denaro, progettazione, approvvigionamento, installazione, test e operazioni. 

Per rispondere a queste esigenze dei clienti, Oracle e Microsoft hanno consentito un'esperienza integrata di più cloud. La rete tra cloud viene stabilita connettendosi a un circuito [ExpressRoute](../../../expressroute/expressroute-introduction.md) in Microsoft Azure con un circuito [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) in OCI. Questa connettività è possibile quando una località di peering di Azure ExpressRoute si trova in prossimità o nella stessa località di peering del FastConnect OCI. Questa configurazione consente una connettività sicura e veloce tra i due cloud senza la necessità di un provider di servizi intermedio.

Con ExpressRoute e FastConnect, i clienti possono usare il peering di una rete virtuale in Azure con una rete cloud virtuale in OCI, purché lo spazio di indirizzi IP privato non si sovrappongano. Il peering delle due reti consente a una risorsa nella rete virtuale di comunicare con una risorsa nella rete cloud virtuale OCI come se si trovassero nella stessa rete.

## <a name="network-security"></a>Sicurezza di rete

La sicurezza di rete è un componente fondamentale di qualsiasi applicazione aziendale ed è fondamentale per questa soluzione multicloud. Il traffico in corso su ExpressRoute e FastConnect passa attraverso una rete privata. Questa configurazione consente la comunicazione sicura tra una rete virtuale di Azure e una rete cloud virtuale Oracle. Non è necessario fornire un indirizzo IP pubblico ad alcuna macchina virtuale in Azure. Analogamente, non è necessario un gateway Internet in OCI. Tutte le comunicazioni avvengono tramite l'indirizzo IP privato dei computer.

Inoltre, è possibile configurare gli [elenchi di sicurezza](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) nella rete del cloud virtuale OCI e nelle regole di sicurezza (collegate ai [gruppi di sicurezza di rete](../../../virtual-network/security-overview.md)di Azure). Usare queste regole per controllare il flusso di traffico tra i computer nelle reti virtuali. È possibile aggiungere regole di sicurezza di rete a livello di computer, a livello di subnet e a livello di rete virtuale.
 
## <a name="identity"></a>identità

Identity è uno dei principali pilastri della partnership tra Microsoft e Oracle. È stato eseguito un lavoro significativo per integrare [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDC) con [Azure Active Directory](../../../active-directory/index.yml) (Azure ad). Azure AD è il servizio Microsoft di gestione delle identità e degli accessi basato sul cloud. Consente agli utenti di accedere e accedere a diverse risorse. Azure AD consente inoltre di gestire gli utenti e le relative autorizzazioni.

Attualmente questa integrazione consente di gestire in un'unica posizione centrale, che è Azure Active Directory. Azure AD Sincronizza tutte le modifiche apportate alla directory con la directory Oracle corrispondente e viene usata per Single Sign-On per le soluzioni Oracle tra cloud.

## <a name="next-steps"></a>Passaggi successivi

Introduzione a una [rete tra cloud](configure-azure-oci-networking.md) tra Azure e OCI. 

Per ulteriori informazioni e white paper su OCI, vedere la documentazione sul [cloud Oracle](https://docs.cloud.oracle.com/iaas/Content/home.htm) .
