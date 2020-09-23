---
title: Gestione utenti di Azure Stack Edge Pro | Microsoft Docs
description: Viene descritto come usare la portale di Azure per gestire gli utenti nel Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 3fb82e95d9d7dcfdf59bd05f91beff8e1184955e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904388"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-azure-stack-edge-pro"></a>Usare il portale di Azure per gestire gli utenti in Azure Azure Stack Edge Pro

Questo articolo descrive come gestire gli utenti in Azure Stack Edge Pro. È possibile gestire il Azure Stack Edge Pro tramite la portale di Azure o tramite l'interfaccia utente Web locale. Usare il portale di Azure per aggiungere, modificare o eliminare utenti.

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

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **panoramica > utenti**. Selezionare **+ Aggiungi utente** sulla barra dei comandi.

    ![Selezionare Aggiungi utente](media/azure-stack-edge-manage-users/add-user-1.png)

2. Specificare nome utente e password per l'utente da aggiungere. Confermare la password e selezionare **Aggiungi**.

    ![Specificare nome utente e password](media/azure-stack-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Questi utenti sono riservati dal sistema e non deve essere usati: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Dopo l'avvio e il completamento della creazione dell'utente viene visualizzata una notifica. Dopo aver creato l'utente, dalla barra dei comandi, selezionare **Aggiorna** per visualizzare l'elenco aggiornato degli utenti.


## <a name="modify-user"></a>Modificare un utente

È possibile cambiare la password associata a un utente dopo averlo creato. Selezionare dall'elenco degli utenti. Immettere e confermare la nuova password. Salvare le modifiche.
 
![Modificare un utente](media/azure-stack-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Eliminare un utente

Eseguire i passaggi seguenti nel portale di Azure per eliminare un utente.


1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **panoramica > utenti**.

    ![Selezionare l'utente da eliminare](media/azure-stack-edge-manage-users/delete-user-1.png)

2. Selezionare un utente dall'elenco e quindi fare clic su **Elimina**.  

   ![Selezionare Elimina](media/azure-stack-edge-manage-users/delete-user-2.png)

3. Quando richiesto, confermare l'eliminazione. 

   ![Conferma dell'eliminazione](media/azure-stack-edge-manage-users/delete-user-3.png)

L'elenco di utenti viene aggiornato per riflettere l'utente eliminato.

![Elenco aggiornato degli utenti](media/azure-stack-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire la larghezza di banda](azure-stack-edge-manage-bandwidth-schedules.md).
