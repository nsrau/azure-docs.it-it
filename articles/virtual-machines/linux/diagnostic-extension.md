---
title: Calcolo di Azure - Estensione Diagnostica per Linux | Microsoft Docs
description: Come configurare l'estensione Diagnostica di Azure per Linux (LAD) per raccogliere le metriche e gli eventi dei registri dalle macchine virtuali Linux in esecuzione in Azure.
services: virtual-machines-linux
author: jasonzio
manager: anandram
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/09/2017
ms.author: jasonzio
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: d1efdf9b6b005852e570491aeb723a5758a4c839
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017

---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Usare l'estensione Diagnostica per Linux per monitorare le metriche e i registri

Questo documento descrive la versione 3.0 e successive dell'estensione Diagnostica per Linux.

> [!IMPORTANT]
> Per informazioni sulla versione 2.3 e precedenti, vedere [questo documento](./classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Introduzione

L'estensione Diagnostica per Linux consente all'utente di monitorare l'integrità delle macchine virtuali Linux eseguite in Microsoft Azure. Questo servizio offre le funzionalità seguenti:

* Raccoglie le metriche delle prestazioni di sistema dalla macchina virtuale e le inserisce in una tabella specifica in un account di archiviazione designato.
* Recupera gli eventi di registrazione da SysLog e li inserisce in una tabella specifica nell'account di archiviazione designato.
* Consente agli utenti di personalizzare le metriche dei dati che verranno raccolte e caricate.
* Consente agli utenti di personalizzare i servizi di SysLog e i livelli di gravità degli eventi che vengono raccolti e caricati.
* Consente agli utenti di caricare in una tabella di archiviazione designata i file di log specificati.
* Supporta l'invio delle metriche e del registro agli endpoint EventHub arbitrari e ai BLOB in formato JSON nell'account di archiviazione designato.

Questa estensione funziona con entrambi i modelli di distribuzione di Azure.

## <a name="installing-the-extension-in-your-vm"></a>Installazione dell'estensione nella macchina virtuale

È possibile abilitare questa estensione usando i cmdlet di Azure PowerShell, gli script dell'interfaccia della riga di comando di Azure o i modelli di distribuzione di Azure. Per altre informazioni, vedere [Funzionalità delle estensioni](./extensions-features.md).

Il portale di Azure non può essere usato per abilitare o configurare LAD 3.0, ma esso stesso installa e configura la versione 2.3. Gli avvisi e i grafici del portale di Azure funzionano con i dati di entrambe le versioni dell'estensione.

Le istruzioni di installazione e la [configurazione di esempio scaricabile](https://github.com/Azure/azure-linux-extensions/blob/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) configurano LAD 3.0 per:

* acquisire e archiviare le stesse metriche fornite da LAD 2.3;
* acquisire un insieme utile di metriche di file system, novità della versione 3.0 di LAD;
* acquisire la raccolta di SysLog predefinita abilitata da LAD 2.3;
* abilitare l'esperienza del portale di Azure per la creazione di grafici e avvisi relativi alle metriche della macchina virtuale.

La configurazione scaricabile è solo un esempio; modificarla per adattarla alle proprie esigenze.

### <a name="prerequisites"></a>Prerequisiti

* **Agente Linux di Azure 2.2.0 o versione successiva**. La maggior parte delle immagini della raccolta Linux di macchine virtuali di Azure include la versione 2.2.7 o successive. Eseguire `/usr/sbin/waagent -version` per verificare la versione installata nella macchina virtuale. Se la macchina virtuale esegue una versione precedente dell'agente guest, seguire [queste istruzioni](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent) per aggiornarla.
* **Interfaccia della riga di comando di Azure**. [Configurare l'ambiente dell'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) nella macchina virtuale.
* Un account di archiviazione esistente per archiviare i dati e un token per la firma di accesso condiviso associato che conceda i diritti di accesso necessari.

### <a name="sample-installation"></a>Installazione di esempio

Compilare i parametri corretti nelle prime tre righe, quindi eseguire lo script come radice:

```bash
# Set your Azure VM diagnostic parameters correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://github.com/Azure/azure-linux-extensions/blob/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 9999-12-31T23:59Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

L'URL per la configurazione di esempio e il relativo contenuto sono soggetti a modifiche. Scaricare una copia del file JSON delle impostazioni del portale e personalizzarla in base alle proprie esigenze. Qualsiasi modello o automazione creato dall'utente deve usare la propria copia, invece di scaricare l'URL ogni volta.

### <a name="updating-the-extension-settings"></a>Aggiornamento delle impostazioni di estensione

Dopo aver modificato le impostazioni pubbliche o protette, è necessario distribuirle alla macchina virtuale eseguendo lo stesso comando. Se un'impostazione viene modificata, questo aggiornamento viene inviato all'estensione. LAD ricarica la configurazione e si riavvia.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migrazione dalle versioni precedenti dell'estensione

La versione più recente dell'estensione è **3.0**. **Le versioni precedenti (2.x) sono deprecate e la loro pubblicazione potrebbe essere annullata a partire dal 31 luglio 2018**.

> [!IMPORTANT]
> Questa estensione introduce importanti modifiche alla configurazione dell'estensione. Tale modifica è stata apportata per migliorare la sicurezza dell'estensione. Di conseguenza, la compatibilità con versioni precedenti alla versione 2.x potrebbe non essere mantenuta. In aggiunta, il server di pubblicazione per questa estensione è diverso dal server di pubblicazione per le versioni 2.x.
>
> Per eseguire la migrazione dalla versione 2.x a questa nuova versione dell'estensione, è necessario disinstallare l'estensione precedente, sotto il nome del server di pubblicazione precedente, e installare la versione 3 dell'estensione.

Consigli:

* Installare l'estensione abilitando l'aggiornamento automatico della versione secondaria.
  * Nelle macchine virtuali con modello di distribuzione classico, specificare "3.*" come versione, se si installa l'estensione tramite Powershell o l'interfaccia della riga di comando XPLAT di Azure.
  * Nelle macchine virtuali con il modello di distribuzione Azure Resource Manager, includere ""autoUpgradeMinorVersion": true" nel modello di distribuzione della macchina virtuale.
* Usare un account di archiviazione nuovo o diverso per LAD 3.0. Esistono diverse piccole incompatibilità tra LAD 2.3 e LAD 3.0 che creano problemi nella condivisione di un account:
  * LAD 3.0 inserisce gli eventi SysLog in una tabella con un nome diverso.
  * Le stringhe counterSpecifier per le metriche `builtin` differiscono in LAD 3.0.

## <a name="protected-settings"></a>Impostazioni protette

Questo set di informazioni per la configurazione contiene informazioni riservate che devono essere protette dalla visualizzazione pubblica, ad esempio, le credenziali di archiviazione. Queste impostazioni vengono trasmesse e memorizzate dall'estensione in modo crittografato.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Nome | Valore
---- | -----
storageAccountName | Nome dell'account di archiviazione in cui l'estensione scrive i dati.
storageAccountEndPoint | (facoltativo) Endpoint che identifica il cloud in cui esiste l'account di archiviazione. Se questa impostazione è assente, LAD per impostazione predefinita considera il cloud pubblico di Azure, `https://core.windows.net`. Per usare un account di archiviazione in Azure Germania, Azure per enti pubblici o Azure Cina, impostare questo valore di conseguenza.
storageAccountSasToken | Un [token SAS dell'account](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) per i servizi BLOB e tabella (`ss='bt'`), applicabile a contenitori e oggetti (`srt='co'`), che concede le autorizzazioni di aggiunta, creazione, creazione di elenchi, aggiornamenti e scrittura (`sp='acluw'`). *Non* includere il punto interrogativo (?) principale.
mdsdHttpProxy | (facoltativo) Informazioni sul proxy HTTP necessarie per abilitare l'estensione affinché si connetta all'account di archiviazione e all'endpoint specificati.
sinksConfig | (facoltativo) Informazioni sulle destinazioni alternative a cui possono essere inviati le metriche e gli eventi. Nelle sezioni che seguono vengono illustrati i dettagli specifici di ogni sink di dati supportato dall'estensione.

È possibile costruire con facilità il token SAS richiesto tramite il portale di Azure.

1. Selezionare l'account di archiviazione generico che in cui si desidera che l'estensione scriva
1. Selezionare "Firma di accesso condiviso" nella sezione delle impostazioni del menu a sinistra
1. Creare le sezioni appropriate come descritto in precedenza
1. Fare clic sul pulsante "Genera firma di accesso condiviso".

![immagine](./media/diagnostic-extension/make_sas.png)

Copiare la firma di accesso condiviso generata nel campo storageAccountSasToken; rimuovere il punto interrogativo ("?") principale.

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

Questa sezione facoltativa definisce altre destinazioni a cui l'estensione invia le informazioni raccolte. La matrice "sink" contiene un oggetto per ogni sink di dati aggiuntivo. L'attributo "type" determina gli altri attributi dell'oggetto.

Elemento | Valore
------- | -----
name | Una stringa usata per fare riferimento a questo sink altrove nella configurazione dell'estensione.
type | Il tipo di sink da definire. Determina gli altri valori, se presenti, nelle istanze di questo tipo.

La versione 3.0 dell'estensione Diagnostica per Linux supporta due tipi di sink: EventHub e JsonBlob.

#### <a name="the-eventhub-sink"></a>Sink EventHub

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

La voce "sasURL" contiene l'URL completo, incluso il token della firma di accesso condiviso, per l'hub di eventi in cui devono essere pubblicati i dati. Per LAD è necessario che la firma di accesso condiviso dia un nome a un criterio che consente l'attestazione di trasmissione. Esempio:

* Creare uno spazio dei nomi dell'hub eventi denominato `contosohub`
* Creare un hub eventi nello spazio dei nomi denominato `syslogmsgs`
* Creare un criterio di accesso condiviso nell'hub eventi denominato `writer` che consenta l'attestazione di trasmissione

Se è stata creata una firma di accesso condiviso valida fino alla mezzanotte UTC del 1 gennaio 2018, il valore sasURL potrebbe essere:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Per altre informazioni sulla generazione di token SAS per gli hub eventi, vedere [questa pagina Web](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

#### <a name="the-jsonblob-sink"></a>Sink JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

I dati indirizzati a un sink JsonBlob vengono memorizzati nei BLOB dell'archiviazione di Azure. Ogni istanza di LAD crea un BLOB all'ora per ogni nome di sink. Ciascun BLOB contiene sempre una matrice JSON sintatticamente valida dell'oggetto. Le nuove voci vengono aggiunte in modo atomico alla matrice. I BLOB vengono archiviati in un contenitore con lo stesso nome come sink. Le regole di archiviazione di Azure per i nomi dei contenitori BLOB si applicano ai nomi dei sink JsonBlob: dai 3 ai 63 caratteri ASCII alfanumerici minuscoli o trattini.

## <a name="public-settings"></a>Impostazioni pubbliche

Questa struttura contiene diversi blocchi di impostazioni che controllano le informazioni raccolte dall'estensione. Tutte le impostazioni sono facoltative. Se si specifica `ladCfg`, è necessario specificare anche `StorageAccount`.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Elemento | Valore
------- | -----
StorageAccount | Nome dell'account di archiviazione in cui l'estensione scrive i dati. Deve essere lo stesso nome specificato nelle [Impostazioni protette](#protected-settings).
mdsdHttpProxy | (facoltativo) Uguale al valore indicato in [Impostazioni protette](#protected-settings). Il valore pubblico viene sostituito dal valore privato, se impostato. Inserire le impostazioni proxy che contengono un segreto, ad esempio una password, nelle [Impostazioni protette](#protected-settings).

Gli elementi rimanenti vengono descritti in dettaglio nelle sezioni seguenti.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Questa struttura facoltativa controlla la raccolta di metriche e registri per l'invio al servizio Metriche di Azure e ad altri dati sink. È necessario specificare `performanceCounters` o `syslogEvents` oppure entrambi. È necessario specificare la struttura `metrics`.

Elemento | Valore
------- | -----
eventVolume | (facoltativo) Controlla il numero di partizioni create all'interno della tabella di archiviazione. Può essere uno tra `"Large"`, `"Medium"` o `"Small"`. Se non è specificato, il valore predefinito è `"Medium"`.
sampleRateInSeconds | (facoltativo) L'intervallo predefinito tra la raccolta di metriche non elaborate, ovvero non aggregate. La frequenza di esempio più piccola supportata è 15 secondi. Se non è specificato, il valore predefinito è `15`.

#### <a name="metrics"></a>Metriche

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Elemento | Valore
------- | -----
resourceId | L'ID della risorsa di Azure Resource Manager della macchina virtuale o del set di scalabilità di macchine a cui appartiene la macchina virtuale. Questa impostazione deve essere specificata anche se nella configurazione viene usato un sink JsonBlob.
scheduledTransferPeriod | La frequenza con cui le metriche aggregate devono essere calcolate e trasferite a Metriche di Azure, espressa come un intervallo di tempo IS 8601. Il periodo di trasferimento più piccolo è 60 secondi, ovvero PT1M. È necessario specificare almeno un scheduledTransferPeriod.

Gli esempi delle metriche specificate nella sezione performanceCounters vengono raccolti ogni 15 secondi o alla frequenza di esempio definita in modo esplicito per il contatore. Se vengono visualizzate più frequenze scheduledTransferPeriod, come illustrato nell'esempio, ogni aggregazione viene calcolata in modo indipendente.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Questa sezione facoltativa consente di controllare la raccolta delle metriche. Gli esempi non elaborati vengono aggregati per ogni [scheduledTransferPeriod](#metrics) al fine di produrre questi valori:

* mean
* minimum
* maximum
* ultimo valore raccolto
* numero di esempi non elaborati usati per calcolare l'aggregazione

Elemento | Valore
------- | -----
sinks | (facoltativo) Un elenco di nomi delimitato da virgole di sink a cui LAD invia i risultati di metrica aggregati. Tutte le metriche aggregate vengono pubblicate in ogni sink elencato. Vedere [sinksConfig](#sinksconfig). Esempio: `"EHsink1, myjsonsink"`.
type | Identifica il provider effettivo della metrica.
class | Con "counter" identifica la metrica specifica all'interno dello spazio dei nomi del provider.
counter | Con "class" identifica la metrica specifica all'interno dello spazio dei nomi del provider.
counterSpecifier | Identifica la metrica specifica all'interno dello spazio dei nomi di Metriche di Azure.
condition | (facoltativo) Seleziona un'istanza specifica dell'oggetto a cui si applica la metrica oppure seleziona l'aggregazione in tutte le istanze di tale oggetto. Per ulteriori informazioni, vedere le [`builtin`definizioni delle metriche](#metrics-supported-by-builtin).
sampleRate | Intervallo IS 8601 che imposta la frequenza con cui vengono raccolti gli esempi non elaborati per questa metrica. Se non viene impostata, l'intervallo di raccolta viene impostato dal valore di [sampleRateInSeconds](#ladcfg). La frequenza di esempio più piccola supportata è 15 secondi (PT15S).
unit | Deve essere una delle seguenti stringhe: "Count", "Bytes", "Seconds", "Percent", "CountPerSecond", "BytesPerSecond", "Millisecond". Definisce l'unità per la metrica. Gli utenti dei dati raccolti prevedono che i valori dei dati raccolti corrispondano a questa unità. LAD ignora questo campo.
displayName | L'etichetta, nella lingua specificata dall'impostazione locale associata, da allegare a questi dati in Metriche di Azure. LAD ignora questo campo.

counterSpecifier è un identificatore arbitrario. Gli utenti di metriche, quali le funzionalità dei grafici del portale di Azure e gli avvisi, usano counterSpecifier come la "chiave" che identifica una metrica o un'istanza di una metrica. Per le metriche `builtin`, è consigliabile usare i valori di counterSpecifier che iniziano con `/builtin/`. Se si raccoglie un'istanza specifica di una metrica, è consigliabile allegare l'identificatore dell'istanza del valore counterSpecifier. Di seguito sono riportati alcuni esempi:

* `/builtin/Processor/PercentIdleTime` - Tempo di inattività medio calcolato per tutti i core
* `/builtin/Disk/FreeSpace(/mnt)` - Spazio libero per il file system /mnt
* `/builtin/Disk/FreeSpace` - Spazio libero medio calcolato per tutti i file system montati

Né per LAD né per il portale di Azure si prevede che il valore di counterSpecifier corrisponda a un modello. È consigliabile essere coerenti durante la creazione dei valori di counterSpecifier.

Quando si specifica `performanceCounters`, LAD scrive sempre i dati in una tabella nell'archiviazione di Azure. È possibile che gli stessi dati vengano scritti nel BLOB JSON e/o negli hub eventi, ma non è possibile disabilitare l'inserimento dei dati in una tabella. Tutte le istanze dell'estensione di diagnostica configurate per l'uso dello stesso nome per l'account di archiviazione e dello stesso endpoint aggiungono le metriche e i registri alla stessa tabella. Se troppe macchine virtuali scrivono nella stessa partizione di tabella, Azure può limitare le scritture per tale partizione. L'impostazione eventVolume fa in modo che le voci si diffondano tra 1 (Piccolo), 10 (Medio) o 100 (Grande) partizioni diverse. In genere, "Medio" è sufficiente a garantire che il traffico non venga limitato. La funzionalità Metriche di Azure del portale di Azure usa i dati in questa tabella per creare grafici o per attivare gli avvisi. Il nome della tabella è la concatenazione delle stringhe seguenti:

* `WADMetrics`
* "scheduledTransferPeriod" per i valori aggregati inseriti nella tabella
* `P10DV2S`
* Una data nel formato "AAAAMMGG", che cambia ogni 10 giorni

Gli esempi includono `WADMetricsPT1HP10DV2S20170410` e `WADMetricsPT1MP10DV2S20170609`.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Questa sezione facoltativa consente di controllare la raccolta degli eventi del registro di SysLog. Se viene omessa, gli eventi di SysLog non vengono acquisiti.

La raccolta syslogEventConfiguration ha una voce per ogni impianto di interesse di SysLog. Se minSeverity è "NONE" per un particolare impianto o se tale impianto non viene visualizzato nell'elemento, non viene acquisito alcun evento da tale impianto.

Elemento | Valore
------- | -----
sinks | Un elenco delimitato da virgole di nomi di sink in cui vengono pubblicati i singoli eventi del registro. Tutti gli eventi del registro corrispondenti alle restrizioni in syslogEventConfiguration vengono pubblicati in tutti i sink elencati. Esempio: "EHforsyslog"
facilityName | Un nome dell'impianto SysLog, ad esempio "LOG\_USER" o "LOG\_LOCAL0". Per un elenco completo vedere la sezione "impianto" della [pagina di manuale SysLog](http://man7.org/linux/man-pages/man3/syslog.3.html).
minSeverity | Un livello di gravità SysLog, ad esempio "LOG\_ERR" o "LOG\_INFO". Per un elenco completo vedere la sezione "livello" della [pagina di manuale SysLog](http://man7.org/linux/man-pages/man3/syslog.3.html). L'estensione acquisisce gli eventi inviati all'impianto con livello superiore o uguale a quello specificato.

Quando si specifica `syslogEvents`, LAD scrive sempre i dati in una tabella nell'archiviazione di Azure. È possibile che gli stessi dati vengano scritti nel BLOB JSON e/o negli hub eventi, ma non è possibile disabilitare l'inserimento dei dati in una tabella. Il comportamento del partizionamento per questa tabella è identico a quello descritto per `performanceCounters`. Il nome della tabella è la concatenazione delle stringhe seguenti:

* `LinuxSyslog`
* Una data nel formato "AAAAMMGG", che cambia ogni 10 giorni

Gli esempi includono `LinuxSyslog20170410` e `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Questa sezione facoltativa consente di controllare l'esecuzione delle query arbitrarie [OMI](https://github.com/Microsoft/omi).

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Elemento | Valore
------- | -----
namespace | (facoltativo) Lo spazio dei nomi OMI entro il quale deve essere eseguita la query. Se non viene specificato, il valore predefinito è "root/scx", implementato dai [provider multipiattaforma dei System Center](http://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation).
query | La query OMI da eseguire.
tabella | (facoltativo) La tabella di archiviazione di Azure, nell'account di archiviazione designato. Vedere [Impostazioni protette](#protected-settings).
frequency | (facoltativo) Il numero di secondi tra le esecuzioni della query. Il valore predefinito è 300, ovvero 5 minuti; il valore minimo è 15 secondi.
sinks | (facoltativo) Un elenco di nomi delimitato da virgole di sink aggiuntivi in cui pubblicare i risultati di metrica di esempio non elaborati. Nessuna aggregazione di questi esempi non elaborati viene calcolata dall'estensione o da Metriche di Azure.

È necessario specificare "table" o "sink" o entrambi.

### <a name="filelogs"></a>fileLogs

Consente di controllare l'acquisizione dei file di registro. LAD acquisisce le nuove righe di testo quando vengono scritte nel file e le scrive nelle righe della tabella e/o nei sink specificati, JsonBlob o EventHub.

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Elemento | Valore
------- | -----
file | Il percorso completo del file di registro da esaminate e acquisire. Il percorso deve indicare solo un file. Non è possibile indicare una directory o i caratteri jolly.
tabella | (facoltativo) La tabella di archiviazione di Azure, nell'account di archiviazione designato, come specificato nella configurazione protetta, in cui vengono scritte nuove righe dalla "coda" del file.
sinks | (facoltativo) Un elenco di nomi delimitato da virgole di sink aggiuntivi a cui vengono inviate le righe del registro.

È necessario specificare "table" o "sink" o entrambi.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metriche supportate dal provider Builtin

Il provider di metriche Builtin è un'origine metriche più interessante per molti utenti. Queste metriche sono suddivise in cinque grandi categorie:

* Processore
* Memoria
* Rete
* File system
* Disco

### <a name="builtin-metrics-for-the-processor-class"></a>Metriche Builtin per la classe Processore

La classe di metriche Processore offre informazioni sull'uso del processore nella macchina virtuale. Quando si aggregano le percentuali, il risultato è la media di tutte le CPU. In una macchina virtuale a due core, se un core è completamente occupato, ma l'altro è completamente inattivo, il valore di PercentIdleTime restituito sarà 50. Se ciascun core è occupato al 50% per lo stesso periodo, il risultato restituito sarà ugualmente 50. In una macchina virtuale a due core, se un core è completamente occupato, ma gli altri sono inattivo, il valore di PercentIdleTime restituito sarà 75.

counter | Significato
------- | -------
PercentIdleTime | Percentuale di tempo nella finestra di aggregazione durante la quale i processori hanno eseguito il ciclo inattivo del kernel
PercentProcessorTime | Percentuale di tempo di esecuzione di un thread non inattivo
PercentIOWaitTime | Percentuale di tempo di attesa per il completamento delle operazioni di input/output
PercentInterruptTime | Percentuale di tempo per l'esecuzione di DPC, ovvero chiamate di procedura differite, e interruzioni di hardware o software
PercentUserTime | Relativamente al tempo di inattività nella finestra di aggregazione, la percentuale di tempo impiegato dall'utente con priorità normale
PercentNiceTime | Relativamente al tempo di inattività, la percentuale di tempo impiegata a bassa priorità (interessante)
PercentPrivilegedTime | La percentuale di tempo di inattività impiegata in modalità privilegiata (kernel)

La somma dei primi quattro contatori deve essere 100%. Anche la somma degli ultimi tre contatori deve essere 100%: vengono sommati i valori di PercentProcessorTime, PercentIOWaitTime e PercentInterruptTime.

Per ottenere un'unica metrica aggregata per tutti i processori, impostare `"condition": "IsAggregate=TRUE"`. Per ottenere una metrica per un processore specifico, ad esempio per il secondo processore logico di una macchina virtuale a quattro core, impostare `"condition": "Name=\\"1\\""`. I valori del processore logico sono compresi nell'intervallo `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>Metriche Builtin per la classe Memoria

La classe di memoria delle metriche contiene informazioni sull'uso della memoria, il paging e lo scambio.

counter | Significato
------- | -------
AvailableMemory | Memoria fisica disponibile in MiB
PercentAvailableMemory | Percentuale della memoria totale che indica la memoria fisica disponibile
UsedMemory | Memoria fisica in uso (MiB)
PercentUsedMemory | Percentuale della memoria totale che indica la memoria fisica in uso
PagesPerSec | Paging totale (lettura/scrittura)
PagesReadPerSec | Pagine lette dall'archivio di backup, ad esempio file di scambio, file di programma, file mappato e così via.
PagesWrittenPerSec | Pagine scritte nell'archivio di backup, ad esempio file di scambio, file mappato e così via.
AvailableSwap | Spazio di swapping inutilizzato (MiB)
PercentAvailableSwap | Percentuale dello swapping totale che indica lo spazio di swapping inutilizzato
UsedSwap | Spazio di swapping in uso (MiB)
PercentUsedSwap | Percentuale dello swapping totale che indica lo spazio di swapping in uso

Questa classe di metriche presenta una singola istanza. L'attributo "condition" non presenta impostazioni utili e deve essere omesso.

### <a name="builtin-metrics-for-the-network-class"></a>Metriche Builtin per la classe Rete

La classe di metriche Rete contiene informazioni sulle attività di rete su un interfacce di rete singole sin dall'avvio. LAD non espone le metriche relative alla larghezza di banda, che possono essere recuperate dalle metriche dell'host.

counter | Significato
------- | -------
BytesTransmitted | Byte totali inviati sin dall'avvio
BytesReceived | Byte totali ricevuti sin dall'avvio
BytesTotal | Byte totali inviati o ricevuti sin dall'avvio
PacketsTransmitted | Pacchetti totali inviati sin dall'avvio
PacketsReceived | Pacchetti totali ricevuti sin dall'avvio
TotalRxErrors | Numero di errori di ricezione sin dall'avvio
TotalTxErrors | Numero di errori di trasmissione sin dall'avvio
TotalCollisions | Numero di conflitti segnalati tramite le porte di rete sin dall'avvio

 Anche se questa classe presenta un'istanza, LAD non supporta l'acquisizione delle metriche di rete aggregate per tutti i dispositivi di rete. Per ottenere le metriche per un'interfaccia specifica, ad esempio eth0, impostare `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>Metriche Builtin per la classe File system

La classe di metriche File system contiene informazioni sull'uso del file system. I valori assoluti e in percentuale vengono segnalati come fossero visualizzati da utenti normali, non radice.

counter | Significato
------- | -------
FreeSpace | Spazio disponibile su disco in byte
UsedSpace | Spazio su disco usato in byte
PercentFreeSpace | Percentuale di spazio libero
PercentUsedSpace | Percentuale di spazio usato
PercentFreeInodes | Percentuale di inode non usati
PercentUsedInodes | Percentuale di inode allocati, ovvero in uso, sommati tra tutti i file System
BytesReadPerSecond | Byte letti per secondo
BytesWrittenPerSecond | Byte scritti per secondo
Byte al secondo | Byte letti o scritti per secondo
ReadsPerSecond | Operazioni di lettura per secondo
WritesPerSecond | Operazioni di scrittura per secondo
TransfersPerSecond | Operazioni di lettura o scrittura per secondo

È possibile ottenere i valori aggregati in tutti i file System impostando `"condition": "IsAggregate=True"`. È possibile ottenere i valori per un determinato file system montato, ad esempio "/mnt", può essere ottenuto impostando `"condition": 'Name="/mnt"'`.

### <a name="builtin-metrics-for-the-disk-class"></a>Metriche Builtin per la classe Disco

La classe di metriche Disco contiene informazioni sull'uso del dispositivo del disco. Queste statistiche si applicano all'intera unità. Se sono presenti più file System su un dispositivo, i contatori per il dispositivo sono aggregati in modo efficace per tutti loro.

counter | Significato
------- | -------
ReadsPerSecond | Operazioni di lettura per secondo
WritesPerSecond | Operazioni di scrittura per secondo
TransfersPerSecond | Operazioni totali per secondo
AverageReadTime | Media di secondi per operazione di lettura
AverageWriteTime | Media di secondi per operazione di scrittura
AverageTransferTime | Media di secondi per operazione
AverageDiskQueueLength | Media delle operazioni del disco in coda
ReadBytesPerSecond | Numero di byte letti al secondo
WriteBytesPerSecond | Numero di byte scritti al secondo
Byte al secondo | Numero di byte letti o scritti al secondo

È possibile ottenere i valori aggregati per tutti i dischi impostando `"condition": "IsAggregate=True"`. Per ottenere le informazioni per un dispositivo specifico, ad esempio dev/sdf1, impostare `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Installazione e configurazione di LAD 3.0 tramite l'interfaccia della riga di comando

Supponendo che le impostazioni protette si trovino nel file PrivateConfig.json e che le informazioni di configurazione pubbliche si trovino in PublicConfig.json, eseguire questo comando:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

Il comando presuppone che si stia usando la modalità di Gestione risorse di Azure (arm) dell'interfaccia della riga di comando di Azure. Per configurare LAD per le macchine virtuali che usano il modello di distribuzione classico (ASM), passare alla modalità "asm" (`azure config mode asm`) e omettere il nome del gruppo di risorse nel comando. Per altre informazioni, vedere la [documentazione sull'interfaccia della riga di comando multipiattaforma](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Una configurazione di LAD 3.0 di esempio

In base alle definizioni precedenti, ecco una configurazione dell'estensione 3.0 LAD di esempio con alcune spiegazioni. Per applicare questo esempio al caso in questione, è necessario usare il nome dell'account di archiviazione, il token SAS dell'account e i token SAS di EventHubs.

### <a name="privateconfigjson"></a>PrivateConfig.json

Queste impostazioni private consentono di configurare:

* un account di archiviazione
* un token SAS dell'account corrispondente
* diversi sink, ad esempio JsonBlob o EventHubs con i token SAS

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="publicconfigjson"></a>PublicConfig.json

Con queste impostazioni pubbliche il LAD:

* carica le metriche della percentuale di tempo del processore e dello spazio sul disco usato nella tabella `WADMetrics*`
* carica i messaggi delle "informazioni" sull'"utente" e sulla gravità dell'impianto SysLog nella tabella `LinuxSyslog*`
* carica i risultati delle query OMI non elaborati, ovvero PercentProcessorTime e PercentIdleTime, nella tabella denominata `LinuxCPU`
* carica le righe aggiunte nel file `/var/log/myladtestlog` nella tabella `MyLadTestLog`

In ogni caso, i dati vengono anche caricati:

* nell'archivio BLOB di Azure, il nome del contenitore è definito nel sink JsonBlob
* nell'endopint EventHubs, come specificato nel sink EventHubs

```json
{
  "StorageAccount": "yourdiagstgacct",
  "sampleRateInSeconds": 15,
  "ladCfg": {
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

Nella configurazione `resourceId` deve corrispondere al valore della macchina virtuale o al valore del set di scalabilità della macchina virtuale.

* I valori di resourceId della macchina virtuale in funzione sono noti a grafici e avvisi relativi alle metriche della piattaforma di Azure. Si prevede di trovare i dati della macchina virtuale tramite la chiave di ricerca resourceId.
* Se si usa la scalabilità automatica di Azure, il valore di resourceId nella configurazione di scalabilità automatica deve corrispondere a quello di resourceId usato da LAD.
* Il valore di resourceId viene creato nei nomi di JsonBlobs scritto da LAD.

## <a name="view-your-data"></a>Visualizzare i dati

Usare il portale di Azure per visualizzare i dati sulle prestazioni o impostare gli avvisi:

![immagine](./media/diagnostic-extension/graph_metrics.png)

I dati `performanceCounters` sono sempre archiviati in una tabella di archiviazione di Azure. Le API di Archiviazione di Azure sono disponibili per più linguaggi e piattaforme.

I dati inviati ai sink JsonBlob sono archiviati nei BLOB nell'account di archiviazione indicato in [Impostazioni protette](#protected-settings). È possibile usare i dati BLOB usando qualsiasi API di archiviazione BLOB di Azure.

È anche possibile usare questi strumenti dell'interfaccia utente per accedere ai dati nell'archiviazione di Azure:

* Esplora server di Visual Studio.
* [Microsoft Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/ "Microsoft Azure Storage Explorer").

Questo snapshot di una sessione di Microsoft Azure Storage Explorer mostra le tabelle di archiviazione di Azure e i contenitori generati da un'estensione LAD 3.0 correttamente configurata su una macchina virtuale di test. L'immagine non corrisponde esattamente alla [configurazione LAD 3.0 di esempio](#an-example-lad-30-configuration).

![immagine](./media/diagnostic-extension/stg_explorer.png)

Vedere la relativa [documentazione di EventHubs](../../event-hubs/event-hubs-what-is-event-hubs.md) per avere informazioni su come usare i messaggi pubblicati in un endpoint EventHubs.

## <a name="next-steps"></a>Passaggi successivi

* Creare avvisi sulle metriche in [Monitoraggio di Azure](../../monitoring-and-diagnostics/insights-alerts-portal.md) per le metriche raccolte.
* Creare [grafici di monitoraggio](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) per le metriche.
* Informazioni su come [creare un set di scalabilità di macchine virtuali](/azure/virtual-machines/linux/tutorial-create-vmss) con le metriche per controllare la scalabilità automatica.

