<properties 
   pageTitle="Sostituire un'unità disco nel dispositivo StorSimple | Microsoft Azure"
   description="Viene illustrato come sostituire un'unità disco in un dispositivo principale StorSimple o in uno chassis EBOD."
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

# Sostituzione di un'unità disco nel dispositivo StorSimple

## Panoramica

In questa esercitazione viene illustrato come rimuovere e sostituire un'unità disco rigido che non funziona correttamente o guasta in un dispositivo Microsoft Azure StorSimple. Per sostituire un'unità disco, è necessario:

- Disattivare il blocco antimanomissione

- Rimuovere l'unità disco

- Installare l'unità disco sostitutiva

>[AZURE.IMPORTANT]Prima di rimuovere e sostituire un'unità disco, esaminare le informazioni di sicurezza descritte in [Sostituzione dei componenti hardware di StorSimple](storsimple-hardware-component-replacement.md).

## Disattivazione del blocco antimanomissione

In questa procedura viene illustrato come i blocchi antimanomissione sul dispositivo StorSimple possono essere attivati o disattivati quando si sostituiscono le unità disco. I blocchi antimanomissione vengono montati nei punti di manipolazione del supporto dell'unità e sono accessibili attraverso una piccola apertura nella sezione chiavistello del punto di manipolazione. Le unità vengono fornite con i blocchi impostati sulla posizione bloccata.

#### Per sbloccare il blocco antimanomissione:

1. Inserire la chiave di blocco (un cacciavite "a prova di manomissione" T10 fornito da Microsoft) con attenzione nell'apertura del punto di manipolazione e nel relativo socket. 

    >[AZURE.NOTE]Se il blocco antimanomissione è attivato, l'indicatore rosso è visibile nell'apertura.

    ![Unità disco bloccata](./media/storsimple-disk-drive-replacement/IC741056.png)

    **Figura 1** Blocco antimanomissione attivato

    |Etichetta|Descrizione|
    |:----|:----------|
    |1|Apertura indicatore|
    |2|Blocco antimanomissione|

2. Ruotare la chiave in senso antiorario fino a quando l'indicatore rosso non è visibile nell'apertura sopra la chiave.

3. Rimuovere la chiave.

    ![Unità disco sbloccata](./media/storsimple-disk-drive-replacement/IC741057.png)

    **Figura 2** Unità disco sbloccata

4. È ora possibile rimuovere l'unità disco.

Seguire i passaggi in ordine inverso per attivare il blocco.

## Rimozione dell'unità disco

Il dispositivo StorSimple supporta una configurazione degli spazi di archiviazione RAID 10. Ciò implica che può funzionare normalmente con un'unità a stato solido (SSD), un'unità disco rigido (HDD) o un disco guasto.

>[AZURE.IMPORTANT]
>
>- Se più di un disco nel sistema è guasto, non rimuovere più di un'unità SSD o HDD dal sistema in qualsiasi momento. Ciò potrebbe causare una perdita dei dati.
>
>- Assicurarsi di inserire un'unità SSD sostitutiva in uno slot che in precedenza conteneva un'unità SSD. Analogamente, inserire un'unità HDD sostitutiva in uno slot che in precedenza conteneva un'unità HDD.
>
>- Nel portale di gestione, gli slot sono numerati da 0 a 11. Pertanto, se nel portale viene mostrato che un disco nello slot 2 è guasto, sul dispositivo, cercare il disco guasto nel terzo slot dalla parte superiore sinistra.

Le unità possono essere rimosse e sostituite durante il funzionamento del sistema.

#### Per rimuovere un'unità:

1. Per identificare il disco guasto, nel portale di gestione, passare a **Dispositivi** > **Manutenzione** > **Stato hardware**. Poiché un disco essere guasto nello chassis principale e/o in uno chassis EBOD (se si utilizza un modello 8600), controllare lo stato dei dischi sotto **Componenti condivisi** e **Componenti condivisi dello chassis EBOD**. Un disco guasto verrà visualizzato con uno stato rosso in entrambi gli chassis.

2. Individuare le unità nella parte anteriori dello chassis principale o dello chassis EBOD. Per il disco guasto, si accenderà il LED ambra.

3. Se il disco è sbloccato, procedere al passaggio successivo. Se il disco è bloccato, sbloccarlo seguendo la procedura descritta in [Disattivazione del blocco antimanomissione](#disengage-the-antitamper-lock).

4. Premere il chiavistello nero sul modulo del supporto dell'unità e rimuovere il punto di manipolazione del supporto dell'unità dalla parte anteriore dello chassis.

    ![Rilascio del punto di manipolazione dell'unità disco](./media/storsimple-disk-drive-replacement/IC741051.png)

    **Figura 3** Rilascio del punto di manipolazione dell'unità

5. Quando il punto di manipolazione del supporto dell'unità è completamente esteso, far scorrere il supporto dell'unità fuori dallo chassis.

    ![Scorrimento del disco fuori dall'unità disco](./media/storsimple-disk-drive-replacement/IC741052.png)
    
    **Figura 4** Scorrimento dell'unità disco fuori dal supporto

## Installazione dell'unità disco sostitutiva

Dopo aver rimosso un'unità guasta nel dispositivo Microsoft Azure StorSimple, seguire questa procedura per sostituirla con una nuova unità.

#### Per inserire un'unità:

1. Assicurarsi che il punto di manipolazione del supporto dell'unità sia completamente esteso, come illustrato nella figura seguente.

    ![Unità disco con punto di manipolazione esteso](./media/storsimple-disk-drive-replacement/IC741044.png)

    **Figura 5** Unità con punto di manipolazione esteso

2. Far scorrere il supporto dell'unità completamente nello chassis.

    ![Scorrimento del disco nel supporto dell'unità disco](./media/storsimple-disk-drive-replacement/IC741045.png)

    **Figura 6** Scorrimento del supporto dell'unità nello chassis

3. Con il supporto dell'unità inserito, chiudere il punto di manipolazione del supporto dell'unità continuando a spingere il supporto dell'unità nello chassis, fino a quando il punto di manipolazione del supporto dell'unità non si chiude a scatto in una posizione bloccata.

4. Utilizzare la chiave di blocco fornita da Microsoft (cacciavite Torx a prova di manomissione) per fissare in posizione il punto di manipolazione del supporto girando la vite di blocco di un quarto in senso orario.

5. Verificare che la sostituzione sia stata eseguita correttamente e che l'unità sia funzionante accedendo al portale di gestione e passando a **Manutenzione** > **Stato hardware**. Sotto **Componenti condivisi** o **Componenti condivisi dello chassis EBOD**, lo stato dell'unità deve essere verde, ovvero integro.

    >[AZURE.NOTE]Potrebbero essere necessarie diverse ore affinché lo stato del disco diventi verde dopo la sostituzione.

## Passaggi successivi

Leggere ulteriori informazioni sulla [Sostituzione dei componenti hardware di StorSimple](storsimple-hardware-component-replacement.md).

<!---HONumber=Oct15_HO3-->