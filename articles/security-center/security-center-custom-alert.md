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
ms.openlocfilehash: 8845cb0a91edefa0350558f35488519ec37d064d
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663580"
---
# <a name="custom-alert-rules-in-azure-security-center-retired"></a>Regole di avviso personalizzate nel centro sicurezza di Azure (ritirate)
Questo documento consente di creare regole di avviso personalizzate (anteprima) nel centro sicurezza di Azure.

> [!NOTE]
> Gli avvisi personalizzati sono stati ritirati dal centro sicurezza di Azure il 30 giugno 2019. 

## <a name="retirement-of-custom-alert-rules-in-azure-security-center"></a>Ritiro di regole di avviso personalizzate nel centro sicurezza di Azure

L'esperienza di avvisi personalizzati è stata ritirata il 30 giugno 2019, a causa del ritiro dell'infrastruttura sottostante su cui si basava. Gli avvisi personalizzati definiti prima del retriement non dipendono dall'effetto e gli avvisi di sicurezza basati su tali regole di avviso personalizzate non vengono generati. È comunque possibile visualizzare le query delle regole di avviso personalizzate nel centro sicurezza per ricrearle nelle alternative indicate di seguito:

Gli utenti sono invitati a:
- Abilitare [Sentinel di Azure](https://azure.microsoft.com/services/azure-sentinel/) e usare la funzionalità di [analisi](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) predefinita per ricreare le regole di avviso
- Ricreare gli avvisi con gli avvisi del log di monitoraggio di Azure
                                     
Per tenere gli avvisi esistenti e ricrearli in Azure Sentinel, [avviare Sentinel di Azure](https://portal.azure.com/#create/Microsoft.ASI/preview). Come primo passaggio, selezionare l'area di lavoro in cui sono archiviati gli avvisi personalizzati, quindi selezionare la voce di menu "Analytics" per configurare le regole di avviso personalizzate. Per ulteriori informazioni, consultare la [documentazione](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) .

> [!NOTE]
> Gli avvisi personalizzati che usano le query di istruzioni di [ricerca](https://docs.microsoft.com/azure/azure-monitor/log-query/search-queries) o [Unione](https://docs-analytics-eus.azurewebsites.net/queryLanguage/query_language_unionoperator.html) non sono supportati in Sentinel di Azure. Prima di eseguire la migrazione, modificare questi avvisi.

Per informazioni su come ricreare gli avvisi usando gli avvisi del log di monitoraggio di Azure, vedere [creare, visualizzare e gestire gli avvisi di log con monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log). Per una panoramica generale, vedere [avvisi del log in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

## <a name="what-are-custom-alert-rules-in-security-center"></a>Informazioni sulle regole di avviso personalizzate nel Centro sicurezza

Il Centro sicurezza include un set di [avvisi di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) predefiniti, che vengono attivati in caso di minaccia o di attività sospetta. In alcuni scenari è consigliabile creare un avviso personalizzato per rispondere a esigenze specifiche dell'ambiente.

Le regole di avviso personalizzate nel Centro sicurezza consentono di definire nuovi avvisi di sicurezza in base ai dati già raccolti dall'ambiente. È possibile creare query e usare i risultati di tali query come criteri per la regola personalizzata, che viene eseguita in caso di corrispondenza con i criteri. Per creare le query personalizzate, è possibile usare gli eventi di sicurezza dei computer, i log delle soluzioni di sicurezza dei partner o i dati inseriti tramite API.

> [!NOTE]
> Gli avvisi personalizzati non sono supportati nelle [funzionalità di indagine](security-center-investigation.md) del Centro sicurezza.
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Procedura per la creazione di una regola di avviso nel Centro sicurezza

Aprire il dashboard **Centro sicurezza** e seguire questa procedura per creare una regola di avviso personalizzata:

1.  In **Rilevamento** nel riquadro di sinistra fare clic su **Regole di avviso personalizzate (anteprima)** .
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
