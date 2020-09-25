---
title: Sincronizzare più dispositivi Azure Kinect DK
description: Questo articolo illustra i vantaggi della sincronizzazione tra più dispositivi e come configurare i dispositivi per la sincronizzazione.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: Azure, Kinect, specifiche, hardware, DK, funzionalità, profondità, colori, RGB, IMU, array, profondità, più sincronizzazioni
ms.openlocfilehash: 7c79101de5e5455ae2ff9fd8b5d8369a3832631c
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361161"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>Sincronizzare più dispositivi Azure Kinect DK

Ogni dispositivo Azure Kinect DK include le porte di sincronizzazione 3,5-mm (**sincronizzazione in** **uscita e sincronizzazione**) che è possibile usare per collegare più dispositivi insieme. Dopo aver connesso i dispositivi, il software può coordinare la tempistica dei trigger tra di essi. 

Questo articolo illustra come connettere e sincronizzare i dispositivi.

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>Vantaggi dell'uso di più dispositivi Azure Kinect DK

Esistono diversi motivi per usare più dispositivi Azure Kinect DK, inclusi i seguenti:

- Inserire le occlusioni. Sebbene le trasformazioni dei dati di Azure Kinect DK producano una singola immagine, le due fotocamere (Depth e RGB) sono in realtà a distanza limitata. L'offset rende possibili le occlusioni. Un'occlusione si verifica quando un oggetto Foreground blocca la visualizzazione della parte di un oggetto in background per una delle due fotocamere in un dispositivo. Nell'immagine del colore risultante, l'oggetto di primo piano sembra eseguire il cast di un'ombreggiatura sull'oggetto in background.  
   Nel diagramma seguente, ad esempio, la fotocamera a sinistra Visualizza il pixel grigio "P2". Tuttavia, l'oggetto di primo piano bianco blocca il raggio IR della fotocamera a destra. La fotocamera a destra non contiene dati per "P2".  
   ![Il diagramma mostra due fotocamere dirette nello stesso punto con una di esse bloccate.](./media/occlusion.png)  
   Dispositivi sincronizzati aggiuntivi possono fornire i dati bloccati.
- Analizza gli oggetti in tre dimensioni.
- Aumentare la frequenza dei fotogrammi effettiva a un valore maggiore di 30 fotogrammi al secondo (FPS).
- Acquisisci più immagini a colori 4K della stessa scena, tutte allineate entro 100 microsecondi &mu; al centro di esposizione.
- Aumentare la copertura della fotocamera nello spazio.

## <a name="plan-your-multi-device-configuration"></a>Pianificare la configurazione di più dispositivi

Prima di iniziare, verificare di aver esaminato le [specifiche hardware Kinect dk di Azure](hardware-specification.md) e la [videocamera depth di Azure Kinect dk](depth-camera.md).

### <a name="select-a-device-configuration"></a>Selezionare una configurazione del dispositivo

Per la configurazione del dispositivo è possibile usare uno degli approcci seguenti:

- **Configurazione a catena Daisy**. Sincronizzare un dispositivo master e un massimo di otto dispositivi subordinati.  
   ![Diagramma che illustra come connettere i dispositivi di Azure Kinect DK in una configurazione a catena Daisy.](./media/multicam-sync-daisychain.png)
- **Configurazione a stella**. Sincronizzare un dispositivo master e un massimo di due dispositivi subordinati.  
   ![Diagramma che illustra come configurare più dispositivi Azure DK in una configurazione a stella.](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>Uso di un trigger di sincronizzazione esterno

In entrambe le configurazioni, il dispositivo master fornisce il segnale di attivazione per i dispositivi subordinati. Tuttavia, è possibile usare un'origine esterna personalizzata per il trigger di sincronizzazione. Ad esempio, è possibile usare questa opzione per sincronizzare le acquisizioni di immagini con altre apparecchiature. Nella configurazione a catena di Margherita o nella configurazione a stella, l'origine del trigger esterno si connette al dispositivo master.

L'origine del trigger esterno deve funzionare nello stesso modo del dispositivo master. Deve fornire un segnale di sincronizzazione con le caratteristiche seguenti:

- Attivo alto
- Larghezza impulso: maggiore di 8 &mu; s
- TTL 5V/CMOS
- Capacità massima di guida: non inferiore a 8 milliampere (mA)
- Supporto della frequenza: esattamente 30 FPS, 15 FPS e 5 FPS (la frequenza del segnale VSYNC del Master della fotocamera dei colori)

L'origine del trigger deve recapitare il segnale alla **sincronizzazione** del dispositivo master nella porta usando un cavo audio da 3,5 mm. È possibile usare un cavo stereo o mono. Azure Kinect DK Shorts tutti i manicotti e gli anelli del connettore di cavi audio insieme e ne costituisce un motivo. Come illustrato nel diagramma seguente, il dispositivo riceve il segnale di sincronizzazione solo dal suggerimento del connettore.

![Configurazioni di cavi per un segnale trigger esterno](./media/resources/camera-trigger-signal.jpg)

Per altre informazioni su come usare le apparecchiature esterne, vedere [usare il registratore Kinect di Azure con dispositivi sincronizzati esterni](record-external-synchronized-units.md)

### <a name="plan-your-camera-settings-and-software-configuration"></a>Pianificare le impostazioni della fotocamera e la configurazione del software

Per informazioni su come configurare il software per controllare le fotocamere e usare i dati dell'immagine, vedere [Azure Kinect Sensor SDK](about-sensor-sdk.md).

Questa sezione illustra diversi fattori che influiscono sui dispositivi sincronizzati (ma non sui singoli dispositivi). Il software deve prendere in considerazione questi fattori.

#### <a name="exposure-considerations"></a>Considerazioni sull'esposizione
Se si vuole controllare la tempistica esatta di ogni dispositivo, è consigliabile usare un'impostazione di esposizione manuale. In base all'impostazione di esposizione automatica, ogni fotocamera a colori può modificare dinamicamente l'esposizione effettiva. Poiché l'esposizione influiscono sull'intervallo di tempo, queste modifiche spingono rapidamente le fotocamere fuori dalla sincronizzazione.

Nel ciclo di acquisizione immagini evitare di impostare ripetutamente la stessa impostazione di esposizione. Chiamare l'API solo una volta quando è necessario.

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>Evitare interferenze tra più fotocamere di profondità

Quando più fotocamere di profondità rappresentano campi di visualizzazione sovrapposti per la creazione dell'immagine, ogni fotocamera deve creare il proprio laser associato. Per impedire che i laser interferiscano tra loro, le acquisizioni della fotocamera devono essere bilanciate l'una dall'altra 160 μs o superiore.

Per ogni acquisizione della fotocamera con profondità, il laser viene attivato nove volte ed è attivo solo per 125 &mu; s ogni volta. Il laser è quindi inattivo per 14505 &mu; s o 23905 &mu; s, a seconda della modalità di funzionamento. Questo comportamento indica che il punto di partenza per il calcolo dell'offset è 125 &mu; s.

Inoltre, le differenze tra il clock della fotocamera e l'orologio del firmware del dispositivo aumentano l'offset minimo a 160 &mu; s. Per calcolare un offset più preciso per la configurazione, annotare la modalità di profondità utilizzata e fare riferimento alla [tabella di temporizzazione non elaborata del sensore di profondità](hardware-specification.md#depth-sensor-raw-timing). Utilizzando i dati di questa tabella, è possibile calcolare l'offset minimo (il tempo di esposizione di ogni fotocamera) utilizzando l'equazione seguente:

> *Tempo di esposizione* =*IR Pulses* ( &times; *larghezza impulso*IR) + (tempo di inattività*periodi* di inattività &times; *Idle Time*)

Quando si usa un offset di 160 &mu; s, è possibile configurare fino a nove fotocamere di profondità aggiuntive in modo che ogni laser si accenda mentre gli altri laser sono inattivi.

Nel software usare ```depth_delay_off_color_usec``` o per assicurarsi ```subordinate_delay_off_master_usec``` che ciascun laser IR venga attivato nella propria &mu; finestra di 160 s o abbia un campo di visualizzazione diverso.

## <a name="prepare-your-devices-and-other-hardware"></a>Preparare i dispositivi e altri componenti hardware

Oltre a più dispositivi Azure Kinect DK, potrebbe essere necessario ottenere altri computer host e altro hardware per supportare la configurazione che si vuole compilare. Usare le informazioni in questa sezione per assicurarsi che tutti i dispositivi e l'hardware siano pronti prima di iniziare la configurazione.

### <a name="azure-kinect-dk-devices"></a>Dispositivi Azure Kinect DK

Per ogni dispositivo di Azure Kinect DK che si vuole sincronizzare, seguire questa procedura:

- Verificare che nel dispositivo sia installato il firmware più recente. Per altre informazioni su come aggiornare i dispositivi, vedere aggiornare il [firmware di Azure Kinect dk](update-device-firmware.md). 
- Rimuovere la copertura del dispositivo per visualizzare le porte di sincronizzazione.
- Prendere nota del numero di serie per ogni dispositivo. Questo numero verrà usato più avanti nel processo di installazione.

### <a name="host-computers"></a>Computer host

In genere, ogni DK di Azure Kinect usa il proprio computer host. È possibile usare un controller host dedicato, a seconda di come si usa il dispositivo e la quantità di dati trasferiti tramite la connessione USB. 

Assicurarsi che l'SDK del sensore Kinect di Azure sia installato in ogni computer host. Per altre informazioni su come installare Sensor SDK, vedere [Guida introduttiva: configurare](set-up-azure-kinect-dk.md)l'ambiente Kinect di Azure. 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Computer Linux: memoria USB in Ubuntu

Per impostazione predefinita, i computer host basati su Linux allocano il controller USB solo 16 MB di memoria kernel per gestire i trasferimenti USB. Questo importo è in genere sufficiente per supportare un solo servizio Kinect di Azure. Tuttavia, per supportare più dispositivi, il controller USB deve avere più memoria. Per aumentare la memoria, attenersi alla seguente procedura:

1. Edit/**etc/default/grub**.
1. Individuare la riga seguente:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   Sostituirlo con questa riga:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > Questi comandi impostano la memoria USB su 32 MB. Si tratta di un'impostazione di esempio per due volte il valore predefinito. È possibile impostare un valore molto più grande, a seconda della soluzione.
1. Eseguire **sudo update-grub**.
1. Riavviare il computer.

### <a name="cables"></a>Cavi

Per connettere i dispositivi gli uni agli altri e ai computer host, è necessario usare cavi da maschio a maschio 3,5-mm (noti anche come cavo audio 3,5-mm). I cavi devono avere una lunghezza inferiore a 10 metri e possono essere stereo o mono.

Il numero di cavi che è necessario avere dipende dal numero di dispositivi usati e anche dalla configurazione specifica del dispositivo. La casella DK di Azure Kinect non include i cavi. È necessario acquistarli separatamente.

Se si connettono i dispositivi nella configurazione a stella, è necessario disporre anche di uno splitter per le cuffie.

## <a name="connect-your-devices"></a>Connettere i dati dell'utente

**Per connettere i dispositivi di Azure Kinect DK in una configurazione a catena Daisy**

1. Connettere ogni DK di Azure Kinect a Power.
1. Connettere ogni dispositivo al relativo computer host. 
1. Selezionare un dispositivo come dispositivo master e collegare un cavo audio 3,5-mm alla relativa porta di **sincronizzazione** .
1. Collegare l'altra estremità del cavo alla **sincronizzazione nella** porta del primo dispositivo subordinato.
1. Per connettere un altro dispositivo, collegare un altro cavo alla porta di **sincronizzazione in uscita** del primo dispositivo subordinato e nella **sincronizzazione in** porta del dispositivo successivo.
1. Ripetere il passaggio precedente fino a quando tutti i dispositivi non sono connessi. L'ultimo dispositivo deve avere una sola connessione via cavo. La porta di **sincronizzazione in uscita** deve essere vuota.

**Per connettere i dispositivi di Azure Kinect DK in una configurazione a stella**

1. Connettere ogni DK di Azure Kinect a Power.
1. Connettere ogni dispositivo al relativo computer host. 
1. Selezionare un dispositivo come dispositivo master, quindi collegare la singola estremità della barra di divisione delle cuffie alla relativa porta di **sincronizzazione** .
1. Connettere i cavi audio 3,5-mm alle estremità "Split" dello splitter auricolare.
1. Collegare l'altra estremità di ogni cavo alla **sincronizzazione nella** porta di uno dei dispositivi subordinati.

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>Verificare che i dispositivi siano connessi e che comunicano

Per verificare che i dispositivi siano connessi correttamente, usare il [Visualizzatore Kinect di Azure](azure-kinect-viewer.md). Ripetere questa procedura per testare ogni dispositivo subordinato in combinazione con il dispositivo master

> [!IMPORTANT]  
> Per questa procedura è necessario conosce il numero di serie di ogni DK di Azure Kinect.

1. Aprire due istanze del Visualizzatore Kinect di Azure.
1. In **Apri dispositivo**selezionare il numero di serie del dispositivo subordinato che si desidera testare.  
   ![Apri dispositivo](./media/open-devices.png)
   > [!IMPORTANT]  
   > Per ottenere un allineamento di acquisizione delle immagini preciso tra tutti i dispositivi, è necessario avviare il dispositivo master per ultimo.  
1. In **sincronizzazione esterna**selezionare **Sub**.  
   ![Avvio della fotocamera subordinata](./media/sub-device-start.png)
1.  Selezionare **Inizio**.  
    > [!NOTE]  
    > Poiché si tratta di un dispositivo subordinato, il Visualizzatore Kinect di Azure non visualizza un'immagine dopo l'avvio del dispositivo. Non viene visualizzata alcuna immagine finché il dispositivo subordinato non riceve un segnale di sincronizzazione dal dispositivo master.
1. Dopo l'avvio del dispositivo subordinato, usare l'altra istanza del Visualizzatore Kinect di Azure per aprire il dispositivo master.
1. In **sincronizzazione esterna**selezionare **Master**.
1. Selezionare **Inizio**.

Quando il dispositivo Kinect di Azure master viene avviato, entrambe le istanze del Visualizzatore Kinect di Azure devono visualizzare le immagini.

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>Calibrare i dispositivi come set sincronizzato

Dopo aver verificato che i dispositivi comunicano correttamente, si è pronti per calibrare tali dispositivi per produrre immagini in un singolo dominio.

In un singolo dispositivo, le fotocamere di profondità e RGB vengono calibrate in modo corretto per essere utilizzate insieme. Tuttavia, quando più dispositivi devono collaborare, devono essere calibrati per determinare come trasformare un'immagine dal dominio della fotocamera che l'ha acquisita nel dominio della fotocamera che si vuole usare per elaborare le immagini.

Sono disponibili più opzioni per la calibrazione incrociata dei dispositivi. Microsoft fornisce l' [esempio di codice dello schermo verde di GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen), che usa il metodo OpenCV. Il file Leggimi per questo esempio di codice fornisce altri dettagli e istruzioni per la taratura dei dispositivi.

Per altre informazioni sulla calibrazione, vedere [usare le funzioni di calibrazione Kinect di Azure](use-calibration-functions.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato i dispositivi sincronizzati, è anche possibile imparare a usare il
> [!div class="nextstepaction"]
> [API di registrazione e riproduzione dell'SDK del sensore Kinect di Azure](record-playback-api.md)

## <a name="related-topics"></a>Argomenti correlati

- [Informazioni su Azure Kinect Sensor SDK](about-sensor-sdk.md)
- [Specifiche hardware Kinect DK di Azure](hardware-specification.md) 
- [Guida introduttiva: configurare il DK di Azure Kinect](set-up-azure-kinect-dk.md) 
- [Aggiornare il firmware DK di Azure Kinect](update-device-firmware.md) 
- [Reimpostare Azure Kinect DK](reset-azure-kinect-dk.md) 
- [Visualizzatore Kinect di Azure](azure-kinect-viewer.md) 
