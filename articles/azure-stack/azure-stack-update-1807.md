---
title: Aggiornamento di Azure Stack 1807 | Microsoft Docs
description: Informazioni sulle novità nell'aggiornamento 1807 per i sistemi integrati di Azure Stack, inclusi i problemi noti e su dove scaricare l'aggiornamento.
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
ms.date: 07/24/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: fe37a62d364d53d03a232b6fdc41e38a9ae2e30f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228220"
---
# <a name="azure-stack-1807-update"></a>Aggiornamento di Azure Stack 1807

*Si applica a: i sistemi integrati di Azure Stack*

Questo articolo descrive il contenuto del pacchetto di aggiornamento 1807. Questo aggiornamento include miglioramenti e correzioni di problemi noti per questa versione di Azure Stack e dove scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi correlati direttamente al processo di aggiornamento e i problemi con la build (post-installazione).

> [!IMPORTANT]        
> Questo pacchetto di aggiornamento è solo per i sistemi integrati di Azure Stack. Questo pacchetto di aggiornamento non sono applicabili per Azure Stack Development Kit.

## <a name="build-reference"></a>Riferimento alla compilazione    
È il numero di build di aggiornamento di Azure Stack 1807 **1.1807.XX.X**.  


### <a name="new-features"></a>Nuove funzionalità
Questo aggiornamento include i miglioramenti seguenti per Azure Stack.

- <!-- 1658937 | ASDK, IS --> **Avviare i backup in una pianificazione predefinita** -come appliance, Azure Stack possono ora attiva automaticamente i backup infrastructure periodicamente per evitare l'intervento dell'utente. Azure Stack automaticamente anche consentirà di pulire la condivisione esterna per i backup precedenti rispetto al periodo di memorizzazione definito. 

- <!-- 2496385 | ASDK, IS --> **Nel tempo di backup totale tempo di trasferimento dei dati aggiunti.**

-   <!-- 1702130 | ASDK, IS --> **Backup esterna capacità Mostra ora la capacità corretta della condivisione esterna.** (In precedenza questo era hardcoded a 10 GB.)

- <!-- 2508488 |  IS   -->   Espandere la capacità dal [aggiunta di nodi di unità di scalabilità aggiuntiva](azure-stack-add-scale-node.md).



### <a name="fixed-issues"></a>Problemi risolti

- <!-- 448955 | IS ASDK --> È possibile ora correttamente le query in log attività per i sistemi distribuiti in un fuso orario UTC + N.    

- <!-- 2319627 |  ASDK, IS --> Verifica preliminare per i parametri di configurazione del backup (chiave di crittografia/percorso/nome utente/Password) non è più imposta impostazioni non corrette per la configurazione del backup. (In precedenza, non corretta delle impostazioni sono state impostate nel backup e backup sarebbe avrà quindi esito negativo quando tirggered.)

- <!-- 2215948 |  ASDK, IS --> A questo punto l'elenco dei backup viene aggiornato quando si elimina manualmente il backup dalla condivisione esterna.

- <!-- 2332636 - IS -->  Aggiornamento a questa versione non è più Reimposta il proprietario predefinito della sottoscrizione del provider predefinito all'utente di CloudAdmin predefinito quando distribuito con AD FS.

- <!-- 2360715 |  ASDK, IS -->  Quando si configura integrazione di Data Center, è accedere non è più il file di metadati di AD FS da una condivisione. Per altre informazioni, vedere [impostazione dell'integrazione di AD FS, fornendo il file di metadati di federazione](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

- <!-- 2388980 | ASDK, IS --> È stato risolto un problema che ha impedito agli utenti di assegnato un indirizzo IP pubblico esistente che è stata precedentemente assegnato a un'interfaccia di rete o servizio di bilanciamento del carico a una nuova interfaccia di rete o servizio di bilanciamento del carico.  

-   <!--2292271 | ASDK, IS --> È stato risolto un problema in cui un limite di Quota modificato non riguarda le sottoscrizioni esistenti.  A questo punto, quando si aumenta un limite di Quota per una risorsa di rete che fa parte di un'offerta e piano associato a una sottoscrizione tenant, il nuovo limite si applica a sottoscrizioni esistenti, nonché le nuove sottoscrizioni.

- **Varie correzioni di** per le prestazioni, stabilità, sicurezza e il sistema operativo che viene usato da Azure Stack.


<!-- ### Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Prima di iniziare    

### <a name="prerequisites"></a>Prerequisiti
- Installare Azure Stack [1805 aggiornare](azure-stack-update-1805.md) prima di applicare l'aggiornamento di Azure Stack 1807.  Si è verificato alcun aggiornamento 1806.  

- Prima di iniziare l'installazione dell'aggiornamento 1807, eseguire [Test-AzureStack](azure-stack-diagnostic-test.md) per convalidare lo stato di Azure Stack e risolvere eventuali problemi operativi trovati. Anche gli avvisi attivi rivedere e risolvere gli eventuali che richiedono un'azione.

### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento   
- <!-- 2468613 - IS --> Durante l'installazione di questo aggiornamento, si potrebbero visualizzare avvisi con il titolo *Error: modello per FaultType UserAccounts.New è manca.*  È possibile ignorare questi avvisi. Questi avvisi chiuderà automaticamente al termine dell'installazione dell'aggiornamento.   

- <!-- 2489559 - IS --> Non tentare di creare macchine virtuali durante l'installazione dell'aggiornamento. Per altre informazioni sulla gestione degli aggiornamenti, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Passaggi di post-aggiornamento
*Non sono previsti passaggi di post-aggiornamento per aggiornamento 1807.*

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - Link to KB  
 -->

## <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)
Di seguito sono problemi noti di post-installazione per questa versione di build.

### <a name="portal"></a>Portale  
- <!-- 2551834 - IS, ASDK --> Quando si seleziona **Overview** per un account di archiviazione nei portali di amministratore o utente, le informazioni dalle *Essentials* riquadro non viene visualizzata.  Il riquadro Essentials Visualizza informazioni sull'account, ad esempio relativi *gruppo di risorse*, *posizione*, e *Subscription ID*.  Altre opzioni per la panoramica sono accessibili, ad esempio *Services* e *Monitoring*, nonché le opzioni per *Apri in Esplora* o a *Elimina account di archiviazione* .

  Per visualizzare le informazioni non disponibili, usare il [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) cmdlet di PowerShell.

- <!-- 2551834 - IS, ASDK --> Quando si seleziona **tag** per un account di archiviazione nei portali di amministratore o utente non è possibile caricare le informazioni e non vengono visualizzati.  

  Per visualizzare le informazioni non disponibili, usare il [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) cmdlet di PowerShell. 

- <!-- TBD - IS ASDK --> Alcuni tipi di sottoscrizione amministrativi non disponibili.  Quando si esegue l'aggiornamento di Azure Stack per questa versione, i tipi di due sottoscrizioni che erano [introdotta con la versione 1804](azure-stack-update-1804.md#new-features) non sono visibili nella console. Si tratta di un comportamento previsto. I tipi di sottoscrizione disponibili sono *sottoscrizione di misurazione*, e *sottoscrizione consumo*. Questi tipi di sottoscrizione sono visibili in nuovi ambienti Azure Stack partire dalla versione 1804 ma non sono ancora pronti per l'uso. È consigliabile continuare a usare il *Provider predefinito* tipo di sottoscrizione.  

- <!-- 2403291 - IS ASDK --> Non si dispone di utilizzo della barra di scorrimento orizzontale nella parte inferiore di portali di amministratore e utente. Se non è possibile accedere la barra di scorrimento orizzontale, usare i percorsi di navigazione per passare al pannello precedente nel portale selezionando il nome del pannello si desidera visualizzare nell'elenco di navigazione nella parte superiore sinistra del portale.
  ![Barra di navigazione](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> Potrebbe non essere possibile visualizzare le risorse di calcolo o archiviazione nel portale di amministrazione. La causa del problema è un errore durante l'installazione dell'aggiornamento che causa l'aggiornamento da segnalare in modo errato come esito positivo. Se si verifica questo problema, contattare il supporto tecnico clienti Microsoft per assistenza.

- <!-- TBD - IS --> È possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, selezionare l'icona a forma di ingranaggio in alto a destra del portale e quindi selezionare **ripristinare le impostazioni predefinite**.

- <!-- TBD - IS ASDK --> Eliminazione utente diverso, le sottoscrizioni le risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- <!-- TBD - IS ASDK --> Non è possibile visualizzare le autorizzazioni per la sottoscrizione tramite il portale di Azure Stack. In alternativa, usare PowerShell per verificare le autorizzazioni.


### <a name="health-and-monitoring"></a>Monitoraggio dell'integrità e
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

  Entrambi gli avvisi possono essere tranquillamente ignorati e si chiude automaticamente nel corso del tempo.  

- <!-- 2368581 - IS. ASDK --> Un operatore di Azure Stack, se si riceve un avviso di memoria insufficiente e le macchine virtuali tenant non riuscire a distribuire con un *errore di creazione della macchina virtuale di Fabric*, è possibile che l'indicatore di Azure Stack ha esaurito la memoria disponibile. Usare la [Capacity Planner di Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) per comprendere meglio la capacità disponibile per i carichi di lavoro.


### <a name="compute"></a>Calcolo
- <!-- 2724873 - IS --> Quando si usano i cmdlet di PowerShell **Start-AzsScaleUnitNode** oppure **Stop-AzsScaleunitNode** per gestire le unità di scala, il primo tentativo di avviare o arrestare l'unità di scala potrebbe non riuscire. Se il cmdlet non riesce durante la prima esecuzione, eseguire il cmdlet una seconda volta. La seconda esecuzione avrà esito positivo per completare l'operazione. 

- <!-- 2494144 - IS, ASDK --> Quando si seleziona una dimensione di macchina virtuale per una distribuzione della macchina virtuale, alcune dimensioni di VM serie F non sono visibili nell'ambito del selettore di dimensione quando si crea una macchina virtuale. Le dimensioni delle macchine Virtuali seguenti non vengono visualizzati nel selettore: *F8s_v2*, *F16s_v2*, *F32s_v2*, e *F64s_v2*.  
  In alternativa, usare uno dei metodi seguenti per distribuire una macchina virtuale. In ogni metodo, è necessario specificare le dimensioni di macchina virtuale da usare.

  - **Modello di Azure Resource Manager:** quando si usa un modello, impostare il *vmSize* nel modello per uguale alle dimensioni della macchina virtuale da usare. Ad esempio, la voce seguente viene usata per distribuire una macchina virtuale che usa il *F32s_v2* dimensioni:  

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

- <!-- TBD - IS ASDK --> Quando si creano macchine virtuali nel portale utenti Azure Stack, il portale Visualizza un numero errato di dischi dati che è possibile collegare a una VM serie DS. Le VM serie DS può supportare tutti i dischi dati come la configurazione di Azure.

- <!-- TBD - IS ASDK --> Quando un'immagine di macchina virtuale ha esito negativo deve essere creato, un elemento non riuscito che non è possibile eliminare potrebbe essere aggiunto al pannello calcolo immagini di macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo dovrebbe risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile riprovare a eseguire il download dell'immagine di macchina virtuale che in precedenza non è riuscita.

- <!-- TBD - IS ASDK --> Se il provisioning di un'estensione in una distribuzione della VM impiega troppo tempo, gli utenti devono consentire il timeout provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 IS ASDK --> Diagnostica delle VM di Linux non è supportata in Azure Stack. Quando si distribuisce una VM Linux con abilitata la diagnostica della macchina virtuale, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilitano le metriche di base della VM Linux tramite le impostazioni di diagnostica.  


### <a name="networking"></a>Rete  

- <!-- 1766332 - IS ASDK --> Sotto **Networking**, se si fa clic su **creare Gateway VPN** per configurare una connessione VPN, **basata su criteri** viene elencato come un tipo di VPN. Non selezionare questa opzione. Solo le **basato su Route** opzione è supportata in Azure Stack.

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


### <a name="sql-and-mysql"></a>SQL e MySQL

- <!-- TBD - IS --> È supportato solo il provider di risorse per creare gli elementi nei server di tale host SQL o MySQL. Gli elementi creati in un server host che non vengono creati dal provider di risorse potrebbe essere in uno stato non corrispondente.  

- <!-- IS, ASDK --> Caratteri speciali, tra cui spazi e i periodi, non sono supportati nel **famiglia** oppure **livello** nomi quando si crea uno SKU per i provider di risorse SQL e MySQL.


> [!NOTE]  
> <!-- TBD - IS --> Dopo l'aggiornamento a questa versione di Azure Stack, è possibile continuare a usare i provider di risorse MySQL e SQL distribuito in precedenza.  È consigliabile che aggiornare SQL e MySQL quando diventa disponibile una nuova versione. Come Azure Stack, applicare gli aggiornamenti per i provider di risorse SQL e MySQL in modo sequenziale. Ad esempio, se si usa la versione 1804, applicare prima versione 1805 e quindi aggiornare a 1807.      
>   
> L'installazione di questo aggiornamento non influenza l'utilizzo corrente di provider di risorse SQL o MySQL dagli utenti.
> Indipendentemente dalla versione dei provider di risorse che è usare, i dati degli utenti nei relativi database non viene aggiornati e rimangono accessibili.    



### <a name="app-service"></a>Servizio app
- <!-- 2352906 - IS ASDK --> Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- <!-- 2489178 - IS ASDK --> Per ridimensionare l'infrastruttura (ruoli di lavoro, gestione, i ruoli front-end), è necessario usare PowerShell come descritto nelle note sulla versione per il calcolo.

- <!-- TBD - IS ASDK --> Servizio App può essere distribuito solo nel *sottoscrizione del Provider predefinito* in questo momento. In un aggiornamento futuro, servizio App verrà distribuito nel nuovo *misurazione sottoscrizione* che è stata introdotta in Azure Stack 1804. Quando l'analisi sono supportato l'utilizzo, tutte le distribuzioni esistenti verranno migrate a questo nuovo tipo di sottoscrizione.


### <a name="usage"></a>Uso  
- <!-- TBD - IS ASDK --> Dati misuratore utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* timestamp indicante quando il record è stato creato. Attualmente, è possibile usare questi dati per eseguire l'accounting accurato dell'utilizzo di indirizzi IP pubblico.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Scaricare l'aggiornamento
È possibile scaricare il pacchetto di aggiornamento di Azure Stack 1807 dal [qui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Vedere anche 
- Per usare il punto finale con privilegi (PEP) per monitorare e riprendere gli aggiornamenti, vedere [monitorare gli aggiornamenti in Azure Stack tramite l'endpoint con privilegi](azure-stack-monitor-update.md).
- Per una panoramica di gestione degli aggiornamenti in Azure Stack, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md).
- Per altre informazioni su come applicare gli aggiornamenti con Azure Stack, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).
