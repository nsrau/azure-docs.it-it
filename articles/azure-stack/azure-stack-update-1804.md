---
title: Aggiornamento di Azure Stack 1804 | Microsoft Docs
description: Informazioni sulle novità nell'aggiornamento 1804 per Azure Stack integrati sistemi, i problemi noti e dove scaricare l'aggiornamento.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 16f12d8119a14e668a7502d99fa2d9c976d23833
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393520"
---
# <a name="azure-stack-1804-update"></a>Aggiornamento di Azure Stack 1804

*Si applica a: i sistemi integrati di Azure Stack*

Questo articolo vengono descritti i miglioramenti e correzioni nel pacchetto di aggiornamento 1804, problemi noti per questa versione e dove scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi correlati direttamente al processo di aggiornamento e i problemi con la build (post-installazione).

> [!IMPORTANT]        
> Questo pacchetto di aggiornamento è solo per i sistemi integrati di Azure Stack. Questo pacchetto di aggiornamento non sono applicabili per Azure Stack Development Kit.

## <a name="build-reference"></a>Riferimento alla compilazione    
È il numero di build di aggiornamento di Azure Stack 1804 **20180513.1**.   

### <a name="new-features"></a>Nuove funzionalità
Questo aggiornamento include i miglioramenti seguenti per Azure Stack.

- <!-- 15028744 - IS -->  **Supporto di Visual Studio per le distribuzioni di Azure Stack disconnessi, Usa AD FS**. All'interno di Visual Studio è ora possibile aggiungere sottoscrizioni ed eseguire l'autenticazione tramite AD FS federato le credenziali dell'utente. 
 
- <!-- 1779474, 1779458 - IS --> **Usare le macchine virtuali serie Av2 e F**. Azure Stack è ora possibile usare le macchine virtuali basate sulle dimensioni delle macchine virtuali della serie Av2 e F-series. Per altre informazioni, vedere [le dimensioni di macchina virtuale supportate in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **Le nuove sottoscrizioni amministrative**. Con 1804 sono disponibili due nuovi tipi di sottoscrizione nel portale. Questi nuovi tipi di sottoscrizione sono oltre la sottoscrizione del Provider predefinito e visibili le nuove installazioni di Azure Stack partire dalla versione 1804. *Non usare questi nuovi tipi di sottoscrizione con questa versione di Azure Stack*. Verranno annunciati la disponibilità per usare questi tipi di sottoscrizione con un aggiornamento futuro. 

  Se Azure Stack l'aggiornamento alla versione 1804, i due nuovi tipi di sottoscrizione non sono visibili. Tuttavia, i sistemi integrati di nuove distribuzioni di Azure Stack e le installazioni della versione di Azure Stack Development Kit 1804 o versioni successiva hanno accesso a tutti i tipi di sottoscrizione tre.  

  Questi nuovi tipi di sottoscrizione fanno parte di una modifica di dimensioni maggiori per proteggere la sottoscrizione del Provider predefinito e per renderne più semplice distribuire le risorse condivise come i server di Hosting SQL. Quando si aggiungono altre parti di questa modifica di dimensioni maggiori con gli aggiornamenti futuri ad Azure Stack, le risorse distribuite in questi nuovi tipi di sottoscrizione potrebbero andare perse. 

  Sono ora visibili i tipi di tre sottoscrizione:  
  - Sottoscrizione del Provider predefinito: continuare a usare questo tipo di sottoscrizione. 
  - Sottoscrizione di controllo: *non usare questo tipo di sottoscrizione.*
  - Sottoscrizione di consumo: *non usare questo tipo di sottoscrizione*

  



## <a name="fixed-issues"></a>Problemi risolti

- <!-- IS, ASDK -->  Nel portale di amministrazione, è necessario non è più aggiornare il riquadro di aggiornamento prima di visualizzare le informazioni.
 
- <!-- 2050709  -->  È ora possibile usare il portale di amministrazione per modificare le metriche di archiviazione per il servizio Blob, servizio tabelle e servizio di Accodamento.
 
- <!-- IS, ASDK --> Sotto **Networking**quando fa clic su **connessione** per configurare una connessione VPN, **Site-to-site (IPsec)** è a questo punto l'unica opzione disponibile.

- **Varie correzioni di** per le prestazioni, stabilità, sicurezza e il sistema operativo che viene usato da Azure Stack.

## <a name="additional-releases-timed-with-this-update"></a>Altri rilasci programmati con questo aggiornamento  
Di seguito è ora disponibili, ma non richiede l'aggiornamento di Azure Stack 1804.
- **Aggiornamento di Microsoft Azure Stack System Center Operations Manager Monitoring Pack**. È disponibile per una nuova versione (1.0.3.0) di Microsoft System Center Operations Manager Monitoring Pack per Azure Stack [scaricare](https://www.microsoft.com/download/details.aspx?id=55184). Con questa versione, è possibile usare le entità servizio quando si aggiunge una distribuzione di Azure Stack connesso. Questa versione offre anche un'esperienza di gestione degli aggiornamenti che consente di eseguire l'azione di correzione direttamente dall'interno di Operations Manager. Sono inoltre disponibili nuovi dashboard che visualizzano i provider di risorse, unità di scala e ridimensionare i nodi di unità.

- **Nuovo di Azure Stack Admin PowerShell versione 1.3.0**.  Azure Stack PowerShell 1.3.0 è ora disponibile per l'installazione. Questa versione sono disponibili comandi per tutti i provider di risorse di amministratore per la gestione di Azure Stack.  Con questa versione, verranno deprecato alcuni contenuti da GitHub gli strumenti di Azure Stack [repository](https://github.com/Azure/AzureStack-Tools). 

   Per informazioni dettagliate di installazione, seguire le [istruzioni](azure-stack-powershell-install.md) o il [Guida](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) contenuto per Azure Stack modulo 1.3.0. 

- **Versione iniziale del riferimento all'API Rest di Azure Stack**. Il [riferimento API per tutti i provider di risorse di Azure Stack Admin](https://docs.microsoft.com/rest/api/azure-stack/) viene pubblicato. 


## <a name="before-you-begin"></a>Prima di iniziare    

### <a name="prerequisites"></a>Prerequisiti
- Installare Azure Stack [1803 aggiornare](azure-stack-update-1803.md) prima di applicare l'aggiornamento di Azure Stack 1804.  
  
- Installare la versione più recente disponibile [update o un hotfix per la versione 1803](azure-stack-update-1803.md#post-update-steps). 


### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento   
- Durante l'installazione dell'aggiornamento 1804, si potrebbero visualizzare avvisi con il titolo *Error: modello per FaultType UserAccounts.New è manca.*  È possibile ignorare questi avvisi. Questi avvisi chiuderà automaticamente dopo l'aggiornamento a 1804.   
 
- <!-- TBD - IS --> Non tentare di creare macchine virtuali durante l'installazione dell'aggiornamento. Per altre informazioni sulla gestione degli aggiornamenti, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Passaggi di post-aggiornamento
Dopo l'installazione di 1804, installare eventuali hotfix applicabili. Per altre informazioni, visualizzare i seguenti articoli della knowledge base, nonché nostri [criteri per la manutenzione](azure-stack-servicing-policy.md).  
 - [KB 4344114 - aggiornamento rapido di Azure Stack 1.0.180527.15](https://support.microsoft.com/help/4344114).




### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)
Di seguito sono riportati problemi noti di post-installazione per la compilazione **20180513.1**.

#### <a name="portal"></a>Portale

- La documentazione tecnica di Azure Stack è incentrato sulla versione più recente. A causa di un portale modifiche tra le versioni, ciò che viene visualizzato quando si utilizzano i portali di Azure Stack può variare da quelli visualizzati nella documentazione. 

- <!-- TBD - IS ASDK --> È possibile applicare gli aggiornamenti dei driver tramite un pacchetto di estensione OEM con questa versione di Azure Stack.  Non è disponibile alcuna soluzione per questo problema.

- <!-- 1272111 - IS --> Dopo l'installazione o aggiornamento a questa versione di Azure Stack, potrebbe non essere in grado di visualizzare unità di scala di Azure Stack nel portale di amministrazione.  
  Soluzione alternativa: Utilizzare PowerShell per visualizzare le informazioni sulle unità di scala. Per altre informazioni, vedere la [aiutare](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) contenuto per Azure Stack modulo 1.3.0. 

- <!-- 2332636 - IS -->  Quando si usa AD FS per il sistema di identità di Azure Stack e l'aggiornamento a questa versione di Azure Stack, il proprietario predefinito della sottoscrizione del provider predefinito viene reimpostato per l'oggetto incorporato **CloudAdmin** utente.  
  Soluzione alternativa: Per risolvere questo problema dopo aver installato questo aggiornamento, passaggio 3 da usare la [attivare l'automazione per configurare attestazioni attendibilità del provider in Azure Stack](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedura per reimpostare il proprietario della sottoscrizione del provider predefinito.   

- <!-- TBD - IS ASDK --> Alcuni tipi di sottoscrizione amministrativi non disponibili.  Quando si esegue l'aggiornamento di Azure Stack per questa versione, i tipi di due sottoscrizioni che erano [introdotta con la versione 1804](#new-features) non sono visibili nella console. Si tratta di un comportamento previsto. I tipi di sottoscrizione disponibili sono *sottoscrizione di misurazione*, e *sottoscrizione consumo*. Questi tipi di sottoscrizione sono visibili in nuovi ambienti Azure Stack partire dalla versione 1804 ma non sono ancora pronti per l'uso. È consigliabile continuare a usare il *Provider predefinito* tipo di sottoscrizione.  


- <!-- TBD -  IS ASDK -->La possibilità [per aprire una nuova richiesta di supporto nell'elenco a discesa](azure-stack-manage-portals.md#quick-access-to-help-and-support) all'interno di amministratore del portale non è disponibile. In alternativa, usare il collegamento seguente:     
    - Per Azure Stack i sistemi integrati, usare https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> Non si dispone di utilizzo della barra di scorrimento orizzontale nella parte inferiore di portali di amministratore e utente. Se non è possibile accedere la barra di scorrimento orizzontale, usare i percorsi di navigazione per passare al pannello precedente nel portale selezionando il nome del pannello si desidera visualizzare nell'elenco di navigazione nella parte superiore sinistra del portale.
  ![Barra di navigazione](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> Potrebbe non essere possibile visualizzare le risorse di calcolo o archiviazione nel portale di amministrazione. La causa del problema è un errore durante l'installazione dell'aggiornamento che causa l'aggiornamento da segnalare in modo errato come esito positivo. Se si verifica questo problema, contattare il supporto tecnico clienti Microsoft per assistenza.

- <!-- TBD - IS --> È possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, selezionare l'icona a forma di ingranaggio in alto a destra del portale e quindi selezionare **ripristinare le impostazioni predefinite**.

- <!-- TBD - IS ASDK --> Eliminazione utente diverso, le sottoscrizioni le risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- <!-- TBD - IS ASDK --> Non è possibile visualizzare le autorizzazioni per la sottoscrizione tramite il portale di Azure Stack. In alternativa, usare PowerShell per verificare le autorizzazioni.

- <!-- TBD - IS ASDK --> Nel portale di amministrazione, è possibile notare un avviso critico per la *Microsoft.Update.Admin* componente. Il nome dell'avviso, descrizione e correzione tutti visualizzati come:  
    - *Errore: modello per FaultType ResourceProviderTimeout è manca.*

  Questo avviso può essere tranquillamente ignorato. 


#### <a name="health-and-monitoring"></a>Monitoraggio dell'integrità e
- <!-- 1264761 - IS ASDK -->  Si potrebbero visualizzare avvisi per i *controller integrità* componente con i dettagli seguenti:  

   Avviso #1:
   - NOME: Ruolo di infrastruttura non integro
   - GRAVITÀ: avviso
   - COMPONENTI: Controllo di integrità
   - Descrizione: Il controller di integrità Heartbeat Scanner è disponibile. Ciò può influire sulle metriche e i report sull'integrità.  

  Avviso #2:
   - NOME: Ruolo di infrastruttura non integro
   - GRAVITÀ: avviso
   - COMPONENTI: Controllo di integrità
   - Descrizione: Il controller di integrità errore Scanner è disponibile. Ciò può influire sulle metriche e i report sull'integrità.

  Entrambi gli avvisi possono essere tranquillamente ignorati. Verranno chiusi automaticamente nel corso del tempo.  
 

#### <a name="compute"></a>Calcolo
- <!-- TBD - IS --> Quando si seleziona una dimensione di macchina virtuale per una distribuzione della macchina virtuale, alcune dimensioni di VM serie F non sono visibili nell'ambito del selettore di dimensione quando si crea una macchina virtuale. Le dimensioni delle macchine Virtuali seguenti non vengono visualizzati nel selettore: *F8s_v2*, *F16s_v2*, *F32s_v2*, e *F64s_v2*.  
  In alternativa, usare uno dei metodi seguenti per distribuire una macchina virtuale. In ogni metodo, è necessario specificare le dimensioni di macchina virtuale da usare.

  - **Modello di Azure Resource Manager:** quando si usa un modello, impostare il *vmSize* nel modello per uguale alle dimensioni della macchina virtuale desiderata. Ad esempio, il seguente consente di distribuire una macchina virtuale che usa il *F32s_v2* dimensioni:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Comando di Azure:** è possibile usare il [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) comando e specificare le dimensioni VM come parametro, simile a `--size "Standard_F32s_v2"`.

  - **PowerShell:** con PowerShell è possibile usare [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) con il parametro che specifica le dimensioni VM, simile a `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile usare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario usare il `-Name` parametro anziché `-VMScaleSetName`.

- <!-- TBD - IS --> Quando si crea un set di disponibilità nel portale, dovrà **New** > **calcolo** > **set di disponibilità**, è possibile creare solo un set di disponibilità con un dominio di errore e dominio di aggiornamento 1. Creare set tramite PowerShell, interfaccia della riga di comando, o dall'interno di disponibilità come soluzione alternativa, quando si crea una nuova macchina virtuale, il portale.

- <!-- TBD - IS ASDK --> Quando si creano macchine virtuali nel portale utenti Azure Stack, il portale Visualizza un numero errato di dischi dati che è possibile collegare a una VM serie D. Serie D supportate tutte le macchine virtuali è possono soddisfare tutti i dischi dati come la configurazione di Azure.

- <!-- TBD - IS ASDK --> Quando un'immagine di macchina virtuale ha esito negativo deve essere creato, un elemento non riuscito che non è possibile eliminare potrebbe essere aggiunto al pannello calcolo immagini di macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo dovrebbe risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale che in precedenza non è riuscita.

- <!-- TBD - IS ASDK --> Se il provisioning di un'estensione in una distribuzione della VM impiega troppo tempo, gli utenti devono consentire il timeout provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 IS ASDK --> Diagnostica delle VM di Linux non è supportata in Azure Stack. Quando si distribuisce una VM Linux con abilitata la diagnostica della macchina virtuale, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilitano le metriche di base della VM Linux tramite le impostazioni di diagnostica.  


#### <a name="networking"></a>Rete
- <!-- 1766332 - IS ASDK --> Sotto **Networking**, se si fa clic su **creare Gateway VPN** per configurare una connessione VPN, **basata su criteri** viene elencato come un tipo di VPN. Non selezionare questa opzione. Solo le **basato su Route** opzione è supportata in Azure Stack.

- <!-- 2388980 - IS ASDK --> Dopo che una macchina virtuale viene creata e associata a un indirizzo IP pubblico, si non è possibile annullare l'associazione di tale macchina virtuale da quell'indirizzo IP. Dissociazione sembra funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato alla VM originale.

  Attualmente, è necessario usare solo nuovi indirizzi IP pubblici per nuove VM eventualmente create.

  Questo comportamento si verifica anche se si riassegna una nuova macchina virtuale l'indirizzo IP (noto come un *scambio VIP*). Tutti i successivi tentativi di connettersi tramite il risultato di indirizzi IP in una connessione alla macchina virtuale di origine associata e non a quello nuovo.

- <!-- 2292271 - IS ASDK --> Se si aumenta un limite di Quota per una risorsa di rete che fa parte di un'offerta e il piano associato a una sottoscrizione tenant, il nuovo limite non viene applicato a tale sottoscrizione. Tuttavia, il nuovo limite è applicabile per i nuovi abbonamenti creati dopo che la quota viene aumentata. 

  Per risolvere questo problema, utilizzare un piano del componente aggiuntivo per aumentare una Quota di rete quando il piano è già associato a una sottoscrizione. Per altre informazioni, vedere come [rendere disponibile un piano aggiuntivo](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> È possibile eliminare una sottoscrizione con risorse di zona DNS o le risorse di tabella di Route associate. Per eliminare correttamente la sottoscrizione, è innanzitutto necessario eliminare le risorse di zona DNS e tabella di Route nella sottoscrizione del tenant. 
  

- <!-- 1902460 - IS ASDK --> Azure Stack supporta un'unica *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni tenant. Dopo la creazione della prima rete locale connessione gateway, successivi tentativi di creare una risorsa gateway di rete locale con lo stesso indirizzo IP vengono bloccate.

- <!-- 16309153 - IS ASDK --> In una rete virtuale che è stato creato con l'impostazione Server DNS *automatica*, la modifica di un errore di Server DNS personalizzato. Le impostazioni aggiornate non vengono inviate alle macchine virtuali in tale rete virtuale.

- <!-- TBD - IS ASDK --> Azure Stack non supporta l'aggiunta di interfacce di rete aggiuntiva a un'istanza di macchina virtuale dopo aver distribuita la macchina virtuale. Se la macchina virtuale richiede più di un'interfaccia di rete, devono essere definite in fase di distribuzione.

- <!-- 2096388 IS --> È possibile usare il portale di amministrazione per aggiornare le regole per un gruppo di sicurezza di rete. 

    Soluzione alternativa per il servizio App: se è necessario per desktop remoto alle istanze del Controller, si modificano le regole di sicurezza all'interno dei gruppi di sicurezza di rete con PowerShell.  Come negli esempi seguenti viene *consentire*e quindi ripristinare la configurazione per *negare*:  
    
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

- <!-- TBD - IS --> È supportato solo il provider di risorse per creare gli elementi nei server di tale host SQL o MySQL. Gli elementi creati in un server host che non vengono creati dal provider di risorse potrebbe essere in uno stato non corrispondente.  

- <!-- IS, ASDK --> Caratteri speciali, tra cui spazi e i periodi, non sono supportati nel **famiglia** oppure **livello** nomi quando si crea uno SKU per i provider di risorse SQL e MySQL.


> [!NOTE]  
> <!-- TBD - IS --> Dopo aver aggiornato per Azure Stack 1804, è possibile continuare a usare i provider di risorse MySQL e SQL distribuito in precedenza.  È consigliabile che aggiornare SQL e MySQL quando diventa disponibile una nuova versione. Come Azure Stack, applicare gli aggiornamenti per i provider di risorse SQL e MySQL in modo sequenziale.  Ad esempio, se si usa la versione 1802, applicare prima versione 1803 e quindi aggiornare a 1804.      
>   
> L'installazione dell'aggiornamento 1804 non interessa l'utilizzo corrente di provider di risorse SQL o MySQL dagli utenti.
> Indipendentemente dalla versione dei provider di risorse che è usare, i dati degli utenti nei relativi database non viene aggiornati e rimangono accessibili.    



#### <a name="app-service"></a>Servizio app
- <!-- 2352906 - IS ASDK --> Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- <!-- TBD - IS ASDK --> Per ridimensionare l'infrastruttura (ruoli di lavoro, gestione, i ruoli front-end), è necessario usare PowerShell come descritto nelle note sulla versione per il calcolo.

- <!-- TBD - IS ASDK --> Servizio App può essere distribuito solo nel **sottoscrizione del Provider predefinito** in questo momento.  In un futuro aggiornamento App servizio verranno distribuiti nella nuova sottoscrizione di misurazione introdotta in Azure Stack 1804 e tutte le distribuzioni esistenti verranno migrate anche a questa nuova sottoscrizione.

#### <a name="usage"></a>Uso  
- <!-- TBD - IS ASDK --> Dati misuratore utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* timestamp indicante quando il record è stato creato. Attualmente, è possibile usare questi dati per eseguire l'accounting accurato dell'utilizzo di indirizzi IP pubblico.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Scaricare l'aggiornamento
È possibile scaricare il pacchetto di aggiornamento di Azure Stack 1804 dal [qui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Vedere anche 
- Per usare il punto finale con privilegi (PEP) per monitorare e riprendere gli aggiornamenti, vedere [monitorare gli aggiornamenti in Azure Stack tramite l'endpoint con privilegi](azure-stack-monitor-update.md).
- Per una panoramica di gestione degli aggiornamenti in Azure Stack, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md).
- Per altre informazioni su come applicare gli aggiornamenti con Azure Stack, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).
