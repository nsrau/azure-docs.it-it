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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490359"
---
# <a name="understand-your-azure-external-services-charges"></a>Informazioni sugli addebiti per i servizi esterni di Azure
I servizi esterni vengono pubblicati da fornitori di software di terze parti nel marketplace di Azure. Ad esempio, SendGrid è un servizio esterno che è possibile acquistare in Azure, ma che non viene pubblicato da Microsoft. Alcuni prodotti Microsoft vengono venduti anche tramite Azure Marketplace.

## <a name="how-external-services-are-billed"></a>Modalità di fatturazione dei servizi esterni

- Se si dispone di un [Contratto del cliente Microsoft](#check-access), i servizi di terze parti vengono fatturati con il resto dei servizi di Azure.
- Se non si dispone di un Contratto del cliente Microsoft, i servizi esterni vengono fatturati separatamente dai servizi di Azure.
- Per ogni servizio esterno è previsto un modello di fatturazione diverso. Alcuni servizi vengono fatturati con pagamento in base al consumo, mentre altri prevedono addebiti mensili fissi.
- Non è possibile usare i crediti gratuiti mensili per i servizi esterni. Se si usa una sottoscrizione di Azure che include [crediti gratuiti](https://azure.microsoft.com/pricing/spending-limits/), questi non possono essere applicati agli addebiti dei servizi esterni. Durante il provisioning di un nuovo servizio esterno o di una nuova risorsa, viene visualizzato un avviso:

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

## <a name="view-and-download-invoices"></a>Visualizzare e scaricare fatture

Se si dispone di un [Contratto del cliente Microsoft](#check-access), gli addebiti di terze parti si trovano nella stessa fattura degli addebiti di Azure. Informazioni su come [visualizzare e scaricare la fattura di Azure](billing-download-azure-invoice.md) dal portale di Azure per vedere gli addebiti di terze parti.

Se non si dispone di un Contratto del cliente Microsoft, si riceveranno fatture separate per gli addebiti di terze parti. È possibile visualizzare e scaricare le fatture di Azure Marketplace dal portale di Azure attenendosi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare **Gestione dei costi e fatturazione**.
1. Nel menu a sinistra selezionare **Fatture**.
1. Fare clic sulla scheda **Azure Marketplace e prenotazioni**.  ![Immagine della scheda Azure Marketplace e prenotazioni](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. Nell'elenco a discesa delle sottoscrizioni selezionare la sottoscrizione che contiene i servizi esterni per i quali si vogliono visualizzare le fatture.

## <a name="external-spending-for-ea-customers"></a>Spesa esterna per i clienti EA

I clienti EA possono visualizzare la spesa dei servizi esterni e scaricare i report nel portale EA. Per iniziare, vedere [Azure Marketplace per i clienti EA](https://ea.azure.com/helpdocs/azureMarketplace).

## <a name="manage-payment-for-external-services"></a>Gestire i pagamenti per i servizi esterni

Quando si acquista un servizio esterno, è necessario scegliere una sottoscrizione di Azure per la risorsa. Il metodo di pagamento della sottoscrizione di Azure selezionata diventa il metodo di pagamento del servizio esterno. Per modificare il metodo di pagamento di un servizio esterno, è necessario [modificare il metodo di pagamento della sottoscrizione di Azure](billing-how-to-change-credit-card.md) associata al servizio esterno. È possibile individuare la sottoscrizione a cui è associato l'ordine del servizio esterno attenendosi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Fare clic su **Tutte le risorse** nel menu di spostamento a sinistra.
     ![screenshot della voce di menu tutte le risorse](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Cercare il servizio esterno.
1. Cercare il nome della sottoscrizione nella colonna **Sottoscrizione**.
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
1. Fare clic su **Tutte le risorse** nel menu di spostamento a sinistra.
    ![Screenshot della voce di menu tutte le risorse](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Cercare il servizio esterno.
1. Selezionare la casella accanto alla risorsa da eliminare.
1. Selezionare **Elimina** nella barra dei comandi.
    ![Schermata del pulsante elimina](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Digitare *Sì* nel pannello di conferma.
    ![Eliminare una risorsa](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Fare clic su **Elimina**.

## <a name="check-access"></a>Verificare l'accesso
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- [Avviare l'analisi dei costi](../cost-management/quick-acm-cost-analysis.md)
