---
title: Raccogliere e analizzare messaggi Syslog in Log Analytics di OMS | Documentazione Microsoft
description: "Syslog è un protocollo di registrazione di eventi comunemente usato in Linux. Questo articolo descrive come configurare una raccolta di messaggi Syslog in Log Analytics e illustra i dettagli dei record creati nel repository OMS."
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
ms.date: 09/28/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 061c32fe39530f8b67899b1b9e1104e7fe006380
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2017
---
# <a name="syslog-data-sources-in-log-analytics"></a>Origini dati Syslog in Log Analytics
Syslog è un protocollo di registrazione di eventi comunemente usato in Linux.  Le applicazioni inviano messaggi che possono essere archiviati nel computer locale o recapitati a un agente di raccolta di Syslog.  Quando viene installato, l'agente OMS per Linux configura il daemon Syslog locale in modo da inoltrare i messaggi all'agente.  Quest'ultimo invia quindi il messaggio a Log Analytics, dove viene creato un record corrispondente nel repository OMS.  

> [!NOTE]
> Log Analytics supporta la raccolta di messaggi inviati da rsyslog o syslog-ng, dove rsyslog rappresenta il daemon predefinito. Il daemon SysLog predefinito nella versione 5 di Red Hat Enterprise Linux, CentOS e nella versione Oracle Linux (sysklog) non è supportato per la raccolta di eventi SysLog. Per raccogliere i dati di SysLog da questa versione delle distribuzioni, è necessario installare e configurare il [daemon rsyslog](http://rsyslog.com) in modo da sostituire sysklog.
>
>

![Raccolta Syslog](media/log-analytics-data-sources-syslog/overview.png)

## <a name="configuring-syslog"></a>Configurazione di Syslog
L'agente OMS per Linux raccoglie solo gli eventi con le funzionalità e i livelli di gravità specificati nella configurazione.  È possibile configurare Syslog tramite il portale di OMS o mediante la gestione dei file di configurazione sugli agenti Linux.

### <a name="configure-syslog-in-the-oms-portal"></a>Configurare Syslog nel portale di OMS
Configurare Syslog usando il [menu Dati in Impostazioni di Log Analytics](log-analytics-data-sources.md#configuring-data-sources).  Questa configurazione viene distribuita al file di configurazione su ogni agente Linux.

È possibile aggiungere una nuova funzionalità digitando il nome corrispondente e facendo clic su **+**.  Per ogni funzionalità vengono raccolti solo i messaggi con i livelli di gravità selezionati.  Controllare i livelli di gravità relativi alla funzionalità per la quale si vuole raccogliere i dati.  Non è possibile specificare altri criteri per filtrare i messaggi.

![Configurare Syslog](media/log-analytics-data-sources-syslog/configure.png)

Per impostazione predefinita, viene eseguito automaticamente il push di tutte le modifiche di configurazione in tutti gli agenti.  Per configurare Syslog manualmente su ogni agente Linux, deselezionare la casella *Apply below configuration to my Linux machines*(Applica la configurazione seguente ai computer Linux in uso).

### <a name="configure-syslog-on-linux-agent"></a>Configurare Syslog sull'agente Linux
Durante l' [installazione dell'agente OMS in un client Linux](log-analytics-linux-agents.md), viene installato un file di configurazione syslog predefinito che definisce la funzionalità e il livello di gravità dei messaggi raccolti.  È possibile modificare questo file per cambiare la configurazione.  Il file di configurazione è diverso a seconda del daemon Syslog installato nel client.

> [!NOTE]
> Se si modifica la configurazione di SysLog, è necessario riavviare il daemon SysLog per rendere effettive le modifiche.
>
>

#### <a name="rsyslog"></a>rsyslog
Il file di configurazione per rsyslog si trova in **/etc/rsyslog.d/95-omsagent.conf**.  I contenuti predefiniti sono visualizzati di seguito.  Questo daemon raccoglie i messaggi syslog inviati dall'agente locale per tutte le funzionalità con livello di gravità avviso o superiore.

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

È possibile rimuovere una funzionalità eliminando la sezione corrispondente del file di configurazione.  È possibile limitare i livelli di gravità che vengono raccolti per una particolare funzionalità modificando la voce relativa a tale funzionalità.  Ad esempio, per limitare la funzionalità utente ai messaggi con livello di gravità errore o superiore, modificare la riga del file di configurazione nel modo seguente:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng
Il file di configurazione per syslog-ng si trova in **/etc/syslog-ng/syslog-ng.conf**.  I contenuti predefiniti sono visualizzati di seguito.  Questo daemon raccoglie i messaggi syslog inviati dall'agente locale per tutte le funzionalità e tutti i livelli di gravità.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };

    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };

    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };

    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };

    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };

    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };

    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

È possibile rimuovere una funzionalità eliminando la sezione corrispondente del file di configurazione.  È possibile limitare i livelli di gravità che vengono raccolti per una particolare funzionalità rimuovendoli dall'elenco.  Per limitare la funzionalità utente ai messaggi di avviso e critici, modificare la sezione del file di configurazione nel modo seguente:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Raccolta dei dati da altre porte Syslog
L'agente OMS rimane in ascolto dei messaggi Syslog nel client locale sulla porta 25224.  Quando l'agente viene installato, viene applicata una configurazione di SysLog predefinita, disponibile nella posizione seguente:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

È possibile modificare il numero di porta creando due file di configurazione: un file config FluentD e un file rsyslog-or-syslog-ng a seconda del daemon Syslog che è stato installato.  

* Il file config FluentD deve essere un nuovo file in: `/etc/opt/microsoft/omsagent/conf/omsagent.d`. Sostituire il valore della **porta** con il numero di porta personalizzato.

        <source>
          type syslog
          port %SYSLOG_PORT%
          bind 127.0.0.1
          protocol_type udp
          tag oms.syslog
        </source>
        <filter oms.syslog.**>
          type filter_syslog
        </filter>

* Per rsyslog, creare un nuovo file di configurazione in: `/etc/rsyslog.d/`. Sostituire il valore %SYSLOG_PORT% con il numero di porta personalizzato.  

    > [!NOTE]
    > Se questo valore viene modificato nel file di configurazione `95-omsagent.conf`, verrà sovrascritto quando l'agente applicherà una configurazione predefinita.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* Modificare la configurazione syslog-ng copiando la configurazione di esempio illustrata di seguito e aggiungendo le impostazioni modificate personalizzate alla fine del file di configurazione syslog-ng.conf in `/etc/syslog-ng/`.  **Non** usare l'etichetta predefinita **%WORKSPACE_ID%_oms** o **%WORKSPACE_ID_OMS**. Definire un'etichetta personalizzata per distinguere le modifiche.  

    > [!NOTE]
    > Se questi valori vengono modificati nel file di configurazione, verranno sovrascritti quando l'agente applicherà una configurazione predefinita.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Dopo aver completato le modifiche, riavviare Syslog e il servizio agente OMS per assicurarsi che le modifiche apportate alla configurazione abbiano effetto.   

## <a name="syslog-record-properties"></a>Proprietà dei record Syslog
I record Syslog sono di tipo **Syslog** e hanno le proprietà descritte nella tabella seguente.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| Computer |Computer da cui è stato raccolto l'evento. |
| Facility |Parte del sistema che ha generato il messaggio. |
| HostIP |Indirizzo IP del sistema che ha inviato il messaggio. |
| HostName |Nome del sistema che ha inviato il messaggio. |
| SeverityLevel |Livello di gravità dell'evento. |
| SyslogMessage |Testo del messaggio. |
| ProcessID |ID del processo che ha generato il messaggio. |
| EventTime |Data e ora in cui è stato generato l'evento. |

## <a name="log-queries-with-syslog-records"></a>Query di log con record Syslog
La tabella seguente mostra alcuni esempi di query di log che recuperano i record Syslog.

| Query | DESCRIZIONE |
|:--- |:--- |
| syslog |Tutti i record Syslog. |
| Syslog &#124; where SeverityLevel == "error" |Tutti i record Syslog con livello di gravità errore. |
| Syslog &#124; summarize AggregatedValue = count() by Computer |Numero di record Syslog per computer. |
| Syslog &#124; summarize AggregatedValue = count() by Facility |Numero di record Syslog per funzionalità. |

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.
* Usare [campi personalizzati](log-analytics-custom-fields.md) per analizzare i dati dei record Syslog nei singoli campi.
* [Configurare agenti Linux](log-analytics-linux-agents.md) per raccogliere altri tipi di dati.
