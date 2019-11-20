---
title: Domande frequenti su Internet Analyzer | Microsoft Docs
description: Domande frequenti su Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184262"
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

Internet Analyzer è progettato per fornire informazioni dettagliate sulle prestazioni di rete in base alla popolazione degli utenti. Per prendere le migliori decisioni in merito alle prestazioni per gli utenti, Internet Analyzer confronta le prestazioni di due endpoint Internet usando la popolazione di utenti distinti. L'analizzatore Internet può rispondere a numerose domande e di seguito vengono riportate alcune delle più comuni:

* Qual è l'impatto sulle prestazioni della migrazione cloud? 
    * *Test suggerito: personalizzato (l'infrastruttura locale corrente) rispetto ad Azure (qualsiasi endpoint preconfigurato)*
* Quali sono le differenze tra ospitare i dati nella rete perimetrale rispetto al data center? 
    *  *Test suggerito: Azure e Azure front door, Azure e la rete CDN di Azure di Microsoft*
* Quali sono i vantaggi in termini di prestazioni di Frontdoor di Azure?
    *  *Test suggerito: personalizzato/Azure/rete CDN rispetto a Azure front door*
* Quali sono i vantaggi in termini di prestazioni di Rete CDN di Azure di Microsoft? 
    *  *Test suggerito: Custom/Azure/AFD rispetto alla rete CDN di Azure di Microsoft*
* Come funziona la rete CDN di Azure di Microsoft? 
    *  *Test suggerito: personalizzato (altro endpoint della rete CDN) rispetto alla rete CDN di Azure di Microsoft*
* Qual è il cloud migliore per la tipologia di utenti finali in ogni area? 
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
    * Stati Uniti Orientali
    * Giappone occidentale
    * Europa settentrionale
    * Sudafrica settentrionale
    * Asia sudorientale
    * Emirati Arabi Uniti settentrionali
    * Regno Unito occidentale  
    * Europa occidentale
    * Stati Uniti occidentali
    * Stati Uniti occidentali 2
* **Combinazioni di più aree di Azure**
    * Stati Uniti orientali, Brasile meridionale
    * Stati Uniti orientali, Asia orientale
    * Europa occidentale, Brasile meridionale
    * Europa occidentale, Asia sud-orientale
    * Europa occidentale, Emirati Arabi Uniti settentrionali
    * Stati Uniti occidentali, Stati Uniti orientali
    * Stati Uniti occidentali, Europa occidentale
    * Stati Uniti occidentali, Emirati Arabi Uniti settentrionali
    * Europa occidentale, Emirati Arabi Uniti settentrionali, Asia sud-orientale
    * Stati Uniti occidentali, Europa occidentale, Asia orientale
    * Stati Uniti occidentali, Europa settentrionale, Asia sud-orientale, Emirati Arabi Uniti settentrionali, Sudafrica settentrionale 
* **Azure + Frontdoor di Azure**: distribuiti in qualsiasi combinazione singola o di più aree di Azure tra quelle elencate sopra
* **Azure + Rete CDN di Azure di Microsoft**: distribuiti in qualsiasi combinazione singola di aree di Azure tra quelle elencate sopra
* **Azure + Gestione traffico di Azure**: distribuiti in qualsiasi combinazione di più aree di Azure tra quelle elencate sopra

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>In che modo Internet Analyzer è diverso da altri servizi di monitoraggio offerti da Azure?

Internet Analyzer ti aiuta a comprendere le prestazioni degli utenti finali e ad aiutarti a prendere decisioni per migliorarne le prestazioni. Mentre altri strumenti di monitoraggio di Azure forniscono informazioni approfondite sui servizi di Azure, Internet Analyzer si concentra sulla misurazione delle prestazioni Internet end-to-end per gli utenti.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Come vengono gestiti i dati di misurazione da Internet Analyzer?

Azure offre [processi di sicurezza efficaci e soddisfa una vasta gamma di standard di conformità](https://azure.microsoft.com/support/trust-center/). Solo lo sviluppatore dell’app e il team designato hanno accesso ai dati. Il personale Microsoft può avere accesso limitato ai dati solo in determinate circostanze con il consenso dello sviluppatore. È crittografato in transito e inattivo.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la [Panoramica di Internet Analyzer](internet-analyzer-overview.md).
