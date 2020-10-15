---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 3474a97f4b05fd1f944e18abebcef927232453e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89080571"
---
Se questa è la prima volta che si usa Storage Explorer, è necessario completare i passaggi seguenti.

1. Dalla barra dei comandi superiore passare a **Modifica > API di Azure Stack di destinazione**.

    ![Configurare Storage Explorer](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. Riavviare Storage Explorer per rendere effettive le modifiche.


Completare i passaggi seguenti per connettere l'account di archiviazione e verificare la connessione.

1. In Storage Explorer selezionare gli account di archiviazione. Fare clic con il pulsante destro del mouse e scegliere **Connetti ad archiviazione di Azure**. 

    ![Configurare Storage Explorer](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. Nella finestra di dialogo **Connect to Azure Storage** (Connetti ad Archiviazione di Azure) selezionare **Use a storage account name and key** (Usare il nome e la chiave di un account di archiviazione).

    ![Configurare Storage Explorer](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. Nella finestra di dialogo **Connetti con nome e chiave** seguire questa procedura:

    1. Immettere un nome visualizzato per l'account di archiviazione Edge. 
    2. Specificare il nome dell'account di archiviazione Edge.
    3. Incollare la chiave di accesso ottenuta dalle API locali del dispositivo tramite Azure Resource Manager.
    4. Per il dominio di archiviazione selezionare **Altro (specificare sotto)** e quindi specificare il suffisso dell'endpoint del servizio BLOB nel formato: `<appliance name>.<DNSdomain>`. 
    5. Selezionare **Usa HTTP** perché il trasferimento viene effettuato tramite *HTTP*. 
    6. Selezionare **Avanti**.

    ![Configurare Storage Explorer](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. Nella finestra di dialogo **Riepilogo connessione** verificare le informazioni immesse. Selezionare **Connetti**.

    ![Configurare Storage Explorer](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. L'account aggiunto viene visualizzato nel riquadro sinistro di Storage Explorer con (Esterno, Altro) aggiunto al nome. Per visualizzare il contenitore, selezionare **Contenitori BLOB**.

    ![Visualizzare i contenitori BLOB](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

Il passaggio successivo consiste nel verificare che il trasferimento dei dati funzioni correttamente su questa connessione.

Eseguire la procedura seguente per caricare i dati nell'account di archiviazione Edge nel dispositivo che dovrebbe essere associato automaticamente all'account di archiviazione di Azure mappato.

1. Selezionare il contenitore in cui si vogliono caricare i dati nell'account di archiviazione Edge. Selezionare **Carica** e quindi **Carica file**.

    ![Verificare il trasferimento dei dati](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. Nella finestra di dialogo **Carica file** individuare e selezionare i file da caricare. Selezionare **Avanti**.

    ![Verificare il trasferimento dei dati](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. Verificare che i file siano stati caricati. I file caricati vengono visualizzati nel contenitore.

    ![Verificare il trasferimento dei dati](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. Connettersi quindi all'account di archiviazione di Azure mappato a questo account di archiviazione Edge. Tutti i dati caricati nell'account di archiviazione Edge dovrebbero essere associati automaticamente all'account di archiviazione di Azure. 
    
    Per ottenere la stringa di connessione per l'account di archiviazione di Azure, passare ad **Account di archiviazione di Azure > Chiavi di accesso** e copiare la stringa di connessione.

    ![Verificare il trasferimento dei dati](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    Usare la stringa di connessione per collegarsi all'account di archiviazione di Azure.  

    ![Verificare il trasferimento dei dati](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. Nella finestra di dialogo **Riepilogo connessione** verificare le informazioni immesse. Selezionare **Connetti**.

    ![Verificare il trasferimento dei dati](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. Si noterà che i file caricati nell'account di archiviazione Edge sono stati trasferiti nell'account di archiviazione di Azure.

    ![Verificare il trasferimento dei dati](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)