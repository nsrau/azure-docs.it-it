---
title: Gestione degli utenti in portale Cloud Partner | Azure Marketplace
description: Gestione degli utenti nel portale per Cloud Partner
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
manager: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: pabutler
ms.openlocfilehash: 4dc9fdf94ba6311f1b59c61ec686628a728e2510
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826627"
---
# <a name="managing-users-on-the-cloud-partner-portal"></a>Gestione degli utenti nella portale Cloud Partner

Il [portale cloud partner](https://cloudpartner.azure.com) consente di aggiungere utenti con accesso basato sui ruoli alle offerte di macchine virtuali. Rende più semplice gestire l'accesso e le autorizzazioni mentre più utenti stanno lavorando sulle offerte.

## <a name="add-users-and-assign-roles"></a>Aggiungere utenti e assegnare ruoli 

Usare la procedura seguente per aggiungere utenti e assegnare ruoli per la pubblicazione dell'offerta.

1. Fare clic sulla scheda **utenti** nel riquadro di spostamento a sinistra.

    ![Scheda Utenti](./media/userstab.png)


2. Fare clic su **Add User**.

    ![Selezionare Aggiungi utente](./media/adduser.png)


3. Digitare l'indirizzo di posta elettronica dell'utente e selezionare un'assegnazione di ruolo.  È possibile aggiungere il nuovo utente come "proprietario" o "collaboratore".

    **Indirizzo di posta elettronica**: aggiungere gli indirizzi di posta elettronica dei colleghi che lavoreranno sulla pubblicazione dell'offerta. Sono supportati gli account Microsoft (Outlook, Hotmail e Live) e gli ID organizzazione.

    - Aggiungere un "gruppo di sicurezza/alias di posta elettronica del team/gruppo" per evitare che l'utente lasci l'organizzazione.
    - Assicurarsi che gli ID di posta elettronica specificati nel portale Cloud Partner vengano monitorati per le comunicazioni da Microsoft.
    
    ![Assegna ruolo](./media/assignrole.png)

    **Ruolo**: fare riferimento alla tabella riportata di seguito per identificare il tipo di ruolo utente appropriato.

    ![Livello dei ruoli](./media/roleaccesslevel.png)

    Solo i proprietari possono accedere ai pagamenti di Azure e alle schede dei clienti di Azure in [informazioni dettagliate del venditore](../../cloud-partner-portal-orig/si-getting-started.md).


4. Selezionare il nome del server di pubblicazione che si desidera aggiungere al nuovo utente, quindi fare clic su Aggiungi per completare l'aggiunta dell'utente.

    > [!NOTE]
    > I "nomi degli autori" per i quali viene aggiunto un messaggio di posta elettronica come proprietario o collaboratore vengono visualizzati nell'elenco a discesa del **server di pubblicazione** per la selezione.


    ![Assegnazione ruolo - selezionare l'editore](./media/assignselectpublisher.png)


## <a name="delete-an-existing-user"></a>Elimina un utente esistente

Per rimuovere un utente esistente, cercare il messaggio di posta elettronica nella scheda **utenti** e quindi premere il pulsante **Elimina** sul lato destro.


## <a name="next-steps"></a>Passaggi successivi

È anche possibile usare il portale per [gestire i profili di pubblicazione](./cpp-manage-publisher-profile.md).
