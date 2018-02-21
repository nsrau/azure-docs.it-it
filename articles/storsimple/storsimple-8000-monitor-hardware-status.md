---
title: Stato e componenti hardware di StorSimple serie 8000| Microsoft Docs
description: Informazioni su come monitorare i componenti hardware del dispositivo StorSimple tramite il servizio Gestione dispositivi StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 90724099842eac513c39dccf113ad1c0a63983f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Usare il servizio Gestione dispositivi StorSimple per monitorare i componenti hardware e lo stato
## <a name="overview"></a>Panoramica
Questo articolo descrive i vari componenti fisici e logici del dispositivo StorSimple serie 8000 in locale. Viene inoltre spiegato come monitorare lo stato del componente del dispositivo tramite il pannello **Stato e integrità hardware** nel servizio Gestione dispositivi StorSimple.

Nel pannello **Stato e integrità hardware** è indicato lo stato dell'hardware di tutti i componenti del dispositivo StorSimple.

Nell'elenco dei componenti per 8100, sono disponibili tre sezioni che descrivono:

* **Componenti condivisi** : questi non fanno parte dei controller, ad esempio unità disco, chassis, componenti PCM e sensori di temperatura PCM, tensione linea e corrente linea.
* **Componenti controller 0** : i componenti che si trovano nel controller 0, ad esempio controller, espansore SAS e connettore, sensori di temperatura controller e le varie interfacce di rete.
* **Componenti controller 1** : i componenti che costituiscono il controller 1, simili a quelli indicati per il controller 0.

Un dispositivo 8600 dispone di componenti aggiuntivi che corrispondono allo chassis EBOD (Extended Bunch of Disks). Nell'elenco dei componenti, sono disponibili cinque sezioni. Di queste, tre solo le sezioni che contengono i componenti nello chassis principale e sono identici a quelli descritti per 8100. Esistono due sezioni aggiuntive per lo chassis EBOD che descrivono:

* **Componenti controller 0** : i componenti che si trovano nello chassis EBOD 0, ad esempio controller EBOD, espansore SAS e connettore, sensori di temperatura controller.
* **Componenti controller 1** : i componenti che costituiscono lo chassis EBOD 1, simili a quelli indicati per lo chassis EBOD 0.
* **Componenti condivisi dello chassis EBOD** : i componenti presenti nello chassis EBOD e in PCM che non fanno parte del controller EBOD.

> [!NOTE]
> **Lo stato dell'hardware non è disponibile per un'appliance cloud StorSimple (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Monitorare lo stato dell'hardware
Per visualizzare lo stato dell'hardware di un componente del dispositivo, procedere come segue:

1. Passare a **Dispositivi**e selezionare uno specifico dispositivo StorSimple. Passare a **Monitoraggio > Integrità hardware**.

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Individuare la sezione **Componenti hardware** e scegliere tra i componenti disponibili. Fare clic sull'etichetta del componente per espandere l'elenco e visualizzare lo stato dei vari componenti del dispositivo. Vedere l'[elenco dettagliato dei componenti per l'enclosure principale](#component-list-for-primary-enclosure-of-storsimple-device) e l'[elenco dettagliato dei componenti per l'enclosure EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Per interpretare lo stato del componente, usare lo schema di codifica a colori seguente:
   
   * **Segno di spunta verde**: indica che un componente è integro con lo stato **OK**.
   * **Giallo**: indica un componente danneggiato con lo stato **Avviso**.
   * **Punto esclamativo rosso**: indica che un componente è nello stato di **Errore**.
   * **Bianco con testo nero** : indica che un componente non è presente.
   
   La schermata seguente mostra un dispositivo con componenti negli stati **OK**, **Avviso** ed **Errore**.
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Espandendo l'**elenco dei componenti condivisi**, è possibile vedere che NVRAM e il cluster sono danneggiati.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Espandendo l'elenco **Componenti Controller 1** è possibile vedere che il nodo del cluster è in errore.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Se si rileva un componente che non si trova in uno stato **Integro** , contattare il supporto tecnico Microsoft. Se gli avvisi sono attivati sul dispositivo, si riceverà un messaggio di avviso. Se è necessario sostituire un componente hardware in errore, vedere la [Guida alla sostituzione di componenti hardware](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Elenco di componenti per l’enclosure principale del dispositivo StorSimple
La tabella seguente indica i componenti fisici e logici contenuti nell'enclosure principale, presente nei dispositivi 8100 e 8600, del dispositivo StorSimple in locale.

| Componente | Modulo | type | Località | Unità sostituibile sul campo (FRU)? | DESCRIZIONE |
| --- | --- | --- | --- | --- | --- |
| Unità in slot [0-11] |Unità disco |Fisico |Condiviso |Sì |Viene visualizzata una riga per ogni unità SSD o le unità disco rigido nello chassis principale. |
| Sensore di temperatura ambientale |Chassis |Fisico |Condiviso |No  |Misura la temperatura all'interno dello chassis. |
| Sensore di temperatura piano intermedio |Chassis |Fisico |Condiviso |No  |Misura la temperatura del piano intermedio. |
| Allarme acustico |Chassis |Fisico |Condiviso |No  |Indica se il sottosistema di allarme acustico all'interno dello chassis è funzionale. |
| Chassis |Chassis |Fisico |Condiviso |Sì |Indica la presenza di uno chassis. |
| Impostazioni chassis |Chassis |Fisico |Condiviso |No  |Fa riferimento al pannello anteriore dello chassis. |
| Sensori di tensione linea |PCM |Fisico |Condiviso |No  |Numerosi sensori di tensione linea hanno lo stato visualizzato, che indica se la tensione misurata è compresa nella tolleranza. |
| Sensori di corrente linea |PCM |Fisico |Condiviso |No  |Numerosi sensori di corrente linea hanno lo stato visualizzato, che indica se la corrente misurata è compresa nella tolleranza. |
| Sensori di temperatura in PCM |PCM |Fisico |Condiviso |No  |Numerosi sensori di temperatura come i sensori Inlet e Hotspot hanno lo stato visualizzato, che indica se la temperatura misurata è compresa nella tolleranza. |
| Alimentazione [0-1] |PCM |Fisico |Condiviso |Sì |Viene visualizzata una riga per ogni alimentatore nei due PCM presenti sulla parte posteriore del dispositivo. |
| Raffreddamento [0-1] |PCM |Fisico |Condiviso |Sì |Viene visualizzata una riga per ognuno delle quattro ventole di raffreddamento che risiedono nei due PCM. |
| Batteria [0-1] |PCM |Fisico |Condiviso |Sì |Viene visualizzata una riga per ognuno dei moduli della batteria di backup inseriti nel PCM. |
| Metis |N/D |Logico |Condiviso |N/D |Visualizza lo stato delle batterie: se devono essere messe in carica e se si stanno avvicinando alla fine del ciclo di vita. |
| HDInsight |N/D |Logico |Condiviso |N/D |Visualizza lo stato del cluster creato tra i due moduli controller integrato. |
| Nodo del cluster |N/D |Logico |Condiviso |N/D |Indica lo stato del controller come parte del cluster. |
| Quorum del cluster |N/D |Logico | |N/D |Indica la presenza dell'appartenenza di maggioranza del disco nel pool di archiviazione di unità disco rigido. |
| Spazio dati unità disco rigido |N/D |Logico |Condiviso |N/D |Lo spazio di archiviazione utilizzato per i dati nel pool di archiviazione di unità disco rigido (HDD). |
| Spazio di gestione di unità disco rigido |N/D |Logico |Condiviso |N/D |Lo spazio riservato nel pool di archiviazione di unità disco rigido per le attività di gestione. |
| Spazio quorum unità disco rigido |N/D |Logico |Condiviso |N/D |Lo spazio riservato nel pool di archiviazione di unità disco rigido per il quorum del cluster. |
| Spazio sostituzione unità disco rigido |N/D |Logico |Condiviso |N/D |Lo spazio riservato nel pool di archiviazione di unità disco rigido per la sostituzione del controller. |
| Spazio dati unità SSD |N/D |Logico |Condiviso |N/D |Lo spazio di archiviazione utilizzato per i dati nel pool di archiviazione di unità SSD. |
| Spazio NVRAM SSD |N/D |Logico |Condiviso |N/D |Lo spazio di archiviazione nel pool di archiviazione di unità SSD dedicato per la logica NVRAM. |
| Pool di archiviazione di unità disco rigido |N/D |Logico |Condiviso |N/D |Visualizza lo stato del pool di archiviazione logica creato dal dispositivo HDD. |
| Pool di archiviazione di unità SSD |N/D |Logico |Condiviso |N/D |Visualizza lo stato del pool di archiviazione logica creato dal dispositivo SSD. |
| Controller [0-1] [stato] |I/O |Fisico |Controller |Sì |Visualizza lo stato del controller e se è in modalità attiva o standby all'interno dello chassis. |
| Sensori di temperatura nel controller |I/O |Fisico |Controller |No  |Numerosi sensori di temperatura, ad esempio modulo I/O, temperatura CPU, sensori DIMM e PCIe hanno lo stato visualizzato, che indica se la temperatura rilevata è compresa o meno nella tolleranza. |
| Espansore SAS |I/O |Fisico |Controller |No  |Indica lo stato dell'espansore SCSI associato seriale (SAS), che viene utilizzato per connettere al controller l'archiviazione integrata. |
| Connettore SAS [0-1] |I/O |Fisico |Controller |No  |Indica lo stato di ogni connettore SAS, che viene utilizzato per la connessione dell'archiviazione integrata all'espansore SAS. |
| Interconnessione piano intermedio SBB |I/O |Fisico |Controller |No  |Indica lo stato del connettore del piano intermedio, che viene utilizzato per connettere ogni controller al piano intermedio. |
| Core del processore |I/O |Fisico |Controller |No  |Indica lo stato dei core del processore all'interno di ogni controller. |
| Alimentatore elettronica dello chassis |I/O |Fisico |Controller |No  |Indica lo stato del sistema di alimentazione dello chassis. |
| Diagnostica elettronica dello chassis |I/O |Fisico |Controller |No  |Indica lo stato dei sottosistemi di diagnostica fornite dal controller. |
| Baseboard Management Controller (BMC) |I/O |Fisico |Controller |No  |Indica lo stato del controller BMC (Baseboard Management Controller), che è un processore di servizio specializzato che consente di monitorare il dispositivo hardware tramite sensori e comunica con l'amministratore di sistema tramite una connessione indipendente. |
| Ethernet |I/O |Fisico |Controller |No  |Indica lo stato di ciascuna delle interfacce di rete, vale a dire le porte di gestione e dati fornite sul controller. |
| NVRAM |I/O |Fisico |Controller |No  |Indica lo stato di NVRAM, una memoria RAM non volatile sottoposta a backup tramite la batteria che serve per mantenere le informazioni critiche dell'applicazione in caso di interruzione dell'alimentazione. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Elenco di componenti per l’enclosure EBOD del dispositivo StorSimple
Nella tabella seguente vengono descritti i componenti fisici e logici contenuti nell'enclosure EBOD (presente solo nel modello 8600) del dispositivo StorSimple in locale.

| Componente | Modulo | type | Località | FRU? | DESCRIZIONE |
| --- | --- | --- | --- | --- | --- |
| Unità in slot [0-11] |Unità disco |Fisico |Condiviso |Sì |Viene visualizzata una riga per ogni unità HDD nella parte anteriore dello chassis EBOD. |
| Sensore di temperatura ambientale |Chassis |Fisico |Condiviso |No  |Misura la temperatura all'interno dello chassis. |
| Sensore di temperatura piano intermedio |Chassis |Fisico |Condiviso |No  |Misura la temperatura del piano intermedio. |
| Allarme acustico |Chassis |Fisico |Condiviso |No  |Indica se il sottosistema di allarme acustico all'interno dello chassis è funzionale. |
| Chassis |Chassis |Fisico |Condiviso |Sì |Indica la presenza di uno chassis. |
| Impostazioni chassis |Chassis |Fisico |Condiviso |No  |Fa riferimento alle operazioni o al pannello anteriore dello chassis. |
| Sensori di tensione linea |PCM |Fisico |Condiviso |No  |Numerosi sensori di tensione linea hanno lo stato visualizzato, che indica se la tensione misurata è compresa nella tolleranza. |
| Sensori di corrente linea |PCM |Fisico |Condiviso |No  |Numerosi sensori di corrente linea hanno lo stato visualizzato, che indica se la corrente misurata è compresa nella tolleranza. |
| Sensori di temperatura in PCM |PCM |Fisico |Condiviso |No  |Numerosi sensori di temperatura, ad esempio i sensori Inlet e Hotspot hanno lo stato visualizzato, che indica se la temperatura misurata è compresa nella tolleranza. |
| Alimentazione [0-1] |PCM |Fisico |Condiviso |Sì |Viene visualizzata una riga per ogni alimentatore nei due PCM presenti sulla parte posteriore del dispositivo. |
| Raffreddamento [0-1] |PCM |Fisico |Condiviso |Sì |Viene visualizzata una riga per ognuno delle quattro ventole di raffreddamento che risiedono nei due PCM. |
| Archiviazione locale [HDD] |N/D |Logico |Condiviso |N/D |Visualizza lo stato del pool di archiviazione logica creato dal dispositivo HDD. |
| Controller [0-1] [stato] |I/O |Fisico |Controller |Sì |Visualizza lo stato dei controller nel modulo EBOD. |
| Sensori di temperatura in EBOD |I/O |Fisico |Controller |No  |Numerosi sensori di temperatura di ogni controller hanno lo stato visualizzato, che indica se la temperatura misurata è compresa nella tolleranza. |
| Espansore SAS |I/O |Fisico |Controller |No  |Indica lo stato dell'espansore SAS, che viene utilizzato per connettere al controller l'archiviazione integrata. |
| Connettore SAS [0-2] |I/O |Fisico |Controller |No  |Indica lo stato di ogni connettore SAS, che viene utilizzato per la connessione dell'archiviazione integrata all'espansore SAS. |
| Interconnessione piano intermedio SBB |I/O |Fisico |Controller |No  |Indica lo stato del connettore del piano intermedio, che viene utilizzato per connettere ogni controller al piano intermedio. |
| Alimentatore elettronica dello chassis |I/O |Fisico |Controller |No  |Indica lo stato del sistema di alimentazione dello chassis. |
| Diagnostica elettronica dello chassis |I/O |Fisico |Controller |No  |Indica lo stato dei sottosistemi di diagnostica fornite dal controller. |
| Connessione al controller del dispositivo |I/O |Fisico |Controller |No  |Indica lo stato della connessione tra il modulo I/O EBOD e il controller del dispositivo. |

## <a name="next-steps"></a>Passaggi successivi
* Per usare il servizio Gestione dispositivi StorSimple per la gestione del dispositivo, passare all'articolo relativo all'[uso del servizio Gestione dispositivi StorSimple per gestire il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
* Se è necessario risolvere i problemi relativi a un componente del dispositivo con lo stato danneggiato o guasto, fare riferimento a [Indicatori di monitoraggio di StorSimple](storsimple-monitoring-indicators.md).
* Per sostituire un componente hardware guasto, vedere la [Guida alla sostituzione di componenti hardware di StorSimple](storsimple-hardware-component-replacement.md).
* Se si continuano a riscontrare problemi nei dispositivi, [contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md).

