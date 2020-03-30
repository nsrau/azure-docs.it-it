---
title: Domande frequenti su Analizzatore di Internet Documenti Microsoft
description: Domande frequenti su Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184262"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Domande frequenti su Azure Internet Analyzer (anteprima)Azure Internet Analyzer FAQ (Preview)

Queste sono le domande frequenti per Azure Internet Analyzer: se hai altre domande, vai al forum dei [feedback](https://aka.ms/internetAnalyzerFeedbackForum) e pubblica la tua domanda. Quando viene posta una domanda, la aggiungiamo a questo articolo in modo che possa essere trovata rapidamente e facilmente.

## <a name="how-do-i-participate-in-the-preview"></a>Come posso partecipare all'anteprima?

L'anteprima è disponibile per clienti selezionati. Se sei interessato a partecipare all'anteprima, fai quanto segue:

1. Accedere al [portale](https://ms.portal.azure.com)di Azure .
2. Passare alla pagina **Abbonamenti.**
3. Fare clic sulla sottoscrizione di Azure con cui si prevede di usare Internet Analyzer.
4. Passare alle impostazioni **dei provider di risorse** per la sottoscrizione.
5. Cercare **Microsoft.Network** e fare clic sul pulsante **Registra** (o **Ri-registrati).**
![richiesta di accesso](./media/ia-faq/request-preview-access.png)

6. [Richiedere l'approvazione](https://aka.ms/internetAnalyzerContact) fornendoci l'indirizzo di posta elettronica e l'ID sottoscrizione di Azure usato per effettuare la richiesta di accesso.
7. Dopo l'approvazione della richiesta, si riceverà una conferma tramite posta elettronica e sarà possibile creare/aggiornare/modificare le risorse di Internet Analyzer dalla sottoscrizione di Azure appena consentita.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>È necessario incorporare il client per eseguire un test?

Sì, il client Internet Analyzer deve essere installato nell'applicazione per raccogliere metriche specifiche per gli utenti. [Scopri come installare il client.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Mi viene addebitato il fatto di aver partecipato all'anteprima?
No, Azure Internet Analyzer è gratuito nell'anteprima. Non esiste un contratto di servizio durante l'anteprima.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Quali scenari è progettato per l'aggiornamento di Internet Analyzer?

Internet Analyzer è progettato per fornire informazioni dettagliate sulle prestazioni di rete in base alla popolazione di utenti. Per prendere le decisioni migliori in termini di prestazioni per gli utenti, Internet Analyzer confronta le prestazioni di due endpoint Internet utilizzando la popolazione di utenti distinta. L'analizzatore Internet può rispondere a numerose domande e di seguito vengono riportate alcune delle più comuni:

* Qual è l'impatto sulle prestazioni della migrazione cloud? 
    * *Test consigliato: personalizzato (l'infrastruttura locale corrente) e Azure (qualsiasi endpoint preconfigurato)Suggested Test: Custom (your current on-premises infrastructure) vs.*
* Quali sono le differenze tra ospitare i dati nella rete perimetrale rispetto al data center? 
    *  *Test consigliato: Azure e Azure Front Door, Azure e la rete CDN di Azure di Microsoft*
* Quali sono i vantaggi in termini di prestazioni di Frontdoor di Azure?
    *  *Test consigliato: Personalizzato/ Azure / CDN e porta frontale di Azure*
* Quali sono i vantaggi in termini di prestazioni di Rete CDN di Azure di Microsoft? 
    *  *Test consigliato: Personalizzato/ Azure/ AFD rispetto alla rete CDN di Azure di MicrosoftSuggested Test: Custom/ Azure/ AFD vs.*
* Come funziona la rete CDN di Azure di Microsoft? 
    *  *Test consigliato: personalizzato (altro endpoint della rete CDN) e la rete CDN di Azure di MicrosoftSuggested Test: Custom (other CDN endpoint) vs.*
* Qual è il cloud migliore per la tipologia di utenti finali in ogni area? 
    *  *Test consigliato: Personalizzato (altro servizio cloud) e Azure (qualsiasi endpoint preconfigurato)Suggested Test: Custom (other cloud service) vs.*

## <a name="which-tests-can-i-run-in-preview"></a>Quali test è possibile eseguire in anteprima?

Ogni test creato in Internet Analyzer è composto da due endpoint: Endpoint A e Endpoint B. Una delle seguenti combinazioni può essere eseguita come test:  
* Due endpoint preconfigurati,
* Un endpoint personalizzato e uno preconfigurato, oppure
* Due [endpoint personalizzati](internet-analyzer-custom-endpoint.md) (un endpoint personalizzato deve risiedere in Azure).

Durante l'anteprima sono disponibili i seguenti endpoint preconfigurati:
* **Aree di AzureAzure regions**
    * Brasile meridionale
    * India centrale
    * Stati Uniti centrali
    * Asia orientale
    * Stati Uniti orientali
    * Giappone occidentale
    * Europa settentrionale
    * Sudafrica settentrionale
    * Asia sud-orientale
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

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>In che modo Internet Analyzer è diverso dagli altri servizi di monitoraggio forniti da Azure?

Internet Analyzer consente di comprendere le prestazioni degli utenti finali e di prendere decisioni per migliorarne le prestazioni. Mentre altri strumenti di monitoraggio di Azure forniscono informazioni dettagliate sui servizi di Azure, Internet Analyzer si concentra sulla misurazione delle prestazioni Internet end-to-end per gli utenti.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Come vengono gestiti i dati di misurazione da Internet Analyzer?

Azure offre [processi di sicurezza efficaci e soddisfa una vasta gamma di standard di conformità](https://azure.microsoft.com/support/trust-center/). Solo lo sviluppatore dell’app e il team designato hanno accesso ai dati. Il personale Microsoft può avere accesso limitato ai dati solo in determinate circostanze con il consenso dello sviluppatore. È criptato in transito e a riposo.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [Panoramica](internet-analyzer-overview.md)di Internet Analyzer .
