---
title: Informazioni sugli addebiti riportati nella fattura del Contratto del cliente Microsoft - Azure
description: Informazioni su come leggere e interpretare gli addebiti nella fattura.
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
ms.openlocfilehash: 8ab6cf061531a1ef3c72b2f36d25932c7498d291
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345278"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Informazioni sugli addebiti riportati nella fattura del Contratto del cliente Microsoft

Per capire a cosa si riferiscono gli addebiti riportati nella fattura, è possibile analizzare le singole transazioni. Nell'account di fatturazione per un Contratto del cliente Microsoft, ogni mese viene generata una fattura per ogni profilo di fatturazione. La fattura include tutti gli addebiti del mese precedente. È possibile visualizzare le fatture nel portale di Azure. Per altre informazioni, vedere [Scaricare le fatture per un Contratto del cliente Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Questo articolo di applica a un account di fatturazione per un Contratto del cliente Microsoft. [Verificare di avere accesso a un Contratto del cliente Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Visualizzare le transazioni di una fattura nel portale di Azure

1. Accedere al [portale di Azure](https://www.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale di Azure](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selezionare **Tutte le transazioni** sul lato sinistro della pagina. A seconda dell'accesso, può essere necessario selezionare un account di fatturazione, un profilo di fatturazione o una sezione della fattura, quindi selezionare **Tutte le transazioni**.

4. Nella pagina Tutte le transazioni vengono visualizzate le informazioni seguenti:

    ![Screenshot che mostra l'elenco delle transazioni fatturate](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Colonna  |Definizione  |
    |---------|---------|
    |Data     | La data della transazione  |
    |ID fattura     | L'identificatore della fattura in cui viene fatturata la transazione. Se si invia una richiesta di supporto, condividere l'ID con il supporto tecnico di Azure per velocizzarne la gestione |
    |Tipo di transazione     |  Il tipo di transazione, ad esempio acquisto, annullamento e addebiti per l'utilizzo  |
    |Famiglia di prodotti     | La categoria del prodotto, ad esempio calcolo per le macchine virtuali o database per il database SQL di Azure|
    |SKU di prodotto     | Un codice univoco che identifica l'istanza del prodotto |
    |Amount     |  L'importo della transazione      |
    |Sezione della fattura     | La transazione viene visualizzata in questa sezione della fattura del profilo di fatturazione |
    |Profilo di fatturazione     | La transazione viene visualizzata nella fattura di questo profilo di fatturazione |

5. Cercare l'ID fattura per filtrare le transazioni in base alla fattura.

### <a name="view-transactions-by-invoice-sections"></a>Visualizzare le transazioni per sezioni della fattura

Le sezioni della fattura consentono di organizzare i costi per la fattura di un profilo di fatturazione. Per altre informazioni, vedere [Informazioni sulla sezione della fattura](billing-mca-overview.md#invoice-sections). Quando viene generata una fattura, al suo interno vengono visualizzati gli addebiti per tutte le sezioni del profilo di fatturazione.

L'immagine seguente mostra gli addebiti per la sezione Accounting Dept di una fattura di esempio.

![Immagine di esempio che mostra i dettagli in base alle informazioni della sezione della fattura](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Una volta identificati gli addebiti per una sezione della fattura, è possibile visualizzare le transazioni nel portale di Azure per analizzarli.

1. Passare alla pagina Tutte le transazioni nel portale di Azure per visualizzare le transazioni di una fattura. Per altre informazioni, vedere [Visualizzare le transazioni di una fattura nel portale di Azure](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Filtrare in base al nome della sezione della fattura per visualizzare le transazioni.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Esaminare gli addebiti in sospeso per stimare la fattura successiva

Nell'account di fatturazione per un Contratto del cliente Microsoft, gli addebiti vengono stimati e considerati in sospeso fino a quando non vengono fatturati. È possibile visualizzare gli addebiti in sospeso nel portale di Azure per stimare la fattura successiva. Gli addebiti in sospeso sono stime e non includono le imposte. Gli addebiti effettivi riportati nella fattura successiva variano a seconda degli addebiti in sospeso.

### <a name="view-summary-of-pending-charges"></a>Visualizzare il riepilogo degli addebiti in sospeso

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale di Azure](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione. Nell'account di fatturazione selezionare **Profili di fatturazione** e quindi un profilo di fatturazione.

4. Selezionare la scheda **Riepilogo** nella parte superiore della schermata.

5. Nella sezione relativa agli addebiti vengono visualizzati gli addebiti da inizio mese e quelli del mese scorso.

   ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale di Azure](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Gli addebiti da inizio mese sono quelli in sospeso per il mese corrente e vengono fatturati quando viene generata la fattura per il mese. Se la fattura relativa al mese scorso non è stata ancora generata, anche i relativi addebiti sono in sospeso e verranno visualizzati nella fattura successiva.

### <a name="view-pending-transactions"></a>Visualizzare le transazioni in sospeso

Una volta identificati gli addebiti in sospeso, è possibile analizzare le singole transazioni che li hanno generati. A questo punto, gli addebiti per l'utilizzo in sospeso non vengono visualizzati nella pagina Tutte le transazioni. È possibile visualizzarli nella pagina di sottoscrizioni di Azure. Per altre informazioni, vedere [Visualizzare gli addebiti per l'utilizzo in sospeso](#view-pending-usage-charges)

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale di Azure](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione. Nell'account di fatturazione selezionare **Profili di fatturazione** e quindi un profilo di fatturazione.

4. Selezionare **Tutte le transazioni** sul lato sinistro della pagina.

5. Cercare *In sospeso*. Usare il filtro **Intervallo di tempo** per visualizzare gli addebiti in sospeso per il mese corrente e per quello scorso.

   ![Screenshot che mostra l'elenco di transazioni in sospeso](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Visualizzare gli addebiti per l'utilizzo in sospeso

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare *Gestione dei costi e fatturazione*.

   ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale di Azure](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione. Nell'account di fatturazione selezionare **Profili di fatturazione** e quindi un profilo di fatturazione.

4. Selezionare **Tutte le sottoscrizioni** sul lato sinistro della pagina.

5. La pagina Sottoscrizioni di Azure visualizza gli addebiti del mese corrente e di quello scorso per ogni sottoscrizione del profilo di fatturazione. Gli addebiti da inizio mese sono quelli in sospeso per il mese corrente e vengono fatturati quando viene generata la fattura per il mese. Se la fattura relativa al mese scorso non è stata ancora generata, anche i relativi addebiti sono in sospeso.

    ![Screenshot che mostra l'elenco delle sottoscrizioni di Azure per il profilo di fatturazione](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analizzare gli addebiti per l'utilizzo di Azure

Usare il file CSV dei dati di utilizzo e addebiti di Azure per analizzare gli addebiti basati sull'utilizzo. È possibile scaricare il file per una fattura o per gli addebiti in sospeso. Per altre informazioni, vedere [Come ottenere la fattura e i dati di utilizzo giornalieri di Azure](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Visualizzare l'utilizzo dettagliato in base alla sezione della fattura

È possibile filtrare il file dei dati di utilizzo e addebiti di Azure per riconciliare gli addebiti per l'utilizzo per le sezioni della fattura.

La procedura seguente illustra come riconciliare gli addebiti per le risorse di calcolo per la sezione Accounting Dept della fattura:

![Immagine di esempio che mostra i dettagli in base alle informazioni della sezione della fattura](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | PDF della fattura | CSV dei dati di utilizzo e addebiti di Azure |
 | --- | --- |
 |Accounting Dept |invoiceSectionName |
 |Addebiti per l'utilizzo - Piano di Microsoft Azure |productOrderName |
 |Calcolo |serviceFamily |

1. Filtrare la colonna **invoiceSectionName** nel file CSV in base a **Accounting Dept**.
2. Filtrare la colonna **productOrderName** nel file CSV in base a **Piano di Microsoft Azure**.
3. Filtrare la colonna **serviceFamily** del file CSV in base a **Microsoft.Compute**.

![Screenshot che mostra il file dei dati di utilizzo e addebiti filtrato per sezione della fattura](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>Visualizzare l'utilizzo dettagliato per sottoscrizione

È possibile filtrare il file dei dati di utilizzo e addebiti di Azure per riconciliare gli addebiti per l'utilizzo per le sottoscrizioni. Per visualizzare tutte le sottoscrizioni in un profilo di fatturazione, vedere [Visualizzare gli addebiti per l'utilizzo in sospeso](#view-pending-usage-charges).

Una volta identificati gli addebiti per una sottoscrizione, usare il file CSV dei dati di utilizzo e addebiti di Azure per analizzarli.

L'immagine seguente mostra l'elenco di sottoscrizioni nel portale di Azure.

![Screenshot che mostra l'elenco delle sottoscrizioni di Azure per il profilo di fatturazione](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Filtrare la colonna **subscriptionName** nel file CSV dei dati di utilizzo e addebiti di Azure in base a **WA_Subscription** per visualizzare gli addebiti dettagliati per l'utilizzo relativi alla sottoscrizione WA_Subscription.

![Screenshot che mostra il file dei dati di utilizzo e addebiti filtrato per sottoscrizione](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Pagare la fattura

Nella parte inferiore della fattura sono riportate le istruzioni per il relativo pagamento. [Informazioni sulla modalità di pagamento](billing-mca-understand-your-invoice.md#how-to-pay).

Se la fattura è già stata saldata, è possibile verificare lo stato del pagamento nella pagina Fatture del portale di Azure.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto del cliente Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla fattura e sull'utilizzo dettagliato, vedere:

- [Come ottenere la fattura e i dati di uso giornalieri di Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Informazioni sui termini presenti nella fattura del Contratto del cliente Microsoft](billing-mca-understand-your-invoice.md)
- [Informazioni sui termini presenti nel file CSV di dati di utilizzo del Contratto del cliente Microsoft](billing-mca-understand-your-usage.md)
