<properties 
   pageTitle="Verifica delle impostazioni di Gestione traffico"
   description="In questo articolo vengono fornite le informazioni per verificare le impostazioni di Gestione traffico"
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# Verifica delle impostazioni di Gestione traffico

Il modo migliore per verificare le impostazioni di Gestione traffico è di impostare un numero di client e poi portare singolarmente gli endpoint, costituiti da servizi cloud e siti Web, nel profilo. I suggerimenti seguenti servono a verificare il profilo di Gestione traffico.

## Passaggi per la verifica di base

- **Impostare un valore TTL DNS molto basso** in modo che le modifiche verranno propagate velocemente - Ad esempio, 30 secondi.
- **Conoscere gli indirizzi IP dei servizi cloud e dei siti Web di Azure** nel profilo in corso di verifica.
- **Utilizzare gli strumenti che consentono di risolvere un nome DNS per un indirizzo IP** e visualizzare tale indirizzo. È necessario verificare se il nome di dominio aziendale viene risolto su indirizzi IP degli endpoint nel profilo. Dovrebbero risolversi in modo uniforme al metodo di bilanciamento del carico del profilo di Gestione traffico. Se nel computer in uso è in esecuzione Windows, è possibile utilizzare lo strumento Nslookup.exe da un comando o prompt Windows PowerShell. Altri strumenti disponibili pubblicamente che consentono di visualizzare i dettagli di un indirizzo IP sono disponibili su Internet.

### Per verificare un profilo di Gestione traffico tramite nslookup

1. Aprire un comando o prompt Windows PowerShell come amministratore.
2. Digitare `ipconfig /flushdns` per scaricare la cache del resolver DNS.
3. Digitare `nslookup <your Traffic Manager domain name>`. Ad esempio, il seguente comando verifica il nome di dominio con il prefisso *myapp.contoso*:
    nslookup myapp.contoso.trafficmanager.net
   Il risultato tipico sarà simile al seguente:
   - Accedere al nome DNS e all'indirizzo IP del server DNS per risolvere il nome di dominio di Gestione traffico.
   - Il nome di dominio di Gestione traffico digitato nella riga di comando dopo "nslookup" e l'indirizzo IP su cui viene risolto il dominio di Gestione traffico. Il secondo indirizzo IP è quello più importante da verificare. Deve coincidere con un indirizzo (VIP) IP virtuale pubblico per uno dei servizi cloud o siti Web nel profilo di Gestione traffico in corso di verifica.

## Verifica dei metodi di bilanciamento del carico

### Per verificare un metodo di bilanciamento del carico con criterio di failover

1. Lasciare tutti gli endpoint attivi.
2. Utilizzare un singolo client.
3. Richiedere una risoluzione DNS per il nome di dominio aziendale utilizzando lo strumento Nslookup.exe o un'utilità simile.
4. Assicurarsi che l'indirizzo IP risolto ottenuto sia per l'endpoint principale
5. Arrestare l'endpoint principale o rimuovere il file di monitoraggio in modo che Gestione traffico lo consideri inattivo.
6. Attendere la durata (TTL) DNS del profilo di Gestione traffico più altri due minuti. Ad esempio, se il TTL DNS è 300 secondi (5 minuti), è necessario attendere 7 minuti.
7. Scaricare la cache del client DNS e richiedere una risoluzione DNS. In Windows, è possibile scaricare la cache DNS con il comando ipconfig/flushdns emesso al prompt di comando o Windows PowerShell.
8. Assicurarsi che l'indirizzo IP ottenuto sia utilizzato dall'endpoint secondario.
9. Ripetere la procedura, arrestare l'endpoint secondario, poi il terziario e così via. Ogni volta, assicurarsi che la risoluzione DNS restituisca l'indirizzo IP dell'endpoint successivo nell'elenco. Quando tutti gli endpoint sono stati arrestati, è necessario ottenere nuovamente l'indirizzo IP dell'endpoint principale.

### Per verificare un metodo di bilanciamento del carico Round Robin

1. Lasciare tutti gli endpoint attivi.
2. Utilizzare un singolo client.
3. Richiedere una risoluzione DNS per il dominio aziendale utilizzando lo strumento Nslookup.exe o un'utilità simile.
4. Verificare che l'indirizzo IP ottenuto corrisponda a uno di quelli presenti nell'elenco.
5. Scaricare la cache del client DNS e continuare a ripetere i passaggi 3 e 4. È necessario visualizzare indirizzi IP differenti restituiti per ciascun endpoint. Quindi, il processo verrà ripetuto.

### Per verificare le prestazioni di un metodo di bilanciamento del carico

Per verificare efficacemente le prestazioni di un metodo di bilanciamento del carico, è necessario disporre di client situati in diverse aree del mondo. È possibile creare client in Azure che tenteranno di chiamare i servizi tramite nome di dominio aziendale. In alternativa, se l'azienda dispone di più sedi a livello globale, è possibile accedere ai client in altre parti del mondo in modalità remota ed eseguire i test da tali client.

Sono disponibili servizi di analisi approfondita e DNS basati su Web gratuiti. Alcuni di essi consentono di verificare la risoluzione del nome DNS da località diverse. Eseguire una ricerca in "DNS lookup" per visualizzare gli esempi. Un'altra opzione prevede l'utilizzo di una soluzione di terze parti, come ad esempio Gomez o Keynote, per verificare se i profili distribuiscono il traffico come previsto.

## Vedere anche

[Informazioni sui metodi di bilanciamento del carico di Gestione traffico](traffic-manager-load-balancing-methods.md)

[Attività di configurazione di Gestione traffico](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Panoramica di Gestione traffico](traffic-manager-overview.md)

[Servizi cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operazioni relative a Gestione traffico (riferimento all'API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)


<!--HONumber=49-->