---
title: Informazioni sui dati di Gestione costi di Azure
description: Questo articolo fornisce informazioni sui dati inclusi in Gestione costi di Azure e sulla frequenza di elaborazione, raccolta, visualizzazione e chiusura dei dati.
author: bandersmsft
ms.author: banders
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.openlocfilehash: 6e27f34572eef7d50c7b140c65cb78c6d0678fa8
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477603"
---
# <a name="understand-cost-management-data"></a>Informazioni sui dati di Gestione costi

Questo articolo fornisce informazioni sui dati relativi ai costi e all'utilizzo di Azure, inclusi in Gestione costi di Azure. Viene illustrata la frequenza di elaborazione, raccolta, visualizzazione e chiusura dei dati. L'uso di Azure viene addebitato su base mensile. Sebbene i cicli di fatturazione siano periodi mensili, le date di inizio e fine del ciclo variano in base al tipo di sottoscrizione. La frequenza con cui Gestione costi riceve i dati sull'utilizzo varia a seconda di diversi fattori. Tali fattori includono il tempo necessario per l'elaborazione dei dati e la frequenza con cui i servizi di Azure generano i dati sull'utilizzo per il sistema di fatturazione.

Gestione costi include tutti gli utilizzi e gli acquisti, incluse le prenotazioni e le offerte di terze parti per gli account Contratto Enterprise (EA). Gli account del Contratto del cliente Microsoft e le singole sottoscrizioni con tariffe con pagamento in base al consumo includono solo i servizi di Azure e di Marketplace. Il supporto e altri costi non sono inclusi. I costi vengono stimati fino a quando non viene generata una fattura e non vengono calcolati nei crediti.

Se si ha una nuova sottoscrizione, non è possibile usare immediatamente le funzionalità di Gestione costi. Potrebbero essere necessarie fino a 48 ore prima di poter usarle usare tutte.

## <a name="supported-microsoft-azure-offers"></a>Offerte Microsoft Azure supportate

Le informazioni seguenti mostrano le [offerte di Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) attualmente supportate in Gestione costi di Azure. Per offerta di Azure si intende il tipo di sottoscrizione di Azure di cui si dispone. I dati sono disponibili in Gestione costi a partire dai **Dati disponibili da una determinata** data. Se in una sottoscrizione vengono modificate le offerte, i costi prima della data di modifica dell'offerta non sono disponibili.

| **Categoria**  | **Nome dell'offerta** | **ID quota** | **Numero offerta** | **Dati disponibili da** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure per enti pubblici Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | <sup>1</sup> maggio 2014 |
| **Contratto Enterprise** | Sviluppo/test Enterprise                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | <sup>1</sup> maggio 2014 |
| **Contratto Enterprise** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | <sup>1</sup> maggio 2014 |
| **Contratto del cliente Microsoft** | [Piano di Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/D | <sup>3</sup> marzo 2019 |
| **Contratto del cliente Microsoft** | [Piano di Microsoft Azure per Sviluppo/test](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/D | <sup>3</sup> marzo 2019 |
| **Contratto del cliente Microsoft supportato dai partner** | Piano di Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01 e CSPDEVTEST_2018-05-01<br><br>L'ID quota viene riutilizzato per il Contratto del cliente Microsoft e per le sottoscrizioni CSP legacy. Attualmente sono supportate solo le sottoscrizioni del Contratto del cliente Microsoft. | N/D | Ottobre 2019 |
| **Microsoft Developer Network (MSDN)** | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 ottobre 2018<sup>2</sup> |
| **Pagamento in base al consumo** | [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 ottobre 2018<sup>2</sup> |
| **Pagamento in base al consumo** | [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 ottobre 2018<sup>2</sup> |
| **Pagamento in base al consumo** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2 ottobre 2018<sup>2</sup> |
| **Pagamento in base al consumo** | [Versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 ottobre 2018<sup>2</sup> |
| **Pagamento in base al consumo** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 ottobre 2018<sup>2</sup> |
| **Pagamento in base al consumo** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | 2 ottobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 ottobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 ottobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 ottobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 ottobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 ottobre 2018<sup>2</sup> |

_<sup>**1**</sup> Per i dati prima del maggio 2014, visitare [Azure Enterprise Portal](https://ea.azure.com)._

_<sup>**2**</sup> Per i dati prima del 2 ottobre 2018, visitare il [Centro account di Azure](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> I contratti con i clienti Microsoft sono stati avviati nel marzo 2019 e non dispongono di dati cronologici precedenti._

_<sup>**4**</sup> I dati cronologici per le sottoscrizioni basate su crediti e con pagamento anticipato potrebbero non corrispondere alla fattura. Vedere la sezione [I dati cronologici potrebbero non corrispondere alla fattura](#historical-data-might-not-match-invoice) riportata di seguito._

Le offerte seguenti non sono ancora supportate:

| Category  | **Nome dell'offerta** | **ID quota** | **Numero offerta** |
| --- | --- | --- | --- |
| **Azure Germania** | [Azure Germania con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government con pagamento in base al consumo | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **Cloud Solution Provider (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Cloud Solution Provider (CSP)** | CSP Azure per enti pubblici                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Cloud Solution Provider (CSP)** | Azure Germania in CSP per Microsoft Cloud Germania   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pagamento in base al consumo**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pagamento in base al consumo** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Pagamento in base al consumo**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Piani di supporto** | Supporto tecnico standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Piani di supporto** | Supporto Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Piani di supporto** | Supporto tecnico Developer                   | Default_2014-09-01 | MS-AZR-0043P |
| **Piani di supporto** | Piano di supporto per la Germania                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Piani di supporto** | Supporto tecnico Standard di Azure per enti pubblici   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Piani di supporto** | Supporto Pro-Direct di Azure per enti pubblici | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Piani di supporto** | Supporto tecnico Developer di Azure per enti pubblici  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Determinare il tipo di offerta
Se i dati relativi a una sottoscrizione non vengono visualizzati e si vuole determinare se la sottoscrizione rientra tra le offerte supportate, è possibile convalidare che la sottoscrizione sia supportata. Per convalidare il supporto di una sottoscrizione di Azure, accedere al [portale di Azure](https://portal.azure.com). Selezionare quindi **Tutti i servizi** nel riquadro del menu a sinistra. Nell'elenco dei servizi selezionare **Sottoscrizioni**. Nel menu dell'elenco delle sottoscrizioni selezionare la sottoscrizione da verificare. La sottoscrizione verrà visualizzata nella scheda Panoramica e sarà possibile vedere le informazioni in **Offerta** e **ID offerta**. La figura seguente mostra un esempio.

![Esempio della scheda Panoramica della sottoscrizione che mostra l'offerta e l'ID offerta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Costi inclusi in Gestione costi

Le tabelle seguenti illustrano i dati inclusi o meno in Gestione costi. Tutti i costi vengono stimati fino a quando non viene generata una fattura. I costi indicati non includono crediti gratuiti e prepagati.

| **Incluso** | **Non incluso** |
| --- | --- |
| Utilizzo dei servizi di Azure<sup>5</sup>        | Addebiti per il supporto - Per altre informazioni, vedere [Spiegazioni delle condizioni di fatturazione](../understand/understand-invoice.md). |
| Utilizzo delle offerte del Marketplace<sup>6</sup> | Imposte - Per altre informazioni, vedere [Spiegazioni delle condizioni di fatturazione](../understand/understand-invoice.md). |
| Acquisti nel Marketplace<sup>6</sup>      | Crediti - Per altre informazioni, vedere [Spiegazioni delle condizioni di fatturazione](../understand/understand-invoice.md). |
| Acquisti di prenotazioni<sup>7</sup>      |  |
| Ammortamento degli acquisti di prenotazioni<sup>7</sup>      |  |

_<sup>**5**</sup> L'utilizzo del servizio di Azure è basato sulla prenotazione e sui prezzi negoziati._

_<sup>**6**</sup> Gli acquisti del Marketplace non sono attualmente disponibili per le offerte MSDN e Visual Studio._

_<sup>**7**</sup> Gli acquisti delle prenotazioni sono disponibili attualmente solo per gli account Contratto Enterprise (EA) e Contratto del cliente Microsoft._

## <a name="how-tags-are-used-in-cost-and-usage-data"></a>Uso dei tag nei dati relativi a costi e utilizzo

Gestione costi di Azure riceve i tag come parte di ogni record di utilizzo inviato dai singoli servizi. A questi tag si applicano i vincoli seguenti:

- I tag devono essere applicati direttamente alle risorse e non vengono ereditati in modo implicito dal gruppo di risorse padre.
- I tag di risorsa sono supportati solo per le risorse distribuite in gruppi di risorse.
- Alcune risorse distribuite potrebbero non supportare i tag o non includere i tag nei dati di utilizzo. Vedere [Supporto dei tag per le risorse di Azure](../../azure-resource-manager/tag-support.md).
- I tag di risorsa vengono inclusi solo nei dati di utilizzo quando il tag viene applicato. I tag non vengono applicati ai dati cronologici.
- I tag di risorsa sono disponibili in Gestione costi solo dopo l'aggiornamento dei dati. Vedere [Variazione della frequenza di aggiornamento dei dati sull'utilizzo](#usage-data-update-frequency-varies).
- I tag di risorsa sono disponibili in Gestione costi solo quando la risorsa è attiva o in esecuzione e produce record di utilizzo, quindi non quando una macchina virtuale viene deallocata, ad esempio.
- Per la gestione dei tag è necessario l'accesso come collaboratore a ogni risorsa.
- Per la gestione dei criteri di tag è necessario l'accesso come proprietario o collaboratore ai criteri a un gruppo di gestione, una sottoscrizione o un gruppo di risorse.
    
Se non si trova un tag specifico in gestione costi, considerare quanto segue:

- Il tag è stato applicato direttamente alla risorsa?
- Il tag è stato applicato più di 24 ore fa? Vedere [Variazione della frequenza di aggiornamento dei dati sull'utilizzo](#usage-data-update-frequency-varies)
- Il tipo di risorsa supporta i tag? I tipi di risorse seguenti non supportano i tag nei dati di utilizzo a partire dal 1° dicembre 2019. Vedere [Supporto dei tag per le risorse di Azure](../../azure-resource-manager/tag-support.md) per l'elenco completo dei tag supportati.
    - Directory di Azure Active Directory B2C
    - Firewall di Azure
    - Azure NetApp Files
    - Data Factory
    - Databricks
    - Servizi di bilanciamento del carico
    - Network Watcher
    - Hub di notifica
    - Bus di servizio
    - Time Series Insights
    - gateway VPN
    
Ecco alcuni suggerimenti per l'uso dei tag:

- Pianificare in anticipo e definire una strategia di assegnazione di tag che consenta di suddividere i costi per organizzazione, applicazione, ambiente e così via.
- Usare Criteri di Azure per copiare i tag dei gruppi di risorse nelle singole risorse e applicare la strategia di assegnazione di tag.
- Usare l'API Tag insieme a Query o UsageDetails per ottenere tutti i costi in base ai tag correnti.


## <a name="free-trial-to-pay-as-you-go-upgrade"></a>Aggiornamento della versione di valutazione gratuita al pagamento in base al consumo

Per informazioni sulla disponibilità di servizi di livello gratuito dopo l'aggiornamento ai prezzi con pagamento in base al consumo da una versione di valutazione gratuita, vedere le [domande frequenti sull'account Azure gratuito](https://azure.microsoft.com/free/free-account-faq/).

## <a name="rated-usage-data-refresh-schedule"></a>Pianificazione dell'aggiornamento dei dati sull'utilizzo valutato

I dati sui costi e sull'utilizzo sono disponibili in Gestione dei costi e fatturazione nel portale di Azure e nelle [API di supporto](../index.yml). Durante l'analisi dei costi, tenere presente quanto segue:

- Gli addebiti stimati per il periodo di fatturazione corrente vengono aggiornati sei volte al giorno.
- Gli addebiti stimati per il periodo di fatturazione corrente possono variare a causa di un aumento dell'utilizzo.
- Ogni aggiornamento è cumulativo e comprende tutte le voci e le informazioni relative all'aggiornamento precedente.
- Azure finalizza o _chiude_ il periodo di fatturazione corrente fino a 72 ore (tre giorni di calendario) dopo il termine del periodo di fatturazione.

Gli esempi seguenti illustrano come possono terminare i periodi di fatturazione.

Sottoscrizioni Enterprise Agreement (EA) - Se il mese di fatturazione termina il 31 marzo, gli addebiti stimati vengono aggiornati fino a 72 ore dopo. In questo esempio, entro la mezzanotte (UTC) del 4 aprile.

Sottoscrizioni con pagamento in base al consumo - Se il mese di fatturazione termina il 15 maggio, gli addebiti stimati potrebbero essere aggiornati fino a 72 ore dopo. In questo esempio, entro la mezzanotte (UTC) del 19 maggio.

### <a name="rerated-data"></a>Dati rivalutati

Quando si usano le [API di gestione dei costi](../index.yml), PowerBI o il portale di Azure per recuperare i dati, prevedere una rivalutazione, e quindi una modifica, degli addebiti del periodo di fatturazione corrente, fino alla chiusura della fattura.

## <a name="cost-rounding"></a>Arrotondamento dei costi

I costi indicati in Gestione costi sono arrotondati. I costi restituiti dall'API di query non sono arrotondati. Ad esempio:

- Analisi dei costi nel portale di Azure: gli addebiti vengono arrotondati con le regole standard, ossia se i valori sono maggiori di 0,5 vengono arrotondati per eccesso, altrimenti per difetto. L'arrotondamento viene applicato solo ai valori visualizzati. Non si verifica durante l'elaborazione e l'aggregazione dei dati. Ad esempio, con l'analisi i costi vengono aggregati come segue:
  -    Addebito 1: $ 0,004
  - Addebito 2: $ 0,004
  -    Aggregazione degli addebiti: 0,004 + 0,004 = 0,008. L'addebito visualizzato è $ 0,01.
- API di query: gli addebiti vengono visualizzati con otto cifre decimali senza arrotondamento.


## <a name="usage-data-update-frequency-varies"></a>Variazione della frequenza di aggiornamento dei dati sull'utilizzo

La disponibilità dei dati sull'utilizzo in Gestione costi dipende da due fattori:

- La frequenza con cui i servizi di Azure (ad esempio Archiviazione, Calcolo, Rete CDN e SQL) generano i dati sull'utilizzo.
- Il tempo necessario per l'elaborazione dei dati sull'utilizzo tramite il motore di valutazione e le pipeline di gestione dei costi.

Alcuni servizi generano i dati sull'utilizzo più spesso rispetto ad altri. Pertanto, i dati per alcuni servizi potrebbero essere visualizzati in Gestione costi più rapidamente rispetto ad altri servizi che generano i dati con minore frequenza. In genere, la visualizzazione in Gestione costi dei dati sull'utilizzo richiede da 8 a 24 ore. Tenere presente che i dati per un mese aperto vengono aggiornati in caso di aumento dell'utilizzo, perché gli aggiornamenti sono cumulativi.

## <a name="historical-data-might-not-match-invoice"></a>I dati cronologici potrebbero non corrispondere alla fattura

I dati cronologici per le offerte basate su crediti e con pagamento anticipato potrebbero non corrispondere alla fattura. Alcune offerte di Azure con pagamento in base al consumo, MSDN e Visual Studio possono avere crediti di Azure e pagamenti anticipati applicati alla fattura. Tuttavia, i dati cronologici visualizzati in Gestione costi si basano solo sugli addebiti per l'utilizzo stimato. I dati cronologici di Gestione costi non includono pagamenti e crediti. I dati cronologici visualizzati per le offerte seguenti potrebbero quindi non corrispondere esattamente alla fattura.

- Azure for Students (MS-AZR-0170P)
- Azure in Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Versione di valutazione gratuita (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Vedere anche

- Se non è stata ancora completata la prima guida introduttiva di Gestione costi, esaminarla in [Avviare l’analisi dei costi](../../cost-management/quick-acm-cost-analysis.md).
