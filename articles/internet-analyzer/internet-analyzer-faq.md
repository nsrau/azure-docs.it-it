---
title: Domande frequenti su Internet Analyzer | Microsoft Docs
description: Domande frequenti su Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 61cb6003549530f381b9cbbed74c1cb62c91431c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512899"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Domande frequenti su Azure Internet Analyzer (anteprima)

Queste sono le domande frequenti su Azure Internet Analyzer. per altre domande, visitare il forum dei [Commenti](https://aka.ms/internetAnalyzerFeedbackForum) e inviare una domanda. Quando una domanda viene richiesta di frequente, viene aggiunta a questo articolo per poterla trovare in modo rapido e semplice.

## <a name="how-do-i-participate-in-the-preview"></a>Come si partecipa all'anteprima?

L'anteprima è disponibile per clienti selezionati. Se si è interessati a partecipare all'anteprima, seguire questa procedura:

1. Accedere al [portale di Azure](https://ms.portal.azure.com).
2. Passare alla pagina **sottoscrizioni** .
3. Fare clic sulla sottoscrizione di Azure con cui si prevede di usare Internet Analyzer.
4. Passare alle impostazioni dei **provider di risorse** per la sottoscrizione.
5. Cercare **Microsoft. Network** e fare clic sul pulsante **Register** (o **Re-Register**).
richiesta di accesso ![](./media/ia-faq/request-preview-access.png)

6. [Richiedere l'approvazione](https://aka.ms/internetAnalyzerContact) fornendo l'indirizzo di posta elettronica e l'ID sottoscrizione di Azure usato per effettuare la richiesta di accesso.
7. Una volta approvata la richiesta, si riceverà un messaggio di posta elettronica di conferma e sarà possibile creare/aggiornare/modificare le risorse di Internet Analyzer dalla sottoscrizione di Azure appena consentita.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>È necessario incorporare il client per eseguire un test?

Sì, il client di Internet Analyzer deve essere installato nell'applicazione per raccogliere le metriche specifiche per gli utenti. [Informazioni su come installare il client di.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Per la partecipazione all'anteprima vengono fatturati costi?
No, Azure Internet Analyzer può essere usato gratuitamente in anteprima. Durante l'anteprima non è previsto alcun contratto di servizio.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Quali scenari è stato progettato per Internet Analyzer?

Internet Analyzer è progettato per fornire informazioni dettagliate sulle prestazioni di rete in base alla popolazione degli utenti. Per prendere le migliori decisioni in merito alle prestazioni per gli utenti, Internet Analyzer confronta le prestazioni di due endpoint Internet usando la popolazione di utenti distinti. Sebbene Internet Analyzer possa rispondere a numerose domande, alcune delle più comuni sono:

* Qual è l'effetto sulle prestazioni della migrazione al cloud? 
    * *Test suggerito: personalizzato (l'infrastruttura locale corrente) rispetto ad Azure (qualsiasi endpoint preconfigurato)*
* Qual è il valore di inserimento dei dati nella rete perimetrale rispetto a una data center? 
    *  *Test suggerito: Azure e Azure front door, Azure e la rete CDN di Azure di Microsoft*
* Quali sono i vantaggi in merito alle prestazioni di Azure front door?
    *  *Test suggerito: personalizzato/Azure/rete CDN rispetto a Azure front door*
* Qual è il vantaggio in merito alle prestazioni della rete CDN di Azure di Microsoft? 
    *  *Test suggerito: Custom/Azure/AFD rispetto alla rete CDN di Azure di Microsoft*
* Come funziona la rete CDN di Azure da Microsoft? 
    *  *Test suggerito: personalizzato (altro endpoint della rete CDN) rispetto alla rete CDN di Azure di Microsoft*
* Qual è il cloud migliore per il popolamento dell'utente finale in ogni area? 
    *  *Test suggerito: personalizzato (altro servizio cloud) rispetto ad Azure (qualsiasi endpoint preconfigurato)*

## <a name="which-tests-can-i-run-in-preview"></a>Quali test è possibile eseguire in anteprima?

Ogni test creato in Internet Analyzer è costituito da due endpoint: endpoint A ed endpoint B. Una qualsiasi delle combinazioni seguenti può essere eseguita come test:  
* Due endpoint preconfigurati,
* Un endpoint personalizzato e un endpoint preconfigurato, oppure
* Due [endpoint personalizzati](internet-analyzer-custom-endpoint.md) (un endpoint personalizzato deve risiedere in Azure).

Gli endpoint preconfigurati seguenti sono disponibili durante l'anteprima:
* **Aree di Azure**
    * Brasile meridionale
    * India centrale
    * Stati Uniti centrali
    * Asia orientale
    * Stati Uniti orientali
    * Giappone occidentale
    * Europa settentrionale
    * Sudafrica settentrionale
    * Asia sudorientale
    * Emirati Arabi Uniti settentrionali
    * Regno Unito occidentale  
    * Europa occidentale
    * Stati Uniti occidentali
    * Stati Uniti occidentali 2
* **Più combinazioni di aree di Azure**
    * Stati Uniti orientali, Brasile meridionale
    * Stati Uniti orientali, Asia orientale
    * Europa occidentale, Brasile meridionale
    * Europa occidentale, Asia sudorientale
    * Europa occidentale, Emirati Arabi Uniti settentrionali
    * Stati Uniti occidentali, Stati Uniti orientali
    * Stati Uniti occidentali, Europa occidentale
    * Stati Uniti occidentali, Emirati Arabi Uniti settentrionali
    * Europa occidentale, Emirati Arabi Uniti settentrionali, Asia sudorientale
    * Stati Uniti occidentali, Europa occidentale, Asia orientale
    * Stati Uniti occidentali, Europa settentrionale, Asia sudorientale, Emirati Arabi Uniti settentrionali, Sudafrica settentrionale 
* **Azure + sportello anteriore Azure** distribuito in una o più combinazioni di aree di Azure sopra elencate
* **Azure + rete CDN di Azure di Microsoft** -distribuito in una singola combinazione di area di Azure sopra indicata
* **Azure + Gestione traffico di Azure** -distribuito in qualsiasi combinazione di più aree di Azure sopra indicata

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>In che modo Internet Analyzer è diverso da altri servizi di monitoraggio offerti da Azure?

Internet Analyzer ti aiuta a comprendere le prestazioni degli utenti finali e ad aiutarti a prendere decisioni per migliorarne le prestazioni. Mentre altri strumenti di monitoraggio di Azure forniscono informazioni approfondite sui servizi di Azure, Internet Analyzer si concentra sulla misurazione delle prestazioni Internet end-to-end per gli utenti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la [Panoramica di Internet Analyzer](internet-analyzer-overview.md).
