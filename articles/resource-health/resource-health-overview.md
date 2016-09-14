<properties
   pageTitle="Panoramica su Integrità risorse di Azure | Microsoft Azure"
   description="Panoramica su Integrità risorse di Azure"
   services="Resource health"
   documentationCenter="dev-center-name"
   authors="BernardoAMunoz"
   manager=""
   editor=""/>

<tags
   ms.service="resource-health"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Supportability"
   ms.date="06/01/2016"
   ms.author="BernardoAMunoz"/>

# Panoramica su Integrità risorse di Azure

Integrità risorse di Azure è un nuovo servizio che espone l'integrità delle singole risorse di Azure e offre consigli pratici per risolvere i problemi. In un ambiente cloud in cui non è possibile accedere direttamente ai server o agli elementi dell'infrastruttura, l'obiettivo di Integrità risorse è quello di ridurre il tempo speso dai clienti per risolvere i problemi, in particolare quello per determinare se la causa del problema è interna all'applicazione o dovuta a un evento nella piattaforma Azure.

## Qual è la definizione di "risorsa" e in che modo Integrità risorse stabilisce se una risorsa è integra o meno? 
Una risorsa è un'istanza creata dall'utente di un tipo di risorsa messo a disposizione da un servizio, ad esempio una macchina virtuale, un'app Web o un database SQL.

Integrità risorse si basa su segnali emessi dalla risorsa e/o dal servizio per determinare se una risorsa è integra o meno. È importante notare che Integrità risorse considera attualmente solo l'integrità di un tipo di risorsa specifico e non altri elementi che possono contribuire all'integrità complessiva. Quando ad esempio si segnala lo stato di una macchina virtuale, viene considerata solo la parte di calcolo dell'infrastruttura, ovvero i problemi nella rete non saranno visualizzati in Integrità risorse, a meno che non si verifichi un'interruzione di servizio dichiarata. Quest'ultimo evento sarà segnalato con il banner nella parte superiore del pannello. Altre informazioni sull'interruzione di servizio sono illustrate più avanti in questo articolo.

## In che modo Integrità risorse differisce dal dashboard per l'integrità dei servizi?

Le informazioni messe a disposizione da Integrità risorse sono più granulari di quelle messe a disposizione dal dashboard per l'integrità dei servizi. Mentre il Dashboard per l'integrità dei servizi comunica gli eventi che influiscono sulla disponibilità di un servizio in un'area, Integrità risorse espone informazioni relative a una risorsa specifica, ad esempio espone gli eventi che influiscono sulla disponibilità di una macchina virtuale, un'app web o un database SQL. Se ad esempio un nodo si riavvia in modo imprevisto, i clienti le cui macchine virtuali sono in esecuzione in tale nodo potranno sapere per quale motivo la propria macchina virtuale non è stata disponibile per un periodo di tempo.

## Come accedere a Integrità risorse
Sono disponibili 2 modalità per accedere a Integrità risorse per i servizi messi a disposizione.

### Portale di Azure
Il pannello Integrità risorsa nel portale di Azure offre informazioni dettagliate sull'integrità della risorsa e le azioni consigliate, che variano a seconda dell'integrità corrente della risorsa. Questo pannello offre un'esperienza ottimale quando si eseguono query in Integrità risorse perché facilita l'accesso ad altre risorse all'interno del portale. Come accennato in precedenza, il set di azioni consigliate nel pannello Integrità risorsa variano in base allo stato di integrità corrente:

* Risorse integre: poiché non è stato rilevato alcun problema che possa influire sull'integrità della risorsa, le azioni sono incentrate sul processo di risoluzione dei problemi. È ad esempio possibile accedere al pannello Risoluzione dei problemi, che offre indicazioni su come risolvere i problemi più comunemente riscontrati dai clienti.
* Risorsa non integra: per i problemi causati da Azure, nel pannello verranno visualizzate le azioni intraprese da Microsoft per recuperare la risorsa. Per i problemi causati da azioni eseguite dagli utenti, il pannello elencherà una serie di interventi che i clienti possono intraprendere per risolvere il problema e recuperare la risorsa.

Dopo aver effettuato l'accesso al portale di Azure, esistono due modi per accedere al pannello Integrità risorsa:

###Aprire il pannello della risorsa
Aprire il pannello per una determinata risorsa. Nel pannello Impostazioni visualizzato accanto al pannello della risorsa, fare clic su Integrità risorsa per aprire il pannello Integrità risorsa.

![Pannello Integrità risorsa](./media/resource-health-overview/resourceBladeAndResourceHealth.png)

### Pannello Guida e supporto
Aprire il pannello Guida e supporto facendo clic sul punto interrogativo nell'angolo superiore destro e quindi selezionando Guida e supporto.

**Dalla barra di spostamento in alto**

![Guida e supporto tecnico](./media/resource-health-overview/HelpAndSupport.png)

Facendo clic sul riquadro verrà visualizzato il pannello della sottoscrizione di Integrità risorse che elenca tutte le risorse presenti nella sottoscrizione. Accanto a ogni risorsa è presente un'icona che ne indica l'integrità. Facendo clic su ogni risorsa verrà aperto il pannello Integrità risorsa.

**Riquadro Integrità risorsa**

![Riquadro Integrità risorsa](./media/resource-health-overview/resourceHealthTile.png)

## Cosa significa lo stato di integrità delle risorse?
Esistono 4 possibili stati di integrità per la risorsa.

### Disponibile
Il servizio non ha rilevato problemi nella piattaforma che potrebbero incidere sulla disponibilità della risorsa. Questo stato è indicato da un segno di spunta verde.

![La risorsa è disponibile](./media/resource-health-overview/Available.png)

### Non disponibile

In questo caso, il servizio ha rilevato un problema nella piattaforma che riduce la disponibilità della risorsa, ad esempio il nodo in cui è in esecuzione la macchina virtuale si è riavviato in modo imprevisto. Questo stato è indicato da un'icona di avviso rossa. Altre informazioni sul problema vengono visualizzate nella sezione centrale del pannello, tra cui:

1.	Azioni intraprese da Microsoft per recuperare la risorsa
2.	Sequenza temporale dettagliata del problema, incluso il tempo di risoluzione previsto
3.	Elenco di azioni consigliate agli utenti

![La risorsa non è disponibile](./media/resource-health-overview/Unavailable.png)

### Non disponibile a causa di azione del cliente
La risorsa non è disponibile a causa di una richiesta del cliente, ad esempio l'arresto di una risorsa o un riavvio. Questo stato è indicato da un'icona informativa blu.

![La risorsa non è disponibile a causa di un'azione avviata dall'utente](./media/resource-health-overview/userInitiated.png)

### Sconosciuto
Il servizio non ha ricevuto informazioni sulla risorsa per più di 5 minuti. Questo stato è indicato da un punto interrogativo grigio.

È importante notare che non si tratta di un'indicazione definitiva della presenza di problemi con una risorsa, quindi i clienti devono seguire queste raccomandazioni:

* Se la risorsa è in esecuzione come previsto, ma l'integrità è impostata su Sconosciuta in Integrità risorsa, non sono presenti problemi ed è probabile che lo stato della risorsa torni integro dopo alcuni minuti.
* Se sono presenti problemi di accesso alla risorsa e la sua integrità è impostata su Sconosciuta in Integrità risorsa, potrebbe essere un'indicazione iniziale di un possibile problema e sono necessarie ulteriori analisi fino a quando lo stato non verrà aggiornato in Integro o Danneggiato

![L'integrità della risorsa è sconosciuta](./media/resource-health-overview/unknown.png)

## Eventi che hanno un impatto sui servizi
Se la risorsa può essere interessata da un evento che ha un impatto sui servizi, verrà visualizzato un banner nella parte superiore del pannello Integrità di risorsa. Facendo clic sul banner verrà aperto il pannello Eventi di controllo con altre informazioni sull'interruzione del servizio.

![L'integrità della risorsa può essere interessata da un evento che ha un impatto sui servizi](./media/resource-health-overview/serviceImpactingEvent.png)

## Altre informazioni importanti su Integrità risorse

### Latenza dei segnali
I segnali che indicano l'integrità della risorsa possono presentare ritardi fino a 15 minuti e ciò può causare discrepanze tra lo stato di integrità corrente della risorsa e la sua disponibilità effettiva. Si tratta di un fattore da tenere a mente per evitare di impiegare inutilmente del tempo per analizzare eventuali problemi.

### Caso speciale per SQL 
Integrità risorsa segnala lo stato del database SQL, non di SQL Server. Anche se questo approccio offre un quadro più realistico dell'integrità, è necessario prendere in considerazione più componenti e servizi per determinare l'integrità del database. Il segnale corrente si basa sugli accessi al database e ciò significa che per i database che ricevono regolarmente accessi, incluse le richieste di esecuzione di query, l'integrità verrà visualizzata regolarmente. Se non è stato effettuato l'accesso al database per un periodo di 10 minuti o oltre, verrà impostato lo stato Sconosciuta. Ciò non significa che il database non sia disponibile, ma solo che non è stato generato alcun segnale perché non sono stati eseguiti accessi. Con la connessione al database e l'esecuzione di una query verranno inviati i segnali necessari per determinare e aggiornare lo stato di integrità del database.

## Commenti e suggerimenti
Commenti e suggerimenti sono sempre graditi. È possibile inviare [suggerimenti](https://feedback.azure.com/forums/266794-support-feedback). È possibile inoltre comunicare tramite [Twitter](https://twitter.com/azuresupport) o i [forum MSDN](https://social.msdn.microsoft.com/Forums/azure).

<!---HONumber=AcomDC_0831_2016-->