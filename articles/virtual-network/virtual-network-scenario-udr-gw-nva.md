---
title: Connessione ibrida con applicazione a 2 livelli | Microsoft Docs
description: Informazioni su come distribuire appliance virtuali e route definite dall'utente per creare un ambiente di applicazioni multilivello in Azure
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: jdial

---
# <a name="virtual-appliance-scenario"></a>Scenario dell'appliance virtuale
Uno scenario comune tra i clienti di Azure di grandi dimensioni è la necessità di offrire un'applicazione a due livelli esposta a Internet, consentendo l'accesso al livello back-end da un data center locale. Questo documento illustra uno scenario che prevede route definite dall'utente (UDR), un gateway VPN e appliance di rete virtuali per distribuire un ambiente a due livelli che soddisfi i requisiti seguenti:

* L'applicazione Web deve essere accessibile solo da Internet pubblico.
* Il server Web che ospita l'applicazione deve essere in grado di accedere a un server applicazioni back-end.
* Tutto il traffico da Internet all'applicazione Web deve passare attraverso un'appliance virtuale firewall. Questa appliance virtuale verrà usata solo per il traffico Internet.
* Tutto il traffico verso il server applicazioni deve passare attraverso un'appliance virtuale firewall. Questa appliance virtuale verrà usata per l'accesso al server back-end e l'accesso proveniente dalla rete locale tramite un gateway VPN.
* Gli amministratori devono essere in grado di gestire le appliance virtuali firewall dai computer locali, con una terza appliance virtuale firewall usata esclusivamente per scopi di gestione.

Si tratta di uno scenario di rete perimetrale standard con una rete perimetrale e una rete protetta. Questo scenario può essere creato in Azure usando gruppi di sicurezza di rete, appliance virtuali firewall o una combinazione di entrambi. La tabella seguente mostra un confronto tra vantaggi e svantaggi di gruppi di sicurezza di rete e appliance virtuali firewall.

|  | Vantaggi | Svantaggi |
| --- | --- | --- |
| NSG |Nessun costo. <br/>Integrato nel controllo degli accessi in base al ruolo di Azure. <br/>Le regole possono essere create in modelli di Azure Resource Manager. |La complessità può variare in ambienti più grandi. |
| Firewall |Controllo completo del piano dati. <br/>Gestione centrale con console firewall. |Costo dell'appliance firewall. <br/>Non integrato nel controllo degli accessi in base al ruolo di Azure. |

La soluzione seguente usa appliance virtuali firewall per implementare uno scenario di rete perimetrale/rete protetta.

## <a name="considerations"></a>Considerazioni
È possibile distribuire l'ambiente illustrato in precedenza in Azure usando diverse funzionalità attualmente disponibili come indicato di seguito.

* **Rete virtuale**. Una rete virtuale di Azure funziona in modo analogo a una rete locale e può essere segmentata in una o più subnet per l'isolamento del traffico e la separazione dei compiti.
* **Appliance virtuale**. Numerosi partner offrono appliance virtuali in Azure Marketplace che possono essere usate per i tre firewall descritti in precedenza. 
* **Route definite dall'utente**. Le tabelle di route possono contenere route definite dall'utente usate dalla rete di Azure per controllare il flusso dei pacchetti all'interno di una rete virtuale. Queste tabelle di route possono essere applicate alle subnet. Una delle funzionalità più recenti di Azure è la possibilità di applicare una tabella di route alla subnet del gateway, al fine di inoltrare tutto il traffico in ingresso sulla rete virtuale di Azure da una connessione ibrida verso un'appliance virtuale.
* **Inoltro IP**. Per impostazione predefinita, il motore di rete Azure inoltra i pacchetti alle schede di interfaccia di rete (NIC) virtuale solo se l'indirizzo IP di destinazione dei pacchetti corrisponde all'indirizzo IP della scheda di interfaccia di rete. Se quindi una route definita dall'utente indica che un pacchetto dovrà essere inviato a una specifica appliance virtuale, il motore di rete di Azure rilascerà il pacchetto. Per far sì che il pacchetto venga inviato a una macchina virtuale, in questo caso un'appliance virtuale, che non è la destinazione effettiva del pacchetto è necessario abilitare l'inoltro IP per l'appliance virtuale.
* **Gruppi di sicurezza di rete**. Nell'esempio seguente non vengono usati gruppi di sicurezza di rete, che possono essere tuttavia applicati alle subnet e/o alle schede di interfaccia di rete di questa soluzione per filtrare ulteriormente il traffico in ingresso e in uscita da tali subnet e schede di interfaccia di rete.

![IPv6 connectivity](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

In questo esempio è presente una sottoscrizione che include gli elementi seguenti:

* 2 gruppi di risorse non indicati nel diagramma. 
  * **ONPREMRG**. Contiene tutte le risorse necessarie per simulare una rete locale.
  * **AZURERG**. Contiene tutte le risorse necessarie per l'ambiente di rete virtuale di Azure. 
* Una rete virtuale denominata **onpremvnet** usata per simulare un data center locale segmentato come indicato di seguito.
  * **onpremsn1**. Subnet contenente una macchina virtuale che esegue Ubuntu per simulare un server locale.
  * **onpremsn2**. Subnet contenente una macchina virtuale che esegue Ubuntu per simulare un computer locale usato da un amministratore.
* È presente un'appliance virtuale firewall denominata **OPFW** su **onpremvnet** usata per mantenere un tunnel per **azurevnet**.
* Una rete virtuale denominata **azurevnet** segmentata come indicato di seguito.
  * **azsn1**. Subnet del firewall esterno usata esclusivamente per il firewall esterno. Tutto il traffico Internet in ingresso passerà attraverso questa subnet. Questa subnet contiene solo una scheda di interfaccia di rete collegata al firewall esterno.
  * **azsn2**. Subnet front-end che ospita una macchina virtuale in esecuzione come server Web accessibile da Internet.
  * **azsn3**. Subnet back-end che ospita una macchina virtuale che esegue un server applicazioni back-end accessibile dal server Web front-end.
  * **azsn4**. Subnet di gestione usata esclusivamente per consentire l'accesso di gestione a tutte le appliance virtuali firewall. Questa subnet contiene solo una scheda di interfaccia di rete per ogni appliance virtuale firewall usata nella soluzione.
  * **GatewaySubnet**. Subnet di connessione ibrida di Azure necessaria per consentire a ExpressRoute e al gateway VPN di offrire la connessione tra le reti virtuali di Azure e altre reti. 
* Sono disponibili 3 appliance virtuali firewall nella rete **azurevnet** . 
  * **AZF1**. Firewall esterno esposto a Internet pubblico con una risorsa di indirizzo IP pubblico in Azure. È necessario ottenere un modello dal Marketplace o direttamente dal fornitore dell'appliance per il provisioning di un'appliance virtuale con 3 schede di interfaccia di rete.
  * **AZF2**. Firewall interno usato per gestire il traffico tra **azsn2** e **azsn3**. Anche questa è un'appliance virtuale con 3 schede di interfaccia di rete.
  * **AZF3**. Firewall di gestione accessibile agli amministratori dal centro dati locale e connesso a una subnet usata per la gestione di tutte le appliance firewall. I modelli per appliance virtuali con 2 schede di interfaccia di rete sono disponibili nel Marketplace oppure possono essere richiesti direttamente al fornitore dell'appliance.

## <a name="user-defined-routing-(udr)"></a>Routing definito dall'utente
Ogni subnet in Azure può essere collegata a una tabella di route definite dall'utente usata per definire come viene indirizzato il traffico che ha origine nella subnet. Se non sono presenti route definite dall'utente, Azure usa le route predefinite per consentire il flusso del traffico da una subnet all'altra. Per informazioni approfondite sulle route definite dall'utente, vedere [Cosa sono le route definite dall'utente e l'inoltro IP](virtual-networks-udr-overview.md#ip-forwarding).

Per garantire la comunicazione tramite l'appliance firewall corretta, in base all'ultimo requisito indicato in precedenza, è necessario creare la tabella seguente contenente le route definite dall'utente in **azurevnet**.

### <a name="azgwudr"></a>azgwudr
In questo scenario, il solo traffico da locale ad Azure verrà usato per gestire i firewall connettendosi a **AZF3** e tale traffico deve passare attraverso il firewall interno, **AZF2**. È quindi necessaria una sola route in **GatewaySubnet** come illustrato di seguito.

| Destination | Hop successivo | Spiegazione |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Consente al traffico locale di raggiungere il firewall di gestione **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Destination | Hop successivo | Spiegazione |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Consente il traffico verso la subnet di back-end che ospita il server applicazioni tramite **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Consente di indirizzare il resto del traffico tramite **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Destination | Hop successivo | Spiegazione |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Consente al traffico verso **azsn2** di passare dal server app al server Web attraverso **AZF2** |

È anche necessario creare tabelle route per le subnet in **onpremvnet** per simulare il centro dati locale.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Destination | Hop successivo | Spiegazione |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Consente il traffico verso **onpremsn2** attraverso **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Destination | Hop successivo | Spiegazione |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Consente il traffico verso la subnet back-end in Azure tramite **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Consente il traffico verso **onpremsn1** attraverso **OPFW** |

## <a name="ip-forwarding"></a>Inoltro IP
Le route definite dall'utente e l'inoltro IP sono funzionalità che è possibile usare in combinazione per consentire l'uso delle appliance virtuali per gestire il flusso del traffico in una rete virtuale di Azure.  Un dispositivo virtuale non è altro che una macchina virtuale che esegue un'applicazione utilizzata per gestire il traffico di rete in qualche modo, ad esempio un firewall o un dispositivo NAT.

Questo dispositivo virtuale macchina virtuale deve essere in grado di ricevere traffico in ingresso non viene indirizzato a se stesso. Per consentire a una macchina virtuale di ricevere il traffico indirizzato ad altre destinazioni, è necessario abilitare l'inoltro IP per la macchina virtuale. Si tratta di un'impostazione di Azure e non del sistema operativo guest. L'appliance virtuale deve comunque eseguire qualche tipo di applicazione per gestire il traffico in ingresso e indirizzarlo correttamente.

Per altre informazioni sull'inoltro IP, vedere [Cosa sono le route definite dall'utente e l'inoltro IP](virtual-networks-udr-overview.md#ip-forwarding).

Si supponga ad esempio che una rete virtuale di Azure sia configurata come segue:

* La subnet **onpremsn1** contiene una macchina virtuale denominata **onpremvm1**.
* La subnet **onpremsn2** contiene una macchina virtuale denominata **onpremvm2**.
* Un'appliance virtuale denominata **OPFW** è connessa a **onpremsn1** e **onpremsn2**.
* Una route definita dall'utente collegata a **onpremsn1** specifica che tutto il traffico verso **onpremsn2** dovrà essere inviato a **OPFW**.

Se a questo punto **onpremvm1** tenta di stabilire una connessione a **onpremvm2**, verrà usata la route definita dall'utente e il traffico verrà inviato a **OPFW** come hop successivo. Tenere presente che la destinazione effettiva dei pacchetti non viene modificata, **onpremvm2** rappresenta ancora la destinazione. 

Se l'inoltro IP non è attivato per **OPFW**, la logica della rete virtuale di Azure rilascerà i pacchetti perché consente l'invio di pacchetti a una macchina virtuale solo se l'indirizzo IP della macchina virtuale è la destinazione del pacchetto.

Con l'inoltro IP, la logica della rete virtuale di Azure inoltrerà i pacchetti a OPFW, senza modificare l'indirizzo di destinazione originale. **OPFW** deve gestire i pacchetti e determinare cosa farne.

Per far sì che lo scenario precedente funzioni è necessario abilitare l'inoltro IP sulle schede di interfaccia di rete per **OPFW**, **AZF1**, **AZF2** e **AZF3** che vengono usate per il routing. Si tratta di tutte le schede di interfaccia di rete ad eccezione di quelle collegate alla subnet di gestione. 

## <a name="firewall-rules"></a>Regole del firewall
Come descritto in precedenza, l'inoltro IP assicura solo che i pacchetti vengano inviati alle appliance virtuali. L'appliance deve comunque stabilire come gestire i pacchetti. Nello scenario precedente è necessario creare le regole seguenti nelle appliance:

### <a name="opfw"></a>OPFW
OPFW rappresenta un dispositivo locale contenente le regole seguenti:

* **Route**: tutto il traffico verso 10.0.0.0/16 (**azurevnet**) deve essere inviato attraverso il tunnel **ONPREMAZURE**.
* **Criteri**: consentire tutto il traffico bidirezionale tra **port2** e **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 rappresenta un'appliance virtuale di Azure contenente le regole seguenti:

* **Criteri**: consentire tutto il traffico bidirezionale tra **port1** e **port2**.

### <a name="azf2"></a>AZF2
AZF2 rappresenta un'appliance virtuale di Azure contenente le regole seguenti:

* **Route**: tutto il traffico verso 10.0.0.0/16 (**onpremvnet**) deve essere inviato all'indirizzo IP del gateway di Azure (ovvero 10.0.0.1) attraverso **port1**.
* **Criteri**: consentire tutto il traffico bidirezionale tra **port1** e **port2**.

## <a name="network-security-groups-(nsgs)"></a>Gruppi di sicurezza di rete (NGS)
In questo scenario i gruppi di sicurezza di rete non vengono usati. È tuttavia possibile applicare i gruppi di sicurezza di rete a ogni subnet per limitare il traffico in ingresso e in uscita. È ad esempio possibile applicare le seguenti regole per gruppi di sicurezza di rete alla subnet del firewall esterno.

**In ingresso**

* Consentire tutto il traffico TCP da Internet alla porta 80 in qualsiasi macchina virtuale della subnet.
* Rifiutare il resto del traffico da Internet.

**In uscita**

* Rifiutare il traffico verso Internet.

## <a name="high-level-steps"></a>Passaggi di livello elevato
Per distribuire lo scenario seguire questi passaggi generali.

1. Effettuare l'accesso alla sottoscrizione di Azure.
2. Se si intende distribuire una rete virtuale per simulare la rete locale, effettuare il provisioning delle risorse che fanno parte di **ONPREMRG**.
3. Effettuare il provisioning delle risorse che fanno parte di **AZURERG**.
4. Effettuare il provisioning del tunnel da **onpremvnet** ad **azurevnet**.
5. Dopo aver effettuato il provisioning di tutte le risorse, accedere a **onpremvm2** ed eseguire il ping di 10.0.3.101 per verificare la connessione tra **onpremsn2** e **azsn3**.

<!--HONumber=Oct16_HO2-->


