<properties
	pageTitle="Panoramica dell'API di esportazione di Mobile Engagement"
	description="Questo documento illustra le nozioni di base sull'esportazione dei dati non elaborati generati da dispositivi dell'utente per l'uso in strumenti personalizzati."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="kpiteira"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile"
	ms.date="04/26/2016"
	ms.author="kpiteira"/>

# Panoramica dell'API di esportazione di Mobile Engagement

## Introduzione

In questo documento verranno illustrate le nozioni di base sull'esportazione dei dati non elaborati generati da dispositivi dell'utente per l'uso in strumenti personalizzati.

## Prerequisiti

Per esportare i dati non elaborati da Mobile Engagement, è necessario:

- Impostare l'autenticazione delle API in modo da poterle usare. Vedere [l'installazione manuale per l'autenticazione](mobile-engagement-api-authentication-manual.md),
- Usare le API REST o [.NET SDK](mobile-engagement-dotnet-sdk-service-api.md),
- Un account dell'Archiviazione di Azure.

>[AZURE.NOTE] È anche consigliabile avvalersi di [Esplora archivi di Microsoft Azure](http://storageexplorer.com/), almeno durante la fase di sviluppo in quanto offre un'interfaccia utente facile da usare per interagire con Archiviazione di Azure.

## Cosa può essere esportato

Mobile Engagement consente agli utenti di raccogliere molti tipi di dati e pertanto offre diversi tipi di esportazione appropriati per questi tipi di dati diversi. Esistono due tipi fondamentali di esportazione:

- Snapshot: usata in genere per esportare i dati che rappresentano uno stato e per cui Mobile Engagement non ha una cronologia. Tali dati includono tag (app-info), token o commenti e suggerimenti di campagne push. Di conseguenza questi tipi di esportazione non sono correlati a una data.
- Cronologica: questo tipo di esportazione viene usato per i dati che si accumulano nel tempo, ad esempio eventi o attività.

Nella tabella seguente vengono descritte con attenzione tutte le esportazioni possibili:

| Export Type | Tipo di dati | Descrizione |
|-------------|-----------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Snapshot | Push | Genera un'esportazione di commenti e suggerimenti di campagne push in base all'ID dispositivo o all'utente |
| Snapshot | Tag | Genera un'esportazione dei tag (app-info) associati a ogni dispositivo |
| Snapshot | Dispositivo | Genera un'esportazione della maggior parte dei dati sui dispositivi, ad esempio i dati tecnici quali modello, impostazioni locali, fuso orario e così via, i tag, la data di produzione e altri ancora. |
| Snapshot | Token | Genera un'esportazione di tutti i token validi |
| Cronologica | Attività | Genera un'esportazione di tutte le attività per ogni dispositivo in un determinato periodo di tempo |
| Cronologica | Evento | Genera un'esportazione di tutte le attività per ogni dispositivo in un determinato periodo di tempo |
| Cronologica | Job | Genera un'esportazione di tutti i processi per ogni dispositivo in un determinato periodo di tempo |
| Cronologica | Errore | Genera un'esportazione di tutti gli errori per ogni dispositivo in un determinato periodo di tempo |

## Come funziona?

Le esportazioni sono attività a esecuzione prolungata che potrebbero generare file di dati di grandi dimensioni. Per questo motivo, la loro chiamata non può restituire immediatamente un file da scaricare. Per esportare i dati da Mobile Engagement, è necessario creare un **processo di esportazione** tramite API in cui specificare:

- Il tipo di esportazione (snapshot o cronologica),
- Il tipo di dati,
- Il **contenitore dell'Archiviazione di Azure**, con inclusa una firma di accesso condiviso valida con accesso in scrittura, dove verranno scritti i risultati dell'esportazione.

Si noti che l'avvio del processo potrebbe richiedere alcuni minuti e che la sua esecuzione potrebbe richiedere da qualche secondo per le app molto piccole ad alcune ore per le app con diversi utenti e attività.

Una volta creato il processo, è possibile controllarne lo stato per vedere se è ancora in esecuzione o se è stato completato.

Una volta che il processo è stato completato correttamente, il file di dati risultante è disponibile nel contenitore di archiviazione specificato.

<!---HONumber=AcomDC_0504_2016-->