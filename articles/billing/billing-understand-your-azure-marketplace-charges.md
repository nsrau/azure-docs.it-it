---
title: Informazioni sugli addebiti per i servizi esterni di Azure | Documentazione Microsoft
description: Informazioni sulla fatturazione di servizi esterni, noti in precedenza come Marketplace, in Azure.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 278e873d01eb3dd7d614d771e5b50b8fe624800a
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490359"
---
# <a name="understand-your-azure-external-services-charges"></a>Informazioni sugli addebiti di servizi esterni di Azure
Servizi esterni vengono pubblicati da fornitori di software di terze parti in Azure marketplace. Ad esempio, SendGrid è un servizio esterno che è possibile acquistare in Azure, ma non è pubblicato da Microsoft. Alcuni prodotti Microsoft vengono venduti attraverso Azure marketplace, troppo.

## <a name="how-external-services-are-billed"></a>Modalità di fatturazione dei servizi esterni

- Se si dispone di un [contratto di Microsoft dal cliente](#check-access), vengono fatturati i servizi di terze parti con il resto dei servizi di Azure.
- Se non hai un contratto di Microsoft dal cliente, i servizi esterni vengono fatturati separatamente da servizi di Azure.
- Per ogni servizio esterno è previsto un modello di fatturazione diverso. Alcuni servizi vengono fatturati in modalità con pagamento a consumo, mentre altri sono corretti agli addebiti mensili.
- Non è possibile usare i crediti gratuiti mensili per i servizi esterni. Se si usa una sottoscrizione di Azure che include [crediti gratuiti](https://azure.microsoft.com/pricing/spending-limits/), non possono essere applicati ai costi da servizi esterni. Durante il provisioning di un nuovo servizio esterno o di una nuova risorsa, viene visualizzato un avviso:

    ![Avviso di acquisto di Marketplace](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-invoices"></a>Visualizzare e scaricare le fatture

Se si dispone di un [contratto di Microsoft dal cliente](#check-access), i costi di terze parti vengono nella fattura stesso come i costi di Azure. Informazioni su come [consente di visualizzare e scaricare la fattura di Azure](billing-download-azure-invoice.md) dal portale di Azure per visualizzare gli addebiti di terze parti.

Se non hai un contratto di clienti Microsoft, hai fatture separate per gli addebiti di terze parti. È possibile visualizzare e scaricare le fatture di Azure Marketplace dal portale di Azure seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare **costi di gestione e fatturazione**.
1. Nel menu a sinistra, selezionare **fatture**.
1. Fare clic sui **Azure Marketplace e le prenotazioni** scheda.  ![Immagine della scheda Azure marketplace e le prenotazioni](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. Nell'elenco a discesa sottoscrizione, selezionare la sottoscrizione che contiene i servizi esterni per visualizzare le fatture.

## <a name="external-spending-for-ea-customers"></a>External spesa per i clienti con contratto Enterprise

I clienti EA possono visualizzare la spesa dei servizi esterni e scaricare i report nel portale EA. Per iniziare, vedere [Azure Marketplace per i clienti EA](https://ea.azure.com/helpdocs/azureMarketplace).

## <a name="manage-payment-for-external-services"></a>Gestire i pagamenti per i servizi esterni

Quando si acquista un servizio esterno, si sceglie una sottoscrizione di Azure per la risorsa. Il metodo di pagamento della sottoscrizione di Azure selezionato diventa il metodo di pagamento per il servizio esterno. Per modificare il metodo di pagamento per un servizio esterno, è necessario [modificare il metodo di pagamento della sottoscrizione di Azure](billing-how-to-change-credit-card.md) associato a tale servizio esterno. È possibile determinare quale sottoscrizione è associata l'ordine di servizio esterni a seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Fare clic su **tutte le risorse** nel menu di spostamento a sinistra.
     ![screenshot della voce di menu tutte le risorse](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Cercare il servizio esterno.
1. Cercare il nome della sottoscrizione nel **sottoscrizione** colonna.
    ![screenshot del nome della sottoscrizione per la risorsa](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. Fare clic sul nome della sottoscrizione e [aggiornare il metodo di pagamento attivo](billing-how-to-change-credit-card.md).

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>Annullare un ordine di servizio esterno
Se si desidera annullare l'ordine di servizio esterno, eliminare la risorsa nel [Portale di Azure](https://portal.azure.com).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Fare clic su **tutte le risorse** nel menu di spostamento a sinistra.
    ![screenshot della voce di menu tutte le risorse](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Cercare il servizio esterno.
1. Selezionare la casella accanto alla risorsa che si desidera eliminare.
1. Selezionare **eliminare** nella barra dei comandi.
    ![Screenshot del pulsante di eliminazione](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Tipo di *'Sì'* nel Pannello di conferma.
    ![Elimina la risorsa](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Fare clic su **Elimina**.

## <a name="check-access"></a>Verificare l'accesso
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- [Avviare l'analisi dei costi](../cost-management/quick-acm-cost-analysis.md)
