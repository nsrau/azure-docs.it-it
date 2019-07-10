---
title: Connessioni di rete e alimentazione per Microsoft Azure FXT Edge Filer
description: Informazioni su come cablare le porte di rete e collegare l'alimentazione per il nodo hardware Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: ae179e8ce2a2ba772a7fb14825660e0fff9e7410
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542924"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Esercitazione: Creare connessioni di rete e alimentare il nodo Azure FXT Edge Filer

Questa esercitazione spiega su come cablare le connessioni di rete per un nodo hardware Azure FXT Edge Filer.

In questa esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Scegliere il tipo di cavo di rete per l'ambiente corrente
> * Connettere un nodo Azure FXT Edge Filer alla rete del data center
> * Far passare i cavi nel braccio di gestione cavi (Cable Management Arm, CMA)
> * Connettere l'alimentazione al dispositivo in rack e accenderlo

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione è necessario installare il dispositivo Azure FXT Edge Filer in un rack standard per apparecchiature. Il CMA deve essere installato sul nodo del filer. 

## <a name="identify-ports"></a>Identificare le porte

Identificare le varie porte sul retro del dispositivo Azure FXT Edge Filer. 
 
![Retro di un dispositivo cablato](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Cablare il dispositivo

* Connettere le porte RJ-45 all'origine in rete del data center, come descritto in [Porte di rete](#network-ports).  
* Connettere in modo sicuro la [porta iDRAC](#idrac-port) a una rete separata con un server DHCP sicuro. 
* Usare le porte USB e la porta VGA per connettere una tastiera e un monitor al nodo per la configurazione iniziale. È necessario avviare il nodo e [impostare una password iniziale](fxt-node-password.md) per attivare le altre porte del nodo. Per informazioni dettagliate, leggere [Impostare le password iniziali](fxt-node-password.md). 

Questo articolo descrive anche come [connettere l'alimentazione CA](#connect-power-cables) per il nodo. 

Viene inoltre spiegato come connettersi alla [porta seriale](#serial-port-only-when-necessary) del nodo, se necessario per la risoluzione dei problemi effettuata da tecnici specializzati. 

### <a name="network-ports"></a>Porte di rete 

Ogni nodo Azure FXT Edge Filer include le porte di rete seguenti: 

* Sei porte dati dual rate a 25 GbE/10 GbE ad alta velocità: 

  * Quattro porte fornite da due schede di rete plug-in a doppia porta
  * Due porte fornite dalla scheda di rete mezzanine della scheda madre 

* Due porte a 1 GbE fornite dalla scheda di rete mezzanine della scheda madre 

Le porte dati a 25 GbE/10 GbE ad alta velocità sono dotate di alloggiamenti standard compatibili con SFP28. Per usare i cavi ottici, è necessario installare moduli ricetrasmettitori ottici SFP28 (non forniti).

Le porte a 1 GbE sono dotate di connettori RJ-45 standard.

Per un elenco completo dei cavi, dei commutatori e dei ricetrasmettitori, consultare la [matrice di interoperabilità di Cavium FastlinQ 41000 Series](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

I tipi di connessioni da usare per il sistema dipende dall'ambiente del data center.

* Per la connessione a una rete a 25 GbE, cablare ognuna delle porte dati ad alta velocità con uno dei tipi di cavi seguenti:

  * Cavo ottico e ricetrasmettitore ottico SFP28 con funzionalità a 25 GbE o 25 GbE/10 GbE dual rate
  * Cavo biassiale con attacco diretto a 25 GbE tipo SFP28

* Per la connessione a una rete a 10 GbE, cablare ognuna delle porte dati ad alta velocità con uno dei tipi di cavi seguenti: 

  * Cavo ottico e ricetrasmettitore ottico SFP28 con funzionalità a 10 GbE o 25 GbE/10 GbE dual rate
  * Cavo biassiale con attacco diretto a 25 GbE tipo SFP28
  * Cavo biassiale con attacco diretto a 10 GbE tipo SFP28

* Le porte di rete a 1 GbE vengono usate per il traffico della gestione dei cluster. Selezionare l'opzione **Use 1Gb mgmt network** (Usa la rete di gestione a 1 Gb) durante la creazione del cluster se si vuole creare una rete fisicamente separata per la configurazione dei cluster, come descritto in [Configurare la rete di gestione](fxt-cluster-create.md#configure-the-management-network). Collegare le porte con un cavo Cat5 standard o superiore come descritto nell'elenco dei cavi supportati.

  Se si intende usare porte ad alta velocità per tutto il traffico, è possibile lasciare scollegate le porte a 1 GbE. Per impostazione predefinita, le porte di rete a 1 GbE non vengono usate se è disponibile una porta dati di velocità superiore.  

### <a name="idrac-port"></a>Porta iDRAC  

La porta etichettata come iDRAC è una connessione a 1 Gb che consente la comunicazione con un controller di accesso remoto usato per il monitoraggio e la gestione dell'hardware. Il software FXT usa l'interfaccia IPMI (Intelligent Platform Management Interface) con questo controller per la risoluzione dei problemi e il ripristino. È possibile usare l'[interfaccia iDRAC](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) predefinita per monitorare l'hardware tramite questa porta. L'accesso con iDRAC e IPMI è abilitato per impostazione predefinita. 

> [!Note]
> La porta iDRAC può ignorare il sistema operativo e interagire direttamente con l'hardware nel nodo. 

Usare queste strategie di sicurezza quando si connette e si configura la porta iDRAC:

* Connettere le porte iDRAC solo a una rete fisicamente separata dalla rete dati usata per accedere al cluster.
* Impostare una password di amministratore iDRAC sicura in ogni nodo. È necessario impostare questa password per attivare l'hardware. Seguire le istruzioni in [Impostare le password per l'hardware](fxt-node-password.md).
* Nella configurazione predefinita della porta iDRAC si usa IPv4 e DHCP per l'assegnazione degli indirizzi IP. Assicurarsi che l'ambiente DHCP sia ben protetto e che le connessioni tra i client DHCP e il server DHCP siano limitate. Il pannello di controllo del cluster include le impostazioni per modificare il metodo di configurazione dell'indirizzo dei nodi dopo la creazione del cluster.
* Lasciare la porta iDRAC impostata su "dedicated mode" (impostazione predefinita), che limita il traffico di rete iDRAC/IPMI alla porta RJ-45 dedicata.

La porta iDRAC non richiede una connessione di rete ad alta velocità.
  
### <a name="serial-port-only-when-necessary"></a>Porta seriale (solo quando necessaria)

In alcune circostanze, il Servizio Supporto Tecnico Clienti Microsoft potrebbe chiedere di connettere un terminale alla porta seriale di un nodo per diagnosticare un problema.  

Per collegare la console:

1. Individuare la porta seriale (COM1) sul retro del nodo FXT Edge Filer.
1. Usare un cavo null modem per connettere la porta seriale al terminale configurato per ANSI-115200-8N1.
1. Accedere alla console ed eseguire gli altri passaggi come indicato dal personale del supporto.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Far passare i cavi nel braccio di gestione cavi (Cable Management Arm, CMA)

Con ogni nodo Azure FXT Edge Filer viene fornito un CMA facoltativo. Il CMA semplifica la sistemazione dei cavi e consente di accedere facilmente al retro dello chassis senza dover scollegare i cavi. 

Seguire queste istruzioni per far passare i cavi nel CMA: 

1. Usare le fascette fornite per raggruppare i cavi in entrata e in uscita dai cestelli in modo che non interferiscano con i sistemi adiacenti (1).
1. Con il CMA nella posizione di servizio, far passare il fascio di cavi attraverso i cestelli interno ed esterno (2).
1. Usare le fascette a gancio e occhiello preinstallate a ogni estremità dei cestelli per fissare i cavi (3).
1. Riposizionare il CMA sul supporto (4).
1. Installare il cavo dell'indicatore di stato nel retro del sistema e fissare il cavo facendolo passare nel CMA. Collegare l'altra estremità del cavo all'angolo del cestello esterno del CMA (5). 

   > [!CAUTION]
   > Per evitare potenziali danni causati da cavi sporgenti, bloccare eventuali allentamenti del cavo dell'indicatore di stato dopo aver fatto passare questo cavo nel CMA. 

![Illustrazione del CMA con i cavi installati](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  Se non si è installato il braccio di gestione cavi (CMA, Cable Management Arm), usare le due fascette a gancio e occhiello incluse nel kit di guide per sistemare i cavi sul retro del sistema.
> 
>  1. Individuare le staffe CMA esterne sui lati interni di entrambe le flange del rack.
>  2. Avvolgere delicatamente i cavi tirandoli verso sinistra e verso destra dai connettori del sistema.
>  3. Infilare le fascette a gancio e occhiello attraverso le fessure attrezzate delle staffe CMA esterne su ogni lato del sistema per fissare i fasci di cavi.
> 
>     ![Cavi sistemati senza CMA](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Informazioni sui requisiti degli indirizzi IP

Per i nodi hardware in una cache di archiviazione ibrida Azure FXT Edge Filer gli indirizzi IP vengono gestiti dal software del cluster.

Ogni nodo richiede almeno un indirizzo IP, ma gli indirizzi dei nodi vengono assegnati durante l'aggiunta o la rimozione dei nodi dal cluster. 

Il numero totale di indirizzi IP necessari dipende dal numero di nodi che costituiscono la cache. 

Configurare l'intervallo di indirizzi IP usando il software del Pannello di controllo dopo l'installazione dei nodi. Per altre informazioni, leggere [Raccogliere informazioni per il cluster](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Connettere i cavi di alimentazione

Ogni nodo Azure FXT Edge Filer usa due unità PSU (Power Supply Unit). 

> [!TIP] 
> Per sfruttare le due unità PSU ridondanti, collegare ogni cavo di alimentazione CA a un'unità PDU (Power Distribution Unit) in un circuito derivato indipendente.  
> 
> Per maggiore protezione, per alimentare le unità PDU è possibile usare un gruppo di continuità. 

1. Connettere i cavi di alimentazione inclusi alle unità PSU nello chassis. Assicurarsi che i cavi e le unità PSU siano completamente inseriti. 
1. Collegare i cavi di alimentazione alle unità PDU sul rack per apparecchiature. Se possibile, usare fonti di alimentazione separate per i due cavi. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Accendere un nodo Azure FXT Edge Filer

Per accendere il nodo, premere il pulsante di alimentazione nella parte anteriore del sistema. Il pulsante si trova sul pannello di controllo sul lato destro. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Spegnere un nodo Azure FXT Edge Filer

È possibile usare il pulsante di alimentazione per arrestare il sistema durante il test e prima di aggiungerlo a un cluster. Dopo aver usato un nodo Azure FXT Edge Filer come parte di un cluster, è tuttavia necessario usare il pannello di controllo del cluster per arrestare l'hardware. Per informazioni dettagliate, leggere [Come spegnere in modo sicuro il nodo hardware Azure FXT Edge Filer](fxt-power-off.md). 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato il cablaggio dell'hardware, accedere ognuno dei nodi e inizializzarli impostandone la password radice. 
> [!div class="nextstepaction"]
> [Impostare le password iniziali](fxt-node-password.md)
