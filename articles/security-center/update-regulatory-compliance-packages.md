---
title: Come eseguire l'aggiornamento al monitoraggio dinamico della conformità alle normative nel dashboard di conformità alle normative del Centro sicurezza di Azure Documenti Microsoft
description: Aggiornamento dei pacchetti di conformità alle normative
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
ms.openlocfilehash: fa5027ed285456247891c84e559b74a14237f553
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537781"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard"></a>Aggiornamento ai pacchetti di conformità dinamici nel dashboard Conformità regolamentare

Il Centro sicurezza di Azure confronta continuamente la configurazione delle risorse con i requisiti degli standard, delle normative e dei benchmark del settore. Il dashboard di **conformità alle normative** fornisce informazioni dettagliate sulla posizione in materia di conformità in base a come si soddisfano i controlli e i requisiti di conformità specifici.

Uno standard per il quale è possibile tenere traccia della posizione di conformità è [Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (più formalmente, "CIS Microsoft Azure Foundations Benchmark versione 1.1.0"). 

La rappresentazione del CIS di Azure inizialmente visualizzata nel dashboard di conformità si basa su un set statico di regole incluso nel Centro sicurezza.

Con la funzionalità dei pacchetti di **conformità dinamica,** il Centro sicurezza migliora automaticamente la copertura degli standard del settore nel tempo. I pacchetti di conformità sono essenzialmente iniziative definite in Criteri di Azure.Compliance packages are essentially initiatives defined in Azure Policy. Possono essere assegnati all'ambito selezionato (sottoscrizione, gruppo di gestione e così via). Per visualizzare i dati di conformità mappati come valutazioni nel dashboard, aggiungere un pacchetto di conformità al gruppo di gestione o alla sottoscrizione dall'interno dei criteri di sicurezza. L'aggiunta di un pacchetto di conformità assegna in modo efficace l'iniziativa di conformità alle normative all'ambito selezionato. In questo modo, è possibile tenere traccia delle iniziative normative appena pubblicate come standard di conformità nel dashboard. Quando Microsoft rilascia nuovo contenuto per l'iniziativa (nuovi criteri che eseguono il mapping a più controlli nello standard), il contenuto aggiuntivo viene visualizzato automaticamente nel dashboard.

Il pacchetto di conformità dinamica per il benchmark CIS di **Azure, Azure CIS 1.1.0 (nuovo)**, migliora la versione *statica* originale eseguendo le prestazioni:The dynamic compliance package for the Azure CIS benchmark, Azure CIS 1.1.0 (new) , improves on the original static version by:

* Inclusione di più politiche
* Aggiornamento automatico con una nuova copertura man mano che viene aggiunta 

Eseguire l'aggiornamento al nuovo pacchetto dinamico come descritto di seguito.

## <a name="adding-a-dynamic-compliance-package"></a>Aggiunta di un pacchetto di conformità dinamicoAdding a dynamic compliance package

I passaggi seguenti illustrano come aggiungere il pacchetto dinamico per il monitoraggio della conformità con il benchmark CIS di Azure v1.1.0.The following steps explain how to add the dynamic package for monitoring your compliance with the Azure CIS benchmark v1.1.0.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Aggiornamento al pacchetto di conformità dinamica di Azure CIS 1.1.0 (nuovo) 

1. Aprire la pagina **Criteri di sicurezza.** In questa pagina viene visualizzato il numero di gruppi di gestione, sottoscrizioni, aree di lavoro e la struttura dei gruppi di gestione.

1. Selezionare la sottoscrizione o il gruppo di gestione per il quale si vuole gestire la conformità normativa. È consigliabile selezionare l'ambito più alto per il quale lo standard è applicabile in modo che i dati di conformità vengano aggregati e tracciati per tutte le risorse annidate. 

1. Nella sezione Industry & standards normativi si noterà che Azure CIS 1.1.0 può essere aggiornato per il nuovo contenuto. Fare clic su **Aggiorna ora**. 

1. Facoltativamente, fare clic su **Aggiungi altri standard** per aprire la pagina Aggiungi standard di conformità alle **normative.** Qui, è possibile cercare manualmente **Azure CIS 1.1.0 (Nuovo)** e pacchetti dinamici per altri standard di conformità, ad esempio **NIST SP 800-53 R4**, **SWIFT CSP CSCF-v2020**, **UKO e UK NHS**e **Canada PBMM**.
    
    > [!TIP]
    > Solo gli utenti proprietari o collaboratori dei criteri dispongono delle autorizzazioni necessarie per aggiungere standard di conformità. 

    ![Aggiunta di pacchetti normativi al dashboard di conformità alle normative del Centro sicurezza di AzureAdding regulatory packages to Azure Security Center's regulatory compliance dashboard](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. Nella barra laterale del Centro sicurezza selezionare **Conformità alle normative** per aprire il dashboard di conformità alle normative. 
    * Azure CIS 1.1.0 (Nuovo) viene ora visualizzato nell'elenco degli standard normativi Industry &. 
    * Anche la visualizzazione *statica* originale della conformità di Azure CIS 1.1.0 rimarrà accanto. Potrebbe essere rimosso automaticamente in futuro.

    > [!NOTE]
    > La visualizzazione di uno standard appena aggiunto nel dashboard di conformità potrebbe richiedere alcune ore.


    [![Dashboard di conformità alle normative che mostra il dashboard di conformità delle normative che mostra il dashboard di Azure vecchio e il nuovo](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso quanto segue:

* Come **aggiornare gli standard** mostrati nel dashboard di conformità alle normative ai nuovi pacchetti *dinamici*
* Come **aggiungere pacchetti** di conformità per monitorare la conformità con standard aggiuntivi. 

Per altri materiali correlati, vedere i seguenti articoli: 

- [Dashboard di conformità alle normative del centro sicurezza](security-center-compliance-dashboard.md)
- [Utilizzo dei criteri di sicurezza](tutorial-security-policy.md)
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni su come usare le raccomandazioni del Centro sicurezza di Azure per proteggere le risorse di Azure.