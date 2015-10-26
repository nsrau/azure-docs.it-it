<properties
   pageTitle="Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico"
   description="Come risolvere i problemi relativi ai profili di Gestione traffico quando risulta uno stato Danneggiato."
   services="traffic-manager"
   documentationCenter=""
   authors="kwill-MSFT"
   manager="adinah"
   editor="joaoma" />

<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="joaoma" />

# Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico
Questa pagina descriverà come risolvere i problemi relativi al profilo di Gestione traffico di Azure che mostra uno stato danneggiato e fornirà alcune indicazioni chiave sui probe di Gestione traffico.


Pochi secondi dopo aver configurato un profilo di Gestione traffico che punta ad alcuni dei servizi ospitati cloudapp.net, lo stato risulta Danneggiato.

![Stato Danneggiato](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

Accedendo alla scheda Endpoint del profilo, uno o più endpoint risulteranno con stato Offline:

![Offline](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## Note importanti sui probe di Gestione traffico

- Gestione traffico considera un endpoint ONLINE solo se il probe ottiene una risposta 200 dal percorso probe.
- Un reindirizzamento 30x (o qualsiasi altra risposta diversa da 200) avrà esito negativo, anche se l'URL reindirizzato restituisce 200.

- Per i probe HTTPS, gli errori di certificati vengono ignorati.
 
- Il contenuto effettivo del percorso probe non è importante, purché venga restituito 200. Una tecnica comune se il contenuto effettivo del sito Web non restituisce 200 (ad esempio se le pagine ASP reindirizzano a una pagina di accesso ACS o a un altro URL CNAME) consiste nell'impostare il percorso su un valore simile a "/favicon.ico".
 
- La procedura consigliata prevede di impostare il percorso probe su un valore con logica sufficiente da determinare se il sito sia attivo o non attivo. Impostando il percorso su "/favicon.ico" nell'esempio precedente si testa solo se w3wp.exe risponde, ma non se il proprio sito Web è integro. Un'opzione migliore prevede di impostare un percorso su un valore simile a "/Probe.aspx" e in Probe.aspx includere logica sufficiente per determinare se il sito è integro, ad esempio verificare i contatori di prestazioni per assicurarsi che la CPU non sia già usata al 100% o venga ricevuto un numero elevato di richieste non riuscite, tentare di accedere a risorse come lo stato del database o della sessione per assicurarsi che la logica dell'applicazione funzioni correttamente e così via.
 
- Se tutti gli endpoint di un profilo sono danneggiati, Gestione traffico li gestirà come integri e instraderà il traffico a tutti gli endpoint. In questo modo si evita che eventuali problemi con il meccanismo di probe che individuano erroneamente probe con errori possano comportare un'interruzione completa del servizio.

  

## Risoluzione dei problemi

Uno strumento per la risoluzione dei problemi di probe di Gestione traffico è wget. È possibile ottenere il pacchetto di file binari e dipendenze da [wget](http://gnuwin32.sourceforge.net/packages/wget.htm). Si noti che è possibile utilizzare altri programmi, ad esempio Fiddler o curl anziché wget: fondamentalmente è sufficiente uno strumento in grado di visualizzare la risposta HTTP non elaborata.

Dopo aver installato wget, aprire una finestra del prompt dei comandi ed eseguire wget nell'URL + porta e percorso probe configurato in Gestione traffico. Per questo esempio sarà http://watestsdp2008r2.cloudapp.net:80/Probe.

![Risoluzione dei problemi](./media/traffic-manager-troubleshooting-degraded/traffic-manager-troubleshooting.png)

Uso di wget:

![wget](./media/traffic-manager-troubleshooting-degraded/traffic-manager-wget.png)

 

Si noti che wget indica che l'URL ha restituito un reindirizzamento 301 a http://watestsdp2008r2.cloudapp.net/Default.aspx. Come appreso nella sezione "Note importanti sui probe di Gestione traffico", un reindirizzamento 30x viene considerato un errore dai probe di Gestione traffico e determinerà la segnalazione dello stato del probe come Offline. A questo punto è sufficiente controllare la configurazione del sito Web e assicurarsi che venga restituito 200 dal percorso /Probe oppure riconfigurare il probe di Gestione traffico in modo che punti a un percorso che restituirà 200.

 

Se il probe usa il protocollo HTTPS, sarà necessario aggiungere il parametro "--no-check-certificate" a wget in modo che ignori la mancanza di corrispondenza dei certificati nell'URL cloudapp.net.


## Passaggi successivi


[Informazioni sui metodi di routing di Gestione traffico](traffic-manager-load-balancing-methods.md)

[Gestione traffico di Azure](../traffic-manmager-overview.md)

[Servizi cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operazioni per Gestione traffico (informazioni di riferimento API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlet di Gestione traffico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=Oct15_HO3-->