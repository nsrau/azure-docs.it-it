<properties 
	pageTitle="Monitorare le app per la logica" 
	description="Come verificare il funzionamento delle app per la logica." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2015"
	ms.author="stepsic"/>

#Monitorare le app per la logica

Dopo aver creato un'app per la logica attenendosi alla procedura riportata nell'articolo su come  [creare una nuova app per la logica](app-service-logic-create-a-logic-app.md), è possibile visualizzare l'intera cronologia della sua esecuzione nel portale di Azure. Per visualizzare la cronologia, fare clic su **Sfoglia** nel lato sinistro della schermata del portale e selezionare **App per la logica**. Verrà visualizzato un elenco di app per la logica nella propria sottoscrizione. Un'app per la logica può essere **abilitata** o **disabilitata**. **Se abilitata**, i trigger eseguiranno l'app per la logica in risposta a eventi di attivazione; se **disabilitata** l'app per la logica non verrà eseguita in risposta agli eventi.

![Overview](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Quando viene visualizzato il pannello dell'app per la logica, sono disponibili altre due sezioni utili:

- **Riepilogo**, che indica lo stato più recente ed è un punto di ingresso per la modifica dell'app per la logica.
- **Tutte le esecuzioni**, che mostra un elenco delle esecuzioni di questa app per la logica.

##Esecuzioni

![All Runs](./media/app-service-logic-monitor-your-logic-apps/allruns.png)

Questo elenco di esecuzioni mostra l'**Ora di inizio**, l'**identificatore esecuzioni** (è possibile usarlo quando si chiama l'API REST) e la **Durata** delle particolari esecuzioni. Fare clic su una riga qualsiasi per visualizzare informazioni dettagliate relative a tale esecuzione.

Il pannello dei dettagli mostra un grafico con l'ora e la sequenza di esecuzione di tutte le azioni nell'esecuzione. Immediatamente sotto è visualizzato l'elenco completo di tutte le azioni eseguite.

![Run and Actions](./media/app-service-logic-monitor-your-logic-apps/runandaction.png)

Infine, su una particolare azione, è possibile ottenere tutti i dati passati all'azione e ricevuti dall'azione nelle sezioni **Input** e **Output**.

Un'altra informazione importante è l'**ID di traccia** Questo identificatore viene passato alle intestazioni di tutte le chiamate delle azioni. Se all'interno del proprio servizio è prevista la registrazione, è consigliabile registrare l'ID di traccia, quindi eseguire un controllo incrociato dei propri log con questo identificatore.

##Cronologia e controllo delle versioni dei trigger

Vi sono poi due ulteriori funzionalità, non ancora disponibili nell'interfaccia utente, ma disponibili tramite l'[API REST](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409).

1. **Cronologia di attivazione**: i trigger di polling verificano l'API in base a un determinato intervallo, ma non avviano necessariamente un'esecuzione, a seconda della risposta (ad esempio, `200` significa che è necessario avviare l'esecuzione, mentre `202` che non è necessario). La cronologia di attivazione consente di visualizzare tutte le chiamate eseguite, ma che non eseguono l'app per la logica (le risposte `202`).

2. **Versioni precedenti**: quando si aggiorna la definizione di un'app per la logica, la versione precedente della definizione viene archiviata. Ciò avviene perché se un'esecuzione è già in corso farà riferimento alla versione dell'app per la logica che esisteva all'avvio dell'esecuzione. Non è possibile modificare le definizioni delle esecuzioni quando sono in corso. L'API REST della cronologia delle versioni consente di accedere a queste informazioni.

<!--HONumber=49-->