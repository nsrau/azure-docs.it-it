---
title: 'Architettura: eseguire la migrazione alla rete WAN virtuale di Azure'
description: Informazioni su come eseguire la migrazione alla rete WAN virtuale di Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8aa4fe143c78d2053ce8c48e4866a5522057aa0c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063021"
---
# <a name="migrate-to-azure-virtual-wan"></a>Eseguire la migrazione alla rete WAN virtuale di Azure

La rete WAN virtuale di Azure consente alle aziende di semplificare la connettività globale per trarre vantaggio dalla scalabilità della rete globale di Microsoft. Questo articolo fornisce informazioni tecniche per le aziende che desiderano eseguire la migrazione da una topologia hub-spoke gestita dal cliente esistente a una progettazione che sfrutta Hub WAN virtuali gestiti da Microsoft.

Per informazioni sui vantaggi che la rete WAN virtuale di Azure consente alle aziende che adottano una rete globale aziendale moderna, vedere l' [architettura di rete di transito globale e la rete WAN virtuale](virtual-wan-global-transit-network-architecture.md).

![Figura Hub e](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
spoke **: rete WAN virtuale di Azure**

Il modello di connettività Hub e spoke del data center virtuale di Azure (VCC) è stato adottato da migliaia di clienti per sfruttare il comportamento di routing transitivo predefinito della rete di Azure per creare reti cloud semplici e scalabili. La rete WAN virtuale di Azure si basa su questi concetti e introduce nuove funzionalità che consentono le topologie di connettività globali, non solo tra percorsi locali e Azure, ma anche per consentire ai clienti di sfruttare la scalabilità della rete Microsoft per migliorare le reti globali esistenti.

Questo articolo illustra come eseguire la migrazione di un ambiente ibrido esistente a una rete WAN virtuale.

## <a name="scenario"></a>Scenario

Contoso è un'organizzazione finanziaria globale con uffici in Europa e Asia. La società pianifica di spostare le applicazioni esistenti da un data center locale in Azure e progetta una soluzione iniziale basata sull'architettura di data center virtuale, che include reti virtuali locali hub gestite dal cliente per la connettività ibrida. Nell'ambito del passaggio alle tecnologie basate sul cloud, il team di rete è stato in grado di garantire che la connettività sia stata ottimizzata in futuro.

Nella figura seguente viene illustrata una visualizzazione di alto livello della rete globale esistente, inclusa la connettività a più aree di Azure.

![Figura della topologia](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
di rete esistente Contoso **: topologia di rete esistente contoso**

La topologia di rete esistente presenta le caratteristiche seguenti:

- Una topologia hub-spoke viene usata in più aree, inclusi i circuiti ExpressRoute Premium per la connettività a una rete WAN privata comune.

- In alcuni di questi siti sono anche presenti tunnel VPN collegati direttamente ad Azure per raggiungere le applicazioni ospitate nel cloud Microsoft.

## <a name="requirements"></a>Requisiti

Il team di rete viene incaricato di sviluppare un modello di rete globale in grado di supportare la migrazione di Contoso al cloud e che sia ottimizzato in termini di costi, scalabilità e prestazioni. In breve, devono essere soddisfatti i requisiti seguenti:

- Fornire nelle sedi centrali e nelle filiali un percorso ottimizzato verso le applicazioni ospitate nel cloud.
- Rimuovere la dipendenza dai data center locali esistenti per la terminazione VPN, mantenendo i percorsi di connettività seguenti:
  - **Da ramo a VNet**: gli uffici connessi VPN devono essere in grado di accedere alle applicazioni migrate nel cloud nell'area di Azure locale.
  - Da **ramo a hub**a hub a VNet: gli uffici connessi alla VPN devono poter accedere alle applicazioni migrate nel cloud nell'area di Azure remota.
  - **Ramo a ramo**: gli uffici connessi con VPN a livello di area devono essere in grado di comunicare tra loro e i siti HQ/DC connessi ExpressRoute.
  - Da **ramo a hub**a hub a ramo: gli uffici connessi VPN a livello globale devono essere in grado di comunicare tra loro e con qualsiasi sito HQ/DC connesso ExpressRoute.
  - **Da ramo a Internet**: i siti connessi devono essere in grado di comunicare con Internet. Il traffico deve essere filtrato e registrato.
  - Da **VNet a VNet**: le reti virtuali spoke nella stessa area devono essere in grado di comunicare tra loro.
  - Da **VNet a hub a VNet**: le reti virtuali spoke nelle diverse aree devono essere in grado di comunicare tra loro.
- Fornire la possibilità per gli utenti di roaming di Contoso (portatile e telefono) di accedere alle risorse aziendali mentre non si trovino nella rete aziendale.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Architettura WAN virtuale di Azure

Nella figura seguente viene illustrata una visualizzazione di alto livello della topologia di destinazione aggiornata tramite la rete WAN virtuale di Azure per soddisfare i requisiti descritti nella sezione precedente.

![Figura architettura](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
WAN virtuale di Contoso **: architettura WAN virtuale di Azure**

Riepilogo:

- La sede centrale in Europa rimane connessa a ExpressRoute, i controller di dominio locali in Europa vengono completamente trasferiti in Azure e quindi rimossi.
- I controller di dominio e la sede centrale in Asia rimangono connessi alla rete WAN privata. WAN virtuale di Azure ora usato per aumentare la rete del gestore locale e fornire connettività globale.
- Hub WAN virtuali di Azure distribuito in Europa occidentale e Sud Asia orientale aree di Azure per fornire l'hub di connettività per i dispositivi ExpressRoute e VPN connessi.
- Gli hub forniscono anche la terminazione VPN per gli utenti in roaming tra più tipi di client tramite connettività OpenVPN alla rete a mesh globale, consentendo l'accesso non solo alle applicazioni trasferite in Azure ma anche alle risorse rimaste nell'ambiente locale.
- Connettività Internet per le risorse interne a una rete virtuale fornita dalla rete WAN virtuale di Azure.

Anche la connettività Internet per i siti remoti è fornita dalla rete WAN virtuale di Azure. Breakout Internet locale supportato tramite l'integrazione di partner per l'accesso ottimizzato a servizi SaaS come Office 365.

## <a name="migrate-to-virtual-wan"></a>Eseguire la migrazione alla rete WAN virtuale

Questa sezione illustra i diversi passaggi per la migrazione alla rete WAN virtuale di Azure.

### <a name="step-1-vdc-hub-and-spoke-single-region"></a>Passaggio 1: area singola hub-and-spoke di VDC

Esaminare l'architettura. Nella figura seguente viene illustrata una topologia a singola area per contoso prima dell'implementazione della rete WAN virtuale di Azure:

![Topologia](./media/migrate-from-hub-spoke-topology/figure1.png)
a singola area**Figura 1: Single Region hub-and-spoke**

In conformità con l'approccio del data center virtuale (VCC), la rete virtuale dell'hub gestito dal cliente contiene diversi blocchi di funzioni:

- Servizi condivisi (qualsiasi funzione comune richiesta da più spoke). Esempio: contoso usa i controller di dominio di Windows Server nelle macchine virtuali IaaS (Infrastructure-as-a-Service).
- I servizi firewall di routing/IP sono forniti da un'appliance virtuale di rete di terze parti, abilitando il routing IP di livello 3 spoke-spoke.
- Servizi in ingresso/uscita Internet, tra cui il gateway applicazione di Azure per le richieste HTTPS in ingresso e servizi proxy di terze parti in esecuzione in macchine virtuali per l'accesso in uscita filtrato alle risorse Internet.
- ExpressRoute e gateway di rete virtuale VPN per la connettività alle reti locali.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Passaggio 2: distribuire Hub WAN virtuali

Distribuire un hub WAN virtuale in ogni area. Configurare l'hub WAN virtuale con gateway VPN e gateway ExpressRoute come descritto negli articoli seguenti:

- [Esercitazione: Creare una connessione da sito a sito con la rete WAN virtuale di Azure](virtual-wan-site-to-site-portal.md)
- [Esercitazione: creare un'associazione ExpressRoute usando la rete WAN virtuale di Azure](virtual-wan-expressroute-portal.md)

> [!NOTE]
> La rete WAN virtuale di Azure deve usare lo SKU standard per abilitare alcuni dei percorsi di traffico illustrati in questo articolo.

![Distribuire Hub WAN](./media/migrate-from-hub-spoke-topology/figure2.png)
virtuali**Figura 2: Hub-and-spoke per la migrazione WAN virtuale**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Passaggio 3: connettere siti remoti (ExpressRoute e VPN) a una rete WAN virtuale

Connettere l'hub WAN virtuale ai circuiti ExpressRoute esistenti e configurare le VPN da sito a sito tramite Internet per qualsiasi ramo remoto.

> [!NOTE]
> I circuiti ExpressRoute devono essere aggiornati al tipo di SKU Premium per connetterli all'hub della rete WAN virtuale.

![Connettere siti remoti a una](./media/migrate-from-hub-spoke-topology/figure3.png)
rete WAN virtuale**Figura 3: Hub di rete virtuale-e-spoke alla migrazione della rete WAN virtuale**

A questo punto, le apparecchiature di rete locali inizieranno a ricevere route che riflettono lo spazio degli indirizzi IP assegnato all'hub gestito da WAN virtuale VNet. In questa fase le filiali remote connesse alla VPN vedranno due percorsi alle applicazioni esistenti nelle reti virtuali spoke. Questi dispositivi devono essere configurati per continuare a usare il tunnel verso l'hub del data center virtuale per assicurare il routing simmetrico durante la fase di transizione.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Passaggio 4: testare la connettività ibrida tramite la rete WAN virtuale

Prima di usare l'hub WAN virtuale gestito per la connettività di produzione, è consigliabile configurare una rete virtuale con test spoke e una connessione VNet WAN virtuale. Verificare che le connessioni a questo ambiente di test funzionino tramite ExpressRoute e VPN da sito a sito prima di continuare con i passaggi successivi.

![Testare la connettività ibrida tramite la](./media/migrate-from-hub-spoke-topology/figure4.png)
rete WAN virtuale**Figura 4: Hub-and-spoke per la migrazione WAN virtuale**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Passaggio 5: transizione della connettività all'hub WAN virtuale

![Connettività di transizione all'hub](./media/migrate-from-hub-spoke-topology/figure5.png)
WAN virtuale**Figura 5: Hub di rete virtuale-e-spoke alla migrazione WAN virtuale**

**oggetto**. Eliminare le connessioni peering esistenti dalle reti virtuali spoke all'hub Data Center precedente. L'accesso alle applicazioni nelle reti virtuali spoke non è disponibile finché non vengono completati i passaggi a-c.

**b**. Connettere le reti virtuali spoke all'hub WAN virtuale tramite connessioni VNet.

**c**. Rimuovere le route definite dall'utente usate in precedenza nelle reti virtuali spoke per le comunicazioni spoke-spoke. Questo percorso è ora abilitato dal routing dinamico disponibile nell'hub della rete WAN virtuale.

**d**. I gateway VPN ed ExpressRoute esistenti nell'hub del data center virtuale vengono ora rimossi per consentire il passaggio successivo (e).

**e**. Connettere l'hub del data center virtuale (rete virtuale hub) precedente all'hub della rete WAN virtuale tramite una nuova connessione di rete virtuale.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Passaggio 6: l'hub precedente diventa servizi condivisi spoke

La rete di Azure è ora stata riprogettata in modo da configurare l'hub della rete WAN virtuale come punto centrale nella nuova topologia.

![L'hub precedente diventa servizi condivisi](./media/migrate-from-hub-spoke-topology/figure6.png)
spoke**Figura 6: Hub di rete virtuale-e-spoke alla migrazione WAN virtuale**

Poiché l'hub WAN virtuale è un'entità gestita e non consente la distribuzione di risorse personalizzate, ad esempio macchine virtuali, il blocco dei servizi condivisi ora esiste come rete virtuale spoke e ospita funzioni come l'ingresso Internet tramite applicazione Azure gateway o Appliance virtualizzato di rete. Il traffico tra l'ambiente di servizi condivisi e le macchine virtuali back-end ora transita attraverso l'hub gestito dalla rete WAN virtuale.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Passaggio 7: ottimizzare la connettività locale per usare completamente la rete WAN virtuale

In questa fase, Contoso ha quasi completato la migrazione delle applicazioni aziendali al cloud Microsoft, con solo alcune applicazioni legacy rimanenti all'interno del controller di dominio locale.

![Ottimizzare la connettività locale per usare completamente la rete WAN](./media/migrate-from-hub-spoke-topology/figure7.png)
virtuale**Figura 7: Hub di rete virtuale-e-spoke alla migrazione WAN virtuale**

Per sfruttare tutte le funzionalità della rete WAN virtuale di Azure, Contoso decide di rimuovere le connessioni VPN locali legacy. Le filiali che continuano ad accedere alle reti di sedi centrali o data center possono transitare nella rete globale Microsoft tramite il routing di transito predefinito della rete WAN virtuale di Azure.

> [!NOTE]
> Il servizio Copertura globale di ExpressRoute rappresenta un'alternativa per i clienti che scelgono di sfruttare il backbone Microsoft per integrare le loro reti WAN private esistenti.

## <a name="end-state-architecture-and-traffic-paths"></a>Architettura e percorsi di traffico dello stato finale

![Architettura dello stato finale e percorsi](./media/migrate-from-hub-spoke-topology/figure8.png)
di traffico**Figura: rete WAN virtuale a doppia area**

Questa sezione include alcuni flussi di traffico di esempio per dimostrare come questa topologia soddisfa i requisiti originali.

### <a name="path-1"></a>Percorso 1

Il percorso 1 Mostra il flusso di traffico da un ramo connesso VPN S2S in Asia a una VNet di Azure nell'area Asia orientale meridionale.

Il traffico viene instradato come segue:

- Il ramo Asia è connesso tramite tunnel abilitati per BGP S2S resilienti nell'hub WAN virtuale a sud Asia orientale.

- L'hub della rete WAN virtuale in Asia instrada il traffico in locale alla rete virtuale connessa.

![Flusso 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Percorso 2

Il percorso 2 Mostra il flusso di traffico dal quartier generale europeo connesso ExpressRoute a una VNet di Azure nell'area Asia orientale meridionale.

Il traffico viene instradato come segue:

- La sede centrale europea è connessa tramite il circuito ExpressRoute Premium all'hub WAN virtuale dell'Europa occidentale.

- La connettività globale da hub a hub della rete WAN virtuale garantisce il transito del traffico verso la rete virtuale connessa nell'area remota.

![Flusso 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Percorso 3

Il percorso 3 Mostra il flusso di traffico dal controller di dominio locale in Asia connesso a una rete WAN privata a un ramo collegato S2S europeo.

Il traffico viene instradato come segue:

- Il controller di dominio in Asia è connesso all'operatore della rete WAN privata locale.

- Il circuito ExpressRoute termina localmente in una rete WAN privata si connette all'hub WAN virtuale Asia orientale sud.

- La connettività globale da Hub a hub WAN virtuale consente il transito del traffico.

![Flusso 3](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>Percorso 4

Il percorso 4 Mostra il flusso di traffico da una VNet di Azure nell'area Asia orientale meridionale a un'area di Azure VNet in Europa occidentale.

Il traffico viene instradato come segue:

- La connettività globale da hub a hub della rete WAN virtuale consente il transito nativo di tutte le reti virtuali di Azure connesse senza ulteriori configurazioni dell'utente.

![Flusso 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Percorso 5

Il percorso 5 Mostra il flusso di traffico da utenti VPN mobili (P2S) a una VNet di Azure nell'area Europa occidentale.

Il traffico viene instradato come segue:

- Gli utenti di computer portatili e dispositivi mobili usano il client OpenVPN per la connettività trasparente nel gateway VPN P2S in Europa occidentale.

- L'hub della rete WAN virtuale in Europa occidentale instrada il traffico in locale alla rete virtuale connessa.

![Flusso 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Controllo di sicurezza e criteri tramite Firewall di Azure

Contoso ha ora convalidato la connettività tra tutti i rami e reti virtuali in linea con i requisiti descritti in precedenza in questo articolo. Per soddisfare i requisiti per il controllo di sicurezza e l'isolamento di rete, è necessario continuare a separare e registrare il traffico tramite la rete Hub. In precedenza questa funzione veniva eseguita da un'appliance virtuale di rete. Contoso vuole anche rimuovere le autorizzazioni dei servizi proxy esistenti e usare i servizi nativi di Azure per i filtri Internet in uscita.

![Sicurezza e controllo dei criteri tramite Azure](./media/migrate-from-hub-spoke-topology/security-policy.png)
firewall**figure: firewall di Azure in WAN virtuale (hub virtuale protetto)**

I passaggi di alto livello seguenti sono necessari per introdurre il firewall di Azure negli hub WAN virtuali per abilitare un punto unificato di controllo dei criteri. Per altre informazioni su questo processo e sul concetto di hub virtuali sicuri, vedere [gestione firewall di Azure](../firewall-manager/index.yml).

1. Creare i criteri di Firewall di Azure.
2. Collegare i criteri firewall all'hub della rete WAN virtuale di Azure. Questo passaggio consente all'hub WAN virtuale esistente di funzionare come hub virtuale protetto e distribuisce le risorse del firewall di Azure necessarie.

> [!NOTE]
> Se il firewall di Azure viene distribuito in un hub WAN virtuale standard (SKU: standard): i criteri di V2V, B2V, V2I e B2I FW vengono applicati solo sul traffico proveniente dal reti virtuali e dai rami connessi all'hub specifico in cui viene distribuito il FW di Azure (hub protetto). Il traffico originato da reti virtuali e rami remoti collegati ad altri hub WAN virtuali nella stessa rete WAN virtuale non sarà "con firewall", anche se i rami remoti e VNet sono interconnessi tramite l'hub WAN virtuale ai collegamenti Hub. Il supporto del firewall tra più hub si trova nella Guida di orientamento della rete virtuale di Azure e di gestione firewall.

I percorsi seguenti illustrano i percorsi di connettività abilitati tramite hub virtuali protetti di Azure:

### <a name="path-6"></a>Percorso 6

Il percorso 6 Mostra il flusso di traffico sicuro tra reti virtuali all'interno della stessa area.

Il traffico viene instradato come segue:

- Le reti virtuali connesse allo stesso hub virtuale protetto ora instradano il traffico tramite Firewall di Azure.

- Firewall di Azure può applicare i criteri a questi flussi.

![Flusso 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Percorso 7

Il percorso 7 Mostra il flusso di traffico da una VNet di Azure a Internet o a un servizio di sicurezza di terze parti.

Il traffico viene instradato come segue:

- Le reti virtuali connesse all'hub virtuale protetto possono inviare il traffico a destinazioni pubbliche su Internet, usando l'hub protetto come punto centrale di accesso a Internet.

- Questo traffico può essere filtrato localmente usando le regole FQDN del firewall di Azure o inviato a un servizio di sicurezza di terze parti per l'ispezione.

![Flusso 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Percorso 8

Il percorso 8 Mostra il flusso del traffico da ramo a Internet o da un servizio di sicurezza di terze parti.

Il traffico viene instradato come segue:

- I rami connessi all'hub virtuale sicuro possono inviare il traffico a destinazioni pubbliche su Internet usando l'hub sicuro come punto centrale di accesso a Internet.

- Questo traffico può essere filtrato localmente usando le regole FQDN del firewall di Azure o inviato a un servizio di sicurezza di terze parti per l'ispezione.

![Flusso 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [rete WAN virtuale di Azure](virtual-wan-about.md)
