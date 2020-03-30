---
title: Restituire o sostituire il dispositivo Azure Data Box Edge Documenti Microsoft
description: Viene descritto come restituire o sostituire il dispositivo Azure Data Box Edge.Describes how to return or replace the Azure Data Box Edge device.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: 521277b2eed7edfba016f6a80e8f877decfb0ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651101"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>Restituire o sostituire il dispositivo Azure Data Box EdgeReturn or replace your Azure Data Box Edge device

Questo articolo descrive come cancellare i dati e quindi restituire il dispositivo Azure Data Box Edge.This article describes how to wipe the data and then return your Azure Data Box Edge device. Dopo aver restituito il dispositivo, è anche possibile eliminare la risorsa associata al dispositivo o ordinare un dispositivo sostitutivo.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Cancellare i dati dai dischi dati sul dispositivo
> * Aprire un ticket di supporto per restituire il dispositivo
> * Impacchetta il dispositivo e pianifica un ritiro
> * Eliminare la risorsa nel portale di AzureDelete the resource in Azure portal
> * Ottenere un dispositivo sostitutivoGet a replacement device

## <a name="erase-data-from-the-device"></a>Cancellare i dati dal dispositivo

Per cancellare i dati dai dischi dati del dispositivo, è necessario reimpostare il dispositivo. È possibile reimpostare il dispositivo usando l'interfaccia utente Web locale o l'interfaccia di PowerShell.You can reset your device using the local web UI or the PowerShell interface.

Prima di reimpostare, creare una copia dei dati locali sul dispositivo, se necessario. È possibile copiare i dati dal dispositivo in un contenitore di Archiviazione di Azure.You can copy the data from the device to an Azure Storage container.

Per reimpostare il dispositivo utilizzando l'interfaccia utente Web locale, eseguire la procedura seguente.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > reimpostazione dispositivo**.
2. Selezionare **Reimposta dispositivo**.

    ![Reimpostare il dispositivo](media/data-box-edge-return-device/device-reset-1.png)

3. Quando viene richiesta la conferma, esaminare l'avviso e selezionare **Sì** per continuare.

    ![Confermare la reimpostazione](media/data-box-edge-return-device/device-reset-2.png)  

Il ripristino cancella i dati dai dischi dati del dispositivo. A seconda della quantità di dati sul dispositivo, questo processo richiede circa 30-40 minuti.

In alternativa, connettersi all'interfaccia di PowerShell del dispositivo e utilizzare il `Reset-HcsAppliance` cmdlet per cancellare i dati dai dischi dati. Per ulteriori informazioni, consultate [Reimpostare il dispositivo.](data-box-edge-connect-powershell-interface.md#reset-your-device)

> [!NOTE]
> - Se stai scambiando o aggiornando a un nuovo dispositivo, ti consigliamo di reimpostarlo solo dopo aver ricevuto il nuovo dispositivo.
> - La reimpostazione del dispositivo elimina solo tutti i dati locali dal dispositivo. I dati nel cloud non vengono eliminati e raccolgono [addebiti.](https://azure.microsoft.com/pricing/details/storage/) Questi dati devono essere eliminati separatamente usando uno strumento di gestione dell'archiviazione cloud come [Azure Storage Explorer.This data](https://azure.microsoft.com/features/storage-explorer/)needs to be deleted separately using a cloud storage management tool like Azure Storage Explorer .

## <a name="open-a-support-ticket"></a>Aprire un ticket di supporto

Per iniziare il processo di reso, attenersi alla seguente procedura.

1. Aprire un ticket di supporto con il supporto tecnico Microsoft che indica che si desidera restituire il dispositivo. Selezionare il tipo di problema come **Hardware perimetrale casella dati**.

    ![Aprire il ticket di supporto](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Un tecnico del supporto Microsoft ti contatterà. Fornire i dettagli di spedizione.
3. Se hai bisogno di una scatola di spedizione di ritorno, puoi richiederla. Risposta **Sì** alla domanda **È necessario una casella vuota per restituire**.


## <a name="schedule-a-pickup"></a>Pianificare un ritiro

1. Arrestare il dispositivo. Nell'interfaccia utente Web locale passare a **Manutenzione > Power settings** (Impostazioni di alimentazione).
2. Selezionare **Arresta il sistema**. Quando viene richiesta la conferma, fare clic su **Sì** per continuare. Per ulteriori informazioni, consultate [Gestire l'alimentazione.](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)
3. Scollegare i cavi di alimentazione e rimuovere tutti i cavi di rete dal dispositivo.
4. Preparare il pacco di spedizione usando la propria casella o la casella vuota ricevuta da Azure.Prepare the shipment package by using your own box or the empty box you received from Azure. Posizionare il dispositivo e i cavi di alimentazione forniti con il dispositivo nella confezione.
5. Fissare l'etichetta di spedizione ricevuta da Azure nel pacchetto.
6. Pianificare un ritiro con il corriere di zona. Se si restituisce il dispositivo negli Stati Uniti, il vettore potrebbe essere UPS o FedEx. Per pianificare un ritiro con UPS:

    1. Chiamare il servizio UPS locale (numero verde specifico del paese).
    2. Nella chiamata, citare il numero di tracciabilità della spedizione inversa come indicato sull'etichetta stampata.
    3. Se il numero di ricerca non è quotato, UPS richiederà di pagare un costo aggiuntivo durante il ritiro.

    Invece di programmare il ritiro, puoi anche lasciare il Data Box Edge nella posizione di consegna più vicina.

## <a name="delete-the-resource"></a>Eliminare la risorsa

Dopo che il dispositivo è stato ricevuto nel data center di Azure, il dispositivo viene controllato per verificare la presenza di danni o eventuali segni di manomissione.

- Se il dispositivo arriva intatto e in buona forma, il misuratore di fatturazione si arresta per tale risorsa. Il supporto tecnico Microsoft contatterà l'utente per confermare che il dispositivo è stato restituito. È quindi possibile eliminare la risorsa associata al dispositivo nel portale di Azure.You can then delete the resource associated with the device in the Azure portal.
- Se il dispositivo arriva significativamente danneggiato, possono essere applicate multe. Per informazioni dettagliate, vedere le [domande frequenti sul dispositivo smarrito o danneggiato](https://azure.microsoft.com/pricing/details/databox/edge/) e i Termini di servizio del [prodotto](https://www.microsoft.com/licensing/product-licensing/products).  


È possibile eliminare il dispositivo nel portale di Azure:You can delete the device in the Azure portal:
-   Dopo aver effettuato l'ordine e prima che il dispositivo venga preparato da Microsoft.
-   Dopo aver restituito il dispositivo a Microsoft, passa l'ispezione fisica nel data center di Azure e le chiamate al supporto tecnico Microsoft per confermare che il dispositivo è stato restituito.

Se il dispositivo è stato attivato in un altro abbonamento o posizione, Microsoft sposterà l'ordine nel nuovo abbonamento o nella nuova posizione entro un giorno lavorativo. Dopo aver spostato l'ordine, è possibile eliminare questa risorsa.


Eseguire la procedura seguente per eliminare il dispositivo e la risorsa nel portale di Azure.Take the following steps to delete the device and the resource in Azure portal.

1. Nel portale di Azure passare alla risorsa e quindi a **Panoramica**. Dalla barra dei comandi, selezionare **Elimina.**

    ![Selezionare Elimina](media/data-box-edge-return-device/delete-resource-1.png)

2. Nel pannello **Elimina dispositivo** digitare il nome del dispositivo da eliminare e selezionare **Elimina**.

    ![Conferma dell'eliminazione](media/data-box-edge-return-device/delete-resource-2.png)

Si riceve una notifica dopo che il dispositivo e la risorsa associata sono stati eliminati correttamente.

## <a name="get-a-replacement-device"></a>Ottenere un dispositivo sostitutivoGet a replacement device

Un dispositivo sostitutivo è necessario quando il dispositivo esistente presenta un errore hardware o richiede un aggiornamento. Quando il dispositivo presenta un problema hardware, attenersi alla seguente procedura:

1. [Aprire un ticket di supporto per il problema hardware](#open-a-support-ticket). Il supporto tecnico Microsoft determinerà che un'unità di sostituzione campi (FRU) non è disponibile per questa istanza o che il dispositivo necessita di un aggiornamento hardware. In entrambi i casi, il supporto ordinerà un dispositivo sostitutivo.
2. [Creare una nuova risorsa](data-box-edge-deploy-prep.md#create-a-new-resource) per il dispositivo sostitutivo. Assicurarsi di selezionare la casella di controllo **per l'accesso**a Dispositivo Data Box Edge . 
3. Dopo aver ricevuto un dispositivo sostitutivo, [installarlo](data-box-edge-deploy-install.md) e [attivarlo](data-box-edge-deploy-connect-setup-activate.md) con la nuova risorsa.
4. Seguire tutti i passaggi per restituire il dispositivo originale:
    1. Aprire un altro ticket per restituire il dispositivo originale.
    2. [Cancellare i dati sul dispositivo](#erase-data-from-the-device).
    3. [Programmare un ritiro](#schedule-a-pickup).
    5. [Eliminare la risorsa](#delete-the-resource) associata al dispositivo restituito.



## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire la larghezza di banda](data-box-edge-manage-bandwidth-schedules.md).
