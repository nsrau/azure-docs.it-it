---
title: Visualizzare e scaricare i prezzi di Azure dell'organizzazione
description: Informazioni su come visualizzare e scaricare i prezzi o stimare i costi con i prezzi dell'organizzazione.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: c38cd7f4b2fb1ae88b65031d3c93b153500b96ac
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375771"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Visualizzare e scaricare i prezzi di Azure dell'organizzazione

I clienti di Azure con un contratto Enterprise di Azure, Contratto del cliente Microsoft o Contratto Microsoft Partner possono visualizzare e scaricare i prezzi nel portale di Azure. [Informazioni su come controllare il tipo di account di fatturazione](#check-your-billing-account-type).

## <a name="download-pricing-for-an-enterprise-agreement"></a>Scaricare i prezzi per un contratto Enterprise

In base ai criteri impostati per l'organizzazione dall'amministratore dell'organizzazione, solo determinati ruoli amministrativi consentono di accedere alle informazioni sui prezzi per il Contratto Enterprise dell'organizzazione. Per altre informazioni, vedere [Informazioni sui ruoli amministrativi per il Contratto Enterprise di Azure](billing-understand-ea-roles.md).

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore dell'organizzazione.
1. Cercare *Gestione dei costi e fatturazione*.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. Nell'account di fatturazione selezionare **Utilizzo e addebiti**.

   ![Screenshot che mostra l'utilizzo e gli addebiti in Fatturazione](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. Selezionare ![Screenshot che mostra una ricerca del portale di Azure](./media/billing-ea-pricing/download-icon.png) **Scarica** per il mese.

1. In **Elenco prezzi** selezionare **Scarica CSV**.

   ![Screenshot che mostra il pulsante per scaricare il file CSV di elenco prezzi](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="download-pricing-for-an-mca-or-mpa-account"></a>Scaricare i prezzi per un account Contratto del cliente Microsoft o Contratto Microsoft Partner

Se si dispone di un Contratto del cliente Microsoft, per visualizzare e scaricare i prezzi è necessario avere il ruolo di proprietario, collaboratore, lettore o gestione fatture del profilo di fatturazione. Se si dispone di un Contratto Microsoft Partner, è necessario avere il ruolo di amministratore globale e agente amministratore nell'organizzazione partner per visualizzare e scaricare i prezzi.

### <a name="download-price-sheets-for-billed-charges"></a>Scaricare gli elenchi di prezzi per gli addebiti fatturati

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare *Gestione dei costi e fatturazione*.
1. Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare prima un account di fatturazione.
1. Selezionare **Fatture**.
1. Nella griglia della fattura trovare la riga corrispondente all'elenco di prezzi che si vuole scaricare.
1. Fare clic sui puntini di sospensione (`...`) alla fine della riga.
![Screenshot che mostra i puntini di sospensione selezionati](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Per visualizzare i prezzi per i servizi nella fattura selezionata, scegliere **Elenco prezzi della fattura**.
1. Per visualizzare i prezzi per tutti i servizi di Azure in un periodo di fatturazione specificato, selezionare **Elenco prezzi di Azure**.

![Screenshot che mostra il menu di scelta rapida con gli elenchi di prezzi](./media/billing-ea-pricing/contextmenu-pricesheet.png)

### <a name="download-price-sheets-for-the-current-billing-period"></a>Scaricare gli elenchi di prezzi per il periodo di fatturazione corrente

Se si dispone di un Contratto del cliente Microsoft, è possibile scaricare i prezzi per il periodo di fatturazione corrente.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare *Gestione dei costi e fatturazione*.
1. Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare prima un account di fatturazione.
1. Nell'area **Panoramica** trovare i collegamenti di download sotto agli addebiti da inizio mese.
1. Selezionare **Elenco prezzi di Azure**.
![Screenshot che mostra il download da Panoramica](./media/billing-ea-pricing/open-pricing.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Stimare i costi con il calcolatore dei prezzi di Azure

È anche possibile usare i prezzi dell'organizzazione per stimare i costi con il calcolatore dei prezzi di Azure.

1. Passare al [calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator).
1. In alto a destra selezionare **Accedi**.
1. In **Programs and Offer** (Programmi e offerta)  > **Programma di licenza** selezionare **Enterprise Agreement (EA)** .
1. In **Programs and Offer** (Programmi e offerta)  > **Contratto selezionato** selezionare **Nessun elemento selezionato**.

    ![Screenshot che mostra il pulsante per scaricare il file CSV di elenco prezzi](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Scegliere l'organizzazione.
1. Selezionare **Applica**.
1. Cercare e quindi aggiungere prodotti alla stima.
1. I prezzi stimati indicati vengono calcolati in base ai prezzi per l'organizzazione selezionata.

## <a name="check-your-billing-account-type"></a>Controllare il tipo di account di fatturazione
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Passaggi successivi

Se si è un cliente con contratto EA, vedere:

- [Gestire l'accesso alle informazioni di fatturazione del contratto EA per Azure](billing-manage-access.md)
- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare l'utilizzo e gli addebiti di Microsoft Azure](billing-download-azure-daily-usage.md)
- [Informazioni sulla fattura per i clienti con contratto Enterprise Agreement](billing-understand-your-bill-ea.md)

Se si ha un Contratto del cliente Microsoft, vedere:

- [Informazioni sui termini nell'elenco prezzi per un Contratto del cliente Microsoft](billing-mca-understand-pricesheet.md)
- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare l'utilizzo e gli addebiti di Microsoft Azure](billing-download-azure-daily-usage.md)
- [Visualizzare e scaricare i documenti fiscali per il profilo di fatturazione](billing-mca-download-tax-document.md)
- [Informazioni sugli addebiti nella fattura del profilo di fatturazione](billing-mca-understand-your-bill.md)
