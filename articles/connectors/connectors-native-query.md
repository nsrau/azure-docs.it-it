<properties
	pageTitle="Aggiungere l'azione di query alle app per la logica | Microsoft Azure"
	description="Panoramica dell'azione di query per l'esecuzione di azioni come matrice di filtro."
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
   ms.date="07/20/2016"
   ms.author="jehollan"/>

# Introduzione all'azione di query

Con l'azione di query è possibile usare batch e matrici in modo da eseguire flussi di lavoro per:

- Creare un'attività per tutti i record ad alta priorità di un database.
- Salvare tutti gli allegati PDF per i messaggi di posta elettronica nel BLOB di Azure.

Per iniziare a usare l'azione di query in un'app per la logica, vedere l'articolo su come [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Usare l'azione di query
	
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Ulteriori informazioni sulle azioni.](connectors-overview.md) L'azione di query ha attualmente un'operazione esposta nella finestra di progettazione: Filtra matrice. Questa consente di interrogare una matrice e restituire un set di risultati filtrati. Ecco come è possibile aggiungerla in un'app per la logica:

1. Selezionare il pulsante **Nuovo passaggio**
1. Selezionare **Aggiungi un'azione**
1. Nella casella di ricerca azione digitare "Filtro" per elencare l'azione **Filtra matrice**

	![Selezionare l'azione di query](./media/connectors-native-query/using-action-1.png)

1. Selezionare una matrice da filtrare (la schermata seguente mostra la matrice di risultati da una ricerca su Twitter)
1. Creare una condizione da valutare per ogni elemento (la schermata seguente filtra i tweet da utenti con più di 100 follower)

	![Completare l'azione di query](./media/connectors-native-query/using-action-2.png)

1. L'azione genera una nuova matrice che contiene solo i risultati che soddisfano i requisiti del filtro
1. Fare clic su Salva nell'angolo in alto a sinistra della barra degli strumenti per salvare e pubblicare (attivare) l'app per la logica

---

## Dettagli tecnici

Di seguito sono riportati i dettagli per le azioni supportate da questo connettore.

## Azioni di query

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Ulteriori informazioni sulle azioni.](connectors-overview.md) Il connettore presenta 1 azione possibile.

|Azione|Descrizione|
|---|---|
|Filtra matrice|Valutare una condizione per ogni elemento in una matrice e restituire i risultati|

### Informazioni dettagliate sulle azioni

L'azione di query supporta 1 azione possibile. Di seguito sono riportate le informazioni su ognuna delle azioni, i relativi campi di input obbligatori e facoltativi e i corrispondenti dettagli di output associati al loro uso.

#### Filtra matrice
Eseguire una richiesta HTTP in uscita. L'asterisco (*) indica che il campo è obbligatorio.

|Nome visualizzato|Nome proprietà|Descrizione|
|---|---|---|
|Da*|from|La matrice da filtrare|
|Condizione*|dove|La condizione da valutare per ogni elemento|
<br>

**Dettagli dell'output**

Risposta HTTP

|Nome proprietà|Tipo di dati|Descrizione|
|---|---|---|
|Matrice filtrata|array|Matrice che contiene un oggetto per ogni risultato filtrato|

---

## Passaggi successivi

Di seguito sono riportate informazioni dettagliate su come procedere con le app per la logica e la nostra community.

## Creare un'app per la logica

Provare ora a usare la piattaforma e [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). È possibile esplorare gli altri connettori disponibili nelle app per la logica esaminando l'[elenco di API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->