---
title: Esercitazione - Creare e gestire budget di Azure | Microsoft Docs
description: Questa esercitazione illustra come pianificare e controllare i costi dei servizi di Azure utilizzati.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/22/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: bb02c4903348a3b8c1d129f02be64109ec0f48eb
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76769731"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Esercitazione: Creare e gestire budget di Azure

I budget in Gestione costi consentono di pianificare e promuovere la responsabilizzazione nell'organizzazione. Con i budget, è possibile tenere traccia dei costi sostenuti per i servizi di Azure consumati o sottoscritti per un determinato periodo. Risultano utili per informare altri utenti in merito alle spese per una gestione proattiva dei costi e per monitorare l'andamento della spesa nel tempo. Quando vengono superate le soglie di budget create, vengono attivate solo notifiche. Nessuna delle risorse è interessata e il consumo non viene interrotto. È possibile usare i budget per confrontare e tenere traccia della spesa durante l'analisi dei costi.

I dati relativi ai costi e all'utilizzo sono in genere disponibili entro 12-16 ore e i budget vengono valutati a fronte di questi costi ogni quattro ore. Le notifiche tramite posta elettronica vengono normalmente ricevute entro 12-16 ore.

I budget vengono reimpostati automaticamente alla fine di un periodo (mensile, trimestrale o annuale) per lo stesso importo di budget quando si seleziona una data di scadenza nel futuro. Dato che vengono reimpostati con lo stesso importo di budget, è necessario creare budget distinti se gli importi in valuta previsti differiscono per periodi futuri.

Gli esempi in questa esercitazione illustrano la creazione e la modifica di un budget per una sottoscrizione con contratto Enterprise di Azure.

Per informazioni su come creare budget in Azure per monitorare la spesa, vedere il video relativo all'[applicazione dei budget alle sottoscrizioni tramite il portale di Azure](https://www.youtube.com/watch?v=UrkHiUx19Po).


In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un budget nel portale di Azure
> * Modificare un budget

## <a name="prerequisites"></a>Prerequisites

Sono supportati i budget per un'ampia gamma di tipi di account Azure. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Understand Cost Management data](understand-cost-mgt-data.md) (Informazioni sui dati di Gestione costi). Per visualizzare i budget, è necessario effettuare almeno l'accesso in lettura per l'account Azure.

 Per le sottoscrizioni Azure EA è necessario avere accesso in lettura per visualizzare i budget. Per creare e gestire i budget, è necessario disporre dell'autorizzazione di collaboratore. È possibile creare singoli budget per le sottoscrizioni EA e gruppi di risorse. È tuttavia possibile creare budget per gli account di fatturazione EA.

Le autorizzazioni o gli ambiti di Azure riportati di seguito sono supportati per i budget per ogni sottoscrizione, in base a utente e gruppo. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

- Proprietario - Può creare, modificare o eliminare budget per una sottoscrizione.
- Collaboratore e collaboratore Gestione costi: può creare, modificare o eliminare i propri budget. Può modificare l'importo di budget per budget creati da altri utenti.
- Lettore e lettore Gestione costi: può visualizzare i budget per cui ha l'autorizzazione.

Per altre informazioni sull'assegnazione dell'autorizzazione ai dati di Gestione costi, vedere [Assegnare l'accesso ai dati di Gestione costi](../../cost-management/assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

- Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Creare un budget nel portale di Azure

È possibile creare un budget per una sottoscrizione di Azure per un periodo mensile, trimestrale o annuale. Il contenuto della navigazione nel portale di Azure determina se si crea un budget per una sottoscrizione o per un gruppo di gestione.

Per creare o visualizzare un budget, aprire l'ambito desiderato nel portale di Azure e selezionare **Budget** nel menu. Passare ad esempio a **Sottoscrizioni**, selezionare una sottoscrizione dall'elenco, quindi selezionare **Budget** nel menu. Usare l'etichetta **Ambito** per passare a un ambito diverso, ad esempio un gruppo di gestione, in Budget. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

Dopo aver creato i budget, viene mostrata una semplice visualizzazione della spesa corrente corrispondente.

Scegliere **Aggiungi**.

![Esempio che illustra un elenco di budget già creati](./media/tutorial-acm-create-budgets/budgets01.png)

Verificare che l'ambito visualizzato nella finestra **Crea il budget** sia corretto. Scegliere i filtri da aggiungere. I filtri consentono di creare budget per costi specifici, ad esempio i gruppi di risorse in una sottoscrizione o un servizio come le macchine virtuali. Tutti i filtri che si possono usare per l'analisi dei costi possono essere applicati anche a un budget.

Dopo aver identificato l'ambito e i filtri, digitare un nome di budget. Scegliere quindi il periodo di ripristino del budget, tra mensile, trimestrale o annuale. Il periodo di ripristino determina l'intervallo di tempo analizzato dal budget. Il costo valutato dal budget comincia da zero, all'inizio di ogni nuovo periodo. Quando si crea un budget trimestrale, funziona esattamente come un budget mensile. La differenza è che l'importo di budget per il trimestre viene suddiviso in modo uniforme tra i tre mesi del trimestre. Un importo di budget annuale viene suddiviso in modo uniforme tra tutti i 12 mesi dell'anno di calendario.

Se si ha una sottoscrizione con pagamento in base al consumo, MSDN o Visual Studio, il periodo di fatturazione potrebbe non allinearsi con il mese di calendario. Per questi tipi di sottoscrizioni e gruppi di risorse, è possibile creare un budget allineato al periodo di fatturazione o ai mesi di calendario. Per creare un budget allineato al periodo di fatturazione, selezionare un periodo di ripristino tra **Mese di fatturazione**, **Trimestre di fatturazione** o **Anno di fatturazione**. Per creare un budget allineato al mese di calendario, selezionare un periodo di ripristino tra **Mensile**, **Trimestrale** o **Annuale**.

Successivamente, identificare la data di scadenza, vale a dire quando il budget non sarà più valido e non valuterà più i costi.

In base ai campi scelti nel budget fino a questo momento, verrà visualizzato un grafico che consente di selezionare una soglia da usare per il budget. Il budget suggerito si basa sul costo più elevato previsto che potrebbe essere registrato in futuro. L'importo del budget può essere modificato.

![Esempio che illustra la creazione del budget con dati sui costi mensili ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Dopo aver configurato l'importo del budget, fare clic su **Avanti** per configurare gli avvisi del budget. Per i budget sono richiesti almeno una soglia per i costi (% del budget) e un indirizzo di posta elettronica corrispondente. Facoltativamente, è possibile includere fino a cinque soglie e cinque indirizzi di posta elettronica in un unico budget. Quando viene raggiunta una soglia del budget, le notifiche di posta elettronica vengono generalmente ricevute in meno di 20 ore. Per altre informazioni sulle notifiche, vedere [Use cost alerts](../../cost-management/cost-mgt-alerts-monitor-usage-spending.md) (Usare avvisi per i costi). Nell'esempio seguente viene generato un avviso tramite posta elettronica quando viene raggiunto il 90% del budget. Se si crea un budget con l'API dei budget, è anche possibile assegnare ruoli agli utenti perché ricevano gli avvisi. L'assegnazione dei ruoli agli utenti non è supportata nel portale di Azure. Per altre informazioni sull'API dei budget di Azure, vedere [API dei budget](/rest/api/consumption/budgets).

![Esempio che illustra le condizioni di avviso](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Dopo averlo creato, il budget viene visualizzato nell'analisi dei costi. La visualizzazione del budget in relazione alla tendenza di spesa è uno dei primi passaggi quando si iniziano ad [analizzare i costi e la spesa](../../cost-management/quick-acm-cost-analysis.md).

![Esempio di budget e spesa di esempio illustrato nell'analisi dei costi](./media/tutorial-acm-create-budgets/cost-analysis.png)

Nell'esempio precedente, è stato creato un budget per una sottoscrizione. Tuttavia, è anche possibile creare un budget per un gruppo di risorse. Se si vuole creare un budget per un gruppo di risorse, passare a **Gestione dei costi e fatturazione** &gt; **Sottoscrizioni**&gt; selezionare una sottoscrizione > **Gruppi di risorse** > selezionare un gruppo di risorse > **Budget** > quindi fare clic su **Aggiungi** per aggiungere un budget.

## <a name="costs-in-budget-evaluations"></a>Costi nelle valutazioni del budget

Le valutazioni dei costi del budget includono ora istanze riservate e dati di acquisto. Se i costi vengono addebitati all'utente, è possibile che si ricevano avvisi in quanto per le valutazioni sono previsti addebiti. È consigliabile accedere al [portale di Azure](https://portal.azure.com) per verificare che le soglie del budget siano configurate correttamente per calcolare i nuovi costi. Gli addebiti di fatturazione di Azure non subiscono modifiche. I budget vengono ora valutati in base a un set più completo dei costi. Se i costi vengono addebitati all'utente, il comportamento del budget rimane invariato.

Se si vogliono filtrare i nuovi costi in modo che i budget siano valutati in base agli addebiti iniziali per il consumo di Azure, aggiungere al budget i filtri seguenti:

- Tipo server di pubblicazione: Azure
- Tipo di addebito: Uso

Le valutazioni dei costi del budget si basano sul costo effettivo. Non includono l'ammortamento. Per altre informazioni sulle opzioni di filtro disponibili nei budget, vedere [Informazioni sulle opzioni di raggruppamento e filtro](quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options).


## <a name="trigger-an-action-group"></a>Attivare un gruppo di azioni

Quando si crea o si modifica un budget per un ambito di sottoscrizione o di gruppo di risorse, è possibile configurarlo in modo che chiami un gruppo di azioni. Il gruppo di azioni può eseguire una serie di azioni diverse quando viene raggiunta la soglia del budget. I gruppi di azioni sono attualmente supportati solo per gli ambiti di sottoscrizione e di gruppo di risorse. Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni nel portale di Azure](../../azure-monitor/platform/action-groups.md). Per altre informazioni sull'uso dell'automazione basata sul budget con i gruppi di azioni, vedere [Gestire i costi con i budget di Azure](../manage/cost-management-budget-scenario.md).



Per creare o aggiornare i gruppi di azioni, fare clic su **Gestisci gruppi di azioni** durante la creazione o la modifica di un budget.

![Esempio di creazione di un budget per visualizzare Gestisci gruppi di azioni](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Fare quindi clic su **Aggiungi gruppo di azioni** e creare il gruppo di azioni.


![Immagine della finestra Aggiungi gruppo di azioni](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Dopo aver creato il gruppo di azioni, chiudere la finestra per tornare al budget.

Configurare il budget per usare il gruppo di azioni quando viene raggiunta una singola soglia. Sono supportate fino a cinque soglie diverse.

![Esempio che illustra la selezione del gruppo di azioni per una condizione di avviso](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

Nell'esempio seguente vengono illustrate le soglie del budget impostate su 50%, 75% e 100%. Ogni soglia è configurata per attivare le azioni specificate all'interno del gruppo di azioni designato.

![Esempio che illustra le condizioni di avviso configurate con diversi gruppi di azioni e tipo di azioni](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

L'integrazione del budget con i gruppi di azioni funziona solo per i gruppi di azioni in cui lo schema di avviso comune è disabilitato. Per altre informazioni sulla disabilitazione dello schema, vedere [Come abilitare lo schema di avviso comune](../../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un budget nel portale di Azure
> * Modificare un budget

Passare all'esercitazione successiva per creare un'esportazione ricorrente per i dati di gestione costi.

> [!div class="nextstepaction"]
> [Creare e gestire dati esportati](tutorial-export-acm-data.md)
