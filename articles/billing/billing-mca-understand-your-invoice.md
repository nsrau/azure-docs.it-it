---
title: Informazioni sulla fattura del Contratto del cliente Microsoft in Azure
description: Informazioni su come leggere e comprendere la fattura del Contratto del cliente Microsoft in Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: a93a108a3820f712d6618646d8c11588d2b567b4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223740"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Termini presenti nella fattura del Contratto del cliente Microsoft in Azure

Questo articolo si applica a un account di fatturazione di Azure per un Contratto del cliente Microsoft. [Verificare di avere accesso a un Contratto del cliente Microsoft](#check-access-to-a-microsoft-customer-agreement).

La fattura contiene un riepilogo degli addebiti e le istruzioni per il pagamento. È disponibile per il download in formato PDF (Portable Document Format) dal [Portale di Azure](https://portal.azure.com/). In alternativa, è possibile riceverla tramite posta elettronica. Per altre informazioni, vedere [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md).

## <a name="billing-period"></a>Periodo di fatturazione

La fattura viene emessa mensilmente. È possibile individuare il giorno del mese in cui si riceveranno le fatture controllando la *data della fattura* nelle proprietà del profilo di fatturazione nel [portale di Azure](https://portal.azure.com/). Gli addebiti maturati tra la fine del periodo di fatturazione e la data della fattura saranno inclusi nella fattura del mese successivo, perché rientrano nel periodo di fatturazione successivo. Le date di inizio e di fine del periodo di fatturazione per ogni fattura sono elencate nel documento PDF della fattura sopra **Riepilogo fatturazione**.

## <a name="invoice-terms-and-descriptions"></a>Termini presenti nella fattura e descrizioni

Le sezioni seguenti riportano i termini importanti presenti nella fattura e le descrizioni per ogni termine.

### <a name="invoice-summary"></a>Riepilogo fattura

**Riepilogo fattura** si trova nella parte superiore della prima pagina e contiene informazioni sul profilo di fatturazione e sulla modalità di pagamento.

![Sezione di riepilogo della fattura](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Termine | DESCRIZIONE |
| --- | --- |
| Venduto a |Indirizzo della persona giuridica, riportato nelle proprietà dell'account di fatturazione|
| Indirizzo di fatturazione |Indirizzo di fatturazione del profilo di fatturazione che riceve la fattura, riportato nelle proprietà del profilo di fatturazione|
| Profilo di fatturazione |Nome del profilo di fatturazione che riceve la fattura |
| Numero ordine d'acquisto |Numero d'ordine d'acquisto, facoltativo, assegnato dall'utente per il monitoraggio |
| Numero di fattura |Numero di fattura univoco, generato da Microsoft, usato a scopo di monitoraggio |
| Data fattura |Data in cui la fattura viene generata, di solito da 5 a 12 giorni dopo la fine del ciclo di fatturazione. È possibile controllare la data della fattura nelle proprietà del profilo di fatturazione.|
| Condizioni di pagamento |Modalità di pagamento della fattura Microsoft. *Scadenza entro 30 giorni* significa che il pagamento deve essere effettuato entro 30 giorni dalla data della fattura. |

### <a name="billing-summary"></a>Riepilogo fatturazione

**Riepilogo fatturazione** mostra gli addebiti per il profilo di fatturazione dal periodo di fatturazione precedente, i crediti applicati, le imposte e l'importo totale dovuto.

![Sezione Riepilogo fatturazione](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Termine | DESCRIZIONE |
| --- | --- |
| Charges|Numero totale di addebiti Microsoft per il profilo di fatturazione dall'ultimo periodo di fatturazione |
| Credits |Crediti ricevuti da restituzioni |
| Crediti Azure applicati | Crediti Azure applicati automaticamente agli addebiti di Azure per ogni periodo di fatturazione |
| Subtotale |Importo dovuto al lordo delle imposte |
| Imposta |Tipo e importo delle imposte da pagare, a seconda del paese/area geografica del profilo di fatturazione. Se non è necessario pagare imposte, questa voce non verrà visualizzata nella fattura. |
| Risparmi totali stimati |Importo totale stimato che si è risparmiato grazie agli sconti validi. Se applicabili, i tassi di sconto validi sono elencati sotto gli articoli acquistati nei dettagli per sezione della fattura. |

### <a name="invoice-sections"></a>Sezioni della fattura

Per ogni sezione della fattura per il profilo di fatturazione verranno visualizzati gli addebiti, l'importo dei crediti Azure applicati, le imposte e l'importo totale dovuto.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Dettagli per sezione della fattura

I dettagli mostrano il costo per ogni sezione della fattura, suddiviso per ordine di prodotti. All'interno di ogni ordine di prodotti, il costo viene scomposto in base al tipo di servizio. Gli addebiti giornalieri per i prodotti e i servizi sono riportati nel portale di Azure e nel file CSV relativo all'utilizzo e agli addebiti di Azure. Per altre informazioni, vedere [Informazioni sugli addebiti nella fattura per un Contratto del cliente Microsoft](billing-mca-understand-your-bill.md).

L'importo totale dovuto per ogni famiglia di servizi viene calcolato sottraendo i *crediti Azure* da *crediti/addebiti* e aggiungendo le *imposte*:


![Dettagli per sezione della fattura](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Termine |DESCRIZIONE |
| --- | --- |
| Prezzo unitario | Prezzo unitario valido del servizio (nella valuta dei prezzi) usato per valutare l'utilizzo. È univoco per un prodotto, una famiglia di servizi, un contatore e un'offerta. |
| Qtà | Quantità acquistata o utilizzata durante il periodo di fatturazione |
| Charges/Credits (Addebiti/crediti) | Importo netto degli addebiti dopo l'applicazione di crediti e rimborsi |
| Credito Azure | Importo dei crediti Azure applicati a Charges/Credits (Addebiti/crediti)|
| Aliquota d'imposta | Aliquota/e d'imposta in base al paese/area geografica |
| Importo imposte | Importo delle imposte applicate all'acquisto in base all'aliquota |
| Totale | Importo totale dovuto per l'acquisto |

### <a name="how-to-pay"></a>Metodo di pagamento

Nella parte inferiore della fattura sono riportate le istruzioni per il relativo pagamento. Si può pagare tramite assegno o bonifico oppure online. In caso di pagamento online, è possibile usare una carta di credito o crediti Azure, se applicabili.

### <a name="publisher-information"></a>Informazioni sull'editore

Se nella fattura sono inclusi servizi di terze parti, il nome e l'indirizzo di ogni editore sono elencati nella parte inferiore della fattura.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto del cliente Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sugli addebiti nella fattura del profilo di fatturazione](billing-mca-understand-your-bill.md)
- [Come ottenere la fattura e i dati di uso giornalieri di Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Visualizzare i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)
- [Visualizzare i documenti fiscali per il profilo di fatturazione](billing-mca-download-tax-document.md)
