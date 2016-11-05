---
title: Informazioni sugli addebiti per i servizi esterni | Microsoft Docs
description: Informazioni sulla fatturazione di servizi esterni, noti in precedenza come Marketplace, in Azure.
services: ''
documentationcenter: ''
author: adpick
manager: felixwu
editor: ''
tags: billing

ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: adpick

---
# <a name="understand-your-azure-external-service-charges"></a>Informazioni sugli addebiti per i servizi esterni
Questo articolo illustra la fatturazione di servizi esterni in Azure. I servizi esterni venivano definiti ordini di Marketplace. I servizi esterni vengono resi disponibili da fornitori di servizi indipendenti, ma sono integrati perfettamente nell'ecosistema di Azure. È possibile passare agli argomenti seguenti:

* Identificare i servizi esterni
* Comprendere in che modo la fatturazione è diversa rispetto alle altre risorse di Azure
* Visualizzare e tenere traccia di tutti i costi accumulati in base all'uso di servizi esterni
* Gestire gli ordini di servizi esterni e il metodo di pagamento corrispondente

## <a name="what-are-azure-external-services?"></a>Cosa sono i servizi esterni di Azure?
I servizi esterni erano denominati Azure Marketplace. Si tratta in genere di servizi pubblicati da terze parti disponibili per Azure. Ad esempio, ClearDB e SendGrid sono servizi esterni che è possibile acquistare in Azure, ma che non vengono pubblicati da Microsoft.

### <a name="identify-external-services"></a>Identificare i servizi esterni
Durante il provisioning di un nuovo servizio esterno o di una nuova risorsa, viene visualizzato un avviso:

![Avviso di acquisto di Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> I servizi esterni vengono pubblicati da società non Microsoft, ma talvolta anche i prodotti Microsoft sono classificati come servizi esterni.
> 
> 

### <a name="external-services-are-billed-separately"></a>I servizi esterni vengono fatturati separatamente
I servizi esterni vengono gestiti come ordini singoli all'interno della sottoscrizione di Azure. Il periodo di fatturazione per ogni servizio è impostato al momento dell'acquisto del servizio. Non deve essere confuso con il periodo di fatturazione della sottoscrizione in cui è stato eseguito l'acquisto. Si ricevono anche fatture separate e l'addebito sulla carta di credito viene applicato separatamente.

### <a name="each-external-service-has-a-different-billing-model"></a>Per ogni servizio esterno è previsto un modello di fatturazione diverso
Alcuni servizi vengono fatturati con pagamento in base al consumo, mentre altri prevedono un modello di pagamento su base mensile. Per i servizi esterni di Azure è necessaria una carta di credito e non è possibile pagare tramite fattura.

### <a name="you-can't-use-monthly-free-credits-for-external-services"></a>Non è possibile usare i crediti gratuiti mensili per i servizi esterni
Se si usa una sottoscrizione di Azure che include [crediti gratuiti](https://azure.microsoft.com/pricing/spending-limits/), questi non possono essere applicati ai pagamenti per i servizi esterni. Usare una carta di credito per acquistare servizi esterni.

## <a name="view-external-service-spending-and-history"></a>Visualizzare la spesa e la cronologia dei servizi esterni
È possibile visualizzare un elenco di servizi esterni inclusi in ogni sottoscrizione all'interno del [portale di Azure](https://portal.azure.com/): 

1. Accedere al [portale di Azure](https://portal.azure.com/) e [passare al pannello **Fatturazione**](https://portal.azure.com/?flight=1#blade/Microsoft_Azure_Billing/BillingBlade).
   
    ![Selezionare Fatturazione nel menu Hub](./media/billing-understand-your-azure-marketplace-charges/billing-button.png) 
2. Nella sezione **Costi della sottoscrizione** selezionare la sottoscrizione da visualizzare. 
   
    ![Selezionare una sottoscrizione nel pannello Fatturazione](./media/billing-understand-your-azure-marketplace-charges/select-sub.png)
3. Fare clic su **Servizi esterni**.
   
    ![Fare clic su Servizi esterni nel pannello Sottoscrizione](./media/billing-understand-your-azure-marketplace-charges/external-service-blade.png)
4. Vengono visualizzati gli ordini di servizi esterni, il nome del publisher, il livello di servizio acquistato, il nome assegnato alla risorsa e lo stato corrente dell'ordine. Selezionare un servizio esterno per visualizzare le fatture precedenti.
   
    ![Selezionare un servizio esterno](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. A questo punto è possibile visualizzare gli importi di fatture precedenti, inclusa la ripartizione fiscale.
   
    ![Visualizzare la cronologia di fatturazione dei servizi esterni](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="manage-payment-methods-for-external-service-orders"></a>Gestire i metodi di pagamento per gli ordini di servizi esterni
Aggiornare i metodi di pagamento per gli ordini di servizi esterni da [Centro account](https://account.windowsazure.com/).

> [!NOTE]
> Se la sottoscrizione è stata acquistata con un account aziendale o dell'istituto di istruzione, è necessario [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per apportare modifiche al metodo di pagamento.
> 
> 

1. Accedere al [Centro account](https://account.windowsazure.com/) e [passare alla scheda **Marketplace**](https://account.windowsazure.com/Store)
   
    ![Selezionare Marketplace nel Centro account](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Selezionare il servizio esterno da gestire
   
    ![Selezionare il servizio esterno da gestire](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Sul lato destro della pagina fare clic su **Modifica il metodo di pagamento**. Questo collegamento consente di accedere a un portale diverso per gestire il metodo di pagamento.
   
    ![Riepilogo degli ordini](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Fare clic su **Modifica informazioni** e seguire le istruzioni per aggiornare le informazioni di pagamento.
   
    ![Selezionare Modifica informazioni](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Annullare un ordine di servizio esterno
Se si desidera annullare l'ordine di servizio esterno, è necessario eliminare la risorsa nel [portale di Azure](https://portal.azure.com).

![Eliminare una risorsa](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help?-contact-support."></a>Richiesta di assistenza Contattare il supporto tecnico.
Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

<!--HONumber=Oct16_HO2-->


