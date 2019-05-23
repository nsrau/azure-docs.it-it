---
title: 'Esercitazione: Assegnare un accesso in Azure con Cloudyn | Microsoft Docs'
description: In questa esercitazione si apprenderà come assegnare un accesso ai dati di Cloudyn con account utente che definiscono i livelli di accesso alle entità.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: 4b309d54fae63f16574bb3799d77adfd89f91a57
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967089"
---
# <a name="tutorial-assign-access-to-cloudyn-data"></a>Esercitazione: Assegnare un accesso ai dati di Cloudyn

L'accesso ai dati di Cloudyn è fornito per utente o gestione di entità. Gli account utente Cloudyn determinano l'accesso a *entità* e le funzioni amministrative. Esistono due tipi di accesso: amministratore e utente. A meno che non sia modificato per ogni utente, l'accesso amministrativo consente all'utente di usare senza restrizioni tutte le funzioni del portale Cloudyn, tra cui: gestione degli utenti, la gestione degli elenchi di destinatari e accesso all'entità radice per tutti i dati di entità. L'accesso utente consente agli utenti finali di visualizzare e creare report usando l'accesso di cui dispongono ai dati di entità.

Le entità vengono usate in modo da riflettere la struttura gerarchica dell'organizzazione e consentono di identificare reparti, divisioni e team dell'organizzazione in Cloudyn. La gerarchia delle entità consente di verificare in modo accurato la spesa per le entità.

Al momento della registrazione dell'account o del contratto di Azure, in Cloudyn è stato creato un account con autorizzazione di amministratore, in modo da poter eseguire tutti i passaggi in questa esercitazione. Questa esercitazione illustra l'accesso ai dati di Cloudyn, incluse la gestione utente e la gestione di entità. Si apprenderà come:

> [!div class="checklist"]
> * Creare un utente con accesso amministrativo
> * Creare un utente con accesso utente
> * Eliminare un utente
> * Eliminare o esportare i dati personali
> * Creare e gestire le entità


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

- È necessario disporre di un account Azure.
- È necessario disporre di una registrazione di valutazione o una sottoscrizione a pagamento per Cloudyn.

## <a name="create-a-user-with-admin-access"></a>Creare un utente con accesso amministrativo

Anche se si dispone già di un accesso amministrativo, lo stesso potrebbe essere necessario anche per i colleghi all'interno dell'organizzazione. Nel portale di Cloudyn, fare clic sul simbolo a forma di ingranaggio in alto a destra e selezionare **Gestione utenti**. Fare clic su **Aggiunta nuovo utente** per aggiungere un nuovo utente.

Immettere le informazioni necessarie relative all'utente. L'**ID di accesso** deve essere un indirizzo di posta elettronica valido. Scegliere le autorizzazioni per consentire la Gestione utenti in modo che l'utente possa creare e modificare altri utenti. La gestione degli elenchi di destinatari consente all'utente di modificare gli elenchi di destinatari. Selezionando **Notify user by email** (Notifica utente tramite posta elettronica), all'utente verrà inviato tramite posta elettronica da Cloudyn un collegamento con informazioni di accesso. L'utente imposta una password al primo accesso.

In **User has admin access** (L'utente dispone di un accesso amministrativo), viene selezionata l'entità radice dell'organizzazione. Lasciare la radice selezionata e salvare le informazioni utente. La selezione dell'entità radice consente all'utente di disporre dell'autorizzazione di amministratore non solo per l'entità radice nell'albero, ma anche per tutte le entità che si trovano sotto di essa.  
  ![Esempio che mostra l'accesso amministrativo nella casella Aggiunta nuovo utente](./media/tutorial-user-access/new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Creare un utente con accesso utente
Gli utenti tipici che richiedono l'accesso a dati di Cloudyn, come dashboard e report, devono disporre di un accesso utente per visualizzarli. Creare un nuovo utente con accesso utente simile a quello creato con accesso amministrativo, con le differenze seguenti:

- Deselezionare **Allow User Management** (Consenti gestione utente), **Allow Recipient lists Management** (Consenti gestione elenchi di utenti) e deselezionare tutto nell'elenco **User has admin access** (L'utente dispone dell'accesso amministrativo).
- Selezionare le entità a cui l'utente deve accedere nell'elenco **User has user access** (L'utente dispone di un accesso utente).
- È anche possibile consentire all'amministratore di accedere a entità specifiche, in base alle esigenze.

![Esempio che mostra l'accesso utente nella casella Aggiunta nuovo utente](./media/tutorial-user-access/new-user-access.png)

Per guardare un video di esercitazione sull'aggiunta di utenti, vedere [Aggiungere utenti in Cloudyn](https://youtu.be/Nzn7GLahx30).

## <a name="delete-a-user"></a>Eliminare un utente

Quando si elimina un utente, tutte le entità a cui ha accesso l'utente rimangono intatte. I report *personali* salvati vengono rimossi quando l'utente viene eliminato. I report *pubblici* salvati creati dall'utente non vengono eliminati.

Non è possibile rimuovere se stessi come utente.

> [!WARNING]
> Quando si elimina un utente, non può essere ripristinato.

1.  Nel portale di Cloudyn fare clic sul simbolo a forma di ingranaggio in alto a destra e quindi selezionare **Gestione utenti**.
2.  Nell'elenco di utenti selezionare l'utente che si vuole eliminare e quindi fare clic su **Elimina utente** (simbolo di Cestino).
3.  Nella finestra Elimina utente fare clic su **Sì** e quindi fare clic su **OK**.


## <a name="delete-or-export-personal-data"></a>Eliminare o esportare i dati personali

Se si vogliono eliminare o esportare i dati personali da Cloudyn, è necessario creare un ticket di supporto. Quando viene creato il ticket di supporto, funge da richiesta formale, ovvero Richiesta del soggetto interessato. Microsoft prenderà quindi velocemente misure per rimuovere l'account ed eliminare eventuali dati del cliente o personali. Per informazioni su come è possibile richiedere l'eliminazione o l'esportazione dei dati, vedere [Data Subject Requests of Cloudyn Data](https://www.cloudyn.com/cloudyn-gdpr-requests) (Richieste del soggetto interessato per i dati Cloudyn).

## <a name="create-and-manage-entities"></a>Creare e gestire le entità

Quando si definisce la gerarchia delle entità di costi, una procedura consigliata consiste nell'identificare la struttura dell'organizzazione. Le entità consentono di segmentare la spesa per singoli account o sottoscrizioni. Per creare gruppi logici per gestire e tenere traccia della spesa, è necessario creare delle entità di costo. Mentre si compila la struttura ad albero, prendere in considerazione la modalità preferibile o necessaria per visualizzare i relativi costi isolati per business unit, centri di costo, ambienti e reparti vendite. L'albero delle entità in Cloudyn è flessibile data l'ereditarietà delle entità.

Le singole sottoscrizioni per gli account cloud sono collegate a entità specifiche. È possibile associare un'entità a una sottoscrizione o a un account del provider del servizio cloud. Le entità sono quindi multi-tenant. Tramite le entità, è possibile assegnare a utenti specifici l'accesso al solo segmento dell'azienda pertinente. In questo modo i dati vengono mantenuti isolati, anche attraverso aree di grandi dimensioni di un'azienda, come le società affiliate. L'isolamento dei dati semplifica anche la governance.  

Al momento della registrazione del contratto o dell'account di Azure con Cloudyn, i dati di risorse di Azure, inclusi l'uso, le prestazioni, la fatturazione e i dati di tag delle sottoscrizioni sono stati copiati nel proprio account Cloudyn. Tuttavia, la struttura ad albero delle entità deve essere creata manualmente. Se non si è eseguito la registrazione in Azure Resource Manager, sul portale di Cloudyn saranno disponibili solo i dati di fatturazione e alcuni report di asset.

Nel portale di Cloudyn fare clic sul simbolo a forma di ingranaggio in alto a destra e selezionare **Cloud Accounts** (Account cloud). Si inizierà con una singola entità (radice), per poi compilare la struttura ad albero a partire da essa. Di seguito è riportato un esempio di una gerarchia di entità che potrebbe somigliare a molte organizzazioni IT dopo avere completato la struttura ad albero:

![Esempio di un albero delle entità nella pagina di gestione degli account](./media/tutorial-user-access/entity-tree.png)

Accanto a **Entità**, fare clic su **Aggiungi entità**. Immettere le informazioni sulla persona o sul reparto che si desidera aggiungere. I campi **Nome e cognome** e **Posta elettronica** non devono corrispondere a utenti esistenti. Per visualizzare un elenco di livelli di accesso, cercare *Aggiunta di un'entità* nella Guida.

![Esempio che illustra il nome entità e i livelli di accesso nella casella Aggiungi entità](./media/tutorial-user-access/add-entity.png)

Al termine, **salvare** l'entità.

### <a name="entity-access-levels"></a>Livelli di accesso alle entità

I livelli di accesso alle entità insieme all'accesso dell'utente consentono di definire il tipo di azioni disponibili nel portale di Cloudyn.

- **Enterprise**: consente di creare e gestire le entità di costo figlio.
- **Enterprise + Cost Allocation** (Enterprise + Allocazione costi): consente di creare e gestire le entità di costo figlio inclusa l'allocazione dei costi per gli account consolidati.
- **Enterprise, Cost based on parent cost allocation** (Enterprise, Costo basato su allocazione costi padre): consente di creare e gestire le entità di costo figlio. I costi per l'account sono basati sul modello di allocazione costi dell'elemento padre.
- **Custom Dashboards only** (Solo dashboard personalizzati): consente all'utente di visualizzare solo dashboard personalizzati predefiniti.
- **Dashboard only** (Solo dashboard): consente all'utente di visualizzare solo i dashboard.

### <a name="create-a-cost-entity-hierarchy"></a>Creare una gerarchia di entità di costo

Per creare una gerarchia di entità di costo, è necessario disporre di un account con accesso Enterprise + Cost Allocation (Enterprise + Allocazione costi).

Nel portale di Cloudyn fare clic sul simbolo a forma di ingranaggio in alto a destra e selezionare **Cloud Accounts** (Account cloud). L'albero **Entità** viene visualizzato nel riquadro sinistro. Se necessario, espandere l'albero delle entità in modo che sia possibile visualizzare l'entità che si vuole associare a un account.  Gli account del provider di servizi cloud vengono visualizzati nelle schede nel riquadro di destra. Selezionare una scheda e quindi fare clic su un account o una sottoscrizione, trascinarlo sull'entità e quindi rilasciarlo. Nel riquadro **Spostamento** un messaggio informa che l'account è stato spostato. Fare clic su **OK**.

È anche possibile associare più account a un'entità. Selezionare gli account e quindi fare clic su **Sposta**. Nel riquadro Move Accounts (Sposta account), selezionare l'entità in cui si intende spostare l'account e quindi fare clic su **Salva**. Nel riquadro Move Accounts (Sposta account) viene chiesto di confermare lo spostamento degli account. Fare clic su **Sì** e quindi su **OK**.

Per guardare un video di esercitazione sulla creazione di una gerarchia di entità costo, vedere [Creare una gerarchia di entità costo in Cloudyn](https://youtu.be/dAd9G7u0FmU).

Per gli utenti di un contratto Enterprise di Azure, è disponibile un video di esercitazione sull'associazione di account e sottoscrizioni a entità nella pagina [Connessione ad Azure Resource Manager con Cloudyn](https://youtu.be/oCIwvfBB6kk).

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un utente con accesso amministrativo
> * Creare un utente con accesso utente
> * Eliminare un utente
> * Eliminare o esportare i dati personali
> * Creare e gestire le entità


Se non è già stato abilitato l'accesso all'API di Azure Resource Manager per gli account, vedere l'articolo seguente.

> [!div class="nextstepaction"]
> [Attivare sottoscrizioni e account di Azure](activate-subs-accounts.md)
