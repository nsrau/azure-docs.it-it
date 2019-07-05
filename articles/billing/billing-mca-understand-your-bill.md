---
title: Comprendere gli addebiti nella fattura del cliente contratto Microsoft - Azure
description: Informazioni su come leggere e comprendere gli addebiti nella fattura.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: ee250589133abb1944ff17e39dc650cbae4279c6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490667"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Informazioni sugli addebiti nella fattura di contratto di Microsoft dal cliente

È possibile comprendere gli addebiti nella fattura analizzando le singole transazioni. Nell'account di fatturazione per un contratto di Microsoft dal cliente, la fattura viene generata ogni mese per ogni profilo di fatturazione. La fattura include tutti gli addebiti del mese precedente. È possibile visualizzare le fatture nel portale di Azure. Per altre informazioni, vedere [scaricare le fatture per un contratto di Microsoft dal cliente](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Questo articolo si applica a un account di fatturazione per un contratto di clienti Microsoft. [Controllare se si ha accesso a un contratto di Microsoft dal cliente](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Visualizza transazioni per una fattura nel portale di Azure

1. Accedere al [portale di Azure](https://www.azure.com).

2. Cercare **costi di gestione e fatturazione**.

    ![Screenshot che mostra una ricerca nel portale Azure per gestione costi + fatturazione](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selezionare **tutte le transazioni** dal lato sinistro della pagina. In base all'accesso, potrebbe essere necessario selezionare un account di fatturazione, profilo di fatturazione o una sezione della fattura e quindi selezionare **tutte le transazioni**.

4. Pagina tutte le transazioni vengono visualizzate le informazioni seguenti:

    ![Screenshot che mostra l'elenco delle operazioni di fatturazione](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Colonna  |Definizione  |
    |---------|---------|
    |Date     | Data della transazione  |
    |ID fattura     | L'identificatore per la fattura in cui è stata fatturata alla transazione. Se si invia una richiesta di supporto, condividere l'ID con il supporto di Azure per accelerare la richiesta di supporto |
    |Tipo di transazione     |  Il tipo di transazione, ad esempio i costi di acquisto, Annulla e utilizzo  |
    |Famiglia di prodotti     | La categoria di prodotto, ad esempio risorse di calcolo per le macchine virtuali o database per il database SQL di Azure|
    |Sku prodotto     | Un codice univoco che identifica l'istanza del prodotto |
    |Amount     |  La quantità di transazioni      |
    |Sezione della fattura     | La transazione è visibile in questa sezione della fattura del profilo di fatturazione |
    |Profilo di fatturazione     | La transazione viene visualizzato nella fattura di questo servizio di fatturazione |

5. Cercare l'ID della fattura filtrare le transazioni per la fatturazione.

### <a name="view-transactions-by-invoice-sections"></a>Visualizza transazioni per le sezioni della fattura

Le sezioni della fattura consentono di organizzare i costi per la fatturazione del profilo di fatturazione. Per altre informazioni, vedere [comprendere sezione della fattura](billing-mca-overview.md#invoice-sections). Quando viene generata una fattura, gli addebiti per tutte le sezioni nel profilo di fatturazione vengono visualizzati nella fattura.

L'immagine seguente mostra gli addebiti per la sezione della fattura reparto contabilità in una fattura di esempio.

![Immagine di esempio che mostra i dettagli per informazioni della sezione della fattura](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Dopo aver identificato i costi per una sezione della fattura, è possibile visualizzare le transazioni nel portale di Azure per comprendere gli addebiti.

1. Passare alla pagina di tutte le transazioni nel portale di Azure per visualizzare le transazioni per una fattura. Per altre informazioni, vedere [visualizzare le transazioni per una fattura nel portale di Azure](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Filtra per nome della sezione della fattura per visualizzare le transazioni.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Esaminare in sospeso gli addebiti per stimare la fattura successiva

Nell'account di fatturazione per un contratto di clienti Microsoft, gli addebiti sono stimati e considerati in attesa fino a quando non la fatturazione. È possibile visualizzare in sospeso gli addebiti nel portale di Azure per stimare la fattura successiva. In sospeso vengono stimati addebiti e non includono l'IVA. Gli addebiti effettivi nella fattura successiva variano gli addebiti in sospeso.

### <a name="view-summary-of-pending-charges"></a>Visualizzare il riepilogo di in sospeso addebiti

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **costi di gestione e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale Azure per gestione costi + fatturazione](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selezionare un profilo di fatturazione. In base all'accesso, si potrebbe essere necessario selezionare un account di fatturazione. L'account di fatturazione, selezionare **fatturazione profili** quindi selezionare un profilo di fatturazione.

4. Selezionare **riepilogo** scheda nella parte superiore della schermata.

5. La sezione costi riportati month-to-date e gli addebiti del mese scorso.

   ![Screenshot che mostra una ricerca nel portale Azure per gestione costi + fatturazione](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Gli addebiti month-to-date sono gli addebiti in sospeso per il mese corrente e vengono fatturati quando viene generata la fattura per il mese. Se ancora non generata la fattura per il mese scorso, quindi gli addebiti del mese scorso sono anche in sospeso e verrà visualizzato nella fattura successiva.

### <a name="view-pending-transactions"></a>Visualizzare le transazioni in sospeso

Quando si rileva in sospeso i costi, è possibile comprendere gli addebiti analizzando le singole transazioni che hanno contribuito ai costi. A questo punto, in sospeso utilizzo addebiti non vengono visualizzati nella pagina tutte le transazioni. Nella pagina sottoscrizioni di Azure, è possibile visualizzare gli addebiti di utilizzo in sospeso. Per altre informazioni, vedere [Visualizza in sospeso i costi di utilizzo](#view-pending-usage-charges)

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **costi di gestione e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale Azure per gestione costi + fatturazione](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selezionare un profilo di fatturazione. In base all'accesso, si potrebbe essere necessario selezionare un account di fatturazione. L'account di fatturazione, selezionare **fatturazione profili** quindi selezionare un profilo di fatturazione.

4. Selezionare **tutte le transazioni** dal lato sinistro della pagina.

5. Cercare *in sospeso*. Usare la **Timespan** filtro per visualizzare in sospeso addebiti corrente o il mese scorso.

   ![Screenshot che mostra l'elenco delle transazioni in sospeso](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Visualizzare i costi di utilizzo in sospeso

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare *costi di gestione e fatturazione*.

   ![Screenshot che mostra una ricerca nel portale Azure per gestione costi + fatturazione](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selezionare un profilo di fatturazione. In base all'accesso, si potrebbe essere necessario selezionare un account di fatturazione. L'account di fatturazione, selezionare **fatturazione profili** quindi selezionare un profilo di fatturazione.

4. Selezionare **tutte le sottoscrizioni** uno lato sinistro della pagina.

5. La pagina sottoscrizioni di Azure Visualizza l'oggetto corrente e gli addebiti del mese per ogni sottoscrizione nel profilo di fatturazione. Gli addebiti month-to-date sono gli addebiti in sospeso per il mese corrente e vengono fatturati quando viene generata la fattura per il mese. Se ancora non generata la fattura per il mese scorso, quindi gli addebiti del mese scorso sono anche in sospeso.

    ![Screenshot che mostra l'elenco di sottoscrizioni di Azure per il profilo di fatturazione](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analizzare gli addebiti di utilizzo di Azure

Usare file CSV sull'utilizzo e costi di Azure per analizzare gli addebiti basati sull'utilizzo. È possibile scaricare il file per una fattura o in attesa di costi. Per altre informazioni, vedere [ottenere la fattura e utilizzo i dati giornalieri di Azure](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Visualizzare i dettagli di utilizzo dalla sezione della fattura

È possibile filtrare il file di utilizzo e costi di Azure per risolvere le spese di utilizzo per le sezioni della fattura.

I passaggi seguenti consentono di eseguire la riconciliazione i costi di calcolo per la sezione della fattura reparto contabilità:

![Immagine di esempio che mostra i dettagli per informazioni della sezione della fattura](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Fatture PDF | Utilizzo di Azure e gli addebiti CSV |
 | --- | --- |
 |Reparto di contabilità |invoiceSectionName |
 |Costi di utilizzo - piano di Microsoft Azure |productOrderName |
 |Calcolo |serviceFamily |

1. Filtro il **invoiceSectionName** colonna nel file CSV **Accounting Dept**.
2. Filtro il **productOrderName** colonna nel file CSV **piano di Microsoft Azure**.
3. Filtro il **serviceFamily** colonna nel file CSV **Microsoft. COMPUTE**.

![Screenshot che mostra l'utilizzo e gli addebiti per file filtrati dalla sezione della fattura](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>Visualizzare informazioni dettagliate sull'utilizzo per sottoscrizione

È possibile filtrare il file Azure per CSV sull'utilizzo e costi per risolvere le spese d'uso per le sottoscrizioni. Per visualizzare tutte le sottoscrizioni in un profilo di fatturazione, vedere [Visualizza in sospeso i costi di utilizzo](#view-pending-usage-charges).

Quando si identificano gli addebiti per una sottoscrizione, usare file CSV sull'utilizzo e costi di Azure per analizzare i costi.

L'immagine seguente mostra l'elenco delle sottoscrizioni nel portale di Azure.

![Screenshot che mostra l'elenco di sottoscrizioni di Azure per il profilo di fatturazione](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Filtro il **subscriptionName** colonna il Azure CSV sull'utilizzo e costi per **WA_Subscription** per visualizzare gli addebiti di utilizzo dettagliato per WA_Subscription.

![Screenshot che mostra l'utilizzo e gli addebiti per file filtrati in base a sottoscrizione](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Pagare la fattura

Le istruzioni per pagare la fattura vengono visualizzate nella parte inferiore della fattura. [Informazioni sul pagamento](billing-mca-understand-your-invoice.md#how-to-pay).

Se già stato pagato fattura, è possibile controllare lo stato del pagamento nella pagina delle fatture nel portale di Azure.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un contratto di Microsoft dal cliente
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla fattura e i dettagli di utilizzo, vedere:

- [Come ottenere la fattura e i dati di uso giornalieri di Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Comprendere i termini sulla fattura di contratto di Microsoft dal cliente](billing-mca-understand-your-invoice.md)
- [Informazioni sulle condizioni di utilizzo di contratto di Microsoft dal cliente CSV](billing-mca-understand-your-usage.md)
