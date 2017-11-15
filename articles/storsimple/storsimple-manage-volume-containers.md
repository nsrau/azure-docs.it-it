---
title: Gestire i contenitori del volume di StorSimple | Microsoft Docs
description: "Viene illustrato come è possibile utilizzare la pagina di contenitori del volume di servizio StorSimple Manager per aggiungere, modificare o eliminare un contenitore del volume."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 1c64ce75-1fd3-4d3b-9304-d4dc0fc2b069
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 64f88e27267b36ea8654093b6e725e942f5f5c1c
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-volume-containers"></a>Utilizzare il servizio StorSimple Manager per gestire i contenitori di volume StorSimple.
> [!NOTE]
> Il portale classico per StorSimple è deprecato. Le istanze di Gestione dispositivi StorSimple passeranno automaticamente al nuovo portale di Azure in base alla pianificazione per la funzionalità deprecata. Si riceveranno un messaggio di posta elettronica e una notifica del portale relativi a questo passaggio. Anche questo documento verrà ritirato a breve. Per la versione di questo articolo per il nuovo portale di Azure, vedere [Utilizzare il servizio StorSimple Manager per gestire i contenitori di volume StorSimple](storsimple-8000-manage-volume-containers.md). Per domande relative al passaggio, vedere [Domande frequenti: spostamento nel portale di Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Panoramica
In questa esercitazione viene illustrato come utilizzare il servizio StorSimple Manager per creare e gestire contenitori dei volumi StorSimple.

Un contenitore del volume in un dispositivo StorSimple di Microsoft Azure contiene uno o più volumi che condividono l'account di archiviazione, la crittografia e le impostazioni del consumo di larghezza di banda. Un dispositivo può avere più contenitori del volume per tutti i volumi. 

Un contenitore del volume ha i seguenti attributi:

* **Volumi** : i volumi StorSimple a livelli o aggiunti in locale all'interno del contenitore del volume. Un contenitore del volume può includere fino a 256 volumi StorSimple.
* **Crittografia** : una chiave di crittografia che può essere definita per ogni contenitore del volume. Questa chiave viene utilizzata per crittografare i dati inviati dal dispositivo StorSimple nel cloud. Una chiave a livello militare AES-256 bit viene utilizzata con la chiave immesso dall'utente. Per proteggere i dati, è consigliabile abilitare sempre la crittografia di archiviazione cloud.
* **Account di archiviazione** : l'account di archiviazione collegato al provider di servizi di archiviazione cloud. Tutti i volumi che risiedono in un contenitore di volumi condividono questo account di archiviazione. È possibile scegliere un account di archiviazione da un elenco esistente o creare un nuovo account quando si crea il contenitore del volume e quindi specificare le credenziali di accesso per l'account.
* **Larghezza di banda cloud** : larghezza di banda usata dal dispositivo quando i dati dal dispositivo vengono inviati al cloud. È possibile applicare un controllo della larghezza di banda specificando un valore compreso tra 1 e 1000 Mbps quando si definisce questo contenitore. Se si desidera che il dispositivo usi tutti larghezza di banda disponibile, impostare questo campo su illimitata. È inoltre possibile creare e applicare un modello di larghezza di banda per l'allocazione della larghezza di banda in base alla pianificazione.

![Andare alla pagina Contenitori di volumi.](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

Questa procedure seguenti viene illustrato come utilizzare quest'ultimo **contenitori del Volume** per completare le operazioni comuni seguenti:

* Aggiungere un contenitore di volumi 
* Modificare un contenitore di volumi 
* Eliminare un contenitore di volumi 

## <a name="add-a-volume-container"></a>Aggiungere un contenitore di volumi
Eseguire i passaggi seguenti per aggiungere un contenitore del volume.

[!INCLUDE [storsimple-add-volume-container](../../includes/storsimple-add-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modificare un contenitore di volumi
Eseguire i passaggi seguenti per modificare un contenitore del volume.

[!INCLUDE [storsimple-modify-volume-container](../../includes/storsimple-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Eliminare un contenitore di volumi
Un contenitore del volume ha volumi all'interno di esso. Può essere eliminato solo se sono stati eliminati tutti i volumi in esso contenuti. Eseguire la procedura seguente per eliminare un contenitore del volume.

[!INCLUDE [storsimple-delete-volume-container](../../includes/storsimple-delete-volume-container.md)]

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sulla [gestione di volumi StorSimple](storsimple-manage-volumes.md). 
* Ulteriori informazioni sull’ [utilizzo del servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

