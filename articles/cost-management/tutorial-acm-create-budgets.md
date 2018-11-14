---
title: Esercitazione - Creare e gestire budget di Azure | Microsoft Docs
description: Questa esercitazione illustra come pianificare e controllare i costi dei servizi di Azure utilizzati.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/02/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 338035014cb8e34caa7aea688028a96d039b028c
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2018
ms.locfileid: "50977669"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Esercitazione: Creare e gestire budget di Azure

I budget in Gestione costi consentono di pianificare e promuovere la responsabilizzazione nell'organizzazione. Con i budget, è possibile tenere traccia dei costi sostenuti per i servizi di Azure consumati o sottoscritti per un determinato periodo. Risultano utili per informare altri utenti in merito alle spese per una gestione proattiva dei costi e per monitorare l'andamento della spesa nel tempo. È possibile visualizzare l'andamento della spesa nel tempo. Quando vengono superate le soglie di budget create, vengono attivate solo notifiche. Nessuna delle risorse è interessata e il consumo non viene interrotto. È possibile usare i budget per confrontare e tenere traccia della spesa durante l'analisi dei costi.

I budget vengono reimpostati automaticamente alla fine di un periodo (mensile, trimestrale o annuale) per lo stesso importo di budget quando si seleziona una data di scadenza nel futuro. Dato che vengono reimpostati con lo stesso importo di budget, è necessario creare budget distinti se gli importi in valuta previsti differiscono per periodi futuri.

Gli esempi in questa esercitazione illustrano la creazione e modifica di un budget per una sottoscrizione di Azure Enterprise Agreement (EA).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un budget nel portale di Azure
> * Modificare un budget

## <a name="prerequisites"></a>Prerequisiti

I budget sono disponibili per tutti i clienti con contratto Enterprise (EA) di Azure. È necessario avere accesso in lettura a una sottoscrizione EA di Azure per creare e gestire I budget. È possibile creare singoli budget per le sottoscrizioni EA e gruppi di risorse. È tuttavia possibile creare budget per gli account di fatturazione EA.

Le seguenti autorizzazioni di Azure sono supportate per ogni sottoscrizione per i budget, in base a utente e gruppo:

- Proprietario - Può creare, modificare o eliminare budget per una sottoscrizione.
- Collaboratore - Può creare, modificare o eliminare i propri budget. Può modificare l'importo di budget per budget creati da altri utenti.
- Lettore - Può visualizzare i budget per cui ha l'autorizzazione.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

- Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Creare un budget nel portale di Azure

È possibile creare un budget per una sottoscrizione di Azure per un periodo mensile, trimestrale o annuale. Il contenuto per la navigazione nel portale di Azure determina se si crea un budget per una sottoscrizione o per un gruppo di risorse.

Nel portale di Azure passare a **Gestione dei costi e fatturazione** &gt; **Sottoscrizioni** &gt; Selezionare una sottoscrizione &gt; **Budget**. In questo esempio, il budget creato è per la sottoscrizione selezionata.

Dopo aver creato i budget, viene mostrata una semplice visualizzazione della spesa corrente corrispondente.

Fare clic su **Aggiungi**.

![Budget di Gestione costi](./media/tutorial-acm-create-budgets/budgets01.png)

Nella finestra **Crea il budget** immettere un nome e l'importo per il budget. Scegliere quindi il periodo di durata mensile, trimestrale o annuale. Selezionare poi una data di fine. Per i budget sono richiesti almeno una soglia per i costi (% del budget) e un indirizzo di posta elettronica corrispondente. Facoltativamente, è possibile includere fino a cinque soglie e cinque indirizzi di posta elettronica in un unico budget. Quando viene raggiunta una soglia di budget, in genere vengono ricevute notifiche di posta elettronica in meno di otto ore.

Di seguito è riportato un esempio di creazione di un budget mensile di $ 4.500. Viene generato un avviso tramite posta elettronica quando viene raggiunto il 90% del budget.

![Esempio di budget mensile](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Quando si crea un budget trimestrale, funziona esattamente come un budget mensile. La differenza è che l'importo di budget per il trimestre viene suddiviso in modo uniforme tra i tre mesi del trimestre. Come è prevedibile, un importo di budget annuale viene suddiviso in modo uniforme tra tutti i 12 mesi dell'anno di calendario.

La spesa corrente rispetto al budget viene aggiornata ogni volta che Gestione costi riceve i dati di fatturazione aggiornati. In genere, ogni giorno.

![Spesa corrente rispetto al budget](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

Dopo averlo creato, il budget viene visualizzato nell'analisi dei costi. La visualizzazione del budget in relazione alla tendenza di spesa è uno dei primi passaggi quando si iniziano ad [analizzare i costi e la spesa](quick-acm-cost-analysis.md).

![Budget visualizzato nell'analisi dei costi](./media/tutorial-acm-create-budgets/cost-analysis.png)

Nell'esempio precedente, è stato creato un budget per una sottoscrizione. Tuttavia, è anche possibile creare un budget per un gruppo di risorse. Se si vuole creare un budget per un gruppo di risorse, passare a **Gestione dei costi e fatturazione** &gt; **Sottoscrizioni** &gt; Selezionare una sottoscrizione > **Gruppi di risorse** > selezionare un gruppo di risorse > **Budget** > e quindi fare clic su **Aggiungi** per aggiungere un budget.

## <a name="edit-a-budget"></a>Modificare un budget

A seconda del livello di accesso disponibile, è possibile modificare un budget per modificare le relative proprietà. Nell'esempio seguente, alcune proprietà sono di sola lettura perché l'utente ha solo l'autorizzazione Collaboratore per la sottoscrizione. Attualmente, la **data di scadenza** è disabilitata e non può essere modificata dopo l'impostazione.

![Modifica del budget - autorizzazione Collaboratore](./media/tutorial-acm-create-budgets/edit-budget.png)


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un budget nel portale di Azure
> * Modificare un budget

Passare all'esercitazione successiva per creare un'esportazione ricorrente per i dati di gestione costi.

> [!div class="nextstepaction"]
> [Creare e gestire dati esportati](tutorial-export-acm-data.md)
