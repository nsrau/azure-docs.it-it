<properties 
   pageTitle="Test delle impostazioni di Gestione traffico"
   description="In questo articolo vengono fornite le informazioni per verificare le impostazioni di Gestione traffico"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# Test delle impostazioni di Gestione traffico

Il modo migliore per testare le impostazioni di Gestione traffico consiste nel configurare un determinato numero di client e, successivamente, nell'arrestare uno alla volta gli endpoint nel profilo, costituiti da servizi cloud e siti Web. Per assistenza nella verifica del profilo di Gestione traffico, consultare i suggerimenti riportati di seguito.

## Passaggi di test di base

- **Impostare una durata (TTL) DNS molto bassa** in modo che le modifiche si propaghino velocemente, ad esempio 30 secondi.
- **Prendere nota degli indirizzi IP dei servizi cloud e dei siti Web di Azure** nel profilo da testare.
- **Utilizzare strumenti che consentono di risolvere un nome DNS in un indirizzo IP** e visualizzare l'indirizzo. Si esegue un controllo per verificare che il nome del dominio aziendale venga risolto negli indirizzi IP degli endpoint nel profilo. La risoluzione deve essere coerente con il metodo di bilanciamento del carico del profilo di Gestione traffico. Se si sta usando un computer su cui è in esecuzione Windows, è possibile usare lo strumento Nslookup.exe da un prompt dei comandi o di Windows PowerShell. Altri strumenti disponibili pubblicamente che consentono di visualizzare i dettagli di un indirizzo IP sono disponibili su Internet.

### Per controllare il profilo di Gestione traffico con nslookup

1. Aprire un comando o prompt Windows PowerShell come amministratore.
2. Digitare `ipconfig /flushdns` per scaricare la cache del resolver DNS.
3. Digitare `nslookup <your Traffic Manager domain name>`. Ad esempio, il comando seguente consente di verificare il nome di dominio con il prefisso *myapp.contoso*: nslookup myapp.contoso.trafficmanager.net. Un tipico risultato consente di visualizzare quanto segue:
   - Nome DNS e indirizzo IP del server DNS a cui si accede per risolvere questo nome di dominio di Gestione traffico.
   - Nome del dominio di Gestione traffico digitato nella riga di comando dopo "nslookup" e indirizzo IP in cui viene risolto il dominio di Gestione traffico. Il secondo indirizzo IP è quello più importante da verificare. Deve corrispondere a un indirizzo VIP per uno dei servizi cloud o dei siti Web nel profilo di Gestione traffico in fase di verifica.

## Metodi di bilanciamento del carico di test

### Per verificare un metodo di bilanciamento del carico failover

1. Lasciare tutti gli endpoint attivi.
2. Utilizzare un singolo client.
3. Richiedere una risoluzione DNS per il nome di dominio aziendale utilizzando lo strumento Nslookup.exe o un'utilità simile.
4. Assicurarsi che l'indirizzo IP risolto ottenuto sia per l'endpoint principale
5. Arrestare l'endpoint principale o rimuovere il file di monitoraggio in modo che Gestione traffico lo consideri inattivo.
6. Attendere la durata (TTL) DNS del profilo di Gestione traffico più altri due minuti. Ad esempio, se la durata TTL del DNS è 300 secondi (5 minuti), è necessario attendere 7 minuti.
7. Scaricare la cache del client DNS e richiedere una risoluzione DNS. In Windows è possibile scaricare la cache DNS con il comando ipconfig /flushdns eseguito a un prompt dei comandi o di Windows PowerShell.
8. Assicurarsi che l'indirizzo IP ottenuto sia utilizzato dall'endpoint secondario.
9. Ripetere la procedura, arrestare l'endpoint secondario, poi il terziario e così via. Ogni volta, assicurarsi che la risoluzione DNS restituisca l'indirizzo IP dell'endpoint successivo nell'elenco. Quando tutti gli endpoint sono inattivi, si dovrebbe ottenere nuovamente l'indirizzo IP dell'endpoint primario.

### Per verificare un metodo di bilanciamento del carico round robin

1. Lasciare tutti gli endpoint attivi.
2. Utilizzare un singolo client.
3. Richiedere una risoluzione DNS per il dominio aziendale utilizzando lo strumento Nslookup.exe o un'utilità simile.
4. Verificare che l'indirizzo IP ottenuto corrisponda a uno di quelli presenti nell'elenco.
5. Scaricare la cache del client DNS e continuare a ripetere i passaggi 3 e 4. Vengono visualizzati i diversi indirizzi IP restituiti per ognuno degli endpoint. Il processo viene quindi ripetuto.

### Per verificare un metodo di bilanciamento del carico delle prestazioni

Per verificare in modo efficace il metodo di bilanciamento del carico delle prestazioni, è necessario che i client si trovino in diverse parti del mondo. È possibile creare i client in Azure che tenteranno di chiamare i servizi tramite il nome di dominio aziendale. In alternativa, se la società è globale, è possibile accedere ai client in altre parti del mondo in modalità remota ed eseguire il test da questi client.

Sono disponibili servizi di analisi approfondita e DNS basati su Web gratuiti. Alcuni di essi consentono di verificare la risoluzione del nome DNS da località diverse. Eseguire una ricerca in "Ricerca DNS" per alcuni esempi. Un'altra opzione prevede l'utilizzo di una soluzione di terze parti, come ad esempio Gomez o Keynote, per verificare se i profili distribuiscono il traffico come previsto.

## Vedere anche

[Informazioni sui metodi di bilanciamento del carico di Gestione traffico](traffic-manager-load-balancing-methods.md)

[Attività di configurazione di Gestione traffico](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Panoramica di Gestione traffico](traffic-manager-overview.md)

[Servizi cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operazioni relative a Gestione traffico (riferimento all'API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

<!---HONumber=58-->