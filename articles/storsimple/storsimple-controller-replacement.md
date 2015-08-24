<properties 
   pageTitle="Sostituire un modulo controller nel dispositivo StorSimple | Microsoft Azure"
   description="Viene illustrato come rimuovere e sostituire uno o entrambi i moduli controller nel dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/12/2015"
   ms.author="alkohli" />

# Sostituire un modulo controller nel dispositivo StorSimple

## Panoramica

In questa esercitazione viene illustrato come rimuovere e sostituire uno o entrambi i moduli controller in un dispositivo StorSimple. Viene inoltre illustrata la logica sottostante per gli scenari di sostituzione controller singoli e doppi.

>[AZURE.NOTE]Prima di eseguire una sostituzione del controller, è consigliabile aggiornare sempre il firmware del controller alla versione più recente.
>
>Per evitare danni al dispositivo StorSimple, non rimuovere il controller fino a quando non vengono visualizzati i LED come in un uno dei seguenti modi:
>
>- Tutte le luci sono impostate su OFF.
>- LED 3,![Icona segno di spunta verde](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png)e![Icona croce rossa](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) sono lampeggianti, e LED 0 e LED 7 sono**ACCESI**.

Nella tabella seguente vengono illustrati gli scenari di sostituzione del controller supportato.

|Caso|Scenario di sostituzione|Procedura applicabile|
|:---|:-------------------|:-------------------|
|1|Un controller è in stato di errore, l’altro controller è integro e attivo.|[Sostituzione di un singolo controller](#replace-a-single-controller)|
|2|Entrambi i controller hanno avuto esito negativo e richiedono la sostituzione. Lo chassis, i dischi, e l’enclosure del disco sono integri.|[Sostituzione doppia del controller](#replace-both-controllers)|
|3|I controller dallo stesso dispositivo o da diversi dispositivi vengono invertiti. Lo chassis, i dischi e l’enclosure del disco sono integri.|Verrà visualizzato un messaggio di avviso di mancata corrispondenza dello slot.|
|4|Un controller non è presente e l'altro controller ha avuto esito negativo.|[Sostituzione doppia del controller](#replace-both-controllers)|
|5|Uno o entrambi i controller hanno avuto esito negativo.. Non è possibile accedere al dispositivo tramite la console seriale o Windows PowerShell in remoto.|[Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md)per una procedura di sostituzione manuale.|
|6|I controller dispongono di una versione di build diverse, e questo potrebbe essere dovuto a:<ul><li>i controller dispongono di una versione del software diversa.</li><li>I controller dispongono di una versione del firmware diversa.</li></ul>|Se le versioni del software del controller sono diverse, la logica di sostituzione rileva e aggiorna la versione del software sul controller di sostituzione.<br><br>Se le versioni del firmware del controller sono diverse e la precedente versione del firmware è**non**automaticamente aggiornabile, verrà visualizzato un messaggio di avviso nel portale di gestione. È necessario analizzare gli aggiornamenti e installare gli aggiornamenti firmware.</br></br>Se le precedenti versioni del firmware sono diverse e la versione precedente del firmware del controller è aggiornabile automaticamente, la logica di sostituzione del controller lo rileverà e dopo che il controller viene avviato, il firmware viene aggiornato automaticamente.|

È necessario rimuovere un modulo controller se non funziona. Uno o entrambi i moduli controller possono avere esito negativo, ciò potrebbe comportare una sostituzione di un singolo controller o una sostituzione di entrambi i controller. Per le procedure di sostituzione, vedere gli argomenti seguenti:

- [Sostituire un singolo controller](#replace-a-single-controller)
- [Sostituire entrambi i controller](#replace-both-controllers)
- [Rimuovere un controller](#remove-a-controller)
- [Aggiungere un controller](#insert-a-controller).
- [Identificare il controller attivo sul dispositivo](#identify-the-active-controller-on-your-device)

>[AZURE.IMPORTANT]Prima di rimuovere e sostituire un controller, esaminare le informazioni di sicurezza descritte in [Sostituzione dei componenti hardware di StorSimple](storsimple-hardware-component-replacement.md).

## Sostituzione di un singolo controller

Quando uno dei due controller nel dispositivo Microsoft Azure StorSimple non è riuscita, non funziona correttamente o è mancante, è necessario sostituire un unico controller.

### Logica di sostituzione del singolo controller

In una sostituzione di un controller singolo, è necessario rimuovere prima il controller che ha avuto esito negativo. (Il controller rimanente nel dispositivo è il controller attivo). Quando si inserisce il controller di sostituzione, si verificano le azioni seguenti:

1. Il controller di sostituzione avvia immediatamente la comunicazione con il dispositivo StorSimple.

2. Uno snapshot del disco rigido virtuale (VHD) per il controller attivo viene copiato nel controller sostitutivo.

3. Lo snapshot viene modificato in modo che quando il controller sostitutivo viene avviato dal disco rigido virtuale, verrà riconosciuto come un controller in standby.

4. Dopo aver completato le modifiche, il controller sostitutivo verrà avviato come controller in standby.

5. Quando entrambi i controller sono in esecuzione, il cluster è online.

### Procedura per la sostituzione di un singolo controller

Completare i passaggi seguenti se uno dei controller del dispositivo Microsoft Azure StorSimple ha esito negativo. (L’altro controller deve essere attivo e in esecuzione. Se entrambi i controller hanno esito negativo o non funzionano, andare alla[procedura per la sostituzione doppia del controller](#dual-controller-replacement-steps).)

>[AZURE.NOTE]Il riavvio e il ripristino completo del controller dalla procedura di sostituzione può richiedere 30 - 45 minuti. Il tempo totale richiesto per l'intera procedura, incluso il collegamento dei cavi, è di circa 2 ore.

#### Per rimuovere un singolo modulo del controller che ha avuto esito negativo

1. Nel portale di gestione del servizio StorSimple Manager, fare clic sulla scheda **Dispositivi**e quindi fare clic sul nome del dispositivo che si desidera monitorare.

2. Scegliere la scheda **Manutenzione**e quindi passare alla scheda**Stato hardware**. Lo stato del Controller 0 o Controller 1 deve essere rosso, ad indicare un errore.

    >[AZURE.NOTE]Il controller che ha avuto esito negativo in una sostituzione di un singolo controller è sempre un controller in standby.

3. Usare la figura 1 e la tabella seguente per individuare il modulo del controller che ha avuto esito negativo.

    ![Backplane dei moduli dello chassis principale del dispositivo](./media/storsimple-controller-replacement/IC740994.png)

    **Figura 1**retro del dispositivo StorSimple

    |Etichetta|Descrizione|
    |:----|:----------|
    |1|PCM 0|
    |2|PCM 1|
    |3|Controller 0|
    |4|Controller 1|

4. Nel controller che ha avuto esito negativo, rimuovere tutti i cavi di rete connessi dalle porte dati. Se si utilizza un modello 8600, rimuovere anche i cavi SAS che connettono il controller al controller EBOD.

5. Seguire i passaggi in[Rimuovere un controller](#remove-a-controller)per rimuovere il controller che ha avuto esito negativo.

6. Installare la sostituzione della factory nello stesso slot da cui è stato rimosso il controller che ha avuto esito negativo. In questo modo viene attivata la logica di sostituzione di un singolo controller. Per ulteriori informazioni, vedere[Logica di sostituzione di un singolo controller](#single-controller-replacement-logic).

7. Mentre la logica di sostituzione del singolo controller viene eseguita in background, riconnettere i cavi. Prestare attenzione a collegare tutti i cavi esattamente allo stesso modo i cui erano connessi prima della sostituzione.

8. Dopo aver riavviato il controller, controllare**lo stato del Controller**e lo**stato del Cluster**nel portale di gestione per verificare che il controller sia in uno stato integro e in modalità standby.

>[AZURE.NOTE]Se si sta monitorando il dispositivo tramite la console seriale, è possibile riscontrare più riavvii mentre il controller effettua il ripristino dalla procedura di sostituzione. Quando viene visualizzato il menu della console seriale, si saprà che la sostituzione è completata. Se il menu non viene visualizzato entro due ore dall’inizio della sostituzione del controller,[contattare il supporto Microsoft](storsimple-contact-microsoft-support.md).

## Sostituzione di entrambi i controller

Quando entrambi i controller del dispositivo Microsoft Azure StorSimple hanno avuto esito negativo, non funzionano o mancano, è necessario sostituire entrambi i controller.

### Logica di sostituzione doppia del controller

In una doppia sostituzione di controller, rimuovere prima entrambi i controller che hanno avuto esito negativo e quindi inserire le sostituzioni. Quando vengono inseriti i due controller sostitutivi, si verificano le azioni seguenti:

1. Il controller sostitutivo nello slot 0 verifica quanto segue:
 
   1. Si stanno utilizzando le versioni correnti del firmware e software?

   2. Si tratta di una parte del cluster?

   3. Il controller peer è in esecuzione ed è in funzione cluster?
							
    Se nessuna di queste condizioni sono true, il controller cerca il backup giornaliero più recente (all'interno di**nonDOMstorage**sull'unità S). Il controller copia lo snapshot più recente del file VHD dal backup.

2. Il controller nello slot 0 utilizza lo snapshot per la sua stessa immagine.

3. Nel frattempo, il controller nello slot 1 attende che il controller 0 completi la creazione dell'immagine e si avvii.

4. Dopo l'avvio del controller 0, il controller 1 rileva il cluster creato dal controller 0, che attiva la logica di sostituzione del singolo controller. Per ulteriori informazioni, vedere[Logica di sostituzione di un singolo controller](#single-controller-replacement-logic).

5. Successivamente, verranno eseguiti entrambi i controller e il cluster verrà portato online.

>[AZURE.IMPORTANT]Dopo la sostituzione doppia dei controller, dopo aver configurato il dispositivo StorSimple, è essenziale eseguire un backup manuale del dispositivo. I backup giornalieri di configurazione dispositivo non vengono attivati fino a che non sono trascorse 24 ore. Lavorare con[il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md)per eseguire un backup manuale del dispositivo.

### Procedura per la sostituzione doppia di controller

Questo flusso di lavoro è necessario quando entrambi i controller del dispositivo Microsoft Azure StorSimple hanno avuto esito negativo. Questo problema può verificarsi in un Data Center in cui il sistema di raffreddamento smette di funzionare e di conseguenza, entrambi i controller non riescono in un breve periodo di tempo. A seconda se il dispositivo StorSimple è attivato o disattivato e se si utilizza un modello 8600 o un modello 8100, è necessaria una serie diversa di passaggi.

>[AZURE.IMPORTANT]Il riavvio e il ripristino completo del controller dalla procedura di sostituzione può richiedere da 45 minuti fino a 1 ora. Il tempo totale richiesto per l'intera procedura, incluso il collegamento dei cavi, è di circa 2.5 ore.

#### Per sostituire entrambi i moduli del controller

1. Se il dispositivo è disattivato, ignorare questo passaggio e passare al passaggio successivo. Se il dispositivo è acceso, disattivare il dispositivo.
										
    1. Se si utilizza un modello 8600, disattivare prima l'enclosure principale e poi disattivare l'enclosure EBOD.

    2. Attendere che la periferica sia chiusa completamente. Tutti i LED nella parte posteriore del dispositivo saranno spenti.

2. Rimuovere tutti i cavi di rete connessi alle porte dati. Se si utilizza un modello 8600, rimuovere anche i cavi SAS che collegano l'enclosure principale all'enclosure EBOD.

3. Rimuovere entrambi i controller dal dispositivo StorSimple. Per ulteriori informazioni, vedere[Rimuovere un controller](#remove-a-controller).

4. Inserire la sostituzione della factory prima per il Controller 0 e quindi inserire il Controller 1. Per ulteriori informazioni, vedere[Inserire un controller](#insert-a-controller). In questo modo viene attivata la logica di sostituzione doppia del controller. Per ulteriori informazioni, vedere [Logica di sostituzione doppia del controller](#dual-controller-replacement-logic).

5. Mentre la logica di sostituzione del controller viene eseguita in background, riconnettere i cavi. Prestare attenzione a collegare tutti i cavi esattamente allo stesso modo i cui erano connessi prima della sostituzione. Vedere le istruzioni dettagliate per il modello nella sezione Cablare il dispositivo della sezione [installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md)o[installare il dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

6. Accendere il dispositivo StorSimple. Se si utilizza un modello 8600:

    1. Assicurarsi che l’enclosure EBOD sia accesa per prima.

    2. Attendere che l’enclosure EBOD sia in esecuzione.

    3. Accendere l'enclosure principale.

    4. Dopo che il primo controller viene riavviato e si trova in uno stato integro, il sistema sarà in esecuzione.

    >[AZURE.NOTE]Se si sta monitorando il dispositivo tramite la console seriale, è possibile riscontrare più riavvii mentre il controller effettua il ripristino dalla procedura di sostituzione. Quando viene visualizzato il menu della console seriale, si sa che la sostituzione è completata. Se il menu non viene visualizzato entro 2.5 ore dall’inizio della sostituzione del controller,[contattare il supporto Microsoft](storsimple-contact-microsoft-support.md).

## Rimuovere un controller

Utilizzare la procedura seguente per rimuovere un modulo controller danneggiato dal dispositivo StorSimple.

>[AZURE.NOTE]Le illustrazioni seguenti riguardano il controller 0. Per il controller 1, questi potrebbe essere annullati.

#### Per rimuovere un modulo controller

1. Afferrare la linguetta del modulo tra il pollice e l'indice.

2. Stringere delicatamente il pollice e l'indice per rilasciare la linguetta del controller.

    ![Rilascio della linguetta del Controller](./media/storsimple-controller-replacement/IC741047.png)

    **Figura 2**Rilascio della linguetta

2. Utilizzare la linguetta come appiglio per estrarre il controller dallo chassis.

    ![Scorrimento del Controller all'esterno dello Chassis](./media/storsimple-controller-replacement/IC741048.png)

    **Figura 3**Estrazione del controller dallo chassis

## Inserire un controller

Utilizzare la procedura seguente per installare un modulo controller factory fornito dopo la rimozione di un modulo difettoso dal dispositivo StorSimple.

### Per installare un modulo controller

1. Verificare che non ci siano eventuali danni ai connettori di interfaccia. Non installare il modulo se uno qualsiasi dei perni del connettore è danneggiato oppure si piega.

2. Far scorrere il modulo controller nello chassis mentre la linguetta viene completamente rilasciata.

    ![Scorrimento del Controller all'interno dello Chassis](./media/storsimple-controller-replacement/IC741053.png)

    **Figura 4**Inserire il controller nello chassis

3. Con il modulo controller inserito, inizia a chiudere la linguetta pur continuando a spingere il modulo del controller nello chassis. La linguetta guiderà il controller nella posizione corretta.

    ![Chiusura della linguetta del Controller](./media/storsimple-controller-replacement/IC741054.png)

    **Figura 5**Chiusura della linguetta

4. Quando la linguetta è posizionata correttamente la procedura è terminata. Il LED **OK**dovrebbe ora essere acceso.

    >[AZURE.NOTE]La riattivazione del controller e del LED potrebbe richiedere fino a 5 minuti.

5. Per verificare che la sostituzione abbia avuto esito positivo, nel portale di gestione, passare a**Dispositivi** > **Manutenzione** > **Stato Hardware**e assicurarsi che sia controller 0 e sia il controller 1 siano integri (lo stato deve essere verde).

## Identificare il controller attivo sul dispositivo

Esistono molte situazioni, ad esempio la prima registrazione del dispositivo o sostituzione del controller, che richiedono di individuare il controller attivo in un dispositivo StorSimple. Il controller attivo elabora tutti le operazioni del firmware del disco e di rete. Per identificare il controller attivo, è possibile utilizzare uno dei metodi seguenti:

- [Utilizzare il portale di gestione per identificare il controller attivo](#use-the-management-portal-to-identify-the-active-controller)

- [Utilizzare Windows PowerShell per StorSimple per identificare il controller attivo](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)

- [Controllare il dispositivo fisico per identificare il controller attivo](#check-the-physical-device-to-identify-the-active-controller)

Ognuna di queste procedure è descritta di seguito.

### Utilizzare il portale di gestione per identificare il controller attivo

Nel portale di gestione, passare a**Dispositivi** > **Manutenzione**e scorrere verso la sezione**Controller**. Qui è possibile verificare quale controller è attivo.

![Identificare il Controller attivo nel portale di gestione](./media/storsimple-controller-replacement/IC752072.png)

**Figura 6**Il portale di gestione mostra il controller attivo

### Utilizzare Windows PowerShell per StorSimple per identificare il controller attivo

Quando il dispositivo si accede tramite la console seriale, viene visualizzato un messaggio di intestazione. Il messaggio di intestazione contiene informazioni base sul dispositivo come ad esempio modello, nome, versione del software installato e stato del controller a cui si vuole accede. Nella figura seguente viene illustrato un esempio di messaggio di intestazione:

![Messaggio di intestazione seriale](./media/storsimple-controller-replacement/IC741098.png)

**Figura 7**Messaggio di intestazione che mostra il controller 0 come attivo

È possibile utilizzare il messaggio di intestazione per determinare se il controller a cui si è connessi è attivo o passivo.

### Controllare il dispositivo fisico per identificare il controller attivo

Per identificare il controller attivo sul dispositivo, individuare il LED blu sopra la porta DATI 5 nella parte posteriore dell’enclosure primario.

Se il LED lampeggia, il controller è attivo e l'altro controller è in modalità standby. Come ausilio, utilizzare il diagramma e la tabella seguente.

![Piano posteriore dell'enclosure principale del dispositivo con porte dati](./media/storsimple-controller-replacement/IC741055.png)

**Figura 8**Parte posteriore dell’enclosure principale con porte dati e LED di monitoraggio

|Etichetta|Descrizione|
|:----|:----------|
|1-6|porte di rete DATI da 0 a 5|
|7|LED blu|


## Passaggi successivi

Leggere ulteriori informazioni sulla [Sostituzione dei componenti hardware di StorSimple](storsimple-hardware-component-replacement.md).

<!---HONumber=August15_HO7-->