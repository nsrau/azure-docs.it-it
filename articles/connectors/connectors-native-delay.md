<properties
	pageTitle="Aggiungere ritardo nelle app per la logica | Microsoft Azure"
	description="Panoramica delle azioni di ritardo e ritardo fino a e di come usarle con un'app per la logica di Azure."
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

# Introduzione alle azioni si ritardo e ritardo fino a

Le azioni di ritardo e "ritardo fino a" consentono di completare scenari di flusso di lavoro.

Ad esempio, è possibile:

- Attendere fino a un determinato giorno della settimana per inviare un aggiornamento di stato tramite posta elettronica.
- Ritardare il flusso di lavoro fino a quando una chiamata HTTP viene completata prima di riprendere e recuperare i risultati.

Per iniziare a usare un'azione di ritardo in un'app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Usare le azioni di ritardo

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](connectors-overview.md).

Di seguito è riportata una sequenza di esempio di come usare un passaggio di ritardo in un'app per la logica:

1. Dopo aver aggiunto un trigger, fare clic su **Nuovo passaggio** per aggiungere un'azione.
2. Cercare **ritardo** per visualizzare le azioni di ritardo. In questo esempio si selezionerà **Ritarda**.

	![Azioni di ritardo](./media/connectors-native-delay/using-action-1.png)  

3. Completare tutte le proprietà dell'azione per configurare il ritardo.

	![Configurazione del ritardo](./media/connectors-native-delay/using-action-2.png)  

4. Fare clic su **Salva** per pubblicare e attivare l'app per la logica.


## Informazioni dettagliate sulle azioni

Il trigger di ricorrenza ha le proprietà seguenti che possono essere configurate.

### Azione Ritarda

Questa azione ritarda l'esecuzione per un determinato intervallo di tempo. Un asterisco (*) indica che è un campo obbligatorio.

|Nome visualizzato|Nome proprietà|Descrizione|
|---|---|---|
|Conteggio*|count|Il numero di unità di tempo di ritardo|
|Unità*|unit|Unità di tempo: `Second`, `Minute`, `Hour` o `Day`|
<br>

### Azione Ritarda fino a

Questa azione ritarda l'esecuzione fino a una data e ora specificate. Un asterisco (*) indica che è un campo obbligatorio.

|Nome visualizzato|Nome proprietà|Descrizione|
|---|---|---|
|Anno*|timestamp|L'anno di termine del ritardo (GMT)|
|Mese*|timestamp|Il mese di termine del ritardo (GMT)|
|Giorno*|timestamp|Il giorno di termine del ritardo (GMT)|
<br>  


## Passaggi successivi

Provare ora a usare la piattaforma e [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). È possibile esplorare gli altri connettori disponibili nelle app per la logica esaminando l'[elenco di API](apis-list.md).

<!---HONumber=AcomDC_0810_2016-->