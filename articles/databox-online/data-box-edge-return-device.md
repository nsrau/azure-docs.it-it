---
title: Restituire o sostituire il dispositivo Azure Data Box Edge | Microsoft Docs
description: Viene descritto come restituire o sostituire il dispositivo Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 07/19/2019
ms.author: alkohli
ms.openlocfilehash: a5fee604a529e9ca6153f6c189f199577ae65426
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356155"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>Restituisce o sostituisce il dispositivo Azure Data Box Edge

Questo articolo descrive come eliminare i dati e restituire il dispositivo Azure Data Box Edge. Dopo aver restituito il dispositivo, è anche possibile eliminare la risorsa associata al dispositivo o ordinare un dispositivo sostitutivo.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Cancellazione dei dati dai dischi dati del dispositivo
> * Aprire un ticket di supporto per la restituzione del dispositivo
> * Comprimere il dispositivo e pianificare un pickup
> * Elimina la risorsa in portale di Azure
> * Ottenere un dispositivo sostitutivo

## <a name="erase-data-from-the-device"></a>Cancellare i dati dal dispositivo

Per eliminare i dati dai dischi dati del dispositivo, è necessario reimpostare il dispositivo. È possibile reimpostare il dispositivo usando l'interfaccia utente Web locale o l'interfaccia di PowerShell.

Prima di reimpostare, creare una copia dei dati locali nel dispositivo, se necessario. È possibile copiare i dati dal dispositivo a un contenitore di archiviazione di Azure.

Per reimpostare il dispositivo tramite l'interfaccia utente Web locale, seguire questa procedura.

1. Nell'interfaccia utente Web locale passare a **manutenzione > reimpostazione del dispositivo**.
2. Selezionare **Reimposta dispositivo**.

    ![Reimpostare il dispositivo](media/data-box-edge-return-device/device-reset-1.png)

3. Quando viene richiesta la conferma, esaminare l'avviso e selezionare **Sì** per continuare.

    ![Conferma reimpostazione](media/data-box-edge-return-device/device-reset-2.png)  

Il ripristino cancella i dati dai dischi dati del dispositivo. A seconda della quantità di dati nel dispositivo, questo processo richiede circa 30-40 minuti.

In alternativa, connettersi all'interfaccia di PowerShell del dispositivo e usare il `Reset-HcsAppliance` cmdlet per cancellare i dati dai dischi dati. Per altre informazioni, vedere [reimpostare il dispositivo](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Se si sta scambiando o aggiornando un nuovo dispositivo, si consiglia di reimpostare il dispositivo solo dopo la ricezione del nuovo dispositivo.
> - Il ripristino del dispositivo eliminerà solo tutti i dati locali dal dispositivo. I dati presenti nel cloud non vengono eliminati e gli [addebiti](https://azure.microsoft.com/pricing/details/storage/) vengono raccolti. Questi dati devono essere eliminati separatamente usando uno strumento di gestione dell'archiviazione cloud come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Aprire un ticket di supporto

Per iniziare il processo di restituzione, seguire questa procedura.

1. Aprire un ticket di supporto con supporto tecnico Microsoft per indicare che si vuole restituire il dispositivo. Selezionare il tipo di problema come **Data Box Edge hardware**.

    ![Apri ticket di supporto](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Un tecnico supporto tecnico Microsoft ti contatterà. Specificare i dettagli della spedizione.
3. Se è necessaria una casella di spedizione di ritorno, è possibile richiederla. Risposta **Sì** alla domanda è **necessario che venga restituita una casella vuota**.


## <a name="schedule-a-pickup"></a>Pianificare un prelievo

1. Arrestare il dispositivo. Nell'interfaccia utente Web locale passare a **Manutenzione > Power settings** (Impostazioni di alimentazione).
2. Selezionare **Arresta**. Quando viene richiesta la conferma, fare clic su **Sì** per continuare. Per altre informazioni, vedere [gestire la potenza](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Scollegare i cavi di alimentazione e rimuovere tutti i cavi di rete dal dispositivo.
4. Preparare il pacchetto di spedizione usando la propria casella o la casella vuota ricevuta da Azure. Posizionare il dispositivo e i cavi di alimentazione forniti con il dispositivo nella casella.
5. Applicare l'etichetta di spedizione ricevuta da Azure nel pacchetto.
6. Pianificare un ritiro con il corriere di zona. Se la restituzione del dispositivo è in uso, il vettore è UPS. Per pianificare un ritiro:

    1. Chiamare il servizio UPS locale (numero verde specifico del paese).
    2. Nella chiamata, citare il numero di tracciamento della spedizione inversa come indicato nell'etichetta stampata.
    3. Se il numero di tracciabilità non è racchiuso tra virgolette, UPS richiederà di pagare un addebito aggiuntivo durante il ritiro.

    Anziché pianificare il prelievo, è anche possibile eliminare il Data Box Edge nel percorso di rilascio più vicino.

## <a name="delete-the-resource"></a>Elimina la risorsa

Dopo la ricezione del dispositivo nel Data Center di Azure, il dispositivo viene controllato per eventuali danni o eventuali segnali di manomissione.

- Se il dispositivo arriva intatto e in forma corretta, il contatore di fatturazione si interrompe per tale risorsa. Supporto tecnico Microsoft contatterà l'utente per verificare che il dispositivo sia stato restituito. È quindi possibile eliminare la risorsa associata al dispositivo nella portale di Azure.
- Se il dispositivo viene danneggiato in modo significativo, è possibile che vengano applicate le ammende. Per informazioni dettagliate, vedere le [domande frequenti su dispositivi persi o danneggiati e condizioni per il](https://azure.microsoft.com/pricing/details/databox/edge/) [prodotto del servizio](https://www.microsoft.com/licensing/product-licensing/products).  


È possibile eliminare il dispositivo nel portale di Azure:
-   Dopo aver effettuato l'ordine e prima che il dispositivo venga preparato da Microsoft.
-   Dopo la restituzione del dispositivo a Microsoft, il controllo fisico viene passato al Data Center di Azure e supporto tecnico Microsoft chiama per verificare che il dispositivo sia stato restituito.

Se il dispositivo è stato attivato in un'altra sottoscrizione o località, Microsoft sposterà l'ordine nella nuova sottoscrizione o località entro un giorno lavorativo. Dopo lo spostamento dell'ordine, è possibile eliminare questa risorsa.


Eseguire la procedura seguente per eliminare il dispositivo e la risorsa in portale di Azure.

1. Nella portale di Azure passare alla risorsa e quindi a **Panoramica**. Dalla barra dei comandi, selezionare **Elimina**.

    ![Selezionare Elimina](media/data-box-edge-return-device/delete-resource-1.png)

2. Nel pannello **Elimina dispositivo** Digitare il nome del dispositivo che si desidera eliminare e selezionare **Elimina**.

    ![Conferma eliminazione](media/data-box-edge-return-device/delete-resource-2.png)

Si riceverà una notifica dopo che il dispositivo e la risorsa associata verranno eliminati correttamente.

## <a name="get-a-replacement-device"></a>Ottenere un dispositivo sostitutivo

Un dispositivo sostitutivo è necessario quando il dispositivo esistente presenta un errore hardware o richiede un aggiornamento. Quando il dispositivo presenta un problema hardware, seguire questa procedura:

1. [Aprire un ticket di supporto per problemi hardware](#open-a-support-ticket). Supporto tecnico Microsoft determinerà che un'unità di sostituzione dei campi (FRU) non è disponibile per questa istanza o che il dispositivo necessita di un aggiornamento hardware. In entrambi i casi, il supporto ordina un dispositivo sostitutivo.
2. [Creare una nuova risorsa](data-box-edge-deploy-prep.md#create-a-new-resource) per il dispositivo sostitutivo. Assicurarsi di selezionare la casella di controllo con **un dispositivo data box Edge**. 
3. Dopo aver ricevuto un dispositivo sostitutivo, [installare](data-box-edge-deploy-install.md) e [attivare](data-box-edge-deploy-connect-setup-activate.md) il dispositivo sostitutivo con la nuova risorsa.
4. Seguire tutti i passaggi per restituire il dispositivo originale:
    1. Aprire un altro ticket per restituire il dispositivo originale.
    2. [Cancellare i dati nel dispositivo](#erase-data-from-the-device).
    3. [Pianificare un prelievo](#schedule-a-pickup).
    5. [Eliminare la risorsa](#delete-the-resource) associata al dispositivo restituito.



## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire la larghezza di banda](data-box-edge-manage-bandwidth-schedules.md).
