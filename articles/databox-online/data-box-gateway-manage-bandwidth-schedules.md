---
title: Gestione di utenti in Azure Data Box Gateway | Microsoft Docs
description: Descrive come usare il portale di Azure per gestire le pianificazioni della larghezza di banda in Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 10/09/2018
ms.author: alkohli
ms.openlocfilehash: e4bc5de9345bcb65f82ca7ac0095b3e0fd3907e9
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165999"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-gateway"></a>Usare il portale di Azure per gestire le pianificazioni della larghezza di banda in Azure Data Box Gateway  

Questo articolo descrive come gestire gli utenti in Azure Data Box Gateway. Le pianificazioni della larghezza di banda consentono di configurare l'uso della larghezza di banda della rete in più pianificazioni orarie. Queste pianificazioni possono essere applicate alle operazioni di caricamento e download dal dispositivo al cloud. 

È possibile aggiungere, modificare o eliminare le pianificazioni della larghezza di banda per il Data Box Gateway dal portale di Azure.

> [!IMPORTANT]
> - Data Box Gateway è in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Aggiungere una pianificazione
> * Modificare una pianificazione
> * Eliminare una pianificazione 


## <a name="add-a-schedule"></a>Aggiungere una pianificazione

Seguire questa procedura nel portale di Azure per aggiungere un utente.

1. Nel portale di Azure della risorsa Data Box Gateway, passare a **Larghezza di banda**.
2. Nel riquadro a destra fare clic su **+Aggiungi pianificazione**.

    ![Fare clic su Aggiungi utente](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-1.png)

3. In **Aggiungi pianificazione**: 

    1. Indicare il **giorno di inizio**, il **giorno di fine**, l'**orario di inizio** e l'**orario di fine** della pianificazione. 
    2. Se questa pianificazione verrà eseguita tutto il giorno, sarà possibile selezionare l'opzione **Tutto il giorno**. 
    3. Per **Velocità larghezza di banda** si intende la larghezza di banda espressa in megabit al secondo (Mbps) usata dal dispositivo nelle operazioni che coinvolgono il cloud (sia caricamento sia download). Specificare un numero compreso tra 1 e 1000 per questo campo. 
    4. Selezionare una larghezza di banda **Senza limiti** se non si vuole limitare la data di caricamento e download. 
    5. Fare clic su **Aggiungi**.

    ![Fare clic su Aggiungi utente](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-2.png)

3. Verrà creata una pianificazione con i parametri specificati. Questa pianificazione viene quindi visualizzata nell'elenco delle pianificazioni della larghezza di banda nel portale.


## <a name="edit-schedule"></a>Modificare la pianificazione

Eseguire la procedura seguente per modificare una pianificazione della larghezza di banda. 

1. Nel portale di Azure, selezionare la risorsa Data Box Gateway e quindi passare a Larghezza di banda. 
2. Nell'elenco delle pianificazioni della larghezza di banda, selezionare e fare clic su una pianificazione da modificare.
    ![Modificare un utente](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-1.png)

3. Apportare le modifiche desiderate e salvarle.

    ![Modificare un utente](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-2.png)

4. Dopo aver modificato la pianificazione, l'elenco di pianificazioni viene aggiornato per riflettere la pianificazione modificata.

    ![Modificare un utente](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Eliminare una pianificazione

Usare la procedura seguente per eliminare una pianificazione della larghezza di banda associata al dispositivo Data Box Gateway.

1. Nel portale di Azure, selezionare la risorsa Data Box Gateway e quindi passare a **Larghezza di banda**.  

2. Nell'elenco delle pianificazioni della larghezza di banda, selezionare una pianificazione da eliminare. Fare clic con il pulsante destro del mouse per visualizzare il menu di scelta rapida e quindi fare clic su **Elimina**. 

   ![Eliminare un utente](media/data-box-gateway-manage-bandwidth-schedules/delete-schedule-1.png)

3.  Dopo aver eliminato la pianificazione, l'elenco di pianificazioni viene aggiornato.



## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire la larghezza di banda](data-box-gateway-manage-bandwidth-schedules.md).
