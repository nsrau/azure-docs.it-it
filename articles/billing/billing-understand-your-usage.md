---
title: Comprendere il file di utilizzo dettagliato di Azure | Microsoft Docs
description: Informazioni su come leggere e comprendere le sezioni del file CSV di utilizzo dettagliato relativo alla sottoscrizione di Azure
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 0d4f2e19ec3417b7ca111f777e34b1ce66ddb32d
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>Comprendere i dettagli degli addebiti basati sui dati di utilizzo dettagliato di Microsoft Azure 
Il file CSV con gli addebiti basati sull'utilizzo dettagliato contiene gli addebiti sia a livello giornaliero sia a livello di contatore per il periodo di fatturazione corrente. 

Per ottenere questo file con i dati di utilizzo dettagliato, vedere [Come ottenere la fattura e i dati di utilizzo giornalieri di Azure](billing-download-azure-invoice-daily-usage-date.md).
È disponibile in un formato di file CSV (valori separati dalla virgola) che è possibile aprire in un foglio di calcolo. Se sono disponibili due versioni, scaricare la versione 2. Questo è il formato di file più recente.

Gli addebiti in base all'utilizzo corrispondono agli addebiti **mensili** totali per una sottoscrizione e non tengono in considerazione eventuali accrediti o sconti applicabili.


<div style="padding-top: 56.25%; position: relative; width: 100%;">
<iframe style="position: absolute;top: 0;left: 0;right: 0;bottom: 0;" width="100%" height="100%" src="https://www.youtube.com/embed/p13S350M2Vk" frameborder="0" allowfullscreen></iframe>
</div>

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>Termini e descrizioni relativi al file di utilizzo dettagliato
Le sezioni seguenti descrivono i termini più importanti presenti nella versione 2 del file di utilizzo dettagliato.

### <a name="statement"></a>Istruzione
Nella sezione superiore del file CSV di utilizzo dettagliato sono riportati i servizi usati durante il periodo di fatturazione mensile. La tabella seguente elenca i termini e le descrizioni mostrate in questa sezione.

| Termine | Descrizione |
| --- | --- |
|Periodo di fatturazione |Indica il periodo di fatturazione in cui sono stati usati i contatori |
|Categoria misuratore |Identifica il servizio di primo livello a cui si riferisce l'utilizzo |
|Sottocategoria misuratore |Definisce il tipo di servizio di Azure che può influire sulla tariffa |
|Nome misuratore |Identifica l'unità di misura del contatore usato |
|Area misuratore |Identifica la posizione del centro dati per i servizi il cui prezzo dipende dalla posizione stessa del centro dati |
|SKU |Indica l'identificatore di sistema univoco per ogni contatore di Azure |
|Unità |Identifica l'unità in base alla quale viene addebitato il servizio. Ad esempio, GB, ore, decine di migliaia. |
|Quantità consumata |Contiene la quantità riportata dal contatore usata durante il periodo di fatturazione |
|Quantità inclusa |Contiene la quantità riportata dal contatore inclusa gratuitamente nel periodo di fatturazione corrente |
|Quantità in eccesso |Mostra la differenza tra la quantità utilizzata e la quantità inclusa. Sarà addebitato questo importo. Per le offerte di pagamento in base al consumo senza alcuna quantità inclusa, questo totale corrisponde a quello della quantità consumata. |
|Entro l'impegno |Indica i costi del contatore detratti dall'importo dell'impegno associato all'offerta di 6 o 12 mesi. I costi del contatore vengono detratti in ordine cronologico. |
|Valuta |Identifica la valuta usata nel periodo di fatturazione corrente |
|Eccedenza |Indica i costi del contatore che eccedono l'importo dell'impegno associato all'offerta di 6 o 12 mesi |
|Tariffa dell'impegno |Indica la tariffa dell'impegno basata sull'importo totale dell'impegno associato all'offerta di 6 o 12 mesi |
|Tariffa |Indica la tariffa addebitata per unità fatturabile |
|Valore |Indica il risultato della moltiplicazione della colonna Quantità in eccesso per la colonna Tariffa. Se la quantità consumata non supera la quantità inclusa, non è indicato alcun addebito in questa colonna. |

### <a name="daily-usage"></a>Utilizzo giornaliero

La sezione del file CSV sull'utilizzo giornaliero indica i dettagli di utilizzo che influiscono sulle tariffe di fatturazione. La tabella seguente elenca i termini e le descrizioni mostrate in questa sezione.

| Termine | Descrizione |
| --- | --- |
|Data utilizzo |Indica la data in cui è stato usato il contatore |
|Categoria misuratore |Identifica il servizio di primo livello a cui si riferisce l'utilizzo |
|ID misuratore |Identifica l'ID del contatore fatturato usato per determinare i prezzi relativi al consumo da fatturare |
|Sottocategoria misuratore |Definisce il tipo di servizio di Azure che può influire sulla tariffa |
|Nome misuratore |Identifica l'unità di misura del contatore usato |
|Area misuratore |Identifica la posizione del centro dati per i servizi il cui prezzo dipende dalla posizione stessa del centro dati |
|Unità |Identifica l'unità in base alla quale viene addebitato il contatore. Ad esempio, GB, ore, decine di migliaia. |
|Quantità consumata |Contiene la quantità riportata dal contatore usata nel giorno corrente |
|Percorso della risorsa |Identifica il centro dati in cui il contatore è in esecuzione |
|Servizio utilizzato |Indica il servizio della piattaforma Azure usato |
|Gruppo di risorse |Definisce il gruppo di risorse in cui è in esecuzione il contatore distribuito. <br/><br/>Per altre informazioni, vedere [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
|ID istanza | Specifica l'identificatore del contatore, <br/><br/> che contiene il nome specificato per il contatore al momento della creazione. È il nome della risorsa o l'ID risorsa completo. Per altre informazioni, vedere [L'API di Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). |
|Tag | Identifica il tag assegnato al contatore. Usare i tag per raggruppare i record di fatturazione.<br/><br/>È possibile, ad esempio, usare i tag per distribuire i costi in base al reparto che usa il contatore. I servizi che supportano la creazione di tag sono macchine virtuali, archiviazione e servizi di rete di cui si esegue il provisioning mediante l'[API di Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). Per altre informazioni, vedere [Organize your Azure resources with tags](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) (Organizzare le risorse di Azure con i tag). |
|Informazioni aggiuntive |Metadati specifici del servizio. Ad esempio un tipo di immagine per una macchina virtuale. |
|Informazioni servizio 1 |Indica il nome del progetto a cui il servizio appartiene nella sottoscrizione |
|Informazioni servizio 2 |Campo legacy che acquisisce i metadati specifici del servizio facoltativo |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>Come posso assicurarmi che gli addebiti nel file di utilizzo dettagliato siano corretti?
Se nel file di utilizzo dettagliato è presente un addebito su cui si vogliono ricevere altre informazioni, vedere [Comprendere la fattura per Microsoft Azure](./billing-understand-your-bill.md)

## <a name="external"></a>A cosa si riferiscono gli addebiti per servizi esterni?
I servizi esterni, noti anche come ordini Marketplace, sono erogati da fornitori di servizi indipendenti e vengono fatturati separatamente. Questi addebiti non compaiono nella fattura di Azure. Per altre informazioni, vedere [Informazioni sugli addebiti per i servizi esterni](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?) per ottenere una rapida risoluzione del problema.

