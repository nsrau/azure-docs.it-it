<properties 
   pageTitle="Installare il dispositivo StorSimple 8100 | Microsoft Azure"
   description="Viene descritto come disimballare, montare su rack e cablare il dispositivo StorSimple 8100 prima di distribuire e configurare il software."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="11/16/2015"
   ms.author="alkohli" />

# Disimballaggio, montaggio su rack e cablaggio del dispositivo StorSimple 8100

## Panoramica

Il dispositivo Microsoft Azure StorSimple 8100 è un dispositivo a singolo enclosure montato su rack. In questa esercitazione viene illustrato come disimballare, montare su rack e cablare l'hardware del dispositivo StorSimple 8100 prima di configurare e distribuire il dispositivo.

## Disimballare il dispositivo StorSimple 8100

Nella seguente procedura vengono fornite istruzioni chiare e dettagliate su come disimballare il dispositivo di archiviazione StorSimple 8100. Questo dispositivo viene fornito in una singola confezione.

### Preparazione del disimballaggio del dispositivo

Prima di disimballare il dispositivo, consultare le informazioni seguenti.

![Icona di avviso](./media/storsimple-safety/IC740879.png)![icona peso elevato](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **AVVISO**

1. Accertarsi che siano disponibili almeno due persone per sostenere il peso dell'enclosure, nel caso in cui debba essere trasportato manualmente. Un'enclosure completamente configurata può pesare fino a 32 kg.
1. Posizionare la scatola su una superficie piana e orizzontale.
 
Successivamente, completare la procedura seguente per disimballare il dispositivo.

#### Per disimballare il dispositivo

1. Controllare le scatole e le protezioni in schiuma per verificare l'eventuale presenza di ammaccature, tagli, danni provocati dall'acqua o altri danni evidenti. Se la scatola o le protezioni risultano danneggiate in modo grave, non aprire la confezione. [Contattare il Supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per istruzioni su come verificare il corretto funzionamento del dispositivo. 

2. Disimballare la scatola. La figura seguente illustra il dispositivo StorSimple disimballato.

     ![Disimballare il dispositivo di archiviazione](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)

    **Dispositivo di archiviazione disimballato**

     Etichetta | Descrizione 
     ----- | -------------
     1 | Scatola
     2 | Protezione inferiore
     3 | Dispositivo
     4 | Protezione superiore
     5 | Scatola accessori


3. Dopo aver disimballato le due scatole, assicurarsi di avere a disposizione:

   - 1 Dispositivo a singolo enclosure
   - 2 cavi di alimentazione
   - 1 cavo Ethernet incrociato
   - 2 cavi console seriali
   - 1 convertitore seriale-USB per l'accesso seriale
   - 1 cacciavite T10 tamper-proof
   - 4 schede da QSFP a SFP+ da usare con interfacce di rete 10 GbE
   - 1 kit per il montaggio in rack (2 guide laterali con componenti di montaggio)
   - Guida introduttiva

    In caso di mancato ricevimento di uno qualsiasi degli elementi sopra elencati, [contattare il Supporto Tecnico Microsoft](storsimple-contact-microsoft-support.md).

Il passaggio successivo consiste nel montaggio su rack del dispositivo.

## Montare su rack il dispositivo StorSimple 8100

Attenersi alla procedura seguente per installare il dispositivo di archiviazione StorSimple 8100 in un rack standard da 48,26 cm (19 pollici) con montanti anteriori e posteriori. Il dispositivo StorSimple 8100 è costituito da un'enclosure principale.

L'installazione si articola in più passaggi, ciascuno dei quali è descritto nelle seguenti procedure.

### Preparare il sito

Il dispositivo deve essere installato su un rack standard da 48,26 cm (19 pollici) con montanti anteriori e posteriori. Utilizzare la procedura seguente per predisporre l'installazione del rack.

#### Per predisporre l'ambiente per l'installazione del rack

1. Assicurarsi che il dispositivo sia collocato in sicurezza su una superficie di lavoro (o simile) orizzontale, stabile e piana.

2. Verificare che nell'ambiente in cui si intende effettuare l'installazione sia disponibile una fonte di alimentazione CA standard indipendente o un'unità PDU (Power Distribution Unit) rack con un gruppo di continuità.

3. Assicurarsi che nel rack in cui si intende montare il dispositivo sia disponibile uno slot 2U.

![Icona di avviso](./media/storsimple-safety/IC740879.png)![icona peso elevato](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **AVVISO**
 
Se si prevede di trasportare il dispositivo a mano, assicurarsi che siano disponibili due persone. Un'enclosure completamente configurata può pesare fino a 32 kg.

### Prerequisiti del rack

L'enclosure 8100 è stata progettata per essere installata in un armadio rack standard da 48,26 cm (8100 pollici) con:

- Profondità minima di 70,71 cm (27,84 pollici) tra un montante del rack e un altro
- Peso massimo del dispositivo pari a 32 kg
- Contropressione massima di 5 Pascal (corrispondente alla pressione esercitata da una colonna di acqua di 0,5 mm).

### Kit per il montaggio su rack

Viene fornito un set di guide di montaggio da usare con l'armadio rack da 48,26 cm (19 pollici). Le guide sono state testate per sostenere il peso massimo dell'enclosure. Inoltre, grazie alla presenza delle guide, è possibile installare anche altre enclosure senza occupare spazio all'interno del rack.


#### Per installare il dispositivo sulle guide

2. Eseguire questo passaggio solo se nel dispositivo non sono installate guide interne. In genere, le guide sono preinstallate. In caso contrario, installare le slitte delle guide destra e sinistra ai lati dello chassis dell'enclosure. Tali slitte vengono fissate a ciascun lato con sei viti metriche. Per un migliore orientamento, le slitte delle guide sono contrassegnate con le diciture **LH – Front** (sinistra - anteriore) e **RH – Front** (destra - anteriore), mentre l'estremità a contatto con la parte posteriore dell'enclosure è affusolata.<br/>

    ![Fissaggio delle slitte delle guide allo chassis dell'enclosure](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

   **Fissaggio delle slitte delle guide interne ai lati dell'enclosure**

    	Label | Description
    	----- | -----------
    	1     | M 3x4 button-head screws
   		2     | Chassis slides


3. Fissare i gruppi guida destra esterna e guida sinistra esterna agli elementi verticali dell'armadio rack. Per un corretto orientamento, i supporti sono contrassegnati con le diciture **LH** (sinistra), **RH** (destra) e **This side up** (alto). 

4. Individuare i perni nella parte anteriore e posteriore del gruppo guida. Estendere la guida in modo da posizionarla tra i montanti del rack e inserire i perni nei fori degli elementi verticali dei montanti anteriori e posteriori. Assicurarsi che il gruppo guida sia in posizione orizzontale.

5. Fissare il gruppo guida agli elementi verticali del rack con due delle viti metriche fornite. Usare una vite per l'elemento anteriore e una per quello posteriore.

6. Ripetere la procedura per l'altro gruppo guida.<br/>

     ![Fissaggio delle slitte delle guide all'armadio rack](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Fissaggio dei gruppi guida esterna al rack**

     Etichetta | Descrizione
     ----- | -----------
     1 | Vite di fissaggio
     2 | Montante rack anteriore con fori quadrati
     3 | Perni di posizionamento della guida sinistra (parte anteriore)
     4 | Vite di fissaggio
     5 | Perni di posizionamento della guida sinistra (parte posteriore)


### Montaggio del dispositivo nel rack

Per montare il dispositivo nel rack usando le guide appena installate, seguire questa procedura.

#### Per montare il dispositivo

1. Con l'aiuto di un'altra persona, sollevare l'enclosure e allinearla alle guide del rack. 

2. Inserire con cura il dispositivo nelle guide e spingerlo nell'armadio rack completamente.<br/>

    ![Inserimento del dispositivo nel rack](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Montaggio del dispositivo nel rack**


3. Rimuovere i copriflangia anteriori sinistro e destro estraendoli dalle relative sedi. I copriflangia sono semplicemente inseriti sulle flange.

5. Fissare l'enclosure nel rack installando una vite a croce in ciascuna flangia, sinistra e destra.

4. Installare i copriflangia premendoli nella posizione corretta fino a inserirli completamente.<br/>

     ![Installazione dei copriflangia](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
 
    **Installazione dei copriflangia**

     Etichetta | Descrizione
     ----- | -----------
     1 | Vite di fissaggio dell'enclosure

Il passaggio successivo consiste nel cablare il dispositivo per l'alimentazione, l'accesso di rete e quello seriale.

## Cablare il dispositivo StorSimple 8100

In questo argomento vengono illustrate le procedure per cablare il dispositivo StorSimple 8100 per l'alimentazione, la connessione di rete e quella seriale.

### Prerequisiti

Prima di iniziare il cablaggio del dispositivo, è necessario avere a disposizione:

- Il dispositivo di archiviazione, interamente disimballato e montato in rack.

- 2 cavi di alimentazione inclusi con il dispositivo

- Accesso a 2 unità PDU (Power Distribution Unit) - consigliato

- Cavi di rete

- Cavi seriali forniti

- Convertitore seriale-USB con il driver appropriato installato sul PC (se necessario)

- Le 4 schede da QSFP a SFP+ fornite di serie, da usare con interfacce di rete 10 GbE

- [Hardware supportato per le interfacce di rete 10 GbE sul dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)


### Cablaggio di alimentazione

Il dispositivo include moduli PCM (Power and Cooling Modules) Entrambi i PCM devono essere installati e collegati a una fonte di alimentazione diversa per assicurare disponibilità elevata.

Per cablare il dispositivo per l'alimentazione, attenersi alla seguente procedura.

[AZURE.INCLUDE [storsimple-cavo-8100-per-alimentazione](../../includes/storsimple-cable-8100-for-power.md)]

### Cablaggio di rete

Il dispositivo presenta una configurazione di tipo attivo-standby: in qualsiasi momento uno dei moduli controller è attivo ed elabora tutte le operazioni su disco e di rete, mentre l'altro è in standby. In caso di malfunzionamento di un controller, quello in standby si attiva immediatamente e continua tutte le operazioni su disco e di rete.

Per supportare il failover del controller ridondante, è necessario cablare la rete del dispositivo come indicato nella procedura seguente.

#### Per cablare il dispositivo per la connessione di rete

1. Il dispositivo è dotato di sei interfacce di rete su ciascun controller: quattro porte Ethernet da 1 Gbps e due da 10 Gbps. Identificare le varie porte dati nel backplane del dispositivo.

    ![Backplane del dispositivo 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Parte posteriore del dispositivo con le porte dati**
 
     Etichetta | Descrizione
     ------- | -----------
     0,1,4,5 | Interfacce di rete da 1 GbE
     2,3 | Interfacce di rete da 10 GbE
     6 | Porte seriali

2. Per il cablaggio di rete vedere la seguente figura. La configurazione minima di rete è indicata da linee blu continue. La configurazione aggiuntiva richiesta per una disponibilità elevata e prestazioni ottimali è indicata da linee tratteggiate.

		
    ![Cablare il dispositivo 2U per la rete](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Cablaggio di rete per il dispositivo**

   
	|Etichetta | Descrizione |
    |----- | ----------- |
    | A | LAN con accesso a Internet |
    | B | Controller 0 |
    | C | PCM 0 |
    | D | Controller 1 |
    | E | PCM 1 |
    | F, G | Hosts |
    | 0-5 | Interfacce di rete |


	
Quando si effettua il cablaggio del dispositivo, la configurazione minima richiede:


- Almeno due interfacce di rete connesse in ogni controller, di cui una per l'accesso al cloud e l'altra per iSCSI. La porta DATA 0 viene abilitata automaticamente e configurata mediante la console seriale del dispositivo. Oltre a DATA 0, è presente anche un'altra porta dati che deve essere configurata mediante il portale di Azure. In questo caso, collegare la porta DATA 0 alla rete LAN principale (rete con accesso a Internet). Le altre porte dati possono essere collegate al segmento SAN/iSCSI LAN (VLAN) della rete, a seconda del ruolo previsto.

- Interfacce identiche in ogni controller collegato alla stessa rete, in modo da assicurare la disponibilità in seguito a un failover del controller. Se, ad esempio, si decide di collegare le porte DATA 0 e DATA 3 di uno dei controller, è necessario collegare anche le corrispondenti porte DATA 0 e DATA 3 dell'altro controller.
	
Da tenere presente per prestazioni e disponibilità elevate:


- Se possibile, configurare in ogni controller una coppia di interfacce di rete per l'accesso al cloud (1 GbE) e un'altra coppia per iSCSI (10 GbE consigliati). 

- Se possibile, collegare le interfacce di rete di ogni controller a due interruttori diversi per assicurare la disponibilità in caso di malfunzionamento di un interruttore. L'immagine illustra le due interfacce di rete 10 GbE, DATA 2 e DATA 3, di ogni controller connesso a due diversi interruttori.

Per altre informazioni, consultare la sezione relativa alle **interfacce di rete** nell'argomento sui [requisiti di disponibilità elevata per il dispositivo StorSimple](storsimple-system-requirements/#high-availability-requirements-for-storsimple.md).

>[AZURE.NOTE]Se si usano ricetrasmettitori SFP+ con interfacce di rete 10 GbE, usare le schede QSFP-SFP+ fornite di serie. Altre informazioni sono disponibili in [Hardware supportato per le interfacce di rete 10 GbE sul dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)
    

   
### Cablaggio della porta seriale

Per cablare la porta seriale, attenersi alla seguente procedura.

#### Per cablare il dispositivo per la connessione seriale

1. Il dispositivo è dotato di una porta seriale su ogni controller, identificata da un'icona a forma di chiave inglese. Fare riferimento alla figura disponibile nella sezione [Cablaggio di rete](#network-cabling) per individuare le porte seriali sulla scheda madre del dispositivo. 

2. Identificare il controller attivo sul backplane del dispositivo, indicato da un LED blu lampeggiante.

3. Usare il cavo seriale fornito (se necessario, usare il convertitore USB-seriale per il portatile) e collegare la console o il computer (con emulazione terminal al dispositivo) alla porta seriale del controller attivo.

4. Installare i driver seriali-USB (forniti con il dispositivo) sul computer.

5. Configurare la connessione seriale come segue: 115,200 baud, 8 bit di dati, 1 bit di stop, nessuna parità, set di controllo impostato su nessuno.

6. Verificare che la connessione funzioni premendo INVIO nella console. Deve comparire un menu della console seriale.

>[AZURE.NOTE]**Gestione Lights-Out:** quando il dispositivo è installato in un datacenter remoto o in una sala macchine con accesso limitato, verificare che le connessioni seriali di entrambi i controller siano sempre collegate a un interruttore della console seriale o ad apparecchiature simili. In questo modo, sono possibili operazioni di supporto e controllo remoto fuori banda in caso di interruzione della connessione di rete o malfunzionamenti imprevisti.

Il dispositivo è ora collegato per l'alimentazione, l'accesso di rete e quello seriale. Il passaggio successivo prevede la configurazione del software e la distribuzione del dispositivo.

## Passaggi successivi

Informazioni su come [distribuire e configurare un dispositivo StorSimple locale](storsimple-deployment-walkthrough.md)

<!---HONumber=Nov15_HO4-->