---
title: Esercitazione su come filtrare e analizzare i dati con il calcolo in Azure Data Box Edge | Microsoft Docs
description: Informazioni su come configurare il ruolo di calcolo in Edge e usarlo per trasformare i dati prima dell'invio ad Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 7b3d725eb05c811d3fdd44516c1bde9a8dfbaaac
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924350"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Esercitazione: Trasformare i dati con Azure Data Box Edge

Questa esercitazione descrive come configurare un ruolo di calcolo nel dispositivo Azure Data Box Edge. Dopo aver configurato il ruolo di calcolo, Data Box Edge può trasformare i dati prima che vengano inviati ad Azure.

Il completamento di questa procedura può richiedere circa 10-15 minuti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare il calcolo
> * Aggiungere condivisioni
> * Aggiungere un ruolo di calcolo
> * Verificare la trasformazione e il trasferimento dei dati

 
## <a name="prerequisites"></a>Prerequisiti

Prima di configurare un ruolo di calcolo nel dispositivo Data Box Edge, assicurarsi di:

- Aver attivato il dispositivo Data Box Edge come descritto in [Connettere, configurare e attivare Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Configurare il calcolo

Per configurare il calcolo in Data Box Edge, si creerà una risorsa hub IoT.

1. Nel portale di Azure della risorsa Data Box Edge passare a Panoramica. Nel riquadro **Calcolo** a destra selezionare **Inizia subito**.

    ![Introduzione al calcolo](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Nel riquadro **Configura calcolo Edge** selezionare **Configura calcolo**.
3. Nel pannello **Configura calcolo Edge** immettere quanto segue:

   
    |Campo  |Valore  |
    |---------|---------|
    |Hub IoT     | Scegliere **Nuovo** o **Esistente**. <br> Per impostazione predefinita, per creare una risorsa IoT viene usato un livello Standard (S1). Per usare una risorsa IoT di un livello gratuito, crearne uno e quindi selezionare la risorsa esistente. <br> In ogni caso, la risorsa hub IoT usa la stessa sottoscrizione e lo stesso gruppo di risorse usati dalla risorsa Data Box Edge.     |
    |NOME     |Immettere un nome per la risorsa hub IoT.         |

    ![Introduzione al calcolo](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Selezionare **Create**. La creazione della risorsa hub IoT richiede qualche minuto. Al termine della creazione della risorsa hub IoT, il riquadro **Configura calcolo** viene aggiornato per mostrare la configurazione di calcolo. Per verificare che il ruolo calcolo Edge sia stato configurato, selezionare **Visualizza calcolo** nel riquadro **Configura calcolo**.
    
    ![Introduzione al calcolo](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    Quando il ruolo di calcolo Edge è configurato nel dispositivo Edge, crea due dispositivi: un dispositivo IoT e un dispositivo IoT Edge. Entrambi i dispositivi possono essere visualizzati nella risorsa dell'hub IoT. Nel dispositivo IoT Edge viene eseguito anche un runtime IoT Edge. Attualmente per il dispositivo IoT Edge è disponibile solo la piattaforma Linux.


## <a name="add-shares"></a>Aggiungere condivisioni

Per la distribuzione semplice di questa esercitazione, saranno necessarie due condivisioni: una condivisione Edge e un'altra condivisione locale Edge.

1. Aggiungere una condivisione Edge nel dispositivo seguendo questa procedura:

    1. Nella risorsa Data Box Edge passare a **Calcolo Edge > Inizia subito**.
    2. Nel riquadro **Aggiungi condivisioni** selezionare **Aggiungi**.
    3. Nel pannello **Aggiungi condivisione** specificare il nome della condivisione e selezionarne il tipo.
    4. Per montare la condivisione Edge, selezionare la casella di controllo **Usa la condivisione con il calcolo Edge**.
    5. Selezionare **Account di archiviazione**, **Servizio di archiviazione**, un utente esistente e quindi **Crea**.

        ![Aggiungere una condivisione Edge](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Se è stata creata una condivisione NFS locale, usare l'opzione di comando di sincronizzazione remota (rsync) seguente per copiare i file nella condivisione:

    `rsync <source file path> < destination file path>`

    Per altre informazioni sul comando rsync, vedere la [documentazione di rsync](https://www.computerhope.com/unix/rsync.htm).

    La condivisione Edge verrà creata e si riceverà una notifica di creazione riuscita. L'elenco delle condivisioni può essere aggiornato, ma è necessario attendere il completamento della creazione della condivisione.

2. Aggiungere una condivisione locale Edge nel dispositivo Edge ripetendo tutti i passaggi della procedura precedente e selezionando la casella di controllo **Configura come condivisione locale Edge**. I dati nella condivisione locale rimangono nel dispositivo.

    ![Aggiungere una condivisione locale Edge](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Selezionare **Aggiungi condivisioni** per visualizzare l'elenco aggiornato delle condivisioni.

    ![Elenco aggiornato delle condivisioni](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Aggiungere un modulo

È possibile aggiungere un modulo personalizzato o predefinito. In questo dispositivo Edge non sono presenti moduli personalizzati. Per informazioni su come creare un modulo personalizzato, vedere [Sviluppare un modulo C# per il dispositivo Data Box Edge](data-box-edge-create-iot-edge-module.md).

In questa sezione si aggiungerà un modulo personalizzato al dispositivo IoT Edge creato in [Sviluppare un modulo C# per Data Box Edge](data-box-edge-create-iot-edge-module.md). Questo modulo personalizzato sposta i file da una condivisione locale Edge nel dispositivo Edge a una condivisione cloud Edge nel dispositivo. La condivisione cloud esegue quindi il push dei file all'account di archiviazione di Azure associato.

1. Passare a **Calcolo Edge > Inizia subito**. Nel riquadro **Aggiungi moduli** selezionare **Semplice** come tipo di scenario. Selezionare **Aggiungi**.
2. Nel pannello **Configura e aggiungi modulo** immettere i valori seguenti:

    
    |Campo  |Valore  |
    |---------|---------|
    |NOME     | Nome univoco per il modulo. Questo modulo è un contenitore Docker che può essere distribuito nel dispositivo IoT Edge associato a Data Box Edge.        |
    |URI immagine     | URI dell'immagine del contenitore corrispondente per il modulo.        |
    |Sono richieste credenziali     | Se questa opzione è selezionata, per recuperare i moduli con un URL corrispondente vengono usati un nome utente e una password.        |
    |Condivisione di input     | Selezionare una condivisione di input. In questo caso, la condivisione di input è la condivisione locale Edge. Il modulo usato in questo esempio sposta i file dalla condivisione locale Edge a una condivisione Edge in cui vengono caricati nel cloud.        |
    |Condivisione di output     | Selezionare una condivisione di output. In questo caso, la condivisione di output è la condivisione Edge.        |
    |Tipo di trigger     | Selezionare **File** o **Pianificazione**. Un trigger di file viene attivato ogni volta che si verifica un evento file, ad esempio la scrittura di un file nella condivisione di input. Un trigger pianificato viene attivato in base a una pianificazione definita dall'utente.         |
    |Nome trigger     | Nome univoco per il trigger.         |
    |Variabili di ambiente| Informazioni facoltative utili per definire l'ambiente in cui il modulo verrà eseguito.   |

    ![Aggiungere e configurare un modulo](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Selezionare **Aggiungi**. Il modulo verrà aggiunto. Il riquadro **Aggiungi modulo** verrà aggiornato per indicare la distribuzione del modulo. 

    ![Modulo distribuito](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verificare la trasformazione e il trasferimento dei dati

Il passaggio finale consiste nell'assicurarsi che il modulo sia connesso e in esecuzione come previsto. Lo stato di runtime del modulo deve essere In esecuzione per il dispositivo IoT Edge nella risorsa dell'hub IoT.

Per verificare che il modulo sia in esecuzione, eseguire queste operazioni:

1. Selezionare il riquadro **Aggiungi modulo**. Verrà visualizzato il pannello **Moduli**. Nell'elenco dei moduli identificare il modulo che è stato distribuito. Lo stato di runtime del modulo aggiunto sarà *in esecuzione*.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  In Esplora file connettersi alla condivisione locale Edge e alla condivisione Edge create in precedenza.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Aggiungere i dati alla condivisione locale.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    I dati vengono spostati nella condivisione cloud.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Viene quindi eseguito il push dei dati dalla condivisione cloud all'account di archiviazione. Per visualizzare i dati, passare a Storage Explorer.

    ![Verificare la trasformazione dei dati](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Il processo di convalida è stato completato.


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare il calcolo
> * Aggiungere condivisioni
> * Aggiungere un ruolo di calcolo
> * Verificare la trasformazione e il trasferimento dei dati

Per informazioni su come amministrare il dispositivo Data Box Edge, vedere:

> [!div class="nextstepaction"]
> [Usare l'interfaccia utente Web locale per amministrare un Data Box Edge](data-box-edge-manage-access-power-connectivity-mode.md)
