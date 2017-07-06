---
title: Evitare costi imprevisti e gestire la fatturazione - Azure | Microsoft Docs
description: "Informazioni su come evitare addebiti imprevisti nella fattura di Azure. Usare le funzionalità di gestione e verifica dei costi per una sottoscrizione di Microsoft Azure."
services: 
documentationcenter: 
author: jlian
manager: tonguyen
editor: 
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: jlian
ms.translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 539a3e628dfec1088f683652bc9b743b87875d36
ms.contentlocale: it-it
ms.lasthandoff: 04/26/2017


---
# <a name="prevent-unexpected-costs-with-azure-billing-and-cost-management"></a>Evitare costi imprevisti con la gestione dei costi e alla fatturazione di Azure

Quando si effettua l'iscrizione ad Azure, è consigliabile eseguire alcune operazioni per conoscere meglio la spesa sostenuta. Nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), quando si seleziona la sottoscrizione, è possibile visualizzare la scomposizione dei costi e la velocità attuali, nonché [scaricare le fatture precedenti e i file dei dati dettagliati di uso](billing-download-azure-invoice-daily-usage-date.md). Se si desidera raggruppare i costi per le risorse usate per diversi progetti o team, esaminare i [tag delle risorse](../azure-resource-manager/resource-group-using-tags.md). Se l'organizzazione dispone di un sistema di creazione report, vedere le [API per la fatturazione](billing-usage-rate-card-overview.md). 

Per altre informazioni sui dati di utilizzo giornalieri, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md).

Molte funzionalità descritte in questo articolo non si applicano ai clienti che hanno una sottoscrizione con Cloud Solution Provider (CSP), Azure Sponsorship o con contratto Enterprise Agreement (EA). Al contrario, l'utente può usare diversi set di strumenti per la gestione dei costi. Vedere [Risorse aggiuntive per EA, CSP e Sponsorship](#other-offers).

Se la sottoscrizione è una versione di valutazione gratuita, di [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure in Open (AIO) o BizSpark, consultare la sezione sui [limiti di spesa](#spending-limit) per evitare che la sottoscrizione venga disabilitata all'improvviso. 

## <a name="day-0-before-you-add-azure-services"></a>Giorno 0: prima di aggiungere servizi di Azure

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Stimare i costi online con il calcolatore dei prezzi

Usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) e il [calcolatore del costo totale di proprietà](https://aka.ms/azure-tco-calculator) per ottenere una stima del costo mensile del servizio a cui si è interessati. Per una macchina virtuale (VM) Windows A1 mantenuta costantemente in esecuzione, ad esempio, il costo stimato in ore di calcolo è $66,96 USD/mese:

![Screenshot del calcolatore dei prezzi che mostra che il costo stimato di una VM Windows A1 è 66,96 USD al mese](./media/billing-getting-started/pricing-calc.PNG)

Per altre informazioni, vedere le [domande frequenti sui prezzi](https://azure.microsoft.com/pricing/faq/). Per parlare con un operatore, chiamare il numero 1-800-867-1389.

### <a name="check-your-subscription-and-access"></a>Controllare la sottoscrizione e l'accesso

Per visualizzare i costi è necessario l'[accesso alle informazioni di fatturazione a livello di sottoscrizione](billing-manage-access.md), ma solo l'amministratore dell'account può accedere al [Centro account](https://account.windowsazure.com/Home/Index), modificare le informazioni di fatturazione e gestire le sottoscrizioni. L'amministratore dell'account è la persona che ha completato il processo di iscrizione. Per altre informazioni, vedere [Aggiungere o modificare i ruoli di amministratore di Azure che gestiscono la sottoscrizione o i servizi](billing-add-change-azure-subscription-administrator.md).

Per verificare se si è l'amministratore dell'account, passare al [pannello Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ed esaminare l'elenco delle sottoscrizioni a cui si ha accesso. Se in **Ruolo personale** è riportato *Amministratore dell'account*, non sussistono problemi. Se il ruolo indicato è diverso, ad esempio *Proprietario*, non si hanno privilegi completi.

![Screenshot del ruolo dell'utente nella visualizzazione Sottoscrizioni nel portale di Azure](./media/billing-getting-started/sub-blade-view.PNG)

Se non si è l'amministratore dell'account, è probabile che qualcuno abbia concesso l'accesso parziale tramite il [controllo degli accessi in base al ruolo di Azure Active Directory](../active-directory/role-based-access-control-configure.md). Per gestire le sottoscrizioni e modificare le informazioni di fatturazione, [individuare l'amministratore dell'account](billing-subscription-transfer.md#whoisaa) e chiedere all'amministratore di eseguire le attività oppure il [trasferimento di proprietà della sottoscrizione](billing-subscription-transfer.md).

Se l'amministratore dell'account non fa più parte dell'organizzazione ed è necessario gestire la fatturazione, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 

### <a name="spending-limit"></a> Controllare se è attivo un limite di spesa

Se si ha una sottoscrizione che usa crediti, il limite di spesa è attivato per impostazione predefinita. In questo modo, quando si esauriscono tutti i crediti non verrà effettuato alcun addebito sulla carta di credito. Vedere l'[elenco completo delle offerte di Azure e la disponibilità del limite di spesa](https://azure.microsoft.com/support/legal/offer-details/).

Quando si raggiunge il limite di spesa, tuttavia, i servizi vengono disabilitati. Le VM vengono quindi deallocate. Per evitare tempi di inattività del servizio, è necessario disattivare il limite di spesa. Qualsiasi eccedenza verrà addebitata sulla carta di credito in archivio. 

Per verificare se è attivo il limite di spesa, passare alla [visualizzazione Sottoscrizioni nel Centro account](https://account.windowsazure.com/Subscriptions). Se il limite di spesa è attivato, viene visualizzato un banner:

![Screenshot con avviso che informa che è attivo il limite di spesa nel Centro account](./media/billing-getting-started/spending-limit-banner.PNG)

Fare clic sul banner e seguire le istruzioni per rimuovere il limite di spesa. Se al momento dell'iscrizione non si sono immesse le informazioni relative alla carta di credito, per rimuovere il limite di spesa è necessario inserirle. Per altre informazioni, vedere [Limite di spesa di Azure - Come funziona e come abilitarlo o rimuoverlo](https://azure.microsoft.com/pricing/spending-limits/).

### <a name="set-up-billing-alerts"></a>Impostare avvisi di fatturazione per le sottoscrizioni Microsoft Azure

Configurare avvisi di fatturazione per ricevere messaggi di posta elettronica quando i costi di utilizzo superano un importo specificato. In caso di crediti mensili, configurare avvisi per il raggiungimento di un importo specificato. Per altre informazioni, vedere [Configurare avvisi di fatturazione per le sottoscrizioni di Microsoft Azure](billing-set-up-alerts.md).

![Schermata di un messaggio di posta elettronica relativo a un avviso di fatturazione](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> Dato che questa funzionalità è ancora in anteprima, è consigliabile controllare regolarmente l'utilizzo.

Può essere opportuno usare la stima dei costi del calcolatore dei prezzi come indicazione per il primo avviso.

### <a name="understand-limits-and-quotas-for-your-subscription"></a>Conoscere i limiti e le quote della sottoscrizione

Per ogni sottoscrizione esistono limiti predefiniti relativi ad aspetti come numero di core CPU e indirizzo IP. Tenere presenti tali limiti. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md). Per richiedere un aumento del limite o della quota, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="day-1-as-you-add-services"></a>Giorno 1: al momento dell'aggiunta di servizi

### <a name="review-the-estimated-cost-in-the-portal"></a>Esaminare il costo stimato nel portale

Quando si aggiunge un servizio nel portale di Azure, in genere una visualizzazione mostra un costo mensile stimato simile. Quando si scelgono le dimensioni di una VM Windows, ad esempio, viene visualizzato il costo mensile stimato per le ore di calcolo:

![Esempio: il costo stimato di una VM Windows A1 è 66,96 USD al mese](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="tags"></a>Aggiungere tag alle risorse per raggruppare i dati di fatturazione

È possibile usare tag per raggruppare i dati di fatturazione per i servizi supportati. Se si eseguono più VM per diversi team, ad esempio, è possibile usare i tag per classificare i costi per centro di costo (risorse umane, marketing, finanza) o ambiente (produzione, pre-produzione, testing). 

![Schermata che mostra la configurazione dei tag nel portale](./media/billing-getting-started/tags.PNG)

I tag sono riportati in diverse visualizzazioni di report dei costi. Ad esempio, sono visibili immediatamente nella [visualizzazione dell'analisi dei costi](#costs) e dopo il primo periodo di fatturazione nel [file CSV dei dati dettagliati di utilizzo](#invoice-and-usage).

Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Provare ad abilitare funzionalità per la riduzione dei costi come l'arresto automatico delle VM

A seconda dello scenario specifico, è possibile configurare l'arresto automatico delle VM nel portale di Azure. Per altre informazioni, vedere il post sull'[arresto automatico delle VM con Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Screenshot dell'opzione di arresto automatico nel portale](./media/billing-getting-started/auto-shutdown.PNG)

L'arresto automatico è diverso dall'arresto nella VM con le opzioni di risparmio energia. L'arresto automatico arresta e dealloca le VM per interrompere gli addebiti aggiuntivi per l'utilizzo. Per altre informazioni, vedere le domande frequenti sui prezzi per le [VM Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e le [VM Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) in relazione agli stati delle VM.

Per informazioni su altre funzionalità per la riduzione dei costi per gli ambienti di sviluppo e test, vedere [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

## <a name="cost-reporting"></a> Giorno 2 e oltre: dopo l'uso dei servizi, visualizzare i dati di uso

### <a name="costs"></a> Controllare regolarmente velocità e scomposizione dei costi nel portale

Quando i servizi sono in esecuzione, controllare regolarmente a quanto ammonta il relativo costo. È possibile visualizzare la spesa corrente e la velocità nel portale di Azure. 

1. Accedere al [pannello Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Selezionare la sottoscrizione da visualizzare. Potrebbe essere selezionabile una sola sottoscrizione.

3. Nel pannello popup verranno visualizzate la velocità e la scomposizione dei costi. Questa funzionalità potrebbe non essere supportata per l'offerta specifica. In questo caso, nella parte superiore verrà visualizzato un avviso. Dopo l'aggiunta di un servizio, prima che i dati vengano inseriti è necessario attendere 24 ore.
    
    ![Screenshot della velocità e della scomposizione nel portale di Azure](./media/billing-getting-started/burn-rate.PNG)

4. Può essere opportuno aggiungere la visualizzazione al dashboard.

    ![Screenshot dell'aggiunta di una visualizzazione al dashboard](./media/billing-getting-started/pin.PNG)

5. Fare clic su **Analisi dei costi** nell'elenco a sinistra per visualizzare la scomposizione dei costi per risorsa.

    ![Screenshot della visualizzazione dell'analisi dei costi nel portale di Azure](./media/billing-getting-started/cost-analysis.PNG)

6. È possibile filtrare la visualizzazione in base a diverse proprietà, come [tag](#tags), gruppo di risorse e intervallo di tempo. Fare clic su **Applica** per confermare i filtri e su **Scarica** per esportare la visualizzazione in un file con valori delimitati da virgole (CSV).

7. Fare clic su una risorsa per visualizzare la cronologia di spesa e il costo giorno per giorno.

    ![Screenshot della visualizzazione della cronologia di spesa nel portale di Azure](./media/billing-getting-started/spend-history.PNG)

È consigliabile confrontare tali costi con le stime visualizzate al momento della selezione dei servizi. In caso di differenza significativa tra i costi e le stime, ricontrollare il piano tariffario selezionato per le risorse (VM A1 o A0, ad esempio). 

#### <a name="view-costs-for-all-your-subscriptions-in-the-billing-blade"></a>Visualizzare i costi per tutte le sottoscrizioni nel pannello Fatturazione

Se si gestiscono più sottoscrizioni come amministratore dell'account, è possibile visualizzare l'importo delle fatture e la scomposizione dell'aggregazione per tutte le sottoscrizioni nel [pannello Fatturazione](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade). 

<!-- Add screenshots of multiple subs each with billed usage -->

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Attivare e controllare le raccomandazioni di Azure Advisor

[Azure Advisor](../advisor/advisor-overview.md) è una funzionalità in anteprima che consente di ridurre i costi identificando le risorse con un utilizzo basso. Attivare la funzionalità nel portale di Azure:

![Screenshot del pulsante Azure Advisor nel portale di Azure](./media/billing-getting-started/advisor-button.PNG)

È quindi possibile visualizzare raccomandazioni pratiche sui costi nella scheda **Costo** del dashboard di Advisor:

![Screenshot di un esempio di raccomandazione sui costi di Advisor](./media/billing-getting-started/advisor-action.PNG)

Per altre informazioni, vedere [Advisor Cost recommendations](../advisor/advisor-cost-recommendations.md) (Consigli di Azure sui costi).

### <a name="invoice-and-usage"></a> Ottenere la fattura e i dati dettagliati di utilizzo dopo il primo periodo di fatturazione

Dopo il primo periodo di fatturazione, è possibile scaricare la fattura in formato PDF (Portable Document Format) e i dettagli di utilizzo in formato CSV (Comma-Separated Values). È anche possibile fornire il consenso esplicito all'invio della fattura tramite posta elettronica. Questi file consentono di comprendere quanto viene effettivamente addebitato al netto di imposte, sconti e crediti. Se alla sottoscrizione non è collegato un metodo di pagamento, questi file potrebbero non essere disponibili. Per altre informazioni, vedere [Come ottenere la fattura e i dati di utilizzo giornalieri di Azure](billing-download-azure-invoice-daily-usage-date.md) e [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md).

![Screenshot di una fattura in formato PDF](./media/billing-getting-started/invoice.png)

I tag impostati in precedenza vengono visualizzati nei file CSV dei dati dettagliati di utilizzo:

![Screenshot che mostra i tag in un file CSV dei dati di utilizzo](./media/billing-getting-started/csv.png)

### <a name="billing-api"></a>API per la fatturazione

Usare l'API per la fatturazione per ottenere i dati di utilizzo a livello di codice. Usare insieme l'API RateCard e l'API Usage per ottenere l'utilizzo addebitato. Per altre informazioni, vedere [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Risorse aggiuntive per EA, CSP e Sponsorship

Per iniziare, rivolgersi all'account manager o al partner di Azure.

| Offerta | Risorse |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contratto Enterprise Agreement (EA) | [Portale EA](https://ea.azure.com/), [documentazione della guida](https://ea.azure.com/helpdocs) e [report di Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Provider di soluzioni cloud (CSP) | Rivolgersi al provider |
| Azure Sponsorship | [Portale Sponsorship](https://www.microsoftazuresponsorships.com/) |

Se si è responsabili della gestione IT per un'organizzazione di grandi dimensioni, è consigliabile leggere l'articolo relativo allo [scaffolding aziendale Azure](../azure-resource-manager/resource-manager-subscription-governance.md) e il [white paper per l'IT aziendale](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (download file PDF, solo in lingua inglese).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
