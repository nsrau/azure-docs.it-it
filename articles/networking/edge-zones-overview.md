---
title: Informazioni sull'anteprima dell'area perimetrale di Azure
description: 'Informazioni sulle offerte di calcolo Edge da Microsoft: area perimetrale di Azure.'
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 07/07/2020
ms.author: ganesr
ms.openlocfilehash: be113fec596d4730d55403b6ce4dbd6d2709e273
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260070"
---
# <a name="about-azure-edge-zone-preview"></a>Informazioni sull'anteprima dell'area perimetrale di Azure

Area perimetrale di Azure è una famiglia di offerte da Microsoft Azure che consente l'elaborazione dei dati vicino all'utente. È possibile distribuire macchine virtuali, contenitori e altri servizi di Azure selezionati in zone perimetrali per risolvere i requisiti di bassa latenza e velocità effettiva elevata delle applicazioni.

Gli scenari di casi d'uso tipici per le zone perimetrali includono:

- Comando e controllo in tempo reale in robotica.
- Analisi in tempo reale e inferenza tramite intelligenza artificiale e machine learning.
- Visione artificiale.
- Rendering remoto per scenari di realtà mista e VDI.
- Giochi multiplayer immersivi.
- Streaming multimediale e distribuzione di contenuti.
- Sorveglianza e sicurezza.

Sono disponibili tre tipi di zone perimetrali di Azure:

- Zone perimetrali di Azure
- Zone perimetrali di Azure con Carrier
- Zone perimetrali private di Azure

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Zone perimetrali di Azure

![Zone perimetrali di Azure](./media/edge-zones-overview/edge-zones.png "Zone perimetrali di Azure")

Le zone perimetrali di Azure sono estensioni con footprint ridotto di Azure situate in centri di popolazione lontani dalle aree di Azure. Le zone perimetrali di Azure supportano macchine virtuali, contenitori e un set selezionato di servizi di Azure che consentono di eseguire applicazioni dipendenti dalla latenza e a elevato utilizzo di velocità effettiva vicino agli utenti finali. Le zone perimetrali di Azure fanno parte della rete globale di Microsoft. Forniscono connettività sicura, affidabile e a larghezza di banda elevata tra le applicazioni eseguite all'area perimetrale vicina all'utente. E offrono il set completo di servizi di Azure in esecuzione all'interno delle aree di Azure. Le zone di Azure Edge sono di proprietà e gestite da Microsoft. È possibile usare lo stesso set di strumenti di Azure e lo stesso portale per gestire e distribuire i servizi in zone perimetrali.

I casi d'uso tipici includono:

- Streaming di giochi e giochi.
- Streaming multimediale e distribuzione di contenuti.
- Analisi in tempo reale e inferenza tramite intelligenza artificiale e machine learning.
- Rendering per realtà mista.

Le aree di Azure Edge saranno disponibili nelle aree della metro seguenti:

- New York, NY
- Los Angeles, CA
- Miami, FL

Per ulteriori informazioni, [contattare il team dell'area perimetrale](https://aka.ms/EdgeZones) .

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>Zone perimetrali di Azure con Carrier

![Zone perimetrali con vettore](./media/edge-zones-overview/edge-carrier.png "Zone perimetrali con vettore")

Le zone perimetrali di Azure con carrier sono estensioni con footprint ridotto di Azure inserite nei data center degli operatori mobili nei centri di popolazione. L'area di Azure Edge con l'infrastruttura del vettore viene posizionata un hop dalla rete 5G dell'operatore mobile. Questo posizionamento offre una latenza inferiore a 10 millisecondi per le applicazioni dei dispositivi mobili.

Le zone perimetrali di Azure con vettore vengono distribuite nei data center degli operatori mobili e connesse alla rete globale di Microsoft. Forniscono connettività sicura, affidabile e a larghezza di banda elevata tra le applicazioni che vengono eseguite vicino all'utente. E offrono il set completo di servizi di Azure in esecuzione all'interno delle aree di Azure. Gli sviluppatori possono usare lo stesso set di strumenti familiari per creare e distribuire servizi nelle zone perimetrali.

I casi d'uso tipici includono:

- Streaming di giochi e giochi.
- Streaming multimediale e distribuzione di contenuti.
- Analisi in tempo reale e inferenza tramite intelligenza artificiale e machine learning.
- Rendering per realtà mista.
- Automobili connesse.
- Tele-medicine.

Le zone perimetrali verranno offerte in collaborazione con gli operatori seguenti:

- ALLE&T (Atlanta, Dallas e Los Angeles) 

Gli ISV che lavorano su applicazioni ottimizzate e scalabili connesse a reti 5G possono ora usare la nuova posizione di anteprima di Los Angeles per le zone perimetrali di Azure con&T durante la compilazione e la sperimentazione con piattaforme con latenza ultra-bassa, scenari mobili e connessi. Registrati per il programma early adopter per sfruttare la connettività sicura e a larghezza di banda elevata.

Per ulteriori informazioni, [contattare il team dell'area perimetrale](https://aka.ms/EdgeZones) .

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Zone perimetrali private di Azure

![Zone perimetrali private](./media/edge-zones-overview/private-edge.png "Zone perimetrali private")

Le zone perimetrali private di Azure sono estensioni con footprint ridotto di Azure inserite in locale. La zona Edge privata di Azure è basata sulla piattaforma [Azure stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) . Consente l'accesso a bassa latenza ai servizi di calcolo e di archiviazione distribuiti in locale. La zona Edge privata consente inoltre di distribuire le applicazioni da ISV e funzioni di rete virtualizzate (VNFs) come [applicazioni gestite di Azure](https://azure.microsoft.com/services/managed-applications/) insieme alle macchine virtuali e ai contenitori in locale. Questi VNFs possono includere i core dei pacchetti mobili, i router, i firewall e le appliance SD-WAN. La zona Edge privata di Azure viene fornita con una soluzione di orchestrazione nativa del cloud che consente di gestire i cicli di vita di VNFs e applicazioni dal portale di Azure.

La zona Edge privata di Azure consente di sviluppare e distribuire applicazioni in locale usando gli stessi strumenti noti usati per compilare e distribuire applicazioni in Azure. 

Consente inoltre di: 

- Eseguire reti mobili private (LTE privato, privato 5G).
- Implementare funzioni di sicurezza come i firewall.
- Estendi le tue reti locali tra più rami e Azure usando Appliance SD-WAN nelle stesse appliance della zona Edge privata e Gestisci da Azure.

I casi d'uso tipici includono:

- Comando e controllo in tempo reale in robotica.
- Analisi in tempo reale e inferenza con l'intelligenza artificiale e l'apprendimento automatico.
- Visione artificiale.
- Rendering remoto per scenari di realtà mista e VDI.
- Sorveglianza e sicurezza.

Abbiamo un ampio ecosistema di fornitori di VNF, ISV e partner MSP per abilitare soluzioni end-to-end che usano zone perimetrali private. Per ulteriori informazioni, [contattare il team dell'area perimetrale privata](https://aka.ms/EdgeZonesPartner) .

### <a name="private-edge-zone-partners"></a><a name="private-edge-partners"></a>Partner zona Edge privati

![Partner zona Edge privati](./media/edge-zones-overview/partners.png "Partner di zone Edge private")

#### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>Funzioni di rete virtualizzate (VNFs)

##### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>VEPC (virtuald Packet Core) per reti mobili

- [Reti affermate](https://www.affirmednetworks.com/)
- [Software Druid](https://www.druidsoftware.com/)
- [Expeto](https://www.expeto.io/)
- [Mavenir](https://mavenir.com/)
- [Metaswitch](https://www.metaswitch.com/)
- [Cloud di automazione digitale Nokia](https://www.dac.nokia.com/)

##### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>Partner radio per dispositivi mobili

- [CommScope](https://support.ruckuswireless.com/)

##### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>Fornitori SD-WAN

- [NetFoundry](https://netfoundry.io/)
- [Reti Nuage da Nokia](https://www.nuagenetworks.net/)
- [VMware SD-WAN di Velocloud](https://www.velocloud.com/)

##### <a name="router-vendors"></a><a name="router-vendors"></a>Fornitori di router

- [Arista](https://www.arista.com/)

##### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>Fornitori di firewall

- [Palo Alto Networks](https://www.paloaltonetworks.com/)

##### <a name="managed-solutions-providers-mobile-operators-and-global-system-integrators-gsis"></a><a name="msp-mobile"></a>Provider di soluzioni gestite: operatori mobili e integratori di sistemi globali (GSIs)

| GSIs e operatori | Operatori di telefonia mobile |
| --- | --- |
| Amdocs                       | Etisalat             |
| Torre americana               | NTT Communications   |
| CenturyLink                  | Proximus             |
| Expeto                       | Rogers               |
| Wireless federato           | Telecomunicazioni SK           |
| Infosys                      | Telefonica           |
| Mahindra Tech                | Telstra              |
|                              | Vodafone             |

Per informazioni su come diventare un partner, [contattare il team di zona Edge privata](https://aka.ms/EdgeZonesPartner) .

### <a name="private-edge-zone-solutions"></a><a name="solutions-private-edge"></a>Soluzioni di zone perimetrali private

#### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>Rete mobile privata in zone perimetrali private

![Rete mobile privata in zone perimetrali private](./media/edge-zones-overview/mobile-networks.png "Rete mobile privata in zone perimetrali private")

È ora possibile distribuire una rete mobile privata in zone perimetrali private. Le reti mobili private consentono una latenza ultra-bassa, una capacità elevata e la rete wireless affidabile e sicura necessaria per le applicazioni aziendali cruciali. 

Le reti mobili private possono abilitare scenari come: 
- Comando e controllo dei veicoli autoguidati (AGV) nei warehouse. 
- Comunicazione in tempo reale tra i robot nelle Smart Factory.
- Realtà aumentata e applicazioni Edge di realtà virtuale.

La funzione di rete vEPC (virtualizzated Packet Core) è il cervello di una rete mobile privata. È ora possibile distribuire un vEPC nelle zone perimetrali private. Per un elenco dei partner vEPC disponibili sulle zone perimetrali private, vedere [ISV vEPC](#vEPC).

Per la distribuzione di una soluzione di rete mobile privata in zone perimetrali private sono necessari altri componenti, ad esempio i punti di accesso mobile, le schede SIM e altri VNFs, ad esempio i router. L'accesso a uno spettro con licenza o senza licenza è essenziale per la configurazione di una rete mobile privata. E potrebbe essere necessario assistenza per la pianificazione di RF, il layout fisico, l'installazione e il supporto. Per un elenco di partner, vedere la pagina relativa ai [partner di Mobile Radio](#mobile-radio).

Microsoft fornisce un ecosistema di partner che può essere utile per tutti gli aspetti di questo processo. I partner possono aiutare a pianificare la rete, acquistare i dispositivi necessari, configurare l'hardware e gestire la configurazione da Azure. Un set di partner convalidati strettamente integrati con Microsoft garantisce che la soluzione sarà affidabile e facile da utilizzare. Puoi concentrarti sugli scenari principali e affidati a Microsoft e ai suoi partner per contribuire al resto.

#### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>SD-WAN nelle zone perimetrali private

![SD-WAN nelle zone perimetrali private](./media/edge-zones-overview/sd-wan.png "SD-WAN nelle zone perimetrali private")
 
SD-WAN consente di creare reti WAN (Wide Area Network) di livello aziendale con i vantaggi seguenti:

- Maggiore larghezza di banda
- Accesso a prestazioni elevate al cloud
- Inserimento del servizio
- Affidabilità
- Gestione dei criteri
- Visibilità estesa della rete
    
SD-WAN offre una connettività senza problemi di succursale orchestrata da controller centrali ridondanti a costi inferiori di proprietà.
SD-WAN nelle zone perimetrali private consente di passare da un modello incentrato su investimenti a un modello SaaS (software-as-a-Service) per ridurre i budget IT. È possibile usare i partner SD, l'agente di orchestrazione o il controller, per abilitare nuovi servizi e propagarli immediatamente in tutta la rete.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, contattare i team seguenti:

* [Team zona perimetrale](https://aka.ms/EdgeZones)
* [Team di zona Edge privata, per diventare un partner](https://aka.ms/EdgeZonesPartner)
