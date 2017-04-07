---
title: Connettere computer Linux a Log Analytics di Azure | Documentazione Microsoft
description: Log Analytics consente di raccogliere dati dai computer Linux e di agire in base a essi.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ab5b76d8-9ab5-406e-8768-76fb0632d830
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: b01b0d3d61168c1eec52f3fd040b829e0c51a878
ms.lasthandoff: 03/30/2017


---
# <a name="connect-your-linux-computers-to-log-analytics"></a>Connettere computer Linux a Log Analytics
Log Analytics consente di raccogliere dati dai computer Linux e di agire in base a essi. L'aggiunta di dati raccolti da Linux a OMS consente di gestire sistemi Linux e soluzioni contenitore come Docker indipendentemente dalla posizione in cui si trovano i computer, ovvero quasi ovunque. Le origini dati possono trovarsi nel data center locale come server fisici, in computer virtuali in un servizio ospitato sul cloud come Amazon Web Services (AWS) o Microsoft Azure o anche sul computer portatile sulla scrivania. OMS raccoglie anche dati da computer Windows in modo analogo, quindi supporta un ambiente IT effettivamente ibrido.

È possibile visualizzare e gestire i dati da tutte queste origini dati con Log Analytics in OMS con un singolo portale di gestione. Ciò consente di ridurre la necessità di monitorarle usando molti sistemi diversi, ne semplifica l'utilizzo e consente di esportare qualsiasi tipo di dati in qualsiasi soluzione di analisi business o in qualsiasi sistema già disponibile.

Questo articolo è una breve guida introduttiva che illustra come raccogliere e gestire i dati per i computer Linux usando l'agente OMS per Linux. Maggiori informazioni tecniche, ad esempio informazioni su configurazione del server proxy, informazioni sulle metriche di CollectD e origini dati JSON personalizzati sono disponibili nella [panoramica sull'agente OMS per Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) e nella [documentazione completa sull'agente OMS per Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) su GitHub.

È attualmente possibile raccogliere i tipi di dati seguenti da computer Linux:

* Metriche delle prestazioni
* Eventi SysLog
* Avvisi da Nagios e Zabbix
* Metriche delle prestazioni, inventario e log del contenitore Docker

## <a name="supported-linux-versions"></a>Versioni di Linux supportate
Le versioni x86 e x64 sono supportate ufficialmente su diverse distribuzioni Linux. È tuttavia possibile che l'agente OMS per Linux sia eseguito in altre distribuzioni non elencate.

* Amazon Linux dalla 2012.09 alla 2015.09
* CentOS Linux 5, 6 e 7
* Oracle Linux 5, 6 e 7
* Red Hat Enterprise Linux Server 5, 6 e 7
* Debian GNU/Linux 6, 7 e 8
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10
* SUSE Linux Enterprise Server 11 e 12

## <a name="oms-agent-for-linux"></a>Agente OMS per Linux
Operations Management Suite Agent per Linux è costituito da più pacchetti. Il file della versione rilasciata contiene i pacchetti seguenti, disponibili mediante l'esecuzione dell'aggregazione della shell con `--extract`.

| **Pacchetto** | **Versione** | **Descrizione** |
| --- | --- | --- |
| omsagent |1.1.0 |Agente Operations Management Suite per Linux |
| omsconfig |1.1.1 |Agente di configurazione per l'agente OMS |
| omi |1.0.8.3 |Open Management Infrastructure (OMI): server CIM leggero |
| scx |1.6.2 |Provider OMI CIM per metriche delle prestazioni del sistema operativo |
| apache-cimprov |1.0.0 |Monitoraggio delle prestazioni del server HTTP Apache per OMI. Installato solo se viene rilevato il server HTTP Apache. |
| mysql-cimprov |1.0.0 |Monitoraggio delle prestazioni del server MySQL per OMI. Installato solo se viene rilevato il server MySQL/MariaDB. |
| docker-cimprov |0.1.0 |Provider Docker per OMI. Installato solo se viene rilevato Docker. |

### <a name="additional-installation-artifacts"></a>Elementi di installazione aggiuntivi
Dopo l'installazione dei pacchetti dell'agente OMS per Linux, vengono applicate le modifiche di configurazione aggiuntive seguenti a livello di sistema. Questi elementi vengono rimossi quando viene disinstallato il pacchetto omsagent.

* Viene creato un utente senza privilegi denominato `omsagent` . Si tratta dell'account usato per l'esecuzione del daemon omsagent.
* Viene creato un file "include" sudoers nel percorso /etc/sudoers.d/omsagent. Questo file autorizza omsagent a riavviare i daemon SysLog e omsagent. Se le direttive "include" sudo non sono supportate nella versione di sudo installata, queste voci verranno scritte in /etc/sudoers.
* La configurazione di SysLog viene modificata in modo da inoltrare un sottoinsieme di eventi all'agente. Per altre informazioni, vedere la sezione **Configurazione della raccolta di dati** più avanti.

### <a name="linux-data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati Linux
La tabella seguente mostra i metodi di raccolta di dati e altre informazioni dettagliate sul modo in cui vengono raccolti i dati.

| una sezione source | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Zabbix |![Sì](./media/log-analytics-linux-agents/oms-bullet-green.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |1 minuto |
| Nagios |![Sì](./media/log-analytics-linux-agents/oms-bullet-green.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |All'arrivo |
| syslog |![Sì](./media/log-analytics-linux-agents/oms-bullet-green.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |dall'Archiviazione di Azure: 10 minuti; dall'agente: all'arrivo |
| Contatori delle prestazioni di Linux |![Sì](./media/log-analytics-linux-agents/oms-bullet-green.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |Come pianificato, almeno 10 secondi. |
| Rilevamento modifiche |![Sì](./media/log-analytics-linux-agents/oms-bullet-green.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |Ogni ora |

### <a name="package-requirements"></a>Requisiti del pacchetto
| **Pacchetto obbligatorio** | **Descrizione** | **Versione minima** |
| --- | --- | --- |
| Glibc |Libreria GNU C |2.5-12 |
| Openssl |Librerie OpenSSL |0.9.8e o 1.0 |
| Curl |Client Web cURL |7.15.5 |
| Python-ctypes |Librerie di funzioni |N/D |
| PAM |Moduli di autenticazione modulare |N/D |

> [!NOTE]
> Per raccogliere i messaggi SysLog, è necessario rsyslog o syslog-ng. Il daemon SysLog predefinito nella versione 5 di Red Hat Enterprise Linux, CentOS e nella versione Oracle Linux (sysklog) non è supportato per la raccolta di eventi SysLog. Per raccogliere i dati di SysLog da questa versione delle distribuzioni, è necessario installare e configurare il daemon rsyslog in modo da sostituire sysklog.
>
>

## <a name="quick-install"></a>Installazione rapida
Eseguire i comandi seguenti per scaricare omsagent, convalidare il checksum, quindi installare e caricare l'agente. I comandi sono per la versione a 64 bit. I valori per l'ID area di lavoro e la chiave primaria sono disponibili in **Impostazioni** nella scheda **Origini connesse** del portale di OMS.

![Dettagli dell'area di lavoro](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

È possibile installare e aggiornare l'agente in molti altri modi. Per altre informazioni, vedere i [Passaggi per installare l'agente OMS per Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux).

È inoltre possibile visualizzare il [video con la procedura dettagliata di Azure](https://www.youtube.com/watch?v=mF1wtHPEzT0).

## <a name="choose-your-linux-data-collection-method"></a>Scegliere il metodo di raccolta di dati Linux
La modalità di selezione dei tipi di dati da raccogliere dipende dal fatto che si scelga di usare il portale OMS o di modificare diversi file di configurazione direttamente nei client Linux. Se si sceglie di usare il portale, la configurazione viene inviata automaticamente a tutti i client Linux. Se sono necessarie configurazioni diverse per diversi client Linux, occorrerà modificare i singoli file client oppure usare un'alternativa come PowerShell DSC, Chef o Puppet.

È possibile specificare gli eventi SysLog e i contatori delle prestazioni da raccogliere usando file di configurazione nei computer Linux. *Se si sceglie di configurare la raccolta di dati modificando i file di configurazione dell'agente, è necessario disabilitare la configurazione centralizzata.*  Di seguito sono disponibili istruzioni per la configurazione della raccolta di dati nei file di configurazione dell'agente, oltre alle istruzioni per la disabilitazione della configurazione centrale per tutti gli agenti OMS per Linux o per singoli computer.

### <a name="disable-oms-management-for-an-individual-linux-computer"></a>Disabilitare la gestione OMS per un singolo computer Linux
La raccolta di dati centralizzata per i dati di configurazione viene disabilitata per un singolo computer Linux mediante lo script OMS_MetaConfigHelper.py. Ciò può risultare utile se un sottoinsieme di computer necessita di una configurazione specializzata.

Per disabilitare la configurazione centralizzata:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

Per abilitare di nuovo la configurazione centralizzata:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## <a name="linux-performance-counters"></a>Contatori delle prestazioni di Linux
I contatori delle prestazioni di Linux sono simili ai contatori delle prestazioni di Windows e hanno un funzionamento analogo. È possibile usare le procedure seguenti per aggiungerli e configurarli. Dopo l'aggiunta a OMS, i dati vengono raccolti per questi contatori ogni 30 secondi.

### <a name="to-add-a-linux-performance-counter-in-oms"></a>Per aggiungere un contatore delle prestazioni di Linux in OMS
1. Per configurare gli agenti OMS per Linux usando il portale OMS, è possibile aggiungere contatori delle prestazioni di Linux nella pagina Impostazioni facendo clic su **Dati**.  
2. Alla pagina **Impostazioni** di **Dati** fare clic su **Contatori delle prestazioni di Linux** e quindi selezionare o digitare il nome del contatore da aggiungere.  
    ![dati](./media/log-analytics-linux-agents/oms-settings-data01.png)
3. Se non si conosce il nome completo del contatore, è possibile iniziare a immettere un nome parziale. Verrà visualizzato un elenco di contatori disponibili. Quando si trova il contatore da aggiungere, fare clic sul nome nell'elenco, quindi fare clic sull'icona del segno più per aggiungere il contatore.
4. Dopo l'aggiunta, il contatore viene visualizzato nell'elenco di contatori, evidenziato con una barra colorata.
5. Per impostazione predefinita, l'opzione **Applica la configurazione seguente alle macchine virtuali** è selezionata. Se si vuole disabilitare l'invio dei dati di configurazione, deselezionare l'opzione.
6. Al termine della modifica dei contatori delle prestazioni, nella parte inferiore della pagina fare clic su **Salva** per finalizzare le modifiche. Le modifiche apportate alla configurazione vengono inviate a tutti gli agenti OMS per Linux registrati con OMS, in genere entro 5 minuti.

### <a name="configure-linux-performance-counters-in-oms"></a>Configurare i contatori delle prestazioni di Linux in OMS
Per i contatori delle prestazioni di Windows è possibile scegliere un'istanza specifica per ogni contatore delle prestazioni. Per i contatori delle prestazioni di Linux, tuttavia, qualsiasi istanza di un contatore scelta viene applicata a tutti i contatori figlio del contatore padre. La tabella seguente illustra le istanze comuni disponibili ai contatori delle prestazioni di Linux e Windows.

| **Nome dell'istanza** | **Significato** |
| --- | --- |
| \_Totale |Totale di tutte le istanze |
| \* |Tutte le istanze |
| (/&#124;/var) |Corrisponde alle istanze denominate / oppure /var |

Analogamente, l'intervallo di campionamento scelto per un contatore padre viene applicato a tutti i rispettivi contatori figlio. In altri termini, gli intervalli di campionamento e le istanze di tutti i contatori figlio sono collegati tra loro.

### <a name="add-and-configure-performance-metrics-with-linux"></a>Aggiungere e configurare le metriche delle prestazioni con Linux
Le metriche delle prestazioni sono controllate dalla configurazione in /etc/opt/microsoft/omsagent/&lt;workspace id&gt;/conf/omsagent.conf. Per informazioni sulle classi e sulle metriche disponibili per l'agente OMS per Linux, vedere la pagina relativa alle [Metriche delle prestazioni disponibili](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics) .

Ogni oggetto o categoria delle metriche delle prestazioni da raccogliere deve essere definito nel file di configurazione come singolo elemento `<source>` . La sintassi segue il modello seguente.

```
<source>
  type oms_omi  
  object_name "Processor"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

Ecco i parametri configurabili di questo elemento:

* **Object\_name**: il nome dell'oggetto per la raccolta.
* **Instance\_regex**: un'*espressione regolare* che definisce le istanze da raccogliere. Il valore `.*` specifica tutte le istanze. Per raccogliere le metriche del processore solo per l'istanza \_Total, è possibile specificare `_Total`. Per raccogliere le metriche del processore solo per le istanze crond o sshd, è possibile specificare `(crond|sshd)`.
* **Counter\_name\_regex**: un'*espressione regolare* che definisce i contatori per l'oggetto da raccogliere. Per raccogliere tutti i contatori per l'oggetto, specificare `.*`. Per raccogliere solo i contatori dello spazio di swapping per l'oggetto Memory, è possibile specificare `.+Swap.+`
* **Interval:**: frequenza con cui vengono raccolti i contatori dell'oggetto.

Ecco la configurazione predefinita per le metriche delle prestazioni:

```
<source>
  type oms_omi
  object_name "Physical Disk"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Logical Disk"
  instance_regex ".*
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Processor"
  instance_regex ".*
  counter_name_regex ".*"
  interval 30s
</source>

<source>
  type oms_omi
  object_name "Memory"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

### <a name="enable-mysql-performance-counters-using-linux-commands"></a>Abilitare i contatori delle prestazioni di MySQL usando comandi di Linux
Se viene rilevato un server MySQL o MariaDB nel computer in cui è installata l'aggregazione omsagent, viene installato automaticamente un provider di monitoraggio delle prestazioni per il server MySQL. Questo provider si connette al server MySQL/MariaDB locale per esporre le statistiche relative alle prestazioni. È necessario configurare le credenziali dell'utente di MySQL, in modo che il provider possa accedere al server MySQL.

Per definire un account utente predefinito per il server MySQL su localhost, usare il comando di esempio seguente.

> [!NOTE]
> Il file delle credenziali deve essere leggibile dall'account omsagent. È consigliabile eseguire il comando mycimprovauth come omsgent.
>
>

```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>

sudo /opt/omi/bin/service_control restart
```


In alternativa, è possibile specificare le credenziali di MySQL necessarie in un file, creando il file /var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth. Per altre informazioni sulla gestione delle credenziali di MySQL per il monitoraggio mediante il file mysql-auth, vedere [Gestire le credenziali per il monitoraggio di MySQL nel file di autenticazione](#manage-mysql-monitoring-credentials-in-the-authentication-file).

Per informazioni dettagliate sulle autorizzazioni oggetto richieste dall'utente di MySQL per raccogliere i dati sulle prestazioni del server MySQL, vedere [Autorizzazioni del database necessarie per i contatori delle prestazioni di MySQL](#database-permissions-required-for-mysql-performance-counters) .

### <a name="enable-apache-http-server-performance-counters-using-linux-commands"></a>Abilitare i contatori delle prestazioni del server HTTP Apache usando comandi di Linux
Se viene rilevato un server HTTP Apache nel computer in cui è installata l'aggregazione omsagent, viene installato automaticamente un provider di monitoraggio delle prestazioni per il server HTTP Apache. Questo provider si basa su un "modulo" Apache che deve essere caricato nel server HTTP Apache in modo da accedere ai dati sulle prestazioni.

È possibile caricare il modulo con il comando seguente:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Per scaricare il modulo di monitoraggio Apache, eseguire il comando seguente:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### <a name="to-view-performance-data-with-log-analytics"></a>Per visualizzare i dati sulle prestazioni con Log Analytics
1. Nel portale di Operations Management Suite fare clic sul riquadro Ricerca log.
2. Nella barra di ricerca digitare `* (Type=Perf)` per visualizzare tutti i contatori delle prestazioni.

Poiché OMS raccoglie anche i dati dei contatori delle prestazioni di Windows, è necessario limitare l'ambito della ricerca a dati specifici di Linux. L'esempio seguente consente quindi di visualizzare i dati sulle prestazioni specifici per un server Linux di esempio denominato Chorizo21.

```
Type=Perf Computer=chorizo*
```

![Server di esempio visualizzato nei risultati della ricerca](./media/log-analytics-linux-agents/oms-perfsearch01.png)

Nei risultati è possibile fare clic su **Metriche** per visualizzare i contatori per cui sono stati raccolti i dati. I dati in tempo reale vengono mostrati come grafici per ogni contatore.

![Metriche](./media/log-analytics-linux-agents/oms-perfmetrics01.png)

## <a name="syslog"></a>syslog
SysLog è un protocollo di registrazione di eventi analogo ai registri eventi di Windows. Il funzionamento di entrambi è simile in caso di visualizzazione in OMS.

### <a name="to-add-a-new-linux-syslog-facility-in-oms"></a>Per aggiungere una nuova funzionalità SysLog per Linux in OMS
1. Alla pagina **Impostazioni** di **Dati** fare clic su **Syslog** e quindi, a sinistra dell'icona con il segno più, digitare il nome della funzionalità Syslog da aggiungere.
    ![Syslog per Linux](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2. Se non si conosce il nome completo della funzionalità, è possibile iniziare a immettere un nome parziale. Verrà visualizzato un elenco di funzionalità SysLog disponibili. Quando si trova la funzionalità SysLog da aggiungere, fare clic sul nome nell'elenco, quindi fare clic sull'icona del segno più per aggiungere la funzionalità SysLog.
3. Dopo l'aggiunta, la funzionalità viene visualizzata nell'elenco, evidenziata con una barra colorata. Scegliere quindi il livello di gravità, ovvero le categorie di informazioni sulle funzionalità SysLog, da raccogliere.
4. Nella parte inferiore della pagina fare clic su **Salva** per finalizzare le modifiche. Le modifiche apportate alla configurazione vengono inviate a tutti gli agenti OMS per Linux registrati con OMS, in genere entro 5 minuti.

### <a name="configure-linux-syslog-facilities-in-linux"></a>Configurare le funzionalità SysLog per Linux in Linux
Gli eventi SysLog vengono inviati dal daemon SysLog, ad esempio rsyslog o syslog-ng, a una porta locale su cui è in ascolto l'agente. Per impostazione predefinita, si tratta della porta 25224. Quando l'agente viene installato, viene applicata una configurazione di SysLog predefinita, disponibile in:

Rsyslog: /etc/rsyslog.d/rsyslog-oms.conf

Syslog-ng: /etc/syslog-ng/syslog-ng.conf

La configurazione di SysLog per l'agente OMS carica eventi SysLog da tutte le funzionalità con gravità pari a un avviso o superiore.

> [!NOTE]
> Se si modifica la configurazione di SysLog, è necessario riavviare il daemon SysLog per rendere effettive le modifiche.
>
>

Ecco la configurazione predefinita di SysLog per l'agente OMS per Linux per OMS:

#### <a name="rsyslog"></a>Rsyslog
```
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
```

#### <a name="syslog-ng"></a>Syslog-ng
```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### <a name="to-view-all-syslog-events-with-log-analytics"></a>Per visualizzare tutti gli eventi SysLog con Log Analytics
1. Nel portale di Operations Management Suite fare clic sul riquadro **Ricerca log** .
2. Nel gruppo **Gestione log** scegliere una ricerca SysLog predefinita e quindi selezionare un'opzione per eseguirla.

Questo esempio mostra tutti gli eventi SysLog.

![Eventi SysLog visualizzati in Ricerca log](./media/log-analytics-linux-agents/oms-linux-syslog.png)

È ora possibile esaminare i risultati della ricerca.

## <a name="linux-alerts"></a>Avvisi di Linux
Se si usa Nagios o Zabbix per gestire i computer Linux, OMS può ricevere gli avvisi generati da questi strumenti. Non è tuttavia disponibile attualmente alcun metodo per configurare i dati sugli avvisi in ingresso usando il portale di OMS. Sarà invece necessario modificare un file di configurazione per iniziare a inviare avvisi a OMS.

### <a name="collect-alerts-from-nagios"></a>Raccogliere avvisi da Nagios
Per raccogliere avvisi da un server Nagios, è necessario apportare le modifiche seguenti alla configurazione.

1. Concedere all'utente **omsagent** l'accesso in lettura al file di log Nagios, ad esempio /var/log/nagios/nagios.log. Supponendo che il file nagios.log sia di proprietà del gruppo **nagios**, è possibile aggiungere l'utente **omsagent** al gruppo **nagios**.

    ```
    sudo usermod –a -G nagios omsagent
    ```
2. Modificare il file omsagent.confconfiguration (/etc/opt/microsoft/omsagent/&lt;workspace id&gt;/conf/omsagent.conf). Assicurarsi che le voci seguenti siano presenti e non impostate come commento:

    ```
    <source>
    type tail
    #Update path to point to your nagios.log
    path /var/log/nagios/nagios.log
    format none
    tag oms.nagios
    </source>

    <filter oms.nagios>
    type filter_nagios_log
    </filter>
    ```
3. Riavviare il daemon omsagent:

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="collect-alerts-from-zabbix"></a>Raccogliere avvisi da Zabbix
Per raccogliere avvisi da un server Zabbix, sarà necessario eseguire una procedura analoga a quella precedente per Nagios, ma occorrerà specificare un utente e una password in *testo non crittografato*. Questo approccio non è ideale, ma verrà probabilmente modificato a breve. Per risolvere questo problema, è consigliabile creare l'utente e assegnare all'utente solo le autorizzazioni per il monitoraggio.

Una sezione di esempio del file di configurazione omsagent.conf (/etc/opt/microsoft/omsagent/&lt;workspace id&gt;/conf/omsagent.conf) per Zabbix dovrebbe essere simile alla seguente:

```
<source>
  type zabbix_alerts
  run_interval 1m
  tag oms.zabbix
  zabbix_url http://localhost/zabbix/api_jsonrpc.php
  zabbix_username Admin
  zabbix_password zabbix
</source>

```

### <a name="view-alerts-in-log-analytics-search"></a>Visualizzare avvisi nella ricerca di Log Analytics
Dopo avere configurato i computer Linux per l'invio di avvisi a OMS, è possibile usare alcune semplici query di ricerca nel log per visualizzare gli avvisi. L'esempio di query di ricerca seguente restituisce tutti gli avvisi registrati che sono stati generati. Ad esempio, se si verifica qualche problema nell'infrastruttura IT, i risultati per l'esempio di query seguente potrebbero indicare l'origine del problema. Sarà anche possibile esaminare nel dettaglio gli avvisi in base al sistema di origine, per perfezionare la ricerca. Non sarà quindi necessario passare a diversi sistemi di gestione fin dall'inizio. Se gli avvisi vengono inviati a OMS, sarà possibile iniziare da tale sistema.

```
Type=Alert
```

#### <a name="to-view-all-nagios-alerts-with-log-analytics"></a>Per visualizzare tutti gli avvisi di Nagios con Log Analytics
1. Nel portale di Operations Management Suite fare clic sul riquadro **Ricerca log** .
2. Nella barra per le query immettere la query di ricerca seguente:

    ```
    Type=Alert SourceSystem=Nagios
    ```
   ![Avvisi Nagios visualizzati in Ricerca log](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

Dopo avere visualizzato i risultati della ricerca, è possibile analizzare altri dettagli, ad esempio *AlertState*.

### <a name="to-view-all-zabbix-alerts-with-log-analytics"></a>Per visualizzare tutti gli avvisi di Zabbix con Log Analytics
1. Nel portale di Operations Management Suite fare clic sul riquadro **Ricerca log** .
2. Nella barra per le query immettere la query di ricerca seguente:

    ```
    Type=Alert SourceSystem=Zabbix
    ```
   ![Avvisi Zabbix visualizzati in Ricerca log](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

Dopo avere visualizzato i risultati della ricerca, è possibile analizzare altri dettagli, ad esempio *AlertName*.

## <a name="compatibility-with-system-center-operations-manager"></a>Compatibilità con System Center Operations Manager
L'agente OMS per Linux condivide file binari dell'agente con l'agente System Center Operations Manager. Se si installa l'agente OMS per Linux in un sistema attualmente gestito da Operations Manager, i pacchetti OMI e SCX nel computer vengono aggiornati a una versione più recente. L'agente OMS per Linux e System Center 2012 R2 sono compatibili, ma **System Center 2012 SP1 e le versioni precedenti non sono attualmente compatibili o supportati con l'agente OMS per Linux.**

> [!NOTE]
> Se l'agente OMS per Linux viene installato in un computer attualmente non gestito da Operations Manager e successivamente si vuole gestire il computer con Operations Manager, è necessario modificare la configurazione OMI prima di individuare il computer. **Questo passaggio non è necessario se l'agente Operations Manager viene installato prima dell'agente OMS per Linux.**
>
>

### <a name="to-enable-the-oms-agent-for-linux-to-communicate-with-operations-manager"></a>Per consentire all'agente OMS per Linux di comunicare con Operations Manager
1. Modificare il file /etc/opt/omi/conf/omiserver.conf.
2. Assicurarsi che la riga che inizia con **httpsport=** definisca la porta 1270, ad esempio `httpsport=1270`
3. Riavviare il server OMI:

    ```
    sudo /opt/omi/bin/service_control restart
    ```

## <a name="database-permissions-required-for-mysql-performance-counters"></a>Autorizzazioni del database necessarie per i contatori delle prestazioni di MySQL
Per concedere autorizzazioni a un utente di monitoraggio di MySQL, l'utente che le concede deve avere il privilegio 'GRANT option', oltre al privilegio da concedere.

Perché l'utente di MySQL possa restituire i dati sulle prestazioni, è necessario che l'utente possa accedere alle query seguenti:

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

Oltre a queste query, l'utente di MySQL deve avere accesso di tipo SELECT alle tabelle predefinite seguenti:

* information_schema
* mysql

Questi privilegi possono essere concessi mediante l'esecuzione dei comandi grant seguenti.

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## <a name="manage-mysql-monitoring-credentials-in-the-authentication-file"></a>Gestire le credenziali per il monitoraggio di MySQL nel file di autenticazione
Le sezioni seguenti illustrano come gestire le credenziali di MySQL.

### <a name="configure-the-mysql-omi-provider"></a>Configurare il provider OMI MySQL
Il provider OMI MySQL richiede un utente di MySQL preconfigurato e librerie client MySQL installate, in modo da eseguire query sulle prestazioni/informazioni sull'integrità dall'istanza di MySQL.

### <a name="mysql-omi-authentication-file"></a>File di autenticazione OMI MySQL
Il provider OMI MySQL usa un file di autenticazione per determinare il valore bind-address e la porta su cui è in ascolto l'istanza di MySQL e infine le credenziali da usare per raccogliere metriche. Durante l'installazione, il provider OMI MySQL analizzerà i file di configurazione my.cnf di MySQL (percorsi predefiniti) alla ricerca del valore bind-address e della porta e imposterà in modo parziale il file di configurazione OMI MySQL.

Per completare il monitoraggio di un'istanza del server MySQL, aggiungere un file di autenticazione OMI MySQL pregenerato alla directory corretta.

### <a name="authentication-file-format"></a>Formato del file di autenticazione
Il file di autenticazione OMI MySQL è un file di testo che contiene informazioni sugli elementi seguenti:

* Port
* Bind-address
* Nome utente MySQL
* Password con codifica Base64

Il file di autenticazione OMI MySQL concede privilegi solo per la lettura/scrittura per l'utente Linux che lo ha generato.

```
[Port]=[Bind-Address], [username], [Base64 encoded Password]
(Port)=(Bind-Address), (username), (Base64 encoded Password)
(Port)=(Bind-Address), (username), (Base64 encoded Password)
AutoUpdate=[true|false]
```

Un file di autenticazione OMI MySQL predefinito contiene un'istanza predefinita e un numero di porta, in base alle informazioni disponibili e analizzate nel file di configurazione MySQL trovato.

L'istanza predefinita semplifica la gestione di più istanze di MySQL in un host Linux e viene indicata dall'istanza con porta 0. Tutte le istanze aggiunte erediteranno le proprietà impostate dall'istanza predefinita. Ad esempio, se viene aggiunta l'istanza di MySQL in ascolto sulla porta "3308", il valore bind-address, il nome utente e la password con codifica Base64 dell'istanza predefinita verranno usati per provare a monitorare l'istanza in ascolto sulla porta 3308. Se l'istanza sulla porta 3308 è associata a un altro indirizzo e usa la stessa coppia nome utente e password di MySQL, sarà necessario specificare di nuovo solo il valore bind-address e le altre proprietà verranno ereditate.

Gli esempi del file di autenticazione hanno un aspetto analogo ai seguenti.

Istanza predefinita e istanza con porta 3308:

```
0=127.0.0.1, myuser, cnBwdA==3308=, ,AutoUpdate=true
```

Istanza predefinita e istanza con porta 3308 e password con codifica Base 64 diversa:

```
0=127.0.0.1, myuser, cnBwdA==3308=127.0.1.1, , AutoUpdate=true
```


| **Proprietà** | **Descrizione** |
| --- | --- |
| Port |Port rappresenta la porta corrente su cui è in ascolto l'istanza di MySQL.  La porta 0 indica che le proprietà seguenti vengono usate per l'istanza predefinita. |
| Bind-address |Bind-address corrisponde al valore bind-address corrente di MySQL. |
| username |Nome utente dell'utente di MySQL da usare per monitorare l'istanza del server MySQL. |
| Password con codifica Base64 |Password dell'utente di monitoraggio di MySQL con codifica Base64. |
| AutoUpdate |Quando il provider OMI MySQL viene aggiornato, ripeterà l'analisi alla ricerca di modifiche nel file my.cnf e sovrascriverà il file di autenticazione OMI MySQL. Impostare questo flag su true o false, in base agli aggiornamenti necessari per il file di autenticazione OMI MySQL. |

#### <a name="authentication-file-location"></a>Percorso del file di autenticazione
Il file di autenticazione OMI MySQL deve trovarsi nel percorso seguente e deve essere denominato "mysql-auth":

/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth

Il file e la directory auth/omsagent devono essere di proprietà dell'utente omsagent.

## <a name="agent-logs"></a>Log dell'agente
I log per l'agente OMS per Linux sono disponibili nel percorso seguente:

/var/opt/microsoft/omsagent/&lt;workspace id&gt;/log/

I log per l'agente OMS per Linux per il programma omsconfig (configurazione dell'agente) sono disponibili nel percorso seguente:

/var/opt/microsoft/omsconfig/log/

I log per i componenti OMI e SCX, che forniscono i dati sulle metriche sulle prestazioni, sono disponibili nel percorso seguente:

/var/opt/omi/log/ e /var/opt/microsoft/scx/log

## <a name="troubleshooting-the-oms-agent-for-linux"></a>Risoluzione dei problemi dell'agente OMS per Linux
Usare le seguenti informazioni per diagnosticare e risolvere i problemi comuni.

Se nessuna delle informazioni sulla risoluzione dei problemi presenti in questa sezione è utile, è possibile usare anche le risorse seguenti per risolvere il problema.

* I clienti con supporto tecnico Premier possono registrare un caso di supporto tramite [Premier](https://premier.microsoft.com/)
* I clienti con contratti di supporto tecnico di Azure possono registrare casi di supporto nel [portale di Azure](https://manage.windowsazure.com/?getsupport=true)
* Presentare un [problema GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)
* Per trovare idee e creare un report dei bug, consultare il forum di commenti e suggerimenti [http://aka.ms/opinsightsfeedback](http://aka.ms/opinsightsfeedback)

### <a name="important-log-locations"></a>Percorsi log importanti
| File | Path |
| --- | --- |
| File di log dell'agente OMS per Linux |`/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log ` |
| File di log di configurazione dell'agente OMS |`/var/opt/microsoft/omsconfig/omsconfig.log` |

### <a name="important-configuration-files"></a>File di configurazione importanti
| Categoria | Percorso file |
| --- | --- |
| syslog |`/etc/syslog-ng/syslog-ng.conf` o `/etc/rsyslog.conf` o `/etc/rsyslog.d/95-omsagent.conf` |
| Output e agente generale Performance, Nagios, Zabbix e OMS |`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` |
| Configurazioni aggiuntive |`/etc/opt/microsoft/omsagent/<workspace id>/omsagent.d/*.conf` |

> [!NOTE]
> La modifica dei file di configurazione per i contatori delle prestazioni e Syslog vengono sovrascritti se è abilitata la configurazione del portale di OMS. È possibile disabilitare la configurazione nel portale di OMS (per tutti i nodi) o per i singoli nodi eseguendo le operazioni seguenti:
>
>

```
sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```


### <a name="enable-debug-logging"></a>Abilitazione della registrazione di debug
Per abilitare la registrazione di debug, è possibile usare il plug-in di output di OMS e l'output dettagliato.

#### <a name="oms-output-plugin"></a>Plug-in di output di OMS
FluentD consente al plug-in di specificare i livelli di registrazione per diversi livelli di log per input e output. Per specificare un livello di log diverso per l'output di OMS, modificare la configurazione dell'agente generale nel file `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.

Nella parte inferiore del file di configurazione, modificare la proprietà `log_level` da `info` a `debug`.

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

La registrazione di debug consente di visualizzare i caricamenti in batch nel servizio OMS separati in base a tipo, numero di elementi di dati e tempo impiegato per l'invio.

*Esempio di log abilitato per il debug:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

#### <a name="verbose-output"></a>Output dettagliato
Anziché usare il plug-in output di OMS, è anche possibile inviare elementi di dati direttamente a `stdout`, che è visibile nel file di log dell'agente OMS per Linux.

Nel file di configurazione dell'agente generale OMS in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, impostare come commento il plug-in di output di OMS aggiungendo `#` davanti a ogni riga.

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Sotto l'output di plug-in, rimuovere il commento nella sezione seguente eliminando il simbolo `#` all'inizio di ogni riga.

```
<match **>
  type stdout
</match>
```

### <a name="forwarded-syslog-messages-do-not-appear-in-the-log"></a>I messaggi Syslog inoltrati non vengono visualizzati nel log
#### <a name="probable-causes"></a>Possibili cause
* La configurazione applicata al server Linux non consente la raccolta delle strutture e/o dei livelli di log inviati
* Syslog non viene inoltrato correttamente al server Linux
* Il numero di messaggi inoltrati al secondo è troppo elevato, di conseguenza la configurazione di base dell'agente OMS per Linux non può gestirli

#### <a name="resolutions"></a>Soluzioni
* Verificare che la configurazione nel portale OMS per Syslog disponga di tutte le strutture e dei livelli di log corretti
  * **Portale OMS > Impostazioni > Dati > Syslog**
* Verificare che i daemon di messaggistica Syslog nativi (`rsyslog`, `syslog-ng`) siano in grado di ricevere i messaggi inoltrati
* Controllare le impostazioni del firewall sul server Syslog per verificare che i messaggi non vengano bloccati
* Simulare un messaggio Syslog per OMS usando il comando `logger`, ad esempio:
  * `logger -p local0.err "This is my test message"`

### <a name="problems-connecting-to-oms-when-using-a-proxy"></a>Problemi di connessione a OMS quando si usa un proxy
#### <a name="probable-causes"></a>Possibili cause
* Il proxy specificato durante l'installazione e la configurazione dell'agente non è corretto
* Gli endpoint del servizio OMS non sono presenti nell'elenco elementi consentiti del data center

#### <a name="resolutions"></a>Soluzioni
* Reinstallare l'agente OMS per Linux usando il comando seguente con l'opzione `-v` abilitata. In questo modo l'output dettagliato dell'agente si connette tramite proxy al servizio OMS.
  * `/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`
  * Consultare la documentazione relativa al proxy OMS in [Configuring the agent for use with an HTTP proxy server](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#configuring-the-agent-for-use-with-an-http-proxy-server) (Configurazione dell'agente per l'uso con un server proxy HTTP)
* Verificare che i seguenti endpoint del servizio OMS siano inclusi nell'elenco elementi consentiti

| Risorsa agente | Porte |
| --- | --- |
| &#42;.ods.opinsights.azure.com |Porta 443 |
| &#42;.oms.opinsights.azure.com |Porta 443 |
| ods.systemcenteradvisor.com |Porta 443 |
| &#42;.blob.core.windows.net/ |Porta 443 |

### <a name="a-403-error-is-displayed-when-onboarding"></a>Viene visualizzato un messaggio di errore 403 durante il caricamento
#### <a name="probable-causes"></a>Possibili cause
* Data e ora non sono corrette nel server Linux
* L'ID e la chiave dell'area di lavoro usati non sono corretti

#### <a name="resolution"></a>Risoluzione
* Verificare l'ora sul server Linux con il comando `date`. Se nei dati si verifica una differenza di 15 minuti per eccesso o per difetto rispetto all'ora corrente, il caricamento ha esito negativo. Per risolvere il problema, aggiornare la data e/o il fuso orario del server Linux.
* La versione più recente dell'agente OMS per Linux informa se una differenza di orario provoca un errore di caricamento
* Ripetere il caricamento usando l'ID e la chiave dell'area di lavoro corretti. Per maggiori informazioni, vedere [Onboarding using the command line](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) (Caricamento usando la riga di comando).

### <a name="a-500-error-or-404-error-appears-in-the-log-file-after-onboarding"></a>Nel file di log viene visualizzato un errore 500 o 404 dopo il caricamento
Si tratta di un problema noto che si verifica durante il primo caricamento dei dati di Linux in un'area di lavoro di OMS. Questo non influisce sui dati inviati o su altri problemi. È possibile ignorare gli errori durante il caricamento iniziale.

### <a name="nagios-data-does-not-appear-in-the-oms-portal"></a>I dati di Nagios non vengono visualizzati nel portale di OMS
#### <a name="probable-causes"></a>Possibili cause
* L'utente omsagent non dispone delle autorizzazioni per leggere dai file di log di Nagios
* Le sezioni di origine e filtro di Nagios continuano a essere commentate nel file omsagent.conf

#### <a name="resolutions"></a>Soluzioni
* Per leggere dal file di Nagios, aggiungere l'utente omsagent. Per maggiori informazioni, vedere [Nagios alerts](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts) (Avvisi di Nagios).
* Nel file di configurazione generale dell'agente OMS per Linux in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` assicurarsi che **entrambe** le sezioni (di origine e filtro) di Nagios contengano i commenti rimossi, in modo analogo all'esempio seguente.

```
<source>
  type tail
  path /var/log/nagios/nagios.log
  format none
  tag oms.nagios
</source>

<filter oms.nagios>
  type filter_nagios_log
</filter>
```


### <a name="linux-data-doesnt-appear-in-the-oms-portal"></a>I dati di Linux non vengono visualizzati nel portale di OMS
#### <a name="probable-causes"></a>Possibili cause
* Il caricamento nel servizio OMS ha avuto esito negativo
* La connessione al servizio OMS è bloccata
* Viene eseguito il backup dei dati dell'agente OMS per Linux

#### <a name="resolutions"></a>Soluzioni
* Controllare che il caricamento sul servizio OMS abbia avuto esito positivo verificando che `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` esista.
* Ripetere il caricamento usando la riga di comando omsadmin.sh. Per maggiori informazioni, vedere [Onboarding using the command line](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) (Caricamento usando la riga di comando).
* Se si usa un proxy, seguire i passaggi per la risoluzione dei problemi del proxy riportati sopra
* In alcuni casi, quando l'agente OMS per Linux non può comunicare con il servizio OMS, viene eseguito il backup dei dati dell'agente alle dimensioni intere del buffer, cioè 50 MB. Riavviare l'agente OMS per Linux eseguendo il comando `/opt/microsoft/omsagent/bin/service_control restart`.
  >[AZURE.NOTE] Il problema è risolto nell'agente versione 1.1.0-28 o versioni successive.

### <a name="syslog-linux-performance-counter-configuration-is-not-applied-in-the-oms-portal"></a>La configurazione del contatore delle prestazioni Syslog per Linux non viene applicata nel portale di OMS
#### <a name="probable-causes"></a>Possibili cause
* L'agente di configurazione nell'agente OMS per Linux non ha recuperato la configurazione più recente dal portale di OMS.
* Le impostazioni modificate nel portale non sono state applicate

#### <a name="resolutions"></a>Soluzioni
`omsconfig` è l'agente di configurazione nell'agente OMS per Linux che recupera le modifiche alla configurazione del portale di OMS ogni 5 minuti. Questa configurazione viene quindi applicata ai file di configurazione dell'agente OMS per Linux disponibile su `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.

* In alcuni casi, l'agente di configurazione OMS per Linux potrebbe non essere in grado di comunicare con il servizio di configurazione del portale, di conseguenza la configurazione più recente non viene applicata.
* Verificare che l'agente `omsconfig` sia installato con le operazioni seguenti:

  * `dpkg --list omsconfig` oppure `rpm -qi omsconfig`
  * Se non è installato, reinstallare la versione più recente dell'agente OMS per Linux
* Verificare che l'agente `omsconfig` possa comunicare con il servizio OMS

  * Eseguire il comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`
    * Il comando precedente restituisce la configurazione che l'agente recupera dal portale, incluse le impostazioni di Syslog, i contatori delle prestazioni di Linux e i log personalizzati
    * Se il comando precedente ha esito negativo, eseguire il comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Questo comando forza l'agente omsconfig a comunicare con il servizio OMS per recuperare la configurazione più recente.

### <a name="custom-linux-log-data-does-not-appear-in-the-oms-portal"></a>I dati personalizzati di Linux non vengono visualizzati nel portale di OMS
#### <a name="probable-causes"></a>Possibili cause
* Il caricamento nel servizio OMS ha avuto esito negativo
* L'impostazione **Applica la configurazione seguente ai server Linux** non è stata selezionata
* omsconfig non ha selezionato il log personalizzato più recente dal portale
* L'uso di `omsagent` non consente di accedere al log personalizzato a causa di un problema con le autorizzazioni oppure non è stato possibile trovare `omsagent`. In questo caso, viene visualizzato l'output seguente:
  * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
  * `[DATETIME] [error]: file not accessible by omsagent.`
* Si tratta di un problema noto con la race condition che è stato risolto nell'agente OMS per Linux versione 1.1.0-217

#### <a name="resolutions"></a>Soluzioni
* Verificare che il caricamento sia stato eseguito correttamente determinando se il file `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` esiste.
  * Se necessario, ripetere il caricamento usando la riga di comando omsadmin.sh. Per maggiori informazioni, vedere [Onboarding using the command line](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) (Caricamento usando la riga di comando).
* In **Impostazioni** nella scheda **Dati** del portale di OMS verificare che sia selezionata l'impostazione **Applica la configurazione seguente ai server Linux**  
  ![applicazione della configurazione](./media/log-analytics-linux-agents/customloglinuxenabled.png)
* Verificare che l'agente `omsconfig` possa comunicare con il servizio OMS

  * Eseguire il comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`
  * Il comando precedente restituisce la configurazione che l'agente recupera dal portale, incluse le impostazioni di Syslog, i contatori delle prestazioni di Linux e i log personalizzati
  * Se il comando precedente ha esito negativo, eseguire il comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Questo comando forza l'agente omsconfig a comunicare con il servizio OMS e a recuperare la configurazione più recente.

Invece di eseguire l'utente dell'agente OMS per Linux come utente privilegiato `root`, viene eseguito l'agente OMS per Linux come utente `omsagent`. Nella maggior parte dei casi, è necessario che all'utente venga concessa l'autorizzazione esplicita per leggere alcuni file.

Per concedere l'autorizzazione all'utente `omsagent`, eseguire i comandi seguenti:

1. Aggiungere l'utente `omsagent` a un gruppo specifico con `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Concedere l'accesso universale in lettura al file richiesto con `sudo chmod -R ugo+rw <FILE DIRECTORY>`

È presente un problema noto con la race condition che è stato risolto nell'agente OMS per Linux versione 1.1.0-217. Dopo l'aggiornamento all'agente più recente, eseguire il comando seguente per ottenere la versione più recente del plug-in di output:

```
sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf
```

## <a name="known-limitations"></a>Limitazioni note
Per informazioni sulle limitazioni correnti per l'agente OMS per Linux, vedere le sezioni seguenti.

### <a name="azure-diagnostics"></a>Diagnostica Azure
Per le macchine virtuali Linux in esecuzione in Azure potrebbero essere necessari passaggi aggiuntivi per consentire la raccolta di dati da parte di Diagnostica di Azure e Operations Management Suite. **versione 2.2** dell'estensione di diagnostica per Linux è necessaria per la compatibilità con l'agente OMS per Linux.

Per altre informazioni sull'installazione e sulla configurazione dell'estensione di diagnostica per Linux, vedere [Usare l'interfaccia della riga di comando di Azure per abilitare l'estensione di diagnostica per Linux](../virtual-machines/linux/classic/diagnostic-extension.md#use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension).

**Aggiornamento dell'estensione di diagnostica per Linux dalla versione 2.0 alla versione 2.2 di ASM dell'interfaccia della riga di comando di Azure:**

```
azure vm extension set -u <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.0
azure vm extension set <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

**ARM**

```
azure vm extension set -u <resource-group> <vm-name> Microsoft.Insights.VMDiagnosticsSettings Microsoft.OSTCExtensions 2.0
azure vm extension set <resource-group> <vm-name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

Questi esempi di comando fanno riferimento a un file denominato PrivateConfig.json. Il formato del file deve essere analogo all'esempio seguente.

```
    {
    "storageAccountName":"the storage account to receive data",
    "storageAccountKey":"the key of the account"
    }
```

### <a name="sysklog-is-not-supported"></a>Sysklog non è supportato.
Per raccogliere i messaggi SysLog, è necessario rsyslog o syslog-ng. Il daemon SysLog predefinito nella versione 5 di Red Hat Enterprise Linux, CentOS e nella versione Oracle Linux (sysklog) non è supportato per la raccolta di eventi SysLog. Per raccogliere i dati di SysLog da questa versione delle distribuzioni, è necessario installare e configurare il daemon rsyslog in modo da sostituire sysklog. Per altre informazioni sulla sostituzione di sysklog con rsyslog, vedere la pagina relativa all' [installazione di RPM rsyslog appena compilato](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM).

## <a name="next-steps"></a>Passaggi successivi
* [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Aggiungere soluzioni di Log Analytics dalla raccolta soluzioni) per aggiungere funzionalità e raccogliere i dati.
* Acquisire familiarità con le [ricerche nei log](log-analytics-log-searches.md) per visualizzare le informazioni dettagliate raccolte dalle soluzioni.
* Usare i [dashboard](log-analytics-dashboards.md) per salvare e visualizzare le ricerche personalizzate.

