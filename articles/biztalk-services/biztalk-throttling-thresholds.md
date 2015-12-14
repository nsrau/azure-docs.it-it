<properties 
	pageTitle="Informazioni sulla limitazione in Servizi BizTalk | Microsoft Azure" 
	description="Informazioni sulle soglie di limitazione delle richieste e sui relativi comportamenti di runtime per Servizi BizTalk. La limitazione delle richieste è basata sull'utilizzo della memoria e sul numero di messaggi. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="mandia"/>





# Servizi BizTalk: limitazione

Servizi BizTalk di Azure implementa la limitazione del servizio sulla base di due condizioni: l'utilizzo della memoria e il numero di messaggi simultanei elaborati. In questo argomento sono elencate le soglie di limitazione e viene descritto il comportamento in fase di runtime quando si verifica una condizione di limitazione.

## Soglie di limitazione

Nella tabella seguente sono elencate le origini e le soglie di limitazione:

|| Descrizione | Threshold bassa \\ Threshold elevata| |---|---|---|---| | Memoria| % del sistema della memoria totale disponibile/byte file della pagina. <p><p>Totale byte file di pagina disponibile corrisponde a circa 2 volte la quantità di RAM del sistema. | 60% |70% | | Messaggi elaborati| Numero di messaggi elaborati simultaneamente|40 * numero di memorie centrali|100 * numero di memorie centrali|

Quando viene raggiunta una soglia superiore, Servizi BizTalk di Azure inizia la limitazione. La limitazione viene interrotta quando viene raggiunta la soglia inferiore. Se ad esempio il servizio utilizza il 65% della memoria di sistema, non viene applicata la limitazione. Se invece il servizio inizia a utilizzare il 70% della memoria di sistema, viene applicata la limitazione, che continua fino a quando il servizio non utilizzerà il 60% (soglia inferiore) della memoria di sistema.

Servizi BizTalk di Azure registra lo stato di limitazione (normale o limitato) e la durata della limitazione.


## Comportamento in fase di esecuzione

Quando Servizi BizTalk di Azure entra nello stato di limitazione, si verifica quanto segue:

- La limitazione viene applicata a ogni istanza del ruolo, Ad esempio:<br/> IstanzaRuoloA è limitata. IstanzaRuoloB non è limitata. In questa situazione, i messaggi in IstanzaRuoloB vengono elaborati come previsto. I messaggi in IstanzaRuoloA vengono rimossi e non vengono eseguiti con l'errore seguente:<br/><br/> **Server occupato. Riprova più tardi.**<br/><br/>
- Nessuna origine di pull esegue il polling o scarica un messaggio. Ad esempio:<br/> una pipeline effettua il pull dei messaggi da un'origine FTP esterna. L'istanza del ruolo che effettua il pull entra in stato limitato. In questa situazione, la pipeline interrompe il download di altri messaggi fino a quando l'istanza del ruolo non interrompe la limitazione.
- Al client viene inviata una risposta in modo che possa inviare di nuovo il messaggio.
- È necessario attendere che la limitazione sia risolta. In particolare, occorre attendere che venga raggiunta la soglia inferiore.

## Note importanti
- Non è possibile disabilitare la limitazione.
- Non è possibile modificare le soglie di limitazione.
- La limitazione viene implementata nell'intero sistema.
- Anche il server di database SQL di Azure ha la funzionalità di limitazione incorporata.

## Argomenti aggiuntivi su Servizi BizTalk di Azure

-  [Installare Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Servizi BizTalk: Esercitazioni ed esempi](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Come iniziare a usare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## Vedere anche
- [Servizi BizTalk: Grafico edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Servizi BizTalk: effettuare il provisioning di un servizio BizTalk mediante il portale di Azure classico](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Servizi BizTalk: Tabella degli stati del servizio](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Servizi BizTalk: Schede Dashboard, Monitoraggio, Scalabilità](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servizi BizTalk: backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servizi BizTalk: nome e chiave dell'autorità emittente](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
 

<!---HONumber=AcomDC_1203_2015-->