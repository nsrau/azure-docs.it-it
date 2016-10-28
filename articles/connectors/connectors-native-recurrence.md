<properties
	pageTitle="Aggiungere il trigger di ricorrenza nelle app per la logica | Microsoft Azure"
	description="Panoramica dei trigger di ricorrenza e di come usarli con un'app per la logica di Azure."
	services=""
	documentationCenter=""
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>  

# Introduzione al trigger di ricorrenza

Usando il trigger di ricorrenza è possibile creare potenti flussi di lavoro nel cloud.

Ad esempio, è possibile:

- Pianificare un flusso di lavoro per eseguire una stored procedure SQL ogni giorno.
- Inviare un messaggio di posta elettronica con il riepilogo di tutti i tweet dell'ultima settimana riguardanti un determinato hashtag.

Per informazioni su come iniziare a usare un trigger di ricorrenza in un'app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Usare un trigger di ricorrenza

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](connectors-overview.md).

Ecco una sequenza di esempio di come configurare un trigger di ricorrenza in un'app per la logica:

1. Aggiungere il trigger di **ricorrenza** come primo passaggio di un'app per la logica.
2. Specificare i parametri per l'intervallo di ricorrenza.

L'app per la logica avvia ora un'esecuzione dopo ogni intervallo di tempo.

![Trigger HTTP](./media/connectors-native-recurrence/using-trigger.png)  

## Dettagli del trigger

Il trigger di ricorrenza ha le proprietà seguenti che è possibile configurare.

Attiva un'app per la logica dopo un intervallo di tempo specificato. Un asterisco (*) indica che è un campo obbligatorio.

|Nome visualizzato|Nome proprietà|Descrizione|
|---|---|---|
|Frequenza*|frequency|L'unità di tempo: `Second`, `Minute`, `Hour`, `Day` o `Year`.|
|Intervallo*|interval|Intervallo della frequenza specificata per la ricorrenza.|
|Fuso orario|timeZone|Se viene fornito un valore startTime senza una differenza dall'ora UTC, verrà usato tale valore timeZone.|
|Ora di inizio|startTime|Ora di inizio nel [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).|
<br>  


## Passaggi successivi

Provare ora a usare la piattaforma e [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). È possibile esplorare gli altri connettori disponibili nelle app per la logica esaminando l'[elenco di API](apis-list.md).

<!---HONumber=AcomDC_0810_2016-->