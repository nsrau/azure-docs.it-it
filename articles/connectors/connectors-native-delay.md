<properties
	pageTitle="Aggiungere ritardo nelle app per la logica | Microsoft Azure"
	description="Panoramica delle azioni di ritardo e di ritardo fino a e di come usarle con un'app per la logica di Azure."
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

# Introduzione alle azioni di ritardo e di ritardo fino a

Le azioni di ritardo e di ritardo fino a consentono di completare scenari di flusso di lavoro come

- Attendere fino a un determinato giorno della settimana per inviare un aggiornamento di stato tramite posta elettronica
- Ritardare il flusso di lavoro fino a quando una chiamata HTTP viene completata prima di riprendere e recuperare i risultati

Per iniziare a usare un'azione di ritardo in un'app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Usare le azioni di ritardo

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](connectors-overview.md).

Di seguito è riportata una sequenza di esempio di come usare un passaggio di ritardo in un'app per la logica:

1. Dopo aver aggiunto un trigger, fare clic su **nuovo passaggio** per aggiungere un'azione
1. Cercare "delay" (ritardo) per visualizzare le azioni di ritardo. In questo esempio si selezionerà **Delay** (Ritardo)

	![Azioni di ritardo](./media/connectors-native-delay/using-action-1.png)

1. Completare tutte le proprietà dell'azione per configurare il ritardo

	![Configurazione del ritardo](./media/connectors-native-delay/using-action-2.png)

1. Fare clic su "Salva" per pubblicare e attivare l'app per la logica

---

## Dettagli tecnici

Di seguito sono riportate informazioni dettagliate sulle azioni di ritardo e di ritardo fino a.

### Informazioni dettagliate sulle azioni

Il trigger di ricorrenza ha le proprietà seguenti che possono essere configurate.

#### Delay

Ritarda l'esecuzione per un determinato intervallo di tempo. L'asterisco (*) indica che il campo è obbligatorio.

|Nome visualizzato|Nome proprietà|Descrizione|
|---|---|---|
|Conteggio*|count|Il numero di unità di tempo di ritardo|
|Unità*|unit|L'unità di tempo - `Second`, `Minute`, `Hour` o `Day`|
<br>

#### Delay Until

Ritarda l'esecuzione fino a una data e ora specificate. L'asterisco (*) indica che il campo è obbligatorio.

|Nome visualizzato|Nome proprietà|Descrizione|
|---|---|---|
|Anno*|timestamp|L'anno di termine del ritardo (GMT)|
|Mese*|timestamp|Il mese di termine del ritardo (GMT)|
|Giorno*|timestamp|Il giorno di termine del ritardo (GMT)|
<br>


## Passaggi successivi

Di seguito sono riportate informazioni dettagliate su come procedere con le app per la logica e la nostra community.

## Creare un'app per la logica

Provare ora a usare la piattaforma e [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). È possibile esplorare gli altri connettori disponibili nelle app per la logica esaminando il nostro [elenco di API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->