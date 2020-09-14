---
title: Spedizione autogestita di Microsoft Azure Data Box Disk | Microsoft Docs
description: Descrive il flusso di lavoro della spedizione autogestita per i dispositivi Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/20/2020
ms.author: alkohli
ms.openlocfilehash: 7b069d1bf16e8cbf4dfc245fbb3c66863cd0b994
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055573"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Usare la spedizione autogestita per Azure Data Box Disk nel portale di Azure

Questo articolo descrive le attività di spedizione autogestita per l'ordine, il ritiro e la consegna di un dispositivo Azure Data Box Disk. È possibile gestire Data Box Disk usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

La spedizione autogestita è disponibile come opzione quando si [ordina Azure Data Box Disk](data-box-disk-deploy-ordered.md). La spedizione autogestita è disponibile solo nelle aree geografiche seguenti:

* US Gov
* Europa occidentale
* Giappone
* Singapore
* Corea del Sud
* Sudafrica
* India (anteprima)

## <a name="use-self-managed-shipping"></a>Usare la spedizione autogestita

Quando si effettua l'ordine di Data Box Disk, è possibile scegliere l'opzione di spedizione autogestita.

1. Nell'ordine di Azure Data Box Disk selezionare **+ Aggiungi indirizzo di spedizione** in **Dettagli contatto**.

   ![Spedizione autogestita](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Quando si sceglie il tipo di spedizione, selezionare l'opzione **Spedizione autogestita**. Questa opzione è disponibile solo se ci si trova in una delle aree supportate, come descritto nei prerequisiti.

3. Dopo aver indicato l'indirizzo di spedizione, sarà necessario convalidarlo e completare l'ordine.

   ![Spedizione autogestita](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Una volta che il dispositivo è stato preparato ed è stata ricevuta una notifica di posta elettronica, è possibile pianificare un pickup. Nell'ordine di Azure Data Box Disk passare a **Panoramica**, quindi selezionare **Pianifica ritiro**.

   ![Ordine di un dispositivo Data Box per il ritiro](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Seguire le istruzioni riportate in **Pianifica il ritiro per Azure**. Per poter ottenere il codice di autorizzazione, è necessario inviare un messaggio di posta elettronica a [adbops@microsoft.com](mailto:adbops@microsoft.com) per pianificare il ritiro del dispositivo dal data center dell'area.

   ![Pianificare il ritiro](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Dopo aver pianificato il ritiro del dispositivo, sarà possibile visualizzare il codice di autorizzazione nel riquadro **Pianifica il ritiro per Azure**.

   ![Visualizzazione del codice di autorizzazione](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Prendere nota del **codice di autorizzazione**.

   In base ai requisiti di sicurezza, al momento della pianificazione del prelievo, è necessario presentare il nome della persona che arriverà per la selezione.

   È anche necessario specificare i dettagli relativi a chi accederà al data center per il ritiro. L'utente o il punto di contatto deve esibire un documento d'identità valido che verrà convalidato presso il data center.

   La persona che ritira il dispositivo deve avere anche il **codice di autorizzazione**. Il codice di autorizzazione è univoco per un prelievo o un rilascio e viene convalidato nel Data Center.

7. L'ordine assume automaticamente lo stato **Ritirato** dopo che il dispositivo è stato ritirato dal data center.

   ![Ritirato](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. Dopo il ritiro del dispositivo, è possibile copiare i dati nel Data Box Disk presso il proprio sito. Dopo aver completato la copia dei dati, è possibile preparare la spedizione del Data Box Disk.

   Dopo aver terminato la copia dei dati, sarà necessario contattare gli operatori per pianificare un appuntamento per la consegna. Sarà necessario condividere i dettagli della persona che si presenterà al data center per consegnare i dischi. Il data center dovrà anche verificare il codice di autorizzazione al momento della consegna. Il codice di autorizzazione per la consegna sarà disponibile nel portale di Azure in **Pianifica la consegna**.

   > [!NOTE]
   > Non condividere il codice di autorizzazione tramite posta elettronica. Il codice deve essere verificato solo nel data center durante la consegna.

9. Se è stato ricevuto un appuntamento per la consegna, l'ordine deve avere lo stato **Pronto per la ricezione presso il data center di Azure** nel portale di Azure.

   ![Visualizzazione del codice di autorizzazione](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. Dopo che il documento e il codice di autorizzazione sono stati verificati e il dispositivo è stato consegnato presso il data center, lo stato dell'ordine sarà **Ricevuto**.

    ![Ricezione completata](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Dopo aver ricevuto il dispositivo, la copia dei dati continuerà. Al termine della copia, l'ordine è completato.

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Distribuire Azure Data Box Disk usando il portale di Azure](data-box-disk-quickstart-portal.md)
