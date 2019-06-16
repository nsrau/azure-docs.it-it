---
title: Restituisce il dispositivo periferico casella dei dati di Azure | Microsoft Docs
description: Viene descritto come restituire il dispositivo perimetrale casella dei dati di Azure ed eliminare l'ordine per il dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/07/2019
ms.author: alkohli
ms.openlocfilehash: 9aeae0ab68d809b36a3316054f12a5a9657721f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65468605"
---
# <a name="return-your-azure-data-box-edge-device"></a>Restituisce il dispositivo periferico casella dei dati di Azure

Questo articolo descrive come cancellare i dati e quindi restituire il dispositivo periferico casella dei dati di Azure. Dopo la restituzione del dispositivo, è anche possibile eliminare la risorsa associata al dispositivo.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Cancellare i dati dai dischi dati nel dispositivo
> * Aprire un ticket di supporto per restituire il dispositivo
> * Comprimere il dispositivo e pianificare un ritiro
> * Eliminare la risorsa nel portale di Azure

## <a name="erase-data-from-the-device"></a>Cancellare i dati dal dispositivo

Per cancellare i dati dai dischi dati del dispositivo, è necessario reimpostare il dispositivo. È possibile reimpostare il dispositivo tramite l'interfaccia utente web locale o l'interfaccia di PowerShell.

Prima viene reimpostato, creare una copia dei dati locali sul dispositivo, se necessario. È possibile copiare i dati dal dispositivo in un contenitore di archiviazione di Azure.

Per reimpostare il dispositivo usando l'interfaccia utente web locale, procedere come segue.

1. Nel web locale dell'interfaccia utente, passare a **manutenzione > Reimposta dispositivo**.
2. Selezionare **dispositivo ripristinato alle impostazioni**.

    ![Reimpostare il dispositivo](media/data-box-edge-return-device/device-reset-1.png)

3. Quando viene richiesta la conferma, esaminare l'avviso e selezionare **Sì** per continuare.

    ![Confermare la reimpostazione](media/data-box-edge-return-device/device-reset-2.png)  

La reimpostazione vengono cancellati i dati dai dischi dati dispositivo. A seconda della quantità di dati nel dispositivo, questo processo richiede circa 30 e 40 minuti.

In alternativa, connettersi all'interfaccia di PowerShell del dispositivo e l'uso di `Reset-HcsAppliance` cmdlet di cancellare i dati dai dischi dati. Per altre informazioni, vedere [Reimposta il dispositivo](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Se si esegue lo scambio o l'aggiornamento a un nuovo dispositivo, è consigliabile reimpostare il dispositivo solo dopo aver ricevuto il nuovo dispositivo.
> - Il dispositivo di reimpostare solo Elimina tutti i dati locali, il dispositivo viene spento. I dati che si trova nel cloud non verrà eliminati e raccoglie [addebiti](https://azure.microsoft.com/pricing/details/storage/). Questi dati devono essere eliminato separatamente usando uno strumento di gestione di archiviazione cloud, ad esempio [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Aprire un ticket di supporto

Per iniziare il processo restituito, procedere come segue.

1. Aprire un ticket di supporto con il supporto di Microsoft che indica che si desidera restituire il dispositivo. Selezionare il tipo di problema **dati casella di bordo Hardware**.

    ![Apri ticket di supporto](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Un tecnico del supporto tecnico Microsoft contatterà l'utente. Fornire i dettagli di spedizione.
3. Se occorre una casella di spedizione restituita, è possibile richiederlo. Risposte **Yes** alla domanda **necessario una casella vuota per restituire**.


## <a name="schedule-a-pickup"></a>Pianificare un ritiro

1. Arrestare il dispositivo. Nell'interfaccia utente Web locale passare a **Manutenzione > Power settings** (Impostazioni di alimentazione).
2. Selezionare **arrestare**. Quando viene richiesta la conferma, fare clic su **Sì** per continuare. Per altre informazioni, vedere [gestire la potenza di](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Scollegare i cavi di alimentazione e rimuovere tutti i cavi di rete dal dispositivo.
4. Preparare il pacchetto di spedizione utilizzando una propria finestra o la casella vuota che è stato ricevuto da Azure. Posizionare il dispositivo e i cavi di alimentazione che sono stati forniti con il dispositivo nella finestra di.
5. Applicare l'etichetta di spedizione del pacchetto ricevuto da Azure.
6. Pianificare un ritiro con il corriere di zona. Se la restituzione del dispositivo nell'area Stati Uniti, il gestore telefonico è UPS. Per pianificare un ritiro:

    1. Chiamare il servizio UPS locale (numero verde specifico del paese).
    2. La chiamata, racchiudere il numero riportato sulla stampa etichetta di tracciabilità della spedizione inversa.
    3. Se non è racchiuso tra virgolette il numero di tracciabilità, UPS richiederà pagare un costo aggiuntivo durante il ritiro.

    Invece di pianificare il ritiro, è possibile eliminare anche all'esterno del bordo di finestra di dati in corrispondenza della posizione di partenza più vicina.

## <a name="delete-the-resource"></a>Eliminare la risorsa

Dopo che il dispositivo è ricevuto nel Data Center di Azure, il dispositivo viene controllato per danni o eventuali segnali di manomissione.

- Se il dispositivo arriva intatti e formattati correttamente, viene arrestata fini della fatturazione per tale risorsa. Il supporto tecnico Microsoft contatterà l'utente per confermare che il dispositivo è stato restituito. È quindi possibile eliminare la risorsa associata al dispositivo nel portale di Azure.
- Se il dispositivo arriva seriamente danneggiato, potrebbero essere applicati multe. Per informazioni dettagliate, vedere la [domande frequenti sul dispositivo perso o danneggiato](https://azure.microsoft.com/pricing/details/databox/edge/) e [prodotto Terms of Service](https://www.microsoft.com/licensing/product-licensing/products).  


È possibile eliminare il dispositivo nel portale di Azure:
-   Dopo aver inserito l'ordine e prima che il dispositivo viene preparato da Microsoft.
-   Dopo la restituzione del dispositivo a Microsoft, passa le ispezioni fisiche presso il Data Center di Azure e viene chiamato il supporto tecnico Microsoft per verificare che il dispositivo è stato restituito.

Se non è stato attivato il dispositivo in un'altra sottoscrizione o località, Microsoft sposterà l'ordine per la nuova sottoscrizione o un percorso entro un giorno lavorativo. Dopo che l'ordine viene spostata, è possibile eliminare questa risorsa.


Eseguire i passaggi seguenti per eliminare il dispositivo e la risorsa nel portale di Azure.

1. Nel portale di Azure, passare alla risorsa e quindi **Panoramica**. Dalla barra dei comandi, selezionare **Elimina**.

    ![Selezionare Elimina](media/data-box-edge-return-device/delete-resource-1.png)

2. Nel **Elimina dispositivo** blade, digitare il nome del dispositivo di cui si desidera eliminare, quindi scegliere **eliminare**.

    ![Conferma dell'eliminazione](media/data-box-edge-return-device/delete-resource-2.png)

Si riceverà una notifica dopo il dispositivo e la risorsa associata viene eliminata correttamente.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire la larghezza di banda](data-box-edge-manage-bandwidth-schedules.md).
