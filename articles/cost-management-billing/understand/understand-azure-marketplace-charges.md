---
title: Informazioni sugli addebiti per i servizi esterni
description: Informazioni sulla fatturazione di servizi esterni, noti in precedenza come Marketplace, in Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8da85ec5781ff9575cf380092fea9e41d6af8786
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686123"
---
# <a name="understand-your-azure-external-services-charges"></a>Informazioni sugli addebiti per i servizi esterni di Azure
I servizi esterni vengono pubblicati da fornitori di software di terze parti in Azure Marketplace. Ad esempio, SendGrid è un servizio esterno che è possibile acquistare in Azure, ma che non viene pubblicato da Microsoft. Alcuni prodotti Microsoft vengono venduti anche tramite Azure Marketplace.

## <a name="how-external-services-are-billed"></a>Modalità di fatturazione dei servizi esterni

- Se si dispone di un Contratto del cliente Microsoft o Contratto Microsoft Partner, i servizi di terze parti vengono fatturati con gli altri servizi di Azure. [Controllare il tipo di account di fatturazione ](#check-billing-account-type) per verificare se si ha accesso a un Contratto del cliente Microsoft o Contratto Microsoft Partner.
- Se non si dispone di un Contratto del cliente Microsoft o Contratto Microsoft Partner, i servizi esterni vengono fatturati separatamente dai servizi di Azure. Si riceveranno due fatture per ogni periodo di fatturazione: una per i servizi di Azure e un'altra per gli acquisti nel Marketplace.
- Per ogni servizio esterno è previsto un modello di fatturazione diverso. Alcuni servizi vengono fatturati con pagamento in base al consumo, mentre altri prevedono addebiti mensili fissi.
- Non è possibile usare i crediti gratuiti mensili per i servizi esterni. Se si usa una sottoscrizione di Azure che include [crediti gratuiti](https://azure.microsoft.com/pricing/spending-limits/), questi non possono essere applicati agli addebiti dei servizi esterni. Durante il provisioning di un nuovo servizio esterno o di una nuova risorsa, viene visualizzato un avviso:

    ![Avviso di acquisto di Marketplace](./media/understand-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/understand-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/understand-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/understand-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/understand-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="external-spending-for-ea-customers"></a>Spesa esterna per i clienti EA

I clienti EA possono visualizzare la spesa dei servizi esterni e scaricare i report nel portale EA. Per iniziare, vedere [Azure Marketplace per i clienti EA](https://ea.azure.com/helpdocs/azureMarketplace).

## <a name="view-and-download-invoices-for-external-services"></a>Visualizzare e scaricare le fatture per i servizi esterni

Se si dispone di un Contratto del cliente Microsoft o un Contratto Microsoft Partner, i servizi di terze parti vengono fatturati con gli altri servizi di Azure in una singola fattura. [Controllare il tipo di account di fatturazione ](#check-billing-account-type) per verificare se si ha accesso a un Contratto del cliente Microsoft o Contratto Microsoft Partner. In caso affermativo, vedere [Visualizzare e scaricare le fatture nel portale di Azure](download-azure-invoice.md) per vedere gli addebiti di terze parti.

Se non si dispone di un Contratto del cliente Microsoft o Contratto Microsoft Partner, si riceveranno fatture separate per gli addebiti di terze parti. 

Gli addebiti per Azure Marketplace vengono visualizzati nella valuta locale.

È possibile visualizzare e scaricare le fatture di Azure Marketplace dal portale di Azure attenendosi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare **Gestione dei costi e fatturazione**.
1. Nel menu a sinistra selezionare **Fatture**.
1. Nell'elenco a discesa delle sottoscrizioni selezionare quella associata ai servizi del Marketplace.
1. Nell'elenco di fatture esaminare la colonna **Tipo**. Se una fattura riguarda un servizio del Marketplace, il tipo sarà **Azure Marketplace e prenotazioni**. 

    ![Screenshot del tipo Azure Marketplace nella griglia della fattura](./media/understand-azure-marketplace-charges/marketplace-type-twd.png)

1. Per filtrare in base al tipo in modo da esaminare solo le fatture per Azure Marketplace e prenotazioni, selezionare il filtro **Tipo**. Quindi selezionare **Azure Marketplace e prenotazioni** nell'elenco a discesa.

    ![Screenshot del filtro Tipo selezionato, con l'opzione Azure Marketplace e prenotazioni selezionata nell'elenco a discesa](./media/understand-azure-marketplace-charges/type-filter.png)

1. Selezionare l'icona per il download a destra della fattura da scaricare.

    ![Screenshot che mostra l'icona per il download selezionata per la fattura](./media/understand-azure-marketplace-charges/download-icon-marketplace.png)

1. In **Fattura** selezionare il pulsante blu **Download**.

    ![Screenshot che mostra il pulsante di download per la fattura nel riquadro del contesto](./media/understand-azure-marketplace-charges/invoice-download-marketplace.png)

## <a name="pay-for-external-services-in-the-azure-portal"></a>Pagamento per servizi esterni nel portale di Azure

Se si dispone di un Contratto del cliente Microsoft o Contratto Microsoft Partner, i servizi di terze parti vengono fatturati con gli altri servizi di Azure. [Controllare il tipo di account di fatturazione ](#check-billing-account-type) per verificare se si ha accesso a un Contratto del cliente Microsoft o Contratto Microsoft Partner. In tal caso, è possibile pagare l'intera fattura nel portale di Azure seguendo la procedura descritta in [Pagare la fattura per Microsoft Azure](pay-bill.md).

Se non si dispone di un Contratto del cliente Microsoft o un Contratto Microsoft Partner, è possibile pagare le fatture del Marketplace nel portale di Azure seguendo questa procedura:

1. Seguire i passaggi della sezione precedente, [Visualizzare e scaricare le fatture per i servizi esterni](#view-and-download-invoices-for-external-services), per trovare le fatture del Marketplace.
1. Selezionare il collegamento blu **Pagamento immediato** relativo alla fattura da pagare.

    ![Screenshot che mostra il collegamento Pagamento immediato nella griglia delle fatture](./media/understand-azure-marketplace-charges/pay-now-twd.png)

    >[!NOTE]
    > Il collegamento **Pagamento immediato** viene visualizzato solo se la fattura è di tipo **Azure Marketplace e prenotazioni** e lo stato del pagamento è scaduto.

1. Nella nuova pagina fare clic sul collegamento blu **Selezionare un metodo di pagamento**.

    ![Screenshot che mostra il collegamento Selezionare un metodo di pagamento selezionato](./media/understand-azure-marketplace-charges/select-payment-method-pay-now-twd.png)

1. Dopo aver selezionato un metodo di pagamento, fare clic sul pulsante blu **Pagamento immediato** nell'angolo in basso a sinistra della pagina.
    ![Screenshot che mostra il pulsante Pagamento immediato selezionato](./media/understand-azure-marketplace-charges/pay-now-button-twd.png)

## <a name="change-default-payment-for-external-services"></a>Cambiare il pagamento predefinito per i servizi esterni

Quando si acquista un servizio esterno, è necessario scegliere una sottoscrizione di Azure per la risorsa. Il metodo di pagamento della sottoscrizione di Azure selezionata diventa il metodo di pagamento del servizio esterno. Per modificare il metodo di pagamento di un servizio esterno, è necessario [modificare il metodo di pagamento della sottoscrizione di Azure](../manage/change-credit-card.md) associata al servizio esterno. È possibile individuare la sottoscrizione a cui è associato l'ordine del servizio esterno attenendosi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Fare clic su **Tutte le risorse** nel menu di spostamento a sinistra.
     ![screenshot della voce di menu tutte le risorse](./media/understand-azure-marketplace-charges/all-resources.png)
1. Cercare il servizio esterno.
1. Cercare il nome della sottoscrizione nella colonna **Sottoscrizione**.
    ![screenshot del nome della sottoscrizione per la risorsa](./media/understand-azure-marketplace-charges/sub-selected.png)
1. Fare clic sul nome della sottoscrizione e [aggiornare il metodo di pagamento attivo](../manage/change-credit-card.md).

## <a name="cancel-an-external-service-order"></a>Annullare un ordine di servizio esterno

Se si desidera annullare l'ordine di servizio esterno, eliminare la risorsa nel [Portale di Azure](https://portal.azure.com).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Fare clic su **Tutte le risorse** nel menu di spostamento a sinistra.
    ![Screenshot della voce di menu tutte le risorse](./media/understand-azure-marketplace-charges/all-resources.png)
1. Cercare il servizio esterno.
1. Selezionare la casella accanto alla risorsa da eliminare.
1. Selezionare **Elimina** nella barra dei comandi.
    ![Schermata del pulsante elimina](./media/understand-azure-marketplace-charges/delete-button.png)
1. Digitare *Sì* nel pannello di conferma.
    ![Eliminare una risorsa](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. Fare clic su **Elimina**.

## <a name="check-billing-account-type"></a>Controllare il tipo di account di fatturazione
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- [Avviare l'analisi dei costi](../costs/quick-acm-cost-analysis.md)