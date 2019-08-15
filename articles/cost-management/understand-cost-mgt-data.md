---
title: Informazioni sui dati di Gestione costi di Azure | Microsoft Docs
description: Questo articolo consente di comprendere meglio i dati inclusi in gestione costi di Azure e la frequenza con cui vengono elaborati, raccolti, mostrati e chiusi.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: e44acfc66a8faf76068af9c31233e89c45ee02f7
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68953157"
---
# <a name="understand-cost-management-data"></a>Informazioni sui dati di Gestione costi

Questo articolo aiuta a comprendere meglio i dati di utilizzo e i costi di Azure inclusi in gestione costi di Azure. Spiega la frequenza con cui i dati vengono elaborati, raccolti, mostrati e chiusi. L'uso di Azure viene addebitato su base mensile. Sebbene i cicli di fatturazione siano periodi mensili, le date di inizio e fine del ciclo variano in base al tipo di sottoscrizione. La frequenza con cui Gestione costi riceve i dati sull'utilizzo varia a seconda di diversi fattori. Tali fattori includono il tempo necessario per l'elaborazione dei dati e la frequenza con cui i servizi di Azure generano i dati sull'utilizzo per il sistema di fatturazione.

Gestione costi include tutti gli acquisti e l'utilizzo, incluse le prenotazioni e le offerte di terze parti per gli account Enterprise Agreement (EA). Gli account di Microsoft Customer Agreement (MCA) e le singole sottoscrizioni con tariffe a consumo includono solo i servizi di Azure e del Marketplace. Il supporto e altri costi non sono inclusi. I costi vengono stimati fino a quando non viene generata una fattura e non vengono calcolati nei crediti.

## <a name="supported-microsoft-azure-offers"></a>Offerte di Microsoft Azure supportate

Le informazioni seguenti mostrano le [offerte di Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) attualmente supportate in Gestione costi di Azure. Per offerta di Azure si intende il tipo di sottoscrizione di Azure di cui si dispone. I dati sono disponibili in gestione costi a partire dai **dati disponibili da** data. Se una sottoscrizione viene modificata, i costi prima della data di modifica dell'offerta non saranno disponibili. 

| **Categoria**  | **Nome dell'offerta** | **ID quota** | **Numero offerta** | **Dati disponibili da** |
| --- | --- | --- | --- | --- |
| **Azure Germania** | [Azure Germania con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P | 2 ottobre 2018<sup>2</sup> |
| **Azure Government** | Azure per enti pubblici Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | 2014 maggio<sup>1</sup> |
| **Contratto Enterprise** | Sviluppo/test Enterprise                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | 2014 maggio<sup>1</sup> |
| **Contratto Enterprise** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | 2014 maggio<sup>1</sup> |
| **Contratto per i clienti Microsoft** | [Piano di Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/D | 2019 marzo<sup>3</sup> |
| **Contratto per i clienti Microsoft** | [Piano di Microsoft Azure per sviluppo/test](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/D | 2019 marzo<sup>3</sup> |
| **Microsoft Developer Network (MSDN)** | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p) <sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 ottobre 2018<sup>2</sup> |
| **Pagamento in base al consumo** | [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 ottobre 2018<sup>2</sup> |
| **Pagamento in base al consumo** | [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 ottobre 2018<sup>2</sup> |
| **Pagamento in base al consumo** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2 ottobre 2018<sup>2</sup> |
| **Pagamento in base al consumo** | [Versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) <sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 ottobre 2018<sup>2</sup> |
| **Pagamento in base al consumo** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p) <sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 ottobre 2018<sup>2</sup> |
| **Pagamento in base al consumo** | [Azure per studenti](https://azure.microsoft.com/offers/ms-azr-0170p) <sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P | 2 ottobre 2018<sup>2</sup> |
| **Pagamento in base al consumo** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | 2 ottobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise-MPN](https://azure.microsoft.com/offers/ms-azr-0029p) <sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 ottobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 ottobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 ottobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 ottobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 ottobre 2018<sup>2</sup> |

_<sup>**1**</sup> per i dati prima del 2014 maggio, visitare il [portale di Azure Enterprise](https://ea.azure.com)._

_<sup>**2**</sup> per i dati precedenti al 2 ottobre 2018, visitare il [centro account di Azure](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> i contratti con i clienti Microsoft sono stati avviati nel 2019 marzo e non contengono dati cronologici prima di questo punto._

_<sup>**4**</sup> i dati cronologici per le sottoscrizioni basate su crediti e con pagamento in anticipo potrebbero non corrispondere alla fattura. Vedere [i dati cronologici potrebbero non corrispondere alla fattura](#historical-data-might-not-match-invoice) riportata di seguito._

Le offerte seguenti non sono ancora supportate:

| Category  | **Nome dell'offerta** | **ID quota** | **Numero offerta** |
| --- | --- | --- | --- |
| **Azure Germania** | [Azure Germania con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
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

## <a name="determine-your-offer-type"></a>Determinare il tipo di offerta
Se i dati relativi a una sottoscrizione non vengono visualizzati e si vuole determinare se la sottoscrizione rientra tra le offerte supportate, è possibile convalidare che la sottoscrizione sia supportata. Per convalidare che una sottoscrizione di Azure sia supportata, accedere al [portale di Azure](https://portal.azure.com). Selezionare quindi **Tutti i servizi** nel riquadro del menu a sinistra. Nell'elenco dei servizi selezionare **Sottoscrizioni**. Nel menu dell'elenco delle sottoscrizioni fare clic sulla sottoscrizione da verificare. La sottoscrizione verrà visualizzata nella scheda Panoramica e sarà possibile vedere le informazioni in **Offerta** e **ID offerta**. La figura seguente mostra un esempio.

![Esempio della scheda Panoramica della sottoscrizione che mostra l'offerta e l'ID offerta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Costi inclusi in Gestione costi

Le tabelle seguenti illustrano i dati inclusi o meno in Gestione costi. Tutti i costi vengono stimati fino a quando non viene generata una fattura. I costi indicati non includono crediti gratuiti e prepagati.

**Dati su costi e utilizzo**

| **Incluso** | **Non incluso** |
| --- | --- |
| Utilizzo del servizio di Azure<sup>5</sup>        | Addebiti per il supporto - Per altre informazioni, vedere [Spiegazioni delle condizioni di fatturazione](../billing/billing-understand-your-invoice.md). |
| Utilizzo offerta Marketplace<sup>6</sup> | Imposte - Per altre informazioni, vedere [Spiegazioni delle condizioni di fatturazione](../billing/billing-understand-your-invoice.md). |
| Acquisti nel Marketplace<sup>6</sup>      | Crediti - Per altre informazioni, vedere [Spiegazioni delle condizioni di fatturazione](../billing/billing-understand-your-invoice.md). |
| Acquisti prenotazione<sup>7</sup>      |  |
| Ammortamento degli acquisti di prenotazione<sup>7</sup>      |  |

_<sup>**5**</sup> l'utilizzo dei servizi di Azure è basato sulla prenotazione e sui prezzi negoziati._

_<sup>**6**</sup> gli acquisti nel Marketplace non sono attualmente disponibili per le offerte con pagamento in base al consumo, MSDN e Visual Studio._

_<sup>**7**</sup> gli acquisti di prenotazione sono disponibili solo per gli account Enterprise Agreement (EA) al momento._

**Metadata**

| **Incluso** | **Non incluso** |
| --- | --- |
| Tag delle risorse<sup>8</sup> | Tag dei gruppi di risorse |

_<sup>**8**</sup> i tag delle risorse vengono applicati quando l'utilizzo viene emesso da ogni servizio e non sono disponibili in modo retroattivo per l'utilizzo cronologico._

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

Se si usano le [API di gestione costi](index.yml), Power bi o il portale di Azure per recuperare i dati, si prevede che i costi del periodo di fatturazione corrente vengano rivalutati e, di conseguenza, cambiano fino a quando la fattura non viene chiusa.

## <a name="usage-data-update-frequency-varies"></a>Variazione della frequenza di aggiornamento dei dati sull'utilizzo

La disponibilità dei dati sull'utilizzo in Gestione costi dipende da due fattori:

- La frequenza con cui i servizi di Azure (ad esempio Archiviazione, Calcolo, Rete CDN e SQL) generano i dati sull'utilizzo.
- Il tempo necessario per l'elaborazione dei dati sull'utilizzo tramite il motore di valutazione e le pipeline di gestione dei costi.

Alcuni servizi generano i dati sull'utilizzo più spesso rispetto ad altri. Pertanto, i dati per alcuni servizi potrebbero essere visualizzati in Gestione costi più rapidamente rispetto ad altri servizi che generano i dati con minore frequenza. In genere, la visualizzazione in Gestione costi dei dati sull'utilizzo richiede da 8 a 24 ore. Tenere presente che i dati per un mese aperto vengono aggiornati in caso di aumento dell'utilizzo, perché gli aggiornamenti sono cumulativi.

## <a name="historical-data-might-not-match-invoice"></a>I dati cronologici potrebbero non corrispondere alla fattura

I dati cronologici per le offerte basate su crediti e con pagamento in anticipo potrebbero non corrispondere alla fattura. Alcune offerte di Azure con pagamento in base al consumo, MSDN e Visual Studio possono avere crediti di Azure e pagamenti avanzati applicati alla fattura. Tuttavia, i dati cronologici visualizzati in gestione costi si basano solo sugli addebiti per l'utilizzo stimato. I dati cronologici di gestione costi non includono pagamenti e crediti. Di conseguenza, i dati cronologici visualizzati per le offerte seguenti potrebbero non corrispondere esattamente alla fattura.

- Azure per studenti (MS-AZR-0170P)
- Azure in Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Versione di valutazione gratuita (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Vedere anche

- Se non è stata ancora completata la prima guida introduttiva di Gestione costi, esaminarla in [Avviare l’analisi dei costi](quick-acm-cost-analysis.md).
