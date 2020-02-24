---
title: Reimpostare Azure Kinect DK
description: Descrive come reimpostare l'immagine di fabbrica di un dispositivo Azure Kinect DK
author: Teresa-Motiv
ms.author: v-tea
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: kinect, reimpostazione
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: f851579712a9549c88f1ca652aea4f31457da132
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201899"
---
# <a name="reset-azure-kinect-dk"></a>Reimpostare Azure Kinect DK

In alcune situazioni, può essere necessario reimpostare l'immagine di fabbrica del dispositivo Azure Kinect DK, ad esempio se un aggiornamento del firmware non è stato installato correttamente.

1. Spegnere il dispositivo Azure Kinect DK. A questo scopo, rimuovere il cavo USB e il cavo di alimentazione.
  ![Diagramma che mostra la posizione della vite che copre il pulsante di reimpostazione.](media/reset-azure-kinect-dk-diagram.png)
1. Per trovare il pulsante di reimpostazione, rimuovere la vite situata nella serratura di montaggio del treppiede.
1. Riconnettere il cavo di alimentazione.
1. Inserire la punta di una graffetta raddrizzata nel buco vuoto della vite, nella serratura di montaggio del treppiede.
1. Usare la graffetta per tenere premuto delicatamente il pulsante di reimpostazione.
1. Con il pulsante di reimpostazione premuto, riconnettere il cavo USB.
1. Dopo circa 3 secondi, la luce dell'indicatore di alimentazione diventa gialla. Quando la luce cambia, rilasciare il pulsante di reimpostazione.  
   
   Dopo aver rilasciato il pulsante di reimpostazione, la luce dell'indicatore di alimentazione lampeggia in bianco e in giallo mentre il dispositivo viene reimpostato. 
1. Attendere che la luce dell'indicatore di alimentazione diventi bianca continua.
1. Sostituire la vite nella serratura di montaggio del treppiede, sopra il pulsante di reimpostazione.
1. Usare il visualizzatore di Azure Kinect per verificare se il firmware è stato reimpostato. A questo scopo, avviare il [visualizzatore di Azure Kinect](azure-kinect-viewer.md) e quindi selezionare **Device firmware version info** (Informazioni sulla versione del firmware del dispositivo) per verificare la versione del firmware installata nel dispositivo Azure Kinect DK.

Assicurarsi sempre che nel dispositivo sia installata l'ultima versione del firmware. Per ottenere l'ultima versione del firmware, usare lo strumento Azure Kinect Firmware Tool. Per altre informazioni su come controllare lo stato del firmware, vedere [Controllare la versione del firmware del dispositivo](azure-kinect-firmware-tool.md#check-device-firmware-version).

## <a name="related-topics"></a>Argomenti correlati

- [Informazioni su Azure Kinect DK](about-azure-kinect-dk.md)
- [Configurare Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Specifiche hardware di Azure Kinect DK: ambiente operativo](hardware-specification.md#operating-environment)
- [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md)
- [Visualizzatore di Azure Kinect](azure-kinect-viewer.md)
- [Sincronizzazione tra più dispositivi Azure Kinect DK](multi-camera-sync.md)
