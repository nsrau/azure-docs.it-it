---
title: 'Esercitazione: Creare e gestire i budget di Azure'
description: Questa esercitazione illustra come pianificare e controllare i costi dei servizi di Azure utilizzati.
author: bandersmsft
ms.author: banders
ms.date: 07/15/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: a48e4b594b82f6e910db26fc2319032fbef30b6b
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446004"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Esercitazione: Creare e gestire budget di Azure

I budget in Gestione costi consentono di pianificare e promuovere la responsabilizzazione nell'organizzazione. Con i budget, è possibile tenere traccia dei costi sostenuti per i servizi di Azure consumati o sottoscritti per un determinato periodo. Risultano utili per informare altri utenti in merito alle spese per una gestione proattiva dei costi e per monitorare l'andamento della spesa nel tempo. Quando vengono superate le soglie di budget create, vengono attivate solo notifiche. Nessuna delle risorse è interessata e il consumo non viene interrotto. È possibile usare i budget per confrontare e tenere traccia della spesa durante l'analisi dei costi.

I dati relativi ai costi e all'utilizzo sono in genere disponibili entro 20 ore e i budget vengono valutati a fronte di questi costi ogni 12-14 ore. Quando viene raggiunta una soglia del budget, le notifiche di posta elettronica vengono generalmente inviate entro un'ora dalla valutazione.

I budget vengono reimpostati automaticamente alla fine di un periodo (mensile, trimestrale o annuale) per lo stesso importo di budget quando si seleziona una data di scadenza nel futuro. Dato che vengono reimpostati con lo stesso importo di budget, è necessario creare budget distinti se gli importi in valuta previsti differiscono per periodi futuri.

Gli esempi in questa esercitazione illustrano la creazione e la modifica di un budget per una sottoscrizione con contratto Enterprise di Azure.

Per informazioni su come creare budget in Azure per monitorare la spesa, vedere il video relativo all'[applicazione dei budget alle sottoscrizioni tramite il portale di Azure](https://www.youtube.com/watch?v=UrkHiUx19Po). Per guardare altri video, visitare il [canale YouTube di Gestione costi](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/UrkHiUx19Po]

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un budget nel portale di Azure
> * Creare e modificare i budget con PowerShell
> * Creare un budget con un modello di Azure Resource Manager

## <a name="prerequisites"></a>Prerequisiti

I budget sono supportati per i tipi di account e gli ambiti di Azure seguenti:

- Ambiti di controllo degli accessi in base al ruolo di Azure
    - Gruppi di gestione
    - Subscription
- Ambiti del Contratto Enterprise
    - Account di fatturazione
    - department
    - Account di registrazione
- Singoli contratti
    - Account di fatturazione
- Ambiti del Contratto del cliente Microsoft
    - Account di fatturazione
    - Profilo di fatturazione
    - Sezione della fattura
    - Customer
- Scopi AWS
    - Account esterno
    - Sottoscrizione esterna


Per visualizzare i budget, è necessario effettuare almeno l'accesso in lettura per l'account Azure.

Se si ha una nuova sottoscrizione, non è possibile creare immediatamente un budget o usare altre funzionalità di Gestione costi. Potrebbero essere necessarie fino a 48 ore prima di poter usarle usare tutte.

Per le sottoscrizioni Azure EA è necessario avere accesso in lettura per visualizzare i budget. Per creare e gestire i budget, è necessario disporre dell'autorizzazione di collaboratore.

Le autorizzazioni o gli ambiti di Azure riportati di seguito sono supportati per i budget per ogni sottoscrizione, in base a utente e gruppo. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

- Proprietario - Può creare, modificare o eliminare budget per una sottoscrizione.
- Collaboratore e collaboratore Gestione costi: può creare, modificare o eliminare i propri budget. Può modificare l'importo di budget per budget creati da altri utenti.
- Lettore e lettore Gestione costi: può visualizzare i budget per cui ha l'autorizzazione.

Per altre informazioni sull'assegnazione dell'autorizzazione ai dati di Gestione costi, vedere [Assegnare l'accesso ai dati di Gestione costi](../../cost-management/assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

- Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-budget-in-the-azure-portal"></a>Creare un budget nel portale di Azure

È possibile creare un budget per una sottoscrizione di Azure per un periodo mensile, trimestrale o annuale.

Per creare o visualizzare un budget, aprire l'ambito desiderato nel portale di Azure e selezionare **Budget** nel menu. Passare ad esempio a **Sottoscrizioni**, selezionare una sottoscrizione dall'elenco, quindi selezionare **Budget** nel menu. Usare l'etichetta **Ambito** per passare a un ambito diverso, ad esempio un gruppo di gestione, in Budget. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

Dopo aver creato i budget, viene mostrata una semplice visualizzazione della spesa corrente corrispondente.

Selezionare **Aggiungi**.

![Esempio che illustra un elenco di budget già creati](./media/tutorial-acm-create-budgets/budgets01.png)

Verificare che l'ambito visualizzato nella finestra **Crea il budget** sia corretto. Scegliere i filtri da aggiungere. I filtri consentono di creare budget per costi specifici, ad esempio i gruppi di risorse in una sottoscrizione o un servizio come le macchine virtuali. Tutti i filtri che si possono usare per l'analisi dei costi possono essere applicati anche a un budget.

Dopo aver identificato l'ambito e i filtri, digitare un nome di budget. Scegliere quindi il periodo di ripristino del budget, tra mensile, trimestrale o annuale. Il periodo di ripristino determina l'intervallo di tempo analizzato dal budget. Il costo valutato dal budget comincia da zero, all'inizio di ogni nuovo periodo. Quando si crea un budget trimestrale, funziona esattamente come un budget mensile. La differenza è che l'importo di budget per il trimestre viene suddiviso in modo uniforme tra i tre mesi del trimestre. Un importo di budget annuale viene suddiviso in modo uniforme tra tutti i 12 mesi dell'anno di calendario.

Se si ha una sottoscrizione con pagamento in base al consumo, MSDN o Visual Studio, il periodo di fatturazione potrebbe non allinearsi con il mese di calendario. Per questi tipi di sottoscrizioni e gruppi di risorse, è possibile creare un budget allineato al periodo di fatturazione o ai mesi di calendario. Per creare un budget allineato al periodo di fatturazione, selezionare un periodo di ripristino tra **Mese di fatturazione**, **Trimestre di fatturazione** o **Anno di fatturazione**. Per creare un budget allineato al mese di calendario, selezionare un periodo di ripristino tra **Mensile**, **Trimestrale** o **Annuale**.

Successivamente, identificare la data di scadenza, vale a dire quando il budget non sarà più valido e non valuterà più i costi.

In base ai campi scelti nel budget fino a questo momento, verrà visualizzato un grafico che consente di selezionare una soglia da usare per il budget. Il budget suggerito si basa sul costo più elevato previsto che potrebbe essere registrato in futuro. L'importo del budget può essere modificato.

![Esempio che illustra la creazione del budget con dati sui costi mensili ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Dopo aver configurato l'importo del budget, selezionare **Avanti** per configurare gli avvisi del budget. Per i budget sono richiesti almeno una soglia per i costi (% del budget) e un indirizzo di posta elettronica corrispondente. Facoltativamente, è possibile includere fino a cinque soglie e cinque indirizzi di posta elettronica in un unico budget. Quando viene raggiunta una soglia del budget, le notifiche di posta elettronica vengono generalmente inviate entro un'ora dalla valutazione.

Se si vogliono ricevere messaggi di posta elettronica, aggiungere azure-noreply@microsoft.com all'elenco dei mittenti approvati in modo che i messaggi non vengano recapitati nella cartella Posta indesiderata. Per altre informazioni sulle notifiche, vedere [Use cost alerts](../../cost-management/cost-mgt-alerts-monitor-usage-spending.md) (Usare avvisi per i costi).

Nell'esempio seguente viene generato un avviso tramite posta elettronica quando viene raggiunto il 90% del budget. Se si crea un budget con l'API dei budget, è anche possibile assegnare ruoli agli utenti perché ricevano gli avvisi. L'assegnazione dei ruoli agli utenti non è supportata nel portale di Azure. Per altre informazioni sull'API dei budget di Azure, vedere [API dei budget](/rest/api/consumption/budgets).

I limiti di avviso supportano un intervallo compreso tra 0,01 e 1000% della soglia del budget specificata.

![Esempio che illustra le condizioni di avviso](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Dopo averlo creato, il budget viene visualizzato nell'analisi dei costi. La visualizzazione del budget rispetto alla tendenza di spesa è uno dei primi passaggi quando si iniziano ad [analizzare i costi e la spesa](../../cost-management/quick-acm-cost-analysis.md).

![Esempio di budget e spesa di esempio illustrato nell'analisi dei costi](./media/tutorial-acm-create-budgets/cost-analysis.png)

Nell'esempio precedente, è stato creato un budget per una sottoscrizione. È possibile creare un budget anche per un gruppo di risorse. Se si vuole creare un budget per un gruppo di risorse, passare a **Gestione dei costi e fatturazione** &gt; **Sottoscrizioni**&gt; selezionare una sottoscrizione > **Gruppi di risorse** > selezionare un gruppo di risorse > **Budget** > quindi fare clic su **Aggiungi** per aggiungere un budget.

### <a name="create-a-budget-for-combined-azure-and-aws-costs"></a>Creare un budget per i costi combinati di Azure e AWS

È possibile raggruppare i costi di Azure e AWS assegnando un gruppo di gestione al connettore insieme agli account consolidati e collegati. Assegnare le sottoscrizioni di Azure allo stesso gruppo di gestione. Creare quindi un budget per i costi combinati.

1. In Gestione costi selezionare **Budget**.
1. Selezionare **Aggiungi**.
1. Selezionare **Cambia ambito** quindi selezionare il gruppo di gestione.
1. Continuare a creare il budget fino al completamento.

## <a name="costs-in-budget-evaluations"></a>Costi nelle valutazioni del budget

Le valutazioni dei costi del budget includono ora istanze riservate e dati di acquisto. Se i costi vengono addebitati all'utente, è possibile che si ricevano avvisi in quanto per le valutazioni sono previsti addebiti. È consigliabile accedere al [portale di Azure](https://portal.azure.com) per verificare che le soglie del budget siano configurate correttamente per calcolare i nuovi costi. Gli addebiti di fatturazione di Azure non subiscono modifiche. I budget vengono ora valutati in base a un set più completo dei costi. Se i costi vengono addebitati all'utente, il comportamento del budget rimane invariato.

Se si vogliono filtrare i nuovi costi in modo che i budget siano valutati in base agli addebiti iniziali per il consumo di Azure, aggiungere al budget i filtri seguenti:

- Tipo server di pubblicazione: Azure
- Tipo di addebito: Uso

Le valutazioni dei costi del budget si basano sul costo effettivo. Non includono l'ammortamento. Per altre informazioni sulle opzioni di filtro disponibili nei budget, vedere [Informazioni sulle opzioni di raggruppamento e filtro](group-filter.md).

## <a name="trigger-an-action-group"></a>Attivare un gruppo di azioni

Quando si crea o si modifica un budget per un ambito di sottoscrizione o di gruppo di risorse, è possibile configurarlo in modo che chiami un gruppo di azioni. Il gruppo di azioni può eseguire varie azioni quando viene raggiunta la soglia del budget. I gruppi di azioni sono attualmente supportati solo per gli ambiti di sottoscrizione e di gruppo di risorse. Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni nel portale di Azure](../../azure-monitor/platform/action-groups.md). Per altre informazioni sull'uso dell'automazione basata sul budget con i gruppi di azioni, vedere [Gestire i costi con i budget di Azure](../manage/cost-management-budget-scenario.md).

Per creare o aggiornare i gruppi di azioni, selezionare **Gestisci gruppi di azioni** durante la creazione o la modifica di un budget.

![Esempio di creazione di un budget per visualizzare Gestisci gruppi di azioni](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

Selezionare quindi **Aggiungi gruppo di azioni** e creare il gruppo di azioni.

![Immagine della finestra Aggiungi gruppo di azioni](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Dopo aver creato il gruppo di azioni, chiudere la finestra per tornare al budget.

Configurare il budget per usare il gruppo di azioni quando viene raggiunta una singola soglia. Sono supportate fino a cinque soglie diverse.

![Esempio che illustra la selezione del gruppo di azioni per una condizione di avviso](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

L'esempio seguente mostra le soglie del budget impostate su 50%, 75% e 100%. Ogni soglia è configurata per attivare le azioni specificate all'interno del gruppo di azioni designato.

![Esempio che illustra le condizioni di avviso configurate con diversi gruppi di azioni e tipo di azioni](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

L'integrazione del budget con i gruppi di azioni funziona solo per i gruppi di azioni in cui lo schema di avviso comune è disabilitato. Per altre informazioni sulla disabilitazione dello schema, vedere [Come abilitare lo schema di avviso comune](../../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="create-and-edit-budgets-with-powershell"></a>Creare e modificare i budget con PowerShell

I clienti con contratto Enterprise possono creare e modificare budget a livello di codice usando il modulo Azure PowerShell.  Per scaricare la versione più recente di Azure PowerShell, eseguire il comando seguente:

```azurepowershell-interactive
install-module -name Az
```

I comandi di esempio seguenti creano un budget.

```azurepowershell-interactive
#Sign into Azure Powershell with your account

Connect-AzAccount

#Select a subscription to to monitor with a budget

select-AzSubscription -Subscription "Your Subscription"

#Create an action group email receiver and corresponding action group

$email1 = New-AzActionGroupReceiver -EmailAddress test@test.com -Name EmailReceiver1
$ActionGroupId = (Set-AzActionGroup -ResourceGroupName YourResourceGroup -Name TestAG -ShortName TestAG -Receiver $email1).Id

#Create a monthly budget that sends an email and triggers an Action Group to send a second email. Make sure the StartDate for your monthly budget is set to the first day of the current month. Note that Action Groups can also be used to trigger automation such as Azure Functions or Webhooks.

New-AzConsumptionBudget -Amount 100 -Name TestPSBudget -Category Cost -StartDate 2020-02-01 -TimeGrain Monthly -EndDate 2022-12-31 -ContactEmail test@test.com -NotificationKey Key1 -NotificationThreshold 0.8 -NotificationEnabled -ContactGroup $ActionGroupId
```
## <a name="create-a-budget-with-an-azure-resource-manager-template"></a>Creare un budget con un modello di Azure Resource Manager

È possibile creare un budget con un modello di Azure Resource Manager. Per usare il modello, vedere [Creare un budget con un modello di Azure Resource Manager](quick-create-budget-template.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un budget nel portale di Azure
> * Creare e modificare i budget con PowerShell
> * Creare un budget con un modello di Azure Resource Manager

Passare all'esercitazione successiva per creare un'esportazione ricorrente per i dati di gestione costi.

> [!div class="nextstepaction"]
> [Creare e gestire dati esportati](tutorial-export-acm-data.md)
