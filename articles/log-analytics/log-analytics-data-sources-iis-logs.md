<properties
   pageTitle="Log di IIS in Log Analytics | Microsoft Azure"
   description="Internet Information Services (IIS) archivia le attività dell'utente in file log che possono essere raccolti da Log Analytics. Questo articolo descrive come configurare una raccolta di log di IIS e i dettagli dei record creati nel repository OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="bwren" />

# Log di IIS in Log Analytics
Internet Information Services (IIS) archivia le attività dell'utente in file log che possono essere raccolti da Log Analytics.

![Log di IIS](media/log-analytics-data-sources-iis-logs/overview.png)

## Configurazione dei log di IIS
Log Analytics raccoglie le voci dai file log creati da IIS, quindi è necessario [Configurare la registrazione in IIS](https://technet.microsoft.com/library/hh831775.aspx) e selezionare i campi che Log Analytics dovrà raccogliere. IIS non registra tutti i campi per impostazione predefinita ed è possibile selezionare manualmente campi aggiuntivi oltre a quelli predefiniti.

Log Analytics supporta solo i file log IIS archiviati in formato W3C. Non raccoglie invece i log in formato nativo NCSA o IIS.

Configurare i log di IIS in Log Analytics dal [menu Dati nelle impostazioni di Log Analytics](log-analytics-data-sources.md/configuring-data-sources). Non occorre selezionare nessuna impostazione oltre a **Raccogli i file di log IIS in formato W3C**.

Quando è abilitata la raccolta di log IIS, si consiglia di configurare l'impostazione di rollover dei log IIS su ciascun server.


## Raccolta dei dati

Log Analytics raccoglie le voci dei log di IIS da ogni origine connessa a intervalli di circa 15 minuti. L'agente registra la propria posizione in ogni registro eventi da cui esegue la raccolta. Se l'agente risulta offline per un certo periodo di tempo, Log Analytics raccoglie gli eventi dal momento in cui è stato interrotto, anche se gli eventi sono stati creati mentre l'agente era offline.


## Proprietà dei record del log di IIS

I record dei log di IIS sono di tipo **W3CIISLog**; nella tabella seguente vengono riportate le loro proprietà.

| Proprietà | Descrizione |
|:--|:--|
| Computer | Nome del computer da cui è stato raccolto l'evento. |
| cIP | Indirizzo IP del client. |
| csMethod | Metodo della richiesta, ad esempio GET o POST. |
| csReferer | Sito dal quale l'utente è arrivato al sito corrente tramite un collegamento. |
| csUserAgent | Tipo di browser del client. |
| csUserName | Nome dell'utente autenticato che ha eseguito l'accesso al server. Gli utenti anonimi sono indicati da un trattino. |
| csUriStem | Destinazione della richiesta, ad esempio una pagina Web. |
| csUriQuery | Eventuale query che il client ha tentato di eseguire. |
| ManagementGroupName | Nome del gruppo di gestione per gli agenti SCOM. Per gli altri agenti corrisponde a AOI-<ID area di lavoro> |
| RemoteIPCountry | Paese dell'indirizzo IP del client. |
| RemoteIPLatitude | Latitudine dell'indirizzo IP del client. |
| RemoteIPLongitude | Longitudine dell'indirizzo IP del client. |
| scStatus | Codice stato HTTP. |
| scSubStatus | Codice errore dello stato secondario. |
| scWin32Status | Codice di stato Windows. |
| sIP | Indirizzo IP del server Web. |
| SourceSystem | OpsMgr |
| sPort | Porta del server a cui è connesso il client. |
| sSiteName | Nome del sito IIS. |
| TimeGenerated | Data e ora che in cui la voce è stata registrata. |
| TimeTaken | Tempo impiegato per l'elaborazione della richiesta in millisecondi. |

## Ricerche nei log con i log di IIS

La tabella seguente mostra alcuni esempi di query nei log che recuperano i record dei log di IIS.

| Query | Descrizione |
|:--|:--|
| Tipo=IISLog | Tutti i record del log di IIS. |
| Tipo=IISLog EventLevelName=error | Tutti gli eventi di Windows con livello di gravità dell'errore. |
| Tipo=W3CIISLog | Measure count() by cIP | Numero di voci del log di IIS in base all'indirizzo IP del client. |
| Tipo=W3CIISLog csHost="www.contoso.com" | Measure count() by csUriStem | Numero di voci del log di IIS per ULR per l'host www.contoso.com. |
| Tipo=W3CIISLog | Measure Sum(csBytes) by Computer | top 500000| Numero totale di byte ricevuti da ogni computer che esegue IIS. |

## Passaggi successivi

- Configurare Log Analytics per raccogliere altre [origini dati](log-analytics-data-sources.md) per l'analisi.
- Informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.
- Configurare gli avvisi di Log Analytics per notificare in modo proattivo le condizioni importanti riscontrate nei log di IIS.

<!---HONumber=AcomDC_0518_2016-->