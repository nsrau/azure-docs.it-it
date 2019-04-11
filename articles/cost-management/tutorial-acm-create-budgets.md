---
title: Esercitazione - Creare e gestire budget di Azure | Microsoft Docs
description: Questa esercitazione illustra come pianificare e controllare i costi dei servizi di Azure utilizzati.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 704aefd68f35ca20f72a2a0c46bf11912c139e65
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469489"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Esercitazione: Creare e gestire budget di Azure

I budget in Gestione costi consentono di pianificare e promuovere la responsabilizzazione nell'organizzazione. Con i budget, è possibile tenere traccia dei costi sostenuti per i servizi di Azure consumati o sottoscritti per un determinato periodo. Risultano utili per informare altri utenti in merito alle spese per una gestione proattiva dei costi e per monitorare l'andamento della spesa nel tempo. Quando vengono superate le soglie di budget create, vengono attivate solo notifiche. Nessuna delle risorse è interessata e il consumo non viene interrotto. È possibile usare i budget per confrontare e tenere traccia della spesa durante l'analisi dei costi.

I budget mensili vengono valutati in base alla spesa ogni quattro ore. Tuttavia, i dati e le notifiche per le risorse utilizzate sono disponibili entro otto ore.  

I budget vengono reimpostati automaticamente alla fine di un periodo (mensile, trimestrale o annuale) per lo stesso importo di budget quando si seleziona una data di scadenza nel futuro. Dato che vengono reimpostati con lo stesso importo di budget, è necessario creare budget distinti se gli importi in valuta previsti differiscono per periodi futuri.

Gli esempi in questa esercitazione illustrano la creazione e la modifica di un budget per una sottoscrizione con contratto Enterprise di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un budget nel portale di Azure
> * Modificare un budget

## <a name="prerequisites"></a>Prerequisiti

Sono supportati i budget per un'ampia gamma di tipi di account Azure. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Understand Cost Management data](understand-cost-mgt-data.md) (Informazioni sui dati di Gestione costi). Per visualizzare i budget, è necessario effettuare almeno l'accesso in lettura per l'account Azure.

 Per le sottoscrizioni Azure EA è necessario avere accesso in lettura per visualizzare i budget. Per creare e gestire i budget, è necessario disporre dell'autorizzazione di collaboratore. È possibile creare singoli budget per le sottoscrizioni EA e gruppi di risorse. È tuttavia possibile creare budget per gli account di fatturazione EA.

Le seguenti autorizzazioni di Azure o gli ambiti, sono supportati per ogni sottoscrizione per i budget per utente e gruppo. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

- Proprietario - Può creare, modificare o eliminare budget per una sottoscrizione.
- Collaboratore e collaboratore Gestione costi: può creare, modificare o eliminare i propri budget. Può modificare l'importo di budget per budget creati da altri utenti.
- Lettore e lettore Gestione costi: può visualizzare i budget per cui ha l'autorizzazione.

Per altre informazioni sull'assegnazione dell'autorizzazione ai dati di Gestione costi, vedere [Assegnare l'accesso ai dati di Gestione costi](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

- Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Creare un budget nel portale di Azure

È possibile creare un budget per una sottoscrizione di Azure per un periodo mensile, trimestrale o annuale. Il contenuto per la navigazione nel portale di Azure determina se si crea un budget per una sottoscrizione o per un gruppo di gestione.

Per creare o visualizzare un budget, aprire l'ambito desiderato nel portale di Azure e seleziona **budget** nel menu di scelta. Ad esempio, passare a **abbonamenti**, selezionare una sottoscrizione dall'elenco e quindi selezionare **budget** nel menu di scelta. Usare la **ambito** partite per passare a un ambito diverso, ad esempio un gruppo di gestione nel budget. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

Dopo aver creato i budget, viene mostrata una semplice visualizzazione della spesa corrente corrispondente.

Fare clic su **Aggiungi**.

![Budget di Gestione costi nel portale di Azure](./media/tutorial-acm-create-budgets/budgets01.png)

Nella finestra **Crea il budget** immettere un nome e l'importo per il budget. Scegliere quindi il periodo di durata mensile, trimestrale o annuale. Selezionare poi una data di fine. Per i budget sono richiesti almeno una soglia per i costi (% del budget) e un indirizzo di posta elettronica corrispondente. Facoltativamente, è possibile includere fino a cinque soglie e cinque indirizzi di posta elettronica in un unico budget. Quando viene raggiunta una soglia di budget, in genere vengono ricevute notifiche di posta elettronica in meno di otto ore. Per altre informazioni sulle notifiche, vedere [Use cost alerts](cost-mgt-alerts-monitor-usage-spending.md) (Usare avvisi per i costi).

Se si dispone di una sottoscrizione con pagamento a consumo, MSDN o Visual Studio, il periodo di fatturazione della fattura non potrebbe allinearsi al mese di calendario. Per i tipi di sottoscrizioni e gruppi di risorse, è possibile creare un budget definito è allineato al periodo di fatturazione o i mesi di calendario. Per creare un budget allineato al periodo di fatturazione, selezionare un periodo di ripristino del mese di fatturazione, fatturazione trimestre o anno di fatturazione. Per creare un budget allineato al mese di calendario, selezionare un periodo di ripristino di ogni mese, trimestre o anno.

Di seguito è riportato un esempio di creazione di un budget mensile di $ 4.500. Viene generato un avviso tramite posta elettronica quando viene raggiunto il 90% del budget.

![Informazioni di esempio illustrate nella finestra Crea il budget](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Quando si crea un budget trimestrale, funziona esattamente come un budget mensile. La differenza è che l'importo di budget per il trimestre viene suddiviso in modo uniforme tra i tre mesi del trimestre. Come è prevedibile, un importo di budget annuale viene suddiviso in modo uniforme tra tutti i 12 mesi dell'anno di calendario.

La spesa corrente rispetto al budget viene aggiornata ogni volta che Gestione costi riceve i dati di fatturazione aggiornati. In genere, ogni giorno.

![Informazioni di esempio che mostrano la spesa corrente rispetto al budget](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

Dopo averlo creato, il budget viene visualizzato nell'analisi dei costi. La visualizzazione del budget in relazione alla tendenza di spesa è uno dei primi passaggi quando si iniziano ad [analizzare i costi e la spesa](quick-acm-cost-analysis.md).

![Esempio di budget e spesa di esempio illustrato nell'analisi dei costi](./media/tutorial-acm-create-budgets/cost-analysis.png)

Nell'esempio precedente, è stato creato un budget per una sottoscrizione. Tuttavia, è anche possibile creare un budget per un gruppo di risorse. Se si vuole creare un budget per un gruppo di risorse, passare a **Gestione dei costi e fatturazione** &gt; **Sottoscrizioni** &gt; Selezionare una sottoscrizione > **Gruppi di risorse** > selezionare un gruppo di risorse > **Budget** > e quindi fare clic su **Aggiungi** per aggiungere un budget.

## <a name="edit-a-budget"></a>Modificare un budget

A seconda del livello di accesso disponibile, è possibile modificare un budget per modificare le relative proprietà. Nell'esempio seguente alcune proprietà sono di sola lettura perché l'utente ha solo l'autorizzazione Collaboratore per la sottoscrizione. Attualmente, la **data di scadenza** è disabilitata e non può essere modificata dopo l'impostazione.

![Esempio di modifica di un budget per modificare le varie proprietà](./media/tutorial-acm-create-budgets/edit-budget.png)

## <a name="trigger-an-action-group"></a>Attivare un gruppo di azioni

Quando si crea o si modifica un budget per una sottoscrizione o un ambito di gruppo di risorse, è possibile configurarlo per chiamare un gruppo di azioni. Il gruppo di azioni può eseguire una serie di azioni diverse quando viene raggiunta la soglia di budget. Per altre informazioni sui gruppi di azioni, vedere [creare e gestire gruppi di azione nel portale di Azure](../azure-monitor/platform/action-groups.md). Per altre informazioni sull'uso di automazione in base al budget con gruppi di azioni, vedere [gestire i costi con i budget di Azure](../billing/billing-cost-management-budget-scenario.md).

Per creare o aggiornare i gruppi di azioni, fare clic su **gestire i gruppi di azioni** mentre si sta creando o modificando un budget definito.

![Esempio di creazione di un budget per visualizzare i gruppi di azioni di gestione](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

Fare quindi clic **Aggiungi gruppo di azione** e creare il gruppo di azione.


![Immagine della casella di gruppo di azione Aggiungi](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Dopo l'azione di creazione del gruppo, chiudere la finestra per tornare al proprio budget.

Configurare il tuo budget per usare il gruppo di azioni quando viene raggiunta una soglia singoli. Sono supportati fino a cinque diverse soglie.

![Esempio di selezione del gruppo di azione per una condizione di avviso](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

Nell'esempio seguente vengono illustrate le soglie di budget impostate su 50%, 75% e 100%. Ognuno è configurato per attivare le azioni specificate all'interno del gruppo di azione designato.

![Esempio che illustra le condizioni di avviso configurate con diversi gruppi di azioni e il tipo di azioni](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un budget nel portale di Azure
> * Modificare un budget

Passare all'esercitazione successiva per creare un'esportazione ricorrente per i dati di gestione costi.

> [!div class="nextstepaction"]
> [Creare e gestire dati esportati](tutorial-export-acm-data.md)
