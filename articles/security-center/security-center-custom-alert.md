---
title: Regole di avviso personalizzate nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento consente di creare regole di avviso personalizzate nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: a5deee4209001d8c2212033c2d547d7c4199bd25
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192630"
---
# <a name="custom-alert-rules-in-azure-security-center-preview"></a>Regole di avviso personalizzate nel Centro sicurezza di Azure (anteprima)
Questo documento consente di creare regole di avviso personalizzate nel Centro sicurezza di Azure.

> [!NOTE]
> Avvisi personalizzati verranno ritirati il 30 giugno 2019.

## <a name="retirement-of-custom-alert-rules-in-azure-security-center"></a>Le regole di ritiro di avviso personalizzate nel Centro sicurezza di Azure

L'esperienza avvisi personalizzati verrà ritirata il 30 giugno 2019, a causa di ritiro dell'infrastruttura sottostante di che cui si basa. Nell'intervallo di tempo fino a elementi deprecati, gli utenti saranno in grado di modificare le regole di avviso personalizzate esistenti ma non sarà in grado di aggiungere quelli nuovi.
Gli utenti sono invitati a uno:
- Abilitare [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/) con un solo clic onboarding automaticamente la migrazione degli avvisi esistenti e crearne uno nuovo
- Ricreare gli avvisi con avvisi di log di monitoraggio di Azure
                                     
Per mantenere gli avvisi esistenti e ne esegue la migrazione a Azure Sentinel, please [avviare Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview). Come primo passaggio, selezionare l'area di lavoro in cui sono archiviati gli avvisi personalizzati e quindi selezionare la voce di menu 'Analitica' per eseguire la migrazione automatica degli avvisi.

> [!NOTE]
> Migrazione di avvisi personalizzati per Azure Sentinel è un'unica migrazione di tutti gli avvisi personalizzati nell'area di lavoro selezionato. Al termine della migrazione, gli avvisi personalizzati per tale area di lavoro selezionata non saranno accessibili tramite Centro sicurezza di Azure.
>
> Avvisi personalizzati usando [ricerca](https://docs.microsoft.com/azure/azure-monitor/log-query/search-queries) oppure [Union](https://docs-analytics-eus.azurewebsites.net/queryLanguage/query_language_unionoperator.html) istruzioni query non sono supportate in Azure Sentinel e non verrà eseguita. Modificare questi avvisi prima di eseguire la migrazione.

Per ricreare gli avvisi tramite avvisi di log di monitoraggio di Azure, vedere: [Creare, visualizzare e gestire gli avvisi del log tramite Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) per istruzioni su come creare avvisi del log. Per una panoramica generale degli avvisi del log in Monitoraggio di Azure, fare clic su [qui](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

## <a name="what-are-custom-alert-rules-in-security-center"></a>Informazioni sulle regole di avviso personalizzate nel Centro sicurezza

Il Centro sicurezza include un set di [avvisi di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) predefiniti, che vengono attivati in caso di minaccia o di attività sospetta. In alcuni scenari è consigliabile creare un avviso personalizzato per rispondere a esigenze specifiche dell'ambiente.

Le regole di avviso personalizzate nel Centro sicurezza consentono di definire nuovi avvisi di sicurezza in base ai dati già raccolti dall'ambiente. È possibile creare query e usare i risultati di tali query come criteri per la regola personalizzata, che viene eseguita in caso di corrispondenza con i criteri. Per creare le query personalizzate, è possibile usare gli eventi di sicurezza dei computer, i log delle soluzioni di sicurezza dei partner o i dati inseriti tramite API.

> [!NOTE]
> Gli avvisi personalizzati non sono supportati nelle [funzionalità di indagine](security-center-investigation.md) del Centro sicurezza.
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Procedura per la creazione di una regola di avviso nel Centro sicurezza

Aprire il dashboard **Centro sicurezza** e seguire questa procedura per creare una regola di avviso personalizzata:

1.  In **Rilevamento** nel riquadro di sinistra fare clic su **Regole di avviso personalizzate (anteprima)**.
2.  Nella pagina **Centro sicurezza – Regole di avviso personalizzate (anteprima)** fare clic su **Nuova regola di avviso personalizzata**.

    ![Regola personalizzata](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)

3.  Viene visualizzata la pagina Crea una regola di avviso personalizzata con le regole seguenti:

    ![Create](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  Digitare il nome per la regola personalizzata nel campo **Nome**.
5.  Immettere una breve descrizione che rispecchia la finalità della regola nel campo **Descrizione**.
6.  Selezionare il livello di gravità (Alta, Media, Bassa) in base alle esigenze specifiche nel campo **Gravità**.
7.  Selezionare la sottoscrizione in cui è applicabile questa regola nel campo **Sottoscrizione**.
8.  Selezionare l'area di lavoro da monitorare con questa regola nel campo **Area di lavoro** e quindi nel campo **Query di ricerca** selezionare la query che si desidera usare per ottenere i risultati.

    > [!NOTE]
    > È necessaria l'autorizzazione di scrittura nell'area di lavoro che si seleziona per archiviare l'avviso personalizzato.
    >
    >

    Il risultato della query attiva l'avviso. Si noti che quando si immette una query valida viene visualizzato un segno di spunta verde nell'angolo destro del campo:

    ![Query](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. Nel campo **Periodo** selezionare l'intervallo di tempo durante il quale verrà eseguita la query precedente. Si noti che il risultato della ricerca nella parte inferiore del campo dipenderà dall'intervallo di tempo selezionato.

    ![Periodo](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. Nel campo **Valutazione** selezionare la frequenza per la valutazione e l'esecuzione di questa regola.
12. Nel campo **Numero di risultati** selezionare l'operatore (maggiore di o minore di).
13. Nel campo **Soglia** digitare un numero che verrà usato come riferimento per l'operatore selezionato in precedenza.
14. Abilitare l'opzione **Elimina avvisi** se si vuole configurare un tempo di attesa prima dell'invio di un altro avviso per questa regola da parte del Centro sicurezza.
15. Fare clic su **OK** per terminare.

Dopo la creazione, la nuova regola di avviso verrà visualizzata nell'elenco di regole di avviso personalizzate. Quando vengono soddisfatte le condizioni della regola, verrà attivato un nuovo avviso, che verrà visualizzato nel dashboard **Avvisi di sicurezza**.

![Avviso](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

Si noti che i parametri (query di ricerca, soglia e così via) stabiliti durante la creazione della regola sono disponibili nell'avviso per questa regola personalizzata.

## <a name="see-also"></a>Vedere anche 
In questo documento è stato descritto come creare una regola di avviso personalizzata nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informazioni su come risolvere i problemi comuni nel Centro sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.
