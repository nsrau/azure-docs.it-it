<properties 
	pageTitle="Monitorare le app per la logica in Servizio app di Azure | Microsoft Azure" 
	description="Come verificare il funzionamento delle app per la logica" 
	authors="stepsic-microsoft-com" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="stepsic"/>

# Monitorare le app per la logica

Dopo aver [creato un'app per la logica](app-service-logic-create-a-logic-app.md), è possibile visualizzare l'intera cronologia della sua esecuzione nel portale di Azure. Per visualizzare la cronologia, selezionare **Sfoglia** e quindi **App per la logica**. Verrà visualizzato l'elenco di tutte le app per la logica incluse nella propria sottoscrizione. È possibile selezionare qualsiasi app per la logica e **abilitarla** o **disabilitarla**. Se **abilitata**, i trigger eseguono l'app per la logica in risposta a eventi di attivazione; se **disabilitata**, l'app per la logica non viene eseguita in risposta ad alcun evento.

![Panoramica](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Quando viene visualizzato il pannello dell'app per la logica, sono disponibili altre due sezioni utili:

- **Riepilogo**, che mostra lo stato più recente ed è un punto di ingresso per la modifica dell'app per la logica.
- **Tutte le esecuzioni**, che mostra un elenco delle esecuzioni di questa app per la logica.

## Visualizzare le esecuzioni dell'app

![Tutte le esecuzioni](./media/app-service-logic-monitor-your-logic-apps/allruns.png)

Questo elenco mostra, per ogni esecuzione, l'**Ora di inizio**, l'**identificatore** (è possibile usarlo quando si chiama l'API REST) e la **Durata**. Selezionare una riga qualsiasi per visualizzare informazioni dettagliate relative a tale esecuzione.

Il pannello dei dettagli mostra un grafico con l'ora e la sequenza di esecuzione di tutte le azioni nell'esecuzione. Di seguito è visualizzato l'elenco completo di tutte le azioni eseguite:

![Esecuzione e azioni](./media/app-service-logic-monitor-your-logic-apps/runandaction.png)

Infine, su una particolare azione, è possibile ottenere tutti i dati passati all'azione e ricevuti dall'azione nelle sezioni **Input** e **Output**. Selezionare i collegamenti per visualizzare il contenuto completo (è anche possibile copiare i collegamenti per scaricare il contenuto).

Un'altra informazione importante è l'**ID di traccia**. Questo identificatore viene passato alle intestazioni di tutte le chiamate delle azioni. Se all'interno del proprio servizio è prevista la registrazione, è consigliabile registrare l'ID di traccia, quindi eseguire un controllo incrociato dei propri log con questo identificatore.

## Visualizzare la cronologia di attivazione 

I trigger di polling verificano l'API in base a un determinato intervallo, ma non avviano necessariamente un'esecuzione, che dipende dal tipo di risposta (ad esempio, `200` indica di avviare l'esecuzione, `202` di non avviarla). La cronologia di attivazione consente di visualizzare tutte le chiamate effettuate che, tuttavia, non eseguono l'app per la logica (le risposte `202`).

![Cronologia di attivazione](./media/app-service-logic-monitor-your-logic-apps/triggerhistory.png)

Per ogni trigger è possibile vedere se è stato **Attivato**, se non è stato attivato o se si è verificato un errore (**Non riuscito**). Per esaminare il motivo per cui un trigger non è riuscito, è possibile fare clic sul collegamento **Output**. Se è stato attivato, selezionare il collegamento **Esecuzione** per visualizzarne i dettagli.

Per i trigger *Push* *non* è possibile vedere quante volte sono state avviate le esecuzioni. Sono invece visibili le chiamate di *registrazione del callback*, ovvero le registrazioni eseguite dall'app per la logica per essere richiamata. Se il trigger di push non funziona, potrebbe trattarsi di un problema di registrazione (visibile in Output). Diversamente, potrebbe essere necessario esaminare l'API in modo specifico.

## Abilitare il controllo delle versioni

È disponibile una funzionalità aggiuntiva, non ancora attiva nell'interfaccia utente ma disponibile tramite l'[API REST](https://msdn.microsoft.com/library/azure/mt643788.aspx). Quando si aggiorna la definizione di un'app per la logica, la versione precedente della definizione viene archiviata. Se è già in corso un'esecuzione, infatti, questa continua a fare riferimento alla versione dell'app per la logica che esisteva nel momento in cui è stata avviata. Non è possibile modificare le definizioni delle esecuzioni quando sono in corso. L'API REST della cronologia delle versioni consente di accedere a queste informazioni.
 

<!---HONumber=AcomDC_0302_2016-->