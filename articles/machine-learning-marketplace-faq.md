<properties 
	pageTitle="Domande frequenti sulla pubblicazione e sull'utilizzo delle app di Machine Learning in Azure Marketplace | Azure" 
	description="Domande frequenti" 
	services="machine-learning" 
	documentationCenter="" 
	authors="luisca" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="luisca"/> 

#Domande frequenti sulla pubblicazione e sull'utilizzo delle app di Machine Learning in Azure Marketplace

##Utilizzo da Marketplace


###Domanda 1: Dopo l'immissione dell'input per il servizio Web viene visualizzato l'errore seguente. Perché?
La richiesta ha avuto come risultato un timeout di back-end o un errore di back-end. Il team sta esaminando questo problema. Ci scusiamo per l'inconveniente. (500)
Risposta: È possibile che i parametri di input immessi non siano conformi al formato richiesto per il servizio Web specifico. Fare riferimento al collegamento seguente relativo alla documentazione per individuare il formato corretto per i parametri di input e per informazioni sulle limitazioni di questo servizio Web.

###Domanda 2: Se si copia il collegamento all'API per il servizio Web disponibile in 'Esplorare il set di dati' e lo si incolla in un'altra finestra o scheda del browser, quali credenziali è necessario usare per accedere ai risultati e come vengono visualizzati?
Risposta: È consigliabile usare l'account Marketplace come nome utente e la chiave dell'account principale come password. La chiave dell'account principale è disponibile nella pagina 'Esplorare il set di dati', sotto la descrizione del servizio Web (fare clic sul pulsante 'Mostra'). È possibile che il risultato venga visualizzato nel browser o che sia disponibile per il download, a seconda del browser usato.

###Domanda 3: Dopo l'immissione dell'input per il servizio Web in 'Esplorare il set di dati' viene visualizzato l'errore seguente. Perché?
Si è verificato un errore imprevisto durante l'elaborazione della richiesta. Riprova più tardi.
Risposta: È possibile che uno o più parametri di input del servizio Web abbiano superato il limite di lunghezza durante l'utilizzo del servizio Web nella pagina 'Esplorare il set di dati' del Marketplace. È possibile che i servizi vengano chiamati con una lunghezza di input superiore tramite i metodi POST HTTP. Il codice di esempio è disponibile nei servizi di esempio illustrati di seguito: http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-web-service-examples/.

###

##Pubblicazione da Azure ML nel Marketplace

###Domanda 1: Perché i logo/le immagini/il numero di transazioni non vengono aggiornati per il servizio Web? 
Risposta: I logo o le immagini nel portale di pubblicazione vengono memorizzati nella cache e potrebbero essere necessari fino a 10 giorni per l'aggiornamento del nuovo logo o della nuova immagine nel portale.

###Domanda 2: Perché nella scheda "Dettagli" del servizio Web dell'offerta del Marketplace è visualizzato un errore?
Risposta: Si tratta di un problema noto del Marketplace durante la connessione ad Azure ML per ottenere informazioni dettagliate sui servizi. Il team si sta impegnando per risolvere il problema.

###Domanda 3: Perché il codice R di esempio nei servizi Web di Azure ML non funziona per l'utilizzo dei servizi Web nel Marketplace?
Risposta: I sistemi di autenticazione presentano differenze in caso di connessione diretta ai servizi Web di Azure ML rispetto alla connessione a questi servizi Web tramite il Marketplace. I servizi disponibili in Marketplace sono servizi OData e possono essere chiamati tramite metodi GET o POST. 

###Domanda 4: Perché i collegamenti per il supporto delle offerte del servizio Web non vengono aggiornati correttamente per alcune offerte?
Risposta: I collegamenti per il supporto sono globali per le singole entità di pubblicazione e non sono specifici per le singole offerte. 

###Domanda 5: Come si pubblica un servizio Web con modalità di input batch nel Marketplace?
Risposta: La modalità di input batch non è attualmente supportata nei servizi Web del Marketplace.

###Domanda 6: A chi ci si deve rivolgere per ottenere supporto in caso di domande su come diventare un'entità di pubblicazione di dati o in caso di problemi durante la pubblicazione?
Risposta: Per altre informazioni, contattare il team del Marketplace all'indirizzo datamarketbd@microsoft.com.






<!--HONumber=46--> 
