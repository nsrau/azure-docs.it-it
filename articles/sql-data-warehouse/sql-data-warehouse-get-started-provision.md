<properties
   pageTitle="Introduzione: effettuare il provisioning di SQL Data Warehouse | Microsoft Azure"
   description="Eseguire il provisioning di SQL Data Warehouse attenendosi ai passaggi e alle linee guida seguenti."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/23/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Introduzione: effettuare il provisioning di SQL Data Warehouse #

Questo articolo è una guida rapida al provisioning di SQL Data Warehouse in Azure. Attenendosi a questa guida, sarà possibile eseguire le attività seguenti:

1. Creare un nuovo database di SQL Data Warehouse
2. Configurare un nuovo server logico
3. Impostare una regola del firewall di Azure per abilitare l'accesso client dall'esterno

## Versione di valutazione gratuita di Azure ##
Per completare le attività elencate di seguito, è necessario avere una sottoscrizione di Azure. Se non si ha già accesso a una sottoscrizione di Azure, risolvere prima di tutto questo passaggio.

È possibile accedere a una [versione di valutazione gratuita][] che consente di provare i servizi di Azure, incluso SQL Data Warehouse.


## Accedere al portale Azure ##

Dopo avere ottenuto una sottoscrizione, è possibile accedere al [portale di Azure][]. Procedere all'esecuzione dell'accesso.

In questa prima serie di passaggi si attiverà rapidamente un nuovo server logico e si creerà un nuovo database di SQL Data Warehouse.

## Individuare il servizio SQL Data Warehouse

La prima cosa da fare è individuare il servizio SQL Data Warehouse nel portale di Azure.

Nell'angolo in basso a sinistra del portale di Azure è disponibile il pulsante Nuovo. Questo pulsante è il punto di partenza per la creazione di qualsiasi nuovo servizio all'interno di Azure.

- Fare clic sul pulsante Nuovo.

### Dati e archiviazione

Quando si fa clic sul pulsante Nuovo vengono aperte tutte le categorie di servizi disponibili in Azure. SQL Data Warehouse si trova nella categoria "Dati e archiviazione".

- Fare clic su "Dati e archiviazione" per esaminare e visualizzare i servizi offerti da Azure per questa categoria.

### SQL Data Warehouse

Come si può vedere, Azure offre molti motori di dati e archiviazione. Tuttavia, questa Guida introduttiva è per Data Warehouse di SQL.

- Procedere e selezionare SQL Data Warehouse.

## Configurare SQL Data Warehouse

Per completare il processo di provisioning, configurare semplicemente SQL Data Warehouse.


### Nome database

La prima configurazione riguarda l'assegnazione di un nome al database.



- Per questa avvio rapido, assegnare al database il nome "MySQLDW".


> [AZURE.NOTE]Quando si crea un database personalizzato, si può naturalmente usare un nome qualsiasi, che tuttavia dovrà essere conforme ai requisiti di denominazione di Azure.

### Prestazioni

L'opzione relativa alle prestazioni è **importante**. Tramite questo dispositivo di scorrimento è possibile sfruttare le potenzialità scalabili di SQL Data Warehouse. Le prestazioni possono essere aumentate o diminuite in qualsiasi momento, non solo quando si configura il data warehouse. Spostando il dispositivo verso destra si hanno a disposizione maggiori risorse. Quando le risorse non sono più necessarie, è possibile spostare immediatamente il dispositivo di scorrimento a sinistra, risparmiando così sul costo. SQL Data Warehouse consente di modificare il profilo delle prestazioni su richiesta, senza dover ricreare il data warehouse o spostare dati.

- Usare ora il dispositivo di scorrimento per vedere come aumentano le unità data warehouse quando lo si sposta a destra e come diminuiscono quando lo si risposta a sinistra.

- Prima di completare questo passaggio, assicurarsi di riportare il dispositivo di scorrimento a sinistra. Il nuovo data warehouse è piccolo, quindi non servono molte risorse. Conservarle invece per il resto della versione di valutazione.

### Selezionare l'origine

Questa opzione consente di iniziare con un database vuoto. Scegliere il nuovo database come punto di partenza.

> [AZURE.NOTE]È disponibile anche una seconda opzione. È anche consentito creare il database da un punto di ripristino preesistente (un'opzione di ripristino).

### Server logico

Il database di SQL Data Warehouse risiede in un server logico. Il server logico assicura la coerenza della configurazione a livello di istanza per diversi database e individua il servizio in un data center di Azure.

Le opzioni da impostare sono: 1. Nome server 2. Nome amministratore del server 3. Password 4. Posizione data center 5. Autorizzazione di accesso al server per i servizi di Azure

Impostare questi valori in base alle proprie esigenze. Il nome del server deve essere univoco. È consigliabile scegliere un data center in una località vicina per ridurre la latenza di rete. SQL Data Warehouse include anche potenti funzionalità che consentono di sfruttare altri servizi di Azure. È quindi consigliabile lasciare selezionata la casella di controllo per l'accesso ai servizi di Azure.

> [AZURE.NOTE]SQL Data Warehouse deve usare un server V12. Assicurarsi che questa opzione sia impostata su SÌ. Il server logico può anche essere condiviso dai database SQL di Azure e dai database di SQL Data Warehouse. Deve tuttavia essere un server V12.

> [AZURE.NOTE]Registrare il nome del server, il nome dell'amministratore del server e la password e conservarli in un luogo sicuro. Queste informazioni saranno necessarie per connettersi al database di SQL Data Warehouse.

### Gruppo di risorse
I gruppi di risorse sono contenitori progettati per facilitare la gestione di una raccolta di risorse di Azure.

Per questa guida introduttiva lasciare configurati i valori predefiniti in Gruppo di risorse.

Per altre informazioni, vedere [gruppi di risorse](../azure-portal/resource-group-portal.md)

### Sottoscrizione
Un singolo utente può avere una o più sottoscrizioni di Azure. Se all'account di accesso sono associate più sottoscrizioni, è possibile scegliere quale usare.

Tuttavia, ai fini di questa guida, non è necessario modificare i valori predefiniti.

A questo punto è possibile creare SQL Data Warehouse.

## Creare il data warehouse ##
Per creare il data warehouse personalizzato, è sufficiente fare clic sul pulsante Crea.

Congratulazioni. La creazione del primo database di SQL Data Warehouse è così completata.

Dovrebbe essere visualizzata di nuovo la home page del [portale di Azure][]. Notare che il nuovo database di SQL Data Warehouse è stato aggiunto alla pagina.


A questo punto nessuno può accedere al database di SQL Data Warehouse. Per garantire la sicurezza, per impostazione predefinita il database non è ancora configurato per l'accesso da parte dei client.

L'ultimo passaggio del processo di provisioning consiste quindi nel configurare il servizio per l'accesso esterno.

## Configurare il firewall di Azure ##

Per configurare il firewall di Azure per la prima volta, eseguire la procedura seguente:

1. Fare clic su Sfoglia nel pannello di navigazione sinistro.

2. Scegliere SQL Server.

3. Selezionare il server SQL logico.

4. Fare clic su Impostazioni.

5. Fare clic su Firewall.

6. Impostare la regola del firewall.

    Qui è necessario eseguire due operazioni, ovvero: - Assegnare un nome alla regola del firewall. - Fornire un intervallo IP se non si ha un indirizzo IP statico.

    > [AZURE.NOTE]L'intervallo di indirizzi IP client da includere è l'indirizzo IP pubblico o esterno. Per trovare il proprio indirizzo IP esterno, è possibile usare diversi siti Web, ad esempio <a href="http://www.whatismyip.com" target="\_blank">www.whatismyip.com</a>

7. Salvare la regola del firewall.


Dopo aver configurato il firewall, si potranno stabilire connessioni dal desktop ad Azure SQL Data Warehouse appena creato.

## Passaggi successivi

Dopo aver completato il provisioning del servizio SQL Data Warehouse, è possibile procedere per imparare a usarlo.

Nei prossimi passaggi si apprenderà quindi come: 1. [Connect e query][]il data warehouse. 2. Esportare i dati dal data warehouse nella risorsa di archiviazione blob di Azure. 3. Caricare più dati nel data warehouse.


<!--Image references-->


<!-- Articles -->
[Connect e query]: sql-data-warehouse-get-started-connect-query.md

<!--External links-->
[versione di valutazione gratuita]: https://azure.microsoft.com/it-it/pricing/free-trial/
[portale di Azure]: https://portal.azure.com/

<!---HONumber=July15_HO5-->