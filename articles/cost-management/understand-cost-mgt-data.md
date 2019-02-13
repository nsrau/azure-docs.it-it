---
title: Informazioni sui dati di Gestione costi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni sui dati inclusi in Gestione costi di Azure e sulla frequenza di elaborazione, raccolta, visualizzazione e chiusura dei dati.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 0531c106228190fdc40f494e8eee70ec550f6404
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820258"
---
# <a name="understand-cost-management-data"></a>Informazioni sui dati di Gestione costi

Questo articolo fornisce informazioni sui dati inclusi in Gestione costi di Azure. Viene inoltre illustrata la frequenza di elaborazione, raccolta, visualizzazione e chiusura dei dati. L'uso di Azure viene addebitato su base mensile. Tuttavia, il tipo di sottoscrizione di Azure determina quando termina il mese di fatturazione. La frequenza con cui Gestione costi riceve i dati sull'utilizzo varia a seconda di diversi fattori. Tali fattori includono il tempo necessario per l'elaborazione dei dati e la frequenza con cui i servizi di Azure generano i dati sull'utilizzo per il sistema di fatturazione.

## <a name="supported-microsoft-offers"></a>Offerte Microsoft supportate

Le informazioni seguenti mostrano le [offerte di Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) attualmente supportate in Gestione costi di Azure.  Per offerta di Azure si intende il tipo di sottoscrizione di Azure di cui si dispone.

| Categoria  | **Nome dell'offerta** | **Numero offerta** |
| --- | --- | --- |
| **Contratto Enterprise** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade/) | MS-AZR-0017P |
| **Contratto Enterprise**| Sviluppo/test Enterprise | MS-AZR-0148P |
| **Microsoft Developer Network (MSDN)** | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/) | MS-AZR-0062P |
| **Visual Studio** | [Visual Studio Enterprise - MPN](https://azure.microsoft.com/offers/ms-azr-0029p/) | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P |
| **Diretto/pagamento in base al consumo** | [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) | MS-AZR-0003P |
| **Diretto/pagamento in base al consumo** | Consumo interno di Microsoft Azure | MS-AZR-0015P |
| **Diretto/pagamento in base al consumo** | [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/) | MS-AZR-0023P |
| **Diretto/pagamento in base al consumo** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/) | MS-AZR-0025P |
| **Diretto/pagamento in base al consumo** | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | MS-AZR-0036P |
| **Diretto/pagamento in base al consumo** | [Versione di prova gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) | MS-AZR-0044P |
| **Diretto/pagamento in base al consumo** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/) | MS-AZR-0111P |
| **Diretto/pagamento in base al consumo** | [Azure for Students](https://azure.microsoft.com/en-us/offers/ms-azr-0170p/) | MS-AZR-0170P |
| **Diretto/pagamento in base al consumo** | Azure Pass | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P |

La tabella seguente mostra le offerte non supportate.

| Categoria  | **Nome dell'offerta** | **Numero offerta** |
| --- | --- | --- |
| **Cloud Solution Provider (CSP)** | Microsoft Azure | MS-AZR-0145P |
| **Cloud Solution Provider (CSP)** | CSP Azure per enti pubblici | MS-AZR-USGOV-0145P |
| **Cloud Solution Provider (CSP)** | Azure Germania in CSP per Microsoft Cloud Germania | MS-AZR-DE-0145P |
| **Diretto/pagamento in base al consumo** | Azure for Students Starter | MS-AZR-0144P |
| **Piani di supporto** | Supporto tecnico standard | MS-AZR-0041P |
| **Piani di supporto** | Supporto Professional Direct | MS-AZR-0042P |
| **Piani di supporto** | Supporto tecnico Developer | MS-AZR-0043P |
| **Piani di supporto** | Piano di supporto per la Germania | MS-AZR-DE-0043P |
| **Piani di supporto** | Supporto tecnico Standard di Azure per enti pubblici | MS-AZR-USGOV-0041P |
| **Piani di supporto** | Supporto Pro-Direct di Azure per enti pubblici | MS-AZR-USGOV-0042P |
| **Piani di supporto** | Supporto tecnico Developer di Azure per enti pubblici | MS-AZR-USGOV-0043P |

Se i dati relativi a una sottoscrizione non vengono visualizzati e si vuole determinare se la sottoscrizione rientra tra le offerte supportate, è possibile convalidare che la sottoscrizione sia supportata. Per convalidare che una sottoscrizione di Azure sia supportata, accedere al [portale di Azure](https://portal.azure.com). Selezionare quindi **Tutti i servizi** nel riquadro del menu a sinistra. Nell'elenco dei servizi selezionare **Sottoscrizioni**. Nel menu dell'elenco delle sottoscrizioni fare clic sulla sottoscrizione da verificare. La sottoscrizione verrà visualizzata nella scheda Panoramica e sarà possibile vedere le informazioni in **Offerta** e **ID offerta**. La figura seguente mostra un esempio.

![Esempio della scheda Panoramica della sottoscrizione che mostra l'offerta e l'ID offerta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Costi inclusi in Gestione costi

Le tabelle seguenti illustrano i dati inclusi o meno in Gestione costi.

**Tipi di account**

| **Incluso** | **Non incluso** |
| --- | --- |
| Contratto Enterprise Agreement (EA) | Cloud Solution Provider (CSP) - Per altre informazioni, vedere [Panoramica del Centro per i partner](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview). |
| Pagamento in base al consumo |   |
| Sviluppo/test |   |
| Gratuito, versione di valutazione e sponsorizzato |   |
| Rete di partner |   |
| Azure in Open | &nbsp;  |

**Dati su costi e utilizzo**

| **Incluso** | **Non incluso** |
| --- | --- |
| Utilizzo del servizio Azure<sup>1</sup> | Acquisti di prenotazioni - Per altre informazioni, vedere [API per l'automazione delle prenotazioni di Azure](../billing/billing-reservation-apis.md). |
| Utilizzo delle offerte del Marketplace | Acquisti del Marketplace - Per altre informazioni, vedere [Addebiti per servizi di terze parti](../billing/billing-understand-your-azure-marketplace-charges.md). |
|   | Addebiti per il supporto - Per altre informazioni, vedere [Spiegazioni delle condizioni di fatturazione](../billing/billing-understand-your-invoice.md). |
|   | Imposte - Per altre informazioni, vedere [Spiegazioni delle condizioni di fatturazione](../billing/billing-understand-your-invoice.md). |
|   | Crediti - Per altre informazioni, vedere [Spiegazioni delle condizioni di fatturazione](../billing/billing-understand-your-invoice.md). |

<sup>1</sup> L'utilizzo del servizio di Azure è basato sulla prenotazione e sui prezzi negoziati.

**Metadata**

| **Incluso** | **Non incluso** |
| --- | --- |
| Tag delle risorse<sup>2</sup> | Tag dei gruppi di risorse |

<sup>2</sup> I tag delle risorse vengono applicati al momento della generazione dei dati sull'utilizzo da ogni servizio e non sono disponibili in modo retroattivo per l'utilizzo cronologico.

## <a name="rated-usage-data-refresh-schedule"></a>Pianificazione dell'aggiornamento dei dati sull'utilizzo valutato

I dati sui costi e sull'utilizzo sono disponibili in Gestione dei costi e fatturazione nel portale di Azure e nelle [API di supporto](https://aka.ms/costmgmt/docs). Durante l'analisi dei costi, tenere presente quanto segue:

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

## <a name="see-also"></a>Vedere anche 

- Se non è stata ancora completata la prima guida introduttiva di Gestione costi, esaminarla in [Avviare l’analisi dei costi](quick-acm-cost-analysis.md).
