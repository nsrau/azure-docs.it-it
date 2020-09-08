---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/21/2019
ms.author: alkohli
ms.openlocfilehash: 0c6845f081ccbe42e70964eaa939d58597e3b69b
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89080561"
---
1. Passare all'interfaccia utente Web locale del dispositivo e accedere al dispositivo. Verificare che il dispositivo sia sbloccato.

2. Passare alla pagina **Impostazioni di rete**. Prendere nota dell'indirizzo IP del dispositivo per l'interfaccia di rete usata per la connessione al client.

3. Se si utilizza un client Windows remoto, avviare il **Blocco note** come amministratore e quindi aprire il file hosts che si trova in `C:\Windows\System32\Drivers\etc`.

4. Al file hosts aggiungere: `<Device IP address> <Blob service endpoint>`

    Si è ottenuto l'endpoint del servizio BLOB dall'account di archiviazione Edge creato nel portale di Azure. Si userà solo il suffisso dell'endpoint del servizio BLOB.

    Per indicazioni, vedere l'immagine seguente. Salvare il file `hosts`.

    ![Modificare il file hosts nel client Windows](media/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint/hosts-file-1.png)