---
title: Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure | Documentazione Microsoft
description: "Fornisce una panoramica concettuale sull&quot;utilizzo dell’API di fatturazione e dell’API RestCard di Azure, che sono utilizzate per fornire informazioni sul consumo di risorse e sulle tendenze di Azure."
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
ms.date: 01/07/2017
ms.author: mobandyo;bryanla
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 35bd24f8666d2954be37bd27abebbc6c3c8a6a54


---
# <a name="gain-insights-into-your-microsoft-azure-resource-consumption"></a>Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure
Clienti e partner richiedono la possibilità di prevedere e gestire i costi di Azure con precisione.  Nel passaggio da un modello basato sugli investimenti a un modello basato sui costi operativi, devono inoltre avere la possibilità di eseguire analisi di showback e chargeback, nonché fornire fedeltà di modalità per la stima e la fatturazione, specialmente per le distribuzioni di cloud di grandi dimensioni.

L'API di utilizzo delle risorse di Azure e l’API RateCard illustrate in questo articolo soddisfano queste esigenze, offrendo la possibilità di ottenere nuove informazioni sul consumo delle risorse di Azure.  

## <a name="introducing-the-azure-resource-usage-and-ratecard-apis"></a>Introduzione all’API di utilizzo delle risorse di Azure e all’API RateCard
L'API di utilizzo delle risorse di Azure e l’API RateCard vengono implementate come provider di risorse, nell’ambito della famiglia di API esposte da Gestione risorse di Azure.  

### <a name="azure-resource-usage-api-preview"></a>API di utilizzo delle risorse di Azure (anteprima)
Clienti e partner possono utilizzare l'API di utilizzo delle risorse di Azure per ottenere una stima dei dati di consumo di Azure. Le funzionalità includono:

* **Controllo degli accessi in base al ruolo di Azure**: clienti e partner possono configurare i propri criteri di accesso nel [portale di Azure](https://portal.azure.com) o tramite i [cmdlet di Azure PowerShell](/powershell/azureps-cmdlets-docs) per specificare quali utenti o applicazioni possono avere accesso ai dati di utilizzo della sottoscrizione. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Il chiamante deve anche essere aggiunto al ruolo Lettore, Proprietario o Collaboratore per ottenere l'accesso ai dati di utilizzo per una determinata sottoscrizione di Azure.
* **Aggregazioni orarie o giornaliere** : i chiamanti possono specificare se desiderano i dati di utilizzo di Azure in intervalli orari o giornalieri. L’impostazione predefinita è rappresentata dagli intervalli giornalieri.
* **Metadati dell'istanza forniti (inclusi i tag delle risorse)** : nella risposta vengono forniti i dettagli a livello di istanza, ad esempio l'URI della risorsa completo (/subscriptions/{subscription-id}/..), insieme alle informazioni sul gruppo di risorse e ai tag delle risorse che verranno forniti nella risposta. In tal modo gli utenti potranno allocare l’utilizzo in modo deterministico e programmatico in base ai tag, per casi di utilizzo come l’addebito delle tariffe.
* **Metadati delle risorse forniti** : nella risposta verranno inoltre passati i dettagli delle risorse, ad esempio il nome, la categoria e la sottocategoria del misuratore, per fornire ai chiamanti una migliore comprensione delle risorse utilizzate. Stiamo inoltre lavorando per allineare la terminologia dei metadati delle risorse nel portale di Azure, il CSV di utilizzo di Azure, il CSV di fatturazione EA e altre esperienze pubbliche, per consentire ai clienti di correlare i dati delle diverse esperienze.
* **Utilizzo per tutti i tipi di offerte** : i dati di utilizzo saranno accessibili per tutti i tipi di offerta, inclusi, tra gli altri, il pagamento in base al consumo, MSDN, l’impegno monetario, il credito monetari ed EA.

### <a name="azure-resource-ratecard-api-preview"></a>API RateCard delle risorse di Azure (anteprima)
Clienti e partner possono utilizzare l'API RateCard delle risorse di Azure per ottenere l'elenco delle risorse di Azure disponibili, insieme a una stima delle informazioni di prezzo di ognuna di esse. Le funzionalità includono:

* **Controllo degli accessi in base al ruolo di Azure**: clienti e partner possono configurare i propri criteri di accesso nel [portale di Azure](https://portal.azure.com) o tramite i [cmdlet di Azure PowerShell](/powershell/azureps-cmdlets-docs) per specificare quali utenti o applicazioni possono avere accesso ai dati di RateCard. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Il chiamante deve anche essere aggiunto al ruolo Lettore, Proprietario o Collaboratore per ottenere l'accesso ai dati di utilizzo per una determinata sottoscrizione di Azure.
* **Supporto delle offerte con pagamento in base al consumo, MSDN, impegno monetario e credito monetario (EA non supportato)** : questa API fornisce informazioni sulle tariffe a livello di offerta di Azure e a livello di sottoscrizione.  Il chiamante di questa API deve passare le informazioni sull'offerta per dettagli e tariffe delle risorse.  Dal momento che le offerte EA hanno tariffe personalizzate in base alla registrazione, al momento non è possibile fornire le tariffe EA.

## <a name="scenarios"></a>Scenari
Di seguito sono illustrati alcuni scenari resi possibili con la combinazione di API di utilizzo e API RateCard:

* **Spesa Azure durante il mese** : i clienti possono utilizzare le API di utilizzo e RateCard in combinazione per ottenere informazioni dettagliate relativamente alle spese per il cloud durante il mese, analizzando gli intervalli orari e giornalieri dell’utilizzo e le stime dei costi.
* **Configurazione degli avvisi** : clienti e partner possono configurare avvisi basati sulle risorse o avvisi monetari sul consumo cloud, grazie alle stime sul consumo e sui costi ottenute tramite l’API di utilizzo e RateCard.
* **Previsione delle fatture** : clienti e partner possono ottenere le stime sui consumi e sulle spese per il cloud e applicare algoritmi di Machine Learning per prevedere l’importo della fattura al termine del periodo di fatturazione.
* **Analisi dei costi precedente al consumo** : i clienti possono inoltre utilizzare l'API RateCard per eseguire la stima dell’importo della fattura in caso di spostamento dei carichi di lavoro su Azure, fornendo i numeri di utilizzo desiderati. Se i clienti dispongono di carichi di lavoro esistenti in altri cloud o nei cloud privati, possono inoltre eseguire il mapping dell'utilizzo con le tariffe di Azure per ottenere una stima più accurata della spesa stimata di Azure. In tal modo viene fornita una migliore visione di ciò che è possibile ottenere tramite lo [strumento di calcolo dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/), in quanto (ad esempio) i nostri partner di fatturazione forniscono la capacità di partire da un’offerta ed eseguire un confronto e dimostrare le differenze tra diversi tipi di offerta oltre pagamento in base al consumo, tra cui l’impegno monetario e il credito monetario. Le API forniscono inoltre la possibilità di effettuare modifiche alla stima dei costi in base all’area, abilitando il tipo di analisi di simulazione per prendere decisioni di distribuzione, in quanto la distribuzione di risorse in controller di dominio differenti nel mondo può avere un impatto diretto sul costo totale.
* **Analisi di simulazione** -
  
  * Clienti e partner possono determinare se è più conveniente eseguire i propri carichi di lavoro in un'altra area o in un'altra configurazione della risorsa di Azure. I costi delle risorse di Azure possono differire in base all'area di Azure in cui sono in esecuzione e ciò consente a clienti e partner di ottenere ottimizzazioni dei costi.
  * Clienti e partner possono inoltre determinare se un altro tipo di offerta di Azure offre una tariffa migliore per una risorsa di Azure.

## <a name="partner-solutions"></a>Soluzioni partner
Nell'articolo [L'API di utilizzo e l'API RateCard di Microsoft Azure Usage consentono a Cloudyn di fornire ITFM ai clienti](billing-usage-rate-card-partner-solution-cloudyn.md) viene descritta l'esperienza di integrazione offerta dal partner API di fatturazione di Azure [Cloudyn](https://www.cloudyn.com/microsoft-azure/).  In questo articolo viene fornita una descrizione dettagliata di queste esperienze, incluso un breve video in cui viene mostrato come un cliente di Azure può utilizzare Cloudyn e le API di fatturazione di Azure per ottenere approfondimenti utili sui dati consumo di Azure.

In [Cloud Cruiser e integrazione delle API di fatturazione di Microsoft Azure](billing-usage-rate-card-partner-solution-cloudcruiser.md) viene descritto come la [versione Express di Cloud Cruiser per Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) funzioni direttamente dal portale WAP, consentendo ai clienti di gestire senza problemi gli aspetti operativi e finanziari del cloud privato o pubblico di Microsoft Azure da una singola interfaccia utente.   

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni più dettagliate su entrambe le API, che sono parte del set di API fornito da Gestione risorse di Azure, vedere il [riferimento all’API REST di fatturazione di Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) .
* Se si desidera approfondire il codice di esempio, vedere gli esempi di codice dell'API di fatturazione di Microsoft Azure in [Esempi di codice di Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Altre informazioni
* Per ulteriori informazioni su Gestione risorse di Azure, vedere l'articolo [Panoramica su Gestione risorse di Azure](azure-resource-manager/resource-group-overview.md) .
* Per altre informazioni sulla suite di strumenti necessari per conoscere la spesa relativa al cloud, fare riferimento all'articolo di Gartner sulla [Guida di mercato agli strumenti ITFM](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).




<!--HONumber=Dec16_HO2-->


