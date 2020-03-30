---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123185"
---
L'agente Sincronizzazione file di Azure viene aggiornato a intervalli regolari per aggiungere nuove funzionalità e risolvere eventuali problemi. È consigliabile configurare Microsoft Update per ricevere gli aggiornamenti dell'agente Sincronizzazione file di Azure non appena vengono rilasciati.

#### <a name="major-vs-minor-agent-versions"></a>Confronto tra versioni principali e secondarie dell'agente
* Le versioni principali dell'agente contengono spesso nuove funzionalità e hanno un numero crescente come prima parte del numero di versione. Ad esempio: \*2.\*.\*\*
* Le versioni secondarie dell'agente sono denominate anche "patch" e vengono rilasciate con maggiore frequenza rispetto alle versioni principali. Spesso contengono correzioni di bug e miglioramenti di entità minore, ma non nuove funzionalità. Ad esempio: \*\*.3.\*\*

#### <a name="upgrade-paths"></a>Percorsi di aggiornamento
Esistono quattro modi approvati e testati per installare gli aggiornamenti dell'agente Sincronizzazione file di Azure. 
1. **(Consigliato) Configurare Microsoft Update per scaricare e installare automaticamente gli aggiornamenti dell'agente.**  
    È consigliabile scaricare tutti gli aggiornamenti di Sincronizzazione file di Azure per avere accesso alle ultime correzioni per l'agente server. Microsoft Update semplifica questo processo, scaricando e installando gli aggiornamenti automaticamente.
2. **Usare AfsUpdater.exe per scaricare e installare gli aggiornamenti dell'agente.**  
    Il file AfsUpdater.exe si trova nella directory di installazione dell'agente. Fare doppio clic sul file eseguibile per scaricare e installare gli aggiornamenti dell'agente. 
3. **Applicare una patch a un agente di sincronizzazione file di Azure esistente usando un file di patch di Microsoft Update o un file eseguibile con estensione msp. Il pacchetto di aggiornamento più recente di Sincronizzazione file di Azure può essere scaricato dal catalogo di [Microsoft Update.](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync)**  
    Eseguendo un eseguibile con estensione msp, l'installazione di Sincronizzazione file di Azure viene aggiornata con lo stesso metodo usato automaticamente da Microsoft Update nel percorso di aggiornamento precedente. Applicando una patch di Microsoft Update verrà eseguito un aggiornamento sul posto di un'installazione di Sincronizzazione file di Azure.
4. **Scaricare il programma di installazione dell'agente di sincronizzazione file di Azure più recente [dall'Area download Microsoft](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Per aggiornare un'installazione esistente dell'agente Sincronizzazione file di Azure, disinstallare la versione precedente e quindi installare la versione più recente dal programma di installazione scaricato. Il programma di installazione di Sincronizzazione file di Azure mantiene la registrazione del server, i gruppi di sincronizzazione e tutte le altre impostazioni.

#### <a name="automatic-agent-lifecycle-management"></a>Gestione automatica del ciclo di vita degli agenti
Con l'agente versione 6, il team di sincronizzazione dei file ha introdotto una funzionalità di aggiornamento automatico dell'agente. È possibile selezionare una delle due modalità e specificare una finestra di manutenzione in cui eseguire l'aggiornamento sul server. Questa funzionalità è progettata per facilitare la gestione del ciclo di vita dell'agente fornendo un guardrail che impedisce la scadenza dell'agente o consentendo un'impostazione senza problemi e rimani aggiornata.
1. **L'impostazione predefinita** tenterà di impedire la scadenza dell'agente. Entro 21 giorni dalla data di scadenza registrata di un agente, l'agente tenterà di eseguire l'autoaggiornamento. Si avvierà un tentativo di aggiornamento una volta alla settimana entro 21 giorni prima della scadenza e nella finestra di manutenzione selezionata. **Questa opzione non elimina la necessità di applicare patch regolari di Microsoft Update.**
1. Facoltativamente, è possibile selezionare che l'agente si aggiorni automaticamente non appena sarà disponibile una nuova versione dell'agente (attualmente non applicabile ai server del cluster). Questo aggiornamento si verifica durante la finestra di manutenzione selezionata e consente al server di trarre vantaggio da nuove funzionalità e miglioramenti non appena diventano disponibili in generale. Questa è l'impostazione consigliata e senza preoccupazioni che fornirà le principali versioni dell'agente e le patch di aggiornamento regolari al server. Ogni agente rilasciato è di qualità GA. Se si seleziona questa opzione, Microsoft verrà rilassata all'utente la versione più recente dell'agente. I server del cluster sono esclusi. Al termine dell'anteprima, l'agente sarà disponibile anche [nell'Area download Microsoft](https://go.microsoft.com/fwlink/?linkid=858257) aka.ms/AFS/agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Modifica dell'impostazione di aggiornamento automatico

Le istruzioni seguenti descrivono come modificare le impostazioni dopo aver completato il programma di installazione, se è necessario apportare modifiche.

Aprire una console di PowerShell e passare alla directory in cui è stato installato l'agente di sincronizzazione, quindi importare i cmdlet del server. Per impostazione predefinita, questo sarebbe simile al seguente:By default this would look something like this:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

È possibile `Get-StorageSyncAgentAutoUpdatePolicy` eseguire per controllare l'impostazione del criterio corrente e determinare se si desidera modificarla.

Per modificare l'impostazione del criterio corrente per la traccia di aggiornamento ritardato, è possibile utilizzare:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Per modificare l'impostazione del criterio corrente per la traccia di aggiornamento immediato, è possibile utilizzare:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garanzie relative al ciclo di vita dell'agente e alla gestione del cambiamento
Sincronizzazione file di Azure è un servizio cloud che introduce continuamente nuove funzionalità e miglioramenti. Ciò significa che una specifica versione dell'agente Sincronizzazione file di Azure può essere supportata solo per un periodo di tempo limitato. Per facilitare la distribuzione, le regole seguenti consentono di disporre di tempo e notifiche sufficienti per supportare gli aggiornamenti/aggiornamenti degli agenti nel processo di gestione delle modifiche:To facilitate your deployment, the following rules guarantee you have enough time and notification to accommodate agent updates/upgrades in your change management process:

- Le versioni principali dell'agente sono supportate per almeno sei mesi dalla data di rilascio iniziale.
- Microsoft garantisce che una sovrapposizione di almeno tre mesi tra il supporto delle versioni principali dell'agente. 
- Per i server registrati che usano un agente prossimo alla scadenza vengono generati avvisi almeno tre mesi prima della scadenza. È possibile verificare se un server registrato sta usando una versione precedente dell'agente nella sezione dei server registrati di un servizio di sincronizzazione archiviazione.
- La durata di una versione secondaria dell'agente è legata alla versione principale associata. Ad esempio, quando viene rilasciata la versione 3.0 dell'agente, le versioni 2.\* saranno tutte impostate in modo da scadere insieme.

> [!Note]
> L'installazione di una versione dell'agente con un avviso di scadenza comporterà la visualizzazione di un avviso ma avrà esito positivo. Il tentativo di installare o connettersi a una versione scaduta dell'agente non è supportato e verrà bloccato.
