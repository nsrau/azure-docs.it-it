---
title: Esercitazione su come configurare i certificati per il dispositivo Azure Stack Edge Pro con GPU nel portale di Azure | Microsoft Docs
description: Esercitazione per la distribuzione di Azure Stack Edge Pro con GPU che illustra come configurare i certificati nel dispositivo fisico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 7854aff0b4194efae7c4df653dee18e2676fdd41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91446322"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-with-gpu"></a>Esercitazione: Configurare i certificati per il dispositivo Azure Stack Edge Pro con GPU

Questa esercitazione illustra come configurare i certificati per il dispositivo Azure Stack Edge Pro con una GPU su scheda usando l'interfaccia utente Web locale.

Il tempo impiegato per questo passaggio può variare in base all'opzione specifica scelta e al modo in cui il flusso del certificato viene stabilito nell'ambiente in uso.

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti
> * Configurare i certificati per il dispositivo fisico

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare il dispositivo Azure Stack Edge Pro con GPU, assicurarsi di:

* Aver installato il dispositivo fisico come descritto in [Installare Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).
* Se si prevede di usare certificati propri:
    - È necessario che i certificati siano pronti nel formato appropriato, incluso il certificato della catena di firma. Per informazioni dettagliate sul certificato, vedere [Gestire i certificati](azure-stack-edge-j-series-manage-certificates.md)

<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-j-series-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>Configurare i certificati per il dispositivo

1. I certificati possono essere configurati nella pagina **Certificati**. A seconda che sia stato modificato il nome del dispositivo o il dominio DNS nella pagina **Dispositivo**, è possibile scegliere una delle opzioni seguenti per i certificati.

    - Se il nome del dispositivo o il dominio DNS non è stato modificato nel passaggio precedente e non si vogliono usare certificati propri, è possibile ignorare questo passaggio e procedere con il passaggio successivo. Il dispositivo ha generato automaticamente certificati autofirmati per iniziare. 

        ![Pagina "Certificati" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Se è stato modificato il nome del dispositivo o il dominio DNS, si noterà che lo stato dei certificati viene visualizzato come **Non valido**. 

        ![Pagina "Certificati" dell'interfaccia utente Web locale - 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)    

        Selezionare un certificato per visualizzare i dettagli dello stato.

        ![Pagina "Certificati" dell'interfaccia utente Web locale - 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)  

        Il motivo è che i certificati non riflettono il nome del dispositivo e il dominio DNS aggiornati (usati nel nome del soggetto e nel nome alternativo del soggetto). Per attivare correttamente il dispositivo, scegliere una delle opzioni seguenti: 
    
        - **Generare tutti i certificati del dispositivo**. Questi certificati del dispositivo devono essere usati solo per i test e non usati con i carichi di lavoro di produzione. Per altre informazioni, vedere [Generare i certificati del dispositivo in Azure Stack Edge Pro](#generate-device-certificates).

        - **Caricare certificati personali**. È possibile caricare certificati di endpoint firmati personali e le catene di firma corrispondenti. È necessario prima di tutto aggiungere la catena di firma e quindi caricare i certificati degli endpoint. **È consigliabile caricare sempre i certificati personali per i carichi di lavoro di produzione.** Per altre informazioni, vedere [Caricare i certificati personali nel dispositivo Azure Stack Edge Pro](#bring-your-own-certificates).
    
        - È possibile usare alcuni certificati personali e generare alcuni certificati del dispositivo. L'opzione **Genera certificati** consentirà solo di rigenerare i certificati del dispositivo.

    - Se è stato modificato il nome del dispositivo o il dominio DNS e non si generano certificati o non si caricano i certificati personali, l'**attivazione verrà bloccata**.


### <a name="generate-device-certificates"></a>Generare i certificati del dispositivo

Per generare i certificati del dispositivo, seguire questa procedura.

Attenersi alla procedura seguente per generare di nuovo e scaricare i certificati del dispositivo Azure Stack Edge Pro:

1. Nell'interfaccia utente locale del dispositivo passare a **Configurazione > Certificati**. Selezionare **Genera certificati**.

    ![Generare e scaricare il certificato 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. In **Genera certificati** selezionare **Genera**. 

    ![Generare e scaricare il certificato 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    I certificati del dispositivo vengono ora generati e applicati. Sono necessari alcuni minuti per generare e applicare i certificati.
    
    > [!IMPORTANT]
    > Mentre è in corso l'operazione di generazione dei certificati, non caricare i certificati personali o provare ad aggiungerli tramite l'opzione **+ Aggiungi certificato**.

    Verrà visualizzata una notifica al termine dell'operazione. **Per evitare potenziali problemi di cache, riavviare il browser.**
    
    ![Generare e scaricare il certificato 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. Dopo la generazione dei certificati: 

    - Lo stato di tutti i certificati viene visualizzato come **Valido**. 

        ![Generare e scaricare il certificato 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

    - È possibile selezionare un nome di certificato specifico e visualizzare i dettagli del certificato. 

        ![Generare e scaricare il certificato 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6a.png)

    - La colonna relativa al **download** è ora popolata. Questa colonna contiene collegamenti per scaricare i certificati generati nuovamente. 

        ![Generare e scaricare il certificato 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6b.png)


4. Selezionare il collegamento per il download di un certificato e, quando richiesto, salvare il certificato. 

    ![Generare e scaricare il certificato 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Ripetere questo processo per tutti i certificati che si desidera scaricare. 
    
    ![Generare e scaricare il certificato 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    I certificati generati dal dispositivo vengono salvati come certificati DER con il formato del nome seguente: 

    `<Device name>_<Endpoint name>.cer`. Questi certificati contengono la chiave pubblica per i certificati corrispondenti installati nel dispositivo. 

Sarà necessario installare questi certificati nel sistema client usato per accedere agli endpoint nel dispositivo dell'ambiente del servizio app. Questi certificati stabiliscono una relazione di trust tra il client e il dispositivo.

Per importare e installare questi certificati nel client usato per accedere al dispositivo, seguire la procedura descritta in [Importare i certificati nei client che accedono al dispositivo Azure Stack Edge Pro](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device). 

Se si usa Azure Storage Explorer, sarà necessario installare i certificati nel client in formato PEM e sarà necessario convertire i certificati generati dal dispositivo in formato PEM. 

> [!IMPORTANT]
> - Il collegamento per il download è disponibile solo per i certificati generati dal dispositivo e non per i certificati personali.
> - È possibile decidere di usare una combinazione di certificati generati dal dispositivo e certificati personali, purché vengano soddisfatti altri requisiti per i certificati. Per altre informazioni, vedere [Requisiti per i certificati](azure-stack-edge-j-series-certificate-requirements.md).
    

### <a name="bring-your-own-certificates"></a>Caricare i certificati personali

Seguire questa procedura per aggiungere i certificati personali, inclusa la catena di firma.

1. Per caricare il certificato, nella pagina **Certificati** selezionare **+ Aggiungi certificato**.

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Caricare prima la catena di firma e selezionare **Validate & add** (Convalida e aggiungi).

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. A questo punto è possibile caricare altri certificati. Ad esempio, è possibile caricare i certificati di Azure Resource Manager e dell'endpoint di archiviazione BLOB.

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    È anche possibile caricare il certificato dell'interfaccia utente Web locale. Dopo aver caricato questo certificato, verrà richiesto di riavviare il browser e cancellare la cache. Sarà quindi necessario connettersi all'interfaccia utente Web locale del dispositivo.  

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    È anche possibile caricare il certificato del nodo.

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    In qualsiasi momento, è possibile selezionare un certificato e visualizzare i dettagli per verificare che corrispondano al certificato caricato.

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)

    La pagina Certificati viene aggiornata per riflettere i nuovi certificati aggiunti.

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 10](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-7.png)  

    > [!NOTE]
    > Ad eccezione del cloud pubblico di Azure, i certificati della catena di firma devono essere caricati prima dell'attivazione per tutte le configurazioni del cloud (Azure per enti pubblici o Azure Stack).


Il dispositivo è ora pronto per essere attivato. Selezionare **< Torna alle Attività iniziali**.


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti
> * Configurare i certificati per il dispositivo fisico

Per informazioni su come attivare il dispositivo Azure Stack Edge Pro, vedere:

> [!div class="nextstepaction"]
> [Attivare il dispositivo Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-activate.md)
