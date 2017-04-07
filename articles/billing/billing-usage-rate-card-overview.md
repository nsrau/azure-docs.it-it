---
title: API di fatturazione di Azure | Microsoft Docs
description: "Informazioni sull&quot;utilizzo dell’API di fatturazione e dell’API RestCard di Azure, utilizzate per offrire informazioni sul consumo di risorse e sulle tendenze di Azure."
services: 
documentationcenter: 
author: BryanLa
manager: ruchic
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 03/25/2017
ms.author: mobandyo;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 869734ad4eb2d7a408cc0d33d1492b50b1b8dc0f
ms.lasthandoff: 03/29/2017


---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Usare le API di fatturazione di Azure per ottenere informazioni approfondite sull'uso di Azure a livello di codice
Usare le API di fatturazione di Azure per raccogliere e immettere i dati di uso e delle risorse negli strumenti di analisi dei dati scelti. Le API di utilizzo delle risorse di Azure e RateCard possono aiutare a prevedere e gestire i costi con precisione. Le API vengono implementate come provider di risorse, nell’ambito della famiglia di API esposte da Azure Resource Manager.  

## <a name="azure-resource-usage-api-preview"></a>API di utilizzo delle risorse di Azure (anteprima)
Usare l'API di utilizzo delle risorse di Azure per ottenere una stima dei dati di consumo di Azure. L'API include:

* **Controllo degli accessi in base al ruolo di Azure**: configurare i propri criteri di accesso nel [portale di Azure](https://portal.azure.com) o tramite i [cmdlet di Azure PowerShell](/powershell/azureps-cmdlets-docs) per specificare quali utenti o applicazioni possono avere accesso ai dati di utilizzo della sottoscrizione. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Aggiungere il chiamante al ruolo Lettore, Proprietario o Collaboratore per ottenere l'accesso ai dati di utilizzo per una determinata sottoscrizione di Azure.
* **Aggregazioni orarie o giornaliere** : i chiamanti possono specificare se desiderano i dati di utilizzo di Azure in intervalli orari o giornalieri. L’impostazione predefinita è rappresentata dagli intervalli giornalieri.
* **Metadati dell'istanza (inclusi i tag delle risorse)**: nella risposta vengono forniti i dettagli a livello di istanza, ad esempio l'URI della risorsa completo (/subscriptions/{subscription-id}/..), insieme alle informazioni sul gruppo di risorse e ai tag delle risorse. Tali metadati aiutano gli utenti ad allocare l’utilizzo in modo deterministico e programmatico in base ai tag, per casi di utilizzo come l’addebito delle tariffe.
* **Metadati delle risorse**: dettagli delle risorse, ad esempio il nome, la categoria e la sottocategoria del misuratore, l'unità e l'area, per fornire ai chiamanti una migliore comprensione delle risorse utilizzate. Stiamo inoltre lavorando per allineare la terminologia dei metadati delle risorse nel portale di Azure, il CSV di utilizzo di Azure, il CSV di fatturazione EA e altre esperienze pubbliche, per consentire agli utenti di correlare i dati delle diverse esperienze.
* **Utilizzo per tutti i tipi di offerte** : i dati di utilizzo sono accessibili per tutti i tipi di offerta, tra cui il pagamento in base al consumo, MSDN, l’impegno monetario, il credito monetari ed EA.

## <a name="azure-resource-ratecard-api-preview"></a>API RateCard delle risorse di Azure (anteprima)
Usare l'API RateCard delle risorse di Azure per ottenere l'elenco delle risorse di Azure disponibili e una stima delle informazioni di prezzo di ognuna di esse. L'API include:

* **Controllo degli accessi in base al ruolo di Azure**: configurare i propri criteri di accesso nel [portale di Azure](https://portal.azure.com) o tramite i [cmdlet di Azure PowerShell](/powershell/azureps-cmdlets-docs) per specificare quali utenti o applicazioni possono avere accesso ai dati di RateCard. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Aggiungere il chiamante al ruolo Lettore, Proprietario o Collaboratore per ottenere l'accesso ai dati di utilizzo per una determinata sottoscrizione di Azure.
* **Supporto delle offerte con pagamento in base al consumo, MSDN, impegno monetario e credito monetario (EA non supportato)** : questa API fornisce informazioni sulle tariffe a livello di offerta di Azure.  Il chiamante di questa API deve passare le informazioni sull'offerta per dettagli e tariffe delle risorse. Dal momento che le offerte EA hanno tariffe personalizzate in base alla registrazione, al momento non è possibile fornire le tariffe EA. 

## <a name="scenarios"></a>Scenari
Di seguito sono illustrati alcuni scenari resi possibili con la combinazione di API di utilizzo e API RateCard:

* **Spesa di Azure durante il mese**: usare le API di utilizzo e RateCard per ottenere informazioni più approfondite sulle spese mensili legate al cloud. È possibile analizzare i bucket di utilizzo orari e giornalieri e le stime di addebito.
* **Impostare avvisi**: usare le API di utilizzo e RateCard per ottenere una stima dei consumi e degli addebiti legati al cloud e per impostare avvisi basati sulle risorse o sui costi.
* **Previsione delle fatture**: è possibile ottenere le stime sui consumi e sulle spese per il cloud e applicare algoritmi di Machine Learning per prevedere l’importo della fattura al termine del periodo di fatturazione.
* **Analisi dei costi prima del consumo**: usare l'API RateCard per prevedere l'importo della fattura per l'utilizzo quando si spostano i carichi di lavoro in Azure. Se si dispone di carichi di lavoro esistenti in altri cloud o nei cloud privati, è possibile inoltre eseguire il mapping dell'utilizzo con le tariffe di Azure per ottenere una stima più accurata della spesa stimata di Azure. Questa stima offre la possibilità di partire da un'offerta e di confrontarla con le altre disponibili oltre al pagamento in base al consumo, come l'impegno monetario e il credito monteraio. L'API, inoltre, offre la possibilità di visualizzare le differenze di costo per area geografica e consente di eseguire una simulazione dei costi per facilitare le decisioni legate alla distribuzione.
* **Analisi di simulazione** -
  
  * È possibile determinare se è più conveniente eseguire i propri carichi di lavoro in un'altra area o in un'altra configurazione della risorsa di Azure. I costi delle risorse di Azure possono variare in base all'area di Azure in uso.
  * È inoltre possibile determinare se un altro tipo di offerta di Azure offre una tariffa migliore per una risorsa di Azure.

## <a name="partner-solutions"></a>Soluzioni partner
Nell'articolo [L'API di utilizzo e l'API RateCard di Microsoft Azure Usage consentono a Cloudyn di fornire ITFM ai clienti](billing-usage-rate-card-partner-solution-cloudyn.md) viene descritta l'esperienza di integrazione offerta dal partner API di fatturazione di Azure [Cloudyn](https://www.cloudyn.com/microsoft-azure/). Questo articolo illustra le loro esperienze e include un video che mostra come usare Cloudyn e le API di fatturazione di Azure per ottenere informazioni approfondite dai dati di utilizzo di Azure.

In [Cloud Cruiser e integrazione dell'API di fatturazione di Microsoft Azure](billing-usage-rate-card-partner-solution-cloudcruiser.md) viene descritto come la [versione Express di Cloud Cruiser per Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) funzioni direttamente dal portale Windows Azure Pack (WAP), consentendo ai clienti di gestire senza problemi gli aspetti operativi e finanziari del cloud privato o pubblico di Microsoft Azure da una singola interfaccia utente.   

## <a name="next-steps"></a>Passaggi successivi
* Vedere i [Riferimenti all'API REST della fatturazione di Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) per altre informazioni su entrambe le API.
* Se si desidera approfondire il codice di esempio, vedere gli esempi di codice dell'API di fatturazione di Microsoft Azure in [Esempi di codice di Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Altre informazioni
* Vedere la [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Per altre informazioni sulla suite di strumenti necessari per conoscere la spesa relativa al cloud, vedere l'articolo di Gartner sulla [Guida di mercato agli strumenti ITFM](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).


