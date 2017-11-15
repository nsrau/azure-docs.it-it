---
title: Presentazione di Azure Advisor | Microsoft Docs
description: Usare Azure Advisor per ottimizzare le distribuzioni di Azure.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 906450f75557820bb27762707c3328b08b23cccb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2017
---
# <a name="introduction-to-azure-advisor"></a>Presentazione di Azure Advisor

Informazioni sulle funzionalità chiave di Azure Advisor e risposte alle domande più frequenti.

## <a name="what-is-advisor"></a>Cos'è Advisor?
Advisor è un consulente cloud personalizzato che illustra come seguire le procedure consigliate per ottimizzare le distribuzioni di Azure. Analizza i dati di telemetria dell'uso e della configurazione delle risorse e consiglia soluzioni che consentono di migliorare l'efficienza dei costi, le prestazioni, la disponibilità elevata e la sicurezza delle risorse di Azure.

Con Advisor, è possibile:
* Ottenere consigli personalizzati, attuabili e proattivi sulle procedure consigliate. 
* Migliorare le prestazioni, la sicurezza e la disponibilità elevata delle risorse, cercando le opportunità per ridurre la spesa complessiva di Azure.
* Ricevere consigli con azioni proposte incorporate.

È possibile accedere ad Advisor attraverso il [portale di Azure](https://aka.ms/azureadvisordashboard). Accedere al [portale](https://portal.azure.com), individuare **Advisor** nel menu di spostamento oppure cercarlo nel menu **Altri servizi**.

Nel dashboard di Advisor vengono visualizzati consigli personalizzati per tutte le sottoscrizioni disponibili.  È possibile applicare filtri per visualizzare i consigli in base a sottoscrizioni e tipi di risorse specifici.  I consigli sono suddivisi in quattro categorie. 

* **Disponibilità elevata**: per garantire e migliorare la continuità delle applicazioni aziendali critiche. Per altre informazioni, vedere [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Consigli di Advisor sulla disponibilità elevata).
* **Sicurezza**: per rilevare le minacce e le vulnerabilità che potrebbero portare a potenziali violazioni della sicurezza. Per altre informazioni, vedere [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza).
* **Prestazioni**: per aumentare la velocità delle applicazioni. Per altre informazioni, vedere [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni).
* **Costo**: per ottimizzare e ridurre la spesa complessiva di Azure. Per altre informazioni, vedere [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Azure sui costi).

  ![Tipi di consigli di Advisor](./media/advisor-overview/advisor-dashboard.png)

> [!NOTE]
> Per usare Azure Advisor con una sottoscrizione, è necessario che un *proprietario* della sottoscrizione avvii il dashboard di Advisor.  Questa azione registra la sottoscrizione con Advisor.  Da questo momento in poi qualsiasi *proprietario*, *collaboratore* o *lettore* della sottoscrizione può accedere ai consigli di Advisor per la sottoscrizione. 

È possibile fare clic su una categoria per visualizzare l'elenco di consigli disponibili in tale categoria e selezionare un consiglio per ottenere altre informazioni.  È inoltre possibile trovare informazioni sulle azioni che è possibile eseguire per sfruttare un'opportunità o risolvere un problema.

![Categoria di consigli di Advisor](./media/advisor-overview/advisor-ha-category-example.png) 

Fare clic sull'azione consigliata per implementare il consiglio.  Verrà aperta un'interfaccia semplice che consentirà di implementare i consigli o di fare riferimento alla documentazione di supporto all'implementazione.  Dopo avere implementato un consiglio, Advisor può richiedere fino a un giorno per riconoscere la modifica.

Se non si prevede di eseguire un'azione immediata in base a un consiglio, è possibile posporlo per un periodo di tempo specifico oppure ignorarlo.  Se non si vuole ricevere consigli per una specifica sottoscrizione o uno specifico gruppo di risorse, è possibile configurare Advisor in modo che generi i consigli solo per le sottoscrizioni e i gruppi di risorse specificati.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-do-i-access-advisor"></a>Come si accede ad Advisor?
È possibile accedere ad Advisor attraverso il [portale di Azure](https://aka.ms/azureadvisordashboard). Accedere al [portale](https://portal.azure.com), individuare **Advisor** nel menu di spostamento oppure cercarlo nel menu **Altri servizi**.

È possibile visualizzare i consigli di Advisor anche tramite l'interfaccia delle risorse delle macchine virtuali. Scegliere una macchina virtuale, quindi scorrere fino ai consigli di Advisor nel menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Quali autorizzazioni sono necessarie per accedere ad Advisor?

Per accedere ai consigli di Advisor, è prima di tutto necessario registrare la sottoscrizione con Advisor. Una sottoscrizione viene registrata quando il *proprietario* della sottoscrizione avvia il dashboard di Advisor. Si tratta di un'operazione una tantum. Dopo avere registrato la sottoscrizione, è possibile accedere ai consigli di Advisor come *Proprietario*, *Collaboratore* o *Lettore* di una sottoscrizione.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Per quali risorse fornisce consigli Advisor?

Advisor fornisce consigli per macchine virtuali, set di disponibilità, gateway applicazione, Servizi app, SQL Server, database SQL e Cache Redis.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>È possibile posporre o ignorare un consiglio?

Per posporre o ignorare un consiglio, fare clic sul collegamento **Posponi**. È possibile specificare fino a quando posporre oppure selezionare **Mai** per ignorare il consiglio.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:

* [Get started with Advisor](advisor-get-started.md) (Introduzione ad Advisor)
* [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
* [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni)
* [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sui costi)
