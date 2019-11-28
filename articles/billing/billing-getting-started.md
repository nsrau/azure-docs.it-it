---
title: Evitare costi imprevisti e gestire la fatturazione in Azure
description: Informazioni su come evitare addebiti imprevisti nella fattura di Azure. Usare le funzionalità di gestione e verifica dei costi per l'account Azure.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e5cdd7181e680dbc7406118afeb4aeb837d3aaf2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223901"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Evitare addebiti imprevisti con la gestione dei costi e della fatturazione di Azure

Quando si effettua l'iscrizione ad Azure, è consigliabile eseguire alcune operazioni per conoscere meglio la spesa sostenuta:

- Ottenere i costi stimati prima di aggiungere servizi usando il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/), l'elenco prezzi di Azure o durante l'aggiunta di servizi nel portale di Azure.
- Monitorare i costi con [budget ](../cost-management/tutorial-acm-create-budgets.md), [avvisi](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) e [analisi dei costi](../cost-management/quick-acm-cost-analysis.md).
- Esaminare gli addebiti in fattura confrontandoli con i [file di utilizzo dettagliato](billing-download-azure-invoice-daily-usage-date.md).
- Integrare i dati della fatturazione e dei costi con il proprio sistema di creazione di report usando le API per la [fatturazione](https://docs.microsoft.com/rest/api/billing/) e l'[utilizzo](https://docs.microsoft.com/rest/api/consumption/).
- Usare le risorse e gli strumenti aggiuntivi per i clienti con contratto Enterprise (EA), Cloud Solution Provider (CSP) e Azure Sponsorship.
- Sfruttare [gratuitamente alcuni dei servizi di Azure più diffusi per 12 mesi](billing-create-free-services-included-free-account.md) disponibili con l'[account Azure gratuito](https://azure.microsoft.com/free/). Oltre ai consigli elencati di seguito, vedere l'argomento su come [evitare di pagare l'account gratuito](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Ottenere una stima dei costi prima di aggiungere servizi di Azure

Usare uno degli strumenti seguenti per stimare il costo dell'utilizzo di un servizio di Azure:
- Calcolatore prezzi di Azure
- Elenco prezzi di Azure
- Portale di Azure

Nelle immagini delle sezioni seguenti i prezzi di esempio sono indicati in dollari statunitensi.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Stimare i costi online con il calcolatore dei prezzi

Usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) per ottenere una stima del costo mensile del servizio che si vuole aggiungere. È possibile modificare la valuta per ottenere la stima nella valuta locale.

![Screenshot del menu del calcolatore dei prezzi](./media/billing-getting-started/pricing-calc.png)

È possibile visualizzare il costo stimato per qualsiasi servizio proprietario di Azure. Nello screenshot seguente, ad esempio, per una macchina virtuale Windows A1 mantenuta in esecuzione per tutto il tempo, il costo stimato in ore di calcolo è $ 66,96 al mese:

![Screenshot del calcolatore dei prezzi che mostra che il costo stimato mensile di una macchina virtuale Windows A1](./media/billing-getting-started/pricing-calcvm.png)

Per altre informazioni sui prezzi, vedere [Domande frequenti sui prezzi](https://azure.microsoft.com/pricing/faq/). Per contattare un venditore di Azure, chiamare il numero di telefono indicato nella parte superiore della pagina delle domande frequenti.

### <a name="view-and-download-azure-price-sheet"></a>Visualizzare e scaricare l'elenco prezzi di Azure

Se si ha accesso ad Azure tramite un contratto Enterprise (EA) o un Contratto del cliente Microsoft, è possibile visualizzare e scaricare l'elenco prezzi per l'account Azure. L'elenco prezzi è un file di Excel che contiene i prezzi per tutti i servizi di Azure. Per altre informazioni, vedere [Visualizzare e scaricare i prezzi di Azure](billing-ea-pricing.md).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Esaminare i costi stimati nel portale di Azure

È possibile visualizzare il costo stimato al mese durante l'aggiunta di un servizio nel portale di Azure. Quando si scelgono le dimensioni di una VM Windows, ad esempio, viene visualizzato il costo mensile stimato per le ore di calcolo:

![Esempio: costo stimato mensile di una macchina virtuale Windows A1](./media/billing-getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Monitorare i costi quando si usano i servizi di Azure
È possibile monitorare i costi con gli strumenti seguenti:

- Budget e avvisi per i costi
- Analisi dei costi

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Tenere traccia dei costi con i budget e gli avvisi per i costi

È possibile creare [budget](../cost-management/tutorial-acm-create-budgets.md) per gestire i costi e creare [avvisi](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) per informare automaticamente l'utente e gli stakeholder in caso di anomalie di spesa e rischi di costi eccessivi.

### <a name="costs"></a> Esplorare e analizzare i costi con l'analisi dei costi

Una volta che i servizi di Azure sono in esecuzione, controllare regolarmente i costi per monitorare la spesa di Azure. È possibile usare l'analisi dei costi per ottenere informazioni sull'origine dei costi per l'utilizzo di Azure.

1. Visitare la pagina [Gestione costi e fatturazione nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. Fare clic su **Analisi dei costi** sul lato sinistro della schermata per visualizzare i costi correnti suddivisi in base a vari pivot, ad esempio servizio, località e sottoscrizione. Dopo aver aggiunto o acquistato un servizio, è necessario attendere 24 ore prima che i dati vengano visualizzati. Per impostazione predefinita, l'analisi dei costi indica i costi per l'ambito corrente. Ad esempio, nello screenshot seguente viene visualizzato il costo per l'account di fatturazione Contoso. Usare l'etichetta Ambito per passare a un ambito diverso nell'analisi dei costi. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](../cost-management/understand-work-scopes.md#scopes)

    ![Screenshot della visualizzazione dell'analisi dei costi nel portale di Azure](./media/billing-getting-started/cost-analysis.png)

4. È possibile filtrare in base a diverse proprietà, ad esempio tag, tipo di risorsa e intervallo di tempo. Fare clic su **Aggiungi filtro** per aggiungere il filtro per una proprietà e selezionare i valori da filtrare. Selezionare **Esporta** per esportare la visualizzazione in un file con valori delimitati da virgole (CSV).

5. È inoltre possibile fare clic sulle etichette del grafico per visualizzare la cronologia della spesa giornaliera per l'etichetta corrispondente. Ad esempio: Nello screenshot seguente quando si fa clic sulle macchine virtuali, viene visualizzato il costo giornaliero per l'esecuzione delle macchine virtuali.

    ![Screenshot della visualizzazione della cronologia di spesa nel portale di Azure](./media/billing-getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Ottimizzare e ridurre i costi
Se non si conoscono ancora i principi della gestione costi, leggere [Come ottimizzare gli investimenti per il cloud con Gestione costi di Azure](../cost-management/cost-mgt-best-practices.md).

Nel portale di Azure è anche possibile ottimizzare e ridurre i costi di Azure con l'arresto automatico per le macchine virtuali e le raccomandazioni di Advisor.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Provare ad usare funzionalità per la riduzione dei costi come l'arresto automatico delle macchine virtuali

A seconda dello scenario specifico, è possibile configurare l'arresto automatico delle macchine virtuali nel portale di Azure. Per altre informazioni, vedere il post [Auto shutdown for VMs using Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) (Arresto automatico delle macchine virtuali con Azure Resource Manager).

![Screenshot dell'opzione di arresto automatico nel portale](./media/billing-getting-started/auto-shutdown.png)

L'arresto automatico è diverso dall'arresto nella macchina virtuale con le opzioni di risparmio energia. L'arresto automatico arresta e dealloca le macchine virtuali per interrompere gli addebiti aggiuntivi per l'utilizzo. Per altre informazioni, vedere le domande frequenti sui prezzi per le [VM Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e le [VM Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) in relazione agli stati delle VM.

Per informazioni su altre funzionalità per la riduzione dei costi per gli ambienti di sviluppo e test, vedere [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Attivare e rivedere le raccomandazioni di Azure Advisor

[Azure Advisor](../advisor/advisor-overview.md) consente di ridurre i costi identificando le risorse a basso utilizzo. Cercare **Advisor** nel portale di Azure:

![Screenshot del pulsante Azure Advisor nel portale di Azure](./media/billing-getting-started/advisor-button.png)

Selezionare **Costo** nella parte sinistra. Nella scheda **Costo** verranno visualizzate le raccomandazioni operative:

![Screenshot di un esempio di raccomandazione sui costi di Advisor](./media/billing-getting-started/advisor-action.png)

Vedere [Ottimizzare i costi grazie agli elementi consigliati](../cost-management/tutorial-acm-opt-recommendations.md) per un'esercitazione guidata sulle raccomandazioni di Advisor per il risparmio dei costi.

## <a name="review-charges-against-your-latest-invoice"></a>Esaminare gli addebiti in base alla fattura più recente

Alla fine del ciclo di fatturazione, è disponibile la fattura. È anche possibile [scaricare le fatture e i file di utilizzo dettagliati](billing-download-azure-invoice-daily-usage-date.md) e confrontarli per verificare che gli addebiti siano corretti. Per altre informazioni sul confronto tra i dati di utilizzo giornalieri e la fattura, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md).

Se si usa Azure tramite un Contratto del cliente Microsoft, è anche possibile [confrontare la fattura con le transazioni ](billing-mca-understand-your-bill.md#view-transactions-for-an-invoice-in-the-azure-portal) per comprendere gli addebiti nella fattura.

## <a name="integrate-with-billing-and-consumption-apis"></a>Integrazione con le API di fatturazione e utilizzo

Usare le API di [fatturazione](https://docs.microsoft.com/rest/api/billing/) e [utilizzo](https://docs.microsoft.com/rest/api/consumption/) di Azure per ottenere i dati relativi alla fatturazione e ai costi a livello di codice. Usare insieme l'API RateCard e l'API Usage per ottenere l'utilizzo addebitato. Per altre informazioni, vedere [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Risorse aggiuntive e casi speciali

### <a name="ea-csp-and-sponsorship-customers"></a>Clienti EA, CSP e Sponsorship
Per iniziare, rivolgersi all'account manager o al partner di Azure.

| Offerta | Risorse |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contratto Enterprise Agreement (EA) | [Portale EA](https://ea.azure.com/), [documentazione della guida](https://ea.azure.com/helpdocs) e [report di Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Provider di soluzioni cloud (CSP) | Rivolgersi al provider |
| Azure Sponsorship | [Portale Sponsorship](https://www.microsoftazuresponsorships.com/) |

Se si è responsabili della gestione IT per un'organizzazione di grandi dimensioni, è consigliabile leggere l'articolo relativo allo [scaffolding aziendale Azure](/azure/architecture/cloud-adoption-guide/subscription-governance) e il [white paper per l'IT aziendale](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (download file PDF, solo in lingua inglese).

### <a name="EA"></a> Visualizzazioni dei costi per i contratti Enterprise Agreement nel portale di Azure

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

Per visualizzare i costi, è necessario l'accesso a livello di account o di sottoscrizione alle informazioni sui costi o sulla fatturazione. L'accesso varia in base al tipo di account di fatturazione. Per altre informazioni sugli account di fatturazione e su come verificare il tipo di account di fatturazione, vedere [Visualizzare gli account di fatturazione nel portale di Azure](billing-view-all-accounts.md).

Se si ha accesso ad Azure tramite un account di fatturazione del Programma dei Microsoft Online Services, vedere [Gestire l'accesso alle informazioni di fatturazione per Azure](billing-manage-access.md).

Se si ha accesso ad Azure tramite un account di fatturazione del contratto Enterprise (EA), vedere [Informazioni sui ruoli amministrativi per il contratto Enterprise di Azure in Azure](billing-understand-ea-roles.md).

Se si ha accesso ad Azure tramite un account di fatturazione del Contratto del cliente Microsoft, vedere [Informazioni sui ruoli amministrativi per i contratti dei clienti Microsoft in Azure](billing-understand-mca-roles.md).

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
