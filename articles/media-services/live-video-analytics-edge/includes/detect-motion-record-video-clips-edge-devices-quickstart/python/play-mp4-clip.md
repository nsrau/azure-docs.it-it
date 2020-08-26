---
ms.openlocfilehash: f4a1cc432a50a555fe6e050ca318b4cfaf1092d4
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682359"
---
I file MP4 vengono scritti in una directory del dispositivo perimetrale configurata nel file *.env* usando la chiave OUTPUT_VIDEO_FOLDER_ON_DEVICE. Se è stato usato il valore predefinito, i risultati dovrebbero essere nella cartella */var/media/* .

Per riprodurre il clip MP4:

1. Passare al gruppo di risorse, individuare la macchina virtuale e connettersi usando Azure Bastion.

    ![Gruppo di risorse](../../../media/quickstarts/resource-group.png)
    
    ![VM](../../../media/quickstarts/virtual-machine.png)
1. Accedere usando le credenziali generate al momento della [configurazione delle risorse di Azure](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources). 
1. Al prompt dei comandi passare alla directory pertinente. Il percorso predefinito è */var/media*. Si vedranno i file MP4 nella directory.

    ![Output](../../../media/quickstarts/samples-output.png) 

1. Usare il comando [SCP (copia sicura)](../../../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) per copiare i file nel computer locale. 
1. Riprodurre i file usando il [lettore multimediale VLC](https://www.videolan.org/vlc/) o un qualsiasi altro lettore MP4.
