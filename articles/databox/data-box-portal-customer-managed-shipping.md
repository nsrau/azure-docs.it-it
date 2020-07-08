---
title: Spedizione autogestita di Microsoft Azure Data Box | Microsoft Docs
description: Descrive il flusso di lavoro di spedizione autogestita per i dispositivi Azure Data Box
services: databox
author: priestlg
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/20/2020
ms.author: v-grpr
ms.openlocfilehash: 99c7021744467484656c52cf72378c6f553c3913
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84608588"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Usare la spedizione autogestita per Azure Data Box nel portale di Azure

Questo articolo descrive le attività di spedizione autogestita per l'ordine, il ritiro e la consegna di un dispositivo Azure Data Box. È possibile gestire il dispositivo Data Box usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

La spedizione autogestita è disponibile come opzione quando si [ordina Azure Data Box](data-box-deploy-ordered.md). La spedizione autogestita è disponibile solo nelle regioni seguenti:

* US Gov
* Europa occidentale
* Giappone
* Singapore
* Corea del Sud

## <a name="use-self-managed-shipping"></a>Usare la spedizione autogestita

Quando si effettua l'ordine di Data Box, è possibile scegliere l'opzione di spedizione autogestita.

1. Nell'ordine di Azure Data Box, nei **dettagli del contatto** selezionare **+ Aggiungi indirizzo di spedizione**.
   ![Spedizione autogestita](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Quando si sceglie il tipo di spedizione, selezionare l'opzione **Spedizione autogestita**. Questa opzione è disponibile solo se si è in una delle regioni supportate, come descritto nei prerequisiti.

3. Dopo aver indicato il proprio indirizzo di spedizione, sarà necessario convalidarlo e completare l'ordine.
   ![Spedizione autogestita](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Una volta che il dispositivo è stato preparato e si riceve una notifica tramite posta elettronica, è possibile pianificare un pickup.

   Nell'ordine di Azure Data Box passare a **Panoramica** e quindi selezionare **Pianifica ritiro**.

   ![Ordine di un dispositivo Data Box per il ritiro](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Seguire le istruzioni riportate in **Pianifica il ritiro per Azure**.

   Per poter ottenere il codice di autorizzazione, è necessario inviare un messaggio di posta elettronica a [adbops@microsoft.com](mailto:adbops@microsoft.com) per pianificare il ritiro del dispositivo dal data center regionale.

   ![Pianificare il ritiro](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. Dopo aver pianificato il ritiro del dispositivo, sarà possibile visualizzare il codice di autorizzazione del dispositivo nel riquadro **Pianifica il ritiro per Azure**.

   ![Visualizzazione del codice di autorizzazione](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Prendere nota del **codice di autorizzazione**. In base ai requisiti di sicurezza, al momento della pianificazione del prelievo, è necessario presentare il nome della persona che arriverebbe per la selezione.

   È anche necessario specificare i dettagli relativi a chi accederà al data center per il ritiro. L'utente o il punto di contatto deve esibire un ID con foto approvato dall'ente pubblico che verrà convalidato presso il data center.

   Inoltre, la persona che ritira il dispositivo deve avere anche il **codice di autorizzazione**. Il codice di autorizzazione viene convalidato al momento del ritiro nel data center.

7. L'ordine passa automaticamente allo stato **Ritirato** dopo che il dispositivo è stato ritirato dal data center.

    ![Ritirato](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. Dopo il ritiro del dispositivo, copiare i dati nel Data Box presso il proprio sito. Dopo aver completato la copia dei dati, è possibile preparare la spedizione del Data Box. Per altre informazioni, vedere [Preparare per la spedizione](data-box-deploy-picked-up.md#prepare-to-ship).

   Il passaggio **Preparare per la spedizione** deve essere completato senza errori critici, in caso contrario sarà necessario eseguirlo di nuovo dopo aver apportato le opportune correzioni. Al termine della preparazione per la spedizione, è possibile visualizzare il codice di autorizzazione per la consegna nell'interfaccia utente locale del dispositivo.

   > [!NOTE]
   > Non condividere il codice di autorizzazione via posta elettronica. Il codice deve essere verificato solo nel data center durante la consegna.

9. Se è stato ricevuto un appuntamento per la consegna, l'ordine deve essere **Pronto per la ricezione presso il data center di Azure** nel portale di Azure. Per restituire il dispositivo, seguire le istruzioni in **Pianificare la consegna**.

   ![Visualizzazione del codice di autorizzazione](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. Dopo che l'ID e il codice di autorizzazione sono stati verificati e il dispositivo è stato consegnato presso il data center, lo stato dell'ordine deve essere **Ricevuto**.

    ![Ricezione completata](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. Ricevuto il dispositivo, la copia dei dati continuerà. Al termine della copia, l'ordine è completato.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Data Box](data-box-quickstart-portal.md)
