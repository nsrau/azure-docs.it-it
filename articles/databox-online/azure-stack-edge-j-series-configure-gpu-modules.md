---
title: Eseguire un modulo GPU sul dispositivo GPU Microsoft Azure Stack Edge | Microsoft Docs
description: Viene descritto come configurare ed eseguire un modulo in GPU in un dispositivo Azure Stack Edge tramite l'portale di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 419b8beb866711e80b4366df4398eb248256021b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266953"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-device"></a>Configurare ed eseguire un modulo in GPU in Azure Stack dispositivo perimetrale

Il dispositivo Azure Stack Edge contiene una o più unità di elaborazione grafica (GPU). Le GPU sono una scelta comune per i calcoli di intelligenza artificiale, perché offrono funzionalità di elaborazione parallela e sono più veloci al rendering delle immagini rispetto alle unità di elaborazione centrali (CPU). Per altre informazioni sulla GPU contenuta nel dispositivo Azure Stack Edge, vedere le [specifiche tecniche del dispositivo Azure stack Edge](azure-stack-edge-gpu-technical-specifications-compliance.md).

Questo articolo descrive come configurare ed eseguire un modulo sulla GPU sul dispositivo Azure Stack Edge. In questo articolo si useranno le **cifre** del modulo contenitore disponibili pubblicamente scritte per le GPU NVIDIA T4. Questa procedura può essere usata per configurare qualsiasi altro modulo pubblicato da NVIDIA per queste GPU.


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. È possibile accedere a un dispositivo Azure Stack Edge a 1 nodo abilitato per GPU. Questo dispositivo viene attivato con una risorsa in Azure.  

## <a name="configure-module-to-use-gpu"></a>Configurare il modulo per l'uso della GPU

Per configurare un modulo per l'uso della GPU sul dispositivo Azure Stack Edge per eseguire un modulo, seguire questa procedura.

1. Nella portale di Azure passare alla risorsa associata al dispositivo. 

2. Passare a **Calcolo Edge > Inizia subito**. Nel riquadro **Configura calcolo Edge** selezionare Configura.

    ![Configurare il modulo per l'uso della GPU 1](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. Nel pannello **Configura calcolo Edge** :

    1. Per **Hub**tutto, scegliere **Crea nuovo**.
    2. Specificare un nome per la risorsa dell'hub Internet delle cose che si vuole creare per il dispositivo. PER usare un livello gratuito, selezionare una risorsa esistente. 
    3. Prendere nota del dispositivo IoT Edge e del dispositivo gateway di Internet delle cose creato con la risorsa dell'hub Internet delle cose. Queste informazioni vengono usate nei passaggi successivi.

    ![Configurare il modulo per l'uso della GPU 2](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. Sono necessari alcuni minuti per creare la risorsa dell'hub Internet. Dopo aver creato la risorsa, nel riquadro **Configura calcolo Edge** selezionare **Visualizza configurazione** per visualizzare i dettagli della risorsa hub Internet.

    ![Configurare il modulo per l'uso della GPU 4](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-4.png)

5. Passare a **gestione automatica dispositivi > IOT Edge**.

    ![Configurare il modulo per l'uso della GPU 6](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

    Nel riquadro di destra viene visualizzato il dispositivo IoT Edge associato al dispositivo Azure Stack Edge. Corrisponde al dispositivo IoT Edge creato nel passaggio precedente durante la creazione della risorsa dell'hub Internet. 
    
6. Selezionare questo dispositivo IoT Edge.

   ![Configurare il modulo per l'uso della GPU 7](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

7.  Selezionare **Imposta moduli**.

    ![Configurare il modulo per l'uso della GPU 8](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

8. Selezionare **+ Aggiungi** e quindi selezionare **+ IOT Edge modulo**. 

    ![Configurare il modulo per l'uso della GPU 9](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

9. Nella scheda **aggiungi IOT Edge modulo** :

    1. Specificare l' **URI dell'immagine**. Qui si useranno le **cifre** del modulo NVIDIA disponibili pubblicamente. 
    
    2. Impostare il **criterio di riavvio** su **Always**.
    
    3. Impostare **lo stato desiderato** su **in esecuzione**.
    
    ![Configurare il modulo per l'uso della GPU 10](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

10. Nella scheda **variabili di ambiente** specificare il nome della variabile e il valore corrispondente. 

    1. Per fare in modo che il modulo corrente usi una GPU in questo dispositivo, usare la NVIDIA_VISIBLE_DEVICES. 

    2. Impostare il valore su 0 o 1. In questo modo si garantisce che il dispositivo sia usato almeno una GPU per questo modulo. Quando si imposta il valore su 0, 1, significa che entrambe le GPU del dispositivo sono utilizzate da questo modulo.

        ![Configurare il modulo per l'uso della GPU 11](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

        Per altre informazioni sulle variabili di ambiente che è possibile usare con la GPU NVIDIA, vedere [runtime del contenitore NVIDIA](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > È possibile eseguire il mapping di una GPU solo a un modulo. Un modulo può tuttavia usare una o nessuna GPU. 

11. Immettere un nome per il modulo. A questo punto è possibile scegliere di specificare l'opzione di creazione del contenitore e modificare le impostazioni del modulo gemello o, se necessario, selezionare **Aggiungi**. 

    ![Configurare il modulo per l'uso della GPU 12](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

12. Verificare che il modulo sia in esecuzione e selezionare **Verifica + crea**.    

    ![Configurare il modulo per l'uso della GPU 13](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

13. Nella scheda **Verifica e crea** sono visualizzate le opzioni di distribuzione selezionate. Esaminare le opzioni e selezionare **Crea**.
    
    ![Configurare il modulo per l'uso della GPU 14](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

14. Prendere nota dello stato di **Runtime** del modulo. 
    
    ![Configurare il modulo per l'uso della GPU 15](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    Sono necessari alcuni minuti per la distribuzione del modulo. Selezionare **Aggiorna** per visualizzare l'aggiornamento **dello stato di runtime** per l' **esecuzione**.

    ![Configurare il modulo per l'uso della GPU 16](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [variabili di ambiente che è possibile usare con la GPU NVIDIA](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).
