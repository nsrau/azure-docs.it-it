<properties 
   pageTitle="Sostituire la batteria sul dispositivo StorSimple | Microsoft Azure"
   description="Viene descritto come rimuovere, sostituire e mantenere il modulo della batteria di backup nel dispositivo StorSimple."
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
   ms.date="08/31/2015"
   ms.author="alkohli" />

# Sostituzione del modulo della batteria di backup nel dispositivo StorSimple

## Panoramica

Il modulo di alimentazione e raffreddamento (PCM, Power and Cooling Module) dello chassis principale nel dispositivo Microsoft Azure StorSimple dispone di un pacchetto di batteria aggiuntivo. Tale pacchetto fornisce l'alimentazione in modo che il dispositivo StorSimple possa salvare i dati in caso di perdita dell'alimentazione CA allo chassis principale. Questo pacchetto di batteria viene definito come *modulo della batteria di backup*. Il modulo della batteria di backup è disponibile solo per lo chassis principale nel dispositivo StorSimple (lo chassis EBOD non contiene un modulo della batteria di backup).

In questa esercitazione viene illustrato come:

- Rimuovere il modulo della batteria di backup 
- Installare un nuovo modulo della batteria di backup
- Mantenimento del modulo della batteria di backup

>[AZURE.IMPORTANT]Prima di rimuovere e sostituire un modulo della batteria di backup, esaminare le informazioni di sicurezza descritte in [Introduzione alla sostituzione dei componenti hardware di StorSimple](storsimple-hardware-component-replacement.md).

## Rimuovere il modulo della batteria di backup

Il modulo della batteria di backup per il dispositivo Microsoft Azure StorSimple è un'unità sostituibile sul campo. Prima di installarlo nel PCM, il modulo della batteria deve essere archiviato nel pacchetto originale.

#### Per rimuovere il modulo della batteria di backup:

1. Nel portale di gestione, passare a **Dispositivi** > **Manutenzione** > **Stato hardware**. Sotto **Componenti condivisi**, controllare lo stato della batteria.

2. Identificare il PCM in cui la batteria è guasta. Nella Figura 1 viene mostrata la parte posteriore del dispositivo StorSimple.

    ![Backplane dei moduli dello chassis principale del dispositivo](./media/storsimple-battery-replacement/IC740994.png)

    **Figura 1** Parte posteriore del dispositivo principale in cui vengono mostrati il PCM e i moduli del controller

    |Etichetta|Descrizione|
    |:----|:----------|
    |1|PCM 0|
    |2|PCM 1|
    |3|Controller 0|
    |4|Controller 1|

    Come mostrato nella Figura 2 numero 3, l'indicatore LED di monitoraggio sul PCM 0 che corrisponde a **Guasto alla batteria** deve essere attivato.

    ![Backplane degli indicatori LED di monitoraggio del PCM del dispositivo](./media/storsimple-battery-replacement/IC740992.png)

    **Figura 2** Parte posteriore del PCM in cui vengono mostrati gli indicatori LED di monitoraggio

    |Etichetta|Descrizione|
    |:---|:-----------|
    |1|Guasto dell’alimentazione CA|
    |2|Guasto alla ventola|
    |3|Guasto alla batteria|
    |4|PCM OK|
    |5|Guasto dell'alimentazione CC|
    |6|Integrità della batteria|

3. Per rimuovere il PCM con una batteria guasta, seguire i passaggi descritti in [Rimozione di un PCM](storsimple-power-cooling-module-replacement.md#remove-a-pcm).

4. Dopo aver rimosso il PCM, sollevare e ruotare il punto di manipolazione del modulo della batteria verso l'alto, come indicato nella figura riportata di seguito ed estrarlo fino a rimuovere la batteria.

    ![Rimozione della batteria dal PCM](./media/storsimple-battery-replacement/IC741019.png)

    **Figura 3** Rimozione della batteria dal PCM

5. Inserire il modulo nel pacchetto dell'unità sostituibile sul campo.

6. Restituire l'unità difettosa a Microsoft per un'assistenza e una gestione appropriate.

## Installazione di un nuovo modulo della batteria di backup

Eseguire i passaggi seguenti per installare il modulo della batteria sostitutiva nello chassis principale del dispositivo StorSimple.

#### Per installare il modulo della batteria:

1. Inserire il modulo della batteria di backup con l'orientamento appropriato nel PCM.

2. Premere completamente il punto di manipolazione del modulo della batteria per alloggiare il connettore.

3. Sostituire il PCM nello chassis principale seguendo le linee guida descritte in [Sostituzione del modulo di alimentazione e raffreddamento nel dispositivo StorSimple](storsimple-power-cooling-module-replacement.md).

4. Una volta completata la sostituzione, accedere al portale di gestione e passare a **Dispositivi** > **Manutenzione** > **Stato hardware**, quindi verificare lo stato della batteria per assicurarsi che l'installazione sia avvenuta correttamente. Se il LED mostra uno stato verde, la batteria è integra.

## Mantenimento del modulo della batteria di backup

Nel dispositivo, il modulo della batteria di backup fornisce alimentazione al controller durante un evento di perdita dell'alimentazione. Consente al dispositivo StorSimple di salvare i dati critici prima dell'arresto in modo controllato. Con due batterie completamente cariche nei PCM, il sistema può gestire due eventi di perdita consecutivi.

Nel portale di gestione, **Stato hardware** nella pagina **Manutenzione** indica se la batteria non funziona correttamente o quando si sta avvicinando la fine del ciclo di vita. Lo stato della batteria è indicato da **Batteria in PCM 0** o **Batteria in PCM 1** sotto **Componenti condivisi**. In questa pagina verrà visualizzato uno stato **DANNEGGIATO** per indicare l'avvicinarsi della fine del ciclo di vita e **NON RIUSCITO** per indicare che è stata raggiunta la fine del ciclo di vita.

>[AZURE.NOTE]La batteria può segnalare **NON RIUSCITO** quando è semplicemente necessario ricaricarla.
 
Se viene visualizzato lo stato **DANNEGGIATO**, è consigliabile adottare la linea di azione seguente:

- Nel sistema potrebbe essersi verificata una perdita di alimentazione recente o le batterie potrebbero essere sottoposte alla manutenzione periodica. Osservare il sistema per 12 ore prima di procedere.

    - Se lo stato è ancora **DANNEGGIATO** dopo 12 ore di connessione continua all'alimentazione CA con i controller e i PCM in esecuzione, la batteria deve essere sostituita. [Contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per un modulo della batteria di backup sostitutivo.

    - Se lo stato diventa OK dopo 12 ore, la batteria è operativa e necessitava soltanto di una ricarica di manutenzione.

- Se non si è verificata una perdita dell'alimentazione CA associata e il PCM è acceso e connesso all'alimentazione CA, la batteria deve essere sostituita. [Contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per ordinare un modulo della batteria di backup sostitutivo.

>[AZURE.IMPORTANT]Smaltire la batteria guasta in conformità con le normative nazionali e regionali.

## Passaggi successivi

Leggere ulteriori informazioni sulla [Sostituzione dei componenti hardware di StorSimple](storsimple-hardware-component-replacement.md).

<!---HONumber=Oct15_HO3-->