---
title: Informazioni sulla fattura per Azure | Microsoft Docs
description: Informazioni su come leggere e comprendere l'utilizzo e la fattura per la sottoscrizione di Azure
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: 668b32e99ba9a3bdf8e8f16ac51c35c609444cd9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Comprendere la fattura per Microsoft Azure
Per comprendere la fattura di Azure, confrontare la fattura con il file dei dettagli di utilizzo giornaliero e con i report di gestione dei costi nel portale di Azure.

>[!NOTE]
>Questo articolo non si applica ai clienti con contratto Enterprise Agreement (EA). Se si ha un contratto Enterprise Agreement (EA), [è possibile trovare la documentazione per la fatturazione in Enterprise Portal](https://ea.azure.com/helpdocs/understandingYourInvoice).  

Per ottenere la fattura in formato PDF e una copia del download del file dei dettagli di utilizzo giornaliero in formato CSV, vedere [Ottenere la fattura e i dati di utilizzo giornaliero di Azure](billing-download-azure-invoice-daily-usage-date.md). 

Per una spiegazione dettagliata dei termini e delle descrizioni nella fattura e nel file dei dettagli di utilizzo giornaliero, vedere [Understand terms on your Microsoft Azure invoice](billing-understand-your-invoice.md) (Comprendere i termini nella fattura di Microsoft Azure) e [Understand terms on your Microsoft Azure detailed usage](billing-understand-your-usage.md) (Comprendere i termini nel file dei dettagli di utilizzo giornaliero). 

Per informazioni sui report di gestione dei costi, vedere [Gestione dei costi del Portale di Azure](https://docs.microsoft.com/en-us/azure/billing/billing-getting-started).

## <a name="charges"></a>Come posso assicurarmi che gli addebiti nella fattura siano corretti?
<div style="padding-top: 56.25%; position: relative; width: 100%;">
<iframe style="position: absolute;top: 0;left: 0;right: 0;bottom: 0;" width="100%" height="100%" src="https://www.youtube.com/embed/3YegFD769Pk" frameborder="0" allowfullscreen></iframe>
</div>

Se nella fattura è presente un addebito su cui si vogliono ricevere altre informazioni, sono disponibili due opzioni.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Opzione 1: Rivedere la fattura e confrontare l'utilizzo e i costi con il file dei dettagli di utilizzo in formato CSV

Il file con i dettagli di utilizzo in formato CSV illustra gli addebiti per periodo di fatturazione e utilizzo giornaliero. Per ottenere questi file con i dettagli di utilizzo in formato CSV, vedere [Ottenere la fattura e i dati di utilizzo giornaliero di Azure](https://docs.microsoft.com/en-us/azure/billing/billing-download-azure-invoice-daily-usage-date).

Gli addebiti relativi all'utilizzo vengono visualizzati a livello di contatore. I termini seguenti hanno lo stesso significato sia nella fattura che nel file con i dettagli di utilizzo. Ad esempio, il ciclo di fatturazione indicato nella fattura corrisponde al periodo di fatturazione indicato nel file con i dettagli di utilizzo.

 | Fattura (PDF) | Dettagli di utilizzo (CSV)|
 | --- | --- |
|Ciclo di fatturazione | Periodo di fatturazione |
 |Nome |Categoria misuratore |
 |Tipo |Sottocategoria contatore |
 |Risorsa |Nome misuratore |
 |Region |Area misuratore |
 |Consumato |Quantità consumata |
 |Incluso |Quantità inclusa |
 |Fatturabile |Quantità in eccesso |

La sezione **Costi di utilizzo** nella fattura riporta il valore totale per ogni misuratore utilizzato durante il periodo di fatturazione. Ad esempio, lo screenshot seguente illustra l'addebito per l'utilizzo del servizio Utilità di pianificazione di Azure.

![Addebiti di utilizzo nella fattura](./media/billing-understand-your-bill/1.png)

La sezione **Statement** (Rendiconto) del file CSV con i dettagli di utilizzo indica lo stesso addebito. Sia la quantità indicata in *Consumato* che *Valore* hanno una corrispondenza nella fattura.

![Addebiti di utilizzo nel file CSV](./media/billing-understand-your-bill/2.png)

Per visualizzare una suddivisione di questo addebito su base giornaliera, andare alla sezione **Utilizzo giornaliero** del CSV. Filtrare "Utilità di pianificazione" in *Categoria misuratore* per visualizzare i giorni in cui il misuratore è stato usato e la quantità di consumo. Vengono elencate anche le informazioni *Risorsa* e *Gruppo di risorse* per il confronto. La somma dei valori in *Consumato* corrisponderà a quanto indicato nella fattura.

![Sezione Utilizzo giornaliero nel CSV](./media/billing-understand-your-bill/3.png)

Per ottenere il costo giornaliero, moltiplicare le quantità in *Consumato* con il valore *Tariffa* della sezione **Statement** (Rendiconto).

Per altre informazioni sulla fattura, vedere [Comprendere la fattura di Azure](billing-understand-your-invoice.md).

Per informazioni su ogni colonna del CSV, vedere [Informazioni sui dettagli di utilizzo di Azure](billing-understand-your-invoice.md).

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Opzione 2: Rivedere la fattura e confrontarla con l'utilizzo e i costi riportati nel portale di Azure

È anche possibile usare il portale di Azure per verificare gli addebiti. Il portale di Azure offre grafici di gestione dei costi per una rapida panoramica dell'utilizzo e degli addebiti nella fattura.

Per continuare con l'esempio precedente, vedere la [pagina Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), selezionare la sottoscrizione e quindi scegliere **Analisi dei costi**. È quindi possibile specificare l'intervallo di tempo e visualizzare i costi di utilizzo per il servizio Utilità di pianificazione di Azure.

![Visualizzazione dell'analisi dei costi nel Portale di Azure](./media/billing-understand-your-bill/4.png)

Per visualizzare la suddivisione dei costi giornalieri in **Cronologia dei costi**, fare clic sulla riga.

![Visualizzazione Cronologia dei costi nel portale di Azure](./media/billing-understand-your-bill/5.png)

Per altre informazioni, vedere [Evitare costi imprevisti con la gestione dei costi e alla fatturazione di Azure](billing-getting-started.md#costs).

## <a name="external"></a>A cosa si riferiscono gli addebiti per servizi esterni?
I servizi esterni, noti anche come ordini di Azure Marketplace, sono erogati da fornitori di servizi indipendenti e vengono fatturati separatamente. Questi addebiti non compaiono nella fattura di Azure. Per altre informazioni, vedere [Informazioni sugli addebiti per i servizi esterni](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Come si effettua il pagamento?

Se il metodo di pagamento è impostato su carta di credito o carta di debito, il pagamento viene addebitato automaticamente entro 10 giorni dal termine del periodo di fatturazione. Nell'estratto conto della carta di credito la voce sarà **MSFT Azure**.

Se si [paga tramite fattura](billing-how-to-pay-by-invoice.md), inviare il pagamento al destinatario elencato nella parte inferiore della fattura. Per altre informazioni, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Come si controlla lo stato di un pagamento effettuato con carta di credito?

[Creare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per richiedere lo stato del pagamento. 

## <a name="tips-for-cost-management"></a>Suggerimenti per la gestione dei costi
- Stimare i costi usando il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) e il [calcolatore del costo totale di proprietà](https://aka.ms/azure-tco-calculator) e ottenere [informazioni dettagliate sui prezzi per ogni servizio](https://azure.microsoft.com/en-us/pricing/).
- [Impostare avvisi di fatturazione](billing-set-up-alerts.md).
- [Controllare regolarmente utilizzo e costi nel portale di Azure](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
