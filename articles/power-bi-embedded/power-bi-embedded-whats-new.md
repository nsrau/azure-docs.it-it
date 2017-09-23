---
title: "Novità di Power BI Embedded"
description: "Informazioni più recenti sulle novità di Power BI Embedded"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
ms.translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 07c53a5d6b1881a4c207a2aefed9fcede0fa069e
ms.contentlocale: it-it
ms.lasthandoff: 03/14/2017

---
# <a name="whats-new-in-power-bi-embedded"></a>Novità di Power BI Embedded

Gli aggiornamenti su **Power BI Embedded** vengono rilasciati a intervalli regolari. Non tutte le nuove versioni includono tuttavia nuove funzionalità destinate all'utente, in quanto alcune sono incentrate sulle funzionalità del servizio back-end. In questa pagina vengono evidenziate nuove funzionalità destinate all'utente. È consigliabile controllarla spesso.

## <a name="march-2017"></a>Marzo 2017

<iframe width="640" height="360" src="https://www.youtube.com/embed/ibuN4DzCl5c?showinfo=0" frameborder="0" allowfullscreen></iframe>

**Funzionalità self-service**

* [Creare un nuovo report](power-bi-embedded-create-report-from-dataset.md)
* [Salvataggio con nome di un report](power-bi-embedded-save-reports.md)
* Incorporare report in modalità lettura/modifica/creazione 
* [Passare dalla modalità di modifica alla modalità di lettura dei report e viceversa](power-bi-embedded-toggle-mode.md)

**Connettività dei dati con API REST**

* [Creare un set di dati](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* Effettuare il push dei dati 

**API di gestione**

* Clonare report e set di dati
* Associare un report a un diverso set di dati

**Esempi**

* [Esempio dell'incorporamento di report con JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo) aggiornato

## <a name="december-2016"></a>Dicembre 2016

* [New JavaScript embed sample](https://microsoft.github.io/PowerBI-JavaScript/demo/) (Nuovo esempio per incorporare JavaScript)

## <a name="october-2016"></a>Ottobre 2016

* [Advanced Analytics with Power BI Embedded and R](https://powerbi.microsoft.com/blog/r-in-pbie/) (Analisi avanzata con Power BI Embedded e R)

## <a name="august-31st-2016"></a>31 agosto 2016
Contenuti di questa versione:

* Tutti i nuovi SDK JavaScript che supportano [navigazione tra le pagine e filtri avanzati](power-bi-embedded-interact-with-reports.md).
* Power BI Embedded è ora supportato nel data center del Canada Centrale. Controllare lo [stato del datacenter](https://azure.microsoft.com/status/).

## <a name="july-11th-2016"></a>11 luglio 2016
Contenuti di questa versione:

* **Novità** Il servizio Power BI Embedded non è più disponibile in anteprima: è ora disponibile a livello generale.  
* Tutte le API REST sono state spostate da **/beta** a **/v1.0**.
* Gli SDK per .NET e JavaScript sono stati aggiornati per la versione **v1.0**.
* Le chiamate all'API di Power BI possono essere ora autenticate direttamente mediante le chiavi API. I token dell'app sono necessari solo per l'incorporamento. I token di provisioning e di sviluppo sono stati quindi deprecati nelle API della versione 1.0 , ma continueranno a funzionare nella versione Beta fino al 30/12/2016. Per altre informazioni, vedere [Informazioni sul flusso dei token delle app in Power BI Embedded](power-bi-embedded-app-token-flow.md).
* Supporto della sicurezza a livello di riga per token dell'app e report incorporati. Per altre informazioni, vedere [Row level security with Power BI Embedded](power-bi-embedded-rls.md)(Sicurezza a livello di riga con Power BI Embedded).
* Applicazione di esempio aggiornata per tutte le chiamate all'API **v1.0** .
* Supporto di Power BI Embedded per Azure SDK, PowerShell e interfaccia della riga di comando.
* Gli utenti possono esportare i dati di visualizzazione in un file con estensione **csv**.
* Power BI Embedded è ora supportato in tutte le lingue/impostazioni locali di Microsoft Azure. Per altre informazioni, vedere [Microsoft Azure - Extent of Localization](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx)(Microsoft Azure: estensione della localizzazione).


