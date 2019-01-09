---
title: Trasformare i dati con Azure Data Box Edge | Microsoft Docs
description: Informazioni su come configurare il ruolo di calcolo in Edge e usarlo per trasformare i dati prima dell'invio ad Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c0901f22e4941fdfaa21138153a06e97c2d6095f
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630377"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>Esercitazione: Trasformare i dati con Azure Data Box Edge (anteprima)

Questa esercitazione descrive come configurare un ruolo di calcolo nel dispositivo Azure Data Box Edge. Dopo aver configurato il ruolo di calcolo, Data Box Edge può trasformare i dati prima che vengano inviati ad Azure.

Per completare questa procedura, sono necessari circa 30-45 minuti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una risorsa dell'hub IoT di Azure
> * Configurare il ruolo di calcolo
> * Aggiungere un ruolo di calcolo
> * Verificare la trasformazione e il trasferimento dei dati

> [!IMPORTANT]
> Data Box Edge è disponibile in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione.
 
## <a name="prerequisites"></a>Prerequisiti

Prima di configurare un ruolo di calcolo nel dispositivo Data Box Edge, assicurarsi di:

* Aver attivato il dispositivo Data Box Edge come descritto in [Connettere, configurare e attivare Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="create-an-iot-hub-resource"></a>Creare una risorsa dell'hub IoT

Prima di configurare il ruolo di calcolo in Data Box Edge, è necessario creare una risorsa dell'hub IoT.

Per istruzioni dettagliate, vedere [Creare un hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub). Usare la stessa sottoscrizione e lo stesso gruppo di risorse usati per la risorsa Data Box Edge.

![Creare una risorsa dell'hub IoT](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Se non è ancora stato configurato un ruolo di calcolo Edge, tenere presenti le considerazioni seguenti:

- La risorsa dell'hub IoT non ha alcun dispositivo Azure IoT o Azure IoT Edge.
- Non è possibile creare condivisioni locali di Edge. Quando si aggiunge una condivisione, non è abilitata l'opzione per creare una condivisione locale per il calcolo Edge.


## <a name="set-up-compute-role"></a>Configurare il ruolo di calcolo

Quando il ruolo di calcolo Edge è configurato nel dispositivo Edge, crea due dispositivi: un dispositivo IoT e un dispositivo IoT Edge. Entrambi i dispositivi possono essere visualizzati nella risorsa dell'hub IoT.

Per configurare il ruolo di calcolo nel dispositivo, eseguire queste operazioni:

1. Passare alla risorsa Data Box Edge, selezionare **Panoramica** e quindi **Configura ruolo calcolo**. 

    ![Collegamento Panoramica nel riquadro sinistro](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    È anche possibile passare a **Moduli** e selezionare **Configura calcolo**.

    ![Collegamenti "Moduli" e "Configura calcolo"](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
1. Nell'elenco a discesa selezionare la **risorsa dell'hub IoT** creata nel passaggio precedente.  
    Attualmente per il dispositivo IoT Edge è disponibile solo la piattaforma Linux. 
    
1. Fare clic su **Create**(Crea).

    ![Il pulsante Crea](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
    La creazione del ruolo di calcolo richiede alcuni minuti. A causa di un bug in questa versione, anche quando viene creato il ruolo di calcolo, la schermata non viene aggiornata. Per verificare che il ruolo di calcolo Edge sia stato configurato, passare a **Moduli**.  

    ![Elenco di dispositivi "Configura calcolo Edge"](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

1. Passare di nuovo a **Panoramica**.  
    La schermata viene aggiornata, a indicare che il ruolo di calcolo è configurato.

    ![Configurare un ruolo di calcolo](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
1. Nell'hub IoT usato durante la creazione del ruolo di calcolo Edge passare a **Dispositivi IoT**.  
    È ora abilitato un dispositivo IoT. 

    ![Pagina "Dispositivi IoT"](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

1. Nel riquadro a sinistra selezionare **IoT Edge**.  
    È abilitato anche un dispositivo IoT Edge.

    ![Configurare un ruolo di calcolo](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
1. Selezionare e fare clic sul dispositivo IoT Edge.  
    Su questo dispositivo IoT Edge è in esecuzione un agente di Edge. 

    ![Pagina Dettagli dispositivo](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

    Anche se non sono presenti moduli personalizzati in questo dispositivo Edge, è ora possibile aggiungerne uno. Per informazioni su come creare un modulo personalizzato, vedere [Sviluppare un modulo C# per il dispositivo Data Box Edge](data-box-edge-create-iot-edge-module.md).


## <a name="add-a-custom-module"></a>Aggiungere un modulo personalizzato

In questa sezione si aggiungerà un modulo personalizzato al dispositivo IoT Edge creato in [Sviluppare un modulo C# per Data Box Edge](data-box-edge-create-iot-edge-module.md). 

La procedura seguente usa un esempio in cui il modulo personalizzato usato sposta i file da una condivisione locale nel dispositivo Edge a una condivisione cloud sul dispositivo. La condivisione cloud esegue quindi il push dei file all'account di archiviazione di Azure associato. 

1. Per aggiungere una condivisione locale nel dispositivo Edge, eseguire queste operazioni:

    a. Nella risorsa Data Box Edge, passare a **Condivisioni**. 
    
    b. Selezionare **Aggiungi condivisione** e quindi specificare il nome condivisione e selezionarne il tipo. 
    
    c. Per creare una condivisione locale, selezionare la casella di controllo **Configura come condivisione locale Edge**. 
    
    d. Selezionare **Crea nuovo** oppure **Usa esistente** e quindi **Crea**.

    ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    Se è stata creata una condivisione NFS locale, usare l'opzione di comando di sincronizzazione remota (rsync) seguente per copiare i file nella condivisione:

    `rsync --inplace <source file path> < destination file path>`

    Per altre informazioni sul comando rsync, vedere la [documentazione di rsync](https://www.computerhope.com/unix/rsync.htm). 

    Viene creata la condivisione locale e si riceverà una notifica di creazione riuscita. L'elenco delle condivisioni può essere aggiornato, ma è necessario attendere il completamento della creazione della condivisione.
    
1. Passare all'elenco delle condivisioni. 

    ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
1. Per visualizzare le proprietà della nuova condivisione locale creata, selezionarla. 

1. Nella casella **Punto di montaggio locale per i moduli di calcolo Edge** copiare il valore corrispondente a questa condivisione.  
    Questo punto di montaggio locale verrà usato durante la distribuzione del modulo.

    ![Casella "Punto di montaggio locale per i moduli di calcolo Edge"](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
1. In una condivisione cloud esistente creata nel dispositivo Data Box Edge copiare il punto di montaggio locale per i moduli di calcolo Edge per questa condivisione nel cloud in **Punto di montaggio locale per i moduli di calcolo Edge**.  
    Questo punto di montaggio locale verrà usato durante la distribuzione del modulo.

    ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

1. Per aggiungere un modulo personalizzato al dispositivo IoT Edge, passare alla risorsa dell'hub IoT e quindi a **Dispositivo IoT Edge**. 

1. Selezionare il dispositivo e quindi selezionare **Imposta moduli** in **Dettagli dispositivo**. 

    ![Collegamento Imposta moduli](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

1. In **Aggiungi moduli** eseguire queste operazioni:

    a. Immettere il nome, l'indirizzo, il nome utente e la password per le impostazioni del registro contenitori per il modulo personalizzato.  
    Il nome, l'indirizzo e le credenziali elencate vengono usati per recuperare i moduli con un URL corrispondente. Per distribuire questo modulo, in **Moduli di distribuzione**, selezionare **Modulo IoT Edge**. Questo modulo IoT Edge è un contenitore Docker che è possibile distribuire nel dispositivo IoT Edge associato al proprio dispositivo Data Box Edge.

    ![Pagina Imposta moduli](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    b. Specificare le impostazioni per il modulo personalizzato IoT Edge immettendo il nome del modulo e l'URI immagine per l'immagine del contenitore corrispondente. 
    
    ![Pagina Moduli personalizzati IoT Edge](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    c. Nella casella **Opzioni di creazione del contenitore** immettere i punti di montaggio locali per i moduli Edge copiati nei passaggi precedenti per il cloud e la condivisione locale.
    > [!IMPORTANT]
    > Non creare nuovi percorsi, ma usare quelli copiati. Viene eseguito il mapping tra i punti di montaggio locali e i percorsi **InputFolderPath** e **OutputFolderPath** corrispondenti, specificati nel modulo quando è stato [aggiornato il modulo con codice personalizzato](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code). 
    
    Nella casella **Opzioni di creazione del contenitore** è possibile incollare l'esempio seguente: 
    
    ```
    {
        "HostConfig": {
        "Binds": [
        "/home/hcsshares/mysmblocalshare:/home/LocalShare",
        "/home/hcsshares/mysmbshare1:/home/CloudShare"
        ]
        }
    }
    ```

    Specificare qui anche le variabili di ambiente per il modulo.

    ![Casella Opzioni di creazione del contenitore](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    d. Se necessario, configurare le impostazioni avanzate del runtime di Edge e quindi fare clic su **Avanti**.

    ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
1.  In **Specifica route** impostare le route tra i moduli.  
    In questo esempio immettere il nome della condivisione locale che eseguirà il push dei dati nella condivisione cloud.

    È possibile sostituire *route* con questa stringa di route:       `"route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"`

    ![Sezione Specifica route](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 

1. Selezionare **Avanti**. 

1.  In **Rivedi distribuzione** controllare tutte le impostazioni e quindi selezionare **Invia** per inviare il modulo per la distribuzione.

    ![Pagina Imposta moduli](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
    Questa azione avvia la distribuzione del modulo come mostrato nell'immagine seguente:

    ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>Verificare la trasformazione e il trasferimento dei dati

Il passaggio finale consiste nell'assicurarsi che il modulo sia connesso e in esecuzione come previsto. Lo stato di runtime del modulo deve essere In esecuzione per il dispositivo IoT Edge nella risorsa dell'hub IoT.

![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
Per verificare che il modulo sia in esecuzione, eseguire queste operazioni:

1. Selezionare il modulo e quindi visualizzare Identità del modulo gemello.  
    Lo stato del client per il modulo e del dispositivo Edge deve essere *Connesso*.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
    Dopo che il modulo è in esecuzione, viene visualizzato anche nell'elenco dei moduli Edge nella risorsa Data Box Edge. Lo stato di runtime del modulo aggiunto è *In esecuzione*.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
1.  In Esplora file connettersi alle condivisioni locale e cloud create in precedenza.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
1.  Aggiungere i dati alla condivisione locale.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
    I dati vengono spostati nella condivisione cloud.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

    Viene quindi eseguito il push dei dati dalla condivisione cloud all'account di archiviazione. Per visualizzare i dati, passare a Storage Explorer.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
Il processo di convalida è stato completato.


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare una risorsa dell'hub IoT
> * Configurare il ruolo di calcolo
> * Aggiungere un ruolo di calcolo
> * Verificare la trasformazione e il trasferimento dei dati

Per informazioni su come amministrare il dispositivo Data Box Edge, vedere:

> [!div class="nextstepaction"]
> [Usare l'interfaccia utente Web locale per amministrare un Data Box Edge](https://aka.ms/dbg-docs)


