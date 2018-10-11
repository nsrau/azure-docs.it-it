---
title: Log IIS in Azure Log Analytics | Microsoft Docs
description: Internet Information Services (IIS) archivia le attività dell'utente in file log che possono essere raccolti da Log Analytics.  Questo articolo descrive come configurare una raccolta di log di IIS e i dettagli dei record creati nell'area di lavoro di Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: bwren
ms.comopnent: ''
ms.openlocfilehash: 7b44c0712c4d88ec0bbb7a94f574c2a12faf3550
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2018
ms.locfileid: "48040677"
---
# <a name="iis-logs-in-log-analytics"></a>Log di IIS in Log Analytics
Internet Information Services (IIS) archivia le attività dell'utente in file log che possono essere raccolti da Log Analytics.  

![Log di IIS](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Configurazione dei log di IIS
Log Analytics raccoglie le voci dai file log creati da IIS, quindi è necessario [configurare la registrazione in IIS](https://technet.microsoft.com/library/hh831775.aspx).

Log Analytics supporta solo i file log IIS archiviati in formato W3C e non supporta campi personalizzati o IIS Advanced Logging.  
Log Analytics non raccoglie log in formato nativo NCSA o IIS.

Configurare i log di IIS in Log Analytics dal [menu Dati in Impostazioni di Log Analytics](log-analytics-data-sources.md#configuring-data-sources).  Non occorre selezionare nessuna impostazione oltre a **Raccogli i file di log IIS in formato W3C**.


## <a name="data-collection"></a>Raccolta dei dati
Log Analytics raccoglie le voci di log IIS da ogni agente ogni volta che il log viene chiuso e ne viene creato uno nuovo. Questa frequenza è controllata dall'impostazione **Log File Rollover Schedule** (Pianificazione di rollover dei file di log) per il sito IIS che è una volta al giorno per impostazione predefinita. Ad esempio se l'impostazione è **ogni ora**, Log Analytics raccoglierà i log ogni ora.  Se l'impostazione è **ogni giorno**, Log Analytics raccoglierà i log ogni 24 ore.


## <a name="iis-log-record-properties"></a>Proprietà dei record del log di IIS
I record dei log di IIS sono di tipo **W3CIISLog**; la tabella seguente descrive le loro proprietà:

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| Computer |Nome del computer da cui è stato raccolto l'evento. |
| cIP |Indirizzo IP del client. |
| csMethod |Metodo della richiesta, ad esempio GET o POST. |
| csReferer |Sito dal quale l'utente è arrivato al sito corrente tramite un collegamento. |
| csUserAgent |Tipo di browser del client. |
| csUserName |Nome dell'utente autenticato che ha eseguito l'accesso al server. Gli utenti anonimi sono indicati da un trattino. |
| csUriStem |Destinazione della richiesta, ad esempio una pagina Web. |
| csUriQuery |Eventuale query che il client ha tentato di eseguire. |
| ManagementGroupName |Nome del gruppo di gestione per gli agenti di Operations Manager.  Per gli altri agenti, corrisponde ad AOI-\<ID area di lavoro\> |
| RemoteIPCountry |Paese dell'indirizzo IP del client. |
| RemoteIPLatitude |Latitudine dell'indirizzo IP del client. |
| RemoteIPLongitude |Longitudine dell'indirizzo IP del client. |
| scStatus |Codice stato HTTP. |
| scSubStatus |Codice errore dello stato secondario. |
| scWin32Status |Codice di stato Windows. |
| sIP |Indirizzo IP del server Web. |
| SourceSystem |OpsMgr |
| sPort |Porta del server a cui è connesso il client. |
| sSiteName |Nome del sito IIS. |
| TimeGenerated |Data e ora che in cui la voce è stata registrata. |
| TimeTaken |Tempo impiegato per l'elaborazione della richiesta in millisecondi. |

## <a name="log-searches-with-iis-logs"></a>Ricerche nei log con i log di IIS
La tabella seguente mostra alcuni esempi di query nei log che recuperano i record dei log di IIS.

| Query | DESCRIZIONE |
|:--- |:--- |
| W3CIISLog |Tutti i record del log di IIS. |
| W3CIISLog &#124; where scStatus==500 |Tutti i record del log IIS con stato restituito pari a 500. |
| W3CIISLog &#124; summarize count() by cIP |Numero di voci del log di IIS in base all'indirizzo IP del client. |
| W3CIISLog &#124; where csHost=="www.contoso.com" &#124; summarize count() by csUriStem |Numero di voci del log di IIS per ULR per l'host www.contoso.com. |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |Numero totale di byte ricevuti da ogni computer che esegue IIS. |

## <a name="next-steps"></a>Passaggi successivi
* Configurare Log Analytics per raccogliere altre [origini dati](log-analytics-data-sources.md) per l'analisi.
* Altre informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.
* Configurare gli avvisi di Log Analytics per notificare in modo proattivo le condizioni importanti riscontrate nei log di IIS.
