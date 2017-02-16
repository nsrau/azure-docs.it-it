---
title: Informazioni sulla fattura | Documentazione Microsoft
description: Informazioni su come leggere e comprendere l&quot;utilizzo e la fattura per la sottoscrizione di Azure
services: 
documentationcenter: 
author: genlin
manager: stevenpo
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/2/2016
ms.author: erihur;genli
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b107405081429626bf1e758567ae5427e2dbca15


---
# <a name="understand-your-bill-for-microsoft-azure"></a>Comprendere la fattura per Microsoft Azure
Per comprendere la fattura di Azure, esaminare la fattura con il riepilogo delle spese e il file con i dettagli di utilizzo giornaliero separato. Questo articolo descrive la maggior parte dei termini presenti sulla fattura e nel file di utilizzo giornaliero. Per ottenere questi file, vedere [Come scaricare la fattura e i dati di utilizzo giornalieri di Azure](../billing-download-azure-invoice-daily-usage-date.md). Se si usa una sottoscrizione di valutazione gratuita, è possibile ottenere le informazioni sull'utilizzo giornaliero ma la fattura non viene emessa.

Gli addebiti per le sottoscrizioni di Microsoft Azure dipendono dal piano tariffario. Alcuni piani tariffari, come quello riservato ai sottoscrittori di Visual Studio Enterprise (MPN), includono crediti mensili utilizzabili per qualsiasi servizio di Azure in base alle esigenze.

Nella fattura corrente possono essere presenti fino a 24 ore di utilizzo riferite alla fine del periodo di fatturazione precedente. Inoltre gli addebiti elencati negli estratti conto per i clienti internazionali sono solo a scopo di valutazione. Le banche hanno costi diversi per i tassi di conversione.

## <a name="a-namepdfa-understand-your-invoice-pdf"></a><a name="pdf"></a> Comprendere la fattura (PDF)
La fattura riporta un riepilogo degli addebiti. È disponibile per il download in formato PDF (Portable Document Format) dal [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [Come scaricare la fattura e i dati di utilizzo giornalieri di Azure](../billing-download-azure-invoice-daily-usage-date.md). 

Le sezioni seguenti riportano la maggior parte dei termini che sono presenti sulle fatture e le descrizioni per ciascun termine.

### <a name="account-information"></a>Informazioni sull'account
La sezione delle informazioni account riporta informazioni sull'utilizzo e il profilo.

![intestazione](./media/billing-understand-your-bill/Header.png)

| Termine | Descrizione |
| --- | --- |
| N. fattura |Identificatore univoco della fattura per scopi di registrazione. |
| Ciclo di fatturazione |Periodo a cui la fattura si riferisce |
| Data fattura |Data in cui è stata generata la fattura. |
| Metodo di pagamento |Tipo di pagamento utilizzato per l'account, ovvero fattura o carta di credito. Se è necessario aggiornare la carta di credito, vedere l'articolo [Come modificare la carta di credito usata per pagare una sottoscrizione di Azure](../billing-how-to-change-credit-card.md). Se l'utente rappresenta un'azienda, è possibile pagare la sottoscrizione di Azure con metodi di pagamento della fattura, ad esempio assegni, assegni con pagamento in 24 ore e bonifici. Vedere [Fatturazione di Azure](https://azure.microsoft.com/pricing/invoicing/). |
| Indirizzo di fatturazione |Indirizzo per i pagamenti per Microsoft Azure. È possibile aggiornare l'indirizzo di fatturazione associato con la carta di credito visitando la [pagina di sottoscrizione](https://account.windowsazure.com/subscriptions) nel Centro account di Azure. |
| Offerta di sottoscrizione |Tipo di offerta di sottoscrizione che è stato acquistato, ossia con pagamento in base al consumo, BizSpark Plus, Azure Pass e così via. Per passare dal pagamento in base al consumo a un'altra formula di sottoscrizione, vedere [Trasferire la sottoscrizione di Azure a un'altra offerta](../billing-how-to-switch-azure-offer.md) . |
| Indirizzo di posta elettronica proprietario account |Indirizzo di posta elettronica dell'account associato all'account Microsoft Azure. Per cambiare l'indirizzo e-mail, vedere [Come modificare le informazioni sul profilo dell'account Azure, ad esempio indirizzo di posta elettronica, indirizzo e numero di telefono di contatto](../billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Comprendere il riepilogo della fattura
La sezione relativa al **Riepilogo fattura** riassume le transazioni avvenute dall'ultima fattura e gli addebiti per l'utilizzo attuale.

![riepilogo della fattura](./media/billing-understand-your-bill/InvoiceSummary.png)

Nella sezione della fattura relativa a saldo precedente, pagamenti e saldo in sospeso sono riepilogate le transazioni avvenute dall'ultima fattura.

| Termine | Description |
| --- | --- |
| Saldo precedente |Importo totale dovuto nell'ultima fattura. |
| Pagamenti |Pagamenti totali applicati all'ultima fattura. |
| Saldo in sospeso (dal ciclo di fatturazione precedente) |Eventuali rettifiche della fattura (crediti o saldi) applicate all'account dopo l'ultima fattura. |

### <a name="understand-the-current-charges"></a>Comprendere gli addebiti attuali
La sezione Costi attuali della fattura riporta informazioni dettagliate sui costi mensili. 

| Termine | Description |
| --- | --- |
| Addebiti per utilizzo |Gli addebiti per l'utilizzo corrispondono agli addebiti mensili totali per una sottoscrizione. Questi sono costi addebitati per l'utilizzo effettuato nel mese precedente. |
| Sconti |Sconti sui servizi che sono stati applicati alla fattura corrente. |
| Rettifiche |Crediti vari o addebiti in sospeso applicati alla fattura corrente. Ad esempio, se è stata attivata l'offerta Visual Studio Enterprise con MSDN, sarà indicato un credito mensile. Se si annulla la sottoscrizione, vengono riportati tutti gli addebiti di utilizzo mensile che superano il credito mensile ottenuto con l'offerta di sottoscrizione. Gli addebiti sono conteggiati dall'inizio del periodo di fatturazione corrente fino alla data di annullamento della sottoscrizione. |


### <a name="sold-to-and-payment-instructions"></a>Istruzioni "venduto a" e di pagamento
La tabella seguente descrive le istruzioni "venduto a" e di pagamento riportate nella fattura.

| Termine | Description |
| --- | --- |
| Venduto a |Prepopolato con l'indirizzo del profilo associato all'account. Se è necessario cambiarlo, vedere [Come modificare le informazioni sul profilo dell'account Azure, ad esempio indirizzo di posta elettronica, indirizzo e numero di telefono di contatto](../billing-how-to-change-azure-account-profile.md). |
| Istruzioni per il pagamento |Se il metodo di pagamento scelto è quello con fattura, qui è indicato dove inviare assegni, assegni urgenti o bonifici. Per altre informazioni, vedere [Fatturazione di Azure](https://azure.microsoft.com/pricing/invoicing/) |

## <a name="a-namecsva-understand-detailed-usage-charges-csv"></a><a name="csv"></a> Comprendere i dettagli degli addebiti basati sull'uso (CSV)
Il file con i dati di utilizzo mostra la quantità di ogni risorsa che è stata utilizzata nel periodo di fatturazione corrente. È disponibile in un formato di file CSV (valori separati dalla virgola) che è possibile aprire in un foglio di calcolo. Se sono disponibili due versioni, scaricare la versione 2. Questo è il formato di file più recente. Per altre informazioni, vedere [Come scaricare la fattura e i dati di utilizzo giornalieri di Azure](../billing-download-azure-invoice-daily-usage-date.md).

I costi di utilizzo sono i costi **mensili** totali di una sottoscrizione dedotti eventuali crediti o sconti. Questi sono costi addebitati per l'utilizzo effettuato nel mese precedente.  

Le sezioni seguenti descrivono la maggior parte dei termini presenti nella versione 2 del file dei dati sull'utilizzo dettagliati.

### <a name="statement"></a>Istruzione 
La sezione superiore del file riporta i servizi che sono stati utilizzati durante il ciclo di fatturazione del mese precedente. La tabella seguente elenca i termini e le descrizioni mostrate in questa sezione.

| Termine | Descrizione |
| --- | --- |
|Periodo di fatturazione |Indica il periodo di fatturazione in cui la risorsa o il servizio è stata utilizzato. |
|Categoria misuratore |Identifica il servizio di primo livello a cui si riferisce l'utilizzo. |
|Sottocategoria misuratore |Definisce il tipo di servizio di Azure e può influire sulla tariffa. |
|Nome misuratore |Identifica l'unità di misura per la risorsa consumata. |
|Area misuratore |Identifica la posizione del datacenter per determinati servizi il cui prezzo dipende dalla posizione stessa. |
|SKU |Indica l'identificatore di sistema univoco per ogni risorsa di Azure. |
|Unità |Identifica l'unità in base alla quale viene addebitato il servizio. Ad esempio, GB, ore, decine di migliaia. |
|Quantità consumata |La quantità di risorsa utilizzata durante il periodo di fatturazione. |
|Quantità inclusa |Contiene la quantità di risorsa inclusa gratuitamente nel periodo di fatturazione corrente. |
|Quantità in eccesso |Mostra la differenza tra la quantità utilizzata e la quantità inclusa. Sarà addebitato questo importo. Per le offerte di pagamento in base al consumo senza alcuna quantità inclusa, questo totale corrisponde a quello della quantità consumata. |
|Entro l'impegno |Indica i costi della risorsa detratti dall'importo dell'impegno associato all'offerta di 6 o 12 mesi. I costi della risorsa sono detratti in ordine cronologico. |
|Valuta |La valuta utilizzata per il periodo di fatturazione corrente. |
|Eccedenza |Indica i costi della risorsa che eccedono l'importo dell'impegno associato all'offerta di 6 o 12 mesi. |
|Tariffa dell'impegno |Indica la tariffa dell'impegno basata sull'importo totale dell'impegno associato all'offerta di 6 o 12 mesi. |
|Tariffa |Indica la tariffa addebitata per ogni unità fatturabile. |
|Valore |Indica il risultato della moltiplicazione della colonna Quantità in eccesso per la colonna Tariffa. Se la quantità consumata non supera la quantità inclusa, non è indicato alcun addebito in questa colonna. |

### <a name="daily-usage"></a>Utilizzo giornaliero 

La sezione del file sull'utilizzo giornaliero indica i dettagli di utilizzo che influiscono sulle tariffe di fatturazione. La tabella seguente elenca i termini e le descrizioni mostrate in questa sezione. 

| Termine| Descrizione |
| --- | --- |
|Data utilizzo |Data in cui la risorsa è stata utilizzata. |
|Categoria misuratore |Identifica il servizio di primo livello a cui si riferisce l'utilizzo. |
|ID misuratore |ID della metrica fatturata. Viene utilizzato come identificatore per la fatturazione dei costi. |
|Sottocategoria misuratore |Definisce il servizio di Azure e può influire sulla tariffa. |
|Nome misuratore |Identifica l'unità di misura per la risorsa consumata. |
|Area misuratore|Identifica la posizione del datacenter per determinati servizi il cui prezzo dipende dalla posizione stessa. |
|Unità |Identifica l'unità in base alla quale viene addebitato il servizio. Ad esempio, GB, ore, decine di migliaia. |
|Quantità consumata |La quantità di risorsa consumata per il giorno corrente. |
|Percorso della risorsa |Identifica il datacenter in cui la risorsa è in esecuzione. |
|Servizio utilizzato |Il servizio della piattaforma Azure che è stato utilizzato. |
|Gruppo di risorse |Il gruppo di risorse in cui la risorsa distribuita è in esecuzione. Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). |
|ID istanza |Identificatore della risorsa. L'identificatore contiene il nome specificato per la risorsa al momento della creazione. È il nome della risorsa o l'ID risorsa completo. Per altre informazioni, vedere [L'API di Azure Resource Manager](/rest/api/resources/resources). |
|Tag |Tag assegnato alla risorsa. Usare i tag per raggruppare i record di fatturazione. Ad esempio è possibile usare i tag per distribuire i costi in base al reparto che utilizza la risorsa. I servizi che supportano la creazione di tag sono macchine virtuali, archiviazione e servizi di rete di cui si esegue il provisioning mediante l'[API di Azure Resource Manager](/rest/api/resources/resources). Per altre informazioni, vedere [Organize your Azure resources with tags](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) (Organizzare le risorse di Azure con i tag). |
|Informazioni aggiuntive |Metadati specifici del servizio. Ad esempio un tipo di immagine per una macchina virtuale. |
|Informazioni servizio 1 |Il nome del progetto a cui il servizio appartiene nella sottoscrizione. |
|Informazioni servizio 2 |Si tratta di un campo legacy che acquisisce i metadati specifici del servizio facoltativo. |

## <a name="how-do-i-make-a-payment"></a>Come si effettua un pagamento?
Se il metodo di pagamento è impostato su carta di credito o carta di debito, il pagamento viene eseguito automaticamente. Se è stata configurata una [modalità di pagamento con fattura](https://azure.microsoft.com/pricing/invoicing/), inviare il pagamento al destinatario indicato nella parte inferiore della fattura. Per altre informazioni, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Come si controlla lo stato di un pagamento effettuato con carta di credito?
[Creare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per richiedere lo stato del pagamento. 

## <a name="what-about-marketplace-orders-or-external-service-charges"></a>Per quanto riguarda gli ordini Marketplace o gli addebiti per servizi esterni?
I servizi esterni venivano definiti ordini di Marketplace. I servizi esterni vengono resi disponibili da fornitori di servizi indipendenti, ma sono integrati nell'ecosistema di Azure. Per altre informazioni, vedere [Informazioni sugli addebiti per i servizi esterni](../billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico. 
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
 






<!--HONumber=Dec16_HO2-->


