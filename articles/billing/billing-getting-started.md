---
title: Impedisci costi imprevisti e Gestisci la fatturazione in Azure
description: Informazioni su come evitare addebiti imprevisti nella fattura di Azure. Usare le funzionalità di gestione e rilevamento dei costi per una sottoscrizione di Azure.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: b64e84c3fff27675029ff35f27972a4aca014ec3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68612031"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Evitare addebiti imprevisti con la gestione dei costi e della fatturazione di Azure

Quando ci si iscrive ad Azure, è possibile eseguire diverse operazioni per ottenere una migliore idea della spesa:

- Il [calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/) può fornire una stima dei costi prima di creare una risorsa di Azure. 

- Il [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) fornisce la scomposizione e la previsione dei costi correnti. 

- Per raggruppare e conoscere i costi per le risorse usate per diversi progetti o team, esaminare i [tag delle risorse](../azure-resource-manager/resource-group-using-tags.md). Se l'organizzazione dispone di un sistema di creazione report, vedere le [API per la fatturazione](billing-usage-rate-card-overview.md).

- Se la sottoscrizione è stata creata da un Enterprise Agreement (EA), è possibile visualizzare i costi nel portale di Azure. Se la sottoscrizione è tramite un provider di soluzioni cloud (CSP) o Azure Sponsorship, alcune delle funzionalità seguenti potrebbero non essere applicabili all'utente. Per ulteriori informazioni, vedere [risorse aggiuntive per EA, CSP e Sponsorship](#other-offers).

- Se la sottoscrizione è un'offerta di valutazione gratuita, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure in Open (AIO) o BizSpark, la sottoscrizione viene disabilitata automaticamente quando vengono usati tutti i crediti. È possibile ottenere altre informazioni sui [limiti di spesa](#spending-limit) per evitare che la sottoscrizione venga disabilitata all'improvviso.

- Se hai effettuato l'iscrizione per ottenere un [account Azure gratuito](https://azure.microsoft.com/free/), [puoi usare gratuitamente alcuni dei servizi di Azure più diffusi per 12 mesi](billing-create-free-services-included-free-account.md). Oltre ai consigli elencati di seguito, vedere l'argomento su come [evitare di pagare l'account gratuito](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Ottenere una stima dei costi prima di aggiungere servizi di Azure

Di seguito sono riportate alcune informazioni aggiuntive sulla stima dei costi mediante gli strumenti seguenti:
- Calcolatore prezzi di Azure
- Portale di Azure
- Limite di spesa

Le immagini nelle sezioni seguenti illustrano i prezzi di esempio in dollari statunitensi.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Stimare i costi online con il calcolatore dei prezzi

Usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) per ottenere una stima del costo mensile del servizio a cui si è interessati. È possibile aggiungere qualsiasi risorsa di Azure per la prima entità per ottenere un costo stimato. Nel calcolatore dei prezzi è possibile modificare il tipo di valuta.

![Screenshot del menu del calcolatore dei prezzi](./media/billing-getting-started/pricing-calc.png)

Nel calcolatore dei prezzi, ad esempio, una macchina virtuale (VM) di Windows a1 è stimata per un costo di una determinata quantità/mese nelle ore di calcolo se viene lasciata in esecuzione per l'intera durata:

![Screenshot del calcolatore dei prezzi che mostra un costo stimato di una VM Windows a1 al mese](./media/billing-getting-started/pricing-calcvm.png)

Per ulteriori informazioni sui prezzi, vedere le [domande frequenti sui prezzi](https://azure.microsoft.com/pricing/faq/). Se si vuole comunicare con un venditore di Azure, chiamare il numero di telefono visualizzato nella parte superiore della pagina delle domande frequenti.

### <a name="review-estimated-costs-in-the-azure-portal"></a>Esaminare i costi stimati nella portale di Azure

In genere, quando si aggiunge un servizio nel portale di Azure, viene visualizzata una vista che mostra un costo stimato al mese nella valuta fatturata. Quando si scelgono le dimensioni di una VM Windows, ad esempio, viene visualizzato il costo mensile stimato per le ore di calcolo:

![Esempio: una VM Windows a1 che mostra i costi stimati al mese](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a> Controllare se è attivo un limite di spesa

Se si ha una sottoscrizione che usa crediti, il limite di spesa è attivato per impostazione predefinita. In questo modo, quando si esauriscono tutti i crediti non verrà effettuato alcun addebito sulla carta di credito. Vedere l'[elenco completo delle offerte di Azure e la disponibilità del limite di spesa](https://azure.microsoft.com/support/legal/offer-details/).

Tuttavia, quando si raggiunge il limite di spesa, i servizi vengono disabilitati. Le VM vengono quindi deallocate. Per evitare tempi di inattività del servizio, è necessario disattivare il limite di spesa. Qualsiasi eccedenza verrà addebitata sulla carta di credito in archivio.

Per verificare se è stato impostato un limite di spesa, passare alla [Visualizzazione sottoscrizioni nel centro account](https://account.windowsazure.com/Subscriptions). Se il limite di spesa è impostato su on, viene visualizzato un banner simile al seguente:

![Screenshot con avviso che informa che è attivo il limite di spesa nel Centro account](./media/billing-getting-started/spending-limit-banner.png)

Fare clic sul banner e seguire le istruzioni per rimuovere il limite di spesa. Se al momento dell'iscrizione non si sono immesse le informazioni relative alla carta di credito, per rimuovere il limite di spesa è necessario inserirle. Per altre informazioni, vedere [Limite di spesa di Azure - Come funziona e come abilitarlo o rimuoverlo](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="use-budgets-and-cost-alerts"></a>Usare i budget e gli avvisi relativi ai costi

È possibile creare [budget](../cost-management/tutorial-acm-create-budgets.md) per gestire i costi e creare [avvisi](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) che notificano automaticamente agli stakeholder le anomalie di spesa e i rischi. Gli avvisi si basano sulla spesa rispetto alle soglie di budget e dei costi.

## <a name="monitor-costs-when-using-azure-services"></a>Monitorare i costi quando si usano i servizi di Azure
È possibile monitorare i costi con gli strumenti seguenti:

- Tag
- Suddivisione dei costi e velocità
- Analisi dei costi

### <a name="tags"></a>Aggiungere tag alle risorse per raggruppare i dati di fatturazione

È possibile usare tag per raggruppare i dati di fatturazione per i servizi supportati. Ad esempio, se si eseguono più macchine virtuali per diversi team, è possibile usare i tag per suddividere i costi in base al centro di costo, ad esempio: HR, marketing, finanza e così via) o ambiente (ad esempio: produzione, pre-produzione, test).

![Schermata che mostra la configurazione dei tag nel portale](./media/billing-getting-started/tags.png)

I tag sono riportati in diverse visualizzazioni di report dei costi. Sono, ad esempio, visibili nella [visualizzazione analisi dei costi](#costs) immediatamente e nel file CSV di utilizzo dettagliato dopo il primo periodo di fatturazione.

Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a>Monitorare la suddivisione dei costi e la velocità

Dopo aver eseguito i servizi di Azure, controllare regolarmente gli addebiti. È possibile visualizzare la spesa e la velocità correnti nella portale di Azure.

1. Vedere il [pannello Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selezionare una sottoscrizione.

2. Se è supportata per la sottoscrizione, viene visualizzata la suddivisione dei costi e la velocità.

    ![Screenshot della velocità e della scomposizione nel portale di Azure](./media/billing-getting-started/burn-rate.PNG)

3. Fare clic su [analisi dei costi](../cost-management/quick-acm-cost-analysis.md) nell'elenco a sinistra per visualizzare la suddivisione dei costi per risorsa. Dopo aver aggiunto un servizio, attendere 24 ore per visualizzare i dati.

    ![Screenshot della visualizzazione dell'analisi dei costi nel portale di Azure](./media/billing-getting-started/cost-analysis.png)

4. È possibile filtrare la visualizzazione in base a diverse proprietà come [tag](#tags), tipo di risorsa, gruppo di risorse e intervallo di tempo. Fare clic su **applica** per confermare i filtri e **scaricare** se si desidera esportare la vista in un file con valori delimitati da virgole (CSV).

5. È anche possibile fare clic su una risorsa per visualizzare la cronologia di spesa e il costo giornaliero della risorsa.

    ![Screenshot della visualizzazione della cronologia di spesa nel portale di Azure](./media/billing-getting-started/costhistory.png)

Confrontare i costi visualizzati con le stime visualizzate quando sono stati selezionati i servizi. Se i costi sono significativamente diversi dalle stime, controllare il piano tariffario selezionato per le risorse.

## <a name="optimize-and-reduce-costs"></a>Ottimizzare e ridurre i costi
Se non si ha familiarità con i principi di gestione dei costi, vedere [come ottimizzare l'investimento nel cloud con gestione costi di Azure](../cost-management/cost-mgt-best-practices.md).

Nel portale di Azure è anche possibile ottimizzare e ridurre i costi di Azure con l'arresto automatico per le macchine virtuali e le raccomandazioni di Advisor.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Prendere in considerazione funzionalità di riduzione dei costi come l'arresto automatico per le macchine virtuali

A seconda dello scenario specifico, è possibile configurare l'arresto automatico delle macchine virtuali nel portale di Azure. Per altre informazioni, vedere il post [Auto shutdown for VMs using Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) (Arresto automatico delle macchine virtuali con Azure Resource Manager).

![Screenshot dell'opzione di arresto automatico nel portale](./media/billing-getting-started/auto-shutdown.png)

L'arresto automatico è diverso dall'arresto nella macchina virtuale con le opzioni di risparmio energia. L'arresto automatico arresta e dealloca le macchine virtuali per interrompere gli addebiti aggiuntivi per l'utilizzo. Per altre informazioni, vedere le domande frequenti sui prezzi per le [VM Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e le [VM Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) in relazione agli stati delle VM.

Per informazioni su altre funzionalità per la riduzione dei costi per gli ambienti di sviluppo e test, vedere [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Attivare e rivedere i consigli Azure Advisor

[Azure Advisor](../advisor/advisor-overview.md) consente di ridurre i costi identificando le risorse con utilizzo ridotto. Esaminare Advisor nel portale di Azure:

![Screenshot del pulsante Azure Advisor nel portale di Azure](./media/billing-getting-started/advisor-button.png)

È possibile ottenere indicazioni utili nella scheda **costo** del dashboard di Advisor:

![Screenshot di un esempio di raccomandazione sui costi di Advisor](./media/billing-getting-started/advisor-action.png)

Vedere l'esercitazione [ottimizzare i costi dalle raccomandazioni](../cost-management/tutorial-acm-opt-recommendations.md) per un'esercitazione guidata sulle raccomandazioni di Advisor per il risparmio di costi.

## <a name="review-costs-against-your-latest-invoice"></a>Esaminare i costi rispetto alla fattura più recente

Alla fine del ciclo di fatturazione, è disponibile la fattura più recente. È anche possibile [scaricare le fatture e i file di utilizzo dettagliati](billing-download-azure-invoice-daily-usage-date.md) per assicurarsi che siano stati addebitati correttamente. Per altre informazioni sul confronto tra i dati di utilizzo giornalieri e la fattura, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>API per la fatturazione

Usare l'API di fatturazione di Azure per ottenere i dati di utilizzo a livello di codice. Usare insieme l'API RateCard e l'API Usage per ottenere l'utilizzo addebitato. Per altre informazioni, vedere [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Risorse aggiuntive e casi speciali

### <a name="ea-csp-and-sponsorship-customers"></a>Clienti EA, CSP e Sponsorship
Per iniziare, rivolgersi all'account manager o al partner di Azure.

| Offerta | Risorse |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contratto Enterprise Agreement (EA) | [Portale EA](https://ea.azure.com/), [documentazione della guida](https://ea.azure.com/helpdocs) e [report di Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Provider di soluzioni cloud (CSP) | Rivolgersi al provider |
| Azure Sponsorship | [Portale Sponsorship](https://www.microsoftazuresponsorships.com/) |

Se si è responsabili della gestione IT per un'organizzazione di grandi dimensioni, è consigliabile leggere l'articolo relativo allo [scaffolding aziendale Azure](/azure/architecture/cloud-adoption-guide/subscription-governance) e il [white paper per l'IT aziendale](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (download file PDF, solo in lingua inglese).

#### <a name="EA"></a>Enterprise Agreement le visualizzazioni dei costi nell'portale di Azure

Le visualizzazioni dei costi aziendali sono attualmente disponibili in versione di anteprima pubblica. Elementi da considerare:

- I costi di sottoscrizione sono basati sull'utilizzo e non includono importi prepagati, eccedenze, quantità incluse, rettifiche e imposte. I costi effettivi vengono calcolati a livello di registrazione.
- Gli importi visualizzati nel portale di Azure potrebbero essere diversi rispetto a quelli in Enterprise Portal. Gli aggiornamenti in Enterprise Portal potrebbero richiedere alcuni minuti prima che le modifiche vengano visualizzate nel portale di Azure.
- Se i costi non vengono visualizzati, potrebbe essere per uno dei motivi seguenti:
    - Non si dispone di autorizzazioni a livello di sottoscrizione. Per accedere alla visualizzazione dei costi aziendali, è necessario disporre del ruolo Fatturazione per lettore, Lettore, Collaboratore o Proprietario a livello di sottoscrizione.
    - Si è un proprietario di un account e l'amministratore delle registrazioni ha disabilitato l'impostazione di visualizzazione dei costi per i proprietari degli account.  Contattare l'amministratore delle registrazioni per ottenere l'accesso ai costi.
    - Si è un amministratore del reparto e l'amministratore delle registrazioni ha disabilitato l'impostazione **Visualizza addebiti da** .  Contattare l'amministratore delle registrazioni per ottenere l'accesso.
    - Azure è stato acquistato tramite un partner di canale, il quale non ha rilasciato informazioni sui prezzi.  
- Se si aggiornano le impostazioni relative all'accesso ai costi in Enterprise Portal, si verifica un ritardo di qualche minuto prima che le modifiche vengano visualizzate nel portale di Azure.
- Limite di spesa e materiale sussidiario di fatturazione non si applicano alle sottoscrizioni con contratto Enterprise.

### <a name="check-your-subscription-and-access"></a>Controllare la sottoscrizione e l'accesso

Per visualizzare i costi, è necessario l'[accesso a livello di sottoscrizione alle informazioni di fatturazione](billing-manage-access.md). Solo l'amministratore dell'account può accedere al [Centro account](https://account.azure.com/Subscriptions), modificare le informazioni di fatturazione e gestire le sottoscrizioni. L'amministratore dell'account è la persona che ha completato il processo di iscrizione. Per altre informazioni, vedere [Aggiungere o modificare i ruoli di amministratore di Azure che gestiscono la sottoscrizione o i servizi](billing-add-change-azure-subscription-administrator.md).

Per verificare se si è l'amministratore dell'account, passare a [Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Visualizzare l'elenco delle sottoscrizioni e individuare il **ruolo**. Se l' *amministratore dell'account* è il ruolo, si dispone dei privilegi completi. Se viene indicato un altro elemento, ad esempio *owner*, non si dispone dei privilegi completi.

![Screenshot del ruolo dell'utente nella visualizzazione Sottoscrizioni nel portale di Azure](./media/billing-getting-started/sub-blade-view.PNG)

Per gestire le sottoscrizioni e modificare le informazioni di fatturazione, [individuare l'amministratore dell'account](billing-subscription-transfer.md#whoisaa). Rivolgersi all'amministratore dell'account per completare le attività o [trasferire la sottoscrizione](billing-subscription-transfer.md).

Se l'amministratore dell'account non fa più parte dell'organizzazione ed è necessario gestire la fatturazione, [contattare il supporto tecnico](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Richiedi un credito Contratto di servizio per un evento imprevisto del servizio

Il contratto di servizio definisce gli impegni di Microsoft in merito a tempi di attività e connettività. Un evento imprevisto del servizio viene segnalato quando i servizi di Azure riscontrano un problema che influisca sul tempo di esecuzione o sulla connettività, spesso indicato come un' *interruzione*del servizio. Se non si ottengono e si gestiscono i livelli di servizio per ogni servizio come descritto nel contratto di servizio, è possibile che l'utente sia idoneo per un credito per una parte delle tariffe mensili del servizio.

Per richiedere un credito:

1. Accedere al [portale di Azure](https://portal.azure.com/). Se si hanno più account, assicurarsi di usare quello interessato dal tempo di inattività di Azure. 
2. Creare una nuova richiesta di supporto.
3. In **tipo di problema**selezionare **fatturazione**.
4. In **tipo di problema**selezionare **richiesta di rimborso**.
5. Aggiungere i dettagli per specificare che si sta richiedendo un credito per il contratto di servizio, menzionare data/ora/fuso orario e i servizi interessati (VM, siti Web e così via)
6. Verificare i dettagli del contatto e selezionare **Crea** per inviare la richiesta.

Le soglie SLA variano in base al servizio. Ad esempio, il livello Web SQL dispone di un contratto di contratto del 99,9%, le macchine virtuali hanno un contratto di contratto del 99,95% e il livello SQL standard ha un contratto di contratto del 99,99%.

Per alcuni servizi, esistono prerequisiti per l'applicazione del contratto di servizio. Ad esempio, le macchine virtuali devono avere due o più istanze distribuite nello stesso set di disponibilità.

Per altre informazioni, vedere [contratti di servizio](https://azure.microsoft.com/support/legal/sla/) e [Riepilogo del contratto di servizio per la documentazione dei servizi di Azure](https://azure.microsoft.com/support/legal/sla/summary/) .

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Per eventuali domande o per richiedere assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sull'uso dei [limiti di spesa](billing-spending-limit.md) per evitare la sovraspesa.
- Inizia ad [analizzare i costi di Azure](../cost-management/quick-acm-cost-analysis.md).
