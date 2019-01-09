---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 0a9aaa86d44e71e429f2bfff13a56ddcb1ee2071
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550355"
---
1. Accedere al dispositivo Data Box. Assicurarsi che sia sbloccato.

    ![Dashboard di Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Passare a **Imposta interfacce di rete**. Prendere nota dell'indirizzo IP del dispositivo per l'interfaccia di rete usata per la connessione al client.

    ![Dashboard di Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Passare a **Connetti e copia** e fare clic su **REST (anteprima)**.

    ![Dashboard di Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Dalla finestra di dialogo **Accedi all'account di archiviazione e carica i dati** copiare il valore di **Endpoint di servizio BLOB**.

    ![Dashboard di Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Avviare **Blocco note** come amministratore e quindi aprire il file **hosts** che si trova in `C:\Windows\System32\Drivers\etc`.
6. Al file **hosts** aggiungere: `<device IP address> <Blob service endpoint>`
7. Per indicazioni, vedere l'immagine seguente. Salvare il file **hosts**.

    ![Dashboard di Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
