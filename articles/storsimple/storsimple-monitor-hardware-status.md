<properties 
   pageTitle="Stato e componenti hardware di StorSimple | Microsoft Azure"
   description="Informazioni su come monitorare i componenti hardware del dispositivo StorSimple tramite il servizio StorSimple Manager."
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
   ms.date="08/18/2016"
   ms.author="alkohli" />

# Utilizzare il servizio StorSimple Manager per monitorare le componenti hardware e lo stato

## Overview

In questo articolo vengono descritti i vari componenti fisici e logici del dispositivo StorSimple in locale. Viene inoltre spiegato come monitorare lo stato di componente del dispositivo utilizzando la pagina **Manutenzione** nel servizio StorSimple Manager.

Nella pagina **Manutenzione** è indicato lo stato dell'hardware di tutti i componenti del dispositivo StorSimple.

Nell'elenco dei componenti per 8100, sono disponibili tre sezioni che descrivono:

- **Componenti condivisi**: questi non fanno parte dei controller, ad esempio unità disco, chassis, componenti PCM e sensori di temperatura PCM, tensione linea e corrente linea.

- **Componenti controller 0**: i componenti che si trovano nel controller 0, ad esempio controller, espansore SAS e connettore, sensori di temperatura controller e le varie interfacce di rete.

- **Componenti controller 1**: i componenti che costituiscono il controller 1, simili a quelli indicati per il controller 0.

Un dispositivo 8600 dispone di componenti aggiuntivi che corrispondono allo chassis EBOD (Extended Bunch of Disks). Nell'elenco dei componenti, sono disponibili cinque sezioni. Di queste, tre solo le sezioni che contengono i componenti nello chassis principale e sono identici a quelli descritti per 8100. Esistono due sezioni aggiuntive per lo chassis EBOD che descrivono:

- **Componenti condivisi dello chassis EBOD**: i componenti presenti nello chassis EBOD e in PCM che non fanno parte del controller EBOD.

- **Componenti controller 0**: i componenti che si trovano nello chassis EBOD 0, ad esempio controller EBOD, espansore SAS e connettore, sensori di temperatura controller.

- **Componenti controller 1**: i componenti che costituiscono lo chassis EBOD 1, simili a quelli indicati per lo chassis EBOD 0.

>[AZURE.NOTE] **La sezione dello stato dell'hardware non è presente nella pagina Manutenzione per un dispositivo virtuale StorSimple (1100).**


## Monitorare lo stato dell'hardware

Per visualizzare lo stato dell'hardware di un componente del dispositivo, procedere come segue:

1. Passare a **Dispositivi** e selezionare uno specifico dispositivo StorSimple. Fare clic per passare al menu a livello di dispositivo e quindi fare clic su **Manutenzione**.
2. Individuare la sezione **Stato hardware** e scegliere tra i componenti disponibili (come descritto in precedenza). Fare clic su una freccia che precede l'etichetta del componente per espandere l'elenco e visualizzare lo stato dei vari componenti del dispositivo. Vedere l'[elenco dettagliato dei componenti per lo chassis principale](#component-list-for-primary-enclosure-of-storsimple-device) e l'[elenco dettagliato dei componenti per lo chassis EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).

2. Utilizzare il seguente schema di codifica a colori per interpretare lo stato del componente:
	-  **Segno di spunta verde**: indica che il componente è **Integro** oppure **OK**.
	-  **Giallo**: indica un componente in stato di **avviso**.
	-  **Punto esclamativo rosso**; indica che un componente è nello stato di **Errore** o **Richiede attenzione**.
	-  **Bianco con testo nero**: indica che un componente non è presente.

3. Se si rileva un componente che non si trova in uno stato **Integro**, contattare il supporto tecnico Microsoft. Se gli avvisi sono attivati sul dispositivo, si riceverà un messaggio di avviso. Se è necessario sostituire un componente hardware in errore, vedere la [Guida alla sostituzione di componenti hardware](storsimple-hardware-component-replacement.md).


## Elenco di componenti per l’enclosure principale del dispositivo StorSimple

Nella tabella seguente vengono descritti i componenti fisici e logici contenuti nell’enclosure principale del dispositivo StorSimple in locale.

|Componente|Modulo|Tipo|Percorso|Unità sostituibile sul campo (FRU)?|Descrizione|
|---|---|---|---|---|---|
|Unità in slot [0-11]|Unità disco|Fisico|Condiviso|Sì|Viene visualizzata una riga per ogni unità SSD o le unità disco rigido nello chassis principale.|
|Sensore di temperatura ambientale|Chassis|Fisico|Condiviso|No|Misura la temperatura all'interno dello chassis.|
|Sensore di temperatura piano intermedio|Chassis|Fisico|Condiviso|No|Misura la temperatura del piano intermedio.|
|Allarme acustico|Chassis|Fisico|Condiviso|No|Indica se il sottosistema di allarme acustico all'interno dello chassis è funzionale.|
|Chassis|Chassis|Fisico|Condiviso|Sì|Indica la presenza di uno chassis.|
|Impostazioni chassis|Chassis|Fisico|Condiviso|No|Fa riferimento al pannello anteriore dello chassis.|
|Sensori di tensione linea|PCM|Fisico|Condiviso|No|Numerosi sensori di tensione linea hanno lo stato visualizzato, che indica se la tensione misurata è compresa nella tolleranza.|
|Sensori di corrente linea|PCM|Fisico|Condiviso|No|Numerosi sensori di corrente linea hanno lo stato visualizzato, che indica se la corrente misurata è compresa nella tolleranza.|
|Sensori di temperatura in PCM|PCM|Fisico|Condiviso|No|Numerosi sensori di temperatura come i sensori Inlet e Hotspot hanno lo stato visualizzato, che indica se la temperatura misurata è compresa nella tolleranza.|
|Alimentazione [0-1]|PCM|Fisico|Condiviso|Sì|Viene visualizzata una riga per ogni alimentatore nei due PCM presenti sulla parte posteriore del dispositivo.|
|Raffreddamento [0-1]|PCM|Fisico|Condiviso|Sì|Viene visualizzata una riga per ognuno delle quattro ventole di raffreddamento che risiedono nei due PCM.|
|Batteria [0-1]|PCM|Fisico|Condiviso|Sì|Viene visualizzata una riga per ognuno dei moduli della batteria di backup inseriti nel PCM.|
|Metis|N/D|Logico|Condiviso|N/D|Visualizza lo stato delle batterie: se devono essere messe in carica e se si stanno avvicinando alla fine del ciclo di vita.|
|HDInsight|N/D|Logico|Condiviso|N/D|Visualizza lo stato del cluster creato tra i due moduli controller integrato.|
|Nodo del cluster|N/D|Logico|Condiviso|N/D|Indica lo stato del controller come parte del cluster.|
|Quorum del cluster|N/D|Logico||N/D|Indica la presenza dell'appartenenza di maggioranza del disco nel pool di archiviazione di unità disco rigido.|
|Spazio dati unità disco rigido|N/D|Logico|Condiviso|N/D|Lo spazio di archiviazione utilizzato per i dati nel pool di archiviazione di unità disco rigido (HDD).|
|Spazio di gestione di unità disco rigido|N/D|Logico|Condiviso|N/D|Lo spazio riservato nel pool di archiviazione di unità disco rigido per le attività di gestione.|
|Spazio quorum unità disco rigido|N/D|Logico|Condiviso|N/D|Lo spazio riservato nel pool di archiviazione di unità disco rigido per il quorum del cluster.|
|Spazio sostituzione unità disco rigido|N/D|Logico|Condiviso|N/D|Lo spazio riservato nel pool di archiviazione di unità disco rigido per la sostituzione del controller.|
|Spazio dati unità SSD|N/D|Logico|Condiviso|N/D|Lo spazio di archiviazione utilizzato per i dati nel pool di archiviazione di unità SSD.|
|Spazio NVRAM SSD|N/D|Logico|Condiviso|N/D|Lo spazio di archiviazione nel pool di archiviazione di unità SSD dedicato per la logica NVRAM.|
|Pool di archiviazione di unità disco rigido|N/D|Logico|Condiviso|N/D|Visualizza lo stato del pool di archiviazione logica creato dal dispositivo HDD.|
|Pool di archiviazione di unità SSD|N/D|Logico|Condiviso|N/D|Visualizza lo stato del pool di archiviazione logica creato dal dispositivo SSD.|
|Controller [0-1] [stato]|I/O|Fisico|Controller|Sì|Visualizza lo stato del controller e se è in modalità attiva o standby all'interno dello chassis.|
|Sensori di temperatura nel controller|I/O|Fisico|Controller|No|Numerosi sensori di temperatura, ad esempio modulo I/O, temperatura CPU, sensori DIMM e PCIe hanno lo stato visualizzato, che indica se la temperatura rilevata è compresa o meno nella tolleranza.|
|Espansore SAS|I/O|Fisico|Controller|No|Indica lo stato dell'espansore SCSI associato seriale (SAS), che viene utilizzato per connettere al controller l'archiviazione integrata.|
|Connettore SAS [0-1]|I/O|Fisico|Controller|No|Indica lo stato di ogni connettore SAS, che viene utilizzato per la connessione dell'archiviazione integrata all'espansore SAS.|
|Interconnessione piano intermedio SBB|I/O|Fisico|Controller|No|Indica lo stato del connettore del piano intermedio, che viene utilizzato per connettere ogni controller al piano intermedio.|
|Core del processore|I/O|Fisico|Controller|No|Indica lo stato dei core del processore all'interno di ogni controller.|
|Alimentatore elettronica dello chassis|I/O|Fisico|Controller|No|Indica lo stato del sistema di alimentazione dello chassis.|
|Diagnostica elettronica dello chassis|I/O|Fisico|Controller|No|Indica lo stato dei sottosistemi di diagnostica fornite dal controller.|
|Baseboard Management Controller (BMC)|I/O|Fisico|Controller|No|Indica lo stato del controller BMC (Baseboard Management Controller), che è un processore di servizio specializzato che consente di monitorare il dispositivo hardware tramite sensori e comunica con l'amministratore di sistema tramite una connessione indipendente.|
|Ethernet|I/O|Fisico|Controller|No|Indica lo stato di ciascuna delle interfacce di rete, vale a dire le porte di gestione e dati fornite sul controller.|
|NVRAM|I/O|Fisico|Controller|No|Indica lo stato di NVRAM, una memoria RAM non volatile sottoposta a backup tramite la batteria che serve per mantenere le informazioni critiche dell'applicazione in caso di interruzione dell'alimentazione.|

## Elenco di componenti per l’enclosure EBOD del dispositivo StorSimple

Nella tabella seguente vengono descritti i componenti fisici e logici contenuti nell’enclosure EBOD del dispositivo StorSimple in locale.

|Componente|Modulo|Tipo|Percorso|FRU?|Descrizione|
|---|---|---|---|---|---|
|Unità in slot [0-11]|Unità disco|Fisico|Condiviso|Sì|Viene visualizzata una riga per ogni unità HDD nella parte anteriore dello chassis EBOD.|
|Sensore di temperatura ambientale|Chassis|Fisico|Condiviso|No|Misura la temperatura all'interno dello chassis.|
|Sensore di temperatura piano intermedio|Chassis|Fisico|Condiviso|No|Misura la temperatura del piano intermedio.|
|Allarme acustico|Chassis|Fisico|Condiviso|No|Indica se il sottosistema di allarme acustico all'interno dello chassis è funzionale.|
|Chassis|Chassis|Fisico|Condiviso|Sì|Indica la presenza di uno chassis.|
|Impostazioni chassis|Chassis|Fisico|Condiviso|No|Fa riferimento alle operazioni o al pannello anteriore dello chassis.|
|Sensori di tensione linea|PCM|Fisico|Condiviso|No|Numerosi sensori di tensione linea hanno lo stato visualizzato, che indica se la tensione misurata è compresa nella tolleranza.|
|Sensori di corrente linea|PCM|Fisico|Condiviso|No|Numerosi sensori di corrente linea hanno lo stato visualizzato, che indica se la corrente misurata è compresa nella tolleranza.|
|Sensori di temperatura in PCM|PCM|Fisico|Condiviso|No|Numerosi sensori di temperatura, ad esempio i sensori Inlet e Hotspot hanno lo stato visualizzato, che indica se la temperatura misurata è compresa nella tolleranza.|
|Alimentazione [0-1]|PCM|Fisico|Condiviso|Sì|Viene visualizzata una riga per ogni alimentatore nei due PCM presenti sulla parte posteriore del dispositivo.|
|Raffreddamento [0-1]|PCM|Fisico|Condiviso|Sì|Viene visualizzata una riga per ognuno delle quattro ventole di raffreddamento che risiedono nei due PCM.|
|Archiviazione locale [HDD]|N/D|Logico|Condiviso|N/D|Visualizza lo stato del pool di archiviazione logica creato dal dispositivo HDD.|
|Controller [0-1] [stato]|I/O|Fisico|Controller|Sì|Visualizza lo stato dei controller nel modulo EBOD.|
|Sensori di temperatura in EBOD|I/O|Fisico|Controller|No|Numerosi sensori di temperatura di ogni controller hanno lo stato visualizzato, che indica se la temperatura misurata è compresa nella tolleranza.|
|Espansore SAS|I/O|Fisico|Controller|No|Indica lo stato dell'espansore SAS, che viene utilizzato per connettere al controller l'archiviazione integrata.|
|Connettore SAS [0-2]|I/O|Fisico|Controller|No|Indica lo stato di ogni connettore SAS, che viene utilizzato per la connessione dell'archiviazione integrata all'espansore SAS.|
|Interconnessione piano intermedio SBB|I/O|Fisico|Controller|No|Indica lo stato del connettore del piano intermedio, che viene utilizzato per connettere ogni controller al piano intermedio.|
|Alimentatore elettronica dello chassis|I/O|Fisico|Controller|No|Indica lo stato del sistema di alimentazione dello chassis.|
|Diagnostica elettronica dello chassis|I/O|Fisico|Controller|No|Indica lo stato dei sottosistemi di diagnostica fornite dal controller.|
|Connessione al controller del dispositivo|I/O|Fisico|Controller|No|Indica lo stato della connessione tra il modulo I/O EBOD e il controller del dispositivo.|

## Passaggi successivi
- Per usare il servizio StorSimple Manager per la gestione del dispositivo, passare all'articolo relativo all'[uso del servizio StorSimple Manager per gestire il dispositivo StorSimple](storsimple-manager-service-administration.md).
 
- Se è necessario risolvere i problemi relativi a un componente del dispositivo con lo stato danneggiato o guasto, fare riferimento a [Indicatori di monitoraggio di StorSimple](storsimple-monitoring-indicators.md).

- Per sostituire un componente hardware guasto, vedere la [Guida alla sostituzione di componenti hardware di StorSimple](storsimple-hardware-component-replacement.md).

- Se si continuano a riscontrare problemi nei dispositivi, [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).

<!---HONumber=AcomDC_0831_2016-->