<properties
   pageTitle="Uso dell'archivio query nel database SQL di Azure"
   description="Informazioni su come usare l'archivio query nel database SQL di Azure"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="performance"
   ms.workload="data-management"
   ms.date="05/25/2016"
   ms.author="carlrab"/>

# Uso dell'archivio query nel database SQL di Azure 

L'archivio query di Azure è una funzionalità di database completamente gestita che raccoglie e presenta informazioni cronologiche dettagliate su tutte le query in modo continuo. L'archivio query può essere concepito come un registratore dei dati di volo di un aeroplano che semplifica notevolmente la risoluzione dei problemi relativi alle prestazioni delle query sia per i clienti del cloud sia per i clienti locali. Questo articolo spiega alcuni aspetti specifici dell'uso dell'archivio query in Azure. Usando questi dati di query pre-raccolti, gli utenti possono diagnosticare e risolvere velocemente i problemi di prestazioni e dedicare così più tempo alla propria attività.

Archivio query è [disponibile a livello globale](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) nel database SQL di Azure da novembre 2015. Archivio query è la base per le funzionalità di analisi delle prestazioni e ottimizzazione, come [Advisor per database SQL e il dashboard delle prestazioni](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Al momento della pubblicazione di questo articolo, l'archivio query è in esecuzione in più di 200.000 database utente in Azure e raccoglie informazioni relative alle query da molti mesi senza interruzioni.

> [AZURE.IMPORTANT] Microsoft sta per attivare archivio query per tutti i database SQL di Azure esistenti e nuovi. Questo processo di attivazione attualmente include solo database singleton ma si espanderà in futuro ai database dei pool elastici. Quando si usano pool elastici, abilitare l'archivio query solo per il sottoinsieme piccolo dei database. L'abilitazione dell'archivio query per tutti i database di un pool elastico può comportare un fabbisogno eccessivo di risorse che potrebbe indurre il sistema a non rispondere.

## Archivio query come funzionalità gestita nel database SQL di Azure

Il funzionamento di archivio query nel database SQL di Azure si basa su questi due principi fondamentali:

- Non causa alcun impatto visibile sul carico di lavoro del cliente
- Monitora continuamente le query a meno che ciò non comporti qualche impatto sul carico di lavoro del cliente

L'impatto sul carico di lavoro del cliente può avere due dimensioni:

- ***Disponibilità***: il Contratto di servizio per il database SQL non viene ridotto quando l'archivio query è in esecuzione.
- ***Prestazioni***: il sovraccarico medio introdotto dall'archivio query va tipicamente dall'1 al 2%

Il funzionamento di archivio query in Azure usa risorse limitate (CPU, memoria, disco, dimensione su disco e così via). Rispetta varie limitazioni del sistema al fine di influire minimamente sul normale carico di lavoro. In generale, l'archivio query rispetta le restrizioni relative alle risorse sotto due aspetti:

- ***Limitazioni statiche:*** limitazioni imposte dalla capacità di risorse di un dato livello di servizio (Base, Standard, Premium, Pool elastico).
- ***Limitazioni dinamiche:*** limitazioni imposte dal consumo del carico di lavoro corrente (ovvero le risorse disponibili).

Per garantire il funzionamento continuo e affidabile, il database SQL di Azure ha creato un'infrastruttura di monitoraggio permanente intorno all'archivio query che raccoglie dati operativi chiave da ogni database. Di conseguenza, vengono costantemente monitorati molti indicatori KPI tecnici per garantire l'affidabilità:

- Numero di eccezioni e rimedi automatici
- Numero di database in stato READ\_ONLY e durata dello stato READ\_ONLY
- Primi database per frequenza e durata della pulizia automatica
- Primi database per durata del caricamento dei dati in memoria (durante l'inizializzazione)
- Primi database per durata dello scaricamento dei dati su disco

Il database SQL di Azure usa i dati raccolti per:

- ***Risolvere o attenuare i problemi causati dall'archivio query:*** il database SQL di Azure può rilevare e limitare i problemi che hanno un notevole impatto sul carico di lavoro dei clienti, con bassa latenza (meno di un'ora). Nella maggior parte dei casi i problemi vengono gestiti mediante l'impostazione temporanea dell'archivio query su ***OFF***.
- ***Apprendere modelli di utilizzo su un numero elevato di database e migliorare di conseguenza la qualità e affidabilità delle funzionalità:*** l'archivio query viene migliorato con ogni aggiornamento del database SQL di Azure. 

Di tanto in tanto, gli aggiornamenti dell'archivio query introducono modifiche alle impostazioni predefinite applicate alle configurazioni interne e raramente a quelle esterne (per i clienti). Di conseguenza, l'esperienza del cliente con l'archivio query sul database SQL di Azure può differire rispetto agli ambienti locali a causa dell'azione automatica eseguita dalla piattaforma dell'archivio query:

- Lo stato dell'archivio query può essere cambiato in ***OFF*** per attenuare i problemi e riportato su ***ON*** dopo che il problema è stato risolto.
- La configurazione dell'archivio query può essere modificata per garantire un funzionamento affidabile. Questa operazione può essere eseguita come:
    - Modifiche individuali ai database finalizzate a risolvere problemi di instabilità o inaffidabilità.
    - Implementazione globale di modifiche di configurazione ottimali che avvantaggiano tutti i database che usano l'archivio query.

Cambiare l'archivio query in ***OFF*** è un'azione automatica che ha come ambito i singoli database. Si verifica in presenza di un comportamento del prodotto che influisce negativamente sui database utente per cui il meccanismo di rilevamento ha generato un avviso. Per quel particolare database, l'archivio query rimane ***OFF*** fino a quando non è disponibile una nuova versione con l'implementazione dell'archivio query migliorata. Quando viene attivato lo stato ***OFF***, il cliente viene informato tramite un messaggio di posta elettronica che gli consiglia anche di non abilitare di nuovo l'archivio query fino a quando non sarà stata distribuita una nuova versione. Dopo una nuova distribuzione, l'infrastruttura del database SQL di Azure attiva automaticamente l'archivio query per tutti i database per cui era stato impostato su ***OFF***.

Meno frequentemente il database SQL di Azure può applicare nuove impostazioni di configurazione predefinite per tutti i database utente, ottimizzate per un funzionamento affidabile e una raccolta dei dati continua.

## Configurazione ottimale dell'archivio query

Questa sezione descrive impostazioni di configurazione predefinite ottimali progettate per garantire un funzionamento affidabile di archivio query e delle funzionalità dipendenti, come [Advisor per database SQL e il dashboard delle prestazioni](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). La configurazione predefinita è ottimizzata per la raccolta di dati continua, ossia per un tempo minimo di OFF/READ\_ONLY.

| Configurazione | Descrizione | Default | Commento |
| ------------- | ----------- | ------- | ------- |
| MAX\_STORAGE\_SIZE\_MB | Specifica il limite per lo spazio dati che l'archivio query occuperà all'interno del database del cliente | 100 | Applicato per i nuovi database |
| INTERVAL\_LENGTH\_MINUTES | Definisce la dimensione dell'intervallo di tempo durante il quale le statistiche di runtime raccolte per i piani di query vengono aggregate e rese persistenti. Tutti i piani di query attivi avranno al massimo una riga per un periodo di tempo definito con questa configurazione | 60 | Applicato per i nuovi database |
| STALE\_QUERY\_THRESHOLD\_DAYS | Criterio di pulizia basato sul tempo che controlla il periodo di memorizzazione delle statistiche di runtime persistenti e delle query inattive | 30 | Applicato per i nuovi database e i database con un'impostazione predefinita precedente (367) |
| SIZE\_BASED\_CLEANUP\_MODE | Specifica se la pulizia automatica dei dati verrà eseguita quando la dimensione dati dell'archivio query si avvicina al limite | AUTO | Applicato per tutti i database |
| QUERY\_CAPTURE\_MODE | Specifica se verranno monitorate tutte le query o solo un sottoinsieme di esse | AUTO | Applicato per tutti i database |
| FLUSH\_INTERVAL\_SECONDS | Specifica il periodo massimo durante il quale le statistiche di runtime acquisite vengono mantenute in memoria prima di essere scaricate su disco | 900 | Applicato per i nuovi database |
||||||

I valori predefiniti indicati sopra verranno applicati automaticamente nella fase finale dell'attivazione dell'archivio query in tutti i database SQL di Azure. Dopo questa attivazione, il database SQL di Azure non modificherà i valori di configurazione impostati dai clienti, a meno che non abbiano un impatto negativo sul carico di lavoro primario o sull'affidabilità di funzionamento dell'archivio query.

Se si vuole mantenere le impostazioni personalizzate, usare [ALTER DATABASE con le opzioni dell'archivio query](https://msdn.microsoft.com/library/bb522682.aspx) per ripristinare la configurazione allo stato precedente. Vedere le [procedure consigliate dell'archivio query](https://msdn.microsoft.com/library/mt604821.aspx) per informazioni su come scegliere i parametri di configurazione ottimali.

## Passaggi successivi

[Informazioni dettagliate sulle prestazioni del database SQL](sql-database-performance.md)

## Altre informazioni

Per altre informazioni, vedere gli articoli dedicati ai seguenti argomenti:

- [Un registratore dei dati di volo per il database](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 

- [Scenari di utilizzo dell'archivio query](https://msdn.microsoft.com/library/mt614796.aspx)

- [Monitoraggio delle prestazioni mediante l'archivio query](https://msdn.microsoft.com/library/dn817826.aspx)

<!---HONumber=AcomDC_0525_2016-->