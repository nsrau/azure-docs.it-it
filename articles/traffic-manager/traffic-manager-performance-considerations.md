<properties
   pageTitle="Considerazioni sulle prestazioni per Gestione traffico di Azure | Microsoft Azure"
   description="Informazioni sulle prestazioni in Gestione traffico e su come testare le prestazioni in un sito Web quando si usa Gestione traffico"
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
   ms.date="06/17/2015"
   ms.author="joaoma" />


# Considerazioni sulle prestazioni per Gestione traffico


Un quesito frequente relativo a Gestione traffico di Azure riguarda i potenziali problemi di prestazioni che potrebbe causare. Le domande che vengono poste in genere sono del tipo "Quanta latenza verrà aggiunta da Gestione traffico al sito Web?", "Il sito di monitoraggio indica che il sito Web si è rallentato per alcune ore ieri. Si sono verificati problemi con Gestione traffico in tale periodo?", "Dove si trovano i server di Gestione traffico? Voglio essere certo che si trovino nello stesso data center del mio sito Web per evitare rallentamenti delle prestazioni".

Questa pagina illustrerà l'impatto diretto sulle prestazioni causato da Gestione traffico in un sito Web. Se si dispone di un sito Web negli Stati Uniti orientali e uno in Asia e il sito Web degli Stati Uniti orientali non supera i probe di Gestione traffico, tutti gli utenti verranno indirizzati al sito Web dell'Asia con un impatto sulle prestazioni. Tale impatto però non è imputabile a Gestione traffico.

  

## Nota importante sul funzionamento di Gestione traffico

http://msdn.microsoft.com/library/windowsazure/hh744833.aspx è un'eccellente risorsa per ottenere informazioni su come funziona Gestione traffico, ma la pagina contiene numerose informazioni e può essere difficile individuare i dati chiave relativi alle prestazioni. I punti importanti da esaminare nella documentazione MSDN sono i passaggi 5 e 6 della figura 3, che vengono illustrati più in dettaglio qui di seguito:

- Gestione traffico essenzialmente esegue solo un'azione, ovvero la risoluzione DNS. Questo significa che l'unico impatto sulle prestazioni prodotto da Gestione traffico in un sito Web è la ricerca DNS iniziale.
- Segue una spiegazione della ricerca DNS di Gestione traffico. Gestione traffico inserisce dati e aggiorna regolarmente i server radice DNS Microsoft normali in base ai criteri e ai risultati dei probe. Anche durante la ricerca DNS iniziale non esiste quindi alcun coinvolgimento da parte di Gestione traffico, poiché la richiesta DNS viene gestita dai normali server radice DNS Microsoft. In caso di inattività di Gestione traffico, ad esempio per un errore nelle macchine virtuali che eseguono il probe dei criteri e l'aggiornamento DNS, non si verificherà alcun impatto sul nome DNS di Gestione traffico poiché queste voci nei server DNS Microsoft continueranno a essere mantenute. Come unico effetto non verranno eseguiti il probe e l'aggiornamento in base ai criteri, ovvero se il sito primario diventa inattivo, Gestione traffico non sarà in grado di aggiornare il DNS in modo che punti al sito di failover.
- Il traffico non attraversa Gestione traffico. Non esistono server di Gestione traffico che agiscono come intermediari tra i client e il servizio ospitato in Azure. Al termine della ricerca DNS, Gestione traffico viene rimosso completamente dalle comunicazioni tra client e server.
- La ricerca DNS è molto rapida e viene memorizzata nella cache. La ricerca DNS iniziale dipenderà dal client e dai server DNS configurati. Un client in genere può eseguire una ricerca DNS in ~50 ms (vedere http://www.solvedns.com/dns-comparison/). Al termine della prima ricerca, i risultati vengono memorizzati nella cache per la durata TTL DNS, che per impostazione predefinita è impostata su 300 secondi per Gestione traffico.
- I criteri di Gestione traffico scelti (prestazioni, failover, round robin) non producono alcun effetto sulle prestazioni DNS. I criteri di prestazioni possono influire negativamente sull'esperienza utente, ad esempio qualora si inviino utenti USA a un servizio ospitato in Asia, ma questo problema di prestazioni non è causato da Gestione traffico.

  

## Test delle prestazioni di Gestione traffico

Sono disponibili pubblicamente alcuni siti Web per la determinazione delle prestazioni e del comportamento di Gestione traffico. Questi siti sono utili per determinare la latenza DNS e i servizi ospitati a cui vengono reindirizzati gli utenti in tutto il mondo. Tenere presente che la maggior parte di questi strumenti non memorizza nella cache i risultati DNS, pertanto eseguendo più volte i test verrà mostrata la ricerca DNS completa, mentre i client che si connettono all'endpoint di Gestione traffico visualizzeranno esclusivamente l'impatto prodotto una sola volta sulle prestazioni della ricerca DNS completa per la durata TTL.


## Strumenti di esempio per la misurazione delle prestazioni


Uno degli strumenti più semplici è WebSitePulse. Immettere l'URL e verranno visualizzate le statistiche, ad esempio i tempi di risoluzione DNS, i tempi per il primo byte e per l'ultimo byte e altre statistiche sulle prestazioni. È possibile scegliere tra tre posizioni diverse da cui testare il sito. In questo esempio si osserverà che la prima esecuzione mostra che la prima ricerca DNS impiega 0,204 secondi. La seconda volta in cui viene eseguito il test sullo stesso endpoint di Gestione traffico la ricerca DNS impiega 0,002 secondi poiché i risultati sono già memorizzati nella cache.

http://www.websitepulse.com/help/tools.php


![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

Tempi della ricerca DNS con risultati già memorizzati nella cache:


![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)



Un altro strumento particolarmente utile per ottenere i tempi di risoluzione DNS da più aree geografiche simultaneamente è lo strumento Check Website di Watchmouse. Immettere l'URL e verranno visualizzati i tempi di risoluzione DNS, i tempi di connessione e la velocità da diverse aree geografiche. Questo si rivela utile anche per testare i criteri di prestazioni di Gestione traffico e controllare a quali servizi ospitati vengono reindirizzati i diversi utenti di tutto il mondo.

http://www.watchmouse.com/en/checkit.php


![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

http://tools.pingdom.com/ - Testerà un sito Web e fornirà statistiche sulle prestazioni per ogni elemento della pagina in un grafico. Se si passa alla scheda Page Analysis è possibile visualizzare la percentuale di tempo impiegato per la ricerca DNS.

 

http://www.whatsmydns.net/ - Questo sito eseguirà una ricerca DNS da 20 aree geografiche diverse e visualizzerà i risultati su una mappa. Questa è una rappresentazione visiva eccezionale per determinare il servizio ospitato a cui si connetteranno i client.

 

http://www.digwebinterface.com - Analogo al sito Watchmouse, ma con informazioni più dettagliate sul DNS, inclusi i record A e CNAME. Assicurarsi di selezionare "Colorize output" e "Stats" in Options e di selezionare "All" in Nameservers.

## Conclusioni

Sulla base delle informazioni precedenti, l'unico impatto sulle prestazioni prodotto da Gestione traffico in un sito Web si verifica con la prima ricerca DNS (i tempi variano, ma la media è di ~50 ms). Successivamente l'impatto sulle prestazioni è pari a 0 per la durata TTL DNS (300 secondi per impostazione predefinita). Si verifica infine un aggiornamento della cache DNS dopo la scadenza della durata TTL. La risposta alla domanda "Quanta latenza verrà aggiunta da Gestione traffico al sito Web?" pertanto è essenzialmente zero.


## Passaggi successivi


[Informazioni sui metodi di bilanciamento del carico di Gestione traffico](traffic-manager-load-balancing-methods.md)

[Panoramica di Gestione traffico](../traffic-manmager-overview.md)

[Servizi cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operazioni per Gestione traffico (informazioni di riferimento API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlet di Gestione traffico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=July15_HO2-->