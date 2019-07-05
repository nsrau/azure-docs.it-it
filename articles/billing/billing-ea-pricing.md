---
title: Visualizzare e scaricare i prezzi di Azure dell'organizzazione
description: Informazioni su come visualizzare e scaricare prezzi o stimare i costi con i prezzi della propria organizzazione.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: a7f7da197a06dbbb730a7386882e534b8381cf9e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491362"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Visualizzare e scaricare i prezzi di Azure dell'organizzazione

I clienti di Azure con un Azure Enterprise Agreement (EA) o [contratto di Microsoft dal cliente](#check-your-access-to-a-microsoft-customer-agreement) può visualizzare e scaricare i relativi prezzi nel portale di Azure.

## <a name="ea-pricing"></a>Prezzi con contratto Enterprise

In base ai criteri impostati per l'organizzazione dall'amministratore dell'organizzazione, solo determinati ruoli amministrativi consentono di accedere alle informazioni sui prezzi per il Contratto Enterprise dell'organizzazione. Per altre informazioni, vedere [Informazioni sui ruoli amministrativi per il Contratto Enterprise di Azure](billing-understand-ea-roles.md).

1. Come amministratore dell'organizzazione, accedi alla [portale di Azure](https://portal.azure.com/).
1. Cercare *costi di gestione e fatturazione*.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. Nell'account di fatturazione selezionare **Utilizzo e addebiti**.

   ![Screenshot che mostra l'utilizzo e gli addebiti in Fatturazione](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. Selezionare ![Screenshot che mostra una ricerca del portale di Azure](./media/billing-ea-pricing/download-icon.png) **Scarica** per il mese.

1. In **Elenco prezzi** selezionare **Scarica CSV**.

   ![Screenshot che mostra il pulsante per scaricare il file CSV di elenco prezzi](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="microsoft-customer-agreement-pricing"></a>Contratto di Microsoft dal cliente dei prezzi

È necessario essere il fatturazione profilo proprietario, collaboratore, lettore o gestione della fattura per visualizzare e scaricare i prezzi. Per altre informazioni sui ruoli di fatturazione per i contratti dei clienti Microsoft, vedere [attività e ruoli del profilo di fatturazione](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="download-price-sheets-for-the-current-billing-period"></a>Download di elenchi prezzi per il periodo di fatturazione corrente

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare *costi di gestione e fatturazione*.
1. Selezionare un profilo di fatturazione. In base all'accesso, occorre prima selezionare un account di fatturazione.
1. Nel **Panoramica** area, fare clic sui collegamenti di download sotto gli addebiti month-to-date.
1. Selezionare **pricesheet Azure**.
![Screenshot che mostra il download dal dashboard Panoramica](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>Download di elenchi prezzi per gli addebiti fatturati

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare *costi di gestione e fatturazione*.
1. Selezionare un profilo di fatturazione. In base all'accesso, occorre prima selezionare un account di fatturazione.
1. Selezionare **Fatture**.
1. Nella griglia della fattura, individuare la riga della fattura corrispondenti all'elenco prezzi che si desidera scaricare.
1. Fare clic sui puntini di sospensione (`...`) alla fine della riga.
![Screenshot che mostra i puntini di sospensione selezionato](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Se si desidera visualizzare i prezzi per i servizi nella fattura selezionato, selezionare **pricesheet fattura**.
1. Se si desidera visualizzare i prezzi per tutti i servizi di Azure per il periodo di fatturazione specifico, selezionare **pricesheet Azure**.

![Screenshot che mostra il menu di scelta rapida con elenchi prezzi](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Stimare i costi con il calcolatore prezzi di Azure

È possibile utilizzare prezzi della tua organizzazione anche per stimare i costi con il calcolatore prezzi di Azure.

1. Passare al [calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator).
1. In alto a destra selezionare **Accedi**.
1. In **Programs and Offer** (Programmi e offerta)  > **Programma di licenza** selezionare **Enterprise Agreement (EA)** .
1. In **Programs and Offer** (Programmi e offerta)  > **Contratto selezionato** selezionare **Nessun elemento selezionato**.

    ![Screenshot che mostra il pulsante per scaricare il file CSV di elenco prezzi](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Scegliere l'organizzazione.
1. Selezionare **Applica**.
1. Cercare e quindi aggiungere i prodotti alla stima.
1. I prezzi stimati indicati vengono calcolati in base ai prezzi per l'organizzazione selezionata.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Controllare l'accesso a un contratto di Microsoft dal cliente
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Passaggi successivi

Se sei un cliente con contratto Enterprise Agreement, vedere:

- [Gestire l'accesso per le informazioni di fatturazione EA per Azure](billing-manage-access.md)
- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare l'utilizzo di Microsoft Azure e negli addebiti](billing-download-azure-daily-usage.md)
- [Comprendere la fattura per i clienti con contratto Enterprise](billing-understand-your-bill-ea.md)

Se si dispone di un contratto di clienti Microsoft, vedere:

- [Comprendere i termini nel foglio di prezzo per un contratto di Microsoft dal cliente](billing-mca-understand-pricesheet.md)
- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare l'utilizzo di Microsoft Azure e negli addebiti](billing-download-azure-daily-usage.md)
- [Visualizzare e scaricare documenti di imposta per il profilo di fatturazione](billing-mca-download-tax-document.md)
- [Comprendere gli addebiti nella fattura del proprio profilo di fatturazione](billing-mca-understand-your-bill.md)
