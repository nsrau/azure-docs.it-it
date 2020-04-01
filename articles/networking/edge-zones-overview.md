---
title: Informazioni sulle aree perimetrali di Azure - Anteprima
description: Scopri le offerte di edge computing di Microsoft.
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 03/31/2020
ms.author: ganesr
ms.openlocfilehash: 4666022fc62fcb423906b46f0ff2274a2191f341
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437618"
---
# <a name="about-azure-edge-zones---preview"></a>Informazioni sulle aree perimetrali di Azure - Anteprima

Le zone perimetrali di Azure sono una famiglia di offerte di Microsoft Azure che consente l'elaborazione dei dati vicino all'utente. È possibile distribuire macchine virtuali, contenitori e altri servizi di Azure selezionati nelle aree perimetrali per soddisfare i requisiti di bassa latenza e velocità effettiva elevata delle applicazioni.

Gli scenari tipici dei casi d'uso per le zone perimetrali includono:Typical use case scenarios for Edge zones include:

- Comando e controllo in tempo reale nella robotica
- Analisi e inferenza in tempo reale con Intelligenza Artificiale e Machine Learning
- Visione artificiale
- Rendering remoto per scenari di realtà mista e VDI
- Gioco multigiocatore immersivo
- Streaming multimediale e distribuzione dei contenuti
- Sorveglianza e sicurezza

Le zone perimetrali di Azure sono disponibili in tre offerte discrete:Azure Edge zones come in three discrete offerings:

- Aree perimetrali di AzureAzure Edge zones
- Azure Edge Zones with Carrier
- Aree perimetrali private di AzureAzure Private Edge zones

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Aree perimetrali di AzureAzure Edge zones

![Aree perimetrali](./media/edge-zones-overview/edge-zones.png "Aree perimetrali")

Le zone perimetrali di Azure sono estensioni con footprint ridotto di Azure collocate in centri di popolamento che si trovano lontano dalle aree di Azure.Azure Edge zones small footprint extensions of Azure placed in population centers that are distant from Azure regions. Le aree perimetrali di Azure supportano macchine virtuali, contenitori e un set selezionato di servizi di Azure che consentono di eseguire applicazioni sensibili alla latenza e con utilizzo intensivo della velocità effettiva vicino agli utenti finali. Le aree perimetrali di Azure fanno parte della rete globale Microsoft e offrono connettività sicura, affidabile e a larghezza di banda elevata tra le applicazioni in esecuzione nella zona perimetrale vicino all'utente e il set completo di servizi di Azure in esecuzione all'interno delle aree di Azure. Le zone perimetrali di Azure sono di proprietà e gestite da Microsoft e consentono di usare lo stesso set di strumenti e portale di Azure per gestire e distribuire i servizi nelle aree perimetrali.

I casi d'uso tipici includono:

- Streaming di giochi e giochi
- Streaming multimediale e distribuzione dei contenuti
- Analisi e inferenza in tempo reale utilizzando l'intelligenza artificiale e l'apprendimento automatico
- Rendering per realtà mista

Le zone perimetrali di Azure saranno disponibili nelle aree seguenti:

- New York, NY
- Los Angeles, CA
- Miami, FL

Per ulteriori informazioni, [contattare il team delle zone](https://aka.ms/EdgeZones) perimetrali.

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>Azure Edge Zones with Carrier

![Aree perimetrali con vettore](./media/edge-zones-overview/edge-carrier.png "Aree perimetrali con vettore")

Le zone perimetrali di Azure con vettore sono estensioni con footprint ridotto di Azure posizionate nei data center degli operatori di telefonia mobile nei centri abitati. Le zone perimetrali di Azure con infrastruttura dell'operatore dell'operatore di telefonia continua vengono posizionate a un salto dalla rete 5G dell'operatore di telefonia mobile, offrendo una latenza inferiore a 10 millisecondi alle applicazioni dei dispositivi mobili. Le zone perimetrali di Azure con l'operatore vengono distribuite nei data center degli operatori di telefonia mobile e sono connesse alla rete globale Microsoft. Offrono connettività sicura, affidabile e a larghezza di banda elevata tra le applicazioni in esecuzione vicino all'utente e il set completo di servizi di Azure in esecuzione all'interno delle aree di Azure.They offer secure, reliable, and high-bandwidth connectivity between applications running at the user and the full set of Azure services running within Azure regions. Gli sviluppatori possono usare lo stesso set di strumenti familiari per compilare e distribuire servizi nelle aree perimetrali.

I casi d'uso tipici includono:

- Streaming di giochi e giochi
- Streaming multimediale e distribuzione dei contenuti
- Analisi e inferenza in tempo reale utilizzando l'intelligenza artificiale e l'apprendimento automatico
- Rendering per realtà mista
- Automobili connesse
- Telemedicina

Le zone perimetrali saranno offerte in collaborazione con i seguenti operatori:

- AT&T (Atlanta, Dallas e Los Angeles)
- Etisalat
- Rogers
- Vodafone

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Aree perimetrali private di AzureAzure Private Edge zones

![Aree perimetrali private](./media/edge-zones-overview/private-edge.png "Aree perimetrali private")

Le zone perimetrali private di Azure sono estensioni con footprint ridotto di Azure posizionate in locale. Le zone perimetrali private di Azure sono basate sulla piattaforma [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) e consentono l'accesso a bassa latenza ai servizi di elaborazione e archiviazione distribuiti in locale. Le aree perimetrali private consentono inoltre di distribuire funzioni di rete virtualizzate (VNF), ad esempio core di pacchetti mobili, router, firewall e appliance SD-WAN, e applicazioni da ISVS come [applicazioni gestite](https://azure.microsoft.com/services/managed-applications/) da Azure side-by-side insieme a macchine virtuali e contenitori in locale. Le zone perimetrali private di Azure sono dotate di una soluzione di orchestrazione nativa del cloud che consente di gestire il ciclo di vita delle funzioni di rete virtualizzate (VNF) e delle applicazioni dal portale di Azure.Azure Private Edge zones comes with a cloud-native orchestration solution that lets you manage the lifecycle of Virtualized Network Functions (VNF) and applications from the Azure portal.

Azure Private Edge Zones lets you develop and deploy applications on-premises using the same familiar tools used to build and deploy applications in Azure. È anche possibile eseguire reti mobili private (LTE privato, 5G privato), funzioni di sicurezza come i firewall ed estendere le reti locali su più filiali e Azure usando le appliance SD-WAN nelle stesse appliance della zona perimetrale privata e gestirle da Azure.

I casi d'uso tipici includono:

- Comando e controllo in tempo reale nella robotica
- Analisi e inferenza in tempo reale con Intelligenza Artificiale e Machine Learning
- Visione artificiale
- Rendering remoto per scenari di realtà mista e VDI
- Sorveglianza e sicurezza

Disponiamo di un ricco ecosistema di fornitori VNF, ISV e partner MSP per consentire soluzioni end-to-end che utilizzano zone perimetrali private. Per ulteriori informazioni, [contattare il team delle zone perimetrali private.](https://aka.ms/EdgeZonesPartner)

## <a name="private-edge-zones---partners"></a><a name="private-edge-partners"></a>Aree perimetrali private - Partner

![Partner della zona perimetrale privata](./media/edge-zones-overview/partners.png "Partner della zona perimetrale privata")

### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>Funzioni di rete virtualizzate (VNF)Virtualized Network Functions (VNFs)

#### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>Virtualized Evolved Packet Core (vEPC) per reti mobili

- [Reti affermate](https://www.affirmednetworks.com/)
- [Software Druido](https://www.druidsoftware.com/)
- [Expeto](https://www.expeto.io/)
- [Mavenir](https://mavenir.com/)
- [Metaswitch](https://www.metaswitch.com/)
- [Nokia Digital Automation Cloud](https://www.dac.nokia.com/)

#### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>Partner radio mobili

- [Commscopio Ruckus](https://support.ruckuswireless.com/)

#### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>Fornitori SD-WAN

- [NetFoundry](https://netfoundry.io/)
- [NuageNetworks di Nokia](https://www.nuagenetworks.net/)
- [VMware SD-WAN di Velocloud](https://www.velocloud.com/)

#### <a name="router-vendors"></a><a name="router-vendors"></a>Fornitori di router

- [Arista](https://www.arista.com/)

[Contatta il team delle zone perimetrali private](https://aka.ms/EdgeZonesPartner) per ulteriori informazioni su come diventare un partner.

#### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>Fornitori di firewall

- Palo Alto Networks

### <a name="managed-solutions-providers---mobile-operators-and-global-system-integrators"></a><a name="msp-mobile"></a>Provider di soluzioni gestite - Operatori di telefonia mobile e integratori di sistemi globali

| Operatori e SU globali | Operatori di telefonia mobile |
| --- | --- |
| Amdocs                       | Etisalat             |
| Torre Americana               | Rogers               |
| Expeto                       | Singtel              |
| Wireless federato           | Vodafone             |
| Infosys                      |      *                |
| Tech Mahindra                |      *                |

[Contatta il team delle zone perimetrali private](https://aka.ms/EdgeZonesPartner) per ulteriori informazioni su come diventare un partner.

## <a name="private-edge-zones---solutions"></a><a name="solutions-private-edge"></a>Aree perimetrali private - Soluzioni

### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>Rete mobile privata in aree perimetrali private

![Rete mobile privata in aree perimetrali private](./media/edge-zones-overview/mobile-networks.png "Rete mobile privata in aree perimetrali private")

È ora possibile distribuire una rete mobile privata in aree perimetrali private. Le reti mobili private consentono una latenza estremamente bassa, un'elevata capacità e una rete wireless affidabile e sicura necessaria per le applicazioni aziendali critiche. Le reti mobili private possono abilitare scenari come il comando e il controllo di veicoli guidati automatizzati (AGV) in un magazzino, la comunicazione in tempo reale tra i robot in una fabbrica intelligente e una realtà aumentata e applicazioni di realtà virtuale.

La funzione di rete virtualizzata evoluta packet core (vEPC) forma il cervello di una rete mobile privata. È ora possibile distribuire un vEPC nelle zone perimetrali private. Per un elenco dei partner vEPC disponibili nelle zone perimetrali private, vedere [ISV vEPC](#vEPC).

La distribuzione di una soluzione di rete mobile privata nelle aree perimetrali private richiede altri componenti, ad esempio punti di accesso mobile, schede SIM e altri VNF, ad esempio i router. L'accesso allo spettro concesso in licenza o senza licenza è fondamentale per la creazione di una rete mobile privata. Inoltre, potrebbe essere necessaria assistenza per la pianificazione RF, il layout fisico, l'installazione e il supporto. Per un elenco dei partner, consultate [Partner radio mobili.](#mobile-radio)

Microsoft provides a partner ecosystem that can help with all aspects of this process – from planning the network, purchasing the required devices, setting up hardware, to managing the configuration from Azure. Con un set di partner convalidati che sono strettamente integrati con Microsoft, si può essere certi che la soluzione sarà affidabile e facile da usare. Puoi concentrarti sui tuoi scenari principali, facendo affidamento su Microsoft e sui suoi partner per aiutare con il resto.

### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>SD-WAN in aree perimetrali private

![SD-WAN in aree perimetrali private](./media/edge-zones-overview/sd-wan.png "SD-WAN in aree perimetrali private")
 
SD-WAN come tecnologia consente di creare reti WAN (Wide Area Network) di livello enterprise con maggiore larghezza di banda, accesso ad alte prestazioni al cloud, inserimento dei servizi, affidabilità, gestione dei criteri e ampia visibilità della rete. SD-WAN offre una connettività senza soluzione di continuità nelle succursali orchestrata da controller centrali ridondanti, a costi di proprietà inferiori.
SD-WAN su zone perimetrali private consente di passare dal modello capex-centric o a un modello SaaS (Software-as-a-Service) per ridurre i budget IT. È possibile utilizzare l'agente di orchestrazione o il controller dei partner SD-WAN per abilitare immediatamente nuovi servizi e propagarli in tutta la rete.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, contatta i seguenti team:

* [Team delle zone perimetrali](https://aka.ms/EdgeZones)
* [Team di edge zone private - per diventare un partner](https://aka.ms/EdgeZonesPartner)
