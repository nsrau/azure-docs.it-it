---
title: Come eseguire l'aggiornamento al monitoraggio dinamico della conformità alle normative nel dashboard di conformità normativa del Centro sicurezza di Azure | Microsoft Docs
description: Aggiornamento dei pacchetti di conformità normativa (anteprima)
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
ms.openlocfilehash: f76b5443d6c1e3fd2cebf87cba39ba0a6bbdeaee
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521787"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard-preview"></a>Aggiornare i pacchetti di conformità dinamici nel dashboard conformità normativa (anteprima)

Il Centro sicurezza di Azure confronta continuamente la configurazione delle risorse con i requisiti in standard del settore, normative e benchmark. Il **Dashboard conformità normativa** fornisce informazioni approfondite sul comportamento di conformità in base al modo in cui si soddisfano i requisiti e i controlli di conformità specifici.

Uno standard per il quale è possibile tenere traccia del comportamento di conformità è [Azure cis 1.1.0](https://www.cisecurity.org/benchmark/azure/) (più formalmente, "CIS Microsoft Azure Foundations Benchmark versione 1.1.0"). 

La rappresentazione di Azure CIS che inizialmente viene visualizzata nel dashboard di conformità si basa su un set statico di regole incluso nel centro sicurezza.

Con la funzionalità **pacchetti di conformità dinamica (anteprima)** , il Centro sicurezza migliora automaticamente la copertura degli standard del settore nel tempo. I pacchetti di conformità sono essenzialmente iniziative definite in criteri di Azure. Possono essere assegnati all'ambito selezionato (sottoscrizione, gruppo di gestione e così via). Per visualizzare i dati di conformità mappati come valutazioni nel dashboard, aggiungere un pacchetto di conformità al gruppo di gestione o alla sottoscrizione dall'interno dei criteri di sicurezza. L'aggiunta di un pacchetto di conformità assegna efficacemente l'iniziativa di conformità normativa all'ambito selezionato. In questo modo, è possibile tenere traccia delle iniziative normative appena pubblicate come standard di conformità nel dashboard. Quando Microsoft rilascia nuovo contenuto per l'iniziativa (nuovi criteri con mapping a più controlli nello standard), il contenuto aggiuntivo viene visualizzato automaticamente nel dashboard.

Il pacchetto di conformità dinamica per il benchmark di Azure CIS, **Azure cis 1.1.0 (nuovo)** , migliora la versione *statica* originale in base a quanto segue:

* Inclusione di altri criteri
* Aggiornamento automatico con nuovo code coverage non appena aggiunto 

Eseguire l'aggiornamento al nuovo pacchetto dinamico come descritto di seguito.

## <a name="adding-a-dynamic-compliance-package"></a>Aggiunta di un pacchetto di conformità dinamica

I passaggi seguenti illustrano come aggiungere il pacchetto dinamico per il monitoraggio della conformità con il benchmark Azure CIS v 1.1.0.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Eseguire l'aggiornamento al pacchetto di conformità dinamica di Azure CIS 1.1.0 (nuovo) 

1. Aprire la pagina dei **criteri di sicurezza** . Questa pagina mostra il numero di gruppi di gestione, sottoscrizioni, aree di lavoro e struttura del gruppo di gestione.

1. Selezionare la sottoscrizione o il gruppo di gestione per cui si desidera gestire il comportamento di conformità alle normative. Si consiglia di selezionare l'ambito più elevato per cui è applicabile lo standard in modo che i dati di conformità vengano aggregati e monitorati per tutte le risorse nidificate. 

1. Nella sezione standard Industry & Regulatory Standards (anteprima) si noterà che Azure CIS 1.1.0 può essere aggiornato per il nuovo contenuto. Fare clic su **Aggiorna ora**. 

1. Facoltativamente, fare clic su **Aggiungi altri standard** per aprire la pagina **Aggiungi standard di conformità normativi** . In questa pagina è possibile eseguire ricerche manualmente per i pacchetti di **Azure (nuovi)** e dinamici di Azure per altri standard di conformità come **NIST SP 800-53 R4**, **Swift CSP CSCF-V2020**, **uko e UK NHS**e **Canada PBMM**.
    
    ![Aggiunta di pacchetti normativi al dashboard di conformità alle normative del Centro sicurezza di Azure](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. Dall'intestazione laterale del Centro sicurezza selezionare **conformità normativa** per aprire il dashboard conformità normativa. 
    * Azure CIS 1.1.0 (nuovo) viene ora visualizzato nell'elenco degli standard normativi del settore &. 
    * La visualizzazione *statica* originale della conformità di Azure cis 1.1.0 rimarrà al suo insieme. Potrebbe essere rimossa automaticamente in futuro.

    > [!NOTE]
    > La visualizzazione di uno standard appena aggiunto nel dashboard di conformità potrebbe richiedere alcune ore.


    [![dashboard di conformità normativa che mostra i vecchi e i nuovi Azure CIS](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso quanto segue:

* Come **aggiornare gli standard** mostrati nel dashboard conformità normativa ai nuovi pacchetti *dinamici*
* Come **aggiungere pacchetti di conformità** per monitorare la conformità con gli standard aggiuntivi. 

Per altri materiali correlati, vedere gli articoli seguenti: 

- [Dashboard conformità normativa del Centro sicurezza](security-center-compliance-dashboard.md)
- [Uso dei criteri di sicurezza](tutorial-security-policy.md)
- [Gestione delle raccomandazioni di sicurezza nel centro sicurezza di Azure](security-center-recommendations.md) : informazioni su come usare le raccomandazioni nel centro sicurezza di Azure per proteggere le risorse di Azure.
- Domande frequenti sul [Centro sicurezza di Azure](security-center-faq.md) : risposte alle domande frequenti sull'uso del Centro sicurezza.