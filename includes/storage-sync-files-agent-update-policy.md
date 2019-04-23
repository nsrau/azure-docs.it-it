---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: 4f59f68c1598f737ea7cb3a0e8046fc0779ed9d3
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60118639"
---
L'agente Sincronizzazione file di Azure viene aggiornato a intervalli regolari per aggiungere nuove funzionalità e risolvere eventuali problemi. È consigliabile configurare Microsoft Update per ricevere gli aggiornamenti dell'agente Sincronizzazione file di Azure non appena vengono rilasciati.

#### <a name="major-vs-minor-agent-versions"></a>Confronto tra versioni principali e secondarie dell'agente
* Le versioni principali dell'agente contengono spesso nuove funzionalità e hanno un numero crescente come prima parte del numero di versione. Ad esempio:  \*2.\*.\*\*
* Le versioni secondarie dell'agente sono denominate anche "patch" e vengono rilasciate con maggiore frequenza rispetto alle versioni principali. Spesso contengono correzioni di bug e miglioramenti di entità minore, ma non nuove funzionalità. Ad esempio: \*\*.3.\*\*

#### <a name="upgrade-paths"></a>Percorsi di aggiornamento
Esistono quattro modi approvati e testati per installare gli aggiornamenti dell'agente Sincronizzazione file di Azure. 
1. **(Consigliato) Configurare Microsoft Update per scaricare e installare automaticamente gli aggiornamenti dell'agente.**  
    È consigliabile scaricare tutti gli aggiornamenti di Sincronizzazione file di Azure per avere accesso alle ultime correzioni per l'agente server. Microsoft Update semplifica questo processo, scaricando e installando gli aggiornamenti automaticamente.
2. **Usare AfsUpdater.exe per scaricare e installare gli aggiornamenti dell'agente.**  
    Il file AfsUpdater.exe si trova nella directory di installazione dell'agente. Fare doppio clic sul file eseguibile per scaricare e installare gli aggiornamenti dell'agente. 
3. **Applicare una patch a un agente Sincronizzazione file di Azure esistente usando un file di patch di Microsoft Update o un eseguibile con estensione msp. L'ultimo aggiornamento di Sincronizzazione file di Azure può essere scaricato da [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Eseguendo un eseguibile con estensione msp, l'installazione di Sincronizzazione file di Azure viene aggiornata con lo stesso metodo usato automaticamente da Microsoft Update nel percorso di aggiornamento precedente. Applicando una patch di Microsoft Update verrà eseguito un aggiornamento sul posto di un'installazione di Sincronizzazione file di Azure.
4. **Scaricare il programma di installazione di agente sincronizzazione File di Azure più recente dal [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Per aggiornare un'installazione esistente dell'agente Sincronizzazione file di Azure, disinstallare la versione precedente e quindi installare la versione più recente dal programma di installazione scaricato. Il programma di installazione di Sincronizzazione file di Azure mantiene la registrazione del server, i gruppi di sincronizzazione e tutte le altre impostazioni.

#### <a name="automatic-agent-lifecycle-management"></a>Gestione agente automatica del ciclo di vita
Con la versione dell'agente 6, il team di sincronizzazione file ha introdotto una funzionalità di aggiornamento automatico dell'agente. È possibile selezionare una delle due modalità e specificare una finestra di manutenzione in cui l'aggiornamento deve essere eseguita nel server. Questa funzionalità è progettata per facilitare la gestione del ciclo di vita dell'agente fornendo un guardrail impedendo l'agente dalla scadenza o consentendo un senza problemi, mantenere l'impostazione corrente.
1. Il **impostazione predefinita** proverà a evitare che l'agente dalla scadenza. Entro 21 giorni dalla data di scadenza registrata di un agente, l'agente tenterà di eseguire l'aggiornamento automatico. Verrà avviato un tentativo di eseguire l'aggiornamento di una volta a settimana entro 21 giorni prima della scadenza e nella finestra di manutenzione selezionato. **Questa opzione non elimina la necessità per l'esecuzione regolarmente patch di Microsoft Update.**
2. Facoltativamente, è possibile selezionare che l'agente verrà aggiornato automaticamente se stesso, non appena diventa disponibile una nuova versione dell'agente. Verrà inoltre si verificano durante la finestra di manutenzione selezionati e consentire al server di trarre vantaggio dalle nuove funzionalità e miglioramenti, non appena diventano disponibili a livello generale. Questo è l'impostazione consigliata, preoccupazioni che fornisce le versioni principali dell'agente, nonché le patch di aggiornamento regolare per il server.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garanzie relative al ciclo di vita dell'agente e alla gestione del cambiamento
Sincronizzazione File di Azure è un servizio cloud, che introduce continuamente miglioramenti e nuove funzionalità. Ciò significa che una specifica versione dell'agente Sincronizzazione file di Azure può essere supportata solo per un periodo di tempo limitato. Per facilitare la distribuzione, le regole seguenti assicurarti di che avere sufficiente tempo e la notifica per contenere aggiornamenti dell'agente nel processo di gestione delle modifiche:

- Le versioni principali dell'agente sono supportate per almeno sei mesi dalla data di rilascio iniziale.
- Microsoft garantisce che una sovrapposizione di almeno tre mesi tra il supporto delle versioni principali dell'agente. 
- Per i server registrati che usano un agente prossimo alla scadenza vengono generati avvisi almeno tre mesi prima della scadenza. È possibile verificare se un server registrato sta usando una versione precedente dell'agente nella sezione dei server registrati di un servizio di sincronizzazione archiviazione.
- La durata di una versione secondaria dell'agente è legata alla versione principale associata. Ad esempio, quando viene rilasciata la versione 3.0 dell'agente, le versioni 2.\* saranno tutte impostate in modo da scadere insieme.

> [!Note]
> L'installazione di una versione dell'agente con un avviso di scadenza comporterà la visualizzazione di un avviso ma avrà esito positivo. Il tentativo di installare o connettersi a una versione scaduta dell'agente non è supportato e verrà bloccato.
