---
title: Rilevare le modifiche con Automazione di Azure
description: La soluzione Rilevamento modifiche consente di identificare le modifiche al software e al servizio Windows che si verificano nell'ambiente in uso.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 91a093a44106ad861449b6defb140532698fa668
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Rilevare le modifiche nell'ambiente in uso con la soluzione di rilevamento modifiche

Questo articolo spiega come usare la soluzione Rilevamento modifiche per identificare facilmente le modifiche nell'ambiente. La soluzione rileva le modifiche apportate al software Windows e Linux, ai file di Windows e Linux, alle chiavi del Registro di sistema di Windows, ai servizi di Windows e ai daemon Linux. Rilevando le modifiche alla configurazione è possibile localizzare eventuali problemi operativi.

Le modifiche al software installato, ai servizi di Windows, ai file e al Registro di sistema di Windows e ai daemon Linux nei server monitorati vengono inviate al servizio Log Analytics nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud. Usando le informazioni nel dashboard Change Tracking, è possibile visualizzare facilmente le modifiche apportate all'infrastruttura del server.

## <a name="enable-change-tracking-and-inventory"></a>Abilitare il rilevamento delle modifiche e l'inventario


Per iniziare a tenere traccia delle modifiche, è necessario abilitare la soluzione Rilevamento modifiche e Inventario per l'account di Automazione.

1. Nel portale di Azure passare all'account di Automazione.
1. Selezionare **Rilevamento modifiche** in **CONFIGURAZIONE**.
2. Selezionare un'area di lavoro di Log Analytics esistente oppure **Crea una nuova area di lavoro** e fare clic su **Abilita**.

Verrà abilitata la soluzione per l'account di automazione. L'abilitazione della soluzione può richiedere fino a 15 minuti. Il banner blu notifica quando la soluzione è abilitata. Tornare alla pagina **Rilevamento modifiche** per gestire la soluzione.

## <a name="configuring-change-tracking-and-inventory"></a>Configurazione di Rilevamento modifiche e Inventario

Per informazioni sulla modalità di esecuzione dell'onboarding nella soluzione, vedere [Onboarding Automation solutions](automation-onboard-solutions-from-automation-account.md) (Soluzioni per l'onboarding dall'account di Automazione). Quando si abilita un nuovo file o una chiave del Registro di sistema da rilevare, vengono abilitati sia per Rilevamento modifiche che per Inventario.

### <a name="configure-linux-files-to-track"></a>Configurare i file di Linux da rilevare

Seguire questa procedura per configurare il rilevamento dei file in computer Linux:

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE**. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
2. Nella pagina **Rilevamento modifiche** selezionare **File Linux** e quindi fare clic su **+ Aggiungi** per aggiungere un nuovo file da rilevare.
3. In **Aggiungi file Linux per Rilevamento modifiche** immettere le informazioni per il file o la directory da rilevare e fare clic su **Salva**.

|Proprietà  |DESCRIZIONE  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata.        |
|Item Name     | Nome descrittivo del file da rilevare.        |
|Group     | Nome del gruppo per il raggruppamento logico dei file.        |
|Immettere il percorso     | Percorso in cui cercare il file. Ad esempio: "/etc/*.conf"       |
|Tipo di percorso     | Tipo di elemento da rilevare, i valori possibili sono File e Directory.        |
|Ricorsione     | Determina se viene usata la ricorsione per la ricerca dell'elemento da rilevare.        |
|Usa Sudo     | Questa impostazione determina se viene usato sudo per la ricerca dell'elemento.         |
|Collegamenti     | Questa impostazione determina come vengono gestiti i collegamenti simbolici durante l'attraversamento delle directory.<br> **Ignora**: ignora i collegamenti simbolici e non include i file e le directory a cui viene fatto riferimento.<br>**Segui**: segue i collegamenti simbolici durante la ricorsione e include anche i file e le directory a cui viene fatto riferimento.<br>**Gestisci**: segue i collegamenti simbolici e consente la modifica del contenuto restituito.     |

> [!NOTE]
> Questa opzione dei collegamenti "Gestisci" non è consigliata. Il recupero del contenuto del file non è supportato.

### <a name="configure-windows-files-to-track"></a>Configurare i file di Windows da rilevare

Seguire questa procedura per configurare il rilevamento dei file in computer Windows:

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE**. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
2. Nella pagina **Rilevamento modifiche** selezionare **File Windows** e quindi fare clic su **+ Aggiungi** per aggiungere un nuovo file da rilevare.
3. In **Aggiungi file Windows per Rilevamento modifiche** immettere le informazioni per il file da rilevare e fare clic su **Salva**.

|Proprietà  |DESCRIZIONE  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata.        |
|Item Name     | Nome descrittivo del file da rilevare.        |
|Group     | Nome del gruppo per il raggruppamento logico dei file.        |
|Immettere il percorso     | Percorso in cui cercare il file, ad esempio "c:\temp\myfile.txt"       |

### <a name="configure-windows-registry-keys-to-track"></a>Configurare le chiavi del Registro di Sistema di Windows da rilevare

Seguire questa procedura per configurare il rilevamento delle chiavi del Registro di sistema in computer Windows:

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE**. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
2. Nella pagina **Rilevamento modifiche** selezionare **Registro di sistema di Windows** e quindi fare clic su **+ Aggiungi** per aggiungere una nuova chiave del Registro di sistema da rilevare.
3. In **Aggiungi Registro di sistema di Windows per Rilevamento modifiche** immettere le informazioni per la chiave da rilevare e fare clic su **Salva**.

|Proprietà  |DESCRIZIONE  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata.        |
|Item Name     | Nome descrittivo del file da rilevare.        |
|Group     | Nome del gruppo per il raggruppamento logico dei file.        |
|Chiave del Registro di sistema di Windows   | Percorso in cui cercare il file. Ad esempio: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Limitazioni

La soluzione di Rilevamento modifiche non supporta attualmente gli elementi seguenti:

* Cartelle (directory) per Rilevamento file di Windows
* Ricorsione per Rilevamento file di Windows
* Caratteri jolly per Rilevamento file di Windows
* Variabili di percorso
* File system di rete
* File Content

Altre limitazioni:

* La colonna **Dimensioni massime file** e i valori non sono usati nell'implementazione corrente.
* Se si raccolgono oltre 2500 file in un ciclo di recupero di 30 minuti, è possibile assistere a una riduzione delle prestazioni della soluzione.
* Quando il traffico di rete è elevato, i record di modifica possono impiegare fino a sei ore prima di essere visualizzati.
* Se si modifica la configurazione mentre un computer è arrestato, il computer potrebbe registrare le modifiche che appartenevano alla configurazione precedente.

## <a name="known-issues"></a>Problemi noti

La soluzione Rilevamento modifiche sta riscontrando attualmente i problemi seguenti:
* Gli aggiornamenti rapidi non vengono raccolti per le macchine Windows 10 Creators Update e Windows Server 2016 Core RS3.

## <a name="change-tracking-data-collection-details"></a>Informazioni dettagliate sulla raccolta dei dati di Change Tracking

La tabella seguente mostra la frequenza di raccolta dati per i tipi di modifiche. Per ogni tipo lo snapshot dei dati dello stato corrente viene aggiornato almeno ogni 24 ore:

| **Tipo di modifica** | **Frequenza** |
| --- | --- |
| Registro di sistema di Windows | 50 minuti | 
| File Windows | 30 minuti | 
| File Linux | 15 minuti | 
| Servizi Windows | 30 minuti | 
| Daemon Linux | 5 minuti |
| Software Windows | 30 minuti | 
| Software Linux | 5 minuti | 

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

|Query  |DESCRIZIONE  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Mostra i record dell'inventario più recenti per i servizi Windows che sono stati impostati su Auto (Automatico) ma sono stati segnalati come Stopped (Interrotto)<br>I risultati sono limitati al record più recente per SoftwareName e Computer      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|Mostra i record di modifica per il software rimosso|

## <a name="next-steps"></a>Passaggi successivi

Vedere l'esercitazione su Rilevamento modifiche per altre informazioni sull'uso della soluzione:

> [!div class="nextstepaction"]
> [Risolvere i problemi delle modifiche nell'ambiente](automation-tutorial-troubleshoot-changes.md)

* Per visualizzare dati dettagliati di monitoraggio delle modifiche, usare [Ricerche nei log in Log Analytics](../log-analytics/log-analytics-log-searches.md) .
