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
ms.date: 06/29/2017
ms.author: tonguyen
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 264078587b81d7840fe5976498a91ae4f535b6aa
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017


---
# <a name="understand-your-bill-for-microsoft-azure"></a>Comprendere la fattura per Microsoft Azure
Per comprendere la fattura di Azure, confrontare la fattura con il file dei dettagli di utilizzo giornaliero e con i report di gestione dei costi nel Portale di Azure.

Per scaricare la fattura in formato PDF e il file dei dettagli di utilizzo giornaliero in formato CSV, vedere [Come ottenere la fattura e i dati di utilizzo giornaliero di Azure](billing-download-azure-invoice-daily-usage-date.md). 

Per una spiegazione dettagliata dei termini e delle descrizioni nella fattura e nel file dei dettagli di utilizzo giornaliero, vedere [Understand terms on your Microsoft Azure invoice](billing-understand-your-invoice.md) (Comprendere i termini nella fattura di Microsoft Azure) e [Understand terms on your Microsoft Azure detailed usage](billing-understand-your-usage.md) (Comprendere i termini nel file dei dettagli di utilizzo giornaliero).

Per informazioni sui report di gestione dei costi, vedere [Gestione dei costi del Portale di Azure](https://docs.microsoft.com/en-us/azure/billing/billing-getting-started).

## <a name="reconcile"></a>Come posso assicurarmi che gli addebiti nella fattura siano corretti?
Se nella fattura è presente un addebito su cui si vogliono ricevere altre informazioni, sono disponibili due opzioni:

### <a name="review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Rivedere la fattura e confrontare l'utilizzo e i costi riportati all'interno di essa con il file dei dettagli di utilizzo in formato CSV

Il file con i dettagli di utilizzo in formato CSV illustra gli addebiti per periodo di fatturazione e utilizzo giornaliero. Per ottenere questi file con i dettagli di utilizzo in formato CSV, vedere [Come ottenere la fattura e i dati di utilizzo giornaliero di Azure](https://docs.microsoft.com/en-us/azure/billing/billing-download-azure-invoice-daily-usage-date).

Gli addebiti relativi all'utilizzo vengono visualizzati a livello di contatore. I termini elencati di seguito hanno lo stesso significato nella fattura e nel file con i dettagli di utilizzo. Ad esempio, il ciclo di fatturazione indicato nella fattura corrisponde al periodo di fatturazione indicato nel file con i dettagli di utilizzo.

 | Fattura (PDF) | Dettagli di utilizzo (CSV)|
 | --- | --- |
 |Ciclo di fatturazione | Periodo di fatturazione |
 |Nome |Categoria misuratore |
 |Type |Sottocategoria contatore |
 |Risorsa |Nome misuratore |
 |Region |Area misuratore |
 |Consumato |Quantità consumata |
 |Incluso |Quantità inclusa |
 |Fatturabile |Quantità in eccesso |

La sezione relativa agli addebiti per l'utilizzo nella fattura riporta il valore totale per ogni contatore utilizzato durante il periodo di fatturazione. Ad esempio, lo screenshot seguente illustra l'addebito per l'utilizzo del servizio Utilità di pianificazione di Azure.

![Addebiti di utilizzo nella fattura](./media/billing-understand-your-bill/1.png)

Lo stesso addebito è presente nella sezione del rendiconto del file CSV con i dettagli di utilizzo.

![Addebiti di utilizzo nel file CSV](./media/billing-understand-your-bill/2.png)

Nel file dei dettagli di utilizzo in formato CSV è presente anche una sezione sull'utilizzo giornaliero. La sezione riporta una voce per ogni giorno di utilizzo del contatore. La somma di queste voci deve corrispondere alla quantità utilizzata per il contatore durante il periodo di fatturazione.

Per i dettagli sulle sezioni, sui termini e sulle descrizioni della fattura. Vedere [qui](billing-understand-your-invoice.md).

Per i dettagli sulle sezioni, sui termini e sulle descrizioni nel file di utilizzo giornaliero dettagliate, vedere [qui](billing-understand-your-usage.md).

### <a name="review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Rivedere la fattura e confrontarla con l'utilizzo e i costi riportati nel Portale di Azure

Il Portale di Azure consente anche di verificare gli addebiti. Il portale di Azure offre grafici di gestione dei costi per una rapida panoramica dell'utilizzo e degli addebiti nella fattura.

Ecco alcuni esempi di strumenti di gestione dei costi disponibili nel Portale di Azure:

![Costo per risorsa nel Portale di Azure](./media/billing-understand-your-bill/3.png)

Questo grafico presenta i costi correnti per risorsa.

![Tasso e previsione di spesa nel Portale di Azure](./media/billing-understand-your-bill/4.png)

Questo grafico illustra i costi correnti per questa sottoscrizione e il costo previsto per il resto del periodo di fatturazione.

![Visualizzazione dell'analisi dei costi nel Portale di Azure](./media/billing-understand-your-bill/5.png)

L'analisi dei costi presenta l'utilizzo a livello di risorsa e di contatore per diversi periodi di fatturazione.

Per altre informazioni, vedere [Evitare costi imprevisti con la gestione dei costi e alla fatturazione di Azure](billing-getting-started.md#costs).

## <a name="external"></a>A cosa si riferiscono gli addebiti per servizi esterni?
I servizi esterni, noti anche come ordini Marketplace, sono erogati da fornitori di servizi indipendenti e vengono fatturati separatamente. Questi addebiti non compaiono nella fattura di Azure. Per altre informazioni, vedere [Informazioni sugli addebiti per i servizi esterni](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Come si effettua il pagamento?
Se il metodo di pagamento è impostato su carta di credito o carta di debito, il pagamento viene eseguito automaticamente. Nell'estratto conto della carta di credito la voce sarà **MSFT Azure**.

Se si usa un [metodo di pagamento con fattura](https://azure.microsoft.com/pricing/invoicing/), inviare il pagamento al destinatario elencato nella parte inferiore della fattura. Per altre informazioni, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Come si controlla lo stato di un pagamento effettuato con carta di credito?
[Creare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per richiedere lo stato del pagamento. 

## <a name="tips-for-cost-management"></a>Suggerimenti per la gestione dei costi
- Stimare i costi usando il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/), il [calcolatore del costo totale di proprietà](https://aka.ms/azure-tco-calculator) e quando si aggiunge un servizio
- [Impostare avvisi di fatturazione per le sottoscrizioni Microsoft Azure](billing-set-up-alerts.md)
- [Review your usage and costs regularly in Azure portal](billing-getting-started.md#costs) (Verificare regolarmente utilizzo e costi nel portale di Azure)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico. 
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

