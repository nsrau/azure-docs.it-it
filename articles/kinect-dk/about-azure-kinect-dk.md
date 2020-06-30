---
title: Informazioni su Azure Kinect DK
description: Panoramica degli strumenti di Azure Kinect DK (Developer Kit) e dei servizi integrati.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: overview
ms.date: 06/26/2019
keywords: azure, kinect, panoramica, dev kit, DK, dispositivo, profondità, rilevamento del corpo, voce, servizi cognitivi, Software Development Kit, SDK, firmware
ms.openlocfilehash: 6b3215ba6761521214ebffa2a100c5a1a030ab4c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "85277863"
---
# <a name="about-azure-kinect-dk"></a>Informazioni su Azure Kinect DK

 ![Azure Kinect DK](./media/index/device-image.jpg)

Azure Kinect DK è un kit per sviluppatori con sensori di intelligenza artificiale avanzati che offre sofisticati modelli di visione artificiale e conversione voce/testo.  Kinect contiene un sensore di profondità, un gruppo di microfoni spaziali con videocamera e un sensore di orientamento in un dispositivo all-in-one compatto con più modalità, opzioni e SDK (Software Development Kit). È disponibile per l'acquisto in [Microsoft Store online](https://www.microsoft.com/p/azure-kinect-dk/8pp5vxmd9nhq).

L'ambiente di sviluppo di Azure Kinect DK è costituito dai seguenti SDK:

- Sensor SDK per l'accesso a dispositivi e sensori di basso livello.
- Body Tracking SDK per il rilevamento di corpi in 3D.
- Speech Cognitive Services SDK per abilitare l'accesso del microfono e i servizi Voce di Azure basati sul cloud.

È inoltre possibile usare servizi di visione di Servizi cognitivi con la videocamera RGB del dispositivo.

   ![Diagramma degli SDK di Kinect di Azure](./media/quickstarts/sdk-diagram.jpg)

## <a name="azure-kinect-sensor-sdk"></a>Azure Kinect Sensor SDK

Azure Kinect Sensor SDK fornisce l'accesso ai sensori di basso livello per la configurazione dei dispositivi e dei sensori hardware di Kinect DK di Azure.

Per altre informazioni su Azure Kinect Sensor SDK, vedere [Uso di Sensor SDK](about-sensor-sdk.md).

### <a name="azure-kinect-sensor-sdk-features"></a>Funzionalità di Azure Kinect Sensor SDK

Sensor SDK include le funzionalità seguenti che devono essere installate ed eseguite in Azure Kinect DK:

- Accesso e controllo delle modalità della videocamera RGB (modalità IR passiva, oltre alle modalità di profondità dei campi visivi ampi e stretti) 
- Accesso e controllo della videocamera RGB, ad esempio esposizione e bilanciamento del bianco 
- Accesso ai sensori di movimento (giroscopio e accelerometro) 
- Streaming sincronizzato della videocamera RGB di profondità, con ritardo configurabile tra videocamere 
- Controllo della sincronizzazione di dispositivi esterni con offset del ritardo configurabile tra dispositivi 
- Accesso ai metadati dei fotogrammi della videocamera per la risoluzione delle immagine, il timestamp e così via 
- Accesso ai dati di calibrazione del dispositivo 

### <a name="azure-kinect-sensor-sdk-tools"></a>Strumenti di Azure Kinect Sensor SDK

In Sensor SDK sono disponibili gli strumenti seguenti:

- Uno strumento visualizzatore per monitorare i flussi dei dati dei dispositivi e configurare le diverse modalità.
- Uno strumento di registrazione dei sensori e un'API di lettura della riproduzione che usa il formato di contenitori Matroska.
- Uno strumento di aggiornamento del firmware di Azure Kinect DK.

## <a name="azure-kinect-body-tracking-sdk"></a>Azure Kinect Body Tracking SDK

Body Tracking SDK include una libreria e un runtime di Windows per il rilevamento dei corpi in 3D, se usati con l'hardware di Azure Kinect DK.

### <a name="azure-kinect-body-tracking-features"></a>Funzionalità di rilevamento del corpo di Azure Kinect

Nell'SDK di accompagnamento sono disponibili le funzionalità di rilevamento del corpo seguenti:

- Fornisce la segmentazione del corpo.
- Contiene uno scheletro anatomicamente corretto per ogni corpo parziale o completo nel campo visivo.
- Offre un'identità univoca per ogni corpo.
- È in grado di rilevare i corpi nel corso del tempo.

### <a name="azure-kinect-body-tracking-tools"></a>Strumenti di rilevamento del corpo di Azure Kinect

- Lo strumento di rilevamento del corpo include un visualizzatore per rilevare corpi in 3D.

## <a name="speech-cognitive-services-sdk"></a>Speech SDK di Servizi cognitivi

Speech SDK abilita i servizi voce connessi ad Azure.

### <a name="speech-services"></a>Servizi Voce

- Riconoscimento vocale
- Traduzione vocale
- Sintesi vocale

>[!NOTE]
>Azure Kinect DK non include altoparlanti.

Per altri dettagli e informazioni, vedere la [documentazione del servizio Voce](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="vision-services"></a>Servizi di visione

I servizi di [visione di Servizi cognitivi di Azure](https://azure.microsoft.com/services/cognitive-services/directory/vision/) offrono servizi di Azure in grado di identificare e analizzare il contenuto all'interno di immagini e video.

- [Visione artificiale](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
- [Viso](https://azure.microsoft.com/services/cognitive-services/face/)
- [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/)
- [Content Moderator](https://azure.microsoft.com/services/cognitive-services/content-moderator/)
- [Visione personalizzata](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)

I servizi si evolvono e migliorano costantemente, quindi assicurarsi di controllare regolarmente la disponibilità di [Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/) nuovi o aggiuntivi per migliorare l'applicazione. Per un'anteprima dei nuovi servizi emergenti, vedere i [lab di Servizi cognitivi](https://labs.cognitive.microsoft.com/).

## <a name="azure-kinect-hardware-requirements"></a>Requisiti hardware di Azure Kinect

Azure Kinect DK integra la tecnologia dei sensori più recente di Microsoft in un singolo accessorio connesso tramite USB. Per altre informazioni, vedere [Specifiche hardware di Azure Kinect DK](hardware-specification.md).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata presentata una panoramica di Azure Kinect DK. Il passaggio successivo consiste nell'approfondire la soluzione e configurarla.

> [!div class="nextstepaction"]
>[Avvio rapido: Configurare Azure Kinect DK](set-up-azure-kinect-dk.md)
