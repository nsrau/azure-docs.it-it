---
title: Esercitazione su come attivare il dispositivo Azure Stack Edge con GPU nel portale di Azure | Microsoft Docs
description: Esercitazione su come distribuire Azure Stack Edge con GPU per attivare il dispositivo fisico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 88be4d9753e48f70dae5666e800a54209ed6ba3f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267939"
---
# <a name="tutorial-activate-azure-stack-edge-with-gpu"></a>Esercitazione: Attivare Azure Stack Edge con GPU

Questa esercitazione descrive come è possibile attivare un dispositivo Azure Stack Edge con una GPU su scheda usando l'interfaccia utente Web locale.

Per completare il processo di attivazione sono necessari circa 5 minuti.

In questa esercitazione sono stati trattati gli argomenti seguenti:

> [!div class="checklist"]
> * Prerequisiti
> * Attivare il dispositivo fisico

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare il dispositivo Azure Stack Edge con GPU, assicurarsi di:

* Per il dispositivo fisico: 
    
    - Aver installato il dispositivo fisico come descritto in [Installare Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
    - Aver configurato le impostazioni di rete e di rete di calcolo come descritto in [Configurare la rete, la rete di calcolo e il proxy Web](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    - Avere caricato i certificati del dispositivo personali o generati nel dispositivo se è stato modificato il nome del dispositivo o il dominio DNS tramite la pagina **Dispositivo**. Se questo passaggio non è ancora stato eseguito, verrà visualizzato un errore durante l'attivazione del dispositivo e l'attivazione verrà bloccata. Per altre informazioni, vedere [Configurazione dei certificati](azure-stack-edge-gpu-deploy-configure-certificates.md).
    
* Disporre della chiave di attivazione dal servizio Azure Stack Edge creato per gestire il dispositivo Azure Stack Edge. Per altre informazioni, vedere [Preparare la distribuzione di Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Attivare il dispositivo

1. Nell'interfaccia utente Web locale del dispositivo passare alla pagina **Attività iniziali**.
2. Nel riquadro **Attivazione** selezionare **Attiva**. 

    ![Pagina "Cloud details" (Dettagli cloud) dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. Nel riquadro **Attiva** immettere la **Chiave di attivazione** ottenuta in [Ottenere la chiave di attivazione per Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

4. Selezionare **Applica**.

    ![Pagina "Cloud details" (Dettagli cloud) dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Il dispositivo viene prima di tutto attivato. Viene quindi richiesto di scaricare il file di chiave.
    
    ![Pagina "Cloud details" (Dettagli cloud) dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    Selezionare **Download key file** (Scarica file di chiave) e salvare il file *keys.json* in un percorso sicuro all'esterno del dispositivo. **Questo file di chiave contiene le chiavi di ripristino per il disco del sistema operativo e i dischi dati nel dispositivo**. Ecco il contenuto del file *keys.json*:

        
    ```json
    {
      "Id": "1ab3fe39-26e6-4984-bb22-2e02d3fb147e",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "C086yg1DrPo0DuZB/a7hUh+kBWj804coJfBA9LDzZqw=",
        "hcsdata": "8ohX9bG3YSZl9DZmZLkYl//L9dXi1XiQrqza+iSd64Q="
      },
      "SystemVolumeBitLockerRecoveryKey": "105347-156739-594473-151107-005082-252604-471955-439395",
      "ServiceEncryptionKey": "oEwxNJeULzGRFt6DsLgcLw=="
    }
    ```
        
 
    La tabella seguente illustra le varie chiavi disponibili nel file:
    
    |Campo  |Descrizione  |
    |---------|---------|
    |`Id`    | Questo è l'ID del dispositivo.        |
    |`DataVolumeBitLockerExternalKeys`|Queste sono le chiavi di BitLocker per i dischi dati e vengono usate per ripristinare i dati locali nel dispositivo.|
    |`SystemVolumeBitLockerRecoveryKey`| Questa è la chiave di BitLocker per il volume di sistema. Questa chiave consente di ripristinare la configurazione del sistema e i dati di sistema per il dispositivo. |
    |`ServiceEncryptionKey`| Questa chiave protegge i dati nel percorso verso il servizio di Azure. Questa chiave garantisce che, nel caso in cui venga compromesso il servizio di Azure, non vengano compromesse anche le informazioni archiviate. |

6. Passare alla pagina **Panoramica**. Lo stato del dispositivo dovrebbe essere **Attivato**.

    ![Pagina "Cloud details" (Dettagli cloud) dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
L'attivazione del dispositivo è stata completata. È ora possibile aggiungere condivisioni al dispositivo.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati trattati gli argomenti seguenti:

> [!div class="checklist"]
> * Prerequisiti
> * Attivare il dispositivo fisico

Per informazioni su come trasferire dati con il dispositivo Azure Stack Edge, vedere:

> [!div class="nextstepaction"]
> [Trasferire i dati con Azure Stack Edge](./azure-stack-edge-j-series-deploy-add-shares.md)
