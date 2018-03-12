---
title: Note sulla versione di Microsoft Azure Stack Development Kit | Documenti Microsoft
description: Miglioramenti, correzioni e problemi noti per Azure Stack Development Kit.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: brenduns
ms.reviewer: chjoy
ms.openlocfilehash: deef5d5383fcfd8e13c8088cb7901b07621f53a7
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="azure-stack-development-kit-release-notes"></a>Note sulla versione di Azure Stack Development Kit

*Si applica a: Azure Stack Development Kit*

Queste note sulla versione offrono informazioni sui miglioramenti e correzioni di problemi noti in Azure Stack Development Kit. Se non si è certi quale versione è in esecuzione, è possibile [utilizzare il portale per controllare](azure-stack-updates.md#determine-the-current-version).

## <a name="build-201803021"></a>Compilazione 20180302.1

### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni
Vedere il [nuove funzionalità e correzioni](azure-stack-update-1802.md#new-features-and-fixes) sezione di Azure Stack 1802 aggiornamento le note sulla versione per Azure Stack integrati sistemi.

> [!IMPORTANT]    
> Alcuni degli elementi elencati nel **nuove funzionalità e correzioni** sezione sono rilevanti solo per i sistemi Azure Stack integrato.


### <a name="known-issues"></a>Problemi noti
 
#### <a name="portal"></a>Portale
- Il possibilità [per aprire una nuova richiesta di supporto nell'elenco a discesa](azure-stack-manage-portals.md#quick-access-to-help-and-support) all'interno di amministratore del portale non è disponibile. In alternativa, usare il collegamento seguente:     
    - Kit di sviluppo dello Stack di Azure, per utilizzare https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Quando si visualizzano le proprietà di una risorsa o un gruppo di risorse, il **spostare** pulsante è disabilitato. Questo comportamento è previsto. Lo spostamento di risorse o gruppi di risorse tra gruppi di risorse o le sottoscrizioni non è attualmente supportato.
 
- Viene visualizzato un **attivazione richiesto** messaggio di avviso che consiglia di registrare il Kit di sviluppo dello Stack di Azure. Questo comportamento è previsto.

- Se si elimina utente sottoscrizioni nelle risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- È possibile visualizzare le autorizzazioni per la sottoscrizione utilizzando i portali di Stack di Azure. In alternativa, utilizzare PowerShell per verificare le autorizzazioni.

- Nel dashboard del portale di amministrazione, il riquadro di aggiornamento non riesce a visualizzare informazioni sugli aggiornamenti. Per risolvere questo problema, fare clic sul riquadro per aggiornarla.

-   Nel portale di amministrazione, è possibile visualizzare un avviso critico per il componente Microsoft.Update.Admin. Il nome dell'avviso, la descrizione e correzione tutti visualizzati come:  
    - *Errore: modello per FaultType ResourceProviderTimeout è manca.*

    Questo avviso può essere tranquillamente ignorato. 

#### <a name="health-and-monitoring"></a>Monitoraggio dell'integrità e
Nel portale di amministrazione di Stack di Azure, è possibile notare un avviso critico con il nome **in sospeso scadenza certificato esterno**.  Questo avviso può essere tranquillamente ignorato e sulle operazioni del Kit di sviluppo dello Stack di Azure. 


#### <a name="marketplace"></a>Marketplace
- Gli utenti sfogliare il marketplace completo senza una sottoscrizione e di visualizzare gli elementi di amministrazione come piani e alle offerte. Questi elementi sono non funzionale agli utenti.
 
#### <a name="compute"></a>Calcolo
- Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile utilizzare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario utilizzare il `-Name` parametro anziché `-VMScaleSetName`.

- Stack di Azure supporta l'utilizzo solo dischi rigidi virtuali di tipo fisso. Alcune immagini offerti tramite il marketplace nello Stack di Azure usano dischi rigidi virtuali dinamici, ma quelli sono state rimosse. Ridimensionamento di una macchina virtuale (VM) con un disco dinamico collegato lascia la macchina virtuale in uno stato di errore.

  Per evitare questo problema, eliminare la macchina virtuale senza eliminare disco della macchina virtuale, un blob del disco rigido virtuale in un account di archiviazione. Quindi convertire il disco rigido virtuale da un disco dinamico in un disco fisso e quindi ricreare la macchina virtuale.

- Quando si creano macchine virtuali nel portale per gli utenti dello Stack di Azure, il portale consente di visualizzare un numero errato di dischi dati che è possibile collegare a una VM di serie DS. Macchine virtuali di serie DS possono contenere tutti i dischi di dati come la configurazione di Azure.

- Quando non è stato possibile creare un'immagine di macchina virtuale, un elemento non riuscito che è possibile eliminare potrebbe essere aggiunti al pannello di calcolo di immagini della macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo deve risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale che precedentemente non riuscito.

-  Se il provisioning di un'estensione in una distribuzione di macchina virtuale è troppo lunga, gli utenti devono consentire il timeout di provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Rete
- In **rete**, se si fa clic su **connessione** per impostare una connessione VPN, **per rete virtuale a** è elencato come un tipo di connessione. Non selezionare questa opzione. Attualmente, solo il **Site-to-site (IPsec)** opzione è supportata.

- Dopo aver creata e associata a un indirizzo IP pubblico di una macchina virtuale, è non è possibile annullare l'associazione di tale macchina virtuale da tale indirizzo IP. Disassociazione sembra funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato con la macchina virtuale originale.

  Attualmente, è necessario utilizzare solo indirizzi IP pubblici nuovo per nuove macchine virtuali create.

  Questo comportamento si verifica anche se si riassegna l'indirizzo IP per una nuova macchina virtuale (conosciuta come un *scambio VIP*). Tutti i successivi tentativi di connessione attraverso il risultato di indirizzi IP in una connessione alla macchina virtuale di origine associati e non a quello nuovo.

- Bilanciamento di carico interno (ILB) gestisce correttamente gli indirizzi MAC per macchine virtuali di back-end, che comporta l'interruzione quando si usano istanze di Linux nella rete Back-End bilanciamento del carico interno.  Bilanciamento del carico interno funziona correttamente con le istanze di Windows in rete Back-End.

-   La funzionalità di inoltro IP è visibile nel portale, tuttavia abilitare l'inoltro IP non ha alcun effetto. Questa funzionalità non è ancora supportata.

- Stack di Azure supporta un singolo *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni di tenant. Dopo la creazione della prima rete locale gateway connessione, le successive tenta di creare una risorsa di gateway di rete locale con lo stesso indirizzo IP sono bloccati.

- In una rete virtuale che è stato creato con un'impostazione di Server DNS di *automatico*, la modifica di guasto a un Server DNS personalizzato. Le impostazioni aggiornate non vengono inviate a macchine virtuali in tale rete virtuale.
 
- Stack di Azure non supporta l'aggiunta di interfacce di rete aggiuntiva a un'istanza di macchina virtuale dopo aver distribuita la macchina virtuale. Se la macchina virtuale richiede più di un'interfaccia di rete, che devono essere definite in fase di distribuzione.

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Soluzione alternativa per il servizio App: se è necessario per il desktop remoto alle istanze del Controller, si modificano le regole di sicurezza all'interno dei gruppi di sicurezza di rete con PowerShell.  Di seguito sono riportati esempi di come *consentire*e quindi ripristinare la configurazione per *negare*: 
    
    - *Consenti:*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Negazione:*

        ```powershell
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```


#### <a name="sql-and-mysql"></a>SQL e MySQL 
- Può richiedere fino a un'ora prima che gli utenti possono creare database in un nuovo SQL o MySQL SKU.

- Il database server di hosting deve essere dedicato per l'utilizzo di risorse utente e i provider dei carichi di lavoro. È possibile utilizzare un'istanza che è utilizzata da qualsiasi altro consumer, inclusi i servizi di App.


#### <a name="app-service"></a>Servizio app
- Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- Per applicare la scalabilità orizzontale l'infrastruttura (Gestione processi di lavoro, i ruoli front-end), è necessario usare PowerShell, come descritto nelle note sulla versione per il calcolo.
 
#### <a name="usage-and-billing"></a>Utilizzo e fatturazione
- Dati misuratore utilizzo degli indirizzi IP pubblici viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* indicatore che mostra quando è stato creato il record. Attualmente è possibile utilizzare questi dati per eseguire contabilità precisa l'utilizzo degli indirizzi IP pubblico.

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Download degli strumenti di Azure Stack da GitHub
- Quando si utilizza il *invoke-webrequest* da Github degli strumenti cmdlet di PowerShell per scaricare lo Stack di Azure, viene visualizzato un errore:     
    -  *Invoke-webrequest: la richiesta è stata interrotta: Impossibile creare un canale protetto SSL/TLS.*     

  Questo errore si verifica a causa di un recente deprecazione di supporto di GitHub degli Tlsv1 e Tlsv1.1 crittografia standard (impostazione predefinita per PowerShell). Per ulteriori informazioni, vedere [si noti la rimozione di standard di crittografia debole](https://githubengineering.com/crypto-removal-notice/).

  Per risolvere questo problema, aggiungere `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` all'inizio dello script per forzare la console di PowerShell per utilizzare TLSv1.2 durante il download dal repository di GitHub.







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

