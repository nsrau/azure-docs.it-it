---
title: Note sulla versione di Microsoft Azure Stack Development Kit | Documenti Microsoft
description: Miglioramenti, correzioni e problemi noti per Azure Stack Development Kit.
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: andredm
ms.openlocfilehash: 88247733c945de277e4d74b049d5edc6e4d97d3b
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="azure-stack-development-kit-release-notes"></a>Note sulla versione di Azure Stack Development Kit

*Si applica a: Azure Stack Development Kit*

Queste note sulla versione offrono informazioni sui miglioramenti e correzioni di problemi noti in Azure Stack Development Kit. Se non si è certi quale versione è in esecuzione, è possibile [utilizzare il portale per controllare](azure-stack-updates.md#determine-the-current-version).

## <a name="build-201801032"></a>Build 20180103.2

### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni

- Vedere il [nuove funzionalità e correzioni](azure-stack-update-1712.md#new-features-and-fixes) sezione di Azure Stack 1712 aggiornamento le note sulla versione per Azure Stack integrati sistemi.

    > [!IMPORTANT]
    > Alcuni degli elementi elencati nel **nuove funzionalità e correzioni** sezione sono rilevanti solo per i sistemi Azure Stack integrato.

### <a name="known-issues"></a>Problemi noti
 
#### <a name="deployment"></a>Distribuzione
- È necessario specificare un server utilizzando un indirizzo IP durante la distribuzione.

#### <a name="infrastructure-management"></a>Gestione dell'infrastruttura
- Non abilitare il backup di infrastruttura nel **backup infrastruttura** blade.
- L'indirizzo IP di baseboard management controller (BMC) e il modello non vengono visualizzati le informazioni essenziali di un nodo di unità di scala. Questo comportamento è previsto nel Kit di sviluppo dello Stack di Azure.

#### <a name="portal"></a>Portale
- È possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, selezionare l'icona dell'ingranaggio in alto a destra del portale e quindi selezionare **ripristinare le impostazioni predefinite**.
- Quando si visualizzano le proprietà di un gruppo di risorse, il **spostare** pulsante è disabilitato. Questo comportamento è previsto. Spostare i gruppi di risorse tra le sottoscrizioni non è attualmente supportato.
-  Per qualsiasi flusso di lavoro in cui si seleziona una sottoscrizione, gruppo di risorse o alla posizione in un elenco a discesa, si verifichi uno o più dei seguenti problemi:

   - È possibile visualizzare una riga vuota nella parte superiore dell'elenco. È comunque possibile selezionare un elemento come previsto.
   - Se l'elenco di elementi nell'elenco a discesa è breve, non sarà possibile visualizzare i nomi degli elementi.
   - Se si dispone di più sottoscrizioni utente, l'elenco di riepilogo a discesa gruppo di risorse può essere vuoto. 

   Per risolvere i problemi ultimi due, è possibile digitare il nome della sottoscrizione o del gruppo di risorse (se si conosce) oppure è possibile usare PowerShell.

- Verrà visualizzato un **attivazione richiesto** messaggio di avviso che consiglia di registrare il Kit di sviluppo dello Stack di Azure. Questo comportamento è previsto.
- Se il **componente** si fa clic sul collegamento da qualsiasi **ruolo infrastruttura** di avviso, il valore risultante **Panoramica** pannello tenta di caricare e ha esito negativo. Inoltre il * * Panoramica * * blade non ha timeout.
- Se si elimina utente sottoscrizioni nelle risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.
- Non è in grado di visualizzare le autorizzazioni per la sottoscrizione tramite i portali di Stack di Azure. In alternativa, è possibile verificare le autorizzazioni tramite PowerShell.
 
#### <a name="marketplace"></a>Marketplace
- Rimuovere alcuni elementi del marketplace in questa versione per motivi di compatibilità. Questi saranno nuovamente abilitati dopo la convalida ulteriormente.
- Gli utenti sfogliare il marketplace completo senza una sottoscrizione e di visualizzare gli elementi di amministrazione come piani e alle offerte. Questi elementi sono non funzionale agli utenti.
 
#### <a name="compute"></a>Calcolo
- Gli utenti assegnati l'opzione per creare una macchina virtuale con l'archiviazione con ridondanza geografica. Questa configurazione causa l'errore durante la creazione macchina virtuale. 
- È possibile configurare una macchina virtuale set di disponibilità solo con un dominio di errore di uno e un dominio di aggiornamento di uno.
- Non sussiste alcuna esperienza di marketplace per creare il set di scalabilità di macchine virtuali. È possibile creare un set, utilizzando un modello di scalabilità.
- Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile utilizzare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario utilizzare il `-Name` parametro anziché `-VMScaleSetName`.

#### <a name="networking"></a>Rete
- Tramite il portale, è possibile creare un servizio di bilanciamento del carico con un indirizzo IP pubblico. In alternativa, è possibile utilizzare PowerShell per creare il servizio di bilanciamento del carico.
- Quando si crea un servizio di bilanciamento del carico di rete, è necessario creare una regola di translation (NAT) indirizzo di rete. In caso contrario, si riceverà un errore quando si tenta di aggiungere una regola NAT, dopo aver creato il bilanciamento del carico.
- In **rete**, se si fa clic su **connessione** per impostare una connessione VPN, **per rete virtuale a** è elencato come un tipo di connessione. Non selezionare questa opzione. Attualmente, solo il **Site-to-site (IPsec)** opzione è supportata.
- Dopo aver creata e associata a tale indirizzo IP della macchina virtuale è non è possibile eliminare l'associazione di un indirizzo IP pubblico da una macchina virtuale (VM). Disassociazione verrà visualizzata a funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato con la macchina virtuale originale. Questo comportamento si verifica anche se si riassegna l'indirizzo IP per una nuova macchina virtuale (conosciuta come un *scambio VIP*). Tutti i successivi tentativi di connessione attraverso il risultato di indirizzi IP in una connessione alla macchina virtuale di origine associati e non a quello nuovo. Attualmente, è necessario utilizzare solo i nuovi indirizzi IP pubblici per la creazione della nuova macchina virtuale.
- Gli operatori di Azure Stack potrebbero non essere possibile distribuire, eliminare, modificare le reti virtuali o i gruppi di sicurezza di rete. Questo problema si verifica principalmente sui tentativi di aggiornamento successive dello stesso pacchetto. Ciò è causato da un problema di creazione di pacchetti con un aggiornamento che è attualmente in corso il controllo.
- Bilanciamento di carico interno (ILB) gestisce correttamente gli indirizzi MAC per macchine virtuali di back-end che interrompe le istanze di Linux.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- È possibile richiedere un'ora prima che i tenant possono creare database in un nuovo SQL o MySQL SKU. 
- Creazione di elementi direttamente nel server che non vengono eseguite dal provider di risorse di hosting MySQL e SQL Server non è supportata e può comportare uno stato non corrispondente.

#### <a name="app-service"></a>Servizio app
- Un utente deve registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.
 
#### <a name="usage-and-billing"></a>Utilizzo e fatturazione
- Dati misuratore utilizzo degli indirizzi IP pubblici viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* indicatore che mostra quando è stato creato il record. Attualmente è possibile utilizzare questi dati per eseguire contabilità precisa l'utilizzo degli indirizzi IP pubblico.

#### <a name="identity"></a>Identità

Ambienti, distribuiti in Azure Active Directory Federation Services (ADFS) di **azurestack\azurestackadmin** account non è più proprietario della sottoscrizione del Provider predefinito. Anziché la registrazione nel **portale di amministrazione / endpoint adminmanagement** con il **azurestack\azurestackadmin**, è possibile utilizzare il **azurestack\cloudadmin** account, questa operazione che è possibile gestire e usare la sottoscrizione di Provider predefinito.

> [!IMPORTANT]
> Anche il **azurestack\cloudadmin** account è il proprietario della sottoscrizione del Provider predefinito in ambienti di distribuzione di ADFS, non dispone di autorizzazioni a RDP nell'host. Continuare a utilizzare il **azurestack\azurestackadmin** account o l'account amministratore locale per eseguire l'accesso, accedere e gestire l'host in base alle esigenze.

## <a name="build-201711221"></a>Compilazione 20171122.1

### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni

- Vedere il [nuove funzionalità e correzioni](azure-stack-update-1711.md#new-features-and-fixes) sezione di Azure Stack 1711 aggiornamento le note sulla versione per Azure Stack integrati sistemi.

    > [!IMPORTANT]
    > Alcuni degli elementi elencati nel **nuove funzionalità e correzioni** sezione sono rilevanti solo per i sistemi Azure Stack integrato.

### <a name="known-issues"></a>Problemi noti
 
#### <a name="deployment"></a>Distribuzione
- È necessario specificare un server utilizzando un indirizzo IP durante la distribuzione.

#### <a name="infrastructure-management"></a>Gestione dell'infrastruttura
- Non abilitare il backup di infrastruttura nel **backup infrastruttura** blade.
- L'indirizzo IP di baseboard management controller (BMC) e il modello non vengono visualizzati le informazioni essenziali di un nodo di unità di scala. Questo comportamento è previsto nel Kit di sviluppo dello Stack di Azure.

#### <a name="portal"></a>Portale
- È possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, selezionare l'icona dell'ingranaggio in alto a destra del portale e quindi selezionare **ripristinare le impostazioni predefinite**.
- Quando si visualizzano le proprietà di un gruppo di risorse, il **spostare** pulsante è disabilitato. Questo comportamento è previsto. Spostare i gruppi di risorse tra le sottoscrizioni non è attualmente supportato.
-  Per qualsiasi flusso di lavoro in cui si seleziona una sottoscrizione, gruppo di risorse o alla posizione in un elenco a discesa, si verifichi uno o più dei seguenti problemi:

   - È possibile visualizzare una riga vuota nella parte superiore dell'elenco. È comunque possibile selezionare un elemento come previsto.
   - Se l'elenco di elementi nell'elenco a discesa è breve, non sarà possibile visualizzare i nomi degli elementi.
   - Se si dispone di più sottoscrizioni utente, l'elenco di riepilogo a discesa gruppo di risorse può essere vuoto. 

   Per risolvere i problemi ultimi due, è possibile digitare il nome della sottoscrizione o del gruppo di risorse (se si conosce) oppure è possibile usare PowerShell.

- Verrà visualizzato un **attivazione richiesto** messaggio di avviso che consiglia di registrare il Kit di sviluppo dello Stack di Azure. Questo comportamento è previsto.
- Se il **componente** si fa clic sul collegamento da qualsiasi **ruolo infrastruttura** di avviso, il valore risultante **Panoramica** pannello tenta di caricare e ha esito negativo. Inoltre il **Panoramica** blade non ha timeout.
- Se si elimina utente sottoscrizioni nelle risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.
- Non è in grado di visualizzare le autorizzazioni per la sottoscrizione tramite i portali di Stack di Azure. In alternativa, è possibile verificare le autorizzazioni tramite PowerShell.
 
#### <a name="marketplace"></a>Marketplace
- Quando si tenta di aggiungere elementi a marketplace Stack Azure utilizzando il **Aggiungi da Azure** opzione, non tutti gli elementi potrebbero essere visibili per il download.
- Gli utenti sfogliare il marketplace completo senza una sottoscrizione e di visualizzare gli elementi di amministrazione come piani e alle offerte. Questi elementi sono non funzionale agli utenti.
 
#### <a name="compute"></a>Calcolo
- Gli utenti assegnati l'opzione per creare una macchina virtuale con l'archiviazione con ridondanza geografica. Questa configurazione causa l'errore durante la creazione macchina virtuale. 
- È possibile configurare una macchina virtuale set di disponibilità solo con un dominio di errore di uno e un dominio di aggiornamento di uno.
- Non sussiste alcuna esperienza di marketplace per creare il set di scalabilità di macchine virtuali. È possibile creare un set, utilizzando un modello di scalabilità.
- Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile utilizzare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario utilizzare il `-Name` parametro anziché `-VMScaleSetName`.

#### <a name="networking"></a>Rete
- Tramite il portale, è possibile creare un servizio di bilanciamento del carico con un indirizzo IP pubblico. In alternativa, è possibile utilizzare PowerShell per creare il servizio di bilanciamento del carico.
- Quando si crea un servizio di bilanciamento del carico di rete, è necessario creare una regola di translation (NAT) indirizzo di rete. In caso contrario, si riceverà un errore quando si tenta di aggiungere una regola NAT, dopo aver creato il bilanciamento del carico.
- In **rete**, se si fa clic su **connessione** per impostare una connessione VPN, **per rete virtuale a** è elencato come un tipo di connessione. Non selezionare questa opzione. Attualmente, solo il **Site-to-site (IPsec)** opzione è supportata.
- Dopo aver creata e associata a tale indirizzo IP della macchina virtuale è non è possibile eliminare l'associazione di un indirizzo IP pubblico da una macchina virtuale (VM). Disassociazione verrà visualizzata a funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato con la macchina virtuale originale. Questo comportamento si verifica anche se si riassegna l'indirizzo IP per una nuova macchina virtuale (conosciuta come un *scambio VIP*). Tutti i successivi tentativi di connessione attraverso il risultato di indirizzi IP in una connessione alla macchina virtuale di origine associati e non a quello nuovo. Attualmente, è necessario utilizzare solo i nuovi indirizzi IP pubblici per la creazione della nuova macchina virtuale.
- Gli operatori di Azure Stack potrebbero non essere possibile distribuire, eliminare, modificare le reti virtuali o i gruppi di sicurezza di rete. Questo problema si verifica principalmente sui tentativi di aggiornamento successive dello stesso pacchetto. Ciò è causato da un problema di creazione di pacchetti con un aggiornamento che è attualmente in corso il controllo.
- Bilanciamento di carico interno (ILB) gestisce correttamente gli indirizzi MAC per macchine virtuali di back-end che interrompe le istanze di Linux.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- È possibile richiedere un'ora prima che i tenant possono creare database in un nuovo SQL o MySQL SKU. 
- Creazione di elementi direttamente nel server che non vengono eseguite dal provider di risorse di hosting MySQL e SQL Server non è supportata e può comportare uno stato non corrispondente.

    > [!NOTE]
    > Vedere le singole [SQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy) e [MySQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy) articoli per materiale sussidiario per la compatibilità di versione del programma di installazione.

#### <a name="app-service"></a>Servizio app
- Un utente deve registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.
 
#### <a name="usage-and-billing"></a>Utilizzo e fatturazione
- Dati misuratore utilizzo degli indirizzi IP pubblici viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* indicatore che mostra quando è stato creato il record. Attualmente è possibile utilizzare questi dati per eseguire contabilità precisa l'utilizzo degli indirizzi IP pubblico.

#### <a name="identity"></a>Identità

Ambienti, distribuiti in Azure Active Directory Federation Services (ADFS) di **azurestack\azurestackadmin** account non è più proprietario della sottoscrizione del Provider predefinito. Anziché la registrazione nel **portale di amministrazione / endpoint adminmanagement** con il **azurestack\azurestackadmin**, è possibile utilizzare il **azurestack\cloudadmin** account, questa operazione che è possibile gestire e usare la sottoscrizione di Provider predefinito.

> [!IMPORTANT]
> Anche il **azurestack\cloudadmin** account è il proprietario della sottoscrizione del Provider predefinito in ambienti di distribuzione di ADFS, non dispone di autorizzazioni a RDP nell'host. Continuare a utilizzare il **azurestack\azurestackadmin** account o l'account amministratore locale per eseguire l'accesso, accedere e gestire l'host in base alle esigenze.


## <a name="build-201710201"></a>Compilazione 20171020.1

### <a name="improvements-and-fixes"></a>Miglioramenti e correzioni

Per visualizzare l'elenco dei miglioramenti e correzioni nella compilazione 20171020.1, vedere il [miglioramenti e correzioni](azure-stack-update-1710.md#improvements-and-fixes) sezione 1710 note per Azure Stack integrati sistemi. Alcuni degli elementi elencati nella sezione "ulteriori miglioramenti e correzioni" sono rilevanti solo per sistemi integrati.

Inoltre, sono state apportate le seguenti correzioni:
- Risolto un problema in cui il provider di risorse di calcolo visualizzato uno stato sconosciuto.
- Risolto un problema in cui le quote potrebbero non essere visualizzato nel portale di amministrazione dopo crearli e riprovare in un secondo momento visualizzare i dettagli del piano.

### <a name="known-issues"></a>Problemi noti

#### <a name="powershell"></a>PowerShell
- La versione del modulo PowerShell di Azure Resource Manager 1.2.11 viene fornito con un elenco di modifiche di rilievo. Per informazioni sull'aggiornamento dal 1.2.10 versione, vedere il [Guida alla migrazione](https://aka.ms/azspowershellmigration).
 
#### <a name="deployment"></a>Distribuzione
- È necessario specificare un server utilizzando un indirizzo IP durante la distribuzione.

#### <a name="infrastructure-management"></a>Gestione dell'infrastruttura
- Non abilitare il backup di infrastruttura nel **backup infrastruttura** blade.
- L'indirizzo IP di baseboard management controller (BMC) e il modello non vengono visualizzati le informazioni essenziali di un nodo di unità di scala. Questo comportamento è previsto nel Kit di sviluppo dello Stack di Azure.

#### <a name="portal"></a>Portale
- È possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, selezionare l'icona dell'ingranaggio in alto a destra del portale e quindi selezionare **ripristinare le impostazioni predefinite**.
- Quando si visualizzano le proprietà di un gruppo di risorse, il **spostare** pulsante è disabilitato. Questo comportamento è previsto. Spostare i gruppi di risorse tra le sottoscrizioni non è attualmente supportato.
-  Per qualsiasi flusso di lavoro in cui si seleziona una sottoscrizione, gruppo di risorse o alla posizione in un elenco a discesa, si verifichi uno o più dei seguenti problemi:

   - È possibile visualizzare una riga vuota nella parte superiore dell'elenco. È comunque possibile selezionare un elemento come previsto.
   - Se l'elenco di elementi nell'elenco a discesa è breve, non sarà possibile visualizzare i nomi degli elementi.
   - Se si dispone di più sottoscrizioni utente, l'elenco di riepilogo a discesa gruppo di risorse può essere vuoto. 

   Per risolvere i problemi ultimi due, è possibile digitare il nome della sottoscrizione o del gruppo di risorse (se si conosce) oppure è possibile usare PowerShell.

- Verrà visualizzato un **attivazione richiesto** messaggio di avviso che consiglia di registrare il Kit di sviluppo dello Stack di Azure. Questo comportamento è previsto.
- Nel **attivazione richiesto** avviso dettagli dell'avviso, non fare clic sul collegamento per il **AzureBridge** componente. In caso contrario, il **Panoramica** pannello correttamente tenterà di caricare, e non è previsto un timeout.
- Nel portale di amministrazione, può vedere un **errore durante il recupero di tenant** errore durante il **notifiche** area. È possibile ignorare questo errore.
- Se si elimina utente sottoscrizioni nelle risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.
- Non è in grado di visualizzare le autorizzazioni per la sottoscrizione tramite i portali di Stack di Azure. In alternativa, è possibile verificare le autorizzazioni tramite PowerShell.
 
#### <a name="marketplace"></a>Marketplace
- Quando si tenta di aggiungere elementi a marketplace Stack Azure utilizzando il **Aggiungi da Azure** opzione, non tutti gli elementi potrebbero essere visibili per il download.
- Gli utenti sfogliare il marketplace completo senza una sottoscrizione e di visualizzare gli elementi di amministrazione come piani e alle offerte. Questi elementi sono non funzionale agli utenti.
 
#### <a name="compute"></a>Calcolo
- Gli utenti assegnati l'opzione per creare una macchina virtuale con l'archiviazione con ridondanza geografica. Questa configurazione causa l'errore durante la creazione macchina virtuale. 
- È possibile configurare una macchina virtuale set di disponibilità solo con un dominio di errore di uno e un dominio di aggiornamento di uno.
- Non sussiste alcuna esperienza di marketplace per creare il set di scalabilità di macchine virtuali. È possibile creare un set, utilizzando un modello di scalabilità.
- Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile utilizzare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario utilizzare il `-Name` parametro anziché `-VMScaleSetName`.

#### <a name="networking"></a>Rete
- Tramite il portale, è possibile creare un servizio di bilanciamento del carico con un indirizzo IP pubblico. In alternativa, è possibile utilizzare PowerShell per creare il servizio di bilanciamento del carico.
- Quando si crea un servizio di bilanciamento del carico di rete, è necessario creare una regola di translation (NAT) indirizzo di rete. In caso contrario, si riceverà un errore quando si tenta di aggiungere una regola NAT, dopo aver creato il bilanciamento del carico.
- In **rete**, se si fa clic su **connessione** per impostare una connessione VPN, **per rete virtuale a** è elencato come un tipo di connessione. Non selezionare questa opzione. Attualmente, solo il **Site-to-site (IPsec)** opzione è supportata.
- Dopo aver creata e associata a tale indirizzo IP della macchina virtuale è non è possibile eliminare l'associazione di un indirizzo IP pubblico da una macchina virtuale (VM). Disassociazione verrà visualizzata a funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato con la macchina virtuale originale. Questo comportamento si verifica anche se si riassegna l'indirizzo IP per una nuova macchina virtuale (conosciuta come un *scambio VIP*). Tutti i successivi tentativi di connessione attraverso il risultato di indirizzi IP in una connessione alla macchina virtuale di origine associati e non a quello nuovo. Attualmente, è necessario utilizzare solo i nuovi indirizzi IP pubblici per la creazione della nuova macchina virtuale.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- È possibile richiedere un'ora prima che i tenant possono creare database in un nuovo SQL o MySQL SKU. 
- Creazione di elementi direttamente nel server che non vengono eseguite dal provider di risorse di hosting MySQL e SQL Server non è supportata e può comportare uno stato non corrispondente.

#### <a name="app-service"></a>Servizio app
- Un utente deve registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.
 
#### <a name="usage-and-billing"></a>Utilizzo e fatturazione
- Dati misuratore utilizzo degli indirizzi IP pubblici viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* indicatore che mostra quando è stato creato il record. Attualmente è possibile utilizzare questi dati per eseguire contabilità precisa l'utilizzo degli indirizzi IP pubblico.