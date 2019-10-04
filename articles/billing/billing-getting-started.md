---
title: Evitare costi imprevisti e gestire la fatturazione in Azure
description: Informazioni su come evitare addebiti imprevisti nella fattura di Azure. Usare le funzionalità di gestione e verifica dei costi per una sottoscrizione di Azure.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: d6c287d5ead0095a4f7bb5ad754212b134f7103c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719811"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Evitare addebiti imprevisti con la gestione dei costi e della fatturazione di Azure

Quando si effettua l'iscrizione ad Azure, è consigliabile eseguire alcune operazioni per conoscere meglio la spesa sostenuta:

- Il [calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/) può fornire una stima dei costi prima di creare una risorsa di Azure. 

- Il [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) fornisce la scomposizione e la previsione dei costi correnti. 

- Per raggruppare e conoscere i costi per le risorse usate per diversi progetti o team, esaminare i [tag delle risorse](../azure-resource-manager/resource-group-using-tags.md). Se l'organizzazione dispone di un sistema di creazione report, vedere le [API per la fatturazione](billing-usage-rate-card-overview.md).

- Se la sottoscrizione è stata creata da un contratto Enterprise Agreement (EA), è possibile visualizzare i costi nel portale di Azure. Alcune funzionalità descritte di seguito possono non essere applicabili ai clienti che hanno una sottoscrizione ottenuta tramite Cloud Solution Provider (CSP) o Azure Sponsorship. Per altre informazioni, vedere [Risorse aggiuntive per EA, CSP e Sponsorship](#other-offers).

- Se la sottoscrizione è di una versione di valutazione gratuita, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure in Open (AIO) o BizSpark, viene disabilitata automaticamente all'esaurimento dei crediti. È possibile ottenere altre informazioni sui [limiti di spesa](#spending-limit) per evitare che la sottoscrizione venga disabilitata all'improvviso.

- Se è stata eseguita l'iscrizione a un [account gratuito di Azure](https://azure.microsoft.com/free/), [è possibile usare gratuitamente alcuni dei servizi Azure più richiesti per 12 mesi](billing-create-free-services-included-free-account.md). Oltre ai consigli elencati di seguito, vedere l'argomento su come [evitare di pagare l'account gratuito](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Ottenere una stima dei costi prima di aggiungere servizi di Azure

Ecco alcune informazioni aggiuntive sulla stima dei costi con gli strumenti seguenti:
- Calcolatore prezzi di Azure
- Portale di Azure
- Limite di spesa

Nelle immagini delle sezioni seguenti i prezzi di esempio sono indicati in dollari statunitensi.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Stimare i costi online con il calcolatore dei prezzi

Usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) per ottenere una stima del costo mensile del servizio a cui si è interessati. È possibile aggiungere qualsiasi risorsa di Azure proprietaria per ottenere una stima del costo. Nel calcolatore dei prezzi è possibile modificare il tipo di valuta.

![Screenshot del menu del calcolatore dei prezzi](./media/billing-getting-started/pricing-calc.png)

Nel calcolatore dei prezzi per una macchina virtuale Windows A1 mantenuta costantemente in esecuzione, ad esempio, il costo stimato in ore di calcolo è pari a un determinato importo/mese:

![Screenshot del calcolatore dei prezzi che mostra che il costo stimato mensile di una macchina virtuale Windows A1](./media/billing-getting-started/pricing-calcvm.png)

Per altre informazioni sui prezzi, vedere [Domande frequenti sui prezzi](https://azure.microsoft.com/pricing/faq/). Per contattare un venditore di Azure, chiamare il numero di telefono indicato nella parte superiore della pagina delle domande frequenti.

### <a name="review-estimated-costs-in-the-azure-portal"></a>Esaminare i costi stimati nel portale di Azure

Quando si aggiunge un servizio nel portale di Azure, in genere una visualizzazione mostra un costo mensile stimato simile nella valuta fatturata. Quando si scelgono le dimensioni di una VM Windows, ad esempio, viene visualizzato il costo mensile stimato per le ore di calcolo:

![Esempio: costo stimato mensile di una macchina virtuale Windows A1](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a> Controllare se è attivo un limite di spesa

Se si ha una sottoscrizione che usa crediti, il limite di spesa è attivato per impostazione predefinita. In questo modo, quando si esauriscono tutti i crediti non verrà effettuato alcun addebito sulla carta di credito. Vedere l'[elenco completo delle offerte di Azure e la disponibilità del limite di spesa](https://azure.microsoft.com/support/legal/offer-details/).

Quando si raggiunge il limite di spesa, tuttavia, i servizi vengono disabilitati. Le VM vengono quindi deallocate. Per evitare tempi di inattività del servizio, è necessario disattivare il limite di spesa. Qualsiasi eccedenza verrà addebitata sulla carta di credito in archivio.

Per verificare se è attivo un limite di spesa, passare alla [visualizzazione Sottoscrizioni nel Centro account](https://account.windowsazure.com/Subscriptions). Se il limite di spesa è attivato, viene visualizzato un banner simile al seguente:

![Screenshot con avviso che informa che è attivo il limite di spesa nel Centro account](./media/billing-getting-started/spending-limit-banner.png)

Fare clic sul banner e seguire le istruzioni per rimuovere il limite di spesa. Se al momento dell'iscrizione non si sono immesse le informazioni relative alla carta di credito, per rimuovere il limite di spesa è necessario inserirle. Per altre informazioni, vedere [Limite di spesa di Azure - Come funziona e come abilitarlo o rimuoverlo](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="use-budgets-and-cost-alerts"></a>Usare budget e avvisi relativi ai costi

È possibile creare [budget](../cost-management/tutorial-acm-create-budgets.md) per gestire i costi e creare [avvisi](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) per informare automaticamente gli stakeholder in caso di anomalie di spesa e rischi di costi eccessivi. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi.

## <a name="monitor-costs-when-using-azure-services"></a>Monitorare i costi quando si usano i servizi di Azure
È possibile monitorare i costi con gli strumenti seguenti:

- Tag
- Velocità e dettaglio dei costi
- Analisi dei costi

### <a name="tags"></a> Aggiungere tag alle risorse per raggruppare i dati di fatturazione

È possibile usare tag per raggruppare i dati di fatturazione per i servizi supportati. Se si eseguono più VM per diversi team, ad esempio, è possibile usare i tag per classificare i costi per centro di costo, come HR, marketing, finanza e così via, oppure o ambiente, come produzione, pre-produzione, test.

![Schermata che mostra la configurazione dei tag nel portale](./media/billing-getting-started/tags.png)

I tag sono riportati in diverse visualizzazioni di report dei costi. Ad esempio, sono visibili immediatamente nella [visualizzazione dell'analisi dei costi](#costs) e dopo il primo periodo di fatturazione nel file CSV dei dati dettagliati di utilizzo.

Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Monitorare il dettaglio dei costi e la velocità

Dopo aver eseguito i servizi di Azure, controllare regolarmente gli addebiti. È possibile visualizzare la velocità e la spesa corrente nel portale di Azure.

1. Vedere il [pannello Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selezionare una sottoscrizione.

2. Se si tratta di funzionalità supportate per la sottoscrizione, verranno visualizzate la velocità e il dettaglio dei costi.

    ![Screenshot della velocità e della scomposizione nel portale di Azure](./media/billing-getting-started/burn-rate.PNG)

3. Fare clic su [Analisi dei costi](../cost-management/quick-acm-cost-analysis.md) nell'elenco a sinistra per visualizzare il dettaglio dei costi per risorsa. Dopo l'aggiunta di un servizio è necessario attendere 24 ore prima che i dati vengano visualizzati.

    ![Screenshot della visualizzazione dell'analisi dei costi nel portale di Azure](./media/billing-getting-started/cost-analysis.png)

4. È possibile filtrare la visualizzazione in base a diverse proprietà come [tag](#tags), tipo di risorsa, gruppo di risorse e intervallo di tempo. Fare clic su **Applica** per confermare i filtri e su **Scarica** per esportare la visualizzazione in un file con valori delimitati da virgole (CSV).

5. È anche possibile fare clic su una risorsa per visualizzare la cronologia di spesa e il costo giornaliero della risorsa.

    ![Screenshot della visualizzazione della cronologia di spesa nel portale di Azure](./media/billing-getting-started/costhistory.png)

Confrontare i costi con le stime visualizzate al momento della selezione dei servizi. In caso di differenza significativa tra i costi e le stime, controllare il piano tariffario selezionato per le risorse.

## <a name="optimize-and-reduce-costs"></a>Ottimizzare e ridurre i costi
Se non si conoscono ancora i principi della gestione costi, leggere [Come ottimizzare gli investimenti per il cloud con Gestione costi di Azure](../cost-management/cost-mgt-best-practices.md).

Nel portale di Azure è anche possibile ottimizzare e ridurre i costi di Azure con l'arresto automatico per le macchine virtuali e le raccomandazioni di Advisor.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Provare ad usare funzionalità per la riduzione dei costi come l'arresto automatico delle macchine virtuali

A seconda dello scenario specifico, è possibile configurare l'arresto automatico delle macchine virtuali nel portale di Azure. Per altre informazioni, vedere il post [Auto shutdown for VMs using Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) (Arresto automatico delle macchine virtuali con Azure Resource Manager).

![Screenshot dell'opzione di arresto automatico nel portale](./media/billing-getting-started/auto-shutdown.png)

L'arresto automatico è diverso dall'arresto nella macchina virtuale con le opzioni di risparmio energia. L'arresto automatico arresta e dealloca le macchine virtuali per interrompere gli addebiti aggiuntivi per l'utilizzo. Per altre informazioni, vedere le domande frequenti sui prezzi per le [VM Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e le [VM Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) in relazione agli stati delle VM.

Per informazioni su altre funzionalità per la riduzione dei costi per gli ambienti di sviluppo e test, vedere [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Attivare e rivedere le raccomandazioni di Azure Advisor

[Azure Advisor](../advisor/advisor-overview.md) consente di ridurre i costi identificando le risorse a basso utilizzo. Esaminare Advisor nel portale di Azure:

![Screenshot del pulsante Azure Advisor nel portale di Azure](./media/billing-getting-started/advisor-button.png)

È possibile visualizzare raccomandazioni pratiche sui costi nella scheda **Costo** del dashboard di Advisor:

![Screenshot di un esempio di raccomandazione sui costi di Advisor](./media/billing-getting-started/advisor-action.png)

Vedere [Ottimizzare i costi grazie agli elementi consigliati](../cost-management/tutorial-acm-opt-recommendations.md) per un'esercitazione guidata sulle raccomandazioni di Advisor per il risparmio dei costi.

## <a name="review-costs-against-your-latest-invoice"></a>Esaminare i costi in base alla fattura più recente

Alla fine del ciclo di fatturazione, è disponibile la fattura più recente. È anche possibile [scaricare le fatture e i file dei dati dettagliati di utilizzo](billing-download-azure-invoice-daily-usage-date.md) per verificare che gli addebiti siano corretti. Per altre informazioni sul confronto tra i dati di utilizzo giornalieri e la fattura, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>API per la fatturazione

Usare l'API per la fatturazione di Azure per ottenere i dati di utilizzo a livello di codice. Usare insieme l'API RateCard e l'API Usage per ottenere l'utilizzo addebitato. Per altre informazioni, vedere [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Risorse aggiuntive e casi speciali

### <a name="ea-csp-and-sponsorship-customers"></a>Clienti EA, CSP e Sponsorship
Per iniziare, rivolgersi all'account manager o al partner di Azure.

| Offerta | Risorse |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contratto Enterprise Agreement (EA) | [Portale EA](https://ea.azure.com/), [documentazione della guida](https://ea.azure.com/helpdocs) e [report di Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Provider di soluzioni cloud (CSP) | Rivolgersi al provider |
| Azure Sponsorship | [Portale Sponsorship](https://www.microsoftazuresponsorships.com/) |

Se si è responsabili della gestione IT per un'organizzazione di grandi dimensioni, è consigliabile leggere l'articolo relativo allo [scaffolding aziendale Azure](/azure/architecture/cloud-adoption-guide/subscription-governance) e il [white paper per l'IT aziendale](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (download file PDF, solo in lingua inglese).

#### <a name="EA"></a> Visualizzazioni dei costi per i contratti Enterprise Agreement nel portale di Azure

Le visualizzazioni dei costi aziendali sono attualmente disponibili in versione di anteprima pubblica. Elementi da considerare:

- I costi di sottoscrizione sono basati sull'utilizzo e non includono importi prepagati, eccedenze, quantità incluse, rettifiche e imposte. I costi effettivi vengono calcolati a livello di registrazione.
- Gli importi visualizzati nel portale di Azure potrebbero essere diversi rispetto a quelli in Enterprise Portal. Gli aggiornamenti in Enterprise Portal potrebbero richiedere alcuni minuti prima che le modifiche vengano visualizzate nel portale di Azure.
- Se i costi non vengono visualizzati, potrebbe essere per uno dei motivi seguenti:
    - Non si dispone di autorizzazioni a livello di sottoscrizione. Per accedere alla visualizzazione dei costi aziendali, è necessario disporre del ruolo Fatturazione per lettore, Lettore, Collaboratore o Proprietario a livello di sottoscrizione.
    - Si è un proprietario di un account e l'amministratore delle registrazioni ha disabilitato l'impostazione di visualizzazione dei costi per i proprietari degli account.  Contattare l'amministratore delle registrazioni per ottenere l'accesso ai costi.
    - Si è un amministratore di reparto e l'amministratore delle registrazioni ha disabilitato l'impostazione di **visualizzazione dei costi per gli amministratori di reparto**.  Contattare l'amministratore delle registrazioni per ottenere l'accesso.
    - Azure è stato acquistato tramite un partner di canale, il quale non ha rilasciato informazioni sui prezzi.  
- Se si aggiornano le impostazioni relative all'accesso ai costi in Enterprise Portal, si verifica un ritardo di qualche minuto prima che le modifiche vengano visualizzate nel portale di Azure.
- Limite di spesa e materiale sussidiario di fatturazione non si applicano alle sottoscrizioni con contratto Enterprise.

### <a name="check-your-subscription-and-access"></a>Controllare la sottoscrizione e l'accesso

Per visualizzare i costi, è necessario l'[accesso a livello di sottoscrizione alle informazioni di fatturazione](billing-manage-access.md). Solo l'amministratore dell'account può accedere al [Centro account](https://account.azure.com/Subscriptions), modificare le informazioni di fatturazione e gestire le sottoscrizioni. L'amministratore dell'account è la persona che ha completato il processo di iscrizione. Per altre informazioni, vedere [Aggiungere o modificare i ruoli di amministratore di Azure che gestiscono la sottoscrizione o i servizi](billing-add-change-azure-subscription-administrator.md).

Per verificare se si è l'amministratore dell'account, passare a [Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Visualizzare l'elenco delle sottoscrizioni e cercare **Ruolo personale**. Se il ruolo è *Amministratore dell'account*, si hanno i privilegi completi. Se il ruolo indicato è diverso, ad esempio *Proprietario*, non si hanno privilegi completi.

![Screenshot del ruolo dell'utente nella visualizzazione Sottoscrizioni nel portale di Azure](./media/billing-getting-started/sub-blade-view.PNG)

Per gestire le sottoscrizioni e modificare le informazioni di fatturazione, [individuare l'amministratore dell'account](billing-subscription-transfer.md#whoisaa). Chiedere all'amministratore dell'account di completare le attività oppure di [trasferire la proprietà della sottoscrizione](billing-subscription-transfer.md).

Se l'amministratore dell'account non fa più parte dell'organizzazione ed è necessario gestire la fatturazione, [contattare il supporto tecnico](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Richiedere un credito del Contratto di servizio per un evento imprevisto del servizio

Il contratto di servizio definisce gli impegni di Microsoft in merito a tempi di attività e connettività. Viene segnalato un evento imprevisto del servizio quando i servizi di Azure riscontrano un problema che influisce sul tempo di esecuzione o sulla connettività, spesso indicato come un'*interruzione del servizio*. Se Microsoft non raggiunge e garantisce per ogni servizio i livelli di servizio come descritto nel contratto di servizio, l'utente può risultare idoneo a ricevere un credito per una parte dei corrispettivi mensili del servizio.

Per richiedere un credito:

1. Accedere al [portale di Azure](https://portal.azure.com/). Se si hanno più account, assicurarsi di usare quello interessato dal tempo di inattività di Azure. 
2. Creare una nuova richiesta di supporto.
3. In **Tipo di problema** selezionare **Fatturazione**.
4. In **Tipo di problema**, selezionare **Refund Request** (Richiesta di rimborso).
5. Aggiungere i dettagli per specificare che si sta richiedendo un credito per il contratto di servizio, menzionare data/ora/fuso orario e i servizi interessati (macchine virtuali, siti Web e così via)
6. Verificare i dettagli del contatto e selezionare **Crea** per inviare la richiesta.

Le soglie del contratto di servizio variano in base al servizio. Ad esempio al livello Web SQL è associato un contratto di servizio del 99,9%, alle macchine virtuali del 99,95% e al livello standard SQL del 99,99%.

Per alcuni servizi sono previsti alcuni prerequisiti per l'applicazione del contratto di servizio. Ad esempio, le macchine virtuali devono avere due o più istanze distribuite nello stesso set di disponibilità.

Per altre informazioni, vedere [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/) e [Riepilogo dei contratti di servizio per i servizi di Azure](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sull'uso dei [limiti di spesa](billing-spending-limit.md) per evitare costi eccessivi.
- Iniziare ad [analizzare i costi di Azure](../cost-management/quick-acm-cost-analysis.md).
