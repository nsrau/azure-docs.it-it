---
title: Rilevare le modifiche con Automazione di Azure
description: La soluzione Rilevamento modifiche consente di identificare le modifiche al software e al servizio Windows che si verificano nell'ambiente in uso.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1fd800062c4a8362919b1818550b2fca9fa3eb88
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850551"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Rilevare le modifiche nell'ambiente in uso con la soluzione di rilevamento modifiche

Questo articolo spiega come usare la soluzione Rilevamento modifiche per identificare facilmente le modifiche nell'ambiente. La soluzione rileva le modifiche apportate al software Windows e Linux, ai file di Windows e Linux, alle chiavi del Registro di sistema di Windows, ai servizi di Windows e ai daemon Linux. Rilevando le modifiche alla configurazione è possibile localizzare eventuali problemi operativi.

Le modifiche apportate al software installato, ai servizi Windows, al registro di sistema e ai file di Windows e ai Daemon Linux nei server monitorati vengono inviate al servizio monitoraggio di Azure nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud. Usando le informazioni nel dashboard Change Tracking, è possibile visualizzare facilmente le modifiche apportate all'infrastruttura del server.

> [!NOTE]
> Automazione di Azure Rilevamento modifiche tiene traccia delle modifiche apportate alle macchine virtuali. Per tenere traccia delle modifiche apportate alle proprietà Azure Resource Manager, vedere [cronologia delle modifiche](../governance/resource-graph/how-to/get-resource-changes.md)di Azure Resource Graph.

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

Per informazioni sulla modalità di esecuzione dell'onboarding nella soluzione, vedere [Onboarding Automation solutions](automation-onboard-solutions-from-automation-account.md) (Soluzioni per l'onboarding dall'account di Automazione). Dopo aver eseguito l'onboarding del computer con la soluzione di Rilevamento modifiche e inventario, è possibile configurare gli elementi da rilevare. Quando si Abilita un nuovo file o una nuova chiave del registro di sistema per tenere traccia, viene abilitata sia per Rilevamento modifiche sia per l'inventario.

Per tenere traccia delle modifiche nei file sia di Windows che di Linux, vengono usati hash MD5 dei file. Questi hash vengono quindi usati per rilevare se dopo l'ultimo inventario sono state apportate modifiche.

### <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitoraggio dell'integrità dei file nel Centro sicurezza di Azure

Il Centro sicurezza di Azure ha aggiunto il monitoraggio dell'integrità dei file (FIM) basato su Rilevamento modifiche di Azure. Sebbene FIM monitori solo i file e i registri, la soluzione Rilevamento modifiche completa include anche:

- Modifiche software
- Servizi Windows
- Daemon Linux

Se è già stato abilitato FIM e si vuole provare la soluzione completa Rilevamento modifiche, è necessario eseguire i passaggi seguenti. Le impostazioni non vengono rimosse da questo processo.

> [!NOTE]
> L'abilitazione della soluzione Rilevamento modifiche completa può causare addebiti aggiuntivi. per ulteriori informazioni, vedere [prezzi di automazione](https://azure.microsoft.com/pricing/details/automation/).

1. Rimuovere la soluzione di monitoraggio passando all'area di lavoro e individuando l' [elenco delle soluzioni di monitoraggio installate](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Fare clic sul nome della soluzione per aprire la relativa pagina di riepilogo e quindi fare clic su Elimina, come descritto in [rimuovere una soluzione di monitoraggio](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Riabilitare la soluzione passando all'account di automazione e selezionando **rilevamento modifiche** dal menu delle risorse in gestione della **configurazione**.
4. Confermare i dettagli dell'impostazione dell'area di lavoro e fare clic su **Abilita**.

### <a name="configure-linux-files-to-track"></a>Configurare i file di Linux da rilevare

Seguire questa procedura per configurare il rilevamento dei file in computer Linux:

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE**. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
2. Nella pagina **Rilevamento modifiche** selezionare **File Linux** e quindi fare clic su **+ Aggiungi** per aggiungere un nuovo file da rilevare.
3. In **Aggiungi file Linux per Rilevamento modifiche** immettere le informazioni per il file o la directory da rilevare e fare clic su **Salva**.

|Proprietà  |Description  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata.        |
|Nome elemento     | Nome descrittivo del file da rilevare.        |
|Group     | Nome del gruppo per il raggruppamento logico dei file.        |
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

|Proprietà  |Description  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata.        |
|Nome elemento     | Nome descrittivo del file da rilevare.        |
|Group     | Nome del gruppo per il raggruppamento logico dei file.        |
|Immettere il percorso     | Percorso in cui cercare il file, ad esempio "c:\temp\\\*.txt"<br>È anche possibile usare le variabili di ambiente, ad esempio "%winDir%\System32\\\*.*"       |
|Ricorsione     | Determina se viene usata la ricorsione per la ricerca dell'elemento da rilevare.        |
|Caricare il contenuto del file per tutte le impostazioni| Attivare o disattivare il caricamento del contenuto del file per le modifiche rilevate. Opzioni disponibili: **True** o **False**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Impostazioni con caratteri jolly, ricorsione e ambiente

Ricorsione consente di specificare i caratteri jolly per semplificare il rilevamento tra le directory e le variabili di ambiente che consentono di tenere traccia dei file tra gli ambienti con nomi di unità multipli o dinamici. L'elenco seguente mostra le informazioni comuni che è necessario conoscere quando si configura la ricorsione:

* I caratteri jolly sono necessari per tenere traccia di più file
* I caratteri jolly possono essere usati solo nell'ultimo segmento di un percorso. (ad esempio `c:\folder\*file*` o `/etc/*.conf`)
* Se una variabile di ambiente dispone di un percorso non valido, la convalida avrà esito positivo, ma tale percorso avrà esito negativo quando si esegue l'inventario.
* Evitare percorsi generici, ad esempio `c:\*.*`, quando si imposta il percorso, poiché vengono attraversate troppe cartelle.

## <a name="configure-file-content-tracking"></a>Configurare il rilevamento del contenuto di file

È possibile visualizzare il contenuto prima e dopo una modifica di un file con Rilevamento modifiche contenuto file. Questa funzionalità è disponibile per i file di Windows e Linux. Per ogni modifica al file, il contenuto del file viene archiviato in un account di archiviazione e mostra il file prima e dopo la modifica, inline o in modalità side-by-side. Per altre informazioni, vedere [View the contents of a tracked file](change-tracking-file-contents.md) (Visualizzare il contenuto di un file con il rilevamento modifiche applicato).

![visualizzare le modifiche in un file](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Configurare le chiavi del Registro di Sistema di Windows da rilevare

Seguire questa procedura per configurare il rilevamento delle chiavi del Registro di sistema in computer Windows:

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE**. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
2. Nella pagina **Rilevamento modifiche** selezionare **Registro di sistema di Windows** e quindi fare clic su **+ Aggiungi** per aggiungere una nuova chiave del Registro di sistema da rilevare.
3. In **Aggiungi Registro di sistema di Windows per Rilevamento modifiche** immettere le informazioni per la chiave da rilevare e fare clic su **Salva**.

|Proprietà  |Description  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata.        |
|Nome elemento     | Nome descrittivo della chiave del Registro di sistema da rilevare.        |
|Group     | Nome del gruppo per il raggruppamento logico delle chiavi del Registro di sistema.        |
|Chiave del Registro di sistema di Windows   | Percorso in cui cercare la chiave del Registro di sistema. Ad esempio: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Limitazioni

La soluzione Rilevamento modifiche non supporta attualmente gli elementi seguenti:

* Ricorsione per Rilevamento del Registro di sistema di Windows
* File system di rete

Altre limitazioni:

* La colonna **Dimensioni massime file** e i valori non sono usati nell'implementazione corrente.
* Se si raccolgono oltre 2500 file in un ciclo di recupero di 30 minuti, è possibile assistere a una riduzione delle prestazioni della soluzione.
* Quando il traffico di rete è elevato, i record di modifica possono impiegare fino a sei ore prima di essere visualizzati.
* Se si modifica la configurazione mentre un computer è arrestato, il computer potrebbe registrare le modifiche che appartenevano alla configurazione precedente.

## <a name="known-issues"></a>Problemi noti

La soluzione Rilevamento modifiche sta riscontrando attualmente i problemi seguenti:

* Gli aggiornamenti rapidi non vengono raccolti nei computer Windows Server 2016 Core RS3.
* I Daemon Linux possono mostrare uno stato modificato anche se non sono state apportate modifiche. Questo è dovuto al modo in cui viene acquisito il campo `SvcRunLevels`.

## <a name="change-tracking-data-collection-details"></a>Informazioni dettagliate sulla raccolta dei dati di Change Tracking

La tabella seguente mostra la frequenza di raccolta dati per i tipi di modifiche. Per ogni tipo lo snapshot dei dati dello stato corrente viene aggiornato almeno ogni 24 ore:

| **Tipo di modifica** | **Frequenza** |
| --- | --- |
| Registro di sistema di Windows | 50 minuti |
| File Windows | 30 minuti |
| File Linux | 15 minuti |
| Servizi Windows | Da 10 secondi a 30 minuti</br> Impostazione predefinita: 30 minuti |
| Daemon Linux | 5 minuti |
| Software Windows | 30 minuti |
| Software Linux | 5 minuti |

La tabella seguente illustra i limiti dell'elemento di rilevamento per ogni macchina per il rilevamento modifiche.

| **Risorsa** | **Limite**| **Note** |
|---|---|---|
|File|500||
|Registro|250||
|Software Windows|250|Non include hotfix software|
|Pacchetti Linux|1250||
|Services|250||
|Daemon|250||

L'utilizzo medio dei dati di Log Analytics per un computer con Rilevamento modifiche e Inventario è di circa 40 MB al mese. Questo valore è solo un'approssimazione ed è soggetto a modifiche, in base all'ambiente in uso. È consigliabile monitorare l'ambiente per visualizzare l'esatta quantità di dati usati.

### <a name="windows-service-tracking"></a>Rilevamento dei servizi di Windows

La frequenza di raccolta predefinita per i servizi di Windows è 30 minuti. Per configurare la frequenza, passare a **Rilevamento modifiche**. In **Modifica impostazioni** nella scheda **Servizi Windows** è presente un dispositivo di scorrimento che consente di modificare la frequenza di raccolta per i servizi di Windows in un intervallo di tempo compreso tra 10 secondi e 30 minuti. Spostare la barra di scorrimento sulla frequenza desiderata e salvarla automaticamente.

![Dispositivo di scorrimento dei servizi di Windows](./media/change-tracking/windowservices.png)

L'agente rileva solo le modifiche e di conseguenza le prestazioni sono ottimizzate. L'impostazione di una soglia elevata può comportare la perdita di modifiche nel caso in cui il servizio ripristini lo stato originale. L'impostazione della frequenza su un valore inferiore consente di rilevare modifiche che potrebbero essere perse altrimenti.

> [!NOTE]
> Anche se l'agente può rilevare le modifiche in un intervallo di 10 secondi, i dati richiedono alcuni minuti per essere visualizzati nel portale. Le modifiche che si verificano durante il periodo di visualizzazione vengono comunque rilevate e registrate.

### <a name="registry-key-change-tracking"></a>Rilevamento delle modifiche della chiave del Registro di Sistema

Lo scopo del monitoraggio delle modifiche alle chiavi del Registro di sistema è di eseguire il pinpoint dei punti di estensibilità dove è possibile che vengano attivati codice di terze parti e malware. L'elenco seguente mostra le chiavi del Registro di sistema preconfigurate. Queste chiavi sono configurate, ma non abilitate. Per tenere traccia di queste chiavi del Registro di sistema, è necessario abilitarle una per una.

> [!div class="mx-tdBreakAll"]
> |Chiave del Registro | Finalità |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitora le voci di avvio automatico che si collegano direttamente in Esplora risorse e che in genere funzionano in-process con Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitora gli script eseguiti all'avvio.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitora gli script eseguiti all'arresto del sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitora le chiavi che vengono caricate prima dell'accesso degli utenti nel proprio account di Windows. La chiave viene usata per i programmi a 32 bit in esecuzione nei computer a 64 bit.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitora le modifiche apportate alle impostazioni dell'applicazione.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitora le voci di avvio automatico che si collegano direttamente in Esplora risorse e che in genere funzionano in-process con Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitora le voci di avvio automatico che si collegano direttamente in Esplora risorse e che in genere funzionano in-process con Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora la registrazione del gestore della sovrapposizione delle icone.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora la registrazione del gestore della sovrapposizione delle icone per i programmi a 32 bit in esecuzione su computer a 64 bit.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora i nuovi plug-in dell'oggetto browser helper per Internet Explorer. Usati per accedere al DOM (Document Object Model) della pagina corrente e per controllare la navigazione.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora i nuovi plug-in dell'oggetto browser helper per Internet Explorer. Usati per accedere al DOM (Document Object Model) della pagina corrente e per controllare la navigazione per i programmi a 32 bit in esecuzione su computer a 64 bit.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitora le nuove estensioni di Internet Explorer, come i menu degli strumenti personalizzati e i pulsanti della barra degli strumenti personalizzata.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitora le nuove estensioni di Internet Explorer, come i menu degli strumenti personalizzati e i pulsanti della barra degli strumenti personalizzata per i programmi a 32 bit in esecuzione su computer a 64 bit.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora i driver a 32 bit associati con wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. È simile alla sezione [driver] nel file SYSTEM.INI.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora i driver a 32 bit associati con wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc per i programmi a 32 bit in esecuzione su computer a 64 bit. È simile alla sezione [driver] nel file SYSTEM.INI.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitora l'elenco delle DLL di sistema note o comunemente usate. Questo sistema impedisce alle persone di sfruttare autorizzazioni vulnerabili delle directory delle applicazioni rilasciando trojan nelle DLL di sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitora l'elenco dei pacchetti in grado di ricevere le notifiche degli eventi da Winlogon, il modello di supporto di accesso interattivo per il sistema operativo Windows.

## <a name="network-requirements"></a>Requisiti di rete

I seguenti indirizzi sono necessari e specifici per Rilevamento modifiche. La comunicazione verso questi indirizzi avviene sulla porta 443.

|Azure Public  |Azure per enti pubblici  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="use-change-tracking"></a>Uso di Change Tracking

Dopo avere abilitato la soluzione, è possibile visualizzare il riepilogo delle modifiche per i computer monitorati selezionando **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE** nell'account di Automazione.

È possibile visualizzare le modifiche ai computer e quindi analizzare i dettagli per ogni evento. Nella parte superiore del grafico sono disponibili menu a discesa per limitare le informazioni dettagliate e relative al grafico in base al tipo di modifica e agli intervalli di tempo. È anche possibile fare clic sul grafico e trascinare la selezione per selezionare un intervallo di tempo personalizzato. **Il tipo di modifica** sarà uno dei valori seguenti: **eventi**, **daemon**, **file**, **Registro di sistema**, **software**, **servizi di Windows**. Categoria indica il tipo di modifica e può essere **aggiunto**, **modificato**o **rimosso**.

![image of Change Tracking dashboard](./media/change-tracking/change-tracking-dash01.png)

Facendo clic su una modifica o su un evento vengono visualizzate informazioni dettagliate relative a tale modifica. Come si può notare nell'esempio, il tipo di avvio del servizio è stato modificato da Manuale ad Automatico.

![immagine dei dettagli del rilevamento delle modifiche](./media/change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Eseguire ricerche nei log

Oltre ai dettagli forniti nel portale, è possibile eseguire ricerche nei log. Con la pagina **rilevamento modifiche** aperta, fare clic su **log Analytics**. verrà visualizzata la pagina **log** .

### <a name="sample-queries"></a>Query di esempio

La tabella seguente contiene esempi di ricerche log per i record di modifica raccolti da questa soluzione:

|Query  |Description  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Mostra i record dell'inventario più recenti per i servizi Windows che sono stati impostati su Auto (Automatico) ma sono stati segnalati come Stopped (Interrotto)<br>I risultati sono limitati al record più recente per SoftwareName e Computer      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|Mostra i record di modifica per il software rimosso|

## <a name="alert-on-changes"></a>Generare avvisi in base alle modifiche

Una funzionalità importante di Rilevamento modifiche e inventario consiste nella possibilità di generare avvisi relativi allo stato di configurazione e alle eventuali modifiche apportate allo stato di configurazione dell'ambiente ibrido.

Nell'esempio seguente lo screenshot mostra che il file `C:\windows\system32\drivers\etc\hosts` è stato modificato su un computer. Il file hosts è importante perché è usato da Windows per risolvere i nomi host in indirizzi IP e ha la precedenza anche sul DNS. Ciò può causare problemi di connettività o determinare il reindirizzamento del traffico verso siti Web dannosi o comunque pericolosi.

![Grafico che mostra la modifica del file hosts](./media/change-tracking/changes.png)

Per analizzare più a fondo questa modifica, passare alla funzionalità Ricerca log facendo clic su **Log Analytics**. In Ricerca log usare la query `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` per cercare le modifiche al contenuto del file hosts. Questa query esegue la ricerca di modifiche apportate al contenuto di file il cui percorso completo contiene la parola "hosts". È anche possibile cercare un file specifico usando il formato completo del percorso anziché una sola parte (ad esempio, `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

Dopo che la query ha restituito i risultati desiderati, fare clic sul pulsante **Nuova regola di avviso** nell'interfaccia di Ricerca log per aprire la pagina per la creazione dell'avviso. È possibile accedere a questa interfaccia anche tramite **Monitoraggio di Azure** nel portale di Azure. Nell'interfaccia per la creazione dell'avviso, controllare di nuovo la query e modificare la logica dell'avviso. In questo caso, si vuole che l'avviso venga attivato anche se viene rilevata una sola modifica in tutti i computer nell'ambiente.

![Immagine che mostra la query per il rilevamento delle modifiche apportate al file hosts](./media/change-tracking/change-query.png)

Dopo che è stata impostata la logica della condizione, assegnare gruppi di azioni in modo da eseguire azioni in risposta all'avviso attivato. In questo caso, è stato specificato di inviare messaggi di posta elettronica e di creare un ticket di Gestione dei servizi IT.  È possibile configurare l'esecuzione di molte altre azioni utili, ad esempio attivare una funzione di Azure, un runbook di automazione, un webhook o un'app per la logica.

![Immagine della configurazione di un gruppo di azioni per avvisare della modifica](./media/change-tracking/action-groups.png)

Dopo aver impostato tutti i parametri e la logica, è possibile applicare l'avviso all'ambiente.

### <a name="alert-suggestions"></a>Suggerimenti sugli avvisi

Anche se la generazione di un avviso relativo alle modifiche apportate al file hosts è una buona applicazione della funzione di generazione degli avvisi per i dati di Rilevamento modifiche e inventario, esistono molti altri casi in cui è utile generare avvisi, come illustrato dalle query di esempio riportate nella sezione seguente.

|Query  |Description  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|Utile per tenere traccia delle modifiche ai file di sistema critici|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Utile per tenere traccia delle modifiche ai file di configurazione importanti|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|Utile per tenere traccia delle modifiche ai servizi di sistema critici|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState != "Running"|Utile per tenere traccia delle modifiche ai servizi di sistema critici|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|Utile per gli ambienti che richiedono il blocco delle configurazioni software|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion != "8.0.11081.0"|Utile per visualizzare i computer in cui è installata una versione del software obsoleta o non conforme. Segnala l'ultimo stato di configurazione segnalato, non le modifiche.|
|ConfigurationChange <br>&#124;dove RegistryKey = = @ "HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Utile per tenere traccia delle modifiche alle chiavi antivirus di importanza cruciale|
|ConfigurationChange <br>&#124;dove RegistryKey contiene @ "HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| Utile per tenere traccia delle modifiche alle impostazioni del firewall|

## <a name="next-steps"></a>Passaggi successivi

Vedere l'esercitazione su Rilevamento modifiche per altre informazioni sull'uso della soluzione:

> [!div class="nextstepaction"]
> [Risolvere i problemi delle modifiche nell'ambiente](automation-tutorial-troubleshoot-changes.md)

* Usare le [ricerche log nei log di monitoraggio di Azure](../log-analytics/log-analytics-log-searches.md) per visualizzare i dati dettagliati del rilevamento delle modifiche.
