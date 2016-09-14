<properties 
   pageTitle="Attivare o disattivare la dispositivo StorSimple | Microsoft Azure"
   description="Viene illustrato come attivare un nuovo dispositivo StorSimple, attivare un dispositivo che è stato arrestato o che ha subito un'interruzione dell'alimentazione e disattivare un dispositivo in esecuzione."
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
   ms.date="08/23/2016"
   ms.author="alkohli" />

# Attivare o arrestare il dispositivo StorSimple 

## Overview

L’arresto di un dispositivo Microsoft Azure StorSimple non è necessario nel normale funzionamento del sistema. Tuttavia, potrebbe esser necessario spegnere un dispositivo o accenderne uno nuovo. In genere, un arresto è necessario nei casi in cui bisogna sostituire l'hardware non riuscito, spostare fisicamente un'unità o mettere fuori servizio un dispositivo. In questa esercitazione viene descritta la procedura necessaria per l'attivazione e l'arresto del dispositivo StorSimple in scenari diversi.

Nella tabella seguente sono elencati diversi scenari per l'attivazione e l'arresto del dispositivo StorSimple e vengono forniti collegamenti alle procedure appropriate.

|Scenario|Argomenti di riferimento|
|:-------|:---------------|
|Attivare un nuovo dispositivo|[Attivare un nuovo dispositivo](#turn-on-a-new-device)<ul><li>[Nuovo dispositivo con solo l’enclosure principale](#new-device-with-primary-enclosure-only)</li><li>[Nuovo dispositivo con enclosure EBOD](#new-device-with-ebod-enclosure)</li></ul>|
|Attivare un dispositivo dopo l'arresto|[Attivare un dispositivo dopo l'arresto](#turn-on-a-device-after-shutdown)<ul><li>[dispositivo con solo l’enclosure principale ](#device-with-primary-enclosure-only)</li><li>[Dispositivo con enclosure EBOD](#device-with-ebod-enclosure)</li></ul>|
|Attivare un dispositivo dopo un'interruzione dell'alimentazione|[Attivare un dispositivo dopo un'interruzione dell'alimentazione](#turn-on-a-device-after-a-power-loss)<ul><li>[Dispositivo con solo l’enclosure principale](#8100)</li><li>[Dispositivo con enclosure EBOD](#8600)</li></ul>|
|Attivare un dispositivo dopo la perdita della connessione dell’enclosure principale e dell’EBOD|[Attivare un dispositivo dopo la perdita della connessione dell’enclosure principale e dell’enclosure EBOD ](#turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost)|
|Arrestare un dispositivo in esecuzione|[Disattivare un dispositivo in esecuzione](#turn-off-a-running-device)<ul><li>[Dispositivo con solo l’enclosure principale](#8100a)</li><li>[Dispositivo con enclosure EBOD](#8600a)</li></ul>|

## Attivare un nuovo dispositivo

I passaggi per l'attivazione di un dispositivo StorSimple per la prima volta variano a seconda del modello del dispositivo (8100 o 8600). Il modello 8100 ha una sola enclosure principale, mentre il 8600 è un dispositivo con un’enclosure principale e un’enclosure EBOD. Nelle sezioni seguenti sono illustrati i passaggi dettagliati per entrambi i modelli.

- [Nuovo dispositivo con soltanto un’enclosure principale](#new-device-with-primary-enclosure-only)

- [Nuovo dispositivo con enclosure EBOD](#new-device-with-ebod-enclosure)

### Nuovo dispositivo con soltanto un’enclosure principale

Il modello StorSimple 8100 è un dispositivo a enclosure singola. Il dispositivo include moduli PCM (Power and Cooling Modules) Entrambi i PCM devono essere installati e collegati a una fonte di alimentazione diversa per assicurare disponibilità elevata.

Per cablare il dispositivo per l'alimentazione, attenersi alla seguente procedura.

[AZURE.INCLUDE [storsimple-cavo-8100-per-alimentazione](../../includes/storsimple-cable-8100-for-power.md)]

>[AZURE.NOTE]Per l’installazione completa del dispositivo e le istruzioni di cablaggio, vedere[Installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md). Assicurarsi di seguire esattamente le istruzioni.

### Nuovo dispositivo con enclosure EBOD

Il dispositivo StorSimple 8600 è costituito da un'enclosure principale e un'enclosure EBOD. Questa operazione richiede che le unità siano cablate insieme per l'alimentazione e la connettività Serial Attached SCSI (SAS).

Quando si configura il dispositivo per la prima volta, eseguire prima i passaggi per il cablaggio SAS e poi completare i passaggi per il cablaggio di alimentazione.

[AZURE.INCLUDE [storsimple-sas-cavo-8600](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple cavo-8600-per-alimentazione](../../includes/storsimple-cable-8600-for-power.md)]

>[AZURE.NOTE]Per l’installazione completa del dispositivo e le istruzioni di cablaggio, vedere[Installare il dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md). Assicurarsi di seguire esattamente le istruzioni.

## Attivare un dispositivo dopo l'arresto

I passaggi per l'attivazione di un dispositivo StorSimple dopo un arresto variano a seconda del modello del dispositivo (8100 o 8600). Il modello 8100 ha una sola enclosure principale, mentre il 8600 è un dispositivo con un’enclosure principale e un’enclosure EBOD.

- [Dispositivo con soltanto un’enclosure principale](#device-with-primary-enclosure-only)

- [Dispositivo con enclosure EBOD](#device-with-ebod-enclosure)

### Dispositivo con soltanto un’enclosure principale

Dopo un arresto, utilizzare la procedura seguente per attivare un dispositivo StorSimple con un’enclosure principale e nessuna enclosure EBOD.

#### Per attivare un dispositivo con solo un’enclosure principale

1. Assicurarsi che gli interruttori di alimentazione dei moduli PCM siano in posizione OFF. Se non sono in posizione OFF, posizionare gli interruttori su OFF e attendere che gli indicatori luminosi si spengano.

2. Accendere il dispositivo girando l'interruttore di alimentazione di ciascun modulo PCM su ON. In tal modo il dispositivo viene acceso.

3. Controllare le operazioni seguenti per verificare che il dispositivo sia completamente acceso:

    1. I LED OK su entrambi i moduli PCM sono verdi.

    2. I LED di stato in entrambi i controller sono verdi.

    3. Il LED blu su uno dei controller lampeggia, il che indica che il controller è attivo.

    Se una di queste condizioni non vengono soddisfatte, il dispositivo non è integro. [Contattare il supporto Microsoft](storsimple-contact-microsoft-support.md).

### Dispositivo con enclosure EBOD

Dopo un arresto, utilizzare la procedura seguente per attivare un dispositivo StorSimple con un’enclosure principale e un’enclosure EBOD. Eseguire ogni passaggio esattamente come descritto nella sequenza. In caso contrario, si potrebbe causare la perdita di dati.

#### Per attivare un dispositivo con un’enclosure principale e un’enclosure EBOD

1. Assicurarsi che l’enclosure EBOD sia connessa all’enclosure principale. Per ulteriori informazioni, vedere[Installare il dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Assicurarsi che gli interruttori di alimentazione di tutti i moduli PCM sulle enclosure principale ed EBOD siano in posizione OFF. Se non sono in posizione OFF, posizionare gli interruttori su OFF e attendere che gli indicatori luminosi si spengano.

3. Accendere prima l’enclosure EBOD girando gli interruttori di alimentazione di ciascun modulo PCM su ON. I LED PCM dovrebbero essere verdi. Un controller EBOD LED verde su questa unità indica che l’enclosure EBOD è attivata.

4. Accendere l’enclosure principale girando l'interruttore di alimentazione di ciascun modulo PCM su ON. L'intero sistema dovrebbe ora essere acceso.

5. Verificare che i LED SAS siano verdi, il che assicura che la connessione tra l'enclosure EBOD e l’enclosure principale è valida.

## Attivare un dispositivo dopo un'interruzione dell'alimentazione

Un guasto o un'interruzione dell'alimentazione può causare l'arresto di un dispositivo StorSimple. Può verificarsi un'interruzione dell'alimentazione su uno o entrambi gli alimentatori di alimentazione elettrica. Le procedure di ripristino sono diverse a seconda del modello del dispositivo (8100 o 8600). Il modello 8100 ha una sola enclosure principale, mentre il 8600 è un dispositivo con un’enclosure principale e un’enclosure EBOD. In questa sezione viene descritta la procedura di ripristino per ogni scenario.

- [Dispositivo con soltanto un’enclosure principale](#8100)

- [Dispositivo con enclosure EBOD](#8600)

### Dispositivo con solo un’enclosure principale <a name="8100">

Se si verifica una perdita di alimentazione su uno degli alimentatori, il sistema può continuare il normale funzionamento. Tuttavia, per garantire la disponibilità elevata del dispositivo, ripristinare l’alimentazione nell'alimentatore guasto appena possibile.

Se si verifica un guasto nell'alimentazione o un’interruzione dell'alimentazione su entrambi gli alimentatori, il sistema si arresta in modo ordinato e controllato. Quando l'alimentazione viene ripristinata, il sistema si accende automaticamente.

### Dispositivo con enclosure EBOD<a name="8600">

#### Interruzione dell'alimentazione su un solo alimentatore

Se si verifica una perdita di alimentazione su uno degli alimentatori sull’enclosure principale o EBOD, il sistema può continuare il normale funzionamento. Tuttavia, per garantire la disponibilità elevata del dispositivo, si prega di ripristinare l’alimentazione nell'alimentatore guasto appena possibile.

#### Perdita di energia su entrambi gli alimentatori dell’enclosure principale e dell’enclosure EBOD

Se si verifica un guasto nell'alimentazione o un’interruzione dell'alimentazione su entrambi gli alimentatori, l’enclosure EBOD si arresta immediatamente e l’enclosure principale si arresta in modo ordinato e controllato. Quando viene ripristinata l'alimentazione, il dispositivo si accende automaticamente.

Se l’alimentazione è disattivata manualmente, effettuare i passaggi seguenti per ripristinare l'alimentazione del sistema.

1. Accendere l'enclosure EBOD.

2. Dopo l'accensione dell’enclosure EBOD, accendere l'enclosure principale.

### Perdita di energia su entrambi gli alimentatori dell’enclosure EBOD

Quando si configurano i cavi, è necessario assicurarsi che l'enclosure EBOD non sia mai collegata da sola a una PDU separata. Se l'enclosure EBOD e l’enclosure principale non hanno esito positivo contemporaneamente, il sistema viene ripristinato.

Se solo l’enclosure EBOD ha esito negativo su entrambi gli alimentatori, il sistema non viene ripristinato automaticamente. Per attivare il sistema e farlo tornare integro, procedere come segue:

1. Se l'enclosure principale è accesa, disattivare entrambi i PCM.

2. Attendere alcuni minuti per arrestare il sistema.

3. Accendere l'enclosure EBOD.

4. Dopo l'accensione dell’enclosure EBOD, accendere l'enclosure principale.

## Attivare un dispositivo dopo la perdita della connessione dell’enclosure principale e dell’enclosure EBOD

Se la connessione si interrompe tra il controller in standby e il controller EBOD corrispondente, il dispositivo continua a funzionare. Se la connessione tra il controller attivo del sistema e il controller EBOD corrispondente viene persa, dovrebbe verificarsi il failover e il dispositivo dovrebbe continuare a funzionare normalmente.

Quando vengono rimossi entrambi i cavi Serial Attached SCSI (SAS) o viene interrotta la connessione tra l'enclosure EBOD e l'enclosure principale, il dispositivo smette di funzionare. A questo punto, eseguire la procedura seguente.

### Per attivare il dispositivo dopo la perdita della connessione

1. Accesso posteriore del dispositivo.

2. Se la connessione via cavo SAS tra l'enclosure EBOD e l’enclosure principale viene interrotta, tutti i LED SAS sull’enclosure EBOD sono spenti.

3. Arrestare entrambi i Power and Cooling Module (PCM) sull'enclosure EBOD e sull’enclosure principale.

4. Attendere che tutti gli indicatori sul retro di entrambe le enclosure si spengano.

5. Reinserire i cavi SAS e assicurarsi che vi sia una buona connessione tra l'enclosure EBOD e l’enclosure principale.

6. Accendere prima l’enclosure EBOD girando l'interruttore di alimentazione di entrambi i PCM su ON.

7. Assicurarsi che l'enclosure EBOD sia attiva controllando che il LED verde sia acceso.

8. Accendere l'enclosure principale.

9. Assicurarsi che l'enclosure principale sia attiva controllando che il LED verde del controller sia acceso.

10. Verificare che la connessione dell’enclosure EBOD con l’enclosure principale sia buona controllando che i LED SAS (quattro per controller EBOD) siano tutti accesi.

>[AZURE.IMPORTANT] Se i cavi SAS sono difettosi o la connessione tra l'enclosure EBOD e l’enclosure principale non è valida, quando si attiva il sistema, esso entra in modalità di ripristino. Se ciò accade, [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).

## Spegnere un dispositivo in esecuzione

Potrebbe essere necessario arrestare un dispositivo StorSimple in esecuzione se deve essere spostato o messo fuori servizio oppure se ha un componente che non funziona correttamente e che deve essere sostituito. Le procedure sono diverse a seconda del modello del dispositivo StorSimple (8100 o 8600). Il modello 8100 ha una sola enclosure principale, mentre il 8600 è un dispositivo con un’enclosure principale e un’enclosure EBOD. In questa sezione vengono illustrati i passaggi per arrestare un dispositivo in esecuzione.

- [Dispositivo con enclosure principale](#8100a)

- [Dispositivo con enclosure EBOD](#8600a)

### Dispositivo con enclosure principale<a name="8100a"> 

Per arrestare il dispositivo in modo ordinato e controllato, è possibile usare il portale di Azure classico oppure Windows PowerShell per StorSimple.

>[AZURE.IMPORTANT] Non arrestare un dispositivo in esecuzione utilizzando il pulsante di alimentazione sul retro del dispositivo.
>
>Prima di spegnere il dispositivo, assicurarsi che tutti i componenti del dispositivo siano integri. Nel portale di Azure classico passare a **Dispositivi** > **Manutenzione** > **Stato hardware** e verificare che lo stato di tutti i componenti sia verde. Questo vale solo per un sistema integro. Se il dispositivo viene arrestato per la sostituzione di un componente che non funziona correttamente, verrà visualizzato lo stato di errore (rosso) o danneggiato (giallo) per i rispettivi componenti nello **Stato hardware**.

Dopo aver effettuato l'accesso a Windows PowerShell per StorSimple o al portale di Azure classico, seguire i passaggi della procedura [Arrestare un dispositivo StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device).

### Dispositivo con enclosure EBOD<a name="8600a">

>[AZURE.IMPORTANT] Prima di arrestare l'enclosure principale ed EBOD, verificare che tutti i componenti del dispositivo siano integri. Nel portale di Azure classico passare a **Dispositivi** > **Manutenzione** > **Stato hardware** e verificare che tutti i componenti siano integri.

#### Per arrestare un dispositivo in esecuzione con enclosure EBOD

1. Eseguire tutti i passaggi della procedura [Arrestare un dispositivo StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device) per l'enclosure principale.

2. Dopo che l'enclosure principale è stata arrestata, arrestare l'enclosure EBOD girando entrambi gli interruttori del Power and Cooling Module (PCM).

3. Per verificare che l'enclosure EBOD sia stata chiusa, controllare che tutte le luci nella parte posteriore dell’enclosure EBOD siano spente.

>[AZURE.NOTE] I cavi SAS che consentono di collegare l'enclosure EBOD all'enclosure principale non devono essere rimossi fino a dopo l'arresto del sistema.

## Passaggi successivi

[Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md)se si riscontrano problemi durante l'attivazione o l'arresto di un dispositivo StorSimple.

<!---HONumber=AcomDC_0831_2016-->