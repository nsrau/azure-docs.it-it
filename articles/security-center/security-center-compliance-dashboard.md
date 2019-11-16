---
title: 'Esercitazione: Controlli di conformità alle normative - Centro sicurezza di Azure'
description: 'Esercitazione: informazioni su come migliorare la conformità alle normative con il Centro sicurezza di Azure.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2019
ms.author: memildin
ms.openlocfilehash: 01c0f1199ab8c376fe5bb5e864f81a2730879ed1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112326"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Esercitazione: Migliorare la conformità alle normative
---

Il Centro sicurezza di Azure consente di semplificare il processo per soddisfare i requisiti di conformità alle normative con il **dashboard Conformità alle normative**. Nel dashboard, il Centro sicurezza offre informazioni dettagliate sul comportamento di conformità in base a valutazioni continue dell'ambiente Azure. Il Centro sicurezza analizza i fattori di rischio nell'ambiente cloud ibrido in base alle procedure di sicurezza consigliate. Tali valutazioni sono associate ai controlli di conformità di un set di standard supportato. Nel dashboard Conformità alle normative può essere visualizzato lo stato di tutte queste valutazioni all'interno dell'ambiente, nel contesto di una normativa o uno standard specifico. Quando si interviene in base alle raccomandazioni riducendo i fattori di rischio nell'ambiente, il comportamento di conformità risulta migliorato.

In questa esercitazione si apprenderà come:

-   Valutare la conformità alle normative con il dashboard Conformità alle normative

-   Migliorare il comportamento di conformità intervenendo in base alle raccomandazioni

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per esaminare le funzionalità descritte in questa esercitazione, è necessario il piano tariffario Standard del Centro sicurezza. È possibile provare il livello Standard del Centro sicurezza gratuitamente.
Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/). La [Guida introduttiva per il Centro sicurezza di Azure ](https://docs.microsoft.com/azure/security-center/security-center-get-started) illustra come eseguire l'aggiornamento al livello Standard.

##  <a name="assess-your-regulatory-compliance"></a>Valutare la conformità alle normative

Il Centro sicurezza valuta continuamente la configurazione delle risorse per identificare vulnerabilità e problemi di sicurezza. Tali valutazioni vengono presentate come raccomandazioni, con l'obiettivo di migliorare la protezione della sicurezza. Nel dashboard Conformità alle normative è possibile visualizzare un set di standard di conformità con tutti i rispettivi requisiti. I requisiti supportati sono associati alle valutazioni di sicurezza applicabili. Ciò consente di visualizzare il comportamento di conformità rispetto allo standard, in base allo stato delle valutazioni.

La visualizzazione del dashboard Conformità alle normative consente di concentrare l'attenzione sulle lacune nella conformità a uno standard o una normativa importante per l'utente. Questa visualizzazione mirata permette anche il monitoraggio continuo nel tempo del punteggio di conformità all'interno di ambienti cloud e ibridi dinamici.

>[!NOTE]
> Per impostazione predefinita, il Centro sicurezza supporta gli standard normativi seguenti: Azure CIS, PCI DSS 3.2, ISO 27001 e SOC TSP. 
>
> La funzionalità [pacchetti di conformità dinamici (anteprima)](update-regulatory-compliance-packages.md) consente di aggiornare gli standard mostrati nel dashboard Conformità alle normative ai nuovi pacchetti *dinamici*. È anche possibile usare la stessa funzionalità di anteprima per aggiungere nuovi pacchetti di conformità e monitorare la conformità con gli standard aggiuntivi. 

1.  Nel menu principale del Centro sicurezza selezionare **Conformità alle normative** in **CRITERI E CONFORMITÀ**. <br>
Nella parte superiore della schermata verrà visualizzato un dashboard con una panoramica dello stato di conformità al set delle normative di conformità supportate. È possibile visualizzare il punteggio di conformità complessivo e il numero di valutazioni superate e non superate associate a ogni standard.

    ![Descrizione dei computer: affidabilità elevata](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  Selezionare la scheda di uno standard di conformità che interessa. Verrà visualizzato l'elenco di tutti i controlli per tale standard. Per i controlli applicabili, è possibile visualizzare i dettagli delle valutazioni superate e non superate associate al controllo. Alcuni controlli sono disattivati. A tali controlli non è associata alcuna valutazione del Centro sicurezza. Verificare i requisiti ed eseguirne autonomamente la valutazione nel proprio ambiente. Alcuni potrebbero essere correlati ai processi e non essere di tipo tecnico.

    ![Scheda relativa alla conformità](./media/security-center-compliance-dashboard/compliance-pci.png)

1. Per generare e scaricare un report PDF che riepiloga lo stato di conformità corrente per uno specifico standard, fare clic su **Scarica report**.

    Il report fornisce un riepilogo dettagliato dello stato di conformità per lo standard selezionato basato sui dati di valutazione del Centro sicurezza ed è organizzato in base ai controlli di tale particolare standard. Il report può essere condiviso con gli stakeholder di rilievo e può essere usato per fornire giustificativi a revisori interni ed esterni.

    ![download](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Migliorare il comportamento di conformità

In base alle informazioni riportate nel dashboard Conformità alle normative, è possibile migliorare il comportamento di conformità risolvendo le raccomandazioni direttamente all'interno del dashboard.

1.  Fare clic su una qualsiasi delle valutazioni non superate riportate nel dashboard per visualizzare i dettagli relativi alla raccomandazione. Ogni raccomandazione include una serie di passaggi di correzione da seguire per risolvere il problema.

1.  È possibile selezionare una determinata risorsa per visualizzare altri dettagli e risolvere la relativa raccomandazione. <br>Nella scheda dello standard **Azure CIS**, ad esempio, si può fare clic sulla raccomandazione **Richiedi il trasferimento sicuro nell'account di archiviazione**.

    ![Raccomandazione sulla conformità](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. Facendo clic sulle informazioni della raccomandazione e selezionando una risorsa non integra, si accede direttamente all'esperienza per abilitare il **trasferimento sicuro** per l'account di archiviazione nel portale di Azure.

    Per altre informazioni su come applicare le raccomandazioni, leggere l'articolo [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

    ![Raccomandazione sulla conformità](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  Dopo l'intervento per la risoluzione delle raccomandazioni, l'impatto sarà visibile nel report del dashboard relativo alla conformità tramite il miglioramento del punteggio di conformità.

    > [!NOTE]
    > Dato che le valutazioni vengono eseguite a intervalli di circa 12 ore, l'impatto sui dati di conformità sarà visibile solo dopo l'esecuzione delle valutazioni.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare il dashboard Conformità alle normative del Centro sicurezza per:

-   Visualizzare e monitorare il comportamento di conformità in relazione agli standard e alle normative importanti per l'utente.

-   Migliorare lo stato di conformità risolvendo le raccomandazioni rilevanti e osservando il miglioramento del punteggio di conformità.

Il dashboard Conformità alle normative può semplificare notevolmente il processo di conformità e ridurre in modo significativo il tempo necessario per raccogliere prove della conformità dell'ambiente ibrido e Azure in uso.

Per altre informazioni, vedere:

-   [Aggiornare i pacchetti di conformità dinamici nel dashboard Conformità alle normative (anteprima)](update-regulatory-compliance-packages.md): informazioni su questa funzionalità di anteprima che consente di aggiornare gli standard mostrati nel dashboard Conformità alle normative ai nuovi pacchetti *dinamici*. È anche possibile usare la stessa funzionalità di anteprima per aggiungere nuovi pacchetti di conformità e monitorare la conformità con gli standard aggiuntivi. 

-   [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.

-   [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni su come usare le raccomandazioni del Centro sicurezza di Azure per proteggere le risorse di Azure.

-   [Migliorare il punteggio di sicurezza nel Centro sicurezza di Azure](security-center-secure-score.md): informazioni su come classificare in ordine di priorità le raccomandazioni sulla sicurezza per ottenere il massimo miglioramento del comportamento di sicurezza.

-   [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
