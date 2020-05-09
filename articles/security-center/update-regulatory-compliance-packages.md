---
title: Come eseguire l'aggiornamento al monitoraggio dinamico della conformità alle normative nel dashboard di conformità normativa del Centro sicurezza di Azure | Microsoft Docs
description: Aggiornamento dei pacchetti di conformità normativa
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 7a017f9485ec847c0a2c045e4c511b6a68db0cb2
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889928"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Personalizzazione del set di standard nel dashboard conformità normativa

Il Centro sicurezza di Azure confronta continuamente la configurazione delle risorse con i requisiti in standard del settore, normative e benchmark. Il **Dashboard conformità normativa** fornisce informazioni approfondite sul comportamento di conformità in base al modo in cui si soddisfano i requisiti e i controlli di conformità specifici.


## <a name="overview-of-compliance-packages"></a>Panoramica dei pacchetti di conformità

Gli standard di settore, gli standard normativi e i benchmark sono rappresentati nel centro sicurezza come *pacchetti di conformità*.  Ogni pacchetto è un'iniziativa definita in criteri di Azure. Per visualizzare i dati di conformità mappati come valutazioni nel dashboard, aggiungere un pacchetto di conformità al gruppo di gestione o alla sottoscrizione dall'interno della pagina dei **criteri di sicurezza** . Per altre informazioni su criteri e iniziative di Azure, vedere l'articolo relativo all' [uso dei criteri di sicurezza](tutorial-security-policy.md).

Quando è stato caricato uno standard o un benchmark per l'ambito selezionato, lo standard viene visualizzato nel dashboard conformità normativa con tutti i dati di conformità associati mappati come valutazioni. È anche possibile scaricare i report di riepilogo per gli standard caricati.

Microsoft tiene inoltre traccia degli standard normativi e migliora automaticamente la copertura in alcuni pacchetti nel tempo. Quando Microsoft rilascia nuovo contenuto per l'iniziativa (nuovi criteri con mapping a più controlli nello standard), il contenuto aggiuntivo viene visualizzato automaticamente nel dashboard.

> [!TIP]
> Uno standard che migliora nel tempo man mano che Microsoft rilascia nuovo contenuto è **Azure cis 1.1.0 (nuovo)** (più formalmente, la [CSI Microsoft Azure Foundations Benchmark versione 1.1.0](https://www.cisecurity.org/benchmark/azure/)). È necessario aggiungerlo al dashboard insieme a "Azure CIS 1.1.0", la rappresentazione di Azure CIS che è configurata per impostazione predefinita in ogni ambiente del Centro sicurezza. Il pacchetto si basa su un set statico di regole. Il pacchetto più recente include più criteri e verrà aggiornato automaticamente nel tempo. Eseguire l'aggiornamento al nuovo pacchetto dinamico come descritto di seguito.


## <a name="available-packages"></a>Pacchetti disponibili

È possibile aggiungere standard come NIST SP 800-53 R4, SWIFT CSP CSCF-V2020, UK Official e UK NHS, Canada Federal PBMM e Azure CIS 1.1.0 (New)-una rappresentazione più completa di Azure CIS 1.1.0. 

Inoltre, è possibile aggiungere il **benchmark di sicurezza di Azure**, le linee guida specifiche di Azure create da Microsoft per le procedure consigliate per la sicurezza e la conformità basate su Framework di conformità comuni. [Altre informazioni sul benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/introduction).

Gli standard aggiuntivi saranno supportati nel dashboard Man mano che diventano disponibili. 


## <a name="adding-a-regulatory-standard-to-your-dashboard"></a>Aggiunta di uno standard normativo al dashboard

Nei passaggi seguenti viene illustrato come aggiungere un pacchetto per monitorare la conformità con uno degli standard normativi supportati.

> [!NOTE]
> Solo gli utenti che hanno il ruolo di proprietario o collaboratore di criteri hanno le autorizzazioni necessarie per aggiungere gli standard di conformità. 

1. Dall'intestazione laterale del Centro sicurezza selezionare **conformità normativa** per aprire il dashboard conformità normativa. Qui è possibile visualizzare gli standard di conformità attualmente assegnati alle sottoscrizioni attualmente selezionate.   

1. Nella parte superiore della pagina selezionare **Gestisci criteri di conformità**. Verrà visualizzata la pagina Gestione criteri.

1. Selezionare la sottoscrizione o il gruppo di gestione per cui si desidera gestire il comportamento di conformità alle normative. 

    > [!TIP]
    > Si consiglia di selezionare l'ambito più elevato per cui è applicabile lo standard in modo che i dati di conformità vengano aggregati e monitorati per tutte le risorse nidificate. 

1. Per aggiungere gli standard rilevanti per l'organizzazione, fare clic su **Aggiungi altri standard**. 

1. Nella pagina **Aggiungi standard di conformità normativa** è possibile cercare i pacchetti per uno degli standard disponibili. Alcuni degli standard disponibili sono:

    - **Benchmark di sicurezza di Azure**
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF-V2020**
    - **UKO e NHS UK**
    - **Canada PBMM**
    
    ![Aggiunta di pacchetti normativi al dashboard di conformità alle normative del Centro sicurezza di Azure](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Dall'intestazione laterale del Centro sicurezza, selezionare di nuovo **conformità normativa** per tornare al dashboard conformità normativa.
    * Il nuovo standard verrà visualizzato nell'elenco degli standard normativi del settore &. 
    * Se è stato aggiunto **Azure cis 1.1.0 (nuovo)**, la visualizzazione *statica* originale della conformità di Azure cis 1.1.0 rimarrà al suo insieme. Potrebbe essere rimossa automaticamente in futuro.

    > [!NOTE]
    > La visualizzazione di uno standard appena aggiunto nel dashboard di conformità potrebbe richiedere alcune ore.

    [![Dashboard di conformità alle normative che mostra i vecchi e i nuovi Azure CIS](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come **aggiungere i pacchetti di conformità** per monitorare la conformità con gli standard aggiuntivi. 

Per altri materiali correlati, vedere gli articoli seguenti: 

- [Benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [Dashboard conformità normativa del Centro sicurezza](security-center-compliance-dashboard.md)
- [Utilizzo dei criteri di sicurezza](tutorial-security-policy.md)