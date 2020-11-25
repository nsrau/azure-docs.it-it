---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 10feccb53b28181d260e7738ab99bdc2e3c9340c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553168"
---
Completare i passaggi seguenti per connettere l'account di archiviazione e verificare la connessione.

1. In Storage Explorer aprire la finestra di dialogo **Connect to Azure storage**(Connetti ad Archiviazione di Azure). Nella finestra di dialogo **Connect to Azure Storage** (Connetti ad Archiviazione di Azure) selezionare **Use a storage account name and key** (Usare il nome e la chiave di un account di archiviazione).

    ![Screenshot che mostra la finestra di dialogo di dialogo Connetti ad Archiviazione di Azure con l'opzione Usa nome e chiave dell'account di archiviazione selezionata.](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Incollare i valori di **Nome account** e **Chiave account** (valore di key1 dalla pagina **Connetti e copia** dell'interfaccia utente Web locale). Per Dominio endpoint archiviazione selezionare **Altro (specificare sotto)** e quindi specificare l'endpoint di servizio BLOB, come mostrato di seguito. Selezionare l'opzione **Use HTTP** (Usa HTTP) solo per il trasferimento tramite *HTTP*. Se si usa *HTTPS*, lasciare deselezionata l'opzione. Selezionare **Avanti**.

    ![Screenshot che mostra la finestra di dialogo Connetti con nome e chiave con i valori immessi.](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Nella finestra di dialogo **Riepilogo connessione** verificare le informazioni immesse. Selezionare **Connetti**.

    ![Screenshot che mostra la finestra di dialogo Riepilogo connessione con l'opzione Connetti selezionata.](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. L'account aggiunto viene visualizzato nel riquadro sinistro di Storage Explorer con (Esterno, Altro) aggiunto al nome. Per visualizzare il contenitore, fare clic su **Blob Containers** (Contenitori BLOB).

    ![Screenshot che mostra il menu Explorer con l'opzione Contenitori BLOB selezionata.](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
