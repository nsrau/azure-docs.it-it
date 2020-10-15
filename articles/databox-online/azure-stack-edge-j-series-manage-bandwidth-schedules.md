---
title: Azure Stack GPU Pro Edge gestire le pianificazioni della larghezza di banda | Microsoft Docs
description: Viene descritto come usare la portale di Azure per gestire le pianificazioni della larghezza di banda sulla GPU di Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: a0d596c7c1046ea26ac389a48c17fa5abccbfd12
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951605"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge-pro-gpu"></a>Usare il portale di Azure per gestire le pianificazioni della larghezza di banda sulla GPU Pro Azure Stack Edge 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Questo articolo descrive come gestire le pianificazioni della larghezza di banda in Azure Stack Edge Pro. Le pianificazioni della larghezza di banda consentono di configurare l'uso della larghezza di banda della rete in più pianificazioni orarie. Queste pianificazioni possono essere applicate alle operazioni di caricamento e download dal dispositivo al cloud.

È possibile aggiungere, modificare o eliminare le pianificazioni della larghezza di banda per il Azure Stack Edge Pro tramite il portale di Azure.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Aggiungere una pianificazione
> * Modificare una pianificazione
> * Eliminare una pianificazione


## <a name="add-a-schedule"></a>Aggiungere una pianificazione

Seguire questa procedura nel portale di Azure per aggiungere una pianificazione.

1. Nella portale di Azure per la risorsa di Azure Stack Edge passare a **larghezza di banda**.
2. Nel riquadro di destra fare clic su **+ Aggiungi pianificazione**.

    ![Selezionare la larghezza di banda](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-1.png)

3. In **Aggiungi pianificazione**: 

   1. Fornire il **giorno di inizio**, il **giorno di fine**, l'ora di **inizio**e l' **ora di fine** della pianificazione.
   2. Selezionare l'opzione **tutto il giorno** se la pianificazione deve essere eseguita tutto il giorno.
   3. La **velocità della larghezza di banda** è la larghezza di banda in megabit al secondo (Mbps) usata dal dispositivo in operazioni che coinvolgono il cloud (sia uploads che download). Specificare un numero compreso tra 20 e 2.147.483.647 per questo campo.
   4. Selezionare una larghezza di banda **Senza limiti** se non si vuole limitare la data di caricamento e download.
   5. Selezionare **Aggiungi**.

      ![Aggiungere la pianificazione](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-2.png)

3. Verrà creata una pianificazione con i parametri specificati. Questa pianificazione viene quindi visualizzata nell'elenco delle pianificazioni della larghezza di banda nel portale.

    ![Elenco aggiornato delle pianificazioni della larghezza di banda](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Modificare la pianificazione

Eseguire la procedura seguente per modificare una pianificazione della larghezza di banda.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a larghezza di **banda**. 
2. Dall'elenco delle pianificazioni della larghezza di banda selezionare una pianificazione che si desidera modificare.
    ![Selezionare la pianificazione della larghezza di banda](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-1.png)

3. Apportare le modifiche desiderate e salvarle.

    ![Modificare un utente](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-2.png)

4. Dopo aver modificato la pianificazione, l'elenco di pianificazioni viene aggiornato per riflettere la pianificazione modificata.

    ![Modifica utente 2](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Eliminare una pianificazione

Seguire questa procedura per eliminare una pianificazione della larghezza di banda associata al dispositivo Azure Stack Edge Pro.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a larghezza di **banda**.  

2. Nell'elenco delle pianificazioni della larghezza di banda, selezionare una pianificazione da eliminare. In **Modifica la pianificazione** selezionare **Elimina**. Alla richiesta di conferma selezionare **Sì**.

   ![Eliminare un utente](media/azure-stack-edge-j-series-manage-bandwidth-schedules/delete-schedule-2.png)

3. Dopo aver eliminato la pianificazione, l'elenco di pianificazioni viene aggiornato.


## <a name="next-steps"></a>Passaggi successivi

- Vedere le informazioni su come [gestire le condivisioni](azure-stack-edge-j-series-manage-shares.md).
