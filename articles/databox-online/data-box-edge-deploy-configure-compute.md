---
title: Trasformare i dati con Azure Data Box Edge | Microsoft Docs
description: Informazioni su come configurare il ruolo di calcolo in Edge e usarlo per trasformare i dati prima dell'invio ad Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 4729e08399132243543c6f4e1cadd537d185e9e3
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166254"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>Esercitazione: Trasformare i dati con Azure Data Box Edge (anteprima)

Questa esercitazione descrive come configurare il ruolo di calcolo in Edge. Una volta configurato il ruolo di calcolo, Edge può trasformare i dati prima dell'invio ad Azure.

Per completare questa procedura sono necessari circa 30 - 45 minuti. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una risorsa dell'hub IoT
> * Configurare il ruolo di calcolo
> * Aggiungere un ruolo di calcolo
> * Verificare la trasformazione e il trasferimento dei dati

> [!IMPORTANT]
> Data Box Edge è disponibile in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione. 
 
## <a name="prerequisites"></a>Prerequisiti

Prima di configurare il calcolo in Data Box Edge, assicurarsi di:

* Il dispositivo Data Box Edge sia stato attivato come descritto in [Connettere, configurare e attivare Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="create-an-iot-hub-resource"></a>Creare una risorsa dell'hub IoT

Prima di configurare il ruolo di calcolo in Data Box Edge, è necessario creare una risorsa dell'hub IoT.

Per istruzioni dettagliate, vedere [Creare un hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub). Usare la stessa sottoscrizione e lo stesso gruppo di risorse usati per la risorsa di Data Box Edge.

![Creare una risorsa dell'hub IoT](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Quando il ruolo di calcolo di Edge non è configurato, notare che: 
- La risorsa dell'hub IoT non ha alcun dispositivo IoT o IoT Edge.
- Non è possibile creare condivisioni locali di Edge. Quando si aggiunge una condivisione, non è abilitata l'opzione per creare una condivisione locale per il calcolo Edge.


## <a name="set-up-compute-role"></a>Configurare il ruolo di calcolo

Quando il ruolo di calcolo Edge è configurato sul dispositivo Edge, crea due dispositivi: IoT e IoT Edge. Entrambi questi dispositivi possono essere visualizzati nella risorsa dell'hub IoT.

Per configurare il ruolo di calcolo nel dispositivo, procedere come segue.

1. Passare alla risorsa di Data Box Edge e quindi in **Panoramica** fare clic su **Configurare il ruolo di calcolo**. 

    ![Configurare il ruolo di calcolo](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    È anche possibile passare a **Moduli** e fare clic su **Configura calcolo**.

    ![Configurare il ruolo di calcolo](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
2. Dall'elenco a discesa, selezionare la **risorsa dell'hub IoT** creata nel passaggio precedente. In questa fase per il dispositivo IoT Edge è disponibile solo la piattaforma Linux. Fare clic su **Create**(Crea).

    ![Configurare il ruolo di calcolo](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
3. La creazione del ruolo di calcolo richiede alcuni minuti. A causa di un bug in questa versione, anche quando viene creato il ruolo di calcolo, la schermata non viene aggiornata. In **Moduli** si noterà che il calcolo Edge è configurato.  

    ![Configurare il ruolo di calcolo](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

4. Passare di nuovo a **Panoramica**: a questo punto la schermata viene aggiornata, indicando che il ruolo di calcolo è configurato.

    ![Configurare il ruolo di calcolo](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
5. Passare all'hub IoT usato durante la creazione del ruolo di calcolo Edge. Passare a **Dispositivi IoT**. Come si può notare, è stato abilitato un dispositivo IoT. 

    ![Configurare il ruolo di calcolo](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

6. Passare a **IoT Edge**: si noterà che è stato attivato anche un dispositivo IoT Edge.

    ![Configurare il ruolo di calcolo](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
7. Selezionare e fare clic sul dispositivo IoT Edge. Su questo dispositivo IoT Edge è in esecuzione un agente di Edge. 

    ![Configurare il ruolo di calcolo](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

Non sono comunque presenti moduli personalizzati in questo dispositivo Edge. È ora possibile aggiungere un modulo personalizzato a questo dispositivo.


## <a name="add-a-custom-module"></a>Aggiungere un modulo personalizzato

In questa sezione si aggiungerà un modulo personalizzato al dispositivo IoT Edge. 

Questa procedura fornisce un esempio in cui il modulo personalizzato usato sposta i file da una condivisione locale nel dispositivo Edge in una condivisione cloud sul dispositivo. La condivisione cloud quindi esegue il push dei file all'account di archiviazione Azure a essa associato. 

1. Il primo passaggio consiste nell'aggiungere una condivisione locale nel dispositivo Edge. Nella risorsa Data Box Edge, passare a **Condivisioni**. Fare clic su **+ Aggiungi condivisione**. Specificare il nome e selezionare il tipo di condivisione. Per creare una condivisione locale, selezionare l'opzione **Configura come condivisione locale Edge**. Selezionare un **utente esistente** o **crearne uno nuovo**. Fare clic su **Create**(Crea).

    ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    Se è stata creata una condivisione NFS locale, usare la seguente opzione di comando rsync per copiare i file nella condivisione:

    `rsync --inplace <source file path> < destination file path>`

     Per altre informazioni sul comando rsync, vedere la [documentazione di Rsync](https://www.computerhope.com/unix/rsync.htm). 

 
2. Dopo aver creato la condivisione locale e aver ricevuto una notifica di creazione riuscita (l'elenco condivisioni può essere aggiornato prima, ma è necessario attendere il completamento della creazione della condivisione), passare all'elenco di condivisioni. 

    ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
3. Selezionare e fare clic sulla condivisione locale appena creata e visualizzare le proprietà della condivisione. Copiare e salvare il **punto di montaggio locale per i moduli di Edge** corrispondente a questa condivisione.

    ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
    Passare a una condivisione cloud esistente creata nel dispositivo Data Box Edge. Anche in questo caso, copiare e salvare il punto di montaggio locale per i moduli di calcolo Edge per questa condivisione cloud. Questi punti di montaggio locali saranno usati durante la distribuzione del modulo.

    ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

4. Per aggiungere un modulo personalizzato al dispositivo IoT Edge, passare alla risorsa dell'hub IoT e quindi andare al **Dispositivo IoT Edge**. Selezionare e fare clic sul dispositivo. In **Dettagli dispositivo**, dalla barra dei comandi in alto, fare clic su **Imposta moduli**. 

    ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

5. In **Aggiungi moduli** seguire questa procedura:

    1. Indicare **nome**, **indirizzo**, **nome utente** e **password** per le **Impostazioni di Registro contenitori** del modulo personalizzato. Il nome, l'indirizzo e le credenziali elencate vengono usati per recuperare i moduli con un URL corrispondente. Per distribuire questo modulo, in **Moduli di distribuzione**, selezionare **Modulo IoT Edge**. Questo modulo IoT Edge è un contenitore Docker che è possibile distribuire sul dispositivo IoT Edge associato al proprio dispositivo Data Box Edge.

        ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    2. Specificare le impostazioni per il modulo personalizzato IoT Edge. Specificare il **nome** del modulo e l'**URI immagine**. 
    
        ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    3. Nelle **Opzioni di creazione del contenitore** indicare i punti di montaggio locali per i moduli Edge copiati nei passaggi precedenti per il cloud e la condivisione locale (è importare usare questi percorsi invece di crearne di nuovi). Queste condivisioni vengono mappate ai punti di montaggio del contenitore corrispondente. Indicare anche eventuali variabili di ambiente per il modulo.

        ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    4. Se necessario, selezionare **Configura impostazioni avanzate per il runtime di IoT Edge** e quindi fare clic su **Avanti**.

        ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
6.  In **Specificare le route** impostare le route tra i moduli. In questo caso, specificare il nome della condivisione locale che esegue il push dei dati nella condivisione cloud. Fare clic su **Avanti**.

    ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 
 
7.  In **Verificare la distribuzione** rivedere tutte le impostazioni e, se conformi alle proprie esigenze, **inviare** il modulo per la distribuzione.

    ![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
Verrà avviata la distribuzione del modulo come illustrato dal **Modulo personalizzato IoT Edge** in **Moduli**.

![Aggiungere un modulo personalizzato](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>Verificare la trasformazione e il trasferimento dei dati

Il passaggio finale consiste nell'assicurarsi che il modulo sia connesso e in esecuzione come previsto. Per verificare che il modulo sia in esecuzione, seguire questa procedura:

1. Lo stato di runtime del modulo deve essere in esecuzione per il dispositivo IoT Edge nella risorsa dell'hub IoT.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
2. Selezionare e fare clic sul modulo e controllare l'**Identità del modulo gemello**. Lo stato client del dispositivo Edge e del modulo deve essere **Connesso**.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
3.  Quando il modulo è in esecuzione, viene visualizzato anche nell'elenco dei moduli di Edge nella risorsa di Data Box Edge. Lo **stato di runtime** del modulo aggiunto è **in esecuzione**.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
4.  Connettersi alle condivisioni locale e cloud create con Esplora file.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
5.  Aggiungere i dati alla condivisione locale.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
6.  I dati vengono spostati nella condivisione cloud.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

7.  Viene quindi eseguito il push dei dati dalla condivisione cloud all'account di archiviazione. Passare a Storage Explorer per visualizzare i dati.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
Il processo di convalida è terminato.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi a Data Box Edge, ad esempio:

> [!div class="checklist"]
> * Creare una risorsa dell'hub IoT
> * Configurare il ruolo di calcolo
> * Aggiungere un ruolo di calcolo
> * Verificare la trasformazione e il trasferimento dei dati

Passare all'esercitazione successiva per informazioni su come amministrare il Data Box Edge.

> [!div class="nextstepaction"]
> [Usare l'interfaccia utente Web locale per amministrare un Data Box Edge](http://aka.ms/dbg-docs)


