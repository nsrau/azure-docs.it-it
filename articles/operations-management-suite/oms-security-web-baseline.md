---
title: Baseline Web della soluzione Sicurezza e controllo di Operations Management Suite | Microsoft Docs
description: "Questo documento illustra come usare la soluzione Sicurezza e controllo di OMS per eseguire una valutazione baseline Web di tutti i server Web monitorati per finalità di conformità e sicurezza."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ROBOTS: NOINDEX
redirect_url: https://www.microsoft.com/cloud-platform/security-and-compliance
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0d4707dc0c0ffbf40d0d10a6d12b9709a9655258
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Valutazione baseline Web nella soluzione Sicurezza e controllo di Operations Management Suite
Questo documento illustra come usare le funzionalità della valutazione baseline Web nella [soluzione Sicurezza e controllo di Operations Management Suite (OMS)](operations-management-suite-overview.md) per accedere allo stato di protezione delle risorse monitorate.

## <a name="what-is-web-baseline-assessment"></a>Informazioni sulla valutazione baseline Web
OMS Security offre attualmente la possibilità di eseguire una valutazione baseline della sicurezza dei sistemi operativi. Analizza le impostazioni del sistema operativo dei server ogni 24 ore e permette di visualizzare le impostazioni potenzialmente vulnerabili. Per altre informazioni sull'argomento, vedere [Valutazione baseline nella soluzione Sicurezza e controllo di Operations Management Suite](oms-security-baseline.md).

L'obiettivo della valutazione baseline Web è trovare le impostazioni del server Web potenzialmente vulnerabili. Le tre origini principali per le configurazioni baseline Web sono le configurazioni di .NET, ASP.NET e IIS.  Come avviene per la valutazione baseline del sistema operativo, OMS Security analizza i server Web ogni 24 ore e permette di visualizzarne lo stato di sicurezza.  In Internet Information Services (IIS) le configurazioni sono personalizzabili e questo consente di eseguire l'override di diversi livelli di applicazioni e siti. L'analisi controlla le impostazioni per ogni livello di applicazione e sito, oltre che per il livello radice predefinito. Questo permette di identificare il percorso delle impostazioni potenzialmente vulnerabili e di apportare rapidamente le correzioni necessarie.


## <a name="web-security-baseline-assessment"></a>Valutazione baseline della sicurezza Web
Per questa versione di anteprima questa funzionalità sarà accessibile tramite l'opzione di ricerca di OMS. Per eseguire la query appropriata, seguire questa procedura:

1. Nel dashboard principale **Microsoft Operations Management Suite** fare clic sul riquadro **Security and Audit** (Sicurezza e controllo).
2. Nel dashboard **Sicurezza e controllo** fare clic sul pulsante **Ricerca log**.
3. La prima query che è possibile usare è **Web Baseline Assessment Summary** (Riepilogo valutazione baseline Web). Nel campo **Iniziare la ricerca qui** digitare questa query: Type*=SecurityBaselineSummary BaselineType=web*. La figura seguente mostra un esempio di output:

![Riepilogo valutazione baseline Web](./media/oms-security-web-baseline/oms-security-web-baseline-fig1-new.png)

> [!NOTE]
> In questa query ogni record indica il riepilogo della valutazione in un singolo server.

Dopo aver aperto **Ricerca log**, è possibile digitare varie query per ottenere altre informazioni sulla valutazione baseline Web. Oltre alla precedente, in questa versione di anteprima è possibile usare anche le query seguenti.

**Web Baseline Rule Assessment** (Valutazione regola baseline Web): ogni record rappresenta una singola valutazione della regola baseline Web in un server singolo. Include tutti i dati per la regola, il percorso, il risultato previsto e il risultato effettivo.

**Query**: Type*=SecurityBaseline BaselineType=web*

![Valutazione regola baseline Web](./media/oms-security-web-baseline/oms-security-web-baseline-fig2.png)

**Show all results for a specific server** (Mostra tutti i risultati per un server specifico): questa query mostra come visualizzare i risultati di un server specifico.

**Query**: *Type=SecurityBaseline BaselineType=web Computer=BaselineTestVM1*

![Tutti i risultati](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

È anche possibile usare record e query per creare dashboard, report o avvisi personalizzati. La figura seguente mostra un controllo interfaccia utente di esempio che è possibile aggiungere al dashboard. Per informazioni su come visualizzare i dati mediante Visualizza finestra di progettazione in OMS, vedere [questo post di blog](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/). La figura seguente mostra un esempio dell'aspetto del riquadro dopo la personalizzazione.

![Interfaccia utente di esempio](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

> [!NOTE]
> Per conoscere le impostazioni selezionate per la valutazione baseline, è possibile scaricare [questo foglio di calcolo di Excel](https://gallery.technet.microsoft.com/OMS-Web-Baseline-1e811690) che contiene le impostazioni.

## <a name="see-also"></a>Vedere anche
In questo documento sono state fornite informazioni sulla valutazione baseline Web di Sicurezza e controllo di OMS. Per altre informazioni sulle funzionalità di sicurezza di OMS, vedere gli articoli seguenti:

* [Panoramica di Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Monitoraggio e gestione degli avvisi di sicurezza nella soluzione Operations Management Suite per la sicurezza e il controllo](oms-security-responding-alerts.md)
* [Monitoraggio delle risorse nella soluzione Operations Management Suite per la sicurezza e il controllo](oms-security-monitoring-resources.md)


