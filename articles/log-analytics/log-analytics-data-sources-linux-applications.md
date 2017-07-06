---
title: Raccogliere prestazioni delle applicazioni Linux in Log Analytics di OMS | Microsoft Docs
description: Questo articolo fornisce informazioni dettagliate per configurare l&quot;agente OMS per Linux in modo che raccolga i contatori delle prestazioni per il server HTTP Apache e MySQL.
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 04ea6f728e59ec8b47e54fe45e1adc6cbbfb85ff
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---
# <a name="collect-performance-counters-for-linux-applications-in-log-analytics"></a>Raccogliere i contatori delle prestazioni per applicazioni Linux in Log Analytics 
Questo articolo fornisce informazioni dettagliate per configurare l'[agente OMS per Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) in modo che raccolga i contatori delle prestazioni per applicazioni specifiche.  Le applicazioni incluse in questo articolo sono:  

- [MySQL](#MySQL)
- [Server HTTP Apache](#apache-http-server)

## <a name="mysql"></a>MySQL
Se viene rilevato un server MySQL o MariaDB nel computer in cui è installato l'agente OMS, viene automaticamente installato un provider di monitoraggio delle prestazioni per il server MySQL. Questo provider si connette al server MySQL/MariaDB locale per esporre le statistiche relative alle prestazioni. Le credenziali utente di MySQL devono essere configurate in modo che il provider possa accedere al server MySQL.

### <a name="configure-mysql-credentials"></a>Configurare le credenziali di MySQL
Il provider OMI MySQL richiede un utente di MySQL preconfigurato e librerie client MySQL installate, in modo da poter eseguire query sulle prestazioni o sulle informazioni di integrità dall'istanza di MySQL.  Queste credenziali vengono salvate in un file di autenticazione archiviato nell'agente Linux.  Il file di autenticazione specifica il valore bind-address e la porta su cui è in ascolto l'istanza di MySQL e le credenziali da usare per raccogliere metriche.  

Durante l'installazione dell'agente OMS per Linux, il provider OMI MySQL analizzerà i file di configurazione my.cnf di MySQL (percorsi predefiniti) per cercare il valore bind-address e la porta e imposterà in modo parziale il file di configurazione di OMI MySQL.

Il file di autenticazione di MySQL viene archiviato in `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Formato del file di autenticazione
Di seguito è riportato il formato per il file di autenticazione di OMI MySQL

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

Nella tabella seguente sono descritte le voci presenti nel file di autenticazione.

| Proprietà | Descrizione |
|:--|:--|
| Porta | Rappresenta la porta corrente su cui è in ascolto l'istanza di MySQL. La porta 0 indica che per l'istanza predefinita vengono usate le proprietà seguenti. |
| Bind-address| Valore bind-address corrente di MySQL. |
| username| Utente di MySQL usato per monitorare l'istanza del server MySQL. |
| Password con codifica Base64| Password dell'utente di monitoraggio di MySQL con codifica Base64. |
| AutoUpdate| Specifica se ripetere la ricerca di modifiche nel file my.cnf e se sovrascrivere il file di autenticazione di OMI MySQL quando viene aggiornato il provider OMI MySQL. |

### <a name="default-instance"></a>Istanza predefinita
Il file di autenticazione di OMI MySQL può definire un numero di porta e un'istanza predefinita per semplificare la gestione di più istanze di MySQL in un unico host Linux.  L'istanza predefinita è costituita dall'istanza con la porta 0. Tutte le istanze aggiuntive ereditano le proprietà impostate dall'istanza predefinita, a meno che non vengano specificati valori diversi. Se, ad esempio, viene aggiunta l'istanza di MySQL in ascolto sulla porta "3308", il valore bind-address, il nome utente e la password con codifica Base64 dell'istanza predefinita verranno usati per provare a monitorare l'istanza in ascolto sulla porta 3308. Se l'istanza sulla porta 3308 è associata a un altro indirizzo e usa la stessa coppia nome utente e password di MySQL, sarà necessario specificare solo il valore bind-address e le altre proprietà verranno ereditate.

Nella tabella seguente sono riportate impostazioni di un'istanza di esempio 

| Descrizione | File |
|:--|:--|
| Istanza predefinita e istanza con porta 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Istanza predefinita e istanza con porta 3308 e nome e password diversi. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Programma del file di autenticazione OMI MySQL
Con l'installazione del provider OMI MySQL viene fornito un programma per il file di autenticazione di OMI MySQL che consente di modificare il file di autenticazione di OMI MySQL. Il programma del file di autenticazione si trova nel percorso seguente.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> Il file delle credenziali deve essere leggibile dall'account omsagent. È consigliabile eseguire il comando mycimprovauth come omsgent.

La tabella seguente fornisce informazioni dettagliate sulla sintassi per l'utilizzo di mycimprovauth.

| Operazione | Esempio | Descrizione
|:--|:--|:--|
| autoupdate *false\|true* | mycimprovauth autoupdate false | Specifica se il file di autenticazione verrà aggiornato automaticamente in caso di riavvio o aggiornamento. |
| default *bind-address nome utente password* | mycimprovauth default 127.0.0.1 root pwd | Imposta l'istanza predefinita nel file di autenticazione di OMI MySQL.<br>Nel campo della password deve essere immesso un valore in testo normale: la password nel file di autenticazione di OMI MySQL verrà codificata in Base 64. |
| delete *predefinita\|num_port* | mycimprovauth 3308 | Elimina l'istanza specificata per impostazione predefinita o in base al numero di porta. |
| help | mycimprov help | Stampa un elenco di comandi da usare. |
| print | mycimprov print | Stampa un file di autenticazione di OMI MySQL di facile lettura. |
| update port_num *bind-address nome utente password* | mycimprov update 3307 127.0.0.1 root pwd | Aggiorna l'istanza specificata o aggiunge l'istanza, se non esiste. |

I comandi di esempio seguenti definiscono un account utente predefinito per il server MySQL su localhost.  Nel campo della password deve essere immesso un valore in testo normale: la password nel file di autenticazione OMI MySQL verrà codificata in Base 64.

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Autorizzazioni del database necessarie per i contatori delle prestazioni di MySQL
L'utente di MySQL deve poter accedere alle query seguenti per raccogliere dati sulle prestazioni del server MySQL. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


L'utente di MySQL deve anche avere l'accesso di tipo SELECT alle tabelle predefinite seguenti.

- information_schema
- mysql. 

Questi privilegi possono essere concessi mediante l'esecuzione dei comandi grant seguenti.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Per concedere autorizzazioni a un utente di monitoraggio di MySQL, l'utente che le concede deve avere il privilegio 'GRANT option', oltre al privilegio da concedere.

### <a name="define-performance-counters"></a>Definire i contatori delle prestazioni

Dopo aver configurato l'agente OMS per Linux per inviare dati a Log Analytics, è necessario configurare i contatori delle prestazioni da raccogliere.  Usare la procedura descritta in [Origini dati per le prestazioni di Windows e Linux in Log Analytics](log-analytics-data-sources-windows-events.md) con i contatori presenti nella tabella seguente.

| Nome oggetto | Nome contatore |
|:--|:--|
| MySQL Database | Disk Space in Bytes |
| MySQL Database | Tabelle |
| Server MySQL | Aborted Connection Pct |
| Server MySQL | Connection Use Pct |
| Server MySQL | Disk Space Use in Bytes |
| Server MySQL | Full Table Scan Pct |
| Server MySQL | InnoDB Buffer Pool Hit Pct |
| Server MySQL | InnoDB Buffer Pool Use Pct |
| Server MySQL | InnoDB Buffer Pool Use Pct |
| Server MySQL | Key Cache Hit Pct |
| Server MySQL | Key Cache Use Pct |
| Server MySQL | Key Cache Write Pct |
| Server MySQL | Query Cache Hit Pct |
| Server MySQL | Query Cache Prunes Pct |
| Server MySQL | Query Cache Use Pct |
| Server MySQL | Table Cache Hit Pct |
| Server MySQL | Table Cache Use Pct |
| Server MySQL | Table Lock Contention Pct |

## <a name="apache-http-server"></a>Server HTTP Apache 
Se viene rilevato un server HTTP Apache nel computer in cui è installata l'aggregazione omsagent, viene automaticamente installato un provider di monitoraggio delle prestazioni per il server HTTP Apache. Questo provider si basa su un modulo Apache che deve essere caricato nel server HTTP Apache per poter accedere ai dati sulle prestazioni. È possibile caricare il modulo con il comando seguente:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Per scaricare il modulo di monitoraggio Apache, eseguire il comando seguente:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definire i contatori delle prestazioni

Dopo aver configurato l'agente OMS per Linux per inviare dati a Log Analytics, è necessario configurare i contatori delle prestazioni da raccogliere.  Usare la procedura descritta in [Origini dati per le prestazioni di Windows e Linux in Log Analytics](log-analytics-data-sources-windows-events.md) con i contatori presenti nella tabella seguente.

| Nome oggetto | Nome contatore |
|:--|:--|
| Apache HTTP Server | Busy Workers |
| Apache HTTP Server | Idle Workers |
| Apache HTTP Server | Pct Busy Workers |
| Apache HTTP Server | Total Pct CPU |
| Apache Virtual Host | Errors per Minute - Client |
| Apache Virtual Host | Errors per Minute - Server |
| Apache Virtual Host | KB per Request |
| Apache Virtual Host | Requests KB per Second |
| Apache Virtual Host | Requests per Second |



## <a name="next-steps"></a>Passaggi successivi
* [Raccogliere i contatori delle prestazioni](log-analytics-data-sources-performance-counters.md) da agenti Linux.
* Informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni. 
