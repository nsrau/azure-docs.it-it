<properties 
	pageTitle="Modello di determinazione prezzi delle app per la logica | Microsoft Azure" 
	description="Dettagli sulla determinazione dei prezzi per le app per la logica" 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="07/25/2016"
	ms.author="klam"/>

# Modello di determinazione prezzi delle app per la logica

Le app per la logica supportano la scalabilità e l'esecuzione di un flusso di lavoro di integrazione nel cloud. Di seguito sono riportati i dettagli relativi ai piani tariffari e di fatturazione di App per la logica di Azure.

## Prezzi a consumo

Le nuove app per la logica create usano un piano a consumo. Con il modello di determinazione prezzi a consumo delle app per la logica, si paga solo l'uso effettivo. In caso di piano a consumo, le app per la logica non sono soggette a limitazione. Vengono misurate tutte le esecuzioni di azioni all'interno dell'esecuzione di un'istanza dell'app per la logica.

### Informazioni sulle esecuzioni di azioni

Ogni passaggio nella definizione di un'app per la logica è un'azione. Ciò include trigger, passaggi del flusso di controllo come condizioni, ambiti e cicli ForEach e Do until, chiamate a connettori e chiamate ad azioni native. I trigger sono azioni speciali progettate per creare una nuova istanza dell'app per la logica alla ricezione di un determinato evento. Diversi comportamenti dei trigger potrebbero influire sulla misurazione dell'app per la logica.

-	**Trigger di poll**: questo trigger esegue continuamente il poll di un endpoint fino a quando non riceve un messaggio che soddisfa i criteri per la creazione di una nuova istanza di un'app per la logica. L'intervallo di poll può essere configurato nel trigger nella finestra di progettazione delle app per la logica. Ogni richiesta di poll, anche se non determina la creazione di una nuova istanza di un'app per la logica, viene conteggiata come esecuzione di azione.

-	**Trigger di webhook**: questo trigger attende che un client gli invii una richiesta su un determinato endpoint. Ogni richiesta inviata all'endpoint del webhook viene conteggiata come esecuzione di azione. I trigger Richiesta e Webhook HTTP sono entrambi trigger di webhook.

-	**Trigger di ricorrenza**: questo trigger creerà una nuova istanza dell'app per la logica in base all'intervallo di ricorrenza in esso configurato. Un trigger di ricorrenza può ad esempio essere configurato per essere eseguito ogni 3 giorni o anche ogni minuto.

Le esecuzioni dei trigger vengono visualizzate nella sezione Cronologia trigger del pannello delle risorse delle app per la logica.

Tutte le azioni riuscite o non riuscite che sono state eseguite vengono misurate come esecuzione di azione. Le azioni ignorate perché non è stata soddisfatta una condizione o non eseguite perché l'app per la logica è stata terminata prima del completamento non vengono conteggiate come esecuzioni di azioni.

Le azioni eseguite all'interno di cicli vengono conteggiate per iterazione del ciclo. Una singola azione in un ciclo ForEach che scorre un elenco di 10 elementi verrà conteggiata come il numero di elementi dell'elenco (10) moltiplicato per il numero di azioni nel ciclo (1) più 1 per l'avvio del ciclo, ottenendo in questo esempio (10 * 1) + 1 = 11 esecuzioni di azioni.

Per le app per la logica disabilitate non possono essere create nuove istanze. Di conseguenza, nel periodo in cui sono disabilitate non verrà effettuato alcun addebito. Tenere presente che quando si disabilita un'app per la logica, la disattivazione delle istanze prima della disabilitazione completa potrebbe richiedere qualche minuto.

## Piani di servizio app

Per creare un'app per la logica non sono più necessari piani di servizio app. Le app per la logica create in precedenza con un piano di servizio app manterranno il comportamento precedente e verranno quindi limitate, in base al piano scelto, dopo il superamento di un determinato numero di esecuzioni. Per la fatturazione non verrà usata la misurazione dell'esecuzione di azioni.

Piani di servizio app ed esecuzioni di azioni giornaliere consentite:

| |Gratuito/Condiviso/Basic|Standard|Premium|
|---|---|---|---|
|Esecuzioni di azioni al giorno| 200|10\.000|50\.000|

Per modificare un'app per la logica a cui è associato un piano di servizio in un modello a consumo, rimuovere il riferimento al piano di servizio app nella definizione dell'app per la logica. A tale scopo è sufficiente una semplice chiamata a un cmdlet di PowerShell:

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## Prezzi

Per informazioni sui prezzi, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/).

## Passaggi successivi

- [Informazioni sulle app per la logica][whatis]
- [Creare la prima app per la logica][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0727_2016-->