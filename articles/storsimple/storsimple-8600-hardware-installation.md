<properties 
   pageTitle="Installare il dispositivo StorSimple 8600"
   description="In questo argomento viene descritto come disimballare, montare su rack e cablare il dispositivo StorSimple 8600."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/09/2015"
   ms.author="v-sharos" />

# Installare il dispositivo StorSimple 8600

## Panoramica
Microsoft Azure StorSimple 8600 è un dispositivo enclosure con doppio alloggiamento, costituito da un'enclosure principale e un'enclosure EBOD. In questa esercitazione viene illustrato come disimballare, montare su rack e cablare l'hardware del dispositivo StorSimple 8600 prima di configurarne il software.

## Disimballare il dispositivo StorSimple 8600

Nella seguente procedura vengono fornite istruzioni chiare e dettagliate su come disimballare il dispositivo di archiviazione StorSimple 8600. Il dispositivo viene fornito in due scatole separate, una per l'enclosure principale e l'altra per l'enclosure EBOD.

### Preparazione del disimballaggio del dispositivo

Prima di disimballare il dispositivo, consultare le informazioni seguenti.

>[AZURE.WARNING]![icona peso elevato](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png)
>
> 1. Assicurarsi che siano disponibili due persone per sostenere il peso del dispositivo, in caso debba essere trasportato manualmente. Un dispositivo completamente configurato può pesare fino a 32 kg.
>
> 2. Posizionare la scatola su una superficie piana e orizzontale.

Successivamente, completare la procedura seguente per disimballare il dispositivo.

#### Per disimballare il dispositivo

1. Controllare le scatole e le protezioni in schiuma per verificare l'eventuale presenza di ammaccature, tagli, danni provocati dall'acqua o altri danni evidenti. Se la scatola o le protezioni risultano danneggiate in modo grave, non aprire la confezione. Contattare il Supporto tecnico Microsoft per istruzioni su come verificare il corretto funzionamento del dispositivo.

2. Aprire le due scatole e disimballare l'enclosure principale e l'enclosure EBOD. Nella figura seguente viene illustrato il dispositivo di archiviazione disimballato.
 
    **Figura 1: dispositivo di archiviazione disimballato**

    ![Disimballare il dispositivo di archiviazione](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)

     Etichetta | Descrizione 
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
  - 4 schede da QSFP a SFP singole da usare con interfacce di rete 10 GbE
  - 2 kit per il montaggio in rack (4 guide laterali con componenti di montaggio, 2 per l'enclosure principale e 2 per l'enclosure EBOD), 1 in ciascuna scatola
  - Guida introduttiva

    In caso di mancato ricevimento di uno qualsiasi degli elementi sopra elencati, contattare il Supporto Tecnico Microsoft.

Il passaggio successivo consiste nel montaggio su rack del dispositivo.

## Montare su rack il dispositivo StorSimple 8600

Attenersi alla procedura seguente per installare il dispositivo di archiviazione StorSimple 8600 in un rack standard da 48,26 cm (19 pollici) con montanti anteriori e posteriori. Il dispositivo è costituito da due enclosure, una principale e una EBOD, che devono essere montate su rack.

L'installazione si articola in più passaggi, ciascuno dei quali è descritto nelle seguenti procedure.

### Operazioni preliminari

Le enclosure devono essere installate su un rack standard da 48,26 cm (19 pollici) con montanti anteriori e posteriori. Utilizzare la procedura seguente per predisporre l'installazione del rack.

#### Per predisporre l'ambiente per l'installazione del rack

1. Assicurarsi che l'enclosure principale e l'enclosure EBOD siano collocate in sicurezza su una superficie di lavoro (o simile) orizzontale, stabile e piana.

2. Verificare che nell'ambiente in cui si intende effettuare l'installazione sia disponibile una fonte di alimentazione CA standard indipendente o un'unità PDU (Power Distribution Unit) rack con un gruppo di continuità.

3. Assicurarsi che nel rack in cui si intende montare le enclosure sia disponibile uno slot 4U (2 X 2U).

>[AZURE.WARNING]![icona peso elevato](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png)
> 
> Se si prevede di trasportare il dispositivo a mano, assicurarsi che siano disponibili due persone. Un'enclosure completamente configurata può pesare fino a 32 kg.

### Prerequisiti del rack

Le enclosure sono progettate per essere installate in un armadio rack standard da 48,26 cm (19 pollici) con:

- Profondità minima di 70,71 cm (27,84 pollici) tra un montante del rack e un altro
- Peso massimo del dispositivo pari a 32 kg
- Contropressione massima di 5 Pascal (corrispondente alla pressione esercitata da una colonna di acqua di 0,5 mm)

### Kit per il montaggio su rack

Viene fornito un set di guide di montaggio da usare con l'armadio rack da 48,26 cm (19 pollici). Le guide sono state testate per sostenere il peso massimo dell'enclosure. Inoltre, grazie alla presenza delle guide, è possibile installare anche altre enclosure senza occupare spazio all'interno del rack. Iniziare con l'installazione dell'enclosure EBOD.

#### Per installare l'enclosure EBOD sulle guide

1. Tenendo l'enclosure sulla superficie di lavoro, rimuovere i copriflangia anteriori sinistro e destro estraendoli dalle relative sedi. I copriflangia sono semplicemente inseriti sulle flange.

2. In genere, le guide sono preinstallate. In caso contrario, installare le slitte delle guide destra e sinistra ai lati dello chassis dell'enclosure. Tali slitte vengono fissate a ciascun lato con sei viti metriche. Per un migliore orientamento, le slitte delle guide sono contrassegnate con le diciture **LH – Front** (sinistra - anteriore) e **RH – Front** (destra - anteriore), mentre l'estremità a contatto con la parte posteriore dell'enclosure è affusolata.

    **Figura 2: fissaggio delle slitte delle guide ai lati dell'enclosure**

    ![Fissaggio delle slitte delle guide allo chassis dell'enclosure](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    Etichetta | Descrizione
    ----- | -----------
    1 | Viti con testa a bottone M 3X4
    2 | Guide dello chassis
 
3. Fissare i gruppi guida destra e guida sinistra agli elementi verticali dell'armadio rack. Per un corretto orientamento, i supporti sono contrassegnati con le diciture **LH** (sinistra), **RH** (destra) e **This side up** (alto).

4. Individuare i perni nella parte anteriore e posteriore del gruppo guida. Estendere la guida in modo da posizionarla tra i montanti del rack e inserire i perni nei fori degli elementi verticali dei montanti anteriori e posteriori. Assicurarsi che il gruppo guida sia in posizione orizzontale.

5. Fissare il gruppo guida agli elementi verticali del rack con due delle viti metriche fornite. Usare una vite per l'elemento anteriore e una per quello posteriore.

6. Ripetere la procedura per l'altro gruppo guida.

    **Figura 3: fissaggio dei gruppi guida al rack**

     ![Fissaggio delle slitte delle guide all'armadio rack](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

     Etichetta | Descrizione
     ----- | -----------
     1 | Vite di fissaggio
     2 | Montante rack anteriore con fori quadrati
     3 | Perni di posizionamento della guida sinistra (parte anteriore)
     4 | Vite di fissaggio
     5 | Perni di posizionamento della guida destra (parte anteriore)

### Montaggio dell'enclosure EBOD nel rack 

Per montare l'enclosure EBOD nel rack usando le guide appena installate, seguire questa procedura.

#### Per montare l'enclosure EBOD

1. Con l'aiuto di un'altra persona, sollevare l'enclosure e allinearla alle guide del rack. 

2. Inserire con cura l'enclosure nelle guide e spingerla nell'armadio rack completamente.

    **Figura 4: montaggio dell'enclosure nel rack**

    ![Inserimento del dispositivo nel rack](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

3. Fissare l'enclosure nel rack installando una vite a croce in ciascuna flangia, sinistra e destra.

4. Installare i copriflangia premendoli nella posizione corretta fino a inserirli completamente.

    **Figura 5: installazione dei copriflangia**

     ![Installazione dei copriflangia](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
 
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
- Adattatori singoli QSFP-SFP forniti per l'uso con interfacce di rete da 10 GbE
- [Ricetrasmettitori, cavi e commutatori supportati per le interfacce di rete 10 GbE](https://msdn.microsoft.com/library/azure/dn891474.aspx) 

### Cablaggio SAS

Il dispositivo è costituito da un'enclosure principale e un'enclosure EBOD, che devono essere collegate mediante i cavi SAS.

Sarà quindi necessario identificare l'enclosure principale e l'enclosure EBOD.

#### Per identificare l'enclosure principale e l'enclosure EBOD

1. Identificare l'enclosure principale e l'enclosure EBOD. Le due enclosure possono essere identificate osservandone il retro. Per indicazioni, vedere la seguente figura.

    **Figura 6: parte posteriore delle enclosure principale ed EBOD**

    ![Enclosure principale ed enclosure EBOD](./media/storsimple-8600-hardware-installation/HCSBackplaneofprimaryandEBODenclosure.png)

     Etichetta | Descrizione
     ----- | -----------
     1 | Enclosure principale
     2 | Enclosure EBOD


2. Individuare i numeri di serie sulle enclosure principale ed EBOD. L'adesivo con il numero di serie è attaccato alla sporgenza posteriore di ogni enclosure. I due numeri di serie delle enclosure devono essere identici. Se non corrispondono, contattare subito il Supporto Tecnico Microsoft. Per individuare i numeri di serie più facilmente, vedere la seguente figura.

    **Figura 7: parte posteriore del dispositivo con la posizione del numero di serie**

    ![Posizione del numero di serie](./media/storsimple-8600-hardware-installation/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)

     Etichetta | Descrizione
     ----- | -----------
     1 | Sporgenza dell'enclosure


Successivamente, usare i cavi SAS forniti per collegare l'enclosure EBOD all'enclosure principale.

#### Per collegare l'enclosure principale e l'enclosure EBOD

1. Identificare le quattro porte SAS (Serial Attached SCSI) sull'enclosure principale e sull'enclosure EBOD. Le porte SAS sono contrassegnate con la dicitura EBOD sull'enclosure principale e con la dicitura CTRL sull'enclosure EBOD, come illustrato nella figura 8.

    **Figura 8: posizione delle porte SAS sulle enclosure principale ed EBOD**

    ![Posizione delle porte SAS](./media/storsimple-8600-hardware-installation/HCSSAScablingforyourdevice.png)

     Etichetta | Descrizione
     ----- | -----------
     A | Enclosure principale
     B | Enclosure EBOD
     1 | Controller 0
     2 | Controller 1
     3 | Controller 0 EBOD
     4 | Controller 1 EBOD
     5,6 | Porte SAS sull'enclosure principale (contrassegnate con la dicitura EBOD)
     7,8 | Porte SAS sull'enclosure EBOD (contrassegnate con la dicitura CTRL)

2. Usare i cavi SAS forniti per collegare le porte EBOD alle porte CTRL.

    La porta EBOD sul controller 0 deve essere collegata alla porta CTRL sul controller 0 EBOD. La porta EBOD sul controller 1 deve essere collegata alla porta CTRL sul controller 1 EBOD. Per informazioni aggiuntive, vedere la figura 8.

### Cablaggio di alimentazione

L'enclosure principale e l'enclosure EBOD includono due moduli di alimentazione e raffreddamento PCM (Power and Cooling Module) ridondanti. Ogni modulo PCM di ogni enclosure deve essere installato e collegato a una fonte di alimentazione diversa per assicurare disponibilità elevata.

Per cablare il dispositivo per l'alimentazione, attenersi alla seguente procedura.

#### Per cablare il dispositivo per l'alimentazione

1. Assicurarsi che gli interruttori di alimentazione di tutti i moduli PCM siano in posizione OFF.

2. Per l'enclosure principale, collegare i cavi di alimentazione a entrambi i moduli PCM. Nella seguente figura i cavi di alimentazione sono identificati in rosso.

3. Verificare che i due moduli PCM dell'enclosure principale usino fonti di alimentazione separate.

4. Collegare i cavi di alimentazione alle unità PDU (Power Distribution Unit) rack come illustrato nella seguente figura.

5. Ripetere i passaggi da 2 a 4 per l'enclosure EBOD.

6. Accendere l'enclosure EBOD girando l'interruttore di alimentazione di ciascun modulo PCM su ON.

7. Verificare che l'enclosure EBOD sia attiva assicurandosi che i LED dei controller EBOD (di colore verde sul retro dell'enclosure) siano accesi.

8. Accendere l'enclosure principale girando l'interruttore di ciascun modulo PCM su ON.

9. Verificare che il sistema sia attivo assicurandosi che i LED dei controller del dispositivo siano accesi.

10. Verificare che il collegamento tra il controller EBOD e il controller del dispositivo sia attivo, assicurandosi che i LED SAS (4 accanto alla porta SAS) sul controller EBOD siano verdi.

> [AZURE.IMPORTANT]Per garantire la disponibilità elevata del sistema, si consiglia di attenersi allo schema di cablaggio dell'alimentazione illustrato nella seguente figura.

   **Figura 9: cablaggio di alimentazione per il dispositivo**

   ![Figura del cablaggio di alimentazione](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforPower.png)

     Label | Description
     ----- | -----------
     1     | Primary enclosure
     2     | PCM 0
     3     | PCM 1
     4     | Controller 0
     5     | Controller 1
     6     | EBOD controller 0
     7     | EBOD controller 1
     8     | EBOD enclosure
     9     | PDUs
 

### Cablaggio di rete

Il dispositivo presenta una configurazione di tipo attivo-standby: in qualsiasi momento uno dei moduli controller è attivo ed elabora tutte le operazioni su disco e di rete, mentre l'altro è in standby. In caso di malfunzionamento di un controller, quello in standby si attiva immediatamente e continua tutte le operazioni su disco e di rete.

Per supportare il failover del controller ridondante, è necessario cablare la rete del dispositivo come indicato nella procedura seguente.

#### Per cablare il dispositivo per la connessione di rete

1. Il dispositivo è dotato di sei interfacce di rete su ciascun controller: quattro porte Ethernet da 1 Gbps e due da 10 Gbps. Fare riferimento alla figura seguente per identificare le porte dati nel backplane del dispositivo.

    **Figura 10: parte posteriore del dispositivo con le porte dati**

     ![Backplane del dispositivo 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
 
     Etichetta | Descrizione
     ------- | -----------
     0,1,4,5 | Interfacce di rete da 1 GbE
     2,3 | Interfacce di rete da 10 GbE
     6 | Porte seriali

2. Per una disponibilità elevata, il dispositivo richiede almeno due connessioni per ogni controller.
    1. La porta DATA 0 viene abilitata automaticamente e configurata mediante la console seriale del dispositivo. Oltre a DATA 0, è presente anche un'altra porta dati che deve essere configurata mediante il portale di gestione.
    2. Individuare le interfacce di rete identiche su ciascun controller. Se, ad esempio, si decide di collegare le porte DATA 0 e DATA 3 di uno dei controller, è necessario collegare anche le corrispondenti porte DATA 0 e DATA 3 dell'altro controller. 

3. Per una disponibilità elevata, assicurarsi di collegare:
   1. Le interfacce identiche di ogni controller alla rete pertinente per assicurare la disponibilità in seguito a un malfunzionamento del controller.
   2. Le interfacce di ogni controller ad almeno due interruttori diversi per assicurare la disponibilità in seguito a un malfunzionamento dell'interruttore.
   3. La porta DATA 0 alla rete LAN principale (rete con accesso a Internet). Le altre porte dati possono essere collegate al segmento SAN/iSCSI LAN (VLAN) della rete, a seconda del ruolo previsto.

Configurare almeno un'interfaccia di rete per l'accesso cloud e una per iSCSI. Per una disponibilità elevata e prestazioni ottimali, configurare due coppie di interfacce di rete su ciascun controller. Per il cablaggio di rete vedere la seguente figura. La configurazione minima di rete è indicata da linee blu continue. Per una disponibilità elevata e prestazioni ottimali, la configurazione aggiuntiva richiesta è indicata da linee tratteggiate.

**Figura 11: cablaggio di rete per il dispositivo**

![Cablare il dispositivo 4U per la rete](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

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
7 | Enclosure EBOD

### Cablaggio della porta seriale

Per cablare la porta seriale, attenersi alla seguente procedura.

#### Per cablare il dispositivo per la connessione seriale

1. Il dispositivo è dotato di una porta seriale su ogni controller, identificata da un'icona a forma di chiave inglese. Fare riferimento alla figura 10 per individuare le porte seriali sul backplane del dispositivo.

2. Identificare il controller attivo sul backplane del dispositivo, indicato da un LED blu lampeggiante.

3. Usare il cavo seriale fornito (se necessario, usare il convertitore USB-seriale per il portatile) e collegare la console o il computer (con emulazione terminal al dispositivo) alla porta seriale del controller attivo.

4. Installare i driver seriali-USB (forniti con il dispositivo) sul computer.

5. Configurare la connessione seriale come segue:
   - 115.200 baud
   - 8 bit di dati
   - 1 bit di stop
   - Nessuna parità
   - Flusso di controllo impostato su **Nessuno**

6. Verificare che la connessione funzioni premendo INVIO nella console. Deve comparire un menu della console seriale.

> [AZURE.NOTE]**Gestione Lights-Out:** quando il dispositivo è installato in un datacenter remoto o in una sala macchine con accesso limitato, verificare che le connessioni seriali di entrambi i controller siano sempre collegate a un interruttore della console seriale o ad apparecchiature simili. In questo modo, sono possibili operazioni di supporto e controllo remoto fuori banda in caso di interruzione della connessione di rete o malfunzionamenti imprevisti.

Il cablaggio del dispositivo per l'alimentazione, l'accesso di rete e la connessione seriale sono stati completati. Il passaggio successivo consiste nella configurazione del software sul dispositivo.

## Passaggi successivi

A questo punto è possibile [distribuire e configurare il dispositivo StorSimple locale](storsimple-deployment-walkthrough.md)
 

<!---HONumber=62-->