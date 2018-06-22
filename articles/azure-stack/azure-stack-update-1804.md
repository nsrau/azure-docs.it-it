---
title: Aggiornamento di Azure Stack 1804 | Documenti Microsoft
description: Informazioni sulle novità nell'aggiornamento 1804 per lo Stack di Azure integrati sistemi, i problemi noti e come scaricare l'aggiornamento.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 2c2813a7f2d909a23c8f5d4f5ac0280b3f932ba6
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "34700125"
---
# <a name="azure-stack-1804-update"></a>Aggiornamento dello Stack 1804 Azure

*Si applica a: Azure Stack integrate di sistemi*

In questo articolo vengono descritti i miglioramenti e correzioni nel pacchetto di aggiornamento 1804, conosciuti per questa versione e come scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi direttamente correlati al processo di aggiornamento e con la compilazione (post-installazione).

> [!IMPORTANT]        
> Questo pacchetto di aggiornamento è solo per i sistemi Azure Stack integrato. Questo pacchetto di aggiornamento non si applicano al Kit di sviluppo dello Stack di Azure.

## <a name="build-reference"></a>Compilazione di riferimento    
È il numero di build aggiornamento Azure Stack 1804 **20180513.1**.   

### <a name="new-features"></a>Nuove funzionalità
Questo aggiornamento include i miglioramenti seguenti per lo Stack di Azure.

- <!-- 15028744 - IS -->  **Supporto di Visual Studio per le distribuzioni di Azure Stack disconnesse usando ADFS**. All'interno di Visual Studio è ora possibile aggiungere sottoscrizioni e l'autenticazione mediante ADFS federati le credenziali dell'utente. 
 
- <!-- 1779474, 1779458 - IS --> **Utilizzare le macchine virtuali serie Av2 e F**. Stack di Azure è ora possibile usare le macchine virtuali in base alle dimensioni delle macchine virtuali serie Av2 e F. Per altre informazioni, vedere [le dimensioni di macchina virtuale supportate in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **Le nuove sottoscrizioni amministrative**. Con 1804 sono disponibili due nuovi tipi di sottoscrizione nel portale. Questi nuovi tipi di sottoscrizione sono oltre la sottoscrizione del Provider predefinito ed è visibile con le nuove installazioni Azure Stack partire dalla versione 1804. *Non utilizzare questi nuovi tipi di sottoscrizione con questa versione di Azure Stack*. Si annuncerà la disponibilità per l'utilizzo di questi tipi di sottoscrizione con un aggiornamento futuro. 

  Se si aggiorna Stack Azure alla versione 1804, i due nuovi tipi di sottoscrizione non sono visibili. Tuttavia, nuove distribuzioni di Azure Stack integrati sistemi e le installazioni della versione del Kit di sviluppo di Azure Stack 1804 o versioni successiva possono accedere a tutti i tipi di sottoscrizione tre.  

  Questi nuovi tipi di sottoscrizione fanno parte di una modifica di dimensioni maggiore per proteggere la sottoscrizione del Provider predefinito e per semplificare la distribuzione di risorse condivise come server di Hosting SQL. Man mano che si aggiungono altre parti di questa modifica di dimensioni maggiore con gli aggiornamenti futuri allo Stack di Azure, le risorse distribuite in questi nuovi tipi di sottoscrizione potrebbero andare perse. 

  I tipi di tre sottoscrizione ora visibili sono:  
  - Sottoscrizione di Provider predefinito: continuare a utilizzare questo tipo di sottoscrizione. 
  - Sottoscrizione di controllo: *non utilizzano questo tipo di sottoscrizione.*
  - Sottoscrizione di utilizzo: *non utilizzano questo tipo di sottoscrizione*

  



## <a name="fixed-issues"></a>Problemi risolti

- <!-- IS, ASDK -->  Nel portale di amministrazione, non è più necessario aggiornare il riquadro di aggiornamento prima di visualizzare informazioni.
 
- <!-- 2050709  -->  È ora possibile usare il portale di amministrazione per modificare le metriche di archiviazione per il servizio Blob del servizio tabelle e servizio di Accodamento.
 
- <!-- IS, ASDK --> Sotto **Networking**, quando si fa clic su **connessione** per impostare una connessione VPN **Site-to-site (IPsec)** è l'unica opzione disponibile.

- **Varie correzioni** per le prestazioni, stabilità, sicurezza e il sistema operativo che viene utilizzato dallo Stack di Azure.

## <a name="additional-releases-timed-with-this-update"></a>Versioni aggiuntive programmate con questo aggiornamento  
Di seguito è ora disponibili, ma non richiede l'aggiornamento dello Stack Azure 1804.
- **Aggiornamento in Microsoft Azure Stack System Center Operations Manager Monitoring Pack**. È disponibile per una nuova versione (1.0.3.0) di Microsoft System Center Operations Manager Monitoring Pack per Azure Stack [scaricare](https://www.microsoft.com/download/details.aspx?id=55184). Con questa versione, è possibile utilizzare le entità servizio quando si aggiunge una distribuzione di Azure Stack connessa. Questa versione include anche un'esperienza di gestione degli aggiornamenti che consente di eseguire l'azione di correzione direttamente all'interno di Operations Manager. Sono inoltre disponibili nuovi dashboard che visualizzano i provider di risorse, unità di scala e ridimensionare i nodi di unità.

- **Nuova versione di Azure Stack amministrazione PowerShell versione 1.3.0**.  Azure PowerShell Stack 1.3.0 è ora disponibile per l'installazione. Questa versione sono disponibili comandi per tutti i provider di risorse di amministrazione gestire Azure Stack.  Con questa versione, alcuni contenuti verranno deprecati a partire da Azure Stack strumenti GitHub [repository](https://github.com/Azure/AzureStack-Tools). 

   Per informazioni dettagliate di installazione, seguire le [istruzioni](azure-stack-powershell-install.md) o il [Guida](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) contenuto per il modulo di Azure Stack 1.3.0. 

- **Versione del riferimento all'API Rest di Azure Stack iniziale**. Il [riferimento API per tutti i provider di risorse di Azure Stack Admin](https://docs.microsoft.com/rest/api/azure-stack/) ora viene pubblicato. 


## <a name="before-you-begin"></a>Prima di iniziare    

### <a name="prerequisites"></a>Prerequisiti
- Installare lo Stack di Azure [1803 aggiornare](azure-stack-update-1803.md) prima di applicare l'aggiornamento di Azure Stack 1804.    

### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento   
- Durante l'installazione dell'aggiornamento 1804, si potrebbero visualizzare avvisi con il titolo *Error: modello per il tipo FaultType UserAccounts.New mancante.*  È possibile ignorare questi avvisi. Questi avvisi verranno chiuso automaticamente dopo l'aggiornamento a 1804 viene completato.   
 
- <!-- TBD - IS --> Non tentare di creare macchine virtuali durante l'installazione dell'aggiornamento. Per ulteriori informazioni sulla gestione degli aggiornamenti, vedere [gestire gli aggiornamenti in panoramica di Azure Stack](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Passaggi di post-aggiornamento
*Non sono passaggi post-aggiornamento per aggiornamento 1804.*



### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)
Di seguito sono i problemi noti di post-installazione per la compilazione **20180513.1**.

#### <a name="portal"></a>Portale
- <!-- 1272111 - IS --> Dopo l'installazione o aggiornamento alla versione corrente dello Stack di Azure, potrebbe non essere in grado di visualizzare unità di scala dello Stack di Azure nel portale di amministrazione.  
  Soluzione alternativa: Utilizzare PowerShell per visualizzare le informazioni sulle unità di scala. Per altre informazioni, vedere la [Guida](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) contenuto per il modulo di Azure Stack 1.3.0. 

- <!-- 2332636 - IS -->  Quando si usa ADFS per il sistema di identità di Azure Stack e l'aggiornamento alla versione corrente dello Stack di Azure, il proprietario predefinito della sottoscrizione del provider predefinito viene reimpostato su incorporati **CloudAdmin** utente.  
  Soluzione alternativa: Per risolvere questo problema dopo aver installato questo aggiornamento, passaggio 3 da utilizzare il [automazione Trigger per configurare le attestazioni trust del provider in Stack Azure](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedure per reimpostare il proprietario della sottoscrizione del provider predefinito.   

- <!-- TBD - IS ASDK --> Alcuni tipi di sottoscrizione amministrativi non disponibili.  Quando si aggiorna Azure Stack per questa versione, i tipi di due sottoscrizione che in precedenza erano [introdotta con la versione 1804](#new-features) non sono visibili nella console. Si tratta di un comportamento previsto. I tipi di sottoscrizione disponibili sono *misurazione sottoscrizione*, e *sottoscrizione consumo*. Questi tipi di sottoscrizione sono visibili in nuovi ambienti Azure Stack partire dalla versione 1804 ma non sono ancora pronti per l'utilizzo. È consigliabile continuare a usare il *Provider predefinito* tipo di sottoscrizione.  


- <!-- TBD -  IS ASDK -->La capacità [per aprire una nuova richiesta di supporto nell'elenco a discesa](azure-stack-manage-portals.md#quick-access-to-help-and-support) da entro l'amministratore del portale non è attualmente disponibile. In alternativa, usare il collegamento seguente:     
    - Per lo Stack di Azure usare sistemi integrati, https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> Non si dispone di utilizzo della barra di scorrimento orizzontale nella parte inferiore dei portali di amministratore e utente. Se non hai accesso la barra di scorrimento orizzontale, utilizzare il percorso di navigazione per passare a un pannello precedente nel portale di selezionando il nome del pannello si desidera visualizzare nell'elenco di navigazione nella parte superiore sinistra del portale.
  ![Barra di navigazione](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> Che non sia possibile visualizzare le risorse di calcolo o di archiviazione nel portale di amministrazione. La causa del problema è un errore durante l'installazione dell'aggiornamento che causa l'aggiornamento da segnalare in modo non corretto come completata correttamente. Se si verifica questo problema, contattare il supporto tecnico clienti Microsoft per assistenza.

- <!-- TBD - IS --> È possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, selezionare l'icona dell'ingranaggio in alto a destra del portale e quindi selezionare **ripristinare le impostazioni predefinite**.

- <!-- TBD - IS ASDK --> Se si elimina utente sottoscrizioni nelle risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- <!-- TBD - IS ASDK --> È possibile visualizzare le autorizzazioni per la sottoscrizione utilizzando i portali di Stack di Azure. In alternativa, utilizzare PowerShell per verificare le autorizzazioni.

- <!-- TBD - IS ASDK --> Nel portale di amministrazione, è possibile notare un avviso critico per la *Microsoft.Update.Admin* componente. Il nome dell'avviso, la descrizione e correzione tutti visualizzati come:  
    - *Errore: modello per FaultType ResourceProviderTimeout è manca.*

  Questo avviso può essere tranquillamente ignorato. 


#### <a name="health-and-monitoring"></a>Monitoraggio dell'integrità e
- <!-- 1264761 - IS ASDK -->  Si potrebbero visualizzare avvisi per il *controller integrità* componente che ha i seguenti dettagli:  

   Avviso #1:
   - NOME: Ruolo di infrastruttura non integro
   - GRAVITÀ: avviso
   - COMPONENTI: Controllo di integrità
   - Descrizione: Il controller di integrità Heartbeat Scanner è disponibile. Ciò potrebbe influire su rapporti di stato e le metriche.  

  Avviso #2:
   - NOME: Ruolo di infrastruttura non integro
   - GRAVITÀ: avviso
   - COMPONENTI: Controllo di integrità
   - Descrizione: Il controller di integrità scanner di codici di errore non è disponibile. Ciò potrebbe influire su rapporti di stato e le metriche.

  Entrambi gli avvisi possono essere tranquillamente ignorati. Verrà chiusa automaticamente nel corso del tempo.  
 

#### <a name="compute"></a>Calcolo
- <!-- TBD - IS --> Quando si seleziona una dimensione di macchina virtuale per una distribuzione della macchina virtuale, alcune dimensioni di macchina virtuale della serie F non sono visibili nell'ambito del selettore di dimensioni quando si crea una macchina virtuale. Le dimensioni delle macchine Virtuali seguenti non vengono visualizzati nel selettore: *F8s_v2*, *F16s_v2*, *F32s_v2*, e *F64s_v2*.  
  In alternativa, usare uno dei metodi seguenti per distribuire una macchina virtuale. Ogni metodo, è necessario specificare la dimensione di macchina virtuale che si desidera utilizzare.

  - **Modello di gestione risorse di Azure:** quando si utilizza un modello, impostare il *vmSize* nel modello per la dimensione di macchina virtuale desiderata è uguale. Ad esempio, nell'esempio viene utilizzato per distribuire una macchina virtuale che utilizza il *F32s_v2* dimensioni:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **CLI di Azure:** è possibile usare il [creare vm az](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) comando e specificare le dimensioni della VM come parametro, simile a `--size "Standard_F32s_v2"`.

  - **PowerShell:** con PowerShell è possibile utilizzare [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) con il parametro che specifica le dimensioni della VM, simile a `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile utilizzare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario utilizzare il `-Name` parametro anziché `-VMScaleSetName`.

- <!-- TBD - IS --> Quando si crea un set di disponibilità in del portale, passare a **New** > **calcolo** > **set di disponibilità**, è possibile creare solo un set di disponibilità con un dominio di errore e dominio di aggiornamento pari a 1. In alternativa, quando si crea una nuova macchina virtuale, creare il set tramite l'interfaccia CLI, PowerShell o dall'interno di disponibilità il portale.

- <!-- TBD - IS ASDK --> Quando si creano macchine virtuali nel portale per gli utenti dello Stack di Azure, il portale consente di visualizzare un numero errato di dischi dati che è possibile collegare a una VM di serie D. Tutte le serie D supportate macchine virtuali possono contenere tutti i dischi di dati come la configurazione di Azure.

- <!-- TBD - IS ASDK --> Quando non è stato possibile creare un'immagine di macchina virtuale, un elemento non riuscito che è possibile eliminare potrebbe essere aggiunti al pannello di calcolo di immagini della macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo deve risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale che precedentemente non riuscito.

- <!-- TBD - IS ASDK --> Se il provisioning di un'estensione in una distribuzione di macchina virtuale è troppo lunga, gli utenti devono consentire il timeout di provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 IS ASDK --> Diagnostica Linux VM non è supportato nello Stack di Azure. Quando si distribuisce una VM Linux con diagnostica di macchina virtuale abilitata, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilita la metrica di base VM Linux tramite le impostazioni di diagnostica.  


#### <a name="networking"></a>Rete
- <!-- 1766332 - IS ASDK --> Sotto **Networking**, se si fa clic su **crea Gateway VPN** per configurare una connessione VPN, **basata su criteri** è elencato come un tipo di VPN. Non selezionare questa opzione. Solo il **basato su Route** opzione è supportata nello Stack di Azure.

- <!-- 2388980 - IS ASDK --> Dopo aver creata e associata a un indirizzo IP pubblico di una macchina virtuale, è non è possibile annullare l'associazione di tale macchina virtuale da tale indirizzo IP. Disassociazione sembra funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato con la macchina virtuale originale.

  Attualmente, è necessario utilizzare solo indirizzi IP pubblici nuovo per nuove macchine virtuali create.

  Questo comportamento si verifica anche se si riassegna l'indirizzo IP per una nuova macchina virtuale (conosciuta come un *scambio VIP*). Tutti i successivi tentativi di connessione attraverso il risultato di indirizzi IP in una connessione alla macchina virtuale di origine associati e non a quello nuovo.

- <!-- 2292271 - IS ASDK --> Se si genera un limite di Quota per una risorsa di rete che fa parte di un piano che è associato a una sottoscrizione tenant e l'offerta, il nuovo limite non viene applicato a tale sottoscrizione. Tuttavia, il nuovo limite viene applicato a nuove sottoscrizioni creati dopo che la quota viene aumentata. 

  Per risolvere questo problema, utilizzare un piano del componente aggiuntivo per aumentare la Quota di rete quando il piano è già associato a una sottoscrizione. Per altre informazioni, vedere come [rendere disponibile un piano del componente aggiuntivo](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> È possibile eliminare una sottoscrizione con risorse di zona DNS o tabella di Route è associato. Per eliminare correttamente la sottoscrizione, è innanzitutto necessario eliminare le risorse di zona DNS e tabella di Route dalla sottoscrizione tenant. 
  

- <!-- 1902460 - IS ASDK --> Stack di Azure supporta una singola *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni di tenant. Dopo la creazione della prima rete locale gateway connessione, le successive tenta di creare una risorsa di gateway di rete locale con lo stesso indirizzo IP sono bloccati.

- <!-- 16309153 - IS ASDK --> In una rete virtuale che è stato creato con un'impostazione del Server DNS *automatico*, la modifica di guasto a un Server DNS personalizzato. Le impostazioni aggiornate non vengono inviate a macchine virtuali in tale rete virtuale.

- <!-- TBD - IS ASDK --> Stack di Azure non supporta l'aggiunta di interfacce di rete aggiuntiva a un'istanza di macchina virtuale dopo aver distribuita la macchina virtuale. Se la macchina virtuale richiede più di un'interfaccia di rete, che devono essere definite in fase di distribuzione.

- <!-- 2096388 IS --> È possibile utilizzare il portale di amministrazione per aggiornare le regole per un gruppo di sicurezza di rete. 

    Soluzione alternativa per il servizio App: se è necessario per il desktop remoto alle istanze del Controller, si modificano le regole di sicurezza all'interno dei gruppi di sicurezza di rete con PowerShell.  Di seguito sono riportati esempi di come *consentire*e quindi ripristinare la configurazione per *negare*:  
    
    - *Consenti:*
 
      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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

- <!-- TBD - IS --> Per creare gli elementi nei server di tale host SQL o MySQL, è supportato solo il provider di risorse. Gli elementi creati in un server host che non vengono creati dal provider di risorse potrebbe essere in uno stato non corrispondente.  

- <!-- IS, ASDK --> Caratteri speciali, inclusi gli spazi e i periodi di, non sono supportati nel **famiglia** oppure **livello** nomi quando si crea un'unità SKU per i provider di risorse MySQL e SQL Server.


> [!NOTE]  
> <!-- TBD - IS --> Dopo l'aggiornamento alla 1804 dello Stack di Azure, è possibile continuare a utilizzare i provider di risorse MySQL e SQL distribuito in precedenza.  È consigliabile che aggiornare MySQL e SQL Server quando diventa disponibile una nuova versione. Come Stack di Azure, applicare gli aggiornamenti in sequenza per i provider di risorse MySQL e SQL Server.  Ad esempio, se si utilizza versione 1802, applicare prima versione 1803 e quindi aggiornare a 1804.      
>   
> L'installazione dell'aggiornamento 1804 non influenza l'uso del provider di risorse SQL o MySQL dagli utenti.
> Indipendentemente dalla versione dei provider di risorse a cui che si utilizza, agli utenti di dati nei database non sono interessati e rimangono accessibili.    



#### <a name="app-service"></a>Servizio app
- <!-- 2352906 - IS ASDK --> Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- <!-- TBD - IS ASDK --> Per applicare la scalabilità orizzontale l'infrastruttura (Gestione processi di lavoro, i ruoli front-end), è necessario usare PowerShell, come descritto nelle note sulla versione per il calcolo.

- <!-- TBD - IS ASDK --> Servizio App può essere distribuito solo nel **sottoscrizione Provider predefinito** in questo momento.  In un futuro aggiornamento servizio App verrà distribuito nella nuova sottoscrizione di misurazione introdotta in Azure Stack 1804 e tutte le distribuzioni esistenti verranno migrate anche a questa nuova sottoscrizione.

#### <a name="usage"></a>Uso  
- <!-- TBD - IS ASDK --> Dati misuratore sull'utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* stamp che indica quando è stato creato il record. Attualmente è possibile utilizzare questi dati per eseguire contabilità precisa l'utilizzo degli indirizzi IP pubblico.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Scaricare l'aggiornamento
È possibile scaricare il pacchetto di aggiornamento di Azure Stack 1804 dal [qui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Vedere anche 
- Per usare il punto finale con privilegi (PEP) per monitorare e riprendere gli aggiornamenti, vedere [monitorare gli aggiornamenti nello Stack di Azure utilizzando l'endpoint con privilegi](azure-stack-monitor-update.md).
- Per una panoramica del processo di gestione nello Stack di Azure, vedere [gestire gli aggiornamenti in panoramica di Azure Stack](azure-stack-updates.md).
- Per ulteriori informazioni su come applicare gli aggiornamenti con lo Stack di Azure, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).
