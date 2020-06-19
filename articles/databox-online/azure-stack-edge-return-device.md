---
title: Restituire o sostituire il dispositivo Azure Stack Edge | Microsoft Docs
description: Viene descritto come restituire o sostituire il dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: bb73494dd5fe22c3be645f732f9d0958e48edb64
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743629"
---
# <a name="return-or-replace-your-azure-stack-edge-device"></a>Restituire o sostituire il dispositivo Azure Stack Edge

Questo articolo descrive come cancellare i dati e restituire il dispositivo Azure Stack Edge. Dopo aver restituito il dispositivo, si può anche eliminare la risorsa associata al dispositivo o ordinare un dispositivo sostitutivo.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Cancellare i dati dai dischi dati del dispositivo
> * Aprire un ticket di supporto per restituire il dispositivo
> * Imballare il dispositivo e pianificare il ritiro
> * Eliminare la risorsa nel portale di Azure
> * Ottenere un dispositivo sostitutivo

## <a name="erase-data-from-the-device"></a>Cancellare i dati dal dispositivo

Per cancellare i dati dai dischi dati del dispositivo, è necessario reimpostare il dispositivo. A questo scopo è possibile usare l'interfaccia utente Web locale o l'interfaccia di PowerShell.

Prima di procedere con la reimpostazione, creare una copia dei dati locali del dispositivo, se necessario. È possibile copiare i dati del dispositivo in un contenitore di Archiviazione di Azure.

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

## <a name="open-a-support-ticket"></a>Aprire un ticket di supporto

Per iniziare il processo di restituzione, seguire questa procedura.

1. Aprire un ticket di supporto con il supporto tecnico Microsoft indicando che si vuole restituire il dispositivo. Selezionare il tipo di problema **Hardware Azure Stack Edge**.

    ![Aprire il ticket di supporto](media/azure-stack-edge-return-device/open-support-ticket-1.png)  

2. Si verrà contattati da un tecnico del supporto tecnico Microsoft. Specificare i dettagli di spedizione.
3. Se serve una scatola di spedizione, è possibile richiederla. Rispondere **Sì** alla domanda **Need an empty box to return** (Serve una scatola vuota per la restituzione).


## <a name="schedule-a-pickup"></a>Pianificare un ritiro

1. Arrestare il dispositivo. Nell'interfaccia utente Web locale passare a **Manutenzione > Power settings** (Impostazioni di alimentazione).
2. Selezionare **Arresta**. Quando viene chiesta conferma, fare clic su **Sì** per continuare. Per altre informazioni, vedere [Gestire l'avvio/arresto](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Scollegare i cavi di alimentazione e rimuovere tutti i cavi di rete dal dispositivo.
4. Preparare l'imballaggio di spedizione usando la propria scatola o la scatola vuota ricevuta da Azure. Collocare nella scatola il dispositivo e i cavi di alimentazione in dotazione con il dispositivo.
5. Applicare l'etichetta di spedizione ricevuta da Azure sull'imballaggio.
6. Pianificare un ritiro con il corriere di zona. Se il dispositivo viene restituito negli Stati Uniti, il corriere potrebbe essere UPS o FedEx. Per pianificare un ritiro con UPS:

    1. Chiamare il servizio UPS locale (numero verde specifico del paese o area).
    2. Durante la chiamata specificare il numero di tracciabilità della spedizione di reso indicato sull'etichetta stampata.
    3. Se non si specifica il numero di tracciabilità, il servizio UPS addebiterà un costo aggiuntivo al momento del ritiro.

    Anziché pianificare il ritiro, è anche possibile consegnare il dispositivo Azure Stack Edge al più vicino punto di consegna.

## <a name="delete-the-resource"></a>Eliminare la risorsa

Dopo la ricezione nel data center di Azure, il dispositivo viene controllato per escludere la presenza di danni o segnali di manomissione.

- Se il dispositivo arriva intatto e in buono stato, il contatore di fatturazione si interrompe per tale risorsa. Si verrà contattati dal supporto tecnico Microsoft per confermare la restituzione del dispositivo. È quindi possibile eliminare la risorsa associata al dispositivo nel portale di Azure.
- Se il dispositivo arriva notevolmente danneggiato, è possibile che vengano applicate ammende. Per altre informazioni, vedere le [domande frequenti sul dispositivo perso o danneggiato](https://azure.microsoft.com/pricing/details/databox/edge/) e le [condizioni d'uso del servizio](https://www.microsoft.com/licensing/product-licensing/products).  


È possibile eliminare il dispositivo nel portale di Azure:
-    Dopo aver effettuato l'ordine e prima che il dispositivo venga preparato da Microsoft.
-    Dopo la restituzione a Microsoft, il dispositivo viene sottoposto a un'ispezione fisica presso il data center di Azure, quindi il supporto tecnico Microsoft chiama per confermare che il dispositivo è stato restituito.

Se il dispositivo è stato attivato in un'altra sottoscrizione o località, Microsoft sposterà l'ordine nella nuova sottoscrizione o località entro un giorno lavorativo. Una volta spostato l'ordine, è possibile eliminare questa risorsa.


Per eliminare il dispositivo e la risorsa nel portale di Azure, seguire questa procedura.

1. Nel portale di Azure passare alla risorsa e quindi a **Panoramica**. Nella barra dei comandi selezionare **Elimina**.

    ![Selezionare Elimina](media/azure-stack-edge-return-device/delete-resource-1.png)

2. Nel pannello **Elimina dispositivo** digitare il nome del dispositivo da eliminare e selezionare **Elimina**.

    ![Conferma dell'eliminazione](media/azure-stack-edge-return-device/delete-resource-2.png)

Si riceverà una notifica dopo che il dispositivo e la risorsa associata saranno stati eliminati correttamente.

## <a name="get-a-replacement-device"></a>Ottenere un dispositivo sostitutivo

Un dispositivo sostitutivo è necessario quando il dispositivo esistente presenta un errore hardware o necessita di un aggiornamento. Se il dispositivo ha un problema a livello di hardware, seguire questa procedura:

1. [Aprire un ticket di supporto per un problema hardware](#open-a-support-ticket). Il supporto tecnico Microsoft determinerà che un'unità sostituibile sul campo non è disponibile per questa istanza o che l'hardware del dispositivo deve essere aggiornato. In entrambi i casi, il supporto tecnico ordinerà un dispositivo sostitutivo.
2. [Creare una nuova risorsa](azure-stack-edge-deploy-prep.md#create-a-new-resource) per il dispositivo sostitutivo. Selezionare la casella di controllo accanto a **I have an Azure Stack Edge device** (Ho un dispositivo Azure Stack Edge). 
3. Dopo aver ricevuto un dispositivo sostitutivo, [installarlo](azure-stack-edge-deploy-install.md) e [attivarlo](azure-stack-edge-deploy-connect-setup-activate.md) con la nuova risorsa.
4. Seguire tutti i passaggi per restituire il dispositivo originale:
    1. Aprire un altro ticket per restituire il dispositivo originale.
    2. [Cancellare i dati contenuti nel dispositivo](#erase-data-from-the-device).
    3. [Pianificare un ritiro](#schedule-a-pickup).
    5. [Eliminare la risorsa](#delete-the-resource) associata al dispositivo restituito.



## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire la larghezza di banda](azure-stack-edge-manage-bandwidth-schedules.md).
