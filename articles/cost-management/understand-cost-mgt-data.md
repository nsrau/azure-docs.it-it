---
title: Informazioni sui dati di Gestione costi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni sui dati inclusi in Gestione costi di Azure e sulla frequenza di elaborazione, raccolta, visualizzazione e chiusura dei dati.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/11/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 5c8f70ff76da458fcc1433226a51012fb7ccd18e
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544637"
---
# <a name="understand-cost-management-data"></a>Informazioni sui dati di Gestione costi

Questo articolo fornisce informazioni sui dati inclusi in Gestione costi di Azure. Viene inoltre illustrata la frequenza di elaborazione, raccolta, visualizzazione e chiusura dei dati. L'uso di Azure viene addebitato su base mensile. Tuttavia, il tipo di sottoscrizione di Azure determina quando termina il mese di fatturazione. La frequenza con cui Gestione costi riceve i dati sull'utilizzo varia a seconda di diversi fattori. Tali fattori includono il tempo necessario per l'elaborazione dei dati e la frequenza con cui i servizi di Azure generano i dati sull'utilizzo per il sistema di fatturazione.

## <a name="supported-microsoft-offers"></a>Offerte Microsoft supportate

Le informazioni seguenti mostrano le [offerte di Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) attualmente supportate in Gestione costi di Azure.  Per offerta di Azure si intende il tipo di sottoscrizione di Azure di cui si dispone.

| Categoria  | **Nome dell'offerta** | **Quota ID** | **Numero offerta** |
| --- | --- | --- | --- |
| **Azure Germania** | [Azure Germania con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure per enti pubblici Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P |
| **Contratto Enterprise** | Sviluppo/test Enterprise                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P |
| **Contratto Enterprise** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P |
| **Contratto per i clienti di Microsoft** | [Piano di Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/D |
| **Contratto per i clienti di Microsoft** | [Piano di Microsoft Azure per sviluppo/Test](https://azure.microsoft.com/offers/ms-azr-0148g)  | MSDNDevTest_2014-09-01 | N/D |
| **Microsoft Developer Network (MSDN)** | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p) | MSDN_2014-09-01 | MS-AZR-0062P |
| **Pagamento in base al consumo** | [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p)                       | PayAsYouGo_2014-09-01 | MS-AZR-0003P |
| **Pagamento in base al consumo** | [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p)              | MSDNDevTest_2014-09-01 | MS-AZR-0023P |
| **Pagamento in base al consumo** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)           | MPN_2014-09-01 | MS-AZR-0025P |
| **Pagamento in base al consumo** | [Versione di prova gratuita](https://azure.microsoft.com/offers/ms-azr-0044p)                          | FreeTrial_2014-09-01 | MS-AZR-0044P |
| **Pagamento in base al consumo** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p)                       | AzureInOpen_2014-09-01 | MS-AZR-0111P |
| **Pagamento in base al consumo** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)                  | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Pagamento in base al consumo** | Azure Pass                                                                             | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P |
| **Visual Studio** | [Visual Studio Enterprise - MPN](https://azure.microsoft.com/offers/ms-azr-0029p)      | MPN_2014-09-01 | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)          | MSDN_2014-09-01 | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)     | MSDNDevTest_2014-09-01 | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)            | MSDN_2014-09-01 | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)  | MSDN_2014-09-01 | MS-AZR-0064P |

La tabella seguente mostra le offerte non supportate.

| Categoria  | **Nome dell'offerta** | **Quota ID** | **Numero offerta** |
| --- | --- | --- | --- |
| **Cloud Solution Provider (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Cloud Solution Provider (CSP)** | CSP Azure per enti pubblici                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Cloud Solution Provider (CSP)** | Azure Germania in CSP per Microsoft Cloud Germania   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pagamento in base al consumo**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pagamento in base al consumo**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Piani di supporto** | Supporto tecnico standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Piani di supporto** | Supporto Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Piani di supporto** | Supporto tecnico Developer                   | Default_2014-09-01 | MS-AZR-0043P |
| **Piani di supporto** | Piano di supporto per la Germania                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Piani di supporto** | Supporto tecnico Standard di Azure per enti pubblici   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Piani di supporto** | Supporto Pro-Direct di Azure per enti pubblici | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Piani di supporto** | Supporto tecnico Developer di Azure per enti pubblici  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

Per i clienti con le categorie di offerta con pagamento in base al consumo, MSDN e Visual Studio, i dati sono disponibili in Gestione costi a partire dal 10/02/2018. Per accedere ai dati per la sottoscrizione prima 02/10 marzo 2018, è possibile usare la [centro Account di Azure](https://account.azure.com/subscriptions) per scaricare l'utilizzo in dettaglio in un file CSV oppure è possibile utilizzare il [API per dettagli sull'uso](/rest/api/consumption/usagedetails).

## <a name="determine-your-offer-type"></a>Determinare il tipo di offerta
Se i dati relativi a una sottoscrizione non vengono visualizzati e si vuole determinare se la sottoscrizione rientra tra le offerte supportate, è possibile convalidare che la sottoscrizione sia supportata. Per convalidare che una sottoscrizione di Azure sia supportata, accedere al [portale di Azure](https://portal.azure.com). Selezionare quindi **Tutti i servizi** nel riquadro del menu a sinistra. Nell'elenco dei servizi selezionare **Sottoscrizioni**. Nel menu dell'elenco delle sottoscrizioni fare clic sulla sottoscrizione da verificare. La sottoscrizione verrà visualizzata nella scheda Panoramica e sarà possibile vedere le informazioni in **Offerta** e **ID offerta**. La figura seguente mostra un esempio.

![Esempio della scheda Panoramica della sottoscrizione che mostra l'offerta e l'ID offerta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Costi inclusi in Gestione costi

Le tabelle seguenti illustrano i dati inclusi o meno in Gestione costi. Tutti i costi vengono stimati fino a quando non viene generata una fattura. Crediti gratuiti e prepagati non includono i costi visualizzati.

**Dati su costi e utilizzo**

| **Incluso** | **Non incluso** |
| --- | --- |
| Utilizzo del servizio Azure<sup>1</sup> | Acquisti di prenotazioni - Per altre informazioni, vedere [API per l'automazione delle prenotazioni di Azure](../billing/billing-reservation-apis.md). |
| Mercato, offrendo utilizzo<sup>2</sup> | Acquisti del Marketplace - Per altre informazioni, vedere [Addebiti per servizi di terze parti](../billing/billing-understand-your-azure-marketplace-charges.md). |
|   | Addebiti per il supporto - Per altre informazioni, vedere [Spiegazioni delle condizioni di fatturazione](../billing/billing-understand-your-invoice.md). |
|   | Imposte - Per altre informazioni, vedere [Spiegazioni delle condizioni di fatturazione](../billing/billing-understand-your-invoice.md). |
|   | Crediti - Per altre informazioni, vedere [Spiegazioni delle condizioni di fatturazione](../billing/billing-understand-your-invoice.md). |

<sup>1</sup> L'utilizzo del servizio di Azure è basato sulla prenotazione e sui prezzi negoziati.
<sup>2</sup> marketplace offerta utilizzo non è disponibile per il pagamento a consumo, MSDN, e Visual Studio sono disponibili in questo momento.

**Metadata**

| **Incluso** | **Non incluso** |
| --- | --- |
| I tag delle risorse<sup>3</sup> | Tag dei gruppi di risorse |

<sup>3</sup> i tag delle risorse vengono applicati come utilizzo viene generato da ogni servizio e non sono disponibili in modo retroattivo a utilizzo cronologico.

## <a name="rated-usage-data-refresh-schedule"></a>Pianificazione dell'aggiornamento dei dati sull'utilizzo valutato

I dati sui costi e sull'utilizzo sono disponibili in Gestione dei costi e fatturazione nel portale di Azure e nelle [API di supporto](index.yml). Durante l'analisi dei costi, tenere presente quanto segue:

- Gli addebiti stimati per il periodo di fatturazione corrente vengono aggiornati sei volte al giorno.
- Gli addebiti stimati per il periodo di fatturazione corrente possono variare a causa di un aumento dell'utilizzo.
- Ogni aggiornamento è cumulativo e comprende tutte le voci e le informazioni relative all'aggiornamento precedente.
- Azure finalizza o _chiude_ il periodo di fatturazione corrente fino a 72 ore (tre giorni di calendario) dopo il termine del periodo di fatturazione.

Gli esempi seguenti illustrano come possono terminare i periodi di fatturazione.

Sottoscrizioni Enterprise Agreement (EA) - Se il mese di fatturazione termina il 31 marzo, gli addebiti stimati vengono aggiornati fino a 72 ore dopo. In questo esempio, entro la mezzanotte (UTC) del 4 aprile.

Sottoscrizioni con pagamento in base al consumo - Se il mese di fatturazione termina il 15 maggio, gli addebiti stimati potrebbero essere aggiornati fino a 72 ore dopo. In questo esempio, entro la mezzanotte (UTC) del 19 maggio.

### <a name="rerated-data"></a>Dati rivalutati

Quando si usano le [API di gestione dei costi](https://aka.ms/costmgmt/docs), PowerBI o il portale di Azure per recuperare i dati, prevedere una rivalutazione, e quindi una modifica, degli addebiti del periodo di fatturazione corrente, fino alla chiusura della fattura.

## <a name="usage-data-update-frequency-varies"></a>Variazione della frequenza di aggiornamento dei dati sull'utilizzo

La disponibilità dei dati sull'utilizzo in Gestione costi dipende da due fattori:

- La frequenza con cui i servizi di Azure (ad esempio Archiviazione, Calcolo, Rete CDN e SQL) generano i dati sull'utilizzo.
- Il tempo necessario per l'elaborazione dei dati sull'utilizzo tramite il motore di valutazione e le pipeline di gestione dei costi.

Alcuni servizi generano i dati sull'utilizzo più spesso rispetto ad altri. Pertanto, i dati per alcuni servizi potrebbero essere visualizzati in Gestione costi più rapidamente rispetto ad altri servizi che generano i dati con minore frequenza. In genere, la visualizzazione in Gestione costi dei dati sull'utilizzo richiede da 8 a 24 ore. Tenere presente che i dati per un mese aperto vengono aggiornati in caso di aumento dell'utilizzo, perché gli aggiornamenti sono cumulativi.

## <a name="historical-data-might-not-match-invoice"></a>I dati cronologici potrebbero non corrispondere fattura

I dati cronologici per le offerte basate su carta di credito e pagamento anticipato potrebbero non corrispondere la fattura. Alcune offerte di Azure con pagamento a consumo, MSDN e Visual Studio possono avere i crediti di Azure e pagamenti avanzati applicati alla fattura. Tuttavia, i dati cronologici visualizzati in Gestione costi si basa sugli addebiti solo stime sui consumi. Non includono dati cronologici a gestione costi pagamenti e crediti. Di conseguenza, i dati cronologici visualizzati per le offerte seguenti potrebbero non corrispondere esattamente a quelli di fattura.

-   MS-AZR-0029P
-   MS-AZR-0064P
-   MS-AZR-0170P
-   MS-AZR-0062P
-   MS-AZR-0123P
-   MS-AZR-0129P
-   MS-AZR-0044P
-   MS-AZR-0128P
-   MS-AZR-0120P
-   MS-AZR-0125P
-   MS-AZR-0059P
-   MS-AZR-0063P
-   MS-AZR-0060P
-   MS-AZR-0111P

## <a name="see-also"></a>Vedere anche 

- Se non è stata ancora completata la prima guida introduttiva di Gestione costi, esaminarla in [Avviare l’analisi dei costi](quick-acm-cost-analysis.md).
