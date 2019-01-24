---
title: Rilevare le modifiche con Automazione di Azure
description: La soluzione Rilevamento modifiche consente di identificare le modifiche al software e al servizio Windows che si verificano nell'ambiente in uso.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 01/04/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d29a2020d7e7a16e0bac0802a887a28e12630f03
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54433017"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Rilevare le modifiche nell'ambiente in uso con la soluzione di rilevamento modifiche

Questo articolo spiega come usare la soluzione Rilevamento modifiche per identificare facilmente le modifiche nell'ambiente. La soluzione rileva le modifiche apportate al software Windows e Linux, ai file di Windows e Linux, alle chiavi del Registro di sistema di Windows, ai servizi di Windows e ai daemon Linux. Rilevando le modifiche alla configurazione è possibile localizzare eventuali problemi operativi.

Le modifiche al software installato, ai servizi di Windows, ai file e al Registro di sistema di Windows e ai daemon Linux nei server monitorati vengono inviate al servizio Log Analytics nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud. Usando le informazioni nel dashboard Change Tracking, è possibile visualizzare facilmente le modifiche apportate all'infrastruttura del server.

## <a name="supported-windows-operating-systems"></a>Sistemi operativi Windows supportati

Le versioni seguenti del sistema operativo Windows sono ufficialmente supportate per l'agente Windows:

* Windows Server 2008 R2 o versione successiva

## <a name="supported-linux-operating-systems"></a>Sistemi operativi Linux supportati

Le distribuzioni Linux seguenti sono supportate ufficialmente. È tuttavia possibile che l'agente Linux sia eseguito in altre distribuzioni non elencate. Se non diversamente indicato, tutte le versioni minori sono supportate per ogni versione principale elencata.  

### <a name="64-bit"></a>64 bit

* CentOS 6 e 7
* Amazon Linux 2017.09
* Oracle Linux 6 e 7
* Red Hat Enterprise Linux Server 6 e 7
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32 bit

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS e 16.04 LTS

## <a name="onboard"></a>Abilitare Rilevamento modifiche e inventario

Per iniziare a tenere traccia delle modifiche, è necessario abilitare la soluzione Rilevamento modifiche e Inventario. Esistono molti modi per caricare i computer al fine di effettuare il rilevamento modifiche e inventario. Per eseguire l'onboarding della soluzione si consigliano i metodi supportati seguenti.

* [Da una macchina virtuale](automation-onboard-solutions-from-vm.md)
* [Dall'esplorazione di più computer](automation-onboard-solutions-from-browse.md)
* [Dall'account di Automazione](automation-onboard-solutions-from-automation-account.md)
* [Con un runbook di Automazione di Azure](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Configurazione di Rilevamento modifiche e Inventario

Per informazioni sulla modalità di esecuzione dell'onboarding nella soluzione, vedere: [Onboarding Automation solutions](automation-onboard-solutions-from-automation-account.md) (Soluzioni per l'onboarding dall'account di Automazione). Dopo aver ottenuto un processo di onboarding del computer con la soluzione Rilevamento modifiche e Inventario, è possibile configurare gli elementi di cui tenere traccia. Quando si abilita un nuovo file o una chiave del Registro di sistema da rilevare, vengono abilitati sia per Rilevamento modifiche che per Inventario.

Per tenere traccia delle modifiche nei file sia di Windows che di Linux, vengono usati hash MD5 dei file. Questi hash vengono quindi usati per rilevare se dopo l'ultimo inventario sono state apportate modifiche.

### <a name="configure-linux-files-to-track"></a>Configurare i file di Linux da rilevare

Seguire questa procedura per configurare il rilevamento dei file in computer Linux:

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE**. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
2. Nella pagina **Rilevamento modifiche** selezionare **File Linux** e quindi fare clic su **+ Aggiungi** per aggiungere un nuovo file da rilevare.
3. In **Aggiungi file Linux per Rilevamento modifiche** immettere le informazioni per il file o la directory da rilevare e fare clic su **Salva**.

|Proprietà  |Descrizione  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata.        |
|Nome elemento     | Nome descrittivo del file da rilevare.        |
|Gruppo     | Nome del gruppo per il raggruppamento logico dei file.        |
|Immettere il percorso     | Percorso in cui cercare il file. Ad esempio: "/etc/*.conf"       |
|Tipo di percorso     | Tipo di elemento da rilevare, i valori possibili sono File e Directory.        |
|Ricorsione     | Determina se viene usata la ricorsione per la ricerca dell'elemento da rilevare.        |
|Usa Sudo     | Questa impostazione determina se viene usato sudo per la ricerca dell'elemento.         |
|Collegamenti     | Questa impostazione determina come vengono gestiti i collegamenti simbolici durante l'attraversamento delle directory.<br> **Ignora**: ignora i collegamenti simbolici e non include i file e le directory a cui viene fatto riferimento.<br>**Segui**: segue i collegamenti simbolici durante la ricorsione e include anche i file e le directory a cui viene fatto riferimento.<br>**Gestisci**: segue i collegamenti simbolici e consente la modifica del contenuto restituito.     |
|Caricare il contenuto del file per tutte le impostazioni| Attivare o disattivare il caricamento del contenuto del file per le modifiche rilevate. Opzioni disponibili: **True** o **False**.|

> [!NOTE]
> Questa opzione dei collegamenti "Gestisci" non è consigliata. Il recupero del contenuto del file non è supportato.

### <a name="configure-windows-files-to-track"></a>Configurare i file di Windows da rilevare

Seguire questa procedura per configurare il rilevamento dei file in computer Windows:

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE**. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
2. Nella pagina **Rilevamento modifiche** selezionare **File Windows** e quindi fare clic su **+ Aggiungi** per aggiungere un nuovo file da rilevare.
3. In **Aggiungi file Windows per Rilevamento modifiche** immettere le informazioni per il file da rilevare e fare clic su **Salva**.

|Proprietà  |Descrizione  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata.        |
|Nome elemento     | Nome descrittivo del file da rilevare.        |
|Gruppo     | Nome del gruppo per il raggruppamento logico dei file.        |
|Immettere il percorso     | Percorso in cui cercare il file, ad esempio "c:\temp\\\*.txt"<br>È anche possibile usare le variabili di ambiente, ad esempio "%winDir%\System32\\\*.*"       |
|Ricorsione     | Determina se viene usata la ricorsione per la ricerca dell'elemento da rilevare.        |
|Caricare il contenuto del file per tutte le impostazioni| Attivare o disattivare il caricamento del contenuto del file per le modifiche rilevate. Opzioni disponibili: **True** o **False**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Impostazioni con caratteri jolly, ricorsione e ambiente

Ricorsione consente di specificare i caratteri jolly per semplificare il rilevamento tra le directory e le variabili di ambiente che consentono di tenere traccia dei file tra gli ambienti con nomi di unità multipli o dinamici. Di seguito è riportato un elenco di informazioni comuni che è necessario conoscere quando si configura la ricorsione:

* I caratteri jolly sono necessari per tenere traccia di più file
* I caratteri jolly possono essere usati solo nell'ultimo segmento di un percorso. (ad esempio C:\folder\\**file** o /etc/*.conf)
* Se una variabile di ambiente dispone di un percorso non valido, la convalida avrà esito positivo, ma tale percorso avrà esito negativo quando si esegue l'inventario.
* Evitare percorsi generici, ad esempio `c:\*.*`, quando si imposta il percorso, poiché vengono attraversate troppe cartelle.

## <a name="configure-file-content-tracking"></a>Configurare il rilevamento del contenuto di file

È possibile visualizzare il contenuto prima e dopo una modifica di un file con Rilevamento modifiche contenuto file. Tale funzionalità è disponibile per i file di Windows e Linux. Per ogni modifica al file, il contenuto del file viene archiviato in un account di archiviazione e mostra il file prima e dopo la modifica, inline e in modalità side-by-side. Per altre informazioni, vedere [View the contents of a tracked file](change-tracking-file-contents.md) (Visualizzare il contenuto di un file con il rilevamento modifiche applicato).

![visualizzare le modifiche in un file](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Configurare le chiavi del Registro di Sistema di Windows da rilevare

Seguire questa procedura per configurare il rilevamento delle chiavi del Registro di sistema in computer Windows:

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE**. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
2. Nella pagina **Rilevamento modifiche** selezionare **Registro di sistema di Windows** e quindi fare clic su **+ Aggiungi** per aggiungere una nuova chiave del Registro di sistema da rilevare.
3. In **Aggiungi Registro di sistema di Windows per Rilevamento modifiche** immettere le informazioni per la chiave da rilevare e fare clic su **Salva**.

|Proprietà  |Descrizione  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata.        |
|Nome elemento     | Nome descrittivo del file da rilevare.        |
|Gruppo     | Nome del gruppo per il raggruppamento logico dei file.        |
|Chiave del Registro di sistema di Windows   | Percorso in cui cercare il file. Ad esempio: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Limitazioni

La soluzione di Rilevamento modifiche non supporta attualmente gli elementi seguenti:

* Ricorsione per Rilevamento del Registro di sistema di Windows
* File system di rete

Altre limitazioni:

* La colonna **Dimensioni massime file** e i valori non sono usati nell'implementazione corrente.
* Se si raccolgono oltre 2500 file in un ciclo di recupero di 30 minuti, è possibile assistere a una riduzione delle prestazioni della soluzione.
* Quando il traffico di rete è elevato, i record di modifica possono impiegare fino a sei ore prima di essere visualizzati.
* Se si modifica la configurazione mentre un computer è arrestato, il computer potrebbe registrare le modifiche che appartenevano alla configurazione precedente.

## <a name="known-issues"></a>Problemi noti

La soluzione Rilevamento modifiche sta riscontrando attualmente i problemi seguenti:

* Gli aggiornamenti rapidi non vengono raccolti per le macchine Windows 10 Creators Update e Windows Server 2016 Core RS3.
* Per i file di Windows, attualmente la soluzione Rilevamento modifiche non rileva se un nuovo file è stato aggiunto a un percorso cartella rilevato

## <a name="change-tracking-data-collection-details"></a>Informazioni dettagliate sulla raccolta dei dati di Change Tracking

La tabella seguente mostra la frequenza di raccolta dati per i tipi di modifiche. Per ogni tipo lo snapshot dei dati dello stato corrente viene aggiornato almeno ogni 24 ore:

| **Tipo di modifica** | **Frequenza** |
| --- | --- |
| Registro di sistema di Windows | 50 minuti |
| File Windows | 30 minuti |
| File Linux | 15 minuti |
| Servizi Windows | Da 10 secondi a 30 minuti</br> Predefinito: 30 minuti |
| Daemon Linux | 5 minuti |
| Software Windows | 30 minuti |
| Software Linux | 5 minuti |

La tabella seguente illustra i limiti dell'elemento di rilevamento per ogni macchina per il rilevamento modifiche.

| **Risorsa** | **Limite**| **Note** |
|---|---|---|
|File|500||
|Registro|250||
|Software Windows|250|Non include gli aggiornamenti software|
|Pacchetti Linux|1250||
|Servizi|250||
|Daemon|250||

### <a name="windows-service-tracking"></a>Rilevamento dei servizi di Windows

La frequenza di raccolta predefinita per i servizi di Windows è 30 minuti. Per configurare la frequenza, andare a **Rilevamento modifiche**. In **Modifica impostazioni** nella scheda **Servizi Windows** è presente un dispositivo di scorrimento che consente di modificare la frequenza di raccolta per i servizi di Windows in un intervallo di tempo compreso tra 10 secondi e 30 minuti. Spostare la barra di scorrimento sulla frequenza desiderata e salvarla automaticamente.

![Dispositivo di scorrimento dei servizi di Windows](./media/automation-change-tracking/windowservices.png)

L'agente rileva solo le modifiche e di conseguenza le prestazioni sono ottimizzate. Se la soglia delle modifiche viene impostata su un valore troppo elevato, il servizio potrebbe non essere ripristinato allo stato originale. L'impostazione della frequenza su un valore inferiore consente di rilevare modifiche che potrebbero essere perse altrimenti.

> [!NOTE]
> Anche se l'agente può rilevare le modifiche in un intervallo di 10 secondi, i dati richiedono alcuni minuti per essere visualizzati nel portale. Le modifiche che si verificano durante il periodo di visualizzazione vengono comunque rilevate e registrate.
  
### <a name="registry-key-change-tracking"></a>Rilevamento delle modifiche della chiave del Registro di Sistema

Lo scopo del monitoraggio delle modifiche alle chiavi del Registro di sistema è di eseguire il pinpoint dei punti di estensibilità dove è possibile che vengano attivati codice di terze parti e malware. L'elenco seguente mostra le chiavi del Registro di sistema preconfigurate. Queste chiavi sono configurate, ma non abilitate. Per tenere traccia di queste chiavi del Registro di sistema, è necessario abilitarle una per una.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora le voci di avvio automatico che si collegano direttamente in Esplora risorse e che in genere funzionano in-process con Explorer.exe.    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora gli script eseguiti all'avvio.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora gli script eseguiti all'arresto del sistema.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora le chiavi che vengono caricate prima dell'accesso degli utenti nel proprio account di Windows. La chiave viene usata per i programmi a 32 bit in esecuzione nei computer a 64 bit.    |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora le modifiche apportate alle impostazioni dell'applicazione.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora le voci di avvio automatico che si collegano direttamente in Esplora risorse e che in genere funzionano in-process con Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora le voci di avvio automatico che si collegano direttamente in Esplora risorse e che in genere funzionano in-process con Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora la registrazione del gestore delle immagini sovrapposte alle icone.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora la registrazione del gestore delle immagini sovrapposte alle icone per i programmi a 32 bit in esecuzione in computer a 64 bit.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora i nuovi plug-in dell'oggetto browser helper per Internet Explorer. Usati per accedere al DOM (Document Object Model) della pagina corrente e per controllare la navigazione.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora i nuovi plug-in dell'oggetto browser helper per Internet Explorer. Usati per accedere al DOM (Document Object Model) della pagina corrente e per controllare la navigazione per i programmi a 32 bit in esecuzione su computer a 64 bit.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora le nuove estensioni di Internet Explorer, come i menu degli strumenti personalizzati e i pulsanti della barra degli strumenti personalizzati.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora le nuove estensioni di Internet Explorer, come i menu degli strumenti personalizzati e i pulsanti della barra degli strumenti personalizzati per i programmi a 32 bit in esecuzione in computer a 64 bit.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora i driver a 32 bit associati con wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. È simile alla sezione [driver] nel file SYSTEM.INI.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora i driver a 32 bit associati con wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc per i programmi a 32 bit in esecuzione in computer a 64 bit. È simile alla sezione [driver] nel file SYSTEM.INI.|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora l'elenco delle DLL di sistema note o comunemente usate. Questo sistema impedisce di sfruttare le autorizzazioni vulnerabili delle directory delle applicazioni rilasciando trojan nelle DLL di sistema.|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora l'elenco dei pacchetti in grado di ricevere le notifiche degli eventi da Winlogon, il modello di supporto di accesso interattivo per il sistema operativo Windows.|

## <a name="network-requirements"></a>Requisiti di rete

I seguenti indirizzi sono necessari e specifici per Rilevamento modifiche. La comunicazione verso questi indirizzi avviene sulla porta 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="use-change-tracking"></a>Uso di Change Tracking

Dopo avere abilitato la soluzione, è possibile visualizzare il riepilogo delle modifiche per i computer monitorati selezionando **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE** nell'account di Automazione.

È possibile visualizzare le modifiche ai computer e quindi analizzare i dettagli per ogni evento. Nella parte superiore del grafico sono disponibili menu a discesa per limitare le informazioni dettagliate e relative al grafico in base al tipo di modifica e agli intervalli di tempo. È anche possibile fare clic sul grafico e trascinare la selezione per selezionare un intervallo di tempo personalizzato.

![image of Change Tracking dashboard](./media/automation-change-tracking/change-tracking-dash01.png)

Facendo clic su una modifica o su un evento vengono visualizzate informazioni dettagliate relative a tale modifica. Come si può notare nell'esempio, il tipo di avvio del servizio è stato modificato da Manuale ad Automatico.

![immagine dei dettagli del rilevamento delle modifiche](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Eseguire ricerche nei log

Oltre ai dettagli forniti nel portale, è possibile eseguire ricerche nei log. Con la pagina **Rilevamento modifiche** aperta, fare clic su **Log Analytics**. Verrà visualizzata la pagina **Ricerca log**.

### <a name="sample-queries"></a>Query di esempio

La tabella seguente contiene esempi di ricerche log per i record di modifica raccolti da questa soluzione:

|Query  |Descrizione  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Mostra i record dell'inventario più recenti per i servizi Windows che sono stati impostati su Auto (Automatico) ma sono stati segnalati come Stopped (Interrotto)<br>I risultati sono limitati al record più recente per SoftwareName e Computer      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|Mostra i record di modifica per il software rimosso|

## <a name="next-steps"></a>Passaggi successivi

Vedere l'esercitazione su Rilevamento modifiche per altre informazioni sull'uso della soluzione:

> [!div class="nextstepaction"]
> [Risolvere i problemi delle modifiche nell'ambiente](automation-tutorial-troubleshoot-changes.md)

* Per visualizzare dati dettagliati di monitoraggio delle modifiche, usare [Ricerche nei log in Log Analytics](../log-analytics/log-analytics-log-searches.md).

