---
title: Evitare addebiti imprevisti con Gestione dei costi e fatturazione di Azure
description: Informazioni su come evitare addebiti imprevisti nella fattura di Azure e usare le funzionalità di gestione e verifica dei costi per l'account Azure.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 1b63b0316b8314e4c5107ddd3f1d22c1d59cbb08
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281804"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Evitare addebiti imprevisti con la gestione dei costi e della fatturazione di Azure

Quando si effettua l'iscrizione ad Azure, è consigliabile eseguire alcune operazioni per conoscere meglio la spesa sostenuta:

- Ottenere i costi stimati prima di aggiungere servizi usando il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/), l'elenco prezzi di Azure o durante l'aggiunta di servizi nel portale di Azure.
- Monitorare i costi con [budget ](../costs/tutorial-acm-create-budgets.md), [avvisi](../costs/cost-mgt-alerts-monitor-usage-spending.md) e [analisi dei costi](../costs/quick-acm-cost-analysis.md).
- Esaminare gli addebiti in fattura confrontandoli con i [file di utilizzo dettagliato](download-azure-invoice-daily-usage-date.md).
- Integrare i dati della fatturazione e dei costi con il proprio sistema di creazione di report usando le API per la [fatturazione](https://docs.microsoft.com/rest/api/billing/) e l'[utilizzo](https://docs.microsoft.com/rest/api/consumption/).
- Usare le risorse e gli strumenti aggiuntivi per i clienti con contratto Enterprise (EA), Cloud Solution Provider (CSP) e Azure Sponsorship.
- Sfruttare [gratuitamente alcuni dei servizi di Azure più diffusi per 12 mesi](create-free-services.md) disponibili con l'[account Azure gratuito](https://azure.microsoft.com/free/). Oltre ai consigli elencati di seguito, vedere l'argomento su come [evitare di pagare l'account gratuito](avoid-charges-free-account.md).

Se è necessario annullare la sottoscrizione di Azure, vedere [Annullare la sottoscrizione di Azure](cancel-azure-subscription.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Ottenere una stima dei costi prima di aggiungere servizi di Azure

Usare uno degli strumenti seguenti per stimare il costo dell'utilizzo di un servizio di Azure:
- Calcolatore prezzi di Azure
- Elenco prezzi di Azure
- Portale di Azure

Nelle immagini delle sezioni seguenti i prezzi di esempio sono indicati in dollari statunitensi.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Stimare i costi online con il calcolatore dei prezzi

Usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) per ottenere una stima del costo mensile del servizio che si vuole aggiungere. È possibile modificare la valuta per ottenere la stima nella valuta locale.

![Screenshot del menu del calcolatore dei prezzi](./media/getting-started/pricing-calc.png)

È possibile visualizzare il costo stimato per qualsiasi servizio proprietario di Azure. Nello screenshot seguente, ad esempio, per una macchina virtuale Windows A1 mantenuta in esecuzione per tutto il tempo, il costo stimato in ore di calcolo è $ 66,96 al mese:

![Screenshot del calcolatore dei prezzi che mostra che il costo stimato mensile di una macchina virtuale Windows A1](./media/getting-started/pricing-calcvm.png)

Per altre informazioni sui prezzi, vedere [Domande frequenti sui prezzi](https://azure.microsoft.com/pricing/faq/). Per contattare un venditore di Azure, chiamare il numero di telefono indicato nella parte superiore della pagina delle domande frequenti.

### <a name="view-and-download-azure-price-sheet"></a>Visualizzare e scaricare l'elenco prezzi di Azure

Se si ha accesso ad Azure tramite un contratto Enterprise (EA) o un Contratto del cliente Microsoft, è possibile visualizzare e scaricare l'elenco prezzi per l'account Azure. L'elenco prezzi è un file di Excel che contiene i prezzi per tutti i servizi di Azure. Per altre informazioni, vedere [Visualizzare e scaricare i prezzi di Azure](ea-pricing.md).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Esaminare i costi stimati nel portale di Azure

È possibile visualizzare il costo stimato al mese durante l'aggiunta di un servizio nel portale di Azure. Quando si scelgono le dimensioni di una VM Windows, ad esempio, viene visualizzato il costo mensile stimato per le ore di calcolo:

![Esempio: costo stimato mensile di una macchina virtuale Windows A1](./media/getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Monitorare i costi quando si usano i servizi di Azure
È possibile monitorare i costi con gli strumenti seguenti:

- Budget e avvisi per i costi
- Analisi dei costi

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Tenere traccia dei costi con i budget e gli avvisi per i costi

È possibile creare [budget](../costs/tutorial-acm-create-budgets.md) per gestire i costi e creare [avvisi](../costs/cost-mgt-alerts-monitor-usage-spending.md) per informare automaticamente l'utente e gli stakeholder in caso di anomalie di spesa e rischi di costi eccessivi.

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> Esplorare e analizzare i costi con l'analisi dei costi

Una volta che i servizi di Azure sono in esecuzione, controllare regolarmente i costi per monitorare la spesa di Azure. È possibile usare l'analisi dei costi per ottenere informazioni sull'origine dei costi per l'utilizzo di Azure.

1. Visitare la pagina [Gestione costi e fatturazione nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. Fare clic su **Analisi dei costi** sul lato sinistro della schermata per visualizzare i costi correnti suddivisi in base a vari pivot, ad esempio servizio, località e sottoscrizione. Dopo aver aggiunto o acquistato un servizio, è necessario attendere 24 ore prima che i dati vengano visualizzati. Per impostazione predefinita, l'analisi dei costi indica i costi per l'ambito corrente. Ad esempio, nello screenshot seguente viene visualizzato il costo per l'account di fatturazione Contoso. Usare l'etichetta Ambito per passare a un ambito diverso nell'analisi dei costi. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](../costs/understand-work-scopes.md#scopes)

    ![Screenshot della visualizzazione dell'analisi dei costi nel portale di Azure](./media/getting-started/cost-analysis.png)

4. È possibile filtrare in base a diverse proprietà, ad esempio tag, tipo di risorsa e intervallo di tempo. Fare clic su **Aggiungi filtro** per aggiungere il filtro per una proprietà e selezionare i valori da filtrare. Selezionare **Esporta** per esportare la visualizzazione in un file con valori delimitati da virgole (CSV).

5. È inoltre possibile fare clic sulle etichette del grafico per visualizzare la cronologia della spesa giornaliera per l'etichetta corrispondente. Ad esempio: Nello screenshot seguente quando si fa clic sulle macchine virtuali, viene visualizzato il costo giornaliero per l'esecuzione delle macchine virtuali.

    ![Screenshot della visualizzazione della cronologia di spesa nel portale di Azure](./media/getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Ottimizzare e ridurre i costi
Se non si conoscono ancora i principi della gestione costi, leggere [Come ottimizzare gli investimenti per il cloud con Gestione costi di Azure](../costs/cost-mgt-best-practices.md).

Nel portale di Azure è anche possibile ottimizzare e ridurre i costi di Azure con l'arresto automatico per le macchine virtuali e le raccomandazioni di Advisor.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Provare ad usare funzionalità per la riduzione dei costi come l'arresto automatico delle macchine virtuali

A seconda dello scenario specifico, è possibile configurare l'arresto automatico delle macchine virtuali nel portale di Azure. Per altre informazioni, vedere il post [Auto shutdown for VMs using Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) (Arresto automatico delle macchine virtuali con Azure Resource Manager).

![Screenshot dell'opzione di arresto automatico nel portale](./media/getting-started/auto-shutdown.png)

L'arresto automatico è diverso dall'arresto nella macchina virtuale con le opzioni di risparmio energia. L'arresto automatico arresta e dealloca le macchine virtuali per interrompere gli addebiti aggiuntivi per l'utilizzo. Per altre informazioni, vedere le domande frequenti sui prezzi per le [VM Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e le [VM Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) in relazione agli stati delle VM.

Per informazioni su altre funzionalità per la riduzione dei costi per gli ambienti di sviluppo e test, vedere [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Attivare e rivedere le raccomandazioni di Azure Advisor

[Azure Advisor](../../advisor/advisor-overview.md) consente di ridurre i costi identificando le risorse a basso utilizzo. Cercare **Advisor** nel portale di Azure:

![Screenshot del pulsante Azure Advisor nel portale di Azure](./media/getting-started/advisor-button.png)

Selezionare **Costo** nella parte sinistra. Nella scheda **Costo** verranno visualizzate le raccomandazioni operative:

![Screenshot di un esempio di raccomandazione sui costi di Advisor](./media/getting-started/advisor-action.png)

Vedere [Ottimizzare i costi grazie agli elementi consigliati](../costs/tutorial-acm-opt-recommendations.md) per un'esercitazione guidata sulle raccomandazioni di Advisor per il risparmio dei costi.


## <a name="integrate-with-billing-and-consumption-apis"></a>Integrazione con le API di fatturazione e utilizzo

Usare le API di [fatturazione](https://docs.microsoft.com/rest/api/billing/) e [utilizzo](https://docs.microsoft.com/rest/api/consumption/) di Azure per ottenere i dati relativi alla fatturazione e ai costi a livello di codice. Usare insieme l'API RateCard e l'API Usage per ottenere l'utilizzo addebitato. Per altre informazioni, vedere [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure](usage-rate-card-overview.md).

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> Risorse aggiuntive e casi speciali

### <a name="ea-csp-and-sponsorship-customers"></a>Clienti EA, CSP e Sponsorship
Per iniziare, rivolgersi all'account manager o al partner di Azure.

| Offerta | Risorse |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contratto Enterprise Agreement (EA) | [Portale EA](https://ea.azure.com/), [documentazione della guida](https://ea.azure.com/helpdocs) e [report di Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Provider di soluzioni cloud (CSP) | Rivolgersi al provider |
| Azure Sponsorship | [Portale Sponsorship](https://www.microsoftazuresponsorships.com/) |

Se si è responsabili della gestione IT per un'organizzazione di grandi dimensioni, è consigliabile leggere l'articolo relativo allo [scaffolding aziendale Azure](/azure/architecture/cloud-adoption-guide/subscription-governance) e il [white paper per l'IT aziendale](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (download file PDF, solo in lingua inglese).

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a> Visualizzazioni dei costi per i contratti Enterprise Agreement nel portale di Azure

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

Per visualizzare i costi, è necessario l'accesso a livello di account o di sottoscrizione alle informazioni sui costi o sulla fatturazione. L'accesso varia in base al tipo di account di fatturazione. Per altre informazioni sugli account di fatturazione e su come verificare il tipo di account di fatturazione, vedere [Visualizzare gli account di fatturazione nel portale di Azure](view-all-accounts.md).

Se si ha accesso ad Azure tramite un account di fatturazione del Programma dei Microsoft Online Services, vedere [Gestire l'accesso alle informazioni di fatturazione per Azure](manage-billing-access.md).

Se si ha accesso ad Azure tramite un account di fatturazione del contratto Enterprise (EA), vedere [Informazioni sui ruoli amministrativi per il contratto Enterprise di Azure in Azure](understand-ea-roles.md).

Se si ha accesso ad Azure tramite un account di fatturazione del Contratto del cliente Microsoft, vedere [Informazioni sui ruoli amministrativi per i contratti dei clienti Microsoft in Azure](understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Richiedere un credito del Contratto di servizio per un evento imprevisto del servizio

Il contratto di servizio definisce gli impegni di Microsoft in merito a tempi di attività e connettività. Viene segnalato un evento imprevisto del servizio quando i servizi di Azure riscontrano un problema che influisce sul tempo di esecuzione o sulla connettività, spesso indicato come un'*interruzione del servizio*. Se Microsoft non raggiunge e garantisce per ogni servizio i livelli di servizio come descritto nel contratto di servizio, l'utente può risultare idoneo a ricevere un credito per una parte dei corrispettivi mensili del servizio.

Per richiedere un credito:

1. Accedere al [portale di Azure](https://portal.azure.com/). Se si hanno più account, assicurarsi di usare quello interessato dal tempo di inattività di Azure.
2. Creare una nuova richiesta di supporto.
3. In **Tipo di problema** selezionare **Fatturazione**.
4. In **Tipo di problema**, selezionare **Refund Request** (Richiesta di rimborso).
5. Aggiungere i dettagli per specificare che si sta richiedendo un credito per il contratto di servizio, menzionare data/ora/fuso orario e i servizi interessati (macchine virtuali, siti Web e così via).
6. Verificare i dettagli del contatto e selezionare **Crea** per inviare la richiesta.

Le soglie del contratto di servizio variano in base al servizio. Ad esempio al livello Web SQL è associato un contratto di servizio del 99,9%, alle macchine virtuali del 99,95% e al livello standard SQL del 99,99%.

Per alcuni servizi sono previsti alcuni prerequisiti per l'applicazione del contratto di servizio. Ad esempio, le macchine virtuali devono avere due o più istanze distribuite nello stesso set di disponibilità.

Per altre informazioni, vedere [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/) e [Riepilogo dei contratti di servizio per i servizi di Azure](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="analyze-unexpected-charges"></a>Analizzare gli addebiti imprevisti

L'infrastruttura di risorse cloud creata per l'organizzazione è probabilmente complessa. Molti tipi di risorse di Azure possono prevedere tipi di addebiti diversi. Le risorse di Azure potrebbero essere di proprietà di diversi team nell'organizzazione ed essere caratterizzate da tipi di modelli di fatturazione diversi in base alle risorse. Per comprendere meglio gli addebiti, iniziare l'analisi usando una o più delle strategie descritte nelle sezioni seguenti.

### <a name="review-your-invoice-and-identify-the-resource-that-is-responsible-for-the-charge"></a>Verificare la fattura e identificare la risorsa responsabile dell'addebito

Il modo in cui si acquistano i servizi di Azure consente di determinare la metodologia e gli strumenti disponibili quando si identifica la risorsa associata a un addebito. Per determinare quale metodologia si applica, è prima necessario [determinare il tipo di offerta di Azure](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Quindi, identificare la categoria cliente nell'elenco di [offerte di Azure supportate](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

Gli articoli seguenti illustrano le procedure dettagliate che spiegano come esaminare la fattura in base al tipo di cliente. In ogni articolo sono disponibili istruzioni su come scaricare un file CSV contenente i dettagli relativi all'utilizzo e ai costi per un determinato periodo di fatturazione.

- [Processo di revisione della fattura con pagamento in base al consumo](../understand/review-individual-bill.md#compare-invoiced-charges-with-usage-file)
- [Processo di revisione della fattura del Contratto Enterprise](../understand/review-enterprise-agreement-bill.md)
- [Processo di revisione della fattura del Contratto del cliente Microsoft](../understand/review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Processo di revisione della fattura del Contratto Microsoft Partner](../understand/review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

La fattura di Azure aggrega i costi per il mese in base al _contatore_. I contatori vengono usati per tenere traccia dell'utilizzo di una risorsa nel tempo e per calcolare la fattura. Quando si crea una singola risorsa di Azure, come una macchina virtuale, vengono create una o più istanze del contatore per la risorsa.

Filtrare il file CSV di utilizzo in base a _MeterName_, come illustrato nella fattura da analizzare per visualizzare tutte le voci che si applicano al contatore. Il valore di _InstanceID_ per la voce corrisponde alla risorsa di Azure effettiva che ha generato l'addebito.

Una volta identificata la risorsa in questione, è possibile usare Analisi dei costi in Gestione costi di Azure per analizzare ulteriormente i costi correlati alla risorsa. Per altre informazioni sull'uso dell'analisi dei costi, vedere [Avviare l'analisi dei costi](../costs/quick-acm-cost-analysis.md).

### <a name="review-invoiced-charges-in-cost-analysis"></a>Esaminare le tariffe fatturate in Analisi dei costi

Per visualizzare i dettagli della fattura nel portale di Azure, passare ad Analisi dei costi per l'ambito associato alla fattura che si sta analizzando. Selezionare la visualizzazione **Dettagli della fattura**, in cui vengono mostrati gli addebiti indicati nella fattura.

[![Esempio che mostra i dettagli della fattura](./media/getting-started/invoice-details.png)](./media/getting-started/invoice-details.png#lightbox)

La visualizzazione dei dettagli della fattura consente di identificare il servizio con costi imprevisti e determinare le risorse direttamente associate alla risorsa in Analisi dei costi. Se, ad esempio, si vogliono analizzare gli addebiti per il servizio Macchine virtuali, passare alla visualizzazione **Costi accumulati**. Impostare quindi la granularità su **Giornaliera**, filtrare gli addebiti per **Nome del servizio: Macchine virtuali** e raggruppare gli addebiti per **Risorsa**.

[![Esempio che mostra i costi accumulati per Macchine virtuali](./media/getting-started/virtual-machines.png)](./media/getting-started/virtual-machines.png#lightbox)


### <a name="identify-spikes-in-cost-over-time"></a>Identificare i picchi dei costi nel tempo

In alcuni casi è possibile che non si conoscano i costi recenti che hanno comportato variazioni rispetto agli addebiti fatturati. Per comprendere tali variazioni, è possibile usare l'analisi dei costi per [visualizzare un dettaglio giornaliero o mensile dei costi nel tempo](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). Dopo aver creato la visualizzazione, raggruppare gli addebiti in base a **Servizio** o **Risorsa** per identificare le modifiche. È anche possibile modificare la visualizzazione in un **grafico a linee** per visualizzare meglio i dati.

![Esempio che mostra i costi nel tempo nell'analisi dei costi](./media/getting-started/costs-over-time.png)

### <a name="determine-resource-pricing-and-understand-its-billing-model"></a>Determinare i prezzi delle risorse e comprendere il modello di fatturazione

Una singola risorsa può accumulare addebiti generati da più prodotti e servizi di Azure. Per altre informazioni sui prezzi di ogni servizio di Azure, vedere la pagina relativa ai [prezzi di Azure per prodotto](https://azure.microsoft.com/pricing/#product-pricing). Ad esempio, per una singola macchina virtuale creata in Azure potrebbero essere creati i contatori seguenti per tenere traccia dell'utilizzo. Ognuno di essi potrebbe prevedere prezzi diversi.

- Ore di calcolo
- Ore indirizzo IP
- Trasferimento dati in entrata
- Trasferimento dati in uscita
- Disco gestito Standard
- Operazioni disco gestito Standard
- I/O Standard - Disco
- I/O Standard - Lettura BLOB in blocchi
- I/O Standard - Scrittura BLOB in blocchi
- I/O Standard - Eliminazione BLOB in blocchi

Dopo la creazione della VM, ogni contatore inizia a emettere record di utilizzo. L'utilizzo e il prezzo del contatore vengono monitorati nel sistema di misurazione di Azure. È possibile visualizzare i contatori usati per calcolare la fattura nel file CSV di utilizzo.

### <a name="find-the-people-responsible-for-the-resource-and-engage-them"></a>Trovare le persone responsabili della risorsa e coinvolgerle

In genere il team responsabile di una determinata risorsa è a conoscenza delle modifiche apportate a una risorsa. Il loro coinvolgimento è utile quando si identificano i motivi degli addebiti. Ad esempio, il team proprietario potrebbe avere creato la risorsa di recente, averne aggiornato lo SKU (modificando quindi la frequenza della risorsa) o aumentato il carico sulla risorsa a causa di modifiche del codice. Continuare a leggere le sezioni seguenti per altre tecniche per determinare il proprietario di una risorsa.

#### <a name="analyze-the-audit-logs-for-the-resource"></a>Analizzare i log di controllo per la risorsa

Se si è autorizzati a visualizzare una risorsa, dovrebbe essere possibile accedere ai log di controllo. Esaminare i log per trovare l'utente responsabile delle ultime modifiche apportate a una risorsa. Per altre informazioni, vedere [Visualizzare e recuperare gli eventi del log attività di Azure](../../azure-monitor/platform/activity-log-view.md).

#### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Analizzare le autorizzazioni utente per l'ambito padre della risorsa

Gli utenti con accesso in scrittura a una sottoscrizione o a un gruppo di risorse hanno in genere informazioni sulle risorse create. Dovrebbero quindi essere in grado di spiegare lo scopo di una risorsa o di indicare la persona in possesso di tali informazioni. Per identificare gli utenti con autorizzazioni per un ambito della sottoscrizione, vedere [Visualizzare le assegnazioni di ruolo](../../role-based-access-control/check-access.md#view-role-assignments). È possibile usare un processo simile per i gruppi di risorse.

### <a name="get-help-to-identify-charges"></a>Richiedere assistenza per identificare gli addebiti

Se sono state usate le strategie precedenti e il motivo dell'addebito non è tuttora chiaro o se sono necessarie altre informazioni sui problemi di fatturazione, [Creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sull'uso dei [limiti di spesa](spending-limit.md) per evitare costi eccessivi.
- Iniziare ad [analizzare i costi di Azure](../costs/quick-acm-cost-analysis.md).
