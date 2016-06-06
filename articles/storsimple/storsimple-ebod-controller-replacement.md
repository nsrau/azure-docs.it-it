<properties 
   pageTitle="Sostituire un controller EBOD StorSimple | Microsoft Azure"
   description="Viene illustrato come rimuovere e sostituire uno o entrambi i controller EBOD in un dispositivo StorSimple 8600."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="alkohli" />

# Sostituzione di un controller EBOD nel dispositivo StorSimple

## Panoramica

In questa esercitazione viene illustrato come sostituire un modulo controller EBOD guasto nel dispositivo Microsoft Azure StorSimple. Per sostituire un modulo controller EBOD, è necessario:

- Rimuovere il controller EBOD guasto
- Installare un nuovo controller EBOD

Prima di iniziare, tenere in considerazione le informazioni seguenti:

- In tutti gli slot inutilizzati, è necessario inserire moduli EBOD vuoti. Lo chassis non verrà raffreddato correttamente se uno slot è aperto.

- Il controller EBOD è dispone del supporto per lo swapping a caldo e può essere rimosso o sostituito. Non rimuovere un modulo guasto finché non si dispone di una sostituzione. Quando si avvia il processo di sostituzione, deve essere completato entro 10 minuti.

>[AZURE.IMPORTANT] Prima di tentare di rimuovere o sostituire qualsiasi componente di StorSimple, leggere le[convenzioni di sicurezza](storsimple-safety.md#safety-icon-conventions)e altre[precauzioni di sicurezza](storsimple-safety.md).

## Rimozione di un controller EBOD

Prima di sostituire il modulo controller EBOD guasto nel dispositivo StorSimple, assicurarsi che l'altro modulo controller EBOD sia attivo e in esecuzione. Nella procedura e nella tabella seguenti viene illustrato come rimuovere il modulo controller EBOD.

#### Per rimuovere un modulo EBOD:

1. Aprire il portale di Azure classico.

2. Passare a **Dispositivi** > **Manutenzione** > **Stato hardware**, quindi verificare che lo stato del LED per il modulo controller EBOD sia verde e che il LED per il modulo controller EBOD guasto sia rosso.

3. Individuare il modulo controller EBOD guasto nella parte posteriore del dispositivo.

4. Rimuovere i cavi che collegano il modulo controller EBOD al controller prima di rimuovere il modulo EBOD dal sistema.

5. Prendere nota dell'esatta porta SAS del modulo controller EBOD collegata al controller. Dopo la sostituzione del modulo EBOD, sarà necessario ripristinare il sistema a questa configurazione.

    >[AZURE.NOTE] In genere, questa sarà la porta A, etichettata come **Host in entrata** nel diagramma seguente.

    ![Backplane del controller EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)

     **Figura 1** Parte posteriore del modulo EBOD

    |Etichetta|Descrizione|
    |:----|:----------|
    |1|LED di errore|
    |2|LED di alimentazione|
    |3|Connettori SAS|
    |4|LED SAS|
    |5|Porte seriali solo per l'utilizzo predefinito|
    |6|Porta (Host in entrata)|
    |7|Porta B (Host in uscita)|
    |8|Porta C (solo per utilizzo predefinito)|

## Installazione di un nuovo controller EBOD

Nella procedura e nella tabella seguenti viene illustrato come installare un modulo controller EBOD nel dispositivo StorSimple.

#### Per installare un controller EBOD:

1. Verificare la presenza di danni nel dispositivo EBOD, soprattutto sul connettore di interfaccia. Non installare il nuovo controller EBOD se sono presenti perni piegati.

2. Con i chiavistelli in posizione aperta, far scorrere il modulo nello chassis finché non attiva i chiavistelli.

    ![Installazione del controller EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)

    **Figura 2** Installazione del modulo controller EBOD

3. Chiudere il chiavistello. Quando il chiavistello viene attivato si dovrebbe ascoltare un clic.

    ![Rilascio del latch EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)

    **Figura 3** Chiusura del chiavistello del modulo EBOD

4. Riconnettere i cavi. Utilizzare la configurazione esatta presente prima della sostituzione. Vedere il diagramma e la tabella seguenti per informazioni dettagliate su come connettere i cavi.

    ![Cablare il dispositivo 4U per l'alimentazione](./media/storsimple-ebod-controller-replacement/IC770723.png)

    **Figura 4** Riconnessione dei cavi

    |Etichetta|Descrizione|
    |:----|:----------|
    |1|Enclosure principale|
    |2|PCM 0|
    |3|PCM 1|
    |4|Controller 0|
    |5|Controller 1|
    |6|Controller 0 EBOD|
    |7|Controller 1 EBOD|
    |8|Chassis EBOD|
    |9|Unità PDU (Power Distribution Unit)|

## Passaggi successivi

Leggere ulteriori informazioni sulla [Sostituzione dei componenti hardware di StorSimple](storsimple-hardware-component-replacement.md).

<!---HONumber=AcomDC_0525_2016-->