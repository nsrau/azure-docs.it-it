---
title: Evitare costi imprevisti e gestire la fatturazione - Azure | Microsoft Docs
description: "Informazioni su come evitare addebiti imprevisti nella fattura di Azure. Usare le funzionalità di gestione e verifica dei costi per una sottoscrizione di Microsoft Azure."
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: tonguyen
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: d74f649a8b5f35ffe16479576959e5ebb2857bf1
ms.contentlocale: it-it
ms.lasthandoff: 09/22/2017

---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Evitare addebiti imprevisti con la gestione dei costi e della fatturazione di Azure

Quando si effettua l'iscrizione ad Azure, è consigliabile eseguire alcune operazioni per conoscere meglio la spesa sostenuta. Il [calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/) può fornire una stima dei costi prima di creare una risorsa di Azure. Il [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) fornisce la scomposizione e la previsione dei costi correnti. Per raggruppare e conoscere i costi per le risorse usate per diversi progetti o team, esaminare i [tag delle risorse](../azure-resource-manager/resource-group-using-tags.md). Se l'organizzazione dispone di un sistema di creazione report, vedere le [API per la fatturazione](billing-usage-rate-card-overview.md). 

- Se la sottoscrizione è di tipo Enterprise Agreement (EA), è disponibile l'anteprima pubblica per visualizzare i costi nel portale di Azure. Alcune funzionalità descritte di seguito possono non essere applicabili ai clienti che hanno una sottoscrizione ottenuta tramite Cloud Solution Provider (CSP) o Azure Sponsorship. Per altre informazioni, vedere [Risorse aggiuntive per EA, CSP e Sponsorship](#other-offers).

- Se la sottoscrizione è una versione di valutazione gratuita, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure in Open (AIO) o BizSpark, viene automaticamente disabilitata quando sono stati usati tutti i crediti. Informazioni sui [limiti di spesa](#spending-limit) per evitare che la sottoscrizione venga disabilitata all'improvviso.

## <a name="get-estimated-costs-before-adding-azure-services"></a>Ottenere una stima dei costi prima di aggiungere servizi di Azure

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Stimare i costi online con il calcolatore dei prezzi

Usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) per ottenere una stima del costo mensile del servizio a cui si è interessati. È possibile aggiungere qualsiasi risorsa di Azure proprietaria per ottenere una stima del costo.

![Screenshot del menu del calcolatore dei prezzi](./media/billing-getting-started/pricing-calc.png)

Per una macchina virtuale (VM) Windows A1 mantenuta costantemente in esecuzione, ad esempio, il costo stimato in ore di calcolo è $66,96 USD/mese:

![Screenshot del calcolatore dei prezzi che mostra che il costo stimato di una VM Windows A1 è 66,96 USD al mese](./media/billing-getting-started/pricing-calcVM.png)

Per altre informazioni sui prezzi, vedere queste [domande frequenti](https://azure.microsoft.com/pricing/faq/). Per parlare con un operatore, chiamare il numero 1-800-867-1389.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Esaminare il costo stimato nel portale di Azure

Quando si aggiunge un servizio nel portale di Azure, in genere una visualizzazione mostra un costo mensile stimato simile. Quando si scelgono le dimensioni di una VM Windows, ad esempio, viene visualizzato il costo mensile stimato per le ore di calcolo:

![Esempio: il costo stimato di una VM Windows A1 è 66,96 USD al mese](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="set-up-billing-alerts"></a>Impostare avvisi di fatturazione per le sottoscrizioni Microsoft Azure

Configurare avvisi di fatturazione per ricevere messaggi di posta elettronica quando i costi di utilizzo superano un importo specificato. In caso di crediti mensili, configurare avvisi per il raggiungimento di un importo specificato. Per altre informazioni, vedere [Configurare avvisi di fatturazione per le sottoscrizioni di Microsoft Azure](billing-set-up-alerts.md).

![Schermata di un messaggio di posta elettronica relativo a un avviso di fatturazione](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> Dato che questa funzionalità è ancora in anteprima, è consigliabile controllare regolarmente l'utilizzo.

Può essere opportuno usare la stima dei costi del calcolatore dei prezzi come indicazione per il primo avviso.

### <a name="spending-limit"></a> Controllare se è attivo un limite di spesa

Se si ha una sottoscrizione che usa crediti, il limite di spesa è attivato per impostazione predefinita. In questo modo, quando si esauriscono tutti i crediti non verrà effettuato alcun addebito sulla carta di credito. Vedere l'[elenco completo delle offerte di Azure e la disponibilità del limite di spesa](https://azure.microsoft.com/support/legal/offer-details/).

Quando si raggiunge il limite di spesa, tuttavia, i servizi vengono disabilitati. Le VM vengono quindi deallocate. Per evitare tempi di inattività del servizio, è necessario disattivare il limite di spesa. Qualsiasi eccedenza verrà addebitata sulla carta di credito in archivio. 

Per verificare se è attivo il limite di spesa, passare alla [visualizzazione Sottoscrizioni nel Centro account](https://account.windowsazure.com/Subscriptions). Se il limite di spesa è attivato, viene visualizzato un banner:

![Screenshot con avviso che informa che è attivo il limite di spesa nel Centro account](./media/billing-getting-started/spending-limit-banner.PNG)

Fare clic sul banner e seguire le istruzioni per rimuovere il limite di spesa. Se al momento dell'iscrizione non si sono immesse le informazioni relative alla carta di credito, per rimuovere il limite di spesa è necessario inserirle. Per altre informazioni, vedere [Limite di spesa di Azure - Come funziona e come abilitarlo o rimuoverlo](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Modi per monitorare i costi quando si usano i servizi di Azure

### <a name="tags"></a>Aggiungere tag alle risorse per raggruppare i dati di fatturazione

È possibile usare tag per raggruppare i dati di fatturazione per i servizi supportati. Se si eseguono più VM per diversi team, ad esempio, è possibile usare i tag per classificare i costi per centro di costo (risorse umane, marketing, finanza) o ambiente (produzione, pre-produzione, testing). 

![Schermata che mostra la configurazione dei tag nel portale](./media/billing-getting-started/tags.PNG)

I tag sono riportati in diverse visualizzazioni di report dei costi. Ad esempio, sono visibili immediatamente nella [visualizzazione dell'analisi dei costi](#costs) e dopo il primo periodo di fatturazione nel [file CSV dei dati dettagliati di utilizzo](#invoice-and-usage).

Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Controllare regolarmente velocità e scomposizione dei costi nel portale

Quando i servizi sono in esecuzione, controllare regolarmente a quanto ammonta il relativo costo. È possibile visualizzare la spesa corrente e la velocità nel portale di Azure. 

1. Vedere il [pannello Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selezionare una sottoscrizione.

2. Nel pannello popup verranno visualizzate la velocità e la scomposizione dei costi. Questa funzionalità potrebbe non essere supportata per l'offerta specifica. In questo caso, nella parte superiore verrà visualizzato un avviso.

    ![Screenshot della velocità e della scomposizione nel portale di Azure](./media/billing-getting-started/burn-rate.PNG)

3. Fare clic su **Analisi dei costi** nell'elenco a sinistra per visualizzare la scomposizione dei costi per risorsa. Dopo l'aggiunta di un servizio, prima che i dati vengano inseriti è necessario attendere 24 ore.

    ![Screenshot della visualizzazione dell'analisi dei costi nel portale di Azure](./media/billing-getting-started/cost-analysis.PNG)

4. È possibile filtrare la visualizzazione in base a diverse proprietà, come [tag](#tags), gruppo di risorse e intervallo di tempo. Fare clic su **Applica** per confermare i filtri e su **Scarica** per esportare la visualizzazione in un file con valori delimitati da virgole (CSV).

5. È anche possibile fare clic su una risorsa per visualizzare la cronologia di spesa e il costo giornaliero della risorsa.

    ![Screenshot della visualizzazione della cronologia di spesa nel portale di Azure](./media/billing-getting-started/costhistory.PNG)

È consigliabile confrontare tali costi con le stime visualizzate al momento della selezione dei servizi. In caso di differenza significativa tra i costi e le stime, ricontrollare il piano tariffario selezionato per le risorse (VM A1 o A0, ad esempio). 

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Provare ad abilitare funzionalità per la riduzione dei costi come l'arresto automatico delle VM

A seconda dello scenario specifico, è possibile configurare l'arresto automatico delle VM nel portale di Azure. Per altre informazioni, vedere il post sull'[arresto automatico delle VM con Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Screenshot dell'opzione di arresto automatico nel portale](./media/billing-getting-started/auto-shutdown.PNG)

L'arresto automatico è diverso dall'arresto nella VM con le opzioni di risparmio energia. L'arresto automatico arresta e dealloca le VM per interrompere gli addebiti aggiuntivi per l'utilizzo. Per altre informazioni, vedere le domande frequenti sui prezzi per le [VM Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e le [VM Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) in relazione agli stati delle VM.

Per informazioni su altre funzionalità per la riduzione dei costi per gli ambienti di sviluppo e test, vedere [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Attivare e controllare le raccomandazioni di Azure Advisor

[Azure Advisor](../advisor/advisor-overview.md) è una funzionalità in anteprima che consente di ridurre i costi identificando le risorse con un utilizzo basso. Attivare la funzionalità nel portale di Azure:

![Screenshot del pulsante Azure Advisor nel portale di Azure](./media/billing-getting-started/advisor-button.PNG)

È quindi possibile visualizzare raccomandazioni pratiche sui costi nella scheda **Costo** del dashboard di Advisor:

![Screenshot di un esempio di raccomandazione sui costi di Advisor](./media/billing-getting-started/advisor-action.PNG)

Per altre informazioni, vedere [Advisor Cost recommendations](../advisor/advisor-cost-recommendations.md) (Consigli di Azure sui costi).

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>Rivedere i costi alla fine del ciclo di fatturazione

Dopo la fine del ciclo di fatturazione, la fattura diventa disponibile. È anche possibile [scaricare le fatture precedenti e i file dei dati dettagliati di utilizzo](billing-download-azure-invoice-daily-usage-date.md) per verificare che gli addebiti siano corretti. Per altre informazioni sul confronto tra i dati di utilizzo giornalieri e la fattura, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>API per la fatturazione

Usare l'API per la fatturazione per ottenere i dati di utilizzo a livello di codice. Usare insieme l'API RateCard e l'API Usage per ottenere l'utilizzo addebitato. Per altre informazioni, vedere [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Risorse aggiuntive e casi speciali

### <a name="ea-csp-and-sponsorship-customers"></a>Clienti EA, CSP e Sponsorship
Per iniziare, rivolgersi all'account manager o al partner di Azure.

| Offerta | Risorse |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contratto Enterprise Agreement (EA) | [Portale EA](https://ea.azure.com/), [documentazione della guida](https://ea.azure.com/helpdocs) e [report di Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Provider di soluzioni cloud (CSP) | Rivolgersi al provider |
| Azure Sponsorship | [Portale Sponsorship](https://www.microsoftazuresponsorships.com/) |

Se si è responsabili della gestione IT per un'organizzazione di grandi dimensioni, è consigliabile leggere l'articolo relativo allo [scaffolding aziendale Azure](../azure-resource-manager/resource-manager-subscription-governance.md) e il [white paper per l'IT aziendale](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (download file PDF, solo in lingua inglese).

#### <a name="EA"></a> Anteprima delle visualizzazioni dei costi per i contratti Enterprise Agreement nel portale di Azure 

Le visualizzazioni dei costi aziendali sono attualmente disponibili in versione di anteprima pubblica. Elementi da considerare:
- I costi di sottoscrizione sono basati sull'utilizzo e non tengono conto di importi prepagati, eccedenze, quantità incluse, rettifiche e imposte. I costi effettivi vengono calcolati a livello di registrazione. 
- Gli importi visualizzati nel portale di Azure potrebbero subire un ritardo rispetto ai valori in Enterprise Portal.  
- Se i costi non vengono visualizzati, la causa potrebbe essere una delle seguenti:
    - Si è un proprietario di un account e l'amministratore delle registrazioni ha disabilitato l'impostazione di visualizzazione dei costi per i proprietari degli account.  Contattare l'amministratore delle registrazioni per ottenere l'accesso ai costi. 
    - Si è un amministratore di reparto e l'amministratore delle registrazioni ha disabilitato l'impostazione di visualizzazione dei costi per gli amministratori di reparto.  Contattare l'amministratore delle registrazioni per ottenere l'accesso. 
    - Azure è stato acquistato tramite un partner di canale, il quale non ha rilasciato informazioni sui prezzi.  
- Quando in Enterprise Portal vengono aggiornate le impostazioni relative all'accesso ai costi, si verifica un ritardo di qualche minuto prima che le modifiche siano visibili anche nel portale di Azure.
- Limite di spesa, avvisi di fatturazione e linee guida per la fatturazione non riguardano le sottoscrizioni EA.

### <a name="check-your-subscription-and-access"></a>Controllare la sottoscrizione e l'accesso

Per visualizzare i costi è necessario l'[accesso alle informazioni di fatturazione a livello di sottoscrizione](billing-manage-access.md), ma solo l'amministratore dell'account può accedere al [Centro account](https://account.azure.com/Subscriptions), modificare le informazioni di fatturazione e gestire le sottoscrizioni. L'amministratore dell'account è la persona che ha completato il processo di iscrizione. Per altre informazioni, vedere [Aggiungere o modificare i ruoli di amministratore di Azure che gestiscono la sottoscrizione o i servizi](billing-add-change-azure-subscription-administrator.md).

Per verificare se si è l'amministratore dell'account, passare al [pannello Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ed esaminare l'elenco delle sottoscrizioni a cui si ha accesso. Se in **Ruolo personale** è riportato *Amministratore dell'account*, non sussistono problemi. Se il ruolo indicato è diverso, ad esempio *Proprietario*, non si hanno privilegi completi.

![Screenshot del ruolo dell'utente nella visualizzazione Sottoscrizioni nel portale di Azure](./media/billing-getting-started/sub-blade-view.PNG)

Se non si è l'amministratore dell'account, è probabile che qualcuno abbia concesso l'accesso parziale tramite il [controllo degli accessi in base al ruolo di Azure Active Directory](../active-directory/role-based-access-control-configure.md). Per gestire le sottoscrizioni e modificare le informazioni di fatturazione, [individuare l'amministratore dell'account](billing-subscription-transfer.md#whoisaa) e chiedere all'amministratore di eseguire le attività oppure il [trasferimento di proprietà della sottoscrizione](billing-subscription-transfer.md).

Se l'amministratore dell'account non fa più parte dell'organizzazione ed è necessario gestire la fatturazione, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 
## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

