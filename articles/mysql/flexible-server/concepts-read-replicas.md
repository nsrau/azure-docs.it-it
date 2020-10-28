---
title: Leggere repliche-database di Azure per MySQL-server flessibile
description: 'Informazioni sulle repliche di lettura nel database di Azure per MySQL server flessibile: creazione di repliche, connessione alle repliche, monitoraggio della replica e arresto della replica in corso.'
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: 6f3482bdc608d97e4adba5f99393e74f2e6c7cde
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795222"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>Leggere le repliche nel database di Azure per MySQL-server flessibile

> [!IMPORTANT]
> Leggere repliche nel database di Azure per MySQL: il server flessibile è in anteprima.

MySQL è uno dei più diffusi motori di database per l'esecuzione di applicazioni Web e per dispositivi mobili su scala Internet. Molti dei clienti lo usano per i servizi di formazione online, i servizi di streaming video, le soluzioni di pagamento digitale, le piattaforme di e-commerce, i servizi per i giochi, i portali di notizie, i siti Web per enti pubblici e sanitari. Questi servizi devono essere usati e ridimensionati con l'aumentare del traffico nell'applicazione Web o per dispositivi mobili.

Sul lato applicazioni, l'applicazione viene in genere sviluppata in Java o php ed è stata eseguita la migrazione per l'esecuzione nei set di scalabilità di macchine virtuali di Azure o nei servizi app Azure o in contenitori per l'esecuzione in Azure Kubernetes Service (AKS). Con il set di scalabilità di macchine virtuali, il servizio app o AKS come infrastruttura sottostante, la scalabilità delle applicazioni è semplificata mediante il provisioning immediato di nuove macchine virtuali e la replica dei componenti senza stato delle applicazioni per soddisfare le richieste, ma spesso il database finisce a essere un collo di bottiglia come componente centralizzato con stato.

La funzionalità di lettura della replica consente di replicare i dati da un server di database di Azure per MySQL flessibile a un server di sola lettura. È possibile eseguire la replica dal server di origine a un massimo di **10** repliche. Le repliche vengono aggiornate in modo asincrono tramite la tecnologia di replica basata su posizione del file di registro binario nativo, o binlog, del motore MySQL. Per altre informazioni su questo tipo di replica, vedere [MySQL binlog replication overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Panoramica della replica basata su binlog di MySQL).

Le repliche sono nuovi server gestiti in modo analogo al database di Azure di origine per i server flessibili MySQL. Si incorreranno addebiti per la fatturazione per ogni replica di lettura in base al calcolo di cui è stato effettuato il provisioning in Vcore e archiviazione in GB al mese. Per ulteriori informazioni, vedere [prezzi](./concepts-compute-storage.md#pricing).

Per altre informazioni sulle funzionalità di replica di MySQL e sui relativi problemi, vedere la [documentazione sulle repliche di MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Comunicazione senza distorsione
>
> Microsoft supporta un ambiente diversificato ed inclusivo. Questo articolo contiene riferimenti alla parola _slave_ . La [guida di stile Microsoft per la comunicazione senza pregiudizi](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) la riconosce come parola di esclusione. La parola viene usata in questo articolo per coerenza perché è attualmente la parola usata nel software. Quando il software verrà aggiornato per rimuovere la parola, questo articolo verrà aggiornato di conseguenza.
>

## <a name="common-use-cases-for-read-replica"></a>Casi d'uso comuni per la replica di lettura

La funzionalità di replica in lettura aiuta a migliorare prestazioni e scalabilità dei carichi di lavoro con utilizzo elevato della lettura. I carichi di lavoro di lettura possono essere isolati con le repliche, mentre i carichi di lavoro di scrittura possono essere indirizzati all'origine.

Scenari comuni:

* Ridimensionamento dei carichi di lavoro di lettura provenienti dall'applicazione usando proxy di connessione Lightweight come [ProxySQL](https://aka.ms/ProxySQLLoadBalanceReplica) o uso del modello basato su microservizi per scalare le query di lettura provenienti dall'applicazione per leggere le repliche
* I carichi di lavoro di report analitici o BI possono usare le repliche di lettura come origine dati per la creazione di report
* Per gli scenari di produzione o di tutto, in cui le informazioni di telemetria vengono inserite nel motore di database MySQL mentre vengono usate più repliche di lettura per la creazione di report

Poiché le repliche sono di sola lettura, non riducono direttamente gli oneri di capacità di scrittura nell'origine. Questa funzionalità non è destinata a carichi di lavoro con utilizzo elevato di scrittura.

Questa funzionalità di replica in lettura si avvale della replica asincrona di MySQL. La funzionalità non è concepita per scenari di replica sincrona. Si verifica un ritardo misurabile tra l'origine e la replica. I dati nella replica diventano infine coerenti con i dati nell'origine. Usare questa funzionalità per i carichi di lavoro in grado di sostenere questo ritardo.

## <a name="create-a-replica"></a>Creare una replica

Se un server di origine non dispone di server di replica esistenti, l'origine viene innanzitutto riavviata per prepararsi per la replica.

Quando viene avviato il flusso di lavoro per la creazione della replica, viene creato un server di Database di Azure per MySQL vuoto. Il nuovo server viene compilato con i dati presenti nel server di origine. Il tempo di creazione dipende dalla quantità di dati nell'origine e dall'ora dell'ultimo backup completo settimanale. Il tempo può variare da pochi minuti a diverse ore.

> [!NOTE]
> Le repliche di lettura vengono create con la stessa configurazione del server dell'origine. La configurazione del server di replica può essere modificata dopo la creazione. Il server di replica viene sempre creato nello stesso gruppo di risorse, nello stesso percorso e nella stessa sottoscrizione del server di origine. Per creare un server di replica in un gruppo di risorse diverso o in una sottoscrizione diversa, è possibile [spostare il server di replica](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) dopo averlo creato. È consigliabile mantenere la configurazione del server di replica con valori uguali o superiori a quelli dell'origine per assicurarsi che la replica sia in grado di rimanere al passo con l'origine.

Informazioni su come [creare una replica di lettura nel portale di Azure](how-to-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Connessione a una replica

Al momento della creazione, una replica eredita il metodo di connettività del server di origine. Non è possibile modificare il metodo di connettività della replica. Se ad esempio il server di origine ha **accesso privato (integrazione VNet)** , la replica non può essere in **accesso pubblico (indirizzi IP consentiti)** .

La replica eredita l'account amministratore dal server di origine. Tutti gli account utente nel server di origine vengono replicati nelle repliche di lettura. È possibile connettersi a una replica di lettura solo utilizzando gli account utente disponibili nel server di origine.

È possibile connettersi alla replica usando il nome host e un account utente valido, come si farebbe con un normale server di database di Azure per MySQL flessibile. Per un server denominato **myreplica** con il nome utente amministratore **myadmin** è possibile connettersi alla replica usando l'interfaccia della riga di comando di mysql:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin -p
```

Quando richiesto, immettere la password per l'account dell'utente.

## <a name="monitor-replication"></a>Monitorare la replica

Il server flessibile database di Azure per MySQL offre la metrica di **ritardo della replica in secondi** in monitoraggio di Azure. Questa metrica è disponibile per solo le repliche. Questa metrica viene calcolata usando la metrica `seconds_behind_master` disponibile nel comando `SHOW SLAVE STATUS` di MySQL. Impostare un avviso per essere informati quando l'intervallo di replica raggiunge un valore non accettabile per il carico di lavoro.

Se viene visualizzato un ritardo di replica maggiore, vedere [risoluzione dei problemi relativi alla latenza di replica](./../howto-troubleshoot-replication-latency.md) per risolvere i problemi e comprendere le possibili cause.

## <a name="stop-replication"></a>Arrestare la replica

È possibile arrestare la replica tra un'origine e una replica. Dopo che la replica è stata interrotta tra un server di origine e una replica di lettura, la replica diventa un server autonomo. I dati nel server autonomo sono i dati che erano disponibili nella replica al momento dell'esecuzione del comando di arresto della replica. Il server autonomo non si aggiorna al server di origine.

Quando si sceglie di arrestare la replica in una replica, vengono persi tutti i collegamenti all'origine precedente e ad altre repliche. Non esiste un failover automatico tra un'origine e la relativa replica.

> [!IMPORTANT]
> Il server autonomo non può essere di nuovo impostato come replica.
> Prima di arrestare la replica in una replica in lettura, assicurarsi che la replica abbia tutti i dati necessari.

Informazioni su come [arrestare la replica in una replica](how-to-read-replicas-portal.md).

## <a name="failover"></a>Failover

Non esiste un failover automatico tra i server di origine e di replica. 

Le repliche di lettura sono destinate al ridimensionamento dei carichi di lavoro con utilizzo intensivo di lettura e non sono progettate per soddisfare le esigenze di disponibilità elevata di un server. Non esiste un failover automatico tra i server di origine e di replica. L'arresto della replica sulla replica di lettura per portarlo online in modalità lettura/scrittura è il modo in cui viene eseguito il failover manuale.

Poiché la replica è asincrona, esiste un ritardo tra l'origine e la replica. La quantità di ritardo può essere influenzata da diversi fattori, ad esempio la quantità di carico di lavoro in esecuzione nel server di origine e la latenza tra i Data Center. Nella maggior parte dei casi il ritardo della replica è compreso tra pochi secondi e un paio di minuti. È possibile tenere traccia dell'effettivo ritardo di replica usando l' *intervallo di replica* metrica, disponibile per ogni replica. Questa metrica indica il tempo trascorso dall'ultima transazione riprodotta. Si consiglia di identificare il ritardo medio osservando il ritardo della replica in un periodo di tempo. È possibile impostare un avviso per il ritardo di replica, in modo che se non rientra nell'intervallo previsto, è possibile intervenire.

> [!Tip]
> Se si esegue il failover alla replica, il ritardo nel momento in cui si scollega la replica dall'origine indicherà la quantità di dati persi.

Dopo aver deciso di voler eseguire il failover a una replica, 

1. Arrestare la replica nella replica<br/>
   Questo passaggio è necessario per consentire al server di replica di accettare le Scritture. Come parte di questo processo, il server di replica verrà decollegato dall'origine. Una volta avviata l'arresto della replica, il completamento del processo back-end richiede in genere circa 2 minuti. Per comprendere le implicazioni di questa azione, vedere la sezione [arrestare la replica](#stop-replication) di questo articolo.
    
2. Puntare l'applicazione alla replica (precedente)<br/>
   Ogni server dispone di una stringa di connessione univoca. Aggiornare l'applicazione in modo che punti alla replica (precedente) invece che all'origine.
    
Una volta che l'applicazione ha elaborato correttamente le operazioni di lettura e scrittura, il failover è stato completato. La quantità di tempo di inattività di cui l'applicazione dipenderà quando si rileva un problema e si completano i passaggi 1 e 2 precedenti.

## <a name="considerations-and-limitations"></a>Considerazioni e limitazioni

| Scenario | Limitazione/considerazione |
|:-|:-|
| Replica nel server con disponibilità elevata con ridondanza della zona abilitata | Non supportato |
| Prezzi | Il costo dell'esecuzione del server di replica è basato sull'area in cui è in esecuzione il server di replica |
| Riavvio del server di origine | Quando si crea una replica per un'origine senza repliche, l'origine viene innanzitutto riavviata per prepararsi per la replica. Prendere in considerazione questo aspetto ed eseguire queste operazioni durante un periodo di minore attività |
| Nuove repliche | Viene creata una replica di lettura come nuovo server flessibile di database di Azure per MySQL. Un server esistente non può essere impostato come replica. Non è possibile creare una replica di un'altra replica di lettura |
| Configurazione della replica | Una replica viene creata utilizzando la stessa configurazione del server dell'origine. Dopo la creazione di una replica, è possibile modificare diverse impostazioni indipendentemente dal server di origine: generazione di calcolo, Vcore, archiviazione e periodo di conservazione dei backup. È anche possibile modificare il livello di calcolo in modo indipendente.<br> <br> **IMPORTANTE**  <br> -Prima che una configurazione del server di origine venga aggiornata ai nuovi valori, aggiornare la configurazione della replica in valori uguali o superiori. Questa azione garantisce che le repliche siano sempre aggiornate con le modifiche apportate all'origine. <br/> Il metodo di connettività e le impostazioni dei parametri vengono ereditate dal server di origine alla replica quando viene creata la replica. Successivamente le regole della replica sono indipendenti. |
| Repliche arrestate | Se si interrompe la replica tra un server di origine e una replica di lettura, la replica arrestata diventa un server autonomo che accetta letture e scritture. Il server autonomo non può essere di nuovo impostato come replica. |
| Server di origine e autonomi eliminati | Quando viene eliminato un server di origine, la replica viene arrestata per tutte le repliche di lettura. Queste repliche diventano automaticamente server autonomi e possono accettare operazioni di lettura e scrittura. Il server di origine viene eliminato. |
| Account utente | Gli utenti nel server di origine vengono replicati nelle repliche di lettura. È possibile connettersi a una replica di lettura solo utilizzando gli account utente disponibili nel server di origine. |
| Parametri del server | Per evitare che i dati siano esclusi dalla sincronizzazione e scongiurarne potenziali perdite o danneggiamenti, alcuni parametri del server vengono bloccati dall'aggiornamento quando si usano repliche di lettura. <br> I parametri del server seguenti sono bloccati nel server di origine e di replica:<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> Il parametro [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) è bloccato nei server di replica. <br> Per aggiornare uno dei parametri indicati in precedenza nel server di origine, eliminare i server di replica, aggiornare il valore del parametro nell'origine e ricreare le repliche. |
| Altro | -La creazione di una replica di una replica non è supportata. <br> -Le tabelle in memoria potrebbero far sì che le repliche non siano sincronizzate. Si tratta di una limitazione della tecnologia di replica MySQL. Per altre informazioni, vedere la [documentazione di riferimento di MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html). <br>-Assicurarsi che le tabelle del server di origine dispongano di chiavi primarie. La mancanza di chiavi primarie può causare latenza di replica tra l'origine e le repliche.<br>-Esaminare l'elenco completo dei limiti della replica di MySQL nella [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare e gestire le repliche in lettura tramite il portale di Azure](how-to-read-replicas-portal.md)
- Leggere le informazioni su come [creare e gestire repliche in lettura tramite l'interfaccia della riga di comando di Azure](how-to-read-replicas-cli.md)
