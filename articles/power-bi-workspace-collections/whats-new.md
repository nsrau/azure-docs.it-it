---
title: "Novità delle raccolte di aree di lavoro di Power BI"
description: "Consente di ottenere le informazioni più recenti sulle raccolta di aree di lavoro di Power BI"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: a2faf610ca50acdb54353ade7c7a4ecabd314347
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>Novità delle raccolte di aree di lavoro di Power BI

Gli aggiornamenti sulle **raccolte di aree di lavoro di Power BI** vengono rilasciati a intervalli regolari. Non tutte le nuove versioni includono tuttavia nuove funzionalità destinate all'utente, in quanto alcune sono incentrate sulle funzionalità del servizio back-end. In questa pagina vengono evidenziate nuove funzionalità destinate all'utente.

> [!IMPORTANT]
> Le raccolte di aree di lavoro di Power BI sono deprecate e sono disponibili fino a giugno 2018 o fino alla data specificata nel contratto. È consigliabile pianificare la migrazione a Power BI Embedded per evitare interruzioni nell'applicazione. Per informazioni su come eseguire la migrazione dei dati a Power BI Embedded, vedere [Come eseguire la migrazione del contenuto delle raccolte di aree di lavoro di Power BI a Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="march-2017"></a>Marzo 2017

**Funzionalità self-service**

* [Creare un nuovo report](create-report-from-dataset.md)
* [Salvataggio con nome di un report](save-reports.md)
* Incorporare report in modalità lettura/modifica/creazione 
* [Passare dalla modalità di modifica alla modalità di lettura dei report e viceversa](toggle-mode.md)

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

* [Advanced Analytics with Power BI Workspace Collections and R](https://powerbi.microsoft.com/blog/r-in-pbie/) (Analisi avanzata con le raccolte di aree di lavoro di Power BI e R)

## <a name="august-31-2016"></a>31 agosto 2016
Contenuti di questa versione:

* Tutti i nuovi SDK JavaScript che supportano [navigazione tra le pagine e filtri avanzati](interact-with-reports.md).
* Le raccolte di aree di lavoro di Power BI sono ora supportate nel data center Canada centrale. Controllare lo [stato del datacenter](https://azure.microsoft.com/status/).

## <a name="july-11-2016"></a>11 luglio 2016
Contenuti di questa versione:

* **Novità** Il servizio delle raccolte di aree di lavoro di Power BI non è più disponibile in anteprima: è ora disponibile a livello generale.  
* Tutte le API REST sono state spostate da **/beta** a **/v1.0**.
* Gli SDK per .NET e JavaScript sono stati aggiornati per la versione **v1.0**.
* Le chiamate all'API di Power BI possono essere ora autenticate direttamente mediante le chiavi API. I token dell'app sono necessari solo per l'incorporamento. I token di provisioning e di sviluppo sono stati quindi deprecati nelle API della versione 1.0, ma continueranno a funzionare nella versione beta fino al 30 dicembre 2016. Per altre informazioni, vedere [Autenticazione e autorizzazione con le raccolte di aree di lavoro di Power BI](app-token-flow.md).
* Supporto della sicurezza a livello di riga per token dell'app e report incorporati. Per altre informazioni, vedere [Sicurezza a livello di riga con le raccolte di aree di lavoro di Power BI](row-level-security.md).
* Applicazione di esempio aggiornata per tutte le chiamate all'API **v1.0** .
* Supporto delle raccolte di aree di lavoro di Power BI per Azure SDK, PowerShell e l'interfaccia della riga di comando.
* Gli utenti possono esportare i dati di visualizzazione in un file con estensione **csv**.
* Le raccolte di aree di lavoro di Power BI sono ora supportate in tutte le lingue e in tutte le impostazioni locali di Microsoft Azure. Per altre informazioni, vedere [Microsoft Azure - Extent of Localization](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx)(Microsoft Azure: estensione della localizzazione).

