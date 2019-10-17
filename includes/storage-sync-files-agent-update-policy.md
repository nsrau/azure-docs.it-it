---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: 02e9553b9704c96794e0c1113ab3e06458f0f7c8
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391685"
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
3. **Applicare una patch a un agente di Sincronizzazione file di Azure esistente utilizzando un file di patch Microsoft Update o un eseguibile con estensione msp. Il pacchetto di aggiornamento Sincronizzazione file di Azure più recente può essere scaricato dal [Catalogo di Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Eseguendo un eseguibile con estensione msp, l'installazione di Sincronizzazione file di Azure viene aggiornata con lo stesso metodo usato automaticamente da Microsoft Update nel percorso di aggiornamento precedente. Applicando una patch di Microsoft Update verrà eseguito un aggiornamento sul posto di un'installazione di Sincronizzazione file di Azure.
4. **Scaricare il programma di installazione di Sincronizzazione file di Azure Agent più recente dall' [area download Microsoft](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Per aggiornare un'installazione esistente dell'agente Sincronizzazione file di Azure, disinstallare la versione precedente e quindi installare la versione più recente dal programma di installazione scaricato. Il programma di installazione di Sincronizzazione file di Azure mantiene la registrazione del server, i gruppi di sincronizzazione e tutte le altre impostazioni.

#### <a name="automatic-agent-lifecycle-management"></a>Gestione automatica del ciclo di vita dell'agente
Con l'agente versione 6, il team di sincronizzazione file ha introdotto una funzionalità di aggiornamento automatico dell'agente. È possibile selezionare una delle due modalità e specificare una finestra di manutenzione in cui verrà eseguito il tentativo di aggiornamento sul server. Questa funzionalità è stata progettata per semplificare la gestione del ciclo di vita degli agenti, fornendo una barriera di barriera che impedisce la scadenza dell'agente o che consente di mantenere le impostazioni correnti.
1. L' **impostazione predefinita** tenterà di impedire la scadenza dell'agente. Entro 21 giorni dalla data di scadenza pubblicata di un agente, l'agente tenterà di eseguire l'aggiornamento automatico. Verrà avviato un tentativo di aggiornamento una volta alla settimana entro 21 giorni prima della scadenza e nella finestra di manutenzione selezionata. **Questa opzione non elimina la necessità di utilizzare patch Microsoft Update regolari.**
1. Facoltativamente, è possibile selezionare l'aggiornamento automatico dell'agente non appena una nuova versione dell'agente diventa disponibile (attualmente non applicabile ai server in cluster). Questo aggiornamento si verificherà durante la finestra di manutenzione selezionata e consentirà al server di usufruire di nuove funzionalità e miglioramenti non appena diventano disponibili a livello generale. Si tratta dell'impostazione consigliata e senza preoccupazioni che fornirà le versioni principali dell'agente, nonché le patch di aggiornamento regolari per il server. Ogni agente rilasciato è alla qualità GA. Se si seleziona questa opzione, Microsoft effettuerà il volo della versione più recente dell'agente. I server del cluster sono esclusi. Una volta completato il volo, l'agente diventerà disponibile anche nell' [area download Microsoft](https://go.microsoft.com/fwlink/?linkid=858257) aka.ms/AFS/Agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Modifica dell'impostazione di aggiornamento automatico

Le istruzioni seguenti descrivono come modificare le impostazioni dopo aver completato il programma di installazione, se è necessario apportare modifiche.

Aprire una console di PowerShell e passare alla directory in cui è stato installato l'agente di sincronizzazione e quindi importare i cmdlet del server. Per impostazione predefinita, il risultato sarà simile al seguente:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name \StorageSync.Management.ServerCmdlets.dll
```

È possibile eseguire `Get-StorageSyncAgentAutoUpdatePolicy` per controllare l'impostazione dei criteri corrente e determinare se si desidera modificarla.

Per modificare l'impostazione dei criteri corrente sulla traccia di aggiornamento ritardata, è possibile usare:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Per modificare l'impostazione dei criteri corrente sulla traccia di aggiornamento immediato, è possibile usare:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garanzie relative al ciclo di vita dell'agente e alla gestione del cambiamento
Sincronizzazione file di Azure è un servizio cloud che introduce continuamente nuove funzionalità e miglioramenti. Ciò significa che una specifica versione dell'agente Sincronizzazione file di Azure può essere supportata solo per un periodo di tempo limitato. Per semplificare la distribuzione, le regole seguenti garantiscono che l'utente disponga di un tempo sufficiente e di una notifica per gestire gli aggiornamenti o gli aggiornamenti degli agenti nel processo di gestione delle modifiche:

- Le versioni principali dell'agente sono supportate per almeno sei mesi dalla data di rilascio iniziale.
- Microsoft garantisce che una sovrapposizione di almeno tre mesi tra il supporto delle versioni principali dell'agente. 
- Per i server registrati che usano un agente prossimo alla scadenza vengono generati avvisi almeno tre mesi prima della scadenza. È possibile verificare se un server registrato sta usando una versione precedente dell'agente nella sezione dei server registrati di un servizio di sincronizzazione archiviazione.
- La durata di una versione secondaria dell'agente è legata alla versione principale associata. Ad esempio, quando viene rilasciata la versione 3.0 dell'agente, le versioni 2.\* saranno tutte impostate in modo da scadere insieme.

> [!Note]
> L'installazione di una versione dell'agente con un avviso di scadenza comporterà la visualizzazione di un avviso ma avrà esito positivo. Il tentativo di installare o connettersi a una versione scaduta dell'agente non è supportato e verrà bloccato.
