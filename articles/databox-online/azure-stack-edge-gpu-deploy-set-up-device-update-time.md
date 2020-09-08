---
title: Esercitazione su come connettersi a un dispositivo Azure Stack Edge con GPU, configurarlo e attivarlo nel portale di Azure | Microsoft Docs
description: Esercitazione su come distribuire Azure Stack Edge con GPU, connettersi al dispositivo fisico, configurarlo e attivarlo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 41055fbd455d3f7b9da63ee8f7420f008ea75a00
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254509"
---
# <a name="tutorial-configure-device-settings-for-azure-stack-edge-with-gpu"></a>Esercitazione: Configurare le impostazioni del dispositivo per Azure Stack Edge con GPU

Questa esercitazione descrive come configurare le impostazioni correlate al dispositivo Azure Stack Edge con una GPU su scheda. È possibile configurare il nome del dispositivo, il server di aggiornamento e il server di riferimento ora tramite l'interfaccia utente Web locale.

Per completare le impostazioni del dispositivo sono necessari circa 5-7 minuti.

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti
> * Configura impostazioni dispositivo
> * Configurare l'aggiornamento 
> * Configurare l'ora

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare le impostazioni correlate al dispositivo nel dispositivo Azure Stack Edge con GPU, verificare di:

* Per il dispositivo fisico:

    - Aver installato il dispositivo fisico come descritto in [Installare Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
    - Avere configurato la rete e abilitato e configurato la rete di calcolo nel dispositivo, come descritto nei dettagli in [Esercitazione: Configurare la rete per Azure Stack Edge con GPU](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).


## <a name="configure-device-settings"></a>Configura impostazioni dispositivo

Per configurare le impostazioni correlate al dispositivo, seguire questa procedura.
 
1. Nel riquadro **Configurazione dispositivo** selezionare **Configura** per **Dispositivo**.

    ![Pagina "Dispositivo" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-1.png)

    Nella pagina **Dispositivo** seguire questa procedura:

    1. Immettere un nome descrittivo per il dispositivo. Il nome descrittivo deve contenere da 1 a 13 caratteri e può includere lettere, numeri e trattini.

    2. Specificare un **Dominio DNS** per il dispositivo. Questo dominio viene usato per configurare il dispositivo come file server.

    3. Per convalidare e applicare le impostazioni del dispositivo configurate, selezionare **Applica**.

        ![Pagina "Dispositivo" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

        Se il nome del dispositivo e il dominio DNS sono stati modificati, i certificati autofirmati generati automaticamente nel dispositivo non funzioneranno. Quando si configurano i certificati è necessario scegliere una delle opzioni seguenti: 
        
        - Generare e scaricare i certificati del dispositivo. 
        - Caricare i certificati personali per il dispositivo, inclusa la catena di firma.
    

        ![Pagina "Dispositivo" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-3.png)

    4. Quando il nome del dispositivo e il dominio DNS vengono modificati, vengono creati gli endpoint SMB e NFS.  

    5. Dopo aver applicato le impostazioni tornare ad **Attività iniziali**.

## <a name="configure-update"></a>Configurare l'aggiornamento

1. Nel riquadro **Configurazione dispositivo** selezionare **Configura** per **Aggiornamento**. È ora possibile configurare la posizione da cui scaricare gli aggiornamenti per il dispositivo.  

    ![Pagina "Server di aggiornamento" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-1.png)

    - È possibile ottenere gli aggiornamenti direttamente dal **server Microsoft Update**.

        ![Pagina "Server di aggiornamento" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-2.png)

        È anche possibile scegliere di distribuire gli aggiornamenti da **Windows Server Update Services** (WSUS). Specificare il percorso del server WSUS.
        
        ![Pagina "Server di aggiornamento" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > Se viene configurato un server di Windows Update separato e si sceglie di connettersi tramite *HTTPS* (anziché *HTTP*), sono necessari i certificati della catena di firma richiesti per la connessione al server di aggiornamento. Per informazioni su come creare e caricare i certificati, vedere [Gestire i certificati](azure-stack-edge-j-series-manage-certificates.md). 

2. Selezionare **Applica**.
3. Dopo aver configurato il server di aggiornamento, tornare ad **Attività iniziali**.
    

## <a name="configure-time"></a>Configurare l'ora

Seguire questa procedura per configurare le impostazioni relative all'ora nel dispositivo. 

1. Nel riquadro **Configurazione dispositivo** selezionare **Ora**. È possibile selezionare il fuso orario e i server NTP primario e secondario per il dispositivo.  

    > [!IMPORTANT]
    > Sebbene le impostazioni dell'ora siano facoltative, è consigliabile configurare un server NTP primario e un server NTP secondario nella rete locale per il dispositivo. Se non è disponibile un server locale, è possibile configurare server NTP pubblici.
    
    I server NTP sono obbligatori in quanto il dispositivo deve sincronizzare l'ora e consentire l'autenticazione con i provider del servizio cloud.

    ![Pagina "Ora" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-1.png)

2. Nella pagina **Ora** eseguire le operazioni seguenti:
    
    1. Nell'elenco a discesa **Fuso orario** selezionare il fuso orario che corrisponde alla posizione geografica in cui viene distribuito il dispositivo.
        Il fuso orario predefinito per il dispositivo è PST. Il dispositivo utilizzerà questo fuso orario per tutte le operazioni pianificate.

    2. Nella casella **Server NTP primario** immettere il server primario per il dispositivo o accettare il valore predefinito di time.windows.com.  
        Assicurarsi che la rete consenta il traffico NTP dal data center a Internet.

    3. Facoltativamente, nella casella **Server NTP secondario** immettere un server secondario per il dispositivo.

    4. Per convalidare e applicare le impostazioni ora configurate, selezionare **Applica**.

        ![Pagina "Ora" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-2.png)

3. Dopo aver applicato le impostazioni tornare ad **Attività iniziali**.



## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti
> * Configura impostazioni dispositivo
> * Configurare l'aggiornamento 
> * Configurare l'ora

Per informazioni su come configurare i certificati per il dispositivo Azure Stack Edge, vedere:

> [!div class="nextstepaction"]
> [Configurare i certificati](./azure-stack-edge-gpu-deploy-configure-certificates.md)
