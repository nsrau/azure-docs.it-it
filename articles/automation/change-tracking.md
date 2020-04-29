---
title: Panoramica di Rilevamento modifiche e inventario in automazione di Azure
description: Rilevamento modifiche e l'inventario consentono di identificare le modifiche al software e al servizio Microsoft che si verificano nell'ambiente in uso.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: 1208e08f7b85e893ba754bdbdf71a2da4f68c90a
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509067"
---
# <a name="overview-of-change-tracking-and-inventory"></a>Panoramica di Rilevamento modifiche e inventario

Questo articolo presenta Rilevamento modifiche e l'inventario in automazione di Azure. Questa funzionalità consente di tenere traccia delle modifiche apportate alle macchine virtuali e all'infrastruttura server che consentono di individuare i problemi operativi e ambientali del software gestito da Distribution Package Manager. Gli elementi che vengono rilevati da Rilevamento modifiche e dall'inventario includono: 

- Software Windows
- Software Linux (pacchetti)
- File Windows e Linux
- Chiavi del registro di sistema di Windows
- Servizi Microsoft
- Daemon Linux

Rilevamento modifiche e l'inventario ottengono i dati dal servizio monitoraggio di Azure nel cloud. Azure invia le modifiche al software installato, ai servizi Microsoft, ai file e al registro di sistema di Windows e ai Daemon Linux nei server monitorati in monitoraggio di Azure per l'elaborazione. Il servizio cloud applica la logica ai dati ricevuti, li registra e li rende disponibili. 

> [!NOTE]
> Per tenere traccia delle modifiche apportate alle proprietà Azure Resource Manager, vedere [cronologia](../governance/resource-graph/how-to/get-resource-changes.md)delle modifiche di Azure Resource Graph.

Rilevamento modifiche e l'inventario attualmente non supportano gli elementi seguenti:

* Ricorsione per Rilevamento del Registro di sistema di Windows
* Filesystem di rete
* Metodi di installazione diversi
* *file **exe** per Windows

Altre limitazioni:

* La colonna **Dimensioni massime file** e i valori non sono usati nell'implementazione corrente.
* Se si raccolgono più di 2500 file in un ciclo di raccolta di 30 minuti, è possibile che le prestazioni della soluzione risultino ridotte.
* Quando il traffico di rete è elevato, i record delle modifiche possono richiedere fino a sei ore per la visualizzazione.
* Se si modifica una configurazione mentre un computer viene arrestato, il computer potrebbe registrare le modifiche appartenenti alla configurazione precedente.

Attualmente Rilevamento modifiche e l'inventario si verificano i seguenti problemi:

* Gli aggiornamenti rapidi non vengono raccolti nei computer Windows Server 2016 Core RS3.
* I Daemon Linux potrebbero mostrare uno stato modificato anche se non si è verificata alcuna modifica. Questo problema si verifica a causa del modo in `SvcRunLevels` cui vengono acquisiti i dati nel log [ConfigurationChange](https://docs.microsoft.com/azure/azure-monitor/reference/tables/configurationchange) di monitoraggio di Azure.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Rilevamento modifiche e l'inventario e gli agenti Log Analytics di monitoraggio di Azure sono supportati nei sistemi operativi Windows e Linux.

### <a name="windows-operating-systems"></a>Sistemi operativi Windows

La versione del sistema operativo Windows supportata ufficialmente è Windows Server 2008 R2 o versione successiva.

### <a name="linux-operating-systems"></a>Sistemi operativi Linux

Le distribuzioni di Linux descritte di seguito sono ufficialmente supportate per l'agente Log Analytics per Linux. È tuttavia possibile che l'agente Linux sia eseguito in altre distribuzioni non elencate. Se non diversamente indicato, tutte le versioni minori sono supportate per ogni versione principale elencata.

#### <a name="64-bit-linux-operating-systems"></a>sistemi operativi Linux a 64 bit

* CentOS 6 e 7
* Amazon Linux 2017.09
* Oracle Linux 6 e 7
* Red Hat Enterprise Linux Server 6 e 7
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
* SUSE Linux Enterprise Server 12

#### <a name="32-bit-linux-operating-systems"></a>sistemi operativi Linux a 32 bit

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS e 16.04 LTS

## <a name="network-requirements"></a>Requisiti di rete

Rilevamento modifiche e l'inventario richiedono specificamente gli indirizzi di rete elencati nella tabella seguente. Per le comunicazioni con questi indirizzi viene utilizzata la porta 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>Interfaccia utente di Rilevamento modifiche e inventario

Utilizzare Rilevamento modifiche e inventario nel portale di Azure per visualizzare il riepilogo delle modifiche per i computer monitorati. La funzionalità è disponibile selezionando **rilevamento modifiche** in **Gestione configurazione** nell'account di automazione. 

![Dashboard Rilevamento modifiche](./media/change-tracking/change-tracking-dash01.png)

Gli elenchi a discesa sono disponibili nella parte superiore del dashboard per limitare il grafico del rilevamento delle modifiche e informazioni dettagliate in base al tipo di modifica e agli intervalli di tempo. È anche possibile fare clic sul grafico e trascinare la selezione per selezionare un intervallo di tempo personalizzato. 

È possibile fare clic su una modifica o un evento per visualizzare i relativi dettagli. I tipi di modifica disponibili sono:

* Eventi
* DAEMON
* File
* Registro
* Software
* Servizi Microsoft

È possibile aggiungere, modificare o rimuovere ogni modifica. Nell'esempio seguente, è possibile visualizzare una modifica nel tipo di avvio di un servizio da manuale a automatico.

![Dettagli Rilevamento modifiche](./media/change-tracking/change-tracking-details.png)

## <a name="tracking-of-file-changes"></a>Rilevamento delle modifiche ai file

Per tenere traccia delle modifiche nei file in Windows e Linux, Rilevamento modifiche e l'inventario usano gli hash MD5 dei file. La funzionalità utilizza gli hash per rilevare se sono state apportate modifiche dall'ultimo inventario.

## <a name="tracking-of-file-content-changes"></a>Rilevamento delle modifiche al contenuto del file

Rilevamento modifiche e Inventory consente di visualizzare il contenuto di un file Windows o Linux prima e dopo una modifica di file. Per ogni modifica apportata a un file, Rilevamento modifiche e inventario archivia il contenuto del file in un [account di archiviazione di Azure](../storage/common/storage-create-storage-account.md). Quando si esegue il rilevamento del file, è possibile visualizzarne il contenuto prima o dopo una modifica. È possibile visualizzare il contenuto inline o affiancato. 

![Visualizzare le modifiche in un file](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Rilevamento delle chiavi del registro di sistema

Rilevamento modifiche e inventario consentono il monitoraggio delle modifiche apportate alle chiavi del registro di sistema. Il monitoraggio consente di individuare i punti di estendibilità in cui è possibile attivare codice e malware di terze parti. Nella tabella seguente sono elencate le chiavi del registro di sistema preconfigurate, ma non abilitate. Per tenere traccia di queste chiavi, è necessario abilitarle.

> [!div class="mx-tdBreakAll"]
> |Chiave del Registro | Scopo |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitora le voci di avvio automatico comuni che si collegano direttamente in Esplora risorse e che in genere vengono eseguite in-process con **Explorer. exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitora gli script eseguiti all'avvio.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitora gli script eseguiti all'arresto del sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitora le chiavi che vengono caricate prima che l'utente acceda all'account di Windows. La chiave viene usata per le applicazioni a 32 bit in esecuzione su computer a 64 bit.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitora le modifiche apportate alle impostazioni dell'applicazione.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitora le voci di avvio automatico comuni che si collegano direttamente in Esplora risorse e che in genere vengono eseguite in-process con **Explorer. exe**.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitora le voci di avvio automatico comuni che si collegano direttamente in Esplora risorse e che in genere vengono eseguite in-process con **Explorer. exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora la registrazione del gestore della sovrapposizione delle icone.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora la registrazione del gestore della sovrapposizione delle icone per le applicazioni a 32 bit in esecuzione su computer a 64 bit.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora i nuovi plug-in dell'oggetto browser helper per Internet Explorer. Usati per accedere al DOM (Document Object Model) della pagina corrente e per controllare la navigazione.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora i nuovi plug-in dell'oggetto browser helper per Internet Explorer. Usato per accedere al Document Object Model (DOM) della pagina corrente e per controllare la navigazione per le applicazioni a 32 bit in esecuzione su computer a 64 bit.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitora le nuove estensioni di Internet Explorer, come i menu degli strumenti personalizzati e i pulsanti della barra degli strumenti personalizzata.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitora le nuove estensioni di Internet Explorer, ad esempio i menu degli strumenti personalizzati e i pulsanti della barra degli strumenti personalizzati per le applicazioni a 32 bit in esecuzione su computer a 64 bit.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora i driver a 32 bit associati a wavemapper, wave1 e Wave2, msacm. imaadpcm,. msadpcm,. msgsm610 e vidc. Simile alla sezione [Drivers] nel file **System. ini** .
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora i driver a 32 bit associati a wavemapper, wave1 e Wave2, msacm. imaadpcm,. msadpcm,. msgsm610 e vidc per applicazioni a 32 bit in esecuzione su computer a 64 bit. Simile alla sezione [Drivers] nel file **System. ini** .
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitora l'elenco delle DLL di sistema note o comunemente usate. Questo sistema impedisce agli utenti di sfruttare le autorizzazioni vulnerabili delle directory applicative eliminando le versioni Trojan Horse delle DLL di sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitora l'elenco dei pacchetti che possono ricevere notifiche degli eventi da **Winlogon. exe**, il modello di supporto per l'accesso interattivo per Windows.

## <a name="support-for-file-integrity-monitoring-in-azure-security-center"></a>Supporto per il monitoraggio dell'integrità dei file nel centro sicurezza di Azure

Rilevamento modifiche e l'inventario usano il [monitoraggio dell'integrità dei file (FIM) del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring). Sebbene FIM monitori solo i file e i registri, la funzionalità Rilevamento modifiche e inventario completa include anche il monitoraggio per:

- Modifiche software
- Servizi Microsoft
- Daemon Linux

## <a name="recursion-support"></a>Supporto della ricorsione

Rilevamento modifiche e Inventory supporta la ricorsione, che consente di specificare caratteri jolly per semplificare il rilevamento tra le directory. La ricorsione fornisce anche le variabili di ambiente che consentono di tenere traccia dei file in ambienti con più nomi di unità o dinamici. Nell'elenco seguente sono incluse le informazioni comuni che è necessario tenere presente durante la configurazione della ricorsione:

* I caratteri jolly sono necessari per il rilevamento di più file.
* I caratteri jolly possono essere usati solo nell'ultimo segmento di un percorso, ad esempio **file c:\cartella\\*** o **/etc/*. conf**.
* Se una variabile di ambiente ha un percorso non valido, la convalida ha esito positivo, ma il percorso non riesce durante l'esecuzione.
* Evitare i nomi di percorso generale quando si imposta il percorso, perché questo tipo di impostazione può causare l'attraversamento di un numero eccessivo di cartelle.

## <a name="change-tracking-and-inventory-data-collection"></a>Raccolta dati di Rilevamento modifiche e inventario

La tabella seguente mostra la frequenza di raccolta dei dati per i tipi di modifiche supportate da Rilevamento modifiche e l'inventario. Per ogni tipo, anche lo snapshot dei dati dello stato corrente viene aggiornato almeno ogni 24 ore.

| **Uso del comando Modifica tipo** | **Frequenza** |
| --- | --- |
| Registro di sistema di Windows | 50 minuti |
| File Windows | 30 minuti |
| File Linux | 15 minuti |
| Servizi Microsoft | Da 10 secondi a 30 minuti</br> Impostazione predefinita: 30 minuti |
| Daemon Linux | 5 minuti |
| Software Windows | 30 minuti |
| Software Linux | 5 minuti |

La tabella seguente illustra i limiti degli elementi rilevati per computer per Rilevamento modifiche e l'inventario.

| **Risorsa** | **Limite** |
|---|---|---|
|File|500|
|Registro|250|
|Software Windows (esclusi gli hotfix) |250|
|Pacchetti Linux|1250|
|Servizi|250|
|DAEMON|250|

L'utilizzo medio dei dati Log Analytics per un computer con Rilevamento modifiche e l'inventario è di circa 40 MB al mese. Questo valore è solo un'approssimazione ed è soggetto a modifiche, in base all'ambiente in uso. È consigliabile monitorare l'ambiente per visualizzare l'esatta quantità di dati usati.

### <a name="microsoft-service-data"></a>Dati del servizio Microsoft

La frequenza di raccolta predefinita per i servizi Microsoft è 30 minuti. È possibile configurare la frequenza usando un dispositivo di scorrimento nella scheda **Servizi Microsoft** in **Modifica impostazioni**. 

![Dispositivo di scorrimento dei servizi Microsoft](./media/change-tracking/windowservices.png)

Per ottimizzare le prestazioni, l'agente di Log Analytics tiene traccia solo delle modifiche. L'impostazione di una soglia elevata potrebbe non essere modificata se il servizio viene ripristinato allo stato originale. Se si imposta la frequenza su un valore inferiore, è possibile rilevare le modifiche che potrebbero essere perse in caso contrario.

> [!NOTE]
> Mentre l'agente è in grado di tenere traccia delle modifiche fino a un intervallo di 10 secondi, i dati richiedono ancora alcuni minuti per la visualizzazione nel portale di Azure. Le modifiche che si verificano nel tempo per la visualizzazione nel portale vengono comunque registrate e registrate.

## <a name="support-for-alerts-on-configuration-state"></a>Supporto per gli avvisi sullo stato di configurazione

Una funzionalità chiave di Rilevamento modifiche e inventario è costituita da avvisi sulle modifiche allo stato di configurazione dell'ambiente ibrido. Sono disponibili molte azioni utili da attivare in risposta ad avvisi, ad esempio azioni su funzioni di Azure, manuali operativi di automazione, webhook e così via. Gli avvisi relativi alle modifiche apportate al file **C:\windows\system32\drivers\etc\hosts** per un computer sono un'applicazione efficace di avvisi per i dati di rilevamento modifiche e di inventario. Sono disponibili anche molti altri scenari per gli avvisi, inclusi gli scenari di query definiti nella tabella seguente. 

|Query  |Descrizione  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|Utile per tenere traccia delle modifiche apportate ai file critici del sistema.|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Utile per tenere traccia delle modifiche apportate ai file di configurazione delle chiavi.|
|ConfigurationChange <br>&#124; where ConfigChangeType = = "Microsoft Services" e SvcName contiene "W3SVC" e SvcState = = "Stopped"|Utile per tenere traccia delle modifiche apportate ai servizi critici del sistema.|
|ConfigurationChange <br>&#124; where ConfigChangeType = = "daemons" e SvcName contiene "SSH" e SvcState! = "Running"|Utile per tenere traccia delle modifiche apportate ai servizi critici del sistema.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|Utile per gli ambienti che necessitano di configurazioni software bloccate.|
|ConfigurationData <br>&#124; dove softwarename contiene "Monitoring Agent" e CurrentVersion! = "8.0.11081.0"|Utile per visualizzare i computer in cui è installata la versione software obsoleta o non conforme. Questa query segnala l'ultimo stato della configurazione segnalata, ma non segnala le modifiche.|
|ConfigurationChange <br>&#124; where RegistryKey = = @ "HKEY_LOCAL_MACHINE\\software\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Utile per tenere traccia delle modifiche apportate alle chiavi antivirus cruciali.|
|ConfigurationChange <br>&#124; dove RegistryKey contiene @ "HKEY_LOCAL_MACHINE\\System\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\firewallpolicy"| Utile per tenere traccia delle modifiche apportate alle impostazioni del firewall.|

## <a name="next-steps"></a>Passaggi successivi

* Per lavorare con Rilevamento modifiche e l'inventario nella manuali operativi, vedere [gestire rilevamento modifiche e inventario](change-tracking-file-contents.md).
* Per risolvere gli errori relativi a Rilevamento modifiche e inventario, vedere [risolvere i problemi relativi rilevamento modifiche e inventario](automation-tutorial-troubleshoot-changes.md).
* Usare le [ricerche log nei log di monitoraggio di Azure](../log-analytics/log-analytics-log-searches.md) per visualizzare i dati dettagliati del rilevamento delle modifiche.
