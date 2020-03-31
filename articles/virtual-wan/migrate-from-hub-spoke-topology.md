---
title: 'Architettura: eseguire la migrazione alla rete WAN virtuale di AzureArchitecture: Migrate to Azure Virtual WAN'
description: Informazioni su come eseguire la migrazione alla rete WAN virtuale di Azure.Learn how to migrate to Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8aa4fe143c78d2053ce8c48e4866a5522057aa0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063021"
---
# <a name="migrate-to-azure-virtual-wan"></a>Eseguire la migrazione alla rete WAN virtuale di Azure

La rete WAN virtuale di Azure consente alle aziende di semplificare la connettività globale per sfruttare la scalabilità della rete globale Microsoft.Azure Virtual WAN lets companies simplify their global connectivity in order to benefit from the scale of the Microsoft global network. In questo articolo vengono forniti dettagli tecnici per le aziende che desiderano eseguire la migrazione da una topologia hub-and-spoke gestita dal cliente esistente a una progettazione che sfrutta gli hub WAN virtuali gestiti da Microsoft.

Per informazioni sui vantaggi offerti dalla rete WAN virtuale di Azure per le aziende che adottano una rete globale aziendale moderna incentrata sul cloud, vedere Architettura della rete di [transito globale e WAN virtuale](virtual-wan-global-transit-network-architecture.md).

![hub e](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
**figura spoke: RETE virtuale di Azure**

Il modello di connettività hub-and-spoke di Azure Virtual Datacenter (VDC) è stato adottato da migliaia di clienti per sfruttare il comportamento di routing transitivo predefinito di Rete di Azure per creare reti cloud semplici e scalabili. La rete WAN virtuale di Azure si basa su questi concetti e introduce nuove funzionalità che consentono topologie di connettività globali, non solo tra sedi locali e Azure, ma anche consentendo ai clienti di sfruttare la scalabilità della rete Microsoft per reti globali esistenti.

In questo articolo viene illustrato come eseguire la migrazione di un ambiente ibrido esistente alla rete WAN virtuale.

## <a name="scenario"></a>Scenario

Contoso è un'organizzazione finanziaria globale con uffici sia in Europa che in Asia. La società pianifica di spostare le applicazioni esistenti da un data center locale in Azure e progetta una soluzione iniziale basata sull'architettura di data center virtuale, che include reti virtuali locali hub gestite dal cliente per la connettività ibrida. Nell'ambito del passaggio alle tecnologie basate su cloud, il team di rete ha il compito di garantire che la connettività sia ottimizzata per l'azienda.

Nella figura seguente viene illustrata una visualizzazione generale della rete globale esistente, inclusa la connettività a più aree di Azure.The following figure shows a high-level view of the existing global network including connectivity to multiple Azure regions.

![Contoso esistente topology](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
**Figura: Contoso topologia di rete esistente**

La topologia di rete esistente presenta le caratteristiche seguenti:

- Una topologia hub-spoke viene usata in più aree, inclusi i circuiti ExpressRoute Premium per la connettività a una rete WAN privata comune.

- In alcuni di questi siti sono anche presenti tunnel VPN collegati direttamente ad Azure per raggiungere le applicazioni ospitate nel cloud Microsoft.

## <a name="requirements"></a>Requisiti

Il team di rete viene incaricato di sviluppare un modello di rete globale in grado di supportare la migrazione di Contoso al cloud e che sia ottimizzato in termini di costi, scalabilità e prestazioni. In breve, devono essere soddisfatti i requisiti seguenti:

- Fornire nelle sedi centrali e nelle filiali un percorso ottimizzato verso le applicazioni ospitate nel cloud.
- Rimuovere la dipendenza dai data center locali esistenti per la terminazione VPN mantenendo i percorsi di connettività seguenti:
  - **Succursale -to- VNet:** gli uffici connessi VPN devono essere in grado di accedere alle applicazioni migrate nel cloud nell'area di Azure locale.
  - **Branch -to- Hub -to- Hub -to- VNet:** gli uffici connessi VPN devono essere in grado di accedere alle applicazioni migrate nel cloud nell'area remota di Azure.
  - **Filiale -to- succursale:** gli uffici connessi VPN regionali devono essere in grado di comunicare tra loro e i siti del controller di dominio centrale connesso ExpressRoute.
  - **Filiale -to- Hub -to- Hub -to- branch:** gli uffici connessi VPN separati a livello globale devono essere in grado di comunicare tra loro e tutti i siti del server di controllo della rete connesso con ExpressRoute.
  - **Succursale -a- Siti**connessi devono essere in grado di comunicare con Internet. Questo traffico deve essere filtrato e registrato.
  - **VNet -to- VNet**: Le reti virtuali spoke nella stessa area devono essere in grado di comunicare tra loro.
  - **VNet -to- Hub -to- Hub -to- VNet**: Le reti virtuali spoke nelle diverse aree devono essere in grado di comunicare tra loro.
- Offrire agli utenti mobili di Contoso (laptop e telefono) di accedere alle risorse aziendali senza alcuna rete aziendale.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Architettura della rete WAN virtuale di AzureAzure Virtual WAN architecture

Nella figura seguente viene illustrata una visualizzazione generale della topologia di destinazione aggiornata che usa la rete WAN virtuale di Azure per soddisfare i requisiti descritti nella sezione precedente.

![Architettura](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
della WAN virtuale Contoso**Figura: Architettura WAN virtuale di Azure**

Riepilogo:

- La sede centrale in Europa rimane connessa a ExpressRoute, i controller di dominio locali in Europa vengono completamente trasferiti in Azure e quindi rimossi.
- I controller di dominio e la sede centrale in Asia rimangono connessi alla rete WAN privata. La rete WAN virtuale di Azure viene ora usata per aumentare la rete dell'operatore locale e fornire connettività globale.
- Hub della rete WAN virtuale di Azure distribuiti nelle aree di Azure dell'Europa occidentale e del Sud Est asiatico per fornire l'hub di connettività per expressRoute e i dispositivi connessi VPN.
- Gli hub forniscono anche la terminazione VPN per gli utenti in roaming tra più tipi di client tramite connettività OpenVPN alla rete a mesh globale, consentendo l'accesso non solo alle applicazioni trasferite in Azure ma anche alle risorse rimaste nell'ambiente locale.
- Connettività Internet per le risorse interne a una rete virtuale fornita dalla rete WAN virtuale di Azure.

Anche la connettività Internet per i siti remoti è fornita dalla rete WAN virtuale di Azure. Breakout Internet locale supportato tramite l'integrazione di partner per l'accesso ottimizzato a servizi SaaS come Office 365.

## <a name="migrate-to-virtual-wan"></a>Eseguire la migrazione alla rete WAN virtuale

In questa sezione vengono illustrati i vari passaggi per la migrazione alla rete WAN virtuale di Azure.This section shows the various steps for migrating to Azure Virtual WAN.

### <a name="step-1-vdc-hub-and-spoke-single-region"></a>Passaggio 1: Area hub-and-spoke VDC

Esaminare l'architettura. The following figure shows a single region topology for Contoso prior to the rollout of Azure Virtual WAN:

![Topologia](./media/migrate-from-hub-spoke-topology/figure1.png)
a area singola**Figura 1: singola area hub e spoke VDC**

In linea con l'approccio Virtual Data Center (VDC), la rete virtuale hub gestito dal cliente contiene diversi blocchi funzionali:

- Servizi condivisi (qualsiasi funzione comune richiesta da più spoke). Esempio: Contoso utilizza i controller di dominio di Windows Server nelle macchine virtuali IaaS (Infrastructure-as-a-Service).
- I servizi firewall di routing/IP sono forniti da un'appliance virtuale di rete di terze parti, abilitando il routing IP di livello 3 spoke-spoke.
- Servizi in ingresso/uscita Internet, tra cui il gateway applicazione di Azure per le richieste HTTPS in ingresso e servizi proxy di terze parti in esecuzione in macchine virtuali per l'accesso in uscita filtrato alle risorse Internet.
- Gateway di rete virtuale ExpressRoute e VPN per la connettività alle reti locali.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Passaggio 2: Distribuire hub WAN virtualiStep 2: Deploy Virtual WAN hubs

Distribuire un hub WAN virtuale in ogni area. Configurare l'hub WAN virtuale con gateway VPN e gateway ExpressRoute come descritto negli articoli seguenti:Set up the Virtual WAN hub with VPN Gateway and ExpressRoute Gateway as described in the following articles:

- [Esercitazione: Creare una connessione da sito a sito con la rete WAN virtuale di Azure](virtual-wan-site-to-site-portal.md)
- [Esercitazione: Creare un'associazione ExpressRoute usando la rete WAN virtuale di AzureTutorial: Create an ExpressRoute association using Azure Virtual WAN](virtual-wan-expressroute-portal.md)

> [!NOTE]
> La rete WAN virtuale di Azure deve usare lo SKU Standard per abilitare alcuni dei percorsi del traffico illustrati in questo articolo.

![Deploy Virtual WAN](./media/migrate-from-hub-spoke-topology/figure2.png)
hubs**Figura 2: migrazione da hub e spoke VDC a WAN virtuale**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Passaggio 3: Connettere siti remoti (ExpressRoute e VPN) alla rete WAN virtualeStep 3: Connect remote sites (ExpressRoute and VPN) to Virtual WAN

Connettere l'hub WAN virtuale ai circuiti ExpressRoute esistenti e configurare le VPN da sito a sito tramite Internet a qualsiasi filiale remota.

> [!NOTE]
> I circuiti ExpressRoute devono essere aggiornati al tipo di SKU Premium per connetterli all'hub della rete WAN virtuale.

![Connettere i siti](./media/migrate-from-hub-spoke-topology/figure3.png)
remoti alla figura 3 della rete WAN**virtuale: migrazione hub-and-spoke VDC alla rete WAN virtuale**

A questo punto, le apparecchiature di rete locali inizieranno a ricevere route che riflettono lo spazio di indirizzi IP assegnato alla rete virtuale dell'hub gestito dalla rete WAN virtuale. In questa fase le filiali remote connesse alla VPN vedranno due percorsi alle applicazioni esistenti nelle reti virtuali spoke. Questi dispositivi devono essere configurati per continuare a usare il tunnel verso l'hub del data center virtuale per assicurare il routing simmetrico durante la fase di transizione.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Passaggio 4: Testare la connettività ibrida tramite la rete WAN virtualeStep 4: Test hybrid connectivity via Virtual WAN

Prima di utilizzare l'hub WAN virtuale gestito per la connettività di produzione, è consigliabile configurare una rete virtuale di test spoke e una connessione VNet WAN virtuale. Verificare che le connessioni a questo ambiente di test funzionino tramite ExpressRoute e VPN da sito a sito prima di continuare con i passaggi successivi.

![Testare la connettività ibrida tramite Virtual WAN](./media/migrate-from-hub-spoke-topology/figure4.png)
Figura**4: migrazione tra hub e spoke VDC e WAN virtuale**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Passaggio 5: Transizione della connettività all'hub WAN virtualeStep 5: Transition connectivity to virtual WAN hub

![Connettività di transizione](./media/migrate-from-hub-spoke-topology/figure5.png)
all'hub WAN virtuale**Figura 5: migrazione hub-e spoke VDC a WAN virtuale**

**a .** Eliminare le connessioni di peering esistenti dalle reti virtuali Spoke all'hub VDC precedente. L'accesso alle applicazioni nelle reti virtuali spoke non è disponibile finché non vengono completati i passaggi a-c.

**b**. Connettere le reti virtuali spoke all'hub WAN virtuale tramite connessioni VNet.

**c**. Rimuovere le route definite dall'utente usate in precedenza nelle reti virtuali spoke per le comunicazioni spoke-spoke. Questo percorso è ora abilitato dal routing dinamico disponibile nell'hub della rete WAN virtuale.

**d**. I gateway VPN ed ExpressRoute esistenti nell'hub del data center virtuale vengono ora rimossi per consentire il passaggio successivo (e).

**e**. Connettere l'hub del data center virtuale (rete virtuale hub) precedente all'hub della rete WAN virtuale tramite una nuova connessione di rete virtuale.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Passaggio 6: L'hub precedente diventa un servizio condiviso

La rete di Azure è ora stata riprogettata in modo da configurare l'hub della rete WAN virtuale come punto centrale nella nuova topologia.

![Vecchio hub diventa](./media/migrate-from-hub-spoke-topology/figure6.png)
Shared Services spoke**Figura 6: VDC hub-and-spoke alla migrazione WAN virtuale**

Poiché l'hub WAN virtuale è un'entità gestita e non consente la distribuzione di risorse personalizzate, ad esempio macchine virtuali, il blocco dei servizi condivisi ora esiste come rete virtuale spoke e ospita funzioni quali l'ingresso di Internet tramite il gateway applicazione di Azure o appliance virtualizzata di rete. Il traffico tra l'ambiente di servizi condivisi e le macchine virtuali back-end ora transita attraverso l'hub gestito dalla rete WAN virtuale.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Passaggio 7: Ottimizzare la connettività locale per utilizzare completamente la rete WAN virtualeStep 7: Optimize on-premises connectivity to fully utilize Virtual WAN

In questa fase, Contoso ha quasi completato la migrazione delle applicazioni aziendali al cloud Microsoft, con solo alcune applicazioni legacy rimanenti all'interno del controller di dominio locale.

![Ottimizzare la connettività locale per](./media/migrate-from-hub-spoke-topology/figure7.png)
utilizzare appieno la figura 7 della wan**virtuale: migrazione hub e spoke per la migrazione della WAN virtuale VDC**

Per sfruttare tutte le funzionalità della rete WAN virtuale di Azure, Contoso decide di rimuovere le connessioni VPN locali legacy. Le filiali che continuano ad accedere alle reti di sedi centrali o data center possono transitare nella rete globale Microsoft tramite il routing di transito predefinito della rete WAN virtuale di Azure.

> [!NOTE]
> Il servizio Copertura globale di ExpressRoute rappresenta un'alternativa per i clienti che scelgono di sfruttare il backbone Microsoft per integrare le loro reti WAN private esistenti.

## <a name="end-state-architecture-and-traffic-paths"></a>Architettura dello stato finale e percorsi di trafficoEnd-state architecture and traffic paths

![Architettura dello stato finale](./media/migrate-from-hub-spoke-topology/figure8.png)
e percorsi di traffico**Figura: WAN virtuale a doppia area**

Questa sezione include alcuni flussi di traffico di esempio per dimostrare come questa topologia soddisfa i requisiti originali.

### <a name="path-1"></a>Percorso 1

Il percorso 1 mostra il flusso di traffico da un ramo connesso VPN S2S in Asia a una rete virtuale di Azure nell'area del sud-est asiatico.

Il traffico viene instradato come segue:

- La filiale Asia è connessa tramite tunnel BGP S2S resilienti nell'hub WAN virtuale dell'Asia sudorientale.

- L'hub della rete WAN virtuale in Asia instrada il traffico in locale alla rete virtuale connessa.

![Flusso 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Percorso 2

Il percorso 2 mostra il flusso di traffico dalla sede centrale europea connessa ExpressRoute a una rete virtuale di Azure nell'area asia sudorientale.

Il traffico viene instradato come segue:

- La sede centrale europea è connessa tramite un circuito ExpressRoute premium nell'hub WAN virtuale dell'Europa occidentale.

- La connettività globale da hub a hub della rete WAN virtuale garantisce il transito del traffico verso la rete virtuale connessa nell'area remota.

![Flusso 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Percorso 3

Il percorso 3 mostra il flusso di traffico dal controller di dominio locale dell'Asia connesso alla rete WAN privata a un ramo connesso S2S europeo.

Il traffico viene instradato come segue:

- Il controller di dominio in Asia è connesso all'operatore della rete WAN privata locale.

- Il circuito ExpressRoute termina localmente nella rete WAN privata si connette all'hub WAN virtuale dell'Asia sudorientale.

- La connettività globale hub-hub della rete WAN virtuale consente il transito del traffico.

![Flusso 3](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>Percorso 4

Il percorso 4 mostra il flusso di traffico da una rete virtuale di Azure nell'area del Sud-Est asiatico a una rete virtuale di Azure nell'area dell'Europa occidentale.

Il traffico viene instradato come segue:

- La connettività globale da hub a hub della rete WAN virtuale consente il transito nativo di tutte le reti virtuali di Azure connesse senza ulteriori configurazioni dell'utente.

![Flusso 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Percorso 5

Il percorso 5 mostra il flusso di traffico dagli utenti VPN mobili (P2S) a una rete virtuale di Azure nell'area dell'Europa occidentale.

Il traffico viene instradato come segue:

- Gli utenti di laptop e dispositivi mobili utilizzano il client OpenVPN per una connettività trasparente nel gateway VPN P2S in Europa occidentale.

- L'hub della rete WAN virtuale in Europa occidentale instrada il traffico in locale alla rete virtuale connessa.

![Flusso 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Controllo di sicurezza e criteri tramite Firewall di Azure

Contoso ha ora convalidato la connettività tra tutti i rami e le reti virtuali in linea con i requisiti descritti in precedenza in questo articolo. Per soddisfare i requisiti per il controllo della sicurezza e l'isolamento della rete, è necessario continuare a separare e registrare il traffico tramite la rete hub. In precedenza questa funzione veniva eseguita da un'appliance virtuale di rete (NVA). Contoso desidera inoltre rimuovere le autorizzazioni per i servizi proxy esistenti e utilizzare i servizi nativi di Azure per il filtro Internet in uscita.

![Controllo di sicurezza e](./media/migrate-from-hub-spoke-topology/security-policy.png)
criteri tramite La figura del firewall di Azure: Firewall di Azure nella rete WAN virtuale (hub virtuale protetto)Security and policy control via Azure Firewall**Figure: Azure Firewall in Virtual WAN (Secured Virtual hub)**

I passaggi di alto livello seguenti sono necessari per introdurre Firewall di Azure negli hub WAN virtuale per abilitare un punto unificato del controllo dei criteri. Per altre informazioni su questo processo e sul concetto di Hub virtuali protetti, vedere Gestione firewall di Azure.For more information about this process and the concept of Secure Virtual Hubs, see [Azure Firewall Manager](../firewall-manager/index.yml).

1. Creare i criteri di Firewall di Azure.
2. Collegare i criteri firewall all'hub della rete WAN virtuale di Azure. Questo passaggio consente all'hub WAN virtuale esistente di funzionare come hub virtuale protetto e distribuisce le risorse di Firewall di Azure necessarie.

> [!NOTE]
> Se il firewall di Azure viene distribuito in un hub WAN virtuale standard (SKU: Standard): i criteri V2V, B2V, V2I e B2I FW vengono applicati solo al traffico proveniente dalle reti virtuali e dai rami connessi all'hub specifico in cui è distribuito Azure FW (Hub protetto). Il traffico proveniente da reti virtuali remote e filiali collegate ad altri hub WAN virtuali nella stessa WAN virtuale non verrà "firewallizzato", anche se le diramazioni remote e la rete virtuale sono interconnesse tramite hub WAN virtuale ai collegamenti hub. Il supporto firewall per più hub è nella roadmap di Azure Virtual WAN e Firewall Manager.Cross-hub firewalling support is on the Azure Virtual WAN and Firewall Manager roadmap.

I percorsi seguenti mostrano i percorsi di connettività abilitati tramite hub virtuali protetti di Azure:The following paths show the connectivity paths enabled by using Azure secured virtual hubs:

### <a name="path-6"></a>Percorso 6

Il percorso 6 mostra il flusso di traffico protetto tra le reti virtuali all'interno della stessa area.

Il traffico viene instradato come segue:

- Le reti virtuali connesse allo stesso hub virtuale protetto ora instradano il traffico tramite Firewall di Azure.

- Firewall di Azure può applicare i criteri a questi flussi.

![Flusso 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Percorso 7

Il percorso 7 mostra il flusso di traffico da una rete virtuale di Azure a Internet o al servizio di sicurezza di terze parti.

Il traffico viene instradato come segue:

- Le reti virtuali connesse all'hub virtuale protetto possono inviare il traffico a destinazioni pubbliche su Internet, usando l'hub protetto come punto centrale di accesso a Internet.

- Questo traffico può essere filtrato localmente usando le regole FQDN di Firewall di Azure o inviato a un servizio di sicurezza di terze parti per l'ispezione.

![Flusso 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Percorso 8

Il percorso 8 mostra il flusso di traffico da un ramo a Internet o da un servizio di sicurezza di terze parti.

Il traffico viene instradato come segue:

- Le succursali connesse all'hub virtuale protetto possono inviare traffico a destinazioni pubbliche su Internet utilizzando l'hub protetto come punto centrale di accesso a Internet.

- Questo traffico può essere filtrato localmente usando le regole FQDN di Firewall di Azure o inviato a un servizio di sicurezza di terze parti per l'ispezione.

![Flusso 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [rete WAN virtuale di Azure](virtual-wan-about.md)
