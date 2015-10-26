<properties 
	pageTitle="Guida alla risoluzione dei problemi relativi allo streaming live" 
	description="In questo argomento sono disponibili suggerimenti per la risoluzione dei problemi relativi allo streaming live." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/29/2015"    
	ms.author="juliako"/>

#Guida alla risoluzione dei problemi relativi allo streaming live

In questo argomento sono disponibili suggerimenti per la risoluzione di alcuni problemi relativi allo streaming live.

## Problemi relativi ai codificatori locali 

In questa sezione sono disponibili suggerimenti su come risolvere i problemi relativi ai codificatori locali configurati per l'invio di un flusso a velocità in bit singola ai canali AMS abilitati per la codifica live.

###Problema: non esiste alcuna opzione per l'output di un flusso progressivo

- **Potenziale problema**: il codificatore usato non esegue automaticamente il deinterlacciamento. 

	**Passaggi per la risoluzione dei problemi**: cercare un'opzione di deinterlacciamento all'interno dell'interfaccia del codificatore. Dopo aver abilitato il deinterlacciamento, verificare di nuovo se sono disponibili impostazioni per l'output progressivo.
 
###Problema: dopo aver provato varie impostazioni di output per il codificatore è ancora impossibile connettersi. 

- **Potenziale problema**: il canale di codifica di Azure non è stato reimpostato correttamente. 

	**Passaggi per la risoluzione dei problemi**: verificare che il codificatore non effettui più il push ad AMS, arrestare e reimpostare il canale. Quando è di nuovo in esecuzione, provare a connettere il codificatore con le nuove impostazioni. Se il problema persiste, provare a creare un canale del tutto nuovo. A volte i canali possono risultare danneggiati dopo diversi tentativi non riusciti.

- **Potenziale problema**: le dimensioni GOP o le impostazioni del fotogramma chiave non sono ottimali.

	**Passaggi per la risoluzione dei problemi**: l'intervallo consigliato per le dimensioni GOP o il fotogramma chiave è di 2 secondi. Alcuni codificatori calcolano questa impostazione come numero di fotogrammi, mentre altri usano i secondi. Ad esempio, per l'output di 30fps, le dimensioni GOP sarebbero di 60 fotogrammi, equivalenti a 2 secondi.
	 
- **Potenziale problema**: le porte chiuse bloccano il flusso.

	**Passaggi per la risoluzione dei problemi**: durante lo streaming tramite RTP, controllare le impostazioni del firewall e/o del proxy per assicurarsi che le porte in uscita 1935 e 1936 siano aperte. Per lo streaming RTP, verificare che la porta in uscita 2010 sia aperta.


###Problema: quando si configura il codificatore per lo streaming con il protocollo RTP, non è disponibile alcun campo per l'immissione del nome host. 

- **Potenziale problema**: molti codificatori RTP non consentono i nomi host e sarà necessario acquisire un indirizzo IP.  

	**Passaggi per la risoluzione dei problemi**: per trovare l'indirizzo IP, aprire un prompt dei comandi in qualsiasi computer. A tale scopo, in Windows aprire l'avvio per Esegui (WIN+R) e digitare "cmd" per aprire.

	Una volta aperto il prompt dei comandi, digitare "Ping [nome Host AMS]".

	È possibile derivare il nome host omettendo il numero di porta dall'URL di inserimento di Azure, come mostrato nell'esempio seguente:

	rtp://test2-amstest009.rtp.channel.mediaservices.windows.net:2010/

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

###Problema: non è possibile riprodurre il flusso pubblicato.
 
- **Potenziale problema**: nessun endpoint di streaming in esecuzione o non esiste alcuna unità di streaming (unità di scala) allocata. 

	**Passaggi per la risoluzione dei problemi**: passare alla scheda "Endpoint streaming" nello strumento AMSE e verificare che vi sia un endpoint di streaming in esecuzione con una unità di streaming.
	
>[AZURE.NOTE]Se dopo aver seguito le procedure di risoluzione dei problemi non è ancora possibile eseguire correttamente il flusso, inviare un ticket di supporto tramite il portale di gestione di Azure.

<!---HONumber=Oct15_HO3-->