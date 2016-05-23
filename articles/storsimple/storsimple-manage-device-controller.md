<properties
   pageTitle="Gestire i controller del dispositivo StorSimple | Microsoft Azure"
   description="Informazioni su come interrompere, riavviare, arrestare o reimpostare i controller del dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/25/2016"
   ms.author="alkohli" />

# Gestire i controller del dispositivo StorSimple

## Panoramica

In questa esercitazione vengono descritte le diverse operazioni che è possibile eseguire nei controller del dispositivo StorSimple. I controller del dispositivo StorSimple sono controller ridondanti (peer) in una configurazione attiva-passiva. In un determinato momento, un solo controller è attivo e sta elaborando tutte le operazioni su disco e di rete. L'altro controller è in modalità passiva. Se il controller attivo non restituisce l'esito positivo, il controller passivo diventa automaticamente attivo.

In questa esercitazione sono incluse le istruzioni dettagliate per gestire i controller del dispositivo tramite:

- La sezione **Controller** della pagina **Manutenzione** del servizio StorSimple Manager
- Windows PowerShell per StorSimple.

Si consiglia di gestire i controller dei dispositivi tramite il servizio StorSimple Manager. Se un'azione può essere eseguita solo utilizzando Windows PowerShell per StorSimple, nell'esercitazione si esegue una nota.

Dopo aver letto questa esercitazione, si sarà in grado di:

- Riavviare o arrestare il controller di un dispositivo StorSimple
- Arrestare un dispositivo StorSimple
- Ripristinare le impostazioni predefinite del dispositivo StorSimple


## Riavviare o arrestare un singolo controller

Il riavvio o l'arresto del controller non è richiesto come parte del funzionamento normale del sistema. Le operazioni di arresto per un singolo controller del dispositivo sono comuni solo nei casi in cui si richiede la sostituzione di un componente hardware mal funzionante del dispositivo. Il riavvio del controller potrebbe essere necessario anche quando le prestazioni sono influenzate dall'utilizzo eccessivo della memoria o da un controller che non funziona correttamente. Potrebbe inoltre essere necessario eseguire il riavvio dopo la sostituzione di un controller, se si desidera attivare e testare il controller sostituito.

Il riavvio di un dispositivo non è un'operazione problematica per gli iniziatori connessi, supponendo che il controller passivo sia disponibile. Se un controller passivo non è disponibile o è spento, il riavvio del controller attivo potrebbe comportare l'interruzione del servizio e tempi di inattività.

> [AZURE.IMPORTANT]

> - **Un controller in esecuzione non deve mai essere fisicamente rimosso poiché potrebbe causare una perdita di ridondanza e un maggior rischio di tempi di inattività.**

> - La procedura seguente si applica solo al dispositivo StorSimple fisico. Per informazioni su come avviare, arrestare e riavviare il dispositivo virtuale, vedere [Utilizzare il dispositivo virtuale](storsimple-virtual-device-u1.md#work-with-the-storsimple-virtual-device).

È possibile riavviare o arrestare un singolo controller del dispositivo mediante il portale di Azure classico del servizio StorSimple Manager o Windows PowerShell per StorSimple

Per gestire i controller del dispositivo dal portale di Azure classico, effettuare le seguenti operazioni.

#### Per riavviare o arrestare un controller nel portale di Azure classico

1. Andare a **Dispositivi > Manutenzione**.

1. Andare a **Stato hardware** e verificare che lo stato di entrambi i controller del dispositivo sia **integro**.

	![Verificare l'integrità dei controller del dispositivo StorSimple](./media/storsimple-manage-device-controller/IC766017.png)

1. Nella parte inferiore della pagina **Manutenzione** fare clic su **Gestisci controller**.

	![Gestire i controller del dispositivo StorSimple](./media/storsimple-manage-device-controller/IC766018.png)</br>

	>[AZURE.NOTE] Se non è possibile visualizzare **Gestisci controller**, è necessario installare gli aggiornamenti. Per altre informazioni, vedere [Aggiornare il dispositivo StorSimple](storsimple-update-device.md).

1. Nella finestra di dialogo **Modifica impostazioni controller**, effettuare le seguenti operazioni:
    1. Dall'elenco a discesa **Seleziona controller**, selezionare il controller che si desidera gestire. Le opzioni sono Controller 0 e Controller 1. Questi controller vengono identificati anche come attivo o passivo.

		>[AZURE.NOTE] Un controller non può essere gestito se non è disponibile o è spento e quindi non verrà visualizzato nell'elenco a discesa.

    2. Dall'elenco a discesa **Seleziona azione** scegliere **Riavvia controller** o **Arresta controller**.

		![Riavviare il controller passivo del dispositivo StorSimple](./media/storsimple-manage-device-controller/IC766020.png)
    3. Fare clic sull’icona del segno di spunta ![Icona del segno di spunta](./media/storsimple-manage-device-controller/IC740895.png).

Il controller verrà riavviato o arrestato. Nella tabella seguente sono riepilogati i dettagli di ciò che accade in base alle selezioni effettuate nella finestra di dialogo **Modifica impostazioni controller**.


|Nr. selezione|Se si sceglie di...|Viene eseguita questa operazione.|
|---|---|---|
|1\.|Riavviare il controller passivo.|Viene creato un processo per riavviare il controller e si riceve una notifica una volta che il processo è stato creato. A questo punto viene inizializzato il riavvio del controller. È possibile monitorare il processo di riavvio accedendo a **Servizio > Dashboard > Visualizza registri operazioni** e quindi filtrare in base a specifici parametri del servizio.|
|2\.|Riavviare il controller attivo.|Verrà visualizzato l'avviso seguente: "Se si riavvia il controller attivo, verrà eseguito il failover del dispositivo sul controller passivo. Continuare?" </br>Se si sceglie di procedere con questa operazione, i passaggi successivi saranno identici a quelli utilizzati per riavviare il controller passivo (vedere la selezione 1).|
|3\.|Arrestare il controller passivo.|Verrà visualizzato il seguente messaggio: "Dopo l'arresto, sarà necessario premere il pulsante di alimentazione sul controller per accenderlo. Arrestare il controller?" </br>Se si sceglie di procedere con questa operazione, i passaggi successivi saranno identici a quelli utilizzati per riavviare il controller passivo (vedere la selezione 1).|
|4\.|Arrestare il controller attivo.|Verrà visualizzato il seguente messaggio: "Dopo l'arresto, sarà necessario premere il pulsante di alimentazione sul controller per accenderlo. Arrestare il controller?" </br>Se si sceglie di procedere con questa operazione, i passaggi successivi saranno identici a quelli utilizzati per riavviare il controller passivo (vedere la selezione 1).|


#### Per riavviare o arrestare un controller in Windows PowerShell per StorSimple
Eseguire la procedura seguente per arrestare o riavviare un unico controller nel dispositivo StorSimple dal portale di Azure classico.


1. Accedere al dispositivo tramite la console seriale o una sessione telnet da un computer remoto. Connettersi al controller 0 o al controller 1 seguendo i passaggi riportati in [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

1. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**.

1. Nel messaggio dell'intestazione, prendere nota del controller a cui si è connessi (controller 0 o 1) e se è il controller attivo o passivo (standby).
    - Per arrestare un singolo controller, al prompt dei comandi, digitare:

		`Stop-HcsController`

		Il controller a cui si è connessi viene così arrestato. Se si arresta il controller attivo, verrà eseguito il failover sul controller passivo prima dell'arresto.
    - Per riavviare un controller, al prompt dei comandi, digitare:

		`Restart-HcsController`

		Il controller a cui si è connessi viene così riavviato. Se si riavvia il controller attivo, verrà eseguito il failover sul controller passivo prima del riavvio.


## Arrestare un dispositivo StorSimple

In questa sezione viene illustrato come arrestare un dispositivo StorSimple in esecuzione o in errore da un computer remoto. Un dispositivo viene disattivato dopo l'arresto di entrambi i controller del dispositivo. L'arresto di un dispositivo viene eseguito quando il dispositivo viene spostato fisicamente o messo fuori servizio.

> [AZURE.IMPORTANT] Prima di arrestare il dispositivo, verificare l'integrità dei componenti del dispositivo. Passare a **Dispositivi > Manutenzione > Stato hardware** e verificare che lo stato del LED di tutti i componenti sia verde. Solo un dispositivo integro avrà lo stato verde. Se il dispositivo viene arrestato per la sostituzione di un componente che non funziona correttamente, verrà visualizzato lo stato di errore (rosso) o danneggiato (giallo) per i rispettivi componenti.

#### Per arrestare un dispositivo StorSimple

1. Usare la procedura per [riavviare o arrestare un controller](#restart-or-shut-down-a-single-controller) per identificare e arrestare il controller passivo sul dispositivo. È possibile eseguire questa operazione nel portale di Azure classico o in Windows PowerShell per StorSimple.
2. Ripetere il passaggio precedente per arrestare il controller attivo.
3. È ora necessario osservare la superficie posteriore del dispositivo. Una volta che i due controller sono completamente arrestati, il LED di stato di entrambi i controller deve lampeggiare rosso. Se è necessario disattivare completamente il dispositivo in questa fase, posizionare gli interruttori di alimentazione dei moduli PCM (Power and Cooling Modules) su OFF. In tal modo il dispositivo viene spento.


<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

	`Stop-HCSController`

	This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## Ripristinare le impostazioni predefinite di fabbrica del dispositivo

Questa procedura descrive come ripristinare le impostazioni predefinite del dispositivo Microsoft Azure StorSimple usando Windows PowerShell per StorSimple. La reimpostazione di un dispositivo comporta la rimozione di tutti i dati e le impostazioni dall'intero cluster per impostazione predefinita.

Per ripristinare le impostazioni predefinite di fabbrica del dispositivo StorSimple di Microsoft Azure, procedere come segue:

### Per reimpostare il dispositivo per le impostazioni predefinite in Windows PowerShell per StorSimple

1. Accedere al dispositivo tramite la console seriale. Controllare il messaggio dell'intestazione per assicurarsi di essere connessi a controller attivo.

1. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**.

1. Al prompt dei comandi digitare il comando seguente per ripristinare l'intero cluster, rimuovendo tutti i dati, i metadati e le impostazioni del controller:

	`Reset-HcsFactoryDefault`

    Per ripristinare invece un singolo controller, usare il cmdlet [Reset-HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) con il parametro `-scope`.

    Il sistema verrà riavviato più volte. Verrà ricevuta una notifica al termine del processo di ripristino. A seconda del modello di sistema, per completare questo processo possono essere necessari 45-60 minuti per un dispositivo 8100 e 60-90 minuti per un dispositivo 8600.

	> [AZURE.TIP]

	> - Se si usa l'aggiornamento 1.2 o una versione precedente, usare il parametro `–SkipFirmwareVersionCheck` per ignorare il controllo della versione del firmware, altrimenti verrà visualizzato un errore di mancata corrispondenza del firmware che indica che il ripristino delle impostazioni predefinite non può continuare a causa di una mancata corrispondenza tra le versioni del firmware.

	> - La procedura di ripristino delle impostazioni predefinite potrebbe non riuscire per i dispositivi StorSimple con l'aggiornamento 1 o 1.1 nel portale di Azure Government e hanno completato una sostituzione singola o doppia di un controller con controller sostitutivi dotati di software precedente all'aggiornamento 1. Ciò accade quando per l'immagine di ripristino delle impostazioni predefinite viene verificata nel controller la presenza di un file SHA1 che non è presente per il software precedente all'aggiornamento 1. Se il ripristino delle impostazioni predefinite non riesce, contattare il Supporto tecnico Microsoft per assistenza nei passaggi successivi. Questo problema non si presenta con i controller sostitutivi dotati in fabbrica dell'aggiornamento 1 o di software successivo.


## Domande e risposte sulla gestione dei controller del dispositivo

In questa sezione vengono riportate alcune delle domande frequenti relative alla gestione dei controller del dispositivo StorSimple.

**D.** Cosa accade se entrambi i controller del dispositivo sono integri e accesi e si riavvia o si arresta il controller attivo?

**R.** Se entrambi i controller del dispositivo sono integri e accesi, verrà richiesto di confermare. È possibile scegliere di:

- **Riavviare il controller attivo**: viene ricevuta una notifica indicante che il riavvio di un controller attivo comporta il failover del dispositivo sul controller passivo. Il controller verrà riavviato.

- **Arrestare il controller attivo** - viene ricevuta una notifica indicante che l'arresto del controller attivo determina tempi di inattività. Inoltre, è necessario premere il pulsante di alimentazione sul dispositivo per accendere il controller.

**D.** Cosa accade se il controller passivo nel dispositivo non è disponibile o è spento e si riavvia o arresta il controller attivo?

**R.** Se il controller passivo sul dispositivo non è disponibile o è spento e si sceglie di:

- **Riavviare il controller attivo**: viene ricevuta una notifica indicante che se si continua l'operazione si verificherà un'interruzione temporanea del servizio e verrà richiesto di confermare.

- **Arrestare il controller attivo**: viene ricevuta una notifica indicante che se si continua l'operazione si verificheranno tempi di inattività e sarà necessario premere il pulsante di alimentazione su uno o entrambi i controller per accendere il dispositivo. Verrà richiesto di confermare.

**D.** Quand'è che il riavvio o l'arresto del controller non vengono eseguiti?

**R.** Il riavvio o l'arresto di un controller può non riuscire se:

- Un aggiornamento del dispositivo è in corso.

- Un riavvio del controller è già in corso.

- Un arresto del controller è già in corso.

**D.** Come si capisce se un controller è stato riavviato o arrestato?

**R.** È possibile controllare lo stato del controller nella pagina di manutenzione. Lo stato del controller indicherà se un controller è stato riavviato o arrestato. Inoltre, nella pagina degli avvisi è incluso un avviso informativo indicante se il controller è stato riavviato o arrestato. Le operazioni di riavvio e arresto del controller vengono inoltre registrate nei registri delle operazioni. Per altre informazioni sui registri delle operazioni, passare a [Visualizzare i registri delle operazioni](storsimple-service-dashboard.md#view-the-operations-logs).

**D.** Il failover del controller ha impatto sulle operazioni di I/O?

**R.** Le connessioni TCP tra iniziatori e controller attivo verranno reimpostate a causa del failover del controller, ma verranno ristabilite quando il controller passivo assume il controllo. Nel corso di questa operazione potrebbe esserci una pausa temporanea (inferiore a 30 secondi) nell'attività di I/O tra gli iniziatori e il dispositivo.

**D.** Come ripristinare il controller di servizio dopo che è stato arrestato e rimosso?

**R.** Per ripristinare un controller di servizio, è necessario inserirlo nello chassis come descritto in [Sostituire un modulo controller nel dispositivo StorSimple](storsimple-controller-replacement.md).

## Passaggi successivi

- Se si verificano problemi con i controller del dispositivo StorSimple che non si risolvono usando le procedure elencate in questa esercitazione, [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).

- Per altre informazioni sull’utilizzo del servizio StorSimple Manager, passare a[Usare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0511_2016-->