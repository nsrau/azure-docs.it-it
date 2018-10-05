---
title: Procedura dettagliata per l'esempio Project Acoustics - Servizi cognitivi
description: Questa procedura dettagliata descrive la scena di esempio Unity per Project Acoustics, incluse la distribuzione desktop e la realtà virtuale.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: eaf7ff9f7f791fd6d04e6b76d256b4987c50cd13
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434093"
---
# <a name="unity-sample-walkthrough"></a>Procedura dettagliata per l'esempio Unity
Questa procedura dettagliata si riferisce all'esempio Project Acoustics. Per altre informazioni su Project Acoustics, vedere [Introduction to Project Acoustics](what-is-acoustics.md) (Introduzione a Project Acoustics). Per informazioni sull'aggiunta del pacchetto Project Acoustics a un progetto Unity esistente, vedere la [guida introduttiva](getting-started.md).

## <a name="requirements-for-running-the-sample-project"></a>Requisiti per l'esecuzione del progetto di esempio
* Unity 2018.2+, con la versione del runtime di scripting .NET 4.x
* Editor Unity per Windows 64 bit
* Questo esempio supporta come destinazioni Windows Desktop, la piattaforma UWP e Android, inclusi i caschi con visore
* È necessaria la sottoscrizione di Azure Batch per il processo di preparazione

## <a name="sample-project-setup"></a>Configurazione del progetto di esempio
Scaricare e importare **MicrosoftAcoustics.Sample.unitypackage**. Al momento dell'importazione, le impostazioni di progetto tra cui **Spatializer** (Spazializzatore) e **Scripting Runtime Version** (Versione runtime scripting) vengono aggiornate in base ai requisiti del plug-in. Al termine, verrà visualizzato un errore nella console Unity da **AcousticsGeometry.cs** relativo alla modifica della versione del runtime di scripting a **.NET 4.x Equivalent**. La modifica di questa impostazione avviene durante l'importazione del pacchetto, ma per diventare effettiva richiede il riavvio di Unity. Riavviare Unity.

## <a name="running-the-sample"></a>Esecuzione dell'esempio
L'esempio include una scena di demo, **Assets/AcousticsDemo/ProjectAcousticsDemo.unity**. Questa scena ha tre origini audio. Per impostazione predefinita, viene riprodotta una sola origine audio mentre le altre due sono in pausa. Queste si trovano sotto **Origini audio** in **Gerarchia**. Per permettere la creazione di uno script di navigazione generico, la telecamera principale è figlio dell'oggetto CameraHolder. 

![Visualizzazione gerarchica](media/SampleHierarchyView.png)

La scena è già stata preparata e ha un file di voce ACE associato all'oggetto Prefab **MicrosoftAcoustics** in **Hierarchy** (Gerarchia). 

Ascoltare l'audio della scena facendo clic sul pulsante di riproduzione nell'editor Unity. Sul desktop, usare W, A, S, D e il mouse per spostarsi. Per confrontare la scena con e senza acustica, premere il pulsante **R** fino a quando il testo di sovrimpressione diventa rosso e indica "Acustica: disabilitata." Per visualizzare i tasti di scelta rapida per altri controlli, premere **F1**. Tutti i controlli sono anche utilizzabili facendo clic con il pulsante destro del mouse per selezionare l'azione da eseguire, quindi facendo clic a sinistra per eseguire l'azione.

## <a name="targeting-other-platforms"></a>Uso di altre piattaforme di destinazione
L'esempio contiene le impostazioni per l'esecuzione su Windows Desktop, piattaforma UWP, realtà mista, Android e Oculus Go. Per impostazione predefinita, il progetto è configurato per Windows Desktop. Per impostare come destinazione una piattaforma di realtà virtuale, passare alle impostazioni del lettore in **Edit > Project Settings > Player** (Modifica > Impostazioni progetto > Lettore), individuare **XR Settings** (Impostazioni XR) e selezionare la casella di controllo **Virtual Reality Supported** (Realtà virtuale supportata).

![Abilitare la realtà virtuale](media/VRSupport.png)  

Connettere le cuffie per la realtà virtuale al PC. Passare a **File > Build Settings** (File > Impostazioni compilazione) e fare clic su **Build and Run** (Compila ed esegui) per distribuire l'esempio nelle cuffie per la realtà virtuale. Spostarsi nella scena con i controller di movimento per le cuffie oppure provare a usare W, A, S, D sulla tastiera.    
Per impostare come destinazione Android e Oculus Go, scegliere Android nel menu **Build Settings** (Impostazioni compilazione). Fare clic su **Switch Target** (Cambia destinazione) e quindi su **Build and Run** (Compila ed esegui). La scena di esempio verrà distribuita nel dispositivo Android connesso. Per informazioni sullo sviluppo di Unity per Android, vedere la [documentazione di Unity](https://docs.unity3d.com/Manual/android-GettingStarted.html).

![Impostare Android come destinazione](media/TargetAndroid.png)  

## <a name="next-steps"></a>Passaggi successivi
* [Creare un account di Azure](create-azure-account.md) per le preparazioni personalizzate
* Esaminare il [processo di progettazione](design-process.md)

