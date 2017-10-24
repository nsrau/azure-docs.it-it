---
title: Assegnare un accesso in Gestione dei costi di Azure | Microsoft Docs
description: "Assegnare un accesso ai dati di Gestione dei costi con account utente che definiscono i livelli di accesso alle entità."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 09/19/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: aca9f0b3320f298987cf39172fd4b043326a2f4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="assign-access-to-cost-management-data"></a>Assegnare l'accesso ai dati di Gestione dei costi

L'accesso ai dati di Gestione dei costi è fornito per utente o gestione di entità. Gli account utente Cloudyn determinano l'accesso a *entità* e le funzioni amministrative. Esistono due tipi di accesso: amministratore e utente. A meno che non sia modificato per ogni utente, l'accesso amministrativo consente all'utente di usare senza restrizioni tutte le funzioni del portale Cloudyn, tra cui: gestione degli utenti, la gestione degli elenchi di destinatari e accesso all'entità radice per tutti i dati di entità. L'accesso utente consente agli utenti finali di visualizzare e creare report usando l'accesso di cui dispongono ai dati di entità.

Le entità vengono usate in modo da riflettere la struttura gerarchica dell'organizzazione e consentono di identificare reparti, divisioni e team dell'organizzazione in Cloudyn. La gerarchia delle entità consente di verificare in modo accurato la spesa per le entità.

Al momento della registrazione dell'account o del contratto di Azure, in Cloudyn è stato creato un account con autorizzazione di amministratore, in modo da poter eseguire tutti i passaggi in questa esercitazione. Questa esercitazione illustra l'accesso ai dati di gestione dei costi, incluso la gestione utente e la gestione di entità. Si apprenderà come:

> [!div class="checklist"]
> * Creare un utente con accesso amministrativo
> * Creare un utente con accesso utente
> * Creare entità



## <a name="create-a-user-with-admin-access"></a>Creare un utente con accesso amministrativo

Anche se si dispone già di un accesso amministrativo, lo stesso potrebbe essere necessario anche per i colleghi all'interno dell'organizzazione. Nel portale di Cloudyn, fare clic sul simbolo a forma di ingranaggio in alto a destra e selezionare **Gestione utenti**. Fare clic su **Aggiunta nuovo utente** per aggiungere un nuovo utente.

Immettere le informazioni necessarie relative all'utente. È possibile lasciare vuoto il campo della password in modo che l'utente possa impostare una nuova password al primo accesso. Selezionando **Notify user by email** (Notifica utente tramite posta elettronica), all'utente verrà inviato tramite posta elettronica da Cloudyn un collegamento con informazioni di accesso. Scegliere le autorizzazioni per consentire la Gestione utenti in modo che l'utente possa creare e modificare altri utenti. La gestione degli elenchi dei destinatari consente all'utente di modificare gli elenchi di destinatari.

In **User has admin access** (L'utente dispone di un accesso amministrativo), viene selezionata l'entità radice dell'organizzazione. Lasciare la radice selezionata e salvare le informazioni utente. La selezione dell'entità radice consente all'utente di disporre dell'autorizzazione di amministratore non solo per l'entità radice nell'albero, ma anche per tutte le entità che si trovano sotto di essa.  
  ![aggiungere un nuovo utente con accesso amministrativo](.\media\tutorial-user-access\new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Creare un utente con accesso utente
Gli utenti tipici che richiedono l'accesso a dati di gestione dei costi come dashboard e report devono disporre di un accesso utente per visualizzarli. Creare un nuovo utente con accesso utente simile a quello creato con accesso amministrativo, con le differenze seguenti:

- Deselezionare **Allow User Management** (Consenti gestione utente), **Allow Recipient lists Management** (Consenti gestione elenchi di utenti) e deselezionare tutto nell'elenco **User has admin access** (L'utente dispone dell'accesso amministrativo).
- Selezionare le entità a cui l'utente deve accedere nell'elenco **User has user access** (L'utente dispone di un accesso utente).
- È anche possibile consentire all'amministratore di accedere a entità specifiche, in base alle esigenze.

![aggiungere un nuovo utente con accesso utente](.\media\tutorial-user-access\new-user-access.png)

## <a name="create-entities"></a>Creare entità

Quando si definisce la gerarchia delle entità di costi, una procedura consigliata consiste nell'identificare la struttura dell'organizzazione.

Mentre si compila la struttura ad albero, prendere in considerazione la modalità preferibile o necessaria per visualizzare i relativi costi isolati per business unit, centri di costo, ambienti e reparti vendite. L'albero delle entità in Cloudyn è flessibile data l'ereditarietà delle entità. Le singole sottoscrizioni per gli account cloud sono collegate a entità specifiche. Le entità sono quindi multi-tenant. Tramite le entità, è possibile assegnare a utenti specifici l'accesso al solo segmento dell'azienda pertinente. In questo modo i dati vengono mantenuti isolati, anche attraverso aree di grandi dimensioni di un'azienda, come le società affiliate. L'isolamento dei dati semplifica anche la governance.  

Al momento della registrazione del contratto o dell'account di Azure con Cloudyn, i dati di risorse di Azure, inclusi l'uso, le prestazioni, la fatturazione e i dati di tag delle sottoscrizioni sono stati copiati nel proprio account Cloudyn. Tuttavia, la struttura ad albero delle entità deve essere creata manualmente. Se non si è eseguito la registrazione in Azure Resource Manager, sul portale di Cloudyn saranno disponibili solo i dati di fatturazione e alcuni report di asset.

Nel portale di Cloudyn, fare clic su **Impostazioni** in alto a destra e selezionare **Cloud Accounts** (Account cloud). Si inizierà con una singola entità (radice), per poi compilare la struttura ad albero a partire da essa. Di seguito è riportato un esempio di una gerarchia di entità che potrebbe somigliare a molte organizzazioni IT dopo avere completato la struttura ad albero:

![struttura ad albero di entità](.\media\tutorial-user-access\entity-tree.png)

Accanto a **Entità**, fare clic su **Aggiungi entità**. Immettere le informazioni sulla persona o sul reparto che si desidera aggiungere. I campi **Nome e cognome** e **Posta elettronica** non devono corrispondere a utenti esistenti. Per visualizzare un elenco di livelli di accesso, cercare *Aggiunta di un'entità* nella Guida.

![aggiungi entità](.\media\tutorial-user-access\add-entity.png)

Al termine, **salvare** l'entità.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un utente con accesso amministrativo
> * Creare un utente con accesso utente
> * Creare entità

Passare alla prossima esercitazione per imparare come prevedere la spesa usando i dati cronologici.

> [!div class="nextstepaction"]
> [Prevedere la spesa futura](tutorial-forecast-spending.md)
