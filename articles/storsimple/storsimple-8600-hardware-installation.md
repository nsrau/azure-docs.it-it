<properties
   pageTitle="Installare il dispositivo StorSimple 8600 | Microsoft Azure"
   description="Viene descritto come disimballare, montare su rack e cablare il dispositivo StorSimple 8600 prima di distribuire e configurare il software."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />  

# Disimballaggio, montaggio su rack e cablaggio del dispositivo StorSimple 8600

## Overview
Microsoft Azure StorSimple 8600 è un dispositivo enclosure con doppio alloggiamento, costituito da un'enclosure principale e un'enclosure EBOD. In questa esercitazione viene illustrato come disimballare, montare su rack e cablare l'hardware del dispositivo StorSimple 8600 prima di configurarne il software.

## Disimballare il dispositivo StorSimple 8600

Nella seguente procedura vengono fornite istruzioni chiare e dettagliate su come disimballare il dispositivo di archiviazione StorSimple 8600. Il dispositivo viene fornito in due scatole, una per l'enclosure principale e l'altra per l'enclosure EBOD. Queste due scatole vengono poi inserite in una scatola singola.

### Preparazione del disimballaggio del dispositivo

Prima di disimballare il dispositivo, consultare le informazioni seguenti.


![Icona di avviso](./media/storsimple-safety/IC740879.png) ![icona peso elevato](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **AVVISO**

1. Assicurarsi che siano disponibili due persone per sostenere il peso del dispositivo, in caso debba essere trasportato manualmente. Un'enclosure completamente configurata può pesare fino a 32 kg.
1. Posizionare la scatola su una superficie piana e orizzontale.

Successivamente, completare la procedura seguente per disimballare il dispositivo.

#### Per disimballare il dispositivo

1. Controllare le scatole e le protezioni in schiuma per verificare l'eventuale presenza di ammaccature, tagli, danni provocati dall'acqua o altri danni evidenti. Se la scatola o le protezioni risultano danneggiate in modo grave, non aprire la confezione. [Contattare il Supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per istruzioni su come verificare il corretto funzionamento del dispositivo.

2. Aprire la scatola esterna ed estrarre le due scatole corrispondenti all’enclosure principale e all’enclosure EBOD. È ora possibile disimballare l'enclosure principale e l'enclosure EBOD. Nella figura seguente viene illustrato uno degli enclosure disimballato.

    ![Disimballare il dispositivo di archiviazione](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)

    **Dispositivo di archiviazione disimballato**

     Etichetta | Description
     ----- | -------------
     1 | Scatola
     2 | Cavi SAS (nel vassoio cavi e accessori)
     3 | Protezione inferiore
     4 | Dispositivo
     5 | Protezione superiore
     6 | Scatola accessori

3. Dopo aver disimballato le due scatole, assicurarsi di avere a disposizione:

  - 1 enclosure principale (l'enclosure principale e l'enclosure EBOD si trovano in due scatole separate)
  - 1 enclosure EBOD
  - 4 cavi di alimentazione, 2 in ciascuna scatola
  - 2 cavi SAS (per collegare enclosure principale ed EBOD)
  - 1 cavo Ethernet incrociato
  - 2 cavi console seriali
  - 1 convertitore seriale-USB per l'accesso seriale
  - 4 schede da QSFP a SFP da usare con interfacce di rete 10 GbE
  - 2 kit per il montaggio in rack (4 guide laterali con componenti di montaggio, 2 per l'enclosure principale e 2 per l'enclosure EBOD), 1 in ciascuna scatola
  - Guida introduttiva

    In caso di mancato ricevimento di uno qualsiasi degli elementi sopra elencati, [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).

Il passaggio successivo consiste nel montaggio su rack del dispositivo.

## Montare su rack il dispositivo StorSimple 8600

Attenersi alla procedura seguente per installare il dispositivo di archiviazione StorSimple 8600 in un rack standard da 48,26 cm (19 pollici) con montanti anteriori e posteriori. Il dispositivo è costituito da due enclosure, una principale e una EBOD, che devono essere montate su rack.

L'installazione si articola in più passaggi, ciascuno dei quali è descritto nelle seguenti procedure.

> [AZURE.IMPORTANT]
I dispositivi StorSimple devono essere montati su rack per il corretto funzionamento.



### Operazioni preliminari

Le enclosure devono essere installate su un rack standard da 48,26 cm (19 pollici) con montanti anteriori e posteriori. Utilizzare la procedura seguente per predisporre l'installazione del rack.

#### Per predisporre l'ambiente per l'installazione del rack

1. Assicurarsi che l'enclosure principale e l'enclosure EBOD siano collocate in sicurezza su una superficie di lavoro (o simile) orizzontale, stabile e piana.

2. Verificare che nell'ambiente in cui si intende effettuare l'installazione sia disponibile una fonte di alimentazione CA standard indipendente o un'unità PDU (Power Distribution Unit) rack con un gruppo di continuità.

3. Assicurarsi che nel rack in cui si intende montare le enclosure sia disponibile uno slot 4U (2 X 2U).

![Icona di avviso](./media/storsimple-safety/IC740879.png) ![icona peso elevato](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **AVVISO**

 Se si prevede di trasportare il dispositivo a mano, assicurarsi che siano disponibili due persone. Un'enclosure completamente configurata può pesare fino a 32 kg.

### Prerequisiti del rack

Le enclosure sono progettate per essere installate in un armadio rack standard da 48,26 cm (19 pollici) con:

- Profondità minima di 70,71 cm (27,84 pollici) tra un montante del rack e un altro
- Peso massimo del dispositivo pari a 32 kg
- Contropressione massima di 5 Pascal (corrispondente alla pressione esercitata da una colonna di acqua di 0,5 mm)

### Kit per il montaggio su rack

Viene fornito un set di guide di montaggio da usare con l'armadio rack da 48,26 cm (19 pollici). Le guide sono state testate per sostenere il peso massimo dell'enclosure. Inoltre, grazie alla presenza delle guide, è possibile installare anche altre enclosure senza occupare spazio all'interno del rack. Iniziare con l'installazione dell'enclosure EBOD.

#### Per installare l'enclosure EBOD sulle guide

2. Eseguire questo passaggio solo se non ci sono guide interne installate sul dispositivo. In genere, le guide sono preinstallate. In caso contrario, installare le slitte delle guide destra e sinistra ai lati dello chassis dell'enclosure. Tali slitte vengono fissate a ciascun lato con sei viti metriche. Per un migliore orientamento, le slitte delle guide sono contrassegnate con le diciture **LH – Front** (sinistra - anteriore) e **RH – Front** (destra - anteriore), mentre l'estremità a contatto con la parte posteriore dell'enclosure è affusolata.

    ![Fissaggio delle slitte delle guide allo chassis dell'enclosure](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Fissaggio delle slitte delle guide ai lati dell’enclosure**

    Etichetta | Descrizione
    ----- | -----------
    1 | Viti con testa a bottone M 3X4
    2 | Guide dello chassis

3. Fissare i gruppi guida destra e guida sinistra agli elementi verticali dell'armadio rack. Per un corretto orientamento, i supporti sono contrassegnati con le diciture **LH** (sinistra), **RH** (destra) e **This side up** (alto).

4. Individuare i perni nella parte anteriore e posteriore del gruppo guida. Estendere la guida in modo da posizionarla tra i montanti del rack e inserire i perni nei fori degli elementi verticali dei montanti anteriori e posteriori. Assicurarsi che il gruppo guida sia in posizione orizzontale.

5. Fissare il gruppo guida agli elementi verticali del rack con due delle viti metriche fornite. Usare una vite per l'elemento anteriore e una per quello posteriore.

6. Ripetere la procedura per l'altro gruppo guida.

     ![Fissaggio delle slitte delle guide all'armadio rack](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Fissaggio dei gruppi guida al rack**

     Etichetta | Descrizione
     ----- | -----------
     1 | Vite di fissaggio
     2 | Montante rack anteriore con fori quadrati
     3 | Perni di posizionamento della guida sinistra (parte anteriore)
     4 | Vite di fissaggio
     5 | Perni di posizionamento della guida sinistra (parte posteriore)

### Montaggio dell'enclosure EBOD nel rack

Per montare l'enclosure EBOD nel rack usando le guide appena installate, seguire questa procedura.

#### Per montare l'enclosure EBOD

1. Con l'aiuto di un'altra persona, sollevare l'enclosure e allinearla alle guide del rack.

2. Inserire con cura l'enclosure nelle guide e spingerla nell'armadio rack completamente.

    ![Inserimento del dispositivo nel rack](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Montaggio dell'enclosure nel rack**

3. Rimuovere i copriflangia anteriori sinistro e destro estraendoli dalle relative sedi. I copriflangia sono semplicemente inseriti sulle flange.

4. Fissare l'enclosure nel rack installando una vite a croce in ciascuna flangia, sinistra e destra.

4. Installare i copriflangia premendoli nella posizione corretta fino a inserirli completamente.

     ![Installazione dei copriflangia](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)

    **Installazione dei copriflangia**

     Etichetta | Descrizione
     ----- | -----------
     1 | Vite di fissaggio dell'enclosure


### Montaggio dell'enclosure principale nel rack

Una volta terminato il montaggio dell'enclosure EBOD, sarà necessario montare l'enclosure principale seguendo la stessa procedura.

> [AZURE.NOTE]
>
> - È possibile che tra l'enclosure principale e l'enclosure EBOD nel rack siano presenti alcuni slot vuoti.
> - Per collegare l'enclosure principale all'enclosure EBOD, usare il cavo SAS da 2 metri fornito.
> - Non esistono vincoli di posizionamento dell'unità principale rispetto all'unità EBOD. Pertanto, l'enclosure principale può essere posizionata nello slot superiore e l'enclosure EBOD sotto o viceversa.

Il passaggio successivo consiste nel cablare il dispositivo per l'alimentazione, l'accesso di rete e quello seriale.

## Cablare il dispositivo StorSimple 8600

In questo argomento vengono illustrate le procedure per cablare il dispositivo StorSimple 8600 per l'alimentazione, la connessione di rete e quella seriale.

### Prerequisiti

Prima di iniziare il cablaggio del dispositivo, è necessario avere a disposizione:

- L'enclosure principale e l'enclosure EBOD, completamente disimballate
- 4 cavi di alimentazione (2 per l'enclosure principale e 2 per l'enclosure EBOD) forniti con il dispositivo
- 2 cavi SAS forniti con il dispositivo per collegare l'enclosure EBOD all'enclosure principale
- Accesso a 2 unità PDU (Power Distribution Unit) - consigliato
- Cavi di rete
- Cavi seriali forniti
- Convertitore seriale-USB con il driver appropriato installato sul PC (se necessario)
- 4 schede da QSFP a SFP fornite da usare con interfacce di rete 10 GbE
- [Hardware supportato per le interfacce di rete 10 GbE sul dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### SAS e i cavi di alimentazione

Il dispositivo è costituito da un'enclosure principale e un'enclosure EBOD. Questa operazione richiede che le unità siano cablate insieme per l'alimentazione e la connettività Serial Attached SCSI (SAS).

Quando si configura il dispositivo per la prima volta, eseguire prima i passaggi per il cablaggio SAS e poi completare i passaggi per il cablaggio di alimentazione.

[AZURE.INCLUDE [storsimple cavo-8600-per-SAS](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple cavo-8600-per-alimentazione](../../includes/storsimple-cable-8600-for-power.md)]

### Cablaggio di rete

Il dispositivo presenta una configurazione di tipo attivo-standby: in qualsiasi momento uno dei moduli controller è attivo ed elabora tutte le operazioni su disco e di rete, mentre l'altro è in standby. In caso di malfunzionamento di un controller, quello in standby si attiva immediatamente e continua tutte le operazioni su disco e di rete.

Per supportare il failover del controller ridondante, è necessario cablare la rete del dispositivo come indicato nella procedura seguente.

#### Per cablare il dispositivo per la connessione di rete

1. Il dispositivo è dotato di sei interfacce di rete su ciascun controller: quattro porte Ethernet da 1 Gbps e due da 10 Gbps. Fare riferimento alla figura seguente per identificare le porte dati nel backplane del dispositivo.

     ![Backplane del dispositivo 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Parte posteriore del dispositivo con le porte dati**

     Etichetta | Descrizione
     ------- | -----------
     0,1,4,5 | Interfacce di rete da 1 GbE
     2,3 | Interfacce di rete da 10 GbE
     6 | Porte seriali



1. Per il cablaggio di rete vedere la seguente figura. La configurazione minima di rete è indicata da linee blu continue. Per una disponibilità elevata e prestazioni ottimali, la configurazione aggiuntiva richiesta è indicata da linee tratteggiate.

![Cablare il dispositivo 4U per la rete](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Cablaggio di rete per il dispositivo**

Etichetta | Descrizione
----- | -----------
A | LAN con accesso a Internet
B | Controller 0
C | PCM 0
D | Controller 1
E | PCM 1
F | Controller 0 EBOD
G | Controller 1 EBOD
H,I | Host (ad esempio, file server)
0-5 | Interfacce di rete
6 | Enclosure principale
7 | Chassis EBOD

Quando si effettua il cablaggio del dispositivo, la configurazione minima richiede:


- Almeno due interfacce di rete connesse in ogni controller, di cui una per l'accesso al cloud e l'altra per iSCSI. La porta DATA 0 viene abilitata automaticamente e configurata mediante la console seriale del dispositivo. Oltre a DATA 0, è presente anche un'altra porta dati che deve essere configurata mediante il portale di Azure classico. In questo caso, collegare la porta DATA 0 alla rete LAN principale (rete con accesso a Internet). Le altre porte dati possono essere collegate al segmento SAN/iSCSI LAN (VLAN) della rete, a seconda del ruolo previsto.

- Interfacce identiche in ogni controller collegato alla stessa rete, in modo da assicurare la disponibilità in seguito a un failover del controller. Se, ad esempio, si decide di collegare le porte DATA 0 e DATA 3 di uno dei controller, è necessario collegare anche le corrispondenti porte DATA 0 e DATA 3 dell'altro controller.

Da tenere presente per prestazioni e disponibilità elevate:


- Se possibile, configurare in ogni controller una coppia di interfacce di rete per l'accesso al cloud (1 GbE) e un'altra coppia per iSCSI (10 GbE consigliati).

- Se possibile, collegare le interfacce di rete di ogni controller a due interruttori diversi per assicurare la disponibilità in caso di malfunzionamento di un interruttore. L'immagine illustra le due interfacce di rete 10 GbE, DATA 2 e DATA 3, di ogni controller connesso a due diversi interruttori. Per altre informazioni, consultare le **interfacce di rete** sotto i [requisiti di disponibilità elevata per il dispositivo StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Se si usano ricetrasmettitori SFP+ con interfacce di rete 10 GbE, usare le schede QSFP-SFP+ fornite di serie. Per altre informazioni, passare a [Hardware supportato per le interfacce di rete 10 GbE sul dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

### Cablaggio della porta seriale

Per cablare la porta seriale, attenersi alla seguente procedura.

#### Per cablare il dispositivo per la connessione seriale

1. Il dispositivo è dotato di una porta seriale su ogni controller, identificata da un'icona a forma di chiave inglese. Per individuare le porte seriali, vedere la figura che mostra le porte dati sul retro del dispositivo.

2. Identificare il controller attivo sul backplane del dispositivo, indicato da un LED blu lampeggiante.

3. Usare il cavo seriale fornito (se necessario, usare il convertitore USB-seriale per il portatile) e collegare la console o il computer (con emulazione terminal al dispositivo) alla porta seriale del controller attivo.

4. Installare i driver seriali-USB (forniti con il dispositivo) sul computer.

5. Configurare la connessione seriale come segue:
   - 115\.200 baud
   - 8 bit di dati
   - 1 bit di stop
   - Nessuna parità
   - Flusso di controllo impostato su **Nessuno**

6. Verificare che la connessione funzioni premendo INVIO nella console. Deve comparire un menu della console seriale.

> [AZURE.NOTE] **Gestione Lights-Out:** quando il dispositivo è installato in un datacenter remoto o in una sala macchine con accesso limitato, verificare che le connessioni seriali di entrambi i controller siano sempre collegate a un interruttore della console seriale o ad apparecchiature simili. In questo modo, sono possibili operazioni di supporto e controllo remoto fuori banda in caso di interruzione della connessione di rete o malfunzionamenti imprevisti.

Il cablaggio del dispositivo per l'alimentazione, l'accesso di rete e la connessione seriale sono stati completati. Il passaggio successivo consiste nella configurazione del software sul dispositivo.

## Passaggi successivi

A questo punto è possibile [distribuire e configurare il dispositivo StorSimple locale](storsimple-deployment-walkthrough.md).

<!---HONumber=AcomDC_0817_2016-->