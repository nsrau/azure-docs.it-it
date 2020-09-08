---
title: Esercitazione su come configurare i certificati per il dispositivo Azure Stack Edge con GPU nel portale di Azure | Microsoft Docs
description: Esercitazione su come distribuire Azure Stack Edge con GPU e configurare i certificati nel dispositivo fisico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 80a857f80fd2c164637e591fbab43123659cd2f7
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268177"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-with-gpu"></a>Esercitazione: Configurare i certificati per il dispositivo Azure Stack Edge con GPU

Questa esercitazione descrive come configurare i certificati per il dispositivo Azure Stack Edge con una GPU su scheda usando l'interfaccia utente Web locale.

Il tempo impiegato per questo passaggio può variare in base all'opzione specifica scelta e al modo in cui il flusso del certificato viene stabilito nell'ambiente in uso.

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti
> * Configurare i certificati per il dispositivo fisico

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare il dispositivo Azure Stack Edge con GPU, assicurarsi di:

* Aver installato il dispositivo fisico come descritto in [Installare Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
* Se si prevede di usare certificati propri:
    - È necessario che i certificati siano pronti nel formato appropriato, incluso il certificato della catena di firma.
<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-j-series-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>Configurare i certificati per il dispositivo


1. Nel riquadro **Sicurezza** selezionare **Configura** per i certificati. 

    ![Pagina "Certificati" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)

2. A seconda se è stato modificato il nome del dispositivo o il dominio DNS nel riquadro **Configurazione dispositivo**, è possibile scegliere una delle opzioni seguenti per i certificati.

    - Se il nome del dispositivo o il dominio DNS non è stato modificato nel passaggio precedente e non si vogliono usare certificati propri, è possibile ignorare questo passaggio e procedere con il passaggio successivo. Il dispositivo ha generato automaticamente certificati autofirmati per iniziare. 

        ![Pagina "Certificati" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Se è stato modificato il nome del dispositivo o il dominio DNS, per attivare correttamente il dispositivo è possibile scegliere una delle opzioni seguenti: 
    
        - **Generare tutti i certificati del dispositivo**. I certificati del dispositivo devono essere usati solo per i test e non usati con i carichi di lavoro di produzione. ** Per altre informazioni, vedere [Generare i certificati del dispositivo nel dispositivo Azure Stack Edge](#generate-device-certificates).

        - **Caricare certificati personali**. È possibile caricare certificati di endpoint firmati personali e le catene di firma corrispondenti. È necessario prima di tutto aggiungere la catena di firma e quindi caricare i certificati degli endpoint. **È consigliabile caricare sempre i certificati personali per i carichi di lavoro di produzione.** Per altre informazioni, vedere [Caricare i certificati personali nel dispositivo Azure Stack Edge](#bring-your-own-certificates).
    
        - È possibile usare alcuni certificati personali e generare alcuni certificati del dispositivo. L'opzione **Genera certificati** consentirà solo di rigenerare i certificati del dispositivo.

    - Se è stato modificato il nome del dispositivo o il dominio DNS e non si generano certificati o si caricano i certificati personali, l'attivazione verrà bloccata.


### <a name="generate-device-certificates"></a>Generare i certificati del dispositivo

Per generare i certificati del dispositivo, seguire questa procedura.

Attenersi alla procedura seguente per rigenerare e scaricare i certificati del dispositivo Azure Stack Edge:

1. Nell'interfaccia utente locale del dispositivo passare a **Configurazione > Certificati**. Selezionare **Genera certificati**.

    ![Generare e scaricare il certificato 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. In **Genera certificati** selezionare **Genera**.

    ![Generare e scaricare il certificato 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    I certificati del dispositivo vengono ora generati e applicati. 
    
    > [!IMPORTANT]
    > Mentre è in corso l'operazione di generazione dei certificati, non caricare i certificati personali o provare ad aggiungerli tramite l'opzione **+ Aggiungi certificato**.

    Verrà visualizzata una notifica al termine dell'operazione. Per evitare potenziali problemi di cache, riavviare il browser. 
    
    ![Generare e scaricare il certificato 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. Nella pagina **Certificati** si potrà notare che la colonna **Download** è ora compilata e sono disponibili i collegamenti per scaricare i certificati rigenerati. 

    ![Generare e scaricare il certificato 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

4. Selezionare il collegamento per il download di un certificato e, quando richiesto, salvare il certificato. 

    ![Generare e scaricare il certificato 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Ripetere questo processo per tutti i certificati che si desidera scaricare. 
    
    ![Generare e scaricare il certificato 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    I certificati generati dal dispositivo vengono salvati come certificati DER con il formato del nome seguente: 

    `<Device name>_<Endpoint name>.cer`. Questi certificati contengono la chiave pubblica per i certificati corrispondenti installati nel dispositivo. 

Sarà necessario installare questi certificati nel sistema client usato per accedere agli endpoint nel dispositivo dell'ambiente del servizio app. Questi certificati stabiliscono una relazione di trust tra il client e il dispositivo.

Per importare e installare questi certificati nel client usato per accedere al dispositivo, seguire la procedura descritta in [Importare i certificati nei client che accedono al dispositivo Azure Stack Edge](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device). 

Se si usa Azure Storage Explorer, sarà necessario installare i certificati nel client in formato PEM e sarà necessario convertire i certificati generati dal dispositivo in formato PEM. 

> [!IMPORTANT]
> - Il collegamento per il download è disponibile solo per i certificati generati dal dispositivo e non per i certificati personali.
> - È possibile decidere di usare una combinazione di certificati generati dal dispositivo e certificati personali, purché vengano soddisfatti altri requisiti per i certificati. Per altre informazioni, vedere [Requisiti per i certificati](azure-stack-edge-j-series-certificate-requirements.md).
    

### <a name="bring-your-own-certificates"></a>Caricare i certificati personali

Seguire questa procedura per aggiungere i certificati personali, inclusa la catena di firma.

1. Per caricare il certificato, nella pagina **Certificati** selezionare **+ Aggiungi certificato**.

    ![Pagina "Certificati" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Caricare prima la catena di firma e selezionare **Validate & add** (Convalida e aggiungi).

    ![Pagina "Certificati" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. A questo punto è possibile caricare altri certificati. Ad esempio, è possibile caricare i certificati di Azure Resource Manager e dell'endpoint di archiviazione BLOB.

    ![Pagina "Certificati" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    È anche possibile caricare il certificato dell'interfaccia utente Web locale. Dopo aver caricato questo certificato, verrà richiesto di riavviare il browser e cancellare la cache. Sarà quindi necessario connettersi all'interfaccia utente Web locale del dispositivo.  

    ![Pagina "Certificati" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    È anche possibile caricare il certificato del nodo.

    ![Pagina "Certificati" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    La pagina Certificati viene aggiornata per riflettere i nuovi certificati aggiunti.

    ![Pagina "Certificati" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)  

    > [!NOTE]
    > Ad eccezione del cloud pubblico di Azure, i certificati della catena di firma devono essere caricati prima dell'attivazione per tutte le configurazioni del cloud (Azure per enti pubblici o Azure Stack).


Il dispositivo è ora pronto per essere attivato.


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti
> * Configurare i certificati per il dispositivo fisico

Per informazioni su come attivare il dispositivo Azure Stack Edge, vedere:

> [!div class="nextstepaction"]
> [Attivare il dispositivo Azure Stack Edge](./azure-stack-edge-gpu-deploy-activate.md)
