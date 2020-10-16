---
title: Uso del dashboard conformità normativa nel centro sicurezza di Azure
description: Informazioni su come aggiungere e rimuovere gli standard normativi dal dashboard conformità normativa nel centro sicurezza
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2020
ms.author: memildin
ms.openlocfilehash: 5e32aa8491e18a66a3e8b90f065e6b785d1e3bd9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106264"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Personalizzazione del set di standard nel dashboard di conformità alle normative

Il Centro sicurezza di Azure confronta continuamente la configurazione delle risorse con i requisiti di standard del settore, normative e benchmark. Il **dashboard di conformità alle normative** fornisce informazioni dettagliate sulla postura di conformità in base a come vengono soddisfatti specifici controlli e requisiti.


## <a name="overview-of-compliance-packages"></a>Panoramica dei pacchetti di conformità

Gli standard di settore, gli standard normativi e i benchmark sono rappresentati nel Centro sicurezza come *pacchetti di conformità*.  Ogni pacchetto corrisponde a un'iniziativa definita in Criteri di Azure. Per vedere i dati di conformità mappati come valutazioni nel dashboard, aggiungere un pacchetto di conformità al gruppo di gestione o alla sottoscrizione nella pagina **Criteri di sicurezza**. Per altre informazioni su Criteri di Azure e iniziative, vedere [Uso dei criteri di sicurezza](tutorial-security-policy.md).

Quando viene caricato uno standard o un benchmark nell'ambito selezionato, l'iniziativa viene assegnata all'ambito e lo standard viene visualizzato nel dashboard di conformità alle iniziative con i dati di conformità associati mappati come valutazione. È anche possibile scaricare i report di riepilogo per gli standard caricati.

Microsoft tiene inoltre traccia degli standard normativi e migliora automaticamente la propria copertura in alcuni pacchetti nel corso del tempo. Quando Microsoft rilascia nuovi contenuti per l'iniziativa (nuovi criteri mappati a più controlli dello standard), il dashboard li visualizza automaticamente.

> [!TIP]
> Uno degli standard che migliora nel corso del tempo man mano che Microsoft rilascia nuovi contenuti è **Azure CIS 1.1.0 (nuova versione)** (più formalmente, [CIS Microsoft Azure Foundations Benchmark versione 1.1.0](https://www.cisecurity.org/benchmark/azure/)). Sarà necessario aggiungerlo al dashboard insieme da "Azure CIS 1.1.0", la rappresentazione di Azure CIS configurata per impostazione predefinita in ogni ambiente del Centro sicurezza. Questo pacchetto si basa su un set statico di regole. Il pacchetto più recente include più criteri e verrà aggiornato automaticamente nel corso del tempo. Eseguire l'aggiornamento al nuovo pacchetto dinamico come descritto di seguito.


## <a name="available-packages"></a>Pacchetti disponibili

È possibile aggiungere standard come NIST SP 800-53 R4, SWIFT CSP CSCF-v2020, UK Official e UK NHS, Canada Federal PBMM e Azure CIS 1.1.0 (nuova versione), una rappresentazione più completa di Azure CIS 1.1.0. 

Inoltre, è possibile aggiungere **Azure Security Benchmark**, le linee guida specifiche di Azure, create da Microsoft per le procedure consigliate per la sicurezza e la conformità basate su framework di conformità comuni. [Leggere altre informazioni su Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/introduction).

Nel dashboard verranno supportati altri standard non appena saranno disponibili. 


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Aggiungere uno standard normativo al dashboard

La procedura seguente illustra come aggiungere un pacchetto per monitorare la conformità a uno degli standard normativi supportati.

> [!NOTE]
> Solo gli utenti che hanno il ruolo di proprietario o collaboratore ai criteri hanno le autorizzazioni necessarie per aggiungere gli standard di conformità. 

1. Nella barra laterale del Centro sicurezza selezionare **Conformità con le normative** per aprire il dashboard corrispondente. Qui è possibile visualizzare gli standard di conformità attualmente assegnati alle sottoscrizioni selezionate.   

1. Nella parte superiore della pagina selezionare **Gestisci i criteri di conformità**. Verrà visualizzata la pagina Gestione dei criteri.

1. Selezionare la sottoscrizione o il gruppo di gestione per cui gestire la postura di conformità alle normative. 

    > [!TIP]
    > È consigliabile selezionare l'ambito più alto per cui è applicabile lo standard, in modo che i dati di conformità vengano aggregati e monitorati per tutte le risorse annidate. 

1. Per aggiungere gli standard pertinenti per l'organizzazione, fare clic su **Aggiungi altri standard**. 

1. Nella pagina **Aggiungi standard di conformità con le normative** è possibile cercare i pacchetti per uno degli standard disponibili. Alcuni standard disponibili sono:

    - **Azure Security Benchmark**
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF-v2020**
    - **UKO e UK NHS**
    - **Canada PBMM**
    
    ![Aggiunta di pacchetti normativi al dashboard di conformità alle normative del Centro sicurezza di Azure](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Selezionare **Aggiungi** e immettere tutti i dettagli necessari per l'iniziativa specifica, ad esempio ambito, parametri e correzione.

1. Nella barra laterale del Centro sicurezza selezionare di nuovo **Conformità con le normative** per tornare nel dashboard corrispondente.
    * Il nuovo standard verrà visualizzato nell'elenco degli standard normativi e di settore. 
    * Se è stato aggiunto **Azure CIS 1.1.0 (nuova versione)** , la visualizzazione *statica* originale dello standard di conformità Azure CIS 1.1.0 rimarrà affiancata. Potrebbe essere rimossa automaticamente in futuro.

    > [!NOTE]
    > La visualizzazione di uno standard appena aggiunto nel dashboard di conformità potrebbe richiedere alcune ore.

    [![Dashboard di conformità alle normative che mostra il vecchio e il nuovo standard Azure CIS](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)


## <a name="removing-a-standard-from-your-dashboard"></a>Rimozione di uno standard dal dashboard

Se uno degli standard normativi forniti non è pertinente per l'organizzazione, si tratta di un processo semplice per rimuoverli semplicemente dall'interfaccia utente. In questo modo è possibile personalizzare ulteriormente il dashboard di conformità alle normative e concentrarsi solo sugli standard applicabili.

Per rimuovere uno standard:

1. Dal menu del Centro sicurezza selezionare **criteri di sicurezza**.

1. Selezionare la sottoscrizione pertinente da cui si desidera rimuovere uno standard.

    > [!NOTE]
    > È possibile rimuovere uno standard da una sottoscrizione, ma non da un gruppo di gestione. 

    Verrà visualizzata la pagina Criteri di sicurezza. Per la sottoscrizione selezionata, vengono visualizzati i criteri predefiniti, gli standard di settore e normativi e tutte le iniziative personalizzate create.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Rimozione di uno standard normativo dal dashboard di conformità normativa nel centro sicurezza di Azure":::

1. Per lo standard che si desidera rimuovere, selezionare **Disabilita**. Viene visualizzata una finestra di conferma.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Rimozione di uno standard normativo dal dashboard di conformità normativa nel centro sicurezza di Azure":::

1. Selezionare **Sì**. Lo standard verrà rimosso. 


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come **aggiungere pacchetti di conformità** per monitorare la conformità a standard aggiuntivi. 

Per altri materiali correlati, vedere gli articoli seguenti: 

- [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [Dashboard di conformità alle normative del Centro sicurezza](security-center-compliance-dashboard.md)
- [Uso dei criteri di sicurezza](tutorial-security-policy.md)