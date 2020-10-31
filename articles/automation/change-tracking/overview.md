---
title: Panoramica di Automazione di Azure - Rilevamento modifiche e inventario
description: Questo articolo descrive la funzionalità di Rilevamento modifiche e inventario, che consente di identificare le modifiche al software e al servizio Microsoft nell'ambiente in uso.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/26/2020
ms.topic: conceptual
ms.openlocfilehash: 35b1b07bd4d9eef555be26e1c24356b7c352bf38
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100695"
---
# <a name="change-tracking-and-inventory-overview"></a>Panoramica di Rilevamento modifiche e inventario

Questo articolo descrive la funzionalità di Rilevamento modifiche e inventario in Automazione di Azure. Questa funzionalità consente di tenere traccia delle modifiche apportate alle macchine virtuali ospitate in Azure, in locale e in altri ambienti cloud per individuare i problemi operativi e ambientali del software gestito da Distribution Package Manager. Gli elementi rilevati da Rilevamento modifiche e inventario includono:

- Software Windows
- Software Linux (pacchetti)
- File Windows e Linux
- Chiavi del Registro di sistema di Windows
- Servizi Microsoft
- Daemon Linux

> [!NOTE]
> Per tenere traccia delle modifiche apportate alle proprietà di Azure Resource Manager, vedere il grafico [cronologia modifiche](../../governance/resource-graph/how-to/get-resource-changes.md) di Azure Resource Graph.

Rilevamento modifiche e l'inventario usano il [monitoraggio dell'integrità dei file del Centro sicurezza di Azure](../../security-center/security-center-file-integrity-monitoring.md) per esaminare i file del sistema operativo e delle applicazioni e il registro di sistema di Windows. Mentre FIM monitora tali entità, Rilevamento modifiche e l'inventario tiene traccia in modo nativo:

- Modifiche software
- Servizi Microsoft
- Daemon Linux

L'abilitazione di tutte le funzionalità incluse in Rilevamento modifiche e nell'inventario può causare addebiti aggiuntivi. Prima di procedere, esaminare i prezzi di [automazione](https://azure.microsoft.com/pricing/details/automation/) e i [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). 

Rilevamento modifiche e l'inventario inviano i dati ai log di monitoraggio di Azure e i dati raccolti vengono archiviati in un'area di lavoro di Log Analytics. La funzionalità di monitoraggio dell'integrità dei file (FIM) è disponibile solo quando **Azure Defender per i server** è abilitato. Per altre informazioni, vedere [prezzi](../../security-center/security-center-pricing.md) del Centro sicurezza di Azure. FIM carica i dati nella stessa area di lavoro Log Analytics come quello creato per archiviare i dati da Rilevamento modifiche e da inventario. Si consiglia di monitorare l'area di lavoro di Log Analytics collegata per tenere traccia dell'utilizzo esatto. Per altre informazioni sull'analisi dell'utilizzo dei dati dei log di monitoraggio di Azure, vedere [gestire l'utilizzo e i costi](../../azure-monitor/platform/manage-cost-storage.md).

I computer connessi all'area di lavoro Log Analytics usano l' [agente log Analytics](../../azure-monitor/platform/log-analytics-agent.md) per raccogliere dati sulle modifiche al software installato, ai servizi Microsoft, al registro di sistema di Windows e ai file e ai Daemon Linux nei server monitorati. Quando i dati sono disponibili, l'agente li invia ai log di monitoraggio di Azure per l'elaborazione. Log di monitoraggio di Azure applica la logica ai dati ricevuti, li registra e li rende disponibili per l'analisi.

> [!NOTE]
> Rilevamento modifiche e inventario richiedono il collegamento di un'area di lavoro Log Analytics all'account di automazione. Per un elenco completo delle aree supportate, vedere [Mapping dell'area di lavoro di Azure](../how-to/region-mappings.md). I mapping a livello di area non influiscono sulla possibilità di gestire le VM in un'area separata rispetto all'account di Automazione.

## <a name="current-limitations"></a>Limitazioni correnti

Rilevamento modifiche e l'inventario non supportano o presenta le limitazioni seguenti:

- Ricorsione per Rilevamento del Registro di sistema di Windows
- File system di rete
- Metodi di installazione diversi
- *file con *_estensione exe_* archiviati in Windows
- La colonna **Dimensioni massime file** e i valori non sono usati nell'implementazione corrente.
- Se si tenta di raccogliere più di 2500 file in un ciclo di raccolta di 30 minuti, è possibile che le prestazioni Rilevamento modifiche e inventario risultino ridotte.
- Se il traffico di rete è elevato, i record delle modifiche possono richiedere fino a sei ore per la visualizzazione.
- Se si modifica una configurazione mentre un computer o un server viene arrestato, è possibile che vengano pubblicate modifiche che appartengono alla configurazione precedente.
- Raccolta degli aggiornamenti degli hotfix nei computer RS3 Core di Windows Server 2016.
- I daemon Linux possono mostrare uno stato modificato anche se non si è verificata alcuna modifica. Questo problema si verifica a causa del modo `SvcRunLevels` in cui vengono scritti i dati nella tabella [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) di monitoraggio di Azure.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

La funzionalità Rilevamento modifiche e inventario è supportata in tutti i sistemi operativi che soddisfano i requisiti degli agenti di Log Analytics. Per un elenco delle versioni del sistema operativo Windows e Linux attualmente supportate dall'agente di Log Analytics, vedere [sistemi operativi supportati](../../azure-monitor/platform/agents-overview.md#supported-operating-systems) .

Per informazioni sui requisiti dei client per TLS 1,2, vedere [tls 1,2 Enforcement for Azure Automation](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="network-requirements"></a>Requisiti di rete

Gli indirizzi seguenti sono necessari in modo specifico per Rilevamento modifiche e l'inventario. La comunicazione verso questi indirizzi avviene sulla porta 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

Quando si creano regole di sicurezza del gruppo di rete o si configura il firewall di Azure per consentire il traffico verso il servizio di automazione e l'area di lavoro Log Analytics, usare il [tag di servizio](../../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** e **AzureMonitor** . Ciò semplifica la gestione continuativa delle regole di sicurezza di rete. Per connettersi al servizio di automazione dalle macchine virtuali di Azure in modo sicuro e privato, vedere [usare il collegamento privato di Azure](../how-to/private-link-security.md). Per ottenere il tag di servizio e le informazioni sull'intervallo correnti da includere come parte delle configurazioni del firewall locali, vedere [file JSON scaricabili](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

## <a name="enable-change-tracking-and-inventory"></a>Abilitare il rilevamento delle modifiche e l'inventario

È possibile abilitare Rilevamento modifiche e l'inventario nei modi seguenti:

- Dall' [account di automazione](enable-from-automation-account.md) per uno o più computer Azure e non Azure.

- Manualmente per i computer non Azure, inclusi i computer o i server registrati con i [server abilitati per Azure Arc](../../azure-arc/servers/overview.md). Per le macchine ibride, è consigliabile installare l'agente di Log Analytics per Windows connettendo il computer ai [server abilitati per Azure Arc](../../azure-arc/servers/overview.md)e quindi usando i criteri di Azure per assegnare i criteri predefiniti [Distribuisci agente di log Analytics a computer *Linux* o *Windows* Azure Arc](../../governance/policy/samples/built-in-policies.md#monitoring) . Se si prevede di monitorare anche i computer con Monitoraggio di Azure per le macchine virtuali, usare invece l'iniziativa [abilita monitoraggio di Azure per le macchine virtuali](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

- Per una singola VM di Azure dalla [pagina della macchina virtuale](enable-from-vm.md) nella portale di Azure. Questo scenario è disponibile per macchine virtuali Linux e Windows.

- Per [più VM di Azure](enable-from-portal.md) mediante la selezione delle VM dalla pagina Macchina virtuale nel portale di Azure.

## <a name="tracking-file-changes"></a>Rilevamento delle modifiche ai file

Per tenere traccia delle modifiche nei file in Windows e Linux, Rilevamento modifiche e inventa usano gli hash MD5 dei file. La funzionalità usa gli hash per rilevare se sono state apportate modifiche dall'ultimo inventario.

## <a name="tracking-file-content-changes"></a>Rilevamento delle modifiche al contenuto del file

Rilevamento modifiche e Inventory consente di visualizzare il contenuto di un file Windows o Linux. Per ogni modifica apportata a un file, Rilevamento modifiche e inventario archivia il contenuto del file in un [account di Archiviazione di Azure](/azure/storage/common/storage-account-create). Quando si esegue il rilevamento di un file, è possibile visualizzarne il contenuto prima o dopo una modifica. Il contenuto del file può essere visualizzato inline o affiancato.

![Visualizzare le modifiche in un file](./media/overview/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Rilevamento delle chiavi del Registro di sistema

Rilevamento modifiche e Inventory consente il monitoraggio delle modifiche alle chiavi del registro di sistema di Windows. Il monitoraggio consente di individuare i punti di estendibilità in cui è possibile attivare codice e malware di terze parti. Nella tabella seguente sono elencate le chiavi del Registro di sistema preconfigurate, ma non abilitate. Per tenere traccia di queste chiavi, è necessario abilitarle.

> [!div class="mx-tdBreakAll"]
> |Chiave del Registro di sistema | Scopo |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitora gli script eseguiti all'avvio.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitora gli script eseguiti all'arresto del sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitora le chiavi caricate prima dell'accesso degli utenti nel proprio account di Windows. La chiave viene usata per le applicazioni a 32 bit in esecuzione su computer a 64 bit.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitora le modifiche apportate alle impostazioni dell'applicazione.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitora i gestori dei menu di scelta rapida che si collegano direttamente in Esplora risorse e che in genere vengono eseguiti in-process con **explorer.exe** .
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitora i gestori di hook di copia che si collegano direttamente in Esplora risorse e che in genere vengono eseguiti in-process con **explorer.exe** .
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora la registrazione del gestore della sovrapposizione delle icone.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora la registrazione del gestore delle immagini sovrapposte alle icone per le applicazioni a 32 bit in esecuzione in computer a 64 bit.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora i nuovi plug-in dell'oggetto browser helper per Internet Explorer. Usati per accedere al DOM (Document Object Model) della pagina corrente e per controllare la navigazione.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora i nuovi plug-in dell'oggetto browser helper per Internet Explorer. Usati per accedere al DOM (Document Object Model) della pagina corrente e per controllare la navigazione per le applicazioni a 32 bit in esecuzione su computer a 64 bit.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitora le nuove estensioni di Internet Explorer, come i menu degli strumenti personalizzati e i pulsanti della barra degli strumenti personalizzata.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitora le nuove estensioni di Internet Explorer, come i menu degli strumenti personalizzati e i pulsanti della barra degli strumenti personalizzati per le applicazioni a 32 bit in esecuzione in computer a 64 bit.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora i driver a 32 bit associati con wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. È simile alla sezione [driver] nel file **system.ini** .
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora i driver a 32 bit associati con wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc per le applicazioni a 32 bit in esecuzione in computer a 64 bit. È simile alla sezione [driver] nel file **system.ini** .
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitora l'elenco delle DLL di sistema note o comunemente usate. Il monitoraggio impedisce agli utenti di sfruttare le autorizzazioni vulnerabili per le directory applicative eliminando le versioni Trojan Horse delle DLL di sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitora l'elenco dei pacchetti che possono ricevere notifiche degli eventi da **winlogon.exe** , il modello di supporto per l'accesso interattivo per Windows.

## <a name="recursion-support"></a>Supporto della ricorsione

Rilevamento modifiche e inventario supporta la ricorsione, che consente di specificare caratteri jolly per semplificare il rilevamento tra le directory. La ricorsione fornisce anche le variabili di ambiente che consentono di tenere traccia dei file in ambienti con più nomi di unità o nomi di unità dinamici. Nell'elenco seguente sono incluse le informazioni comuni che è necessario tenere presente durante la configurazione della ricorsione:

- I caratteri jolly sono necessari per il rilevamento di più file.

- È possibile usare i caratteri jolly solo nell'ultimo segmento di un percorso di file, ad esempio, **c:\cartella \\ file** _ o _ */etc/* . conf * *.

- Se a una variabile di ambiente è associato ha un percorso non valido, la convalida ha esito positivo, ma il percorso non restituisce errore durante l'esecuzione.

- Quando si imposta il percorso, è consigliabile evitare i nomi di percorso generale, perché questo tipo di impostazione può causare l'attraversamento di un numero eccessivo di cartelle.

## <a name="change-tracking-and-inventory-data-collection"></a>Raccolta dati di Rilevamento modifiche e inventario

La tabella seguente mostra la frequenza di raccolta dei dati per i tipi di modifiche supportate da Rilevamento modifiche e inventario. Per ogni tipo, lo snapshot dei dati dello stato corrente viene aggiornato almeno ogni 24 ore.

| **Tipo di modifica** | **Frequenza** |
| --- | --- |
| Registro di sistema di Windows | 50 minuti |
| File Windows | 30 minuti |
| File Linux | 15 minuti |
| Servizi Microsoft | Da 10 secondi a 30 minuti</br> Predefinito: 30 minuti |
| Daemon Linux | 5 minuti |
| Software Windows | 30 minuti |
| Software Linux | 5 minuti |

La tabella seguente illustra i limiti dell'elemento di rilevamento per ogni macchina per Rilevamento modifiche e inventario.

| **Risorsa** | **Limite** |
|---|---|---|
|File|500|
|Registro|250|
|Software Windows (esclusi gli hotfix) |250|
|Pacchetti Linux|1250|
|Servizi|250|
|Daemon|250|

L'uso medio dei dati di Log Analytics per un computer che usa Rilevamento modifiche e inventario è di circa 40 MB al mese, in base all'ambiente. Con la funzionalità utilizzo e costi stimati dell'area di lavoro Log Analytics, è possibile visualizzare i dati inseriti da Rilevamento modifiche e inventario in un grafico di utilizzo. Questa vista dati consente di valutare l'utilizzo dei dati e di determinare il modo in cui influiscono sulla fattura. Vedere [Comprendere l'utilizzo e stimare i costi](/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs).

### <a name="microsoft-service-data"></a>Dati del servizio Microsoft

La frequenza di raccolta predefinita per i servizi Microsoft è pari a 30 minuti. È possibile configurare la frequenza usando un dispositivo di scorrimento nella scheda **Servizi Microsoft** in **Modifica impostazioni** .

![Dispositivo di scorrimento per i servizi Microsoft](./media/overview/windowservices.png)

Per ottimizzare le prestazioni, l'agente di Log Analytics tiene traccia solo delle modifiche. L'impostazione di una soglia elevata potrebbe non essere modificata se il servizio torna allo stato originale. L'impostazione della frequenza su un valore inferiore consente di rilevare modifiche che altrimenti potrebbero essere perse.

> [!NOTE]
> Anche se l'agente può rilevare le modifiche in un intervallo di 10 secondi, i dati richiedono alcuni minuti per essere visualizzati nel portale di Azure. Le modifiche che si verificano durante il periodo di visualizzazione vengono comunque rilevate e registrate.

## <a name="support-for-alerts-on-configuration-state"></a>Supporto per gli avvisi sullo stato di configurazione

Una funzionalità chiave di Rilevamento modifiche e inventario è costituita da avvisi sulle modifiche allo stato di configurazione dell'ambiente ibrido. Sono disponibili molte azioni utili da attivare in risposta agli avvisi. Ad esempio, azioni su funzioni di Azure, manuali operativi di automazione, webhook e così via. Gli avvisi relativi alle modifiche apportate al file **c:\windows\system32\drivers\etc\hosts** per un computer sono un'applicazione efficace di avvisi per i dati di rilevamento modifiche e di inventario. Sono disponibili anche molti altri scenari per gli avvisi, inclusi gli scenari di query definiti nella tabella seguente.

|Query  |Descrizione  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|Utile per tenere traccia delle modifiche ai file di sistema critici.|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Utile per tenere traccia delle modifiche ai file di configurazione importanti.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|Utile per tenere traccia delle modifiche ai servizi di sistema critici.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState!= "Running"|Utile per tenere traccia delle modifiche ai servizi di sistema critici.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|Utile per gli ambienti che richiedono il blocco delle configurazioni software.|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion!= "8.0.11081.0"|Utile per visualizzare i computer in cui è installata una versione del software obsoleta o non conforme. Questa query segnala l'ultimo stato della configurazione indicato, ma non segnala le modifiche.|
|ConfigurationChange <br>&#124; where RegistryKey == @"HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Utile per tenere traccia delle modifiche alle chiavi antivirus di importanza cruciale.|
|ConfigurationChange <br>&#124; where RegistryKey contains @"HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| Utile per tenere traccia delle modifiche alle impostazioni del firewall.|

## <a name="next-steps"></a>Passaggi successivi

- Per abilitare da un account di automazione, vedere [abilitare rilevamento modifiche e inventario da un account di automazione](enable-from-automation-account.md).

- Per abilitare dal portale di Azure, vedere [abilitare rilevamento modifiche e inventario dal portale di Azure](enable-from-portal.md).

- Per abilitare da un Runbook, vedere [abilitare rilevamento modifiche e inventario da un Runbook](enable-from-runbook.md).

- Per abilitare da una macchina virtuale di Azure, vedere [abilitare rilevamento modifiche e inventario da una macchina virtuale di Azure](enable-from-vm.md).
