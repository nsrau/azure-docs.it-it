---
title: Restituire il dispositivo Pro Azure Stack Edge | Microsoft Docs
description: Informazioni su come cancellare i dati e restituire il dispositivo Azure Stack Edge Pro, quindi eliminare la risorsa associata al dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/27/2020
ms.author: alkohli
ms.openlocfilehash: 730774f33d43754d4ca198ed170159fa4f872e09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903663"
---
# <a name="return-your-azure-stack-edge-pro-device"></a>Restituire il dispositivo Pro Azure Stack Edge

Questo articolo descrive come eliminare i dati e restituire il dispositivo Azure Stack Edge Pro. Dopo avere restituito il dispositivo, è anche possibile eliminare la risorsa associata al dispositivo.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Cancellare i dati dai dischi dati del dispositivo
> * Avviare la restituzione del dispositivo nel portale di Azure
> * Imballare il dispositivo e pianificare il ritiro
> * Eliminare la risorsa nel portale di Azure

## <a name="erase-data-from-the-device"></a>Cancellare i dati dal dispositivo

Per cancellare i dati dai dischi dati del dispositivo, è necessario reimpostare il dispositivo. A questo scopo è possibile usare l'interfaccia utente Web locale o l'interfaccia di PowerShell.

Prima di procedere con la reimpostazione, creare una copia dei dati locali del dispositivo, se necessario. È possibile copiare i dati del dispositivo in un contenitore di Archiviazione di Azure.

È possibile avviare il ritorno del dispositivo anche prima della reimpostazione del dispositivo. 

Per reimpostare il dispositivo tramite l'interfaccia utente Web locale, seguire questa procedura.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Reimpostazione del dispositivo**.
2. Selezionare **Reimposta dispositivo**.

    ![Reimpostare il dispositivo](media/azure-stack-edge-return-device/device-reset-1.png)

3. Quando viene chiesta conferma, esaminare l'avviso e selezionare **Sì** per continuare.

    ![Conferma della reimpostazione](media/azure-stack-edge-return-device/device-reset-2.png)  

La reimpostazione cancella i dati dai dischi dati del dispositivo. A seconda della quantità di dati contenuti nel dispositivo, questo processo richiede circa 30-40 minuti.

In alternativa, connettersi all'interfaccia di PowerShell del dispositivo e usare il cmdlet `Reset-HcsAppliance` per cancellare i dati dai dischi dati. Per altre informazioni, vedere [Reimpostare il dispositivo](azure-stack-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Se si sta facendo un cambio di dispositivo o si sta eseguendo l'aggiornamento a un nuovo dispositivo, è consigliabile reimpostare il dispositivo solo dopo aver ricevuto quello nuovo.
> - La reimpostazione del dispositivo elimina solo tutti i dati locali dal dispositivo. I dati presenti nel cloud non vengono eliminati e sono soggetti ad [addebiti](https://azure.microsoft.com/pricing/details/storage/). Questi dati devono essere eliminati separatamente usando uno strumento di gestione dell'archiviazione nel cloud come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="initiate-device-return"></a>Avviare il ritorno del dispositivo

Per iniziare il processo di restituzione, seguire questa procedura.

1. Passare alla risorsa Pro/Data Box Gateway di Azure Stack Edge in portale di Azure. Nella **Panoramica**passare alla barra dei comandi nel riquadro di destra e selezionare **return Device**. 

    ![Restituisci dispositivo 1](media/azure-stack-edge-return-device/return-device-1.png)  

2. Nel pannello **Restituisci dispositivo** , in **Dettagli di base**:

    1. Fornire il numero di serie del dispositivo. Per ottenere il numero di serie del dispositivo, passare all'interfaccia utente Web locale del dispositivo e quindi passare a **Panoramica**.  
    
    ![Numero di serie 1 del dispositivo](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. Immettere il numero di tag del servizio che corrisponde a cinque o più identificatori di caratteri univoci per il dispositivo. Il tag del servizio si trova nell'angolo inferiore destro del dispositivo (quando si fa fronte al dispositivo). Estrarre il tag informazioni (si tratta di un pannello di etichette di scorrimento). Questo pannello contiene informazioni di sistema, ad esempio tag di servizio, NIC, indirizzo MAC e così via. 
    
    ![Numero di tag del servizio 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. Nell'elenco a discesa scegliere un motivo per la restituzione.

    ![Restituisci dispositivo 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. In **Dettagli spedizione**:

    1. Specificare il nome, il nome della società e l'indirizzo completo della società. Immettere un telefono dell'ufficio, inclusi il prefisso e un ID di posta elettronica per la notifica.
    2. Se serve una scatola di spedizione, è possibile richiederla. Rispondere **Sì** alla domanda **Need an empty box to return** (Serve una scatola vuota per la restituzione).

    ![Restituisci dispositivo 3](media/azure-stack-edge-return-device/return-device-3.png)

4. Esaminare le **condizioni sulla privacy** e selezionare la casella di controllo nella nota che è stata esaminata e accettata le condizioni per la privacy.

5. Selezionare **Avvia la restituzione**.

    ![Restituisci dispositivo 4](media/azure-stack-edge-return-device/return-device-4.png) 

6. Dopo aver acquisito i dettagli restituiti dal dispositivo, è possibile inviare una notifica al team operativo di Azure Stack Edge Pro tramite un messaggio di posta elettronica. È possibile usare l'applicazione di posta elettronica presumendo che l'applicazione di posta elettronica sia installata e configurata. È anche possibile copiare i dati per creare e inviare un messaggio di posta elettronica.

    ![Restituisci dispositivo 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Una volta che il team operativo di Azure Stack Edge Pro riceve il messaggio di posta elettronica, invierà un'etichetta di spedizione inversa. Quando si riceve questa etichetta, è possibile pianificare il ritiro del dispositivo con il vettore. 

## <a name="schedule-a-pickup"></a>Pianificare un ritiro

Per pianificare un prelievo, seguire questa procedura.

1. Arrestare il dispositivo. Nell'interfaccia utente Web locale passare a **Manutenzione > Power settings** (Impostazioni di alimentazione).
2. Selezionare **Arresta**. Quando viene chiesta conferma, fare clic su **Sì** per continuare. Per altre informazioni, vedere [Gestire l'avvio/arresto](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Scollegare i cavi di alimentazione e rimuovere tutti i cavi di rete dal dispositivo.
4. Preparare l'imballaggio di spedizione usando la propria scatola o la scatola vuota ricevuta da Azure. Collocare nella scatola il dispositivo e i cavi di alimentazione in dotazione con il dispositivo.
5. Applicare l'etichetta di spedizione ricevuta da Azure sull'imballaggio.
6. Pianificare un ritiro con il corriere di zona. Se il dispositivo viene restituito negli Stati Uniti, il corriere potrebbe essere UPS o FedEx. Per pianificare un ritiro con UPS:

    1. Chiamare il servizio UPS locale (numero verde specifico del paese o area).
    2. Durante la chiamata specificare il numero di tracciabilità della spedizione di reso indicato sull'etichetta stampata.
    3. Se non si specifica il numero di tracciabilità, il servizio UPS addebiterà un costo aggiuntivo al momento del ritiro.

    Invece di pianificare il prelievo, è anche possibile eliminare il Azure Stack Edge Pro nel percorso di rilascio più vicino.

## <a name="delete-the-resource"></a>Eliminare la risorsa

Dopo la ricezione nel data center di Azure, il dispositivo viene controllato per escludere la presenza di danni o segnali di manomissione.

- Se il dispositivo arriva intatto ed è in forma corretta, il contatore della fatturazione si interrompe per la risorsa. Il team operativo di Azure Stack Edge Pro contatterà l'utente per verificare che il dispositivo sia stato restituito. È quindi possibile eliminare la risorsa associata al dispositivo nel portale di Azure.
- Se il dispositivo arriva notevolmente danneggiato, è possibile che vengano applicate ammende. Per altre informazioni, vedere le [domande frequenti sul dispositivo perso o danneggiato](https://azure.microsoft.com/pricing/details/databox/edge/) e le [condizioni d'uso del servizio](https://www.microsoft.com/licensing/product-licensing/products).  


È possibile eliminare il dispositivo nel portale di Azure:

- Dopo aver effettuato l'ordine e prima che il dispositivo venga preparato da Microsoft.
- Dopo che il dispositivo è stato restituito a Microsoft, il controllo fisico viene passato al Data Center di Azure e il team operativo di Azure Stack Edge Pro chiama per verificare che il dispositivo sia stato restituito.

Se il dispositivo è stato attivato in un'altra sottoscrizione o località, Microsoft sposterà l'ordine nella nuova sottoscrizione o località entro un giorno lavorativo. Una volta spostato l'ordine, è possibile eliminare questa risorsa.


Per eliminare il dispositivo e la risorsa nel portale di Azure, seguire questa procedura.

1. Nel portale di Azure passare alla risorsa e quindi a **Panoramica**. Nella barra dei comandi selezionare **Elimina**.

    ![Selezionare Elimina](media/azure-stack-edge-return-device/delete-resource-1.png)

2. Nel pannello **Elimina dispositivo** digitare il nome del dispositivo da eliminare e selezionare **Elimina**.

    ![Conferma dell'eliminazione](media/azure-stack-edge-return-device/delete-resource-2.png)

Si riceverà una notifica dopo che il dispositivo e la risorsa associata saranno stati eliminati correttamente.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [ottenere una sostituzione Azure stack dispositivo Edge Pro](azure-stack-edge-replace-device.md).
