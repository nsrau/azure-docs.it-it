---
title: Download di Azure Kinect Body Tracking SDK
description: Informazioni su come scaricare ogni versione di Azure Kinect Sensor SDK in Windows o Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, download aggiornamento, più recente, disponibile, installazione, corpo, monitoraggio
ms.openlocfilehash: e3f8233d208e2a45c1af9a52a76b6064b15bfe4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276779"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Scaricare Azure Kinect Body Tracking SDK

Questo documento contiene i collegamenti per installare ogni versione di Azure Kinect Body Tracking SDK.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Contenuto dell'SDK del body tracking di Azure Kinect

- Intestazioni e librerie per la compilazione di un'applicazione di rilevamento del corpo con il DK di Azure.
- DLL ridistribuibili necessarie per il body tracking delle applicazioni con Azure Kinect DK.
- Applicazioni di rilevamento del corpo di esempio.

## <a name="windows-download-links"></a>Collegamenti per il download di Windows

Versione       | Download
--------------|----------
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1) MSI
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0) MSI
0.9.5 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100636) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.5) MSI
0.9.4 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100415) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.4) MSI
0.9.3 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100307) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.3) MSI
0.9.2 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100128) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.2) MSI
0.9.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100063) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.1) MSI
0.9.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=58402) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.0) MSI

## <a name="linux-installation-instructions"></a>Istruzioni per l'installazione in Linux

Attualmente, l'unica distribuzione supportata è Ubuntu 18.04. Per richiedere il supporto per altre distribuzioni, vedere [questa pagina](https://aka.ms/azurekinectfeedback).

Prima di tutto, è necessario configurare il [repository pacchetti di Microsoft](https://packages.microsoft.com/), seguendo le istruzioni disponibili [qui](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software).

Il pacchetto `libk4abt<major>.<minor>-dev` contiene le intestazioni e i file CMake da compilare con `libk4abt`.
Il `libk4abt<major>.<minor>` pacchetto contiene gli oggetti condivisi necessari per eseguire i file eseguibili che dipendono da e `libk4abt` il Visualizzatore di esempio.

Per le esercitazioni di base è richiesto il pacchetto `libk4abt<major>.<minor>-dev`. Per installarlo, eseguire

`sudo apt install libk4abt1.0-dev`

Se il comando riesce, l'SDK è pronto per l'uso.

> [!NOTE]
> Quando si installa l'SDK, ricordare il percorso in cui si esegue l'installazione, Ad esempio, "C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0". Gli esempi a cui viene fatto riferimento negli articoli di questo percorso sono disponibili.
> Gli esempi di rilevamento del corpo si trovano nella cartella [body-tracking-Samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) nel repository Azure-Kinect-Samples. Gli esempi a cui viene fatto riferimento negli articoli sono disponibili qui.

## <a name="change-log"></a>Registro delle modifiche

### <a name="v101"></a>v 1.0.1
* [Correzione di bug] Correzione del problema per cui l'SDK si arresta in modo anomalo se si carica onnxruntime.dll dal percorso in Windows build 19025 o versione successiva: [collegamento](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v 1.0.0
* Funzionalità Aggiungere il wrapper C# al programma di installazione MSI.
* [Correzione di bug] Correzione del problema per cui non è possibile rilevare correttamente la rotazione Head: [collegamento](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Correzione di bug] Correzione del problema per cui l'utilizzo della CPU passa al 100% nel computer Linux: [collegamento](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Campioni Aggiungere due campioni al repository di esempio. Nell'esempio 1 viene illustrato come trasformare i risultati del rilevamento del corpo dallo spazio di profondità al [collegamento](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)dello spazio dei colori; Nell'esempio 2 viene illustrato come rilevare il [collegamento](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) del piano piano

### <a name="v095"></a>v 0.9.5
* Funzionalità Supporto per C#. Il wrapper C# viene compresso nel pacchetto NuGet.
* Funzionalità Supporto per più Tracker. La creazione di più Tracker è consentita. Ora l'utente può creare più Tracker per tenere traccia dei corpi da diversi dispositivi Kinect di Azure.
* Funzionalità Supporto dell'elaborazione multithread per la modalità CPU. Quando è in esecuzione in modalità CPU, verranno usati tutti i core per ottimizzare la velocità.
* Funzionalità Aggiungi `gpu_device_id` a `k4abt_tracker_configuration_t` struct. Consente agli utenti di specificare un dispositivo GPU diverso da quello predefinito per eseguire l'algoritmo di rilevamento del corpo.
* [Correzione di bug/modifica di rilievo] Correzione del digitazione in un nome comune. Modificare il nome comune da `K4ABT_JOINT_SPINE_NAVAL` a `K4ABT_JOINT_SPINE_NAVEL` .

### <a name="v094"></a>v 0.9.4
* Funzionalità Aggiunta del supporto per le giunzioni di mano. L'SDK fornirà informazioni per tre giunzioni aggiuntive per ogni mano: HAND, HANDTIP, THUMB.
* Funzionalità Aggiungere il livello di confidenza per le stime per ogni giunzione rilevata.
* Funzionalità Aggiungere il supporto per la modalità CPU. Modificando il `cpu_only_mode` valore in `k4abt_tracker_configuration_t` , ora l'SDK può essere eseguito in modalità CPU che non richiede che l'utente disponga di una scheda grafica potente.

### <a name="v093"></a>v 0.9.3
* Funzionalità Pubblicare un nuovo modello DNN dnn_model_2_0. Onnx, che in gran parte migliora l'affidabilità del rilevamento del corpo.
* Funzionalità Disabilitare la smussatura temporale per impostazione predefinita. Le giunzioni rilevate saranno più reattive.
* Funzionalità Migliorare l'accuratezza della mappa dell'indice del corpo.
* [Correzione di bug] Correzione di un bug che indica che l'impostazione dell'orientamento del sensore non è valida.
* [Correzione di bug] Modificare il tipo di body_index_map da K4A_IMAGE_FORMAT_CUSTOM a K4A_IMAGE_FORMAT_CUSTOM8.
* [Problema noto] Due corpi di chiusura possono essere Uniti a un segmento a istanza singola.

### <a name="v092"></a>v 0.9.2
* [Modifica di rilievo] Aggiornare in modo che dipenda dalla versione più recente di Azure Kinect Sensor SDK 1.2.0.
* [Modifica API] `k4abt_tracker_create` la funzione inizierà a richiedere un `k4abt_tracker_configuration_t` input. 
* [Modifica API] Modificare `k4abt_frame_get_timestamp_usec` l'API in `k4abt_frame_get_device_timestamp_usec` in modo che sia più specifica e coerente con Sensor SDK 1.2.0.
* Funzionalità Consente agli utenti di specificare l'orientamento di montaggio del sensore quando si crea lo strumento di rilevamento per ottenere risultati di rilevamento del corpo più accurati quando si monta un angolo diverso.
* Funzionalità Fornire nuova API `k4abt_tracker_set_temporal_smoothing` per modificare la quantità di smussatura temporale che l'utente desidera applicare.
* Funzionalità Aggiungere il wrapper C++ K4ABT. hpp.
* Funzionalità Aggiungere l'intestazione della definizione della versione k4abtversion. h.
* [Correzione di bug] Correzione di un bug che provocava un utilizzo estremamente elevato della CPU.
* [Correzione di bug] Correzione del bug di arresto anomalo del logger.

### <a name="v091"></a>v 0.9.1
* [Correzione di bug] Correzione della perdita di memoria durante l'eliminazione del rilevamento
* [Correzione di bug] Migliori messaggi di errore per le dipendenze mancanti
* [Correzione di bug] Errore senza arresti anomali durante la creazione di una seconda istanza di Tracker
* [Correzione di bug] Le variabili di ambiente del logger ora funzionano correttamente
* Supporto di Linux

### <a name="v090"></a>v 0.9.0

* [Modifica di rilievo] È stato eseguito il downgrade della dipendenza SDK a CUDA 10,0 (da CUDA 10,1). Il runtime di ONNX supporta ufficialmente fino a CUDA 10,0.
* [Modifica di rilievo] Passaggio al runtime di ONNX anziché al runtime di Tensorflow. Riduce il primo tempo di avvio del frame e l'utilizzo della memoria. Consente inoltre di ridurre le dimensioni binarie dell'SDK.
* [Modifica API] Rinominato `k4abt_tracker_queue_capture()` in `k4abt_tracker_enqueue_capture()`
* [Modifica API] Si è `k4abt_frame_get_body()` suddiviso in due funzioni separate: `k4abt_frame_get_body_skeleton()` e `k4abt_frame_get_body_id()` . A questo punto è possibile eseguire una query sull'ID corpo senza copiare sempre l'intera struttura di scheletro.
* [Modifica API] Aggiunta  `k4abt_frame_get_timestamp_usec()` funzione per semplificare la procedura che consente agli utenti di eseguire query sul timestamp del frame del corpo.
* Miglioramento dell'accuratezza dell'algoritmo di rilevamento del corpo e affidabilità del rilevamento

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Azure Kinect DK](about-azure-kinect-dk.md)

- [Configurare Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Configurare il rilevamento del corpo Kinect di Azure](body-sdk-setup.md)
