---
title: Evitare costi imprevisti e gestire la fatturazione in Azure | Microsoft Docs
description: Informazioni su come evitare addebiti imprevisti nella fattura di Azure. Usare le funzionalità di gestione e verifica dei costi per una sottoscrizione di Microsoft Azure.
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: banders
ms.openlocfilehash: 146c74fe751e75fb85563378be6f812802928fe2
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918942"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Evitare addebiti imprevisti con la gestione dei costi e della fatturazione di Azure

Quando effettua l'iscrizione ad Azure, esistono diversi aspetti che è eseguire per sfruttare al meglio la spesa sostenuta. Il [calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/) può fornire una stima dei costi prima di creare una risorsa di Azure. Il [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) fornisce la scomposizione e la previsione dei costi correnti. Per raggruppare e conoscere i costi per le risorse usate per diversi progetti o team, esaminare i [tag delle risorse](../azure-resource-manager/resource-group-using-tags.md). Se l'organizzazione dispone di un sistema di creazione report, vedere le [API per la fatturazione](billing-usage-rate-card-overview.md).

- Se la sottoscrizione è di tipo Enterprise Agreement (EA), è disponibile l'anteprima pubblica per visualizzare i costi nel portale di Azure. Alcune funzionalità descritte di seguito possono non essere applicabili ai clienti che hanno una sottoscrizione ottenuta tramite Cloud Solution Provider (CSP) o Azure Sponsorship. Per altre informazioni, vedere [Risorse aggiuntive per EA, CSP e Sponsorship](#other-offers).

- Se la sottoscrizione è una versione di valutazione gratuita, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure in Open (AIO) o BizSpark, viene automaticamente disabilitata quando sono stati usati tutti i crediti. È possibile ottenere altre informazioni sui [limiti di spesa](#spending-limit) per evitare che la sottoscrizione venga disabilitata all'improvviso.

- Se è stata eseguita la registrazione per un [account gratuito di Azure](https://azure.microsoft.com/free/), [è possibile usare gratuitamente alcuni dei servizi Azure più richiesti per 12 mesi](billing-create-free-services-included-free-account.md). Oltre ai consigli elencati di seguito, vedere l'argomento su come [evitare di pagare l'account gratuito](billing-avoid-charges-free-account.md).

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

### <a name="spending-limit"></a> Controllare se è attivo un limite di spesa

Se si ha una sottoscrizione che usa crediti, il limite di spesa è attivato per impostazione predefinita. In questo modo, quando si esauriscono tutti i crediti non verrà effettuato alcun addebito sulla carta di credito. Vedere l'[elenco completo delle offerte di Azure e la disponibilità del limite di spesa](https://azure.microsoft.com/support/legal/offer-details/).

Quando si raggiunge il limite di spesa, tuttavia, i servizi vengono disabilitati. Le VM vengono quindi deallocate. Per evitare tempi di inattività del servizio, è necessario disattivare il limite di spesa. Qualsiasi eccedenza verrà addebitata sulla carta di credito in archivio. 

Per verificare se è attivo il limite di spesa, passare alla [visualizzazione Sottoscrizioni nel Centro account](https://account.windowsazure.com/Subscriptions). Se il limite di spesa è attivato, viene visualizzato un banner:

![Screenshot con avviso che informa che è attivo il limite di spesa nel Centro account](./media/billing-getting-started/spending-limit-banner.PNG)

Fare clic sul banner e seguire le istruzioni per rimuovere il limite di spesa. Se al momento dell'iscrizione non si sono immesse le informazioni relative alla carta di credito, per rimuovere il limite di spesa è necessario inserirle. Per altre informazioni, vedere [Limite di spesa di Azure - Come funziona e come abilitarlo o rimuoverlo](https://azure.microsoft.com/pricing/spending-limits/).

È possibile usare il servizio [Cloudyn](https://www.cloudyn.com/) per la creazione di avvisi che avvertano automaticamente gli stakeholder riguardo ad anomalie di spesa e rischi di costi eccessivi. È possibile creare avvisi usando report che supportano avvisi basati su soglie di budget e costo. Per altre informazioni sull'uso di Cloudyn, consultare [Esercitazione: Esaminare l'utilizzo e i costi](../cost-management/tutorial-review-usage.md).

Questo esempio usa il report **Actual Cost Over Time** (Costo effettivo nel tempo) per inviare una notifica quando la spesa di una macchina virtuale di Azure si avvicina al budget totale. In questo scenario si dispone di un budget totale di 20.000 $ e si vuole ricevere una notifica quando i costi si avvicinano alla prima metà del budget, 9.000 $, e un ulteriore avviso quando i costi raggiungono i 10.000 $.

1. Dal menu nella parte superiore del portale di Cloudyn selezionare **Costo** > **Analisi dei costi** > **Actual Cost Over Time** (Costo effettivo nel tempo). 
2. Impostare **Groups** (Gruppi) su **Service** (Servizio) e impostare **Filter on the service** (Filtro sul servizio) su **Azure/VM** (Azure/macchina virtuale). 
3. Nella parte superiore destra del report selezionare **Actions** (Azioni) e quindi selezionare **Schedule report** (Pianifica report).
4. Per inviare a se stessi un messaggio di posta elettronica del report a intervalli pianificati, selezionare la scheda **Scheduling** (Pianificazione) nella finestra di report **Save or Schedule this report** (Salva o pianifica report). Assicurarsi di selezionare **Send via email** (Invia tramite posta elettronica). Nel report inviato tramite posta elettronica sono inclusi tutti i tag, i raggruppamenti e i filtri usati. 
5. Selezionare la scheda **Threshold** (Soglia) e quindi selezionare **Actual Cost vs. Threshold** (Costo effettivo/soglia). 
   1. Nella casella di soglia **Red alert** (Avviso rosso) immettere 10000. 
   2. Nella casella di soglia **Yellow alert** (Avviso giallo) immettere 9000. 
   3. Nella casella **Number of consecutive alerts** (Numero di avvisi consecutivi) immettere il numero di avvisi consecutivi da ricevere. Dopo aver ricevuto il numero totale di avvisi specificato, non vengono inviati altri avvisi. 
6. Selezionare **Salva**.

    ![Esempio che mostra gli avvisi di colore giallo e rosso in base alle soglie di spesa](./media/billing-getting-started/schedule-alert01.png)

È anche possibile scegliere la metrica di soglia **Cost Percentage vs. Budget** (Percentuale costo/budget) per la creazione degli avvisi. In questo modo è possibile specificare le soglie come percentuali del budget anziché come valori di valuta.

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Modi per monitorare i costi quando si usano i servizi di Azure

### <a name="tags"></a>Aggiungere tag alle risorse per raggruppare i dati di fatturazione

È possibile usare tag per raggruppare i dati di fatturazione per i servizi supportati. Se si eseguono più VM per diversi team, ad esempio, è possibile usare i tag per classificare i costi per centro di costo (risorse umane, marketing, finanza) o ambiente (produzione, pre-produzione, testing). 

![Schermata che mostra la configurazione dei tag nel portale](./media/billing-getting-started/tags.PNG)

I tag sono riportati in diverse visualizzazioni di report dei costi. Ad esempio, sono visibili immediatamente nella [visualizzazione dell'analisi dei costi](#costs) e dopo il primo periodo di fatturazione nel file CSV dei dati dettagliati di utilizzo.

Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Controllare regolarmente velocità e scomposizione dei costi nel portale

Quando i servizi sono in esecuzione, controllare regolarmente a quanto ammonta il relativo costo. È possibile visualizzare la spesa corrente e la velocità nel portale di Azure.

1. Vedere il [pannello Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selezionare una sottoscrizione.

2. Se si tratta di funzionalità supportate per la sottoscrizione, verranno visualizzate la velocità e la scomposizione dei costi.

    ![Screenshot della velocità e della scomposizione nel portale di Azure](./media/billing-getting-started/burn-rate.PNG)

3. Fare clic su **Analisi dei costi** nell'elenco a sinistra per visualizzare la scomposizione dei costi per risorsa. Dopo l'aggiunta di un servizio, prima che i dati vengano inseriti è necessario attendere 24 ore.

    ![Screenshot della visualizzazione dell'analisi dei costi nel portale di Azure](./media/billing-getting-started/cost-analysis.PNG)

4. È possibile filtrare la visualizzazione in base a diverse proprietà come [tag](#tags), tipo di risorsa, gruppo di risorse e intervallo di tempo. Fare clic su **Applica** per confermare i filtri e su **Scarica** per esportare la visualizzazione in un file con valori delimitati da virgole (CSV).

5. È anche possibile fare clic su una risorsa per visualizzare la cronologia di spesa e il costo giornaliero della risorsa.

    ![Screenshot della visualizzazione della cronologia di spesa nel portale di Azure](./media/billing-getting-started/costhistory.PNG)

È consigliabile confrontare tali costi con le stime visualizzate al momento della selezione dei servizi. In caso di differenza significativa tra i costi e le stime, ricontrollare il piano tariffario selezionato per le risorse.

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Provare ad abilitare funzionalità per la riduzione dei costi come l'arresto automatico delle macchine virtuali

A seconda dello scenario specifico, è possibile configurare l'arresto automatico delle macchine virtuali nel portale di Azure. Per altre informazioni, vedere il post [Auto shutdown for VMs using Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) (Arresto automatico delle macchine virtuali con Azure Resource Manager).

![Screenshot dell'opzione di arresto automatico nel portale](./media/billing-getting-started/auto-shutdown.PNG)

L'arresto automatico è diverso dall'arresto nella macchina virtuale con le opzioni di risparmio energia. L'arresto automatico arresta e dealloca le macchine virtuali per interrompere gli addebiti aggiuntivi per l'utilizzo. Per altre informazioni, vedere le domande frequenti sui prezzi per le [VM Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e le [VM Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) in relazione agli stati delle VM.

Per informazioni su altre funzionalità per la riduzione dei costi per gli ambienti di sviluppo e test, vedere [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Attivare e controllare le raccomandazioni di Azure Advisor

[Azure Advisor](../advisor/advisor-overview.md) è una funzionalità che consente di ridurre i costi identificando le risorse con un utilizzo basso. Esaminare Advisor nel portale di Azure:

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

Se si è responsabili della gestione IT per un'organizzazione di grandi dimensioni, è consigliabile leggere l'articolo relativo allo [scaffolding aziendale Azure](/azure/architecture/cloud-adoption-guide/subscription-governance) e il [white paper per l'IT aziendale](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (download file PDF, solo in lingua inglese).

#### <a name="EA"></a> Anteprima delle visualizzazioni dei costi per i contratti Enterprise nel portale di Azure 

Le visualizzazioni dei costi aziendali sono attualmente disponibili in versione di anteprima pubblica. Elementi da considerare:

- I costi di sottoscrizione sono basati sull'utilizzo e non includono importi prepagati, eccedenze, quantità incluse, rettifiche e imposte. I costi effettivi vengono calcolati a livello di registrazione.
- Gli importi visualizzati nel portale di Azure potrebbero essere diversi rispetto a quelli in Enterprise Portal. Gli aggiornamenti in Enterprise Portal potrebbero richiedere alcuni minuti prima che le modifiche vengano visualizzate nel portale di Azure.
- Se i costi non vengono visualizzati, potrebbe essere per uno dei motivi seguenti:
    - Non si dispone di autorizzazioni a livello di sottoscrizione. Per accedere alla visualizzazione dei costi aziendali, è necessario disporre del ruolo Fatturazione per lettore, Lettore, Collaboratore o Proprietario a livello di sottoscrizione.
    - Si è un proprietario di un account e l'amministratore delle registrazioni ha disabilitato l'impostazione di visualizzazione dei costi per i proprietari degli account.  Contattare l'amministratore delle registrazioni per ottenere l'accesso ai costi. 
    - Si è un amministratore di reparto e l'amministratore delle registrazioni ha disabilitato l'impostazione di visualizzazione dei costi per gli amministratori di reparto.  Contattare l'amministratore delle registrazioni per ottenere l'accesso.
    - Azure è stato acquistato tramite un partner di canale, il quale non ha rilasciato informazioni sui prezzi.  
- Se si aggiornano le impostazioni relative all'accesso ai costi in Enterprise Portal, si verifica un ritardo di qualche minuto prima che le modifiche vengano visualizzate nel portale di Azure.
- Limite di spesa e materiale sussidiario di fatturazione non si applicano alle sottoscrizioni con contratto Enterprise.

### <a name="check-your-subscription-and-access"></a>Controllare la sottoscrizione e l'accesso

Per visualizzare i costi, è necessario l'[accesso a livello di sottoscrizione alle informazioni di fatturazione](billing-manage-access.md). Solo l'amministratore dell'account può accedere al [Centro account](https://account.azure.com/Subscriptions), modificare le informazioni di fatturazione e gestire le sottoscrizioni. L'amministratore dell'account è la persona che ha completato il processo di iscrizione. Per altre informazioni, vedere [Aggiungere o modificare i ruoli di amministratore di Azure che gestiscono la sottoscrizione o i servizi](billing-add-change-azure-subscription-administrator.md).

Per verificare se si è l'amministratore dell'account, passare a [Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Esaminare l'elenco delle sottoscrizioni a cui si ha accesso. Se in **Ruolo personale** è riportato *Amministratore dell'account*, non sussistono problemi. Se il ruolo indicato è diverso, ad esempio *Proprietario*, non si hanno privilegi completi.

![Screenshot del ruolo dell'utente nella visualizzazione Sottoscrizioni nel portale di Azure](./media/billing-getting-started/sub-blade-view.PNG)

Se non si è l'amministratore dell'account, è probabile che qualcuno abbia concesso l'accesso parziale usando il [controllo degli accessi in base al ruolo di Azure Active Directory](../role-based-access-control/role-assignments-portal.md). Per gestire le sottoscrizioni e modificare le informazioni di fatturazione, [individuare l'amministratore dell'account](billing-subscription-transfer.md#whoisaa). Chiedere all'amministratore dell'account di eseguire le attività oppure di [trasferire la proprietà della sottoscrizione](billing-subscription-transfer.md).

Se l'amministratore dell'account non fa più parte dell'organizzazione ed è necessario gestire la fatturazione, [contattare il supporto tecnico](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="how-to-request-a-service-level-agreement-credit-for-a-service-incident"></a>Come richiedere un credito del contratto di servizio per un evento imprevisto del servizio

Il contratto di servizio definisce gli impegni di Microsoft in merito a tempi di attività e connettività. Un evento imprevisto del servizio viene segnalato quando servizi di Azure riscontrare un problema che il tempo di attività di impatti o la connettività, noto anche come "interruzione". Se si non ottenere e mantenere i livelli di servizio per ogni servizio, come descritto nel contratto di servizio, potrebbe essere idoneo per un credito per una parte della tariffa mensile pagata.

Per richiedere una carta di credito:

1. Accedere al [portale di Azure](https://portal.azure.com/). Se si hanno più account, assicurarsi di usare quello che è stata influenzata da Azure tempi di inattività. In questo modo il supporto di raccogliere automaticamente le informazioni necessarie in background e risolvere più rapidamente il caso.
2. Creare una nuova richiesta di supporto.
3. Sotto **tipo di problema**, selezionare **fatturazione**.
4. Sotto **tipo di problema**, selezionare **richiesta di rimborso**.
5. Aggiungere i dettagli per specificare che si chiede per un credito del contratto di servizio, indicare la data/ora/fuso orario, nonché i servizi interessati (macchine virtuali, siti Web e così via)
6. Verificare i dettagli di contatto e selezionare il **Create** pulsante per inviare la richiesta.

Le soglie di contratto di servizio variano a seconda del servizio. Ad esempio, di livello Web SQL ha un contratto di servizio del 99,9%, le macchine virtuali hanno un contratto di servizio del 99,95% e il livello Standard di SQL ha un contratto di servizio del 99,99%.

Per alcuni servizi, esistono prerequisiti per il contratto di servizio da applicare. Ad esempio, le macchine virtuali deve avere due o più istanze distribuite nello stesso Set di disponibilità.

Per altre informazioni, vedere la [i contratti di servizio](https://azure.microsoft.com/support/legal/sla/) documentazione e il [riepilogo di contratto di servizio per servizi di Azure](https://azure.microsoft.com/support/legal/sla/summary/) documentazione.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
