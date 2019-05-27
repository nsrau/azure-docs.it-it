---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150750"
---
Completare i passaggi seguenti per connettere l'account di archiviazione e verificare la connessione.

1. In Storage Explorer aprire la finestra di dialogo **Connect to Azure storage**(Connetti ad Archiviazione di Azure). Nella finestra di dialogo **Connect to Azure Storage** (Connetti ad Archiviazione di Azure) selezionare **Use a storage account name and key** (Usare il nome e la chiave di un account di archiviazione).

    ![Dashboard di Data Box](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Incollare i valori di **Nome account** e **Chiave account** (valore di key1 dalla pagina **Connetti e copia** dell'interfaccia utente Web locale). Per Dominio endpoint archiviazione selezionare **Altro (specificare sotto)** e quindi specificare l'endpoint di servizio BLOB, come mostrato di seguito. Selezionare l'opzione **Use HTTP** (Usa HTTP) solo per il trasferimento tramite *HTTP*. Se si usa *HTTPS*, lasciare deselezionata l'opzione. Selezionare **Avanti**.

    ![Dashboard di Data Box](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Nella finestra di dialogo **Riepilogo connessione** verificare le informazioni immesse. Selezionare **Connessione**.

    ![Dashboard di Data Box](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. L'account aggiunto viene visualizzato nel riquadro sinistro di Storage Explorer con (Esterno, Altro) aggiunto al nome. Per visualizzare il contenitore, fare clic su **Blob Containers** (Contenitori BLOB).

    ![Dashboard di Data Box](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
