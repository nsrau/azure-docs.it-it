---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: d01cf90c42efbe611748027d0ea47ff8af3e5621
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553127"
---
1. Accedere al dispositivo Data Box. Assicurarsi che sia sbloccato.

    ![Screenshot che mostra il dashboard con il dispositivo visualizzato come sbloccato.](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Passare a **Imposta interfacce di rete**. Prendere nota dell'indirizzo IP del dispositivo per l'interfaccia di rete usata per la connessione al client.

    ![Screenshot che mostra le impostazioni di rete in cui è visibile l'indirizzo IP.](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Passare a **Connetti e copia** e fare clic su **REST**.

    ![Screenshot che mostra il riquadro Connetti e copia in cui è possibile selezionare REST come impostazione di accesso.](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Dalla finestra di dialogo **Accedi all'account di archiviazione e carica i dati** copiare il valore di **Endpoint di servizio BLOB**.

    ![Screenshot che mostra la finestra di dialogo Accedi all'account di archiviazione e carica i dati in cui copiare il valore di Endpoint di servizio BLOB.](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Avviare **Blocco note** come amministratore e quindi aprire il file **hosts** che si trova in `C:\Windows\System32\Drivers\etc`.
6. Al file **hosts** aggiungere: `<device IP address> <Blob service endpoint>`
7. Per indicazioni, vedere l'immagine seguente. Salvare il file **hosts**.

    ![Screenshot che mostra un documento del Blocco note con l'indirizzo IP e l'endpoint di servizio BLOB aggiunti.](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
