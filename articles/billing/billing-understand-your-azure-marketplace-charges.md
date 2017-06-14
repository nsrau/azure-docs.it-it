---
title: Informazioni sugli addebiti per i servizi esterni di Azure | Documentazione Microsoft
description: Informazioni sulla fatturazione di servizi esterni, noti in precedenza come Marketplace, in Azure.
services: 
documentationcenter: 
author: adpick
manager: ruchic
editor: 
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: adpick
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 3e74bdd1faf268f33fdbbcc428b4b32212508c39
ms.contentlocale: it-it
ms.lasthandoff: 03/03/2017

---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Informazioni sulla fatturazione di Azure per gli addebiti dei servizi esterni
I servizi esterni erano denominati Azure Marketplace. Si tratta in genere di servizi pubblicati da terze parti disponibili per Azure, ma integrati completamente in Azure. Ad esempio, ClearDB e SendGrid sono servizi esterni che è possibile acquistare in Azure, ma che non vengono pubblicati da Microsoft.

Durante il provisioning di un nuovo servizio esterno o di una nuova risorsa, viene visualizzato un avviso:

![Avviso di acquisto di Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> I servizi esterni vengono pubblicati da società non Microsoft, ma talvolta anche i prodotti Microsoft sono classificati come servizi esterni.
> 
> 

## <a name="how-external-services-are-billed"></a>Modalità di fatturazione dei servizi esterni
- I servizi esterni vengono fatturati separatamente. Vengono gestiti come ordini singoli all'interno della sottoscrizione di Azure. Il periodo di fatturazione per ogni servizio è impostato al momento dell'acquisto del servizio. Non deve essere confuso con il periodo di fatturazione della sottoscrizione in cui è stato eseguito l'acquisto. Si ricevono anche fatture separate e l'addebito sulla carta di credito viene applicato separatamente.
- Per ogni servizio esterno è previsto un modello di fatturazione diverso. Alcuni servizi vengono fatturati con pagamento in base al consumo, mentre altri prevedono un modello di pagamento su base mensile. Per i servizi esterni di Azure è necessaria una carta di credito e non è possibile pagare tramite fattura.
- Non è possibile usare i crediti gratuiti mensili per i servizi esterni. Se si usa una sottoscrizione di Azure che include [crediti gratuiti](https://azure.microsoft.com/pricing/spending-limits/), questi non possono essere applicati ai pagamenti per i servizi esterni. Usare una carta di credito per acquistare servizi esterni.


## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Visualizzare la spesa e la cronologia dei servizi esterni nel portale di Azure
È possibile visualizzare un elenco di servizi esterni inclusi in ogni sottoscrizione all'interno del [portale di Azure](https://portal.azure.com/): 

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore account.
2. Nel menu Hub selezionare **Sottoscrizioni**.
   
    ![Selezionare Sottoscrizioni nel menu Hub](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. Nel pannello **Sottoscrizioni** selezionare la sottoscrizione che si vuole visualizzare e quindi **Servizi esterni**.
   
    ![Selezionare una sottoscrizione nel pannello Fatturazione](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Vengono visualizzati gli ordini di servizi esterni, il nome del publisher, il livello di servizio acquistato, il nome assegnato alla risorsa e lo stato corrente dell'ordine. Selezionare un servizio esterno per visualizzare le fatture precedenti.
   
    ![Selezionare un servizio esterno](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. A questo punto è possibile visualizzare gli importi di fatture precedenti, inclusa la ripartizione fiscale.
   
    ![Visualizzare la cronologia di fatturazione dei servizi esterni](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Visualizzare la spesa dei servizi esterni per i clienti con contratto Enterprise (EA, Enterprise Agreement)
I clienti EA possono visualizzare la spesa dei servizi esterni e scaricare i report nel portale EA. Per iniziare, vedere [Azure Marketplace per i clienti EA](https://ea.azure.com/helpdocs/azureMarketplace).

## <a name="manage-payment-methods-for-external-service-orders"></a>Gestire i metodi di pagamento per gli ordini di servizi esterni
Aggiornare i metodi di pagamento per gli ordini di servizi esterni da [Centro account](https://account.windowsazure.com/).

> [!NOTE]
> Se la sottoscrizione è stata acquistata con un account aziendale o dell'istituto di istruzione, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per apportare modifiche al metodo di pagamento.
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
Se si desidera annullare l'ordine di servizio esterno, eliminare la risorsa nel [Portale di Azure](https://portal.azure.com).

![Eliminare una risorsa](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Per eventuali domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.


