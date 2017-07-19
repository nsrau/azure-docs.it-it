---
title: Rilevare le modifiche con Log Analytics di Azure | Microsoft Docs
description: La soluzione Rilevamento modifiche in Log Analytics consente di identificare le modifiche al software e al servizio Windows che si verificano nell'ambiente in uso.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 7e0fa9a83c3c83145a4813422bf73a0e711d0ecc
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="track-software-changes-in-your-environment-with-the-change-tracking-solution"></a>Rilevare le modifiche software nell'ambiente in uso con la soluzione di rilevamento modifiche

![Simbolo di Rilevamento modifiche](./media/log-analytics-change-tracking/change-tracking-symbol.png)

Questo articolo spiega come usare la soluzione di rilevamento delle modifiche in Log Analytics per identificare facilmente le modifiche nell'ambiente. La soluzione rileva le modifiche apportate al software Windows e Linux, ai file e alle chiavi del Registro di Sistema di Windows, ai servizi di Windows e ai daemon Linux. Rilevando le modifiche alla configurazione è possibile localizzare eventuali problemi operativi.

La soluzione viene installata per aggiornare il tipo di agente che è stato installato. La soluzione legge le modifiche al software installato, ai servizi Windows e ai daemon Linux nei server monitorati e invia i dati al servizio Log Analytics nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud. Usando le informazioni nel dashboard Change Tracking, è possibile visualizzare facilmente le modifiche apportate all'infrastruttura del server.

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

* È necessario avere un agente [Windows](log-analytics-windows-agents.md), [Operations Manager](log-analytics-om-agents.md) o [Linux](log-analytics-linux-agents.md) su ogni computer in cui si vuole monitorare le modifiche.
* Aggiungere la soluzione Rilevamento modifiche all'area di lavoro OMS da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ChangeTrackingOMS?tab=Overview) o seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).  Non è richiesta alcuna ulteriore configurazione.

### <a name="configure-linux-files-to-track"></a>Configurare i file di Linux da rilevare
Seguire questa procedura per configurare i file da rilevare in computer Linux.

1. Nel portale di OMS, fare clic su **Impostazioni** (simbolo dell'ingranaggio).
2. Nella pagina **Impostazioni** fare clic su **Dati** e quindi su **Rilevamento file di Linux**.
3. In Rilevamento modifiche file di Linux digitare l'intero percorso, includendo il nome del file che si vuole rilevare, e quindi fare clic sul simbolo **Aggiungi**. Ad esempio: "/etc/*.conf"
4. Fare clic su **Salva**.  
  
> [!NOTE]
> Il rilevamento dei file di Linux contiene funzionalità aggiuntive, tra cui il rilevamento di directory, la ricorsione tramite directory e il rilevamento di caratteri jolly.

### <a name="configure-windows-files-to-track"></a>Configurare i file di Windows da rilevare
Seguire questa procedura per configurare i file da rilevare in computer Windows.

1. Nel portale di OMS, fare clic su **Impostazioni** (simbolo dell'ingranaggio).
2. Nella pagina **Impostazioni** fare clic su **Dati** e poi su **Rilevamento file di Windows**.
3. In Rilevamento modifiche file di Windows digitare l'intero percorso, includendo il nome del file che si vuole rilevare, e fare clic sul simbolo **Aggiungi**. Ad esempio: C:\Programmi (x86)\Internet Explorer\iexplore.exe o C:\Windows\System32\drivers\etc\hosts.
4. Fare clic su **Save**.  
   ![Rilevamento modifiche file di Windows](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="configure-windows-registry-keys-to-track"></a>Configurare le chiavi del Registro di Sistema di Windows da rilevare
Seguire questa procedura per configurare le chiavi del Registro di Sistema da rilevare nei computer Windows.

1. Nel portale di OMS, fare clic su **Impostazioni** (simbolo dell'ingranaggio).
2. Nella pagina **Impostazioni** fare clic su **Dati** e poi su **Rilevamento del Registro di sistema di Windows**.
3. In Rilevamento modifiche del Registro di sistema di Windows digitare l'intera chiave che si vuole rilevare e fare clic sul simbolo **Aggiungi**.
4. Fare clic su **Salva**.  
   ![Rilevamento modifiche del Registro di sistema Windows](./media/log-analytics-change-tracking/windows-registry-change-tracking.png)

### <a name="explanation-of-linux-file-collection-properties"></a>Spiegazione delle proprietà di raccolta di file di Linux
1. **Tipo**
   * **File** (Indicare i metadati di file: dimensioni, data di modifica, hash, ecc.)
   * **Directoy** (Indicare i metadati di directory: dimensioni, data di modifica, ecc.)
2. **Collegamenti** (Gestione dei riferimenti dei collegamenti simbolici di Linux ad altri file o directory)
   * **Ignora** (Ignorare i collegamenti simbolici durante le ricorsioni per non includere i file/le directory a cui viene fatto riferimento)
   * **Segui** (Seguire i collegamenti simbolici durante la ricorsione per includere anche i file/le directory a cui viene fatto riferimento)
   * **Gestisci** (Seguire i collegamenti simbolici e modificare la modalità di gestione del contenuto restituito) 
   > [!NOTE]   
   > L'opzione dei collegamenti "Gestisci" non è consigliata poiché il recupero del contenuto di file non è supportato attualmente.
3. **Esegui ricorsione** (Eseguire la ricorsione dei livelli di cartelle e rilevare tutti i file che soddisfano l'istruzione di percorso)
4. **Sudo** (Consentire di accedere a file o directory che richiedono il privilegio sudo)

### <a name="limitations"></a>Limitazioni
La soluzione di rilevamento modifiche non supporta attualmente gli elementi seguenti:

* Cartelle (directory) per Rilevamento file di Windows
* Ricorsione per Rilevamento file di Windows
* Caratteri jolly per Rilevamento file di Windows
* Variabili di percorso
* File system di rete
* File Content

Altre limitazioni:

* La colonna **Dimensioni massime file** e i valori non sono usati nell'implementazione corrente.
* Se si raccolgono oltre 2500 file in un ciclo di recupero di 30 minuti, è possibile assistere a una riduzione delle prestazioni della soluzione.
* Quando il traffico di rete è elevato, i record di modifica possono impiegare fino a un massimo di sei ore prima di essere visualizzati.
* Se si modifica la configurazione mentre un computer è arrestato, il computer potrebbe registrare le modifiche ai file che appartenevano alla configurazione precedente.

## <a name="change-tracking-data-collection-details"></a>Informazioni dettagliate sulla raccolta dei dati di Change Tracking
Change Tracking consente di raccogliere l'inventario software e i metadati del servizio Windows tramite gli agenti abilitati.

La tabella seguente mostra i metodi di raccolta di dati e altre informazioni dettagliate sul modo in cui vengono raccolti i dati per Change Tracking.

| piattaforma | Agente diretto | Agente SCOM | Agente Linux | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows e Linux |![Sì](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Sì](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Sì](./media/log-analytics-change-tracking/oms-bullet-green.png) |![No](./media/log-analytics-change-tracking/oms-bullet-red.png) |![No](./media/log-analytics-change-tracking/oms-bullet-red.png) |![Sì](./media/log-analytics-change-tracking/oms-bullet-green.png) | Da 5 minuti a 50 minuti, a seconda del tipo di modifica. Per altre informazioni, vedere di seguito. |


La tabella seguente mostra la frequenza di raccolta dati per i tipi di modifiche.

| **tipo di modifica** | **frequency** | **L'****agente** **invia le differenze quando vengono rilevate?**  |
| --- | --- | --- |
| Registro di sistema di Windows | 50 minuti | No |
| File Windows | 30 minuti | Sì. Se non si verifica alcun cambiamento in 24 ore, viene inviato uno snapshot. |
| File Linux | 15 minuti | Sì. Se non si verifica alcun cambiamento in 24 ore, viene inviato uno snapshot. |
| Servizi Windows | 30 minuti | Sì, ogni 30 minuti quando vengono rilevate modifiche. Ogni 24 ore viene inviato uno snapshot, indipendentemente dalle modifiche. Pertanto, lo snapshot viene inviato anche se sono state apportate modifiche. |
| Daemon Linux | 5 minuti | Sì. Se non si verifica alcun cambiamento in 24 ore, viene inviato uno snapshot. |
| Software Windows | 30 minuti | Sì, ogni 30 minuti quando vengono rilevate modifiche. Ogni 24 ore viene inviato uno snapshot, indipendentemente dalle modifiche. Pertanto, lo snapshot viene inviato anche se sono state apportate modifiche. |
| Software Linux | 5 minuti | Sì. Se non si verifica alcun cambiamento in 24 ore, viene inviato uno snapshot. |

### <a name="registry-key-change-tracking"></a>Rilevamento delle modifiche della chiave del Registro di Sistema

Log Analytics esegue il monitoraggio e rilevamento del Registro di sistema Windows con la soluzione Rilevamento modifiche. Lo scopo del monitoraggio delle modifiche alle chiavi del Registro di sistema è di eseguire il pinpoint dei punti di estensibilità dove è possibile che vengano attivati codice di terze parti e malware. L'elenco seguente mostra le chiavi del Registro di sistema predefinite che vengono rilevate dalla soluzione e perché.

- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup
    - Monitora gli script eseguiti all'avvio.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown
    - Monitora gli script eseguiti all'arresto del sistema.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run
    - Monitora le chiavi che vengono caricate prima l'accesso degli utenti nel proprio account di Windows per i programmi a 32 bit in esecuzione su computer a 64 bit.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components
    - Monitora le modifiche apportate alle impostazioni dell'applicazione.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers
    - Monitora le voci di avvio automatico che si collegano direttamente in Esplora risorse e che in genere funzionano in-process con Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers
    - Monitora le voci di avvio automatico che si collegano direttamente in Esplora risorse e che in genere funzionano in-process con Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Background\ShellEx\ContextMenuHandlers
    - Monitora le voci di avvio automatico che si collegano direttamente in Esplora risorse e che in genere funzionano in-process con Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - Monitora la registrazione del gestore della sovrapposizione delle icone.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - Monitora la registrazione del gestore della sovrapposizione delle icone per i programmi a 32 bit in esecuzione su computer a 64 bit.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - Monitora i nuovi plugin degli oggetti helper del browser per Internet Explorer, che possono essere usati per accedere al DOM (Document Object Model) della pagina corrente e per controllare la navigazione.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - Monitora i nuovi plugin degli oggetti helper del browser per Internet Explorer, che possono essere usati per accedere al DOM (Document Object Model) della pagina corrente e per controllare la navigazione per i programmi a 32 bit in esecuzione su computer a 64 bit.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions
    - Monitora le nuove estensioni di Internet Explorer, come i menu degli strumenti personalizzati e i pulsanti della barra degli strumenti personalizzata.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions
    - Monitora le nuove estensioni di Internet Explorer, come i menu degli strumenti personalizzati e i pulsanti della barra degli strumenti personalizzata per i programmi a 32 bit in esecuzione su computer a 64 bit.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    - Monitora i driver a 32 bit associati con wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. È simile alla sezione [driver] nel file SYSTEM.INI.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    - Monitora i driver a 32 bit associati con wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc per i programmi a 32 bit in esecuzione su computer a 64 bit. È simile alla sezione [driver] nel file SYSTEM.INI.
- HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls
    - Monitora l'elenco delle DLL di sistema note o comunemente usate. Questo sistema impedisce alle persone di sfruttare autorizzazioni vulnerabili delle directory delle applicazioni rilasciando trojan nelle DLL di sistema.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify
    - Monitora l'elenco dei pacchetti in grado di ricevere le notifiche degli eventi da Winlogon, il modello di supporto di accesso interattivo per il sistema operativo Windows.


## <a name="use-change-tracking"></a>Uso di Change Tracking
Dopo l'installazione della soluzione, è possibile visualizzare il riepilogo delle modifiche per i server monitorati usando il riquadro **Rilevamento modifiche** nella pagina **Panoramica** di OMS.

![immagine del riquadro Change Tracking](./media/log-analytics-change-tracking/change-tracking-tile.png)

È possibile vedere le modifiche all'infrastruttura e quindi analizzare i dettagli per le categorie seguenti:

* Modifiche per tipo di configurazione per i servizi software e di Windows.
* Modifiche software ad applicazioni e aggiornamenti per singoli server.
* Numero totale di modifiche software per ogni applicazione.
* Pacchetti Linux
* Modifiche dei servizi di Windows per singoli server.
* Modifiche a daemon Linux

![image of Change Tracking dashboard](./media/log-analytics-change-tracking/change-tracking-dash01.png)

![image of Change Tracking dashboard](./media/log-analytics-change-tracking/change-tracking-dash02.png)

### <a name="to-view-changes-for-any-change-type"></a>Per visualizzare le modifiche di qualsiasi tipo
1. Nella pagina **Panoramica** fare clic sul riquadro **Rilevamento modifiche**.
2. Nel dashboard **Rilevamento modifiche** esaminare le informazioni di riepilogo in uno dei pannelli dei tipi di modifiche e quindi fare clic su un tipo per visualizzare le relative informazioni dettagliate nella pagina di **ricerca nei log**.
3. In una pagina di ricerca di log qualsiasi è possibile visualizzare i risultati in base all'ora, ai dettagli e alla cronologia di ricerca. È anche possibile filtrare per facet in modo da limitare i risultati.

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare dati dettagliati di monitoraggio delle modifiche, usare [Ricerche nei log in Log Analytics](log-analytics-log-searches.md) .

