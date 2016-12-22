---
title: Presentazione di Azure Advisor | Microsoft Docs
description: Usare Azure Advisor per ottimizzare le distribuzioni di Azure.
services: advisor
documentationcenter: NA
author: kumudd
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
translationtype: Human Translation
ms.sourcegitcommit: f67ca5996fdde0f0939ac46c2f6280f9d89af3d0
ms.openlocfilehash: a39a5555ce3af59b509a0522725addbb382ff7af

---

# <a name="introduction-to-azure-advisor"></a>Presentazione di Azure Advisor

Informazioni su Azure Advisor, sulle sue funzionalità chiave e sulle domande più frequenti.

## <a name="what-is-azure-advisor"></a>Cos'è Azure Advisor?
Azure Advisor è un consulente cloud personalizzato che illustra come seguire le procedure consigliate per ottimizzare le distribuzioni di Azure. Analizza i dati di telemetria dell'uso e della configurazione delle risorse e consiglia soluzioni che consentono di migliorare l'efficienza dei costi, le prestazioni, la disponibilità elevata e la sicurezza delle risorse di Azure.

Con Azure Advisor, è possibile:
-   ottenere consigli personalizzati, attuabili e proattivi sulle procedure consigliate 
-   migliorare le prestazioni, la sicurezza e la disponibilità elevata delle risorse, cercando al tempo stesso le opportunità ottimali per ridurre la spesa complessiva di Azure
-   ricevere consigli con azioni incorporate

È possibile accedere ad Advisor attraverso il [portale di Azure](https://aka.ms/azureadvisordashboard). Accedere al [portale](https://portal.azure.com), selezionare **Sfoglia** e scorrere fino ad **Azure Advisor**. Nel dashboard di Advisor vengono visualizzati consigli personalizzati per la sottoscrizione selezionata. I consigli sono suddivisi in quattro categorie. 

-   **Disponibilità elevata**: per garantire e migliorare la continuità delle applicazioni aziendali critiche. Per altre informazioni, vedere [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Consigli di Advisor sulla disponibilità elevata).

-   **Sicurezza**: per rilevare le minacce e le vulnerabilità che potrebbero portare a potenziali violazioni della sicurezza. Per altre informazioni, vedere [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza).

-   **Prestazioni**: per aumentare la velocità delle applicazioni. Per altre informazioni, vedere [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni).

-   **Costo**: per ottimizzare e ridurre la spesa complessiva di Azure. Per altre informazioni, vedere [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Azure sui costi).

  ![Tipi di consigli di Advisor](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> Azure Advisor genera consigli per le sottoscrizioni in cui all'utente è assegnato il ruolo di **proprietario**, **collaboratore** o **lettore**.

È possibile fare clic su un consiglio per scoprire altre informazioni. È inoltre possibile trovare informazioni sulle azioni che è possibile eseguire per sfruttare un'opportunità o risolvere un problema. Advisor offre consigli con azioni incorporate o collegamenti alla documentazione. Facendo clic su un'azione incorporata, si passa a una procedura guidata per implementarla. Facendo clic su un collegamento alla documentazione, si viene condotti alla documentazione che descrive come è possibile implementare manualmente l'operazione. 

Advisor aggiorna i consigli ogni ora. Se non si prevede di eseguire un'operazione immediata sulla base di un consiglio, è possibile posporla per un periodo di tempo oppure chiuderla. 

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-do-i-access-advisor"></a>Come si accede ad Advisor?
È possibile accedere ad Advisor attraverso il portale di Azure. Accedere al portale, selezionare **Sfoglia** e scorrere fino ad **Azure Advisor**. È possibile visualizzare i consigli di Advisor anche attraverso il pannello delle risorse delle macchine virtuali. Scegliere una macchina virtuale, quindi scorrere fino ai consigli di Advisor nel menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Quali autorizzazioni sono necessarie per accedere ad Advisor?

Per visualizzare i consigli di Advisor, è necessario disporre dell'accesso ad almeno una sottoscrizione. L'utente deve disporre del ruolo di **proprietario**, **collaboratore**, o **lettore** per la sottoscrizione. I collaboratori e i proprietari delle sottoscrizioni possono calcolare o generare consigli. Tuttavia, con un ruolo di **lettore**, è possibile solo visualizzare consigli di Advisor. 

### <a name="how-often-are-advisor-recommendations-updated"></a>Con che frequenza vengono aggiornati i consigli di Advisor?

I consigli di Advisor vengono aggiornati ogni ora.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Per quali risorse fornisce consigli Advisor?

Advisor fornisce consigli per macchine virtuali, set di disponibilità, Servizi app, SQL Server, database SQL e Cache Redis.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>È possibile posporre o ignorare un consiglio?

Per posporre o ignorare un consiglio, fare clic sul pulsante o sul collegamento **Posponi**. È possibile specificare fino a quando posporre oppure selezionare **Mai** per ignorare il consiglio.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere le risorse seguenti:

-  [Get started with Advisor](advisor-get-started.md) (Introduzione ad Advisor)
-  [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
-  [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
-  [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni)
-  [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sui costi)



<!--HONumber=Nov16_HO4-->


