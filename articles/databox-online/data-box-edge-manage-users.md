---
title: Gestire gli utenti in Azure Data Box Edge | Microsoft Docs
description: Descrive come usare il portale di Azure per gestire gli utenti in Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 443ed983a0eec5dfd8f7a917fbc1440cd66c3db3
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78946132"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>Usare il portale di Azure per gestire gli utenti in Azure Data Box Edge

Questo articolo descrive come gestire gli utenti in Azure Data Box Edge. È possibile gestire Azure Data Box Edge dal portale di Azure o dall'interfaccia utente Web locale. Usare il portale di Azure per aggiungere, modificare o eliminare utenti.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Aggiungere un utente
> * Modificare un utente
> * Eliminare un utente

## <a name="about-users"></a>Informazioni sugli utenti

Gli utenti possono accedere in sola lettura o con privilegi completi. Come indicano i nomi, gli utenti di sola lettura possono visualizzare solo i dati della condivisione. Gli utenti con privilegi completi possono leggere, modificare o eliminare i dati della condivisione e scrivere in tali condivisioni.

 - **Utente con privilegi completi**: un utente locale con accesso completo.
 - **Utente di sola lettura**. un utente locale con accesso di sola lettura. Questi utenti sono associati alle condivisioni che consentono operazioni di sola lettura.

Le autorizzazioni dell'utente vengono prima di tutto definite durante la creazione contemporanea di utente e condivisione. La modifica delle autorizzazioni a livello di condivisione non è attualmente supportata.

## <a name="add-a-user"></a>Aggiungere un utente

Seguire questa procedura nel portale di Azure per aggiungere un utente.

1. Nel portale di Azure passare alla risorsa Data Box Edge e quindi a **Panoramica > Utenti**. Selezionare **+ Aggiungi utente** sulla barra dei comandi.

    ![Selezionare Aggiungi utente](media/data-box-edge-manage-users/add-user-1.png)

2. Specificare nome utente e password per l'utente da aggiungere. Confermare la password e selezionare **Aggiungi**.

    ![Specificare nome utente e password](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Questi utenti sono riservati dal sistema e non deve essere usati: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Dopo l'avvio e il completamento della creazione dell'utente viene visualizzata una notifica. Dopo aver creato l'utente, dalla barra dei comandi, selezionare **Aggiorna** per visualizzare l'elenco aggiornato degli utenti.


## <a name="modify-user"></a>Modificare un utente

È possibile cambiare la password associata a un utente dopo averlo creato. Selezionare dall'elenco degli utenti. Immettere e confermare la nuova password. Salvare le modifiche.
 
![Modificare un utente](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Eliminare un utente

Eseguire i passaggi seguenti nel portale di Azure per eliminare un utente.


1. Nel portale di Azure passare alla risorsa Data Box Edge e quindi a **Panoramica > Utenti**.

    ![Selezionare l'utente da eliminare](media/data-box-edge-manage-users/delete-user-1.png)

2. Selezionare un utente dall'elenco e quindi fare clic su **Elimina**.  

   ![Selezionare Elimina](media/data-box-edge-manage-users/delete-user-2.png)

3. Quando richiesto, confermare l'eliminazione. 

   ![Conferma dell'eliminazione](media/data-box-edge-manage-users/delete-user-3.png)

L'elenco di utenti viene aggiornato per riflettere l'utente eliminato.

![Elenco aggiornato degli utenti](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire la larghezza di banda](data-box-edge-manage-bandwidth-schedules.md).
