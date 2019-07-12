---
title: Integrare Microsoft Azure con l'infrastruttura Cloud Oracle | Microsoft Docs
description: Informazioni sulle soluzioni che integrano App Oracle in esecuzione in Microsoft Azure con i database nell'infrastruttura Cloud Oracle (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: fcdd46ea60ea53088ffacd7d13693b16a208d527
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707461"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Soluzioni di applicazioni Oracle l'integrazione di Microsoft Azure e dell'infrastruttura Cloud di Oracle (anteprima)

Microsoft e Oracle hanno collaborato per fornire a bassa latenza e la connettività tra cloud a elevata velocità effettiva, consentendo di sfruttare il meglio di entrambi i cloud. 

Usa la connettività tra cloud, è possibile suddividere un'applicazione a più livelli per l'esecuzione in Oracle Cloud dell'infrastruttura (OCI), il livello di database e l'applicazione e gli altri livelli in Microsoft Azure. L'esperienza è simile all'esecuzione di stack dell'intera soluzione in un unico cloud. 

> [!IMPORTANT]
> Questa funzionalità tra cloud è attualmente in anteprima e alcuni [si applicano le limitazioni](#preview-limitations). Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

Se si è interessati alla distribuzione di soluzioni Oracle interamente nell'infrastruttura di Azure, vedere [immagini di VM Oracle e rispettiva distribuzione in Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Panoramica dello scenario

Connettività tra cloud offre una soluzione per eseguire applicazioni leader di settore di Oracle e applicazioni personalizzate, in macchine virtuali di Azure sfruttando al massimo i vantaggi dei servizi di database ospitato in OCI. 

Le applicazioni che è possibile eseguire in una configurazione tra cloud includono:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Applicazioni di vendita al dettaglio Oracle
* Gestione finanziaria di Oracle Hyperion

Il diagramma seguente è una panoramica generale della soluzione connesso. Per semplicità, il diagramma illustra solo un livello di applicazione e un livello dati. A seconda dell'architettura dell'applicazione, la soluzione potrebbe includere livelli aggiuntivi, ad esempio un livello web in Azure. Per altre informazioni, vedere le sezioni seguenti.

![Panoramica della soluzione Azure OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>Limiti di anteprima

* Connettività tra cloud in fase di anteprima è limitata a Ashburn OCI (Stati Uniti-ashburn-1) area e l'area Stati Uniti orientali di Azure (Stati Uniti orientali).

## <a name="networking"></a>Rete

Spesso, i clienti aziendali scelgono di diversificare e distribuire i carichi di lavoro in più aree per varie ragioni operative e la business. Per diversificare, i clienti di interconnessione reti cloud tramite internet, VPN IPSec, o soluzione di connettività diretta del provider del cloud tramite la rete locale. Interconnessione reti cloud può richiedere notevoli investimenti nel tempo, denaro, progettazione, dell'approvvigionamento, installazione, test e operazioni. 

Per risolvere queste problematiche dei clienti, è stata abilitata un'esperienza integrata di multi-cloud Oracle e Microsoft. La rete tra cloud viene stabilita tramite la connessione un' [ExpressRoute](../../../expressroute/expressroute-introduction.md) circuito in Microsoft Azure con un [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) circuito in OCI. La connettività viene possibile in una località di peering ExpressRoute di Azure è nelle vicinanze o nella stessa località di peering di FastConnect OCI. Questa configurazione permette la connessione rapida e sicura tra due cloud senza la necessità di un provider di servizi intermedi.

Con ExpressRoute e FastConnect, i clienti possono il peering di una rete virtuale di Azure con una rete virtuale cloud in OCI, purché lo spazio di indirizzi IP privato non si sovrapponga. Peering di due reti consente a una risorsa nella rete virtuale possono comunicare a una risorsa nella rete virtuale cloud OCI come se sono entrambi nella stessa rete.

## <a name="network-security"></a>Sicurezza di rete

Sicurezza di rete è un componente fondamentale di qualsiasi applicazione aziendale e hanno un'importanza fondamentale per questa soluzione multi-cloud. Qualsiasi superamento FastConnect ed ExpressRoute il traffico passa attraverso una rete privata. Questa configurazione consente la comunicazione sicura tra una rete virtuale di Azure e una rete virtuale cloud Oracle. Non è necessario fornire un indirizzo IP pubblico per tutte le macchine virtuali in Azure. Analogamente, non è necessario un gateway internet di OCI. Tutte le comunicazioni avvengono tramite l'indirizzo IP privato delle macchine.

Inoltre, è possibile configurare [gli elenchi di sicurezza](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) sulle regole di rete e sicurezza del cloud virtuale OCI (collegato ad Azure [gruppi di sicurezza di rete](../../../virtual-network/security-overview.md)). Usare queste regole per controllare il traffico che scorre tra le macchine nelle reti virtuali. Le regole di sicurezza di rete possono essere aggiunti a un livello di computer, a un livello di subnet, nonché a livello di rete virtuale.
 
## <a name="identity"></a>identità

Identity è uno dei concetti fondamentali per i core alla partnership tra Microsoft e Oracle. È stato eseguito un lavoro notevole per integrare [Oracle identità Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) con [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Azure AD è basata sul cloud identità e accesso servizio di gestione di Microsoft. Consente agli utenti l'accesso e accedere alle varie risorse. Azure AD consente inoltre di gestire gli utenti e le relative autorizzazioni.

Attualmente, questa integrazione consente di gestire in un'unica posizione centrale, ovvero Azure Active Directory. Azure AD consente di sincronizzare le modifiche nella directory con il servizio directory Oracle corrispondente e viene usato per l'accesso single sign-on per soluzioni Oracle tra cloud.

## <a name="next-steps"></a>Passaggi successivi

Iniziare con una [rete tra cloud](configure-azure-oci-networking.md) tra Azure e OCI. 

Per altre informazioni e i white paper relativi OCI, vedere la [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) documentazione.
