---
title: Gestione di utenti in Azure Data Box Gateway | Microsoft Docs
description: Descrive come usare il portale di Azure per gestire gli utenti in Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 898049fb8c24529db03300ed6dcc2fef43b38ab1
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499931"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-gateway"></a>Usare il portale di Azure per gestire gli utenti in Azure Data Box Gateway 

Questo articolo descrive come gestire gli utenti in Azure Data Box Gateway. È possibile gestire Azure Data Box Gateway dal portale di Azure o dall'interfaccia utente Web locale. Usare il portale di Azure per aggiungere, modificare o eliminare utenti.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Aggiungere un utente
> * Modificare un utente
> * Eliminare un utente 

## <a name="about-users"></a>Informazioni sugli utenti

Gli utenti possono accedere in sola lettura o con privilegi completi. Come indicano i nomi, gli utenti di sola lettura possono visualizzare solo i dati della condivisione. Gli utenti con privilegi completi possono leggere, modificare o eliminare i dati della condivisione e scrivere in tali condivisioni. 

 - **Utente con privilegi completi**: un utente locale con accesso completo. 
 - **Utente di sola lettura**. un utente locale con accesso di sola lettura. Questi utenti sono associati alle condivisioni che consentono operazioni di sola lettura.

Le autorizzazioni dell'utente vengono prima di tutto definite durante la creazione contemporanea di utente e condivisione. Dopo aver definito le autorizzazioni associate a un utente, è possibile modificarle con Esplora file. 


## <a name="add-a-user"></a>Aggiungere un utente

Seguire questa procedura nel portale di Azure per aggiungere un utente.

1. Nel portale di Azure, selezionare la risorsa Data Box Gateway e quindi passare a **Panoramica**. Fare clic su **+ Aggiungi utente** sulla barra dei comandi.

    ![Fare clic su Aggiungi utente](media/data-box-gateway-manage-users/add-user-1.png)

2. Specificare nome utente e password per l'utente da aggiungere. Confermare la password e fare clic su **Aggiungi**.

    ![Fare clic su Aggiungi utente](media/data-box-gateway-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Questi utenti sono riservati dal sistema e non devono essere usati: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Dopo l'avvio e il completamento della creazione degli utenti si riceve una notifica. Dopo aver creato l'utente, dalla barra dei comandi, fare clic su **Aggiorna** per visualizzare l'elenco aggiornato degli utenti.


## <a name="modify-user"></a>Modificare un utente

È possibile cambiare la password associata a un utente dopo averlo creato. Selezionare e fare clic nell'elenco di utenti. Indicare e confermare la nuova password. Salvare le modifiche.
 
![Modificare un utente](media/data-box-gateway-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Eliminare un utente

Eseguire i passaggi seguenti nel portale di Azure per eliminare un utente.

1. Selezionare e fare clic su un utente dall'elenco, quindi fare clic su **Elimina**.  

   ![Eliminare un utente](media/data-box-gateway-manage-users/delete-user-1.png)

2. Quando richiesto, confermare l'eliminazione. 

   ![Eliminare un utente](media/data-box-gateway-manage-users/delete-user-2.png)

L'elenco di utenti viene aggiornato per riflettere l'utente eliminato.

![Eliminare un utente](media/data-box-gateway-manage-users/delete-user-3.png)


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire la larghezza di banda](data-box-gateway-manage-bandwidth-schedules.md).
