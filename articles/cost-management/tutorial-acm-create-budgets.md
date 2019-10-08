---
title: Esercitazione - Creare e gestire budget di Azure | Microsoft Docs
description: Questa esercitazione illustra come pianificare e controllare i costi dei servizi di Azure utilizzati.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 8d8f796fa7db9cab5bcac88a293dd1b98707a571
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025766"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Esercitazione: Creare e gestire budget di Azure

I budget in Gestione costi consentono di pianificare e promuovere la responsabilizzazione nell'organizzazione. Con i budget, è possibile tenere traccia dei costi sostenuti per i servizi di Azure consumati o sottoscritti per un determinato periodo. Risultano utili per informare altri utenti in merito alle spese per una gestione proattiva dei costi e per monitorare l'andamento della spesa nel tempo. Quando vengono superate le soglie di budget create, vengono attivate solo notifiche. Nessuna delle risorse è interessata e il consumo non viene interrotto. È possibile usare i budget per confrontare e tenere traccia della spesa durante l'analisi dei costi.

I dati relativi ai costi e all'utilizzo sono in genere disponibili entro 12-16 ore e i budget vengono valutati a fronte di questi costi ogni quattro ore. Le notifiche tramite posta elettronica vengono normalmente ricevute entro 12-16 ore.

I budget vengono reimpostati automaticamente alla fine di un periodo (mensile, trimestrale o annuale) per lo stesso importo di budget quando si seleziona una data di scadenza nel futuro. Dato che vengono reimpostati con lo stesso importo di budget, è necessario creare budget distinti se gli importi in valuta previsti differiscono per periodi futuri.

Gli esempi in questa esercitazione illustrano la creazione e la modifica di un budget per una sottoscrizione con contratto Enterprise di Azure.

Guarda il video su [come creare un budget per monitorare la spesa con gestione costi di Azure](https://www.youtube.com/watch?v=ExIVG_Gr45A) per scoprire come creare budget in Azure per monitorare la spesa.


In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un budget nel portale di Azure
> * Modificare un budget

## <a name="prerequisites"></a>Prerequisiti

Sono supportati i budget per un'ampia gamma di tipi di account Azure. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Understand Cost Management data](understand-cost-mgt-data.md) (Informazioni sui dati di Gestione costi). Per visualizzare i budget, è necessario effettuare almeno l'accesso in lettura per l'account Azure.

 Per le sottoscrizioni Azure EA è necessario avere accesso in lettura per visualizzare i budget. Per creare e gestire i budget, è necessario disporre dell'autorizzazione di collaboratore. È possibile creare singoli budget per le sottoscrizioni EA e gruppi di risorse. È tuttavia possibile creare budget per gli account di fatturazione EA.

Le seguenti autorizzazioni di Azure o gli ambiti sono supportati per ogni sottoscrizione per i budget per utente e gruppo. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

- Proprietario - Può creare, modificare o eliminare budget per una sottoscrizione.
- Collaboratore e collaboratore Gestione costi: può creare, modificare o eliminare i propri budget. Può modificare l'importo di budget per budget creati da altri utenti.
- Lettore e lettore Gestione costi: può visualizzare i budget per cui ha l'autorizzazione.

Per altre informazioni sull'assegnazione dell'autorizzazione ai dati di Gestione costi, vedere [Assegnare l'accesso ai dati di Gestione costi](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Accedi ad Azure

- Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Creare un budget nel portale di Azure

È possibile creare un budget per una sottoscrizione di Azure per un periodo mensile, trimestrale o annuale. Il contenuto di spostamento nella portale di Azure determina se si crea un budget per una sottoscrizione o per un gruppo di gestione.

Per creare o visualizzare un budget, aprire l'ambito desiderato nel portale di Azure e selezionare **budget** nel menu. Ad esempio, passare a **sottoscrizioni**, selezionare una sottoscrizione dall'elenco e quindi selezionare **budget** nel menu. Utilizzare la pillola **ambito** per passare a un ambito diverso, ad esempio un gruppo di gestione, in budget. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

Dopo aver creato i budget, viene mostrata una semplice visualizzazione della spesa corrente corrispondente.

Fare clic su **Aggiungi**.

![Esempio che mostra un elenco di budget già creati](./media/tutorial-acm-create-budgets/budgets01.png)

Nella finestra **crea budget** verificare che l'ambito visualizzato sia corretto. Scegliere tutti i filtri che si desidera aggiungere. I filtri consentono di creare budget per i costi specifici, ad esempio i gruppi di risorse in una sottoscrizione o un servizio come le macchine virtuali. Qualsiasi filtro che è possibile utilizzare per l'analisi dei costi può essere applicato anche a un budget.

Dopo aver identificato l'ambito e i filtri, digitare un nome di budget. Scegliere quindi un periodo di reimpostazione del budget mensile, trimestrale o annuale. Questo periodo di reimpostazione determina l'intervallo di tempo analizzato dal budget. Il costo valutato dal budget inizia a zero all'inizio di ogni nuovo periodo. Quando si crea un budget trimestrale, funziona esattamente come un budget mensile. La differenza è che l'importo di budget per il trimestre viene suddiviso in modo uniforme tra i tre mesi del trimestre. Un importo di budget annuale è diviso uniformemente tra tutti i 12 mesi dell'anno di calendario.

Se si ha una sottoscrizione con pagamento in base al consumo, MSDN o Visual Studio, il periodo di fatturazione potrebbe non allinearsi con il mese di calendario. Per i tipi di sottoscrizione e i gruppi di risorse, è possibile creare un budget allineato al periodo di fatturazione o ai mesi del calendario. Per creare un budget allineato al periodo di fatturazione, selezionare un periodo di reimpostazione del **mese di fatturazione**, del **trimestre di fatturazione**o dell'anno di **fatturazione**. Per creare un budget allineato al mese di calendario, selezionare un periodo di ripristino **mensile**, **trimestrale**o **annuale**.

Successivamente, identificare la data di scadenza quando il budget diventa non valido e smette di valutare i costi.

In base ai campi scelti nel budget fino a questo punto, viene visualizzato un grafico che consente di selezionare una soglia da usare per il budget. Il budget suggerito si basa sul costo previsto più elevato che potrebbe verificarsi nei periodi futuri. È possibile modificare l'importo del budget.

![Esempio che mostra la creazione del budget con i dati dei costi mensili ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Dopo aver configurato l'importo del budget, fare clic su **Avanti** per configurare gli avvisi del budget. Per i budget sono richiesti almeno una soglia per i costi (% del budget) e un indirizzo di posta elettronica corrispondente. Facoltativamente, è possibile includere fino a cinque soglie e cinque indirizzi di posta elettronica in un unico budget. Quando viene soddisfatta una soglia di budget, le notifiche tramite posta elettronica vengono normalmente ricevute in meno di 20 ore. Per altre informazioni sulle notifiche, vedere [Use cost alerts](cost-mgt-alerts-monitor-usage-spending.md) (Usare avvisi per i costi). Nell'esempio seguente viene generato un avviso di posta elettronica quando viene raggiunto il 90% del budget.

![Esempio che mostra le condizioni di avviso](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Dopo averlo creato, il budget viene visualizzato nell'analisi dei costi. La visualizzazione del budget in relazione alla tendenza di spesa è uno dei primi passaggi quando si iniziano ad [analizzare i costi e la spesa](quick-acm-cost-analysis.md).

![Esempio di budget e spesa di esempio illustrato nell'analisi dei costi](./media/tutorial-acm-create-budgets/cost-analysis.png)

Nell'esempio precedente, è stato creato un budget per una sottoscrizione. Tuttavia, è anche possibile creare un budget per un gruppo di risorse. Se si vuole creare un budget per un gruppo di risorse, passare a **Gestione dei costi e fatturazione** &gt; **Sottoscrizioni** &gt; Selezionare una sottoscrizione > **Gruppi di risorse** > selezionare un gruppo di risorse > **Budget** > e quindi fare clic su **Aggiungi** per aggiungere un budget.

## <a name="trigger-an-action-group"></a>Attivare un gruppo di azione

Quando si crea o si modifica un budget per un ambito di sottoscrizione o di gruppo di risorse, è possibile configurarlo in modo che chiami un gruppo di azione. Il gruppo di azioni può eseguire una serie di azioni diverse quando viene soddisfatta la soglia del budget. Per ulteriori informazioni sui gruppi di azioni, vedere [creare e gestire gruppi di azioni nella portale di Azure](../azure-monitor/platform/action-groups.md). Per altre informazioni sull'uso dell'automazione basata sul budget con i gruppi di azioni, vedere [gestire i costi con i budget di Azure](../billing/billing-cost-management-budget-scenario.md).

Per creare o aggiornare i gruppi di azioni, fare clic su **Gestisci gruppi di azioni** durante la creazione o la modifica di un budget.

![Esempio di creazione di un budget per visualizzare i gruppi di azioni di gestione](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Fare quindi clic su **Aggiungi gruppo di azione** e creare il gruppo di azioni.


![Immagine della casella di gruppo Aggiungi azione](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Dopo aver creato il gruppo di azioni, chiudere la casella per tornare al budget.

Configurare il budget per l'uso del gruppo di azioni quando viene soddisfatta una singola soglia. Sono supportate fino a cinque soglie diverse.

![Esempio che mostra la selezione del gruppo di azioni per una condizione di avviso](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

Nell'esempio seguente vengono illustrate le soglie del budget impostate su 50%, 75% e 100%. Ogni viene configurato per attivare le azioni specificate all'interno del gruppo di azioni designato.

![Esempio che mostra le condizioni di avviso configurate con diversi gruppi di azioni e tipo di azioni](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un budget nel portale di Azure
> * Modificare un budget

Passare all'esercitazione successiva per creare un'esportazione ricorrente per i dati di gestione costi.

> [!div class="nextstepaction"]
> [Creare e gestire dati esportati](tutorial-export-acm-data.md)
