---
title: Aggiornamento di Azure Stack 1803 | Microsoft Docs
description: Informazioni sulle novità nell'aggiornamento 1803 per Azure Stack integrati sistemi, i problemi noti e dove scaricare l'aggiornamento.
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 11430a0d194a722c0c0520c936db3c08b1a6b863
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989572"
---
# <a name="azure-stack-1803-update"></a>Aggiornamento di Azure Stack 1803

*Si applica a: i sistemi integrati di Azure Stack*

Questo articolo vengono descritti i miglioramenti e correzioni nel pacchetto di aggiornamento 1803, problemi noti per questa versione e dove scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi correlati direttamente al processo di aggiornamento e i problemi con la build (post-installazione).

> [!IMPORTANT]        
> Questo pacchetto di aggiornamento è solo per i sistemi integrati di Azure Stack. Questo pacchetto di aggiornamento non sono applicabili per Azure Stack Development Kit.

## <a name="build-reference"></a>Riferimento alla compilazione    
È il numero di build di aggiornamento di Azure Stack 1803 **20180329.1**.


## <a name="before-you-begin"></a>Prima di iniziare    
> [!IMPORTANT]    
> Non tentare di creare macchine virtuali durante l'installazione dell'aggiornamento. Per altre informazioni sulla gestione degli aggiornamenti, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>Prerequisiti
- Installare Azure Stack [1802 Update](azure-stack-update-1802.md) prima di applicare l'aggiornamento di Azure Stack 1803.   

- Installare **AzS Hotfix – 1.0.180312.1-compilare 20180222.2** prima di applicare l'aggiornamento di Azure Stack 1803. Questa correzione degli aggiornamenti di Windows Defender ed è disponibile quando si scaricano gli aggiornamenti per Azure Stack.

  Per installare l'hotfix, seguire le procedure normali per [installazione di aggiornamenti per Azure Stack](azure-stack-apply-updates.md). Il nome dell'aggiornamento viene visualizzato come **AzS Hotfix – 1.0.180312.1**e include i file seguenti: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Dopo il caricamento di questi file per un account di archiviazione e un contenitore, eseguire l'installazione dal riquadro di aggiornamento nel portale di amministrazione. 
  
  A differenza degli aggiornamenti allo Stack di Azure, installare questo aggiornamento non modifica la versione di Azure Stack. Per verificare che questo aggiornamento è installato, visualizzare l'elenco dei **gli aggiornamenti installati**.



### <a name="new-features"></a>Nuove funzionalità 
Questo aggiornamento include i seguenti miglioramenti e correzioni per Azure Stack.

- **Aggiornare i segreti di Azure Stack** : (account e certificati). Per altre informazioni sulla gestione dei segreti, vedere [ruotare i segreti in Azure Stack](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Reindirizzamento automatico a HTTPS** quando si Usa HTTP per accedere ai portali di amministratore e utente. È stato effettuato in base a questo miglioramento [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) commenti e suggerimenti per Azure Stack. 

- <!-- 2202621  --> **Accedere al Marketplace** : È ora possibile aprire il Marketplace di Azure Stack tramite il [+ nuovo](https://ms.portal.azure.com/#create/hub) opzione all'interno di portali di amministratore e utente di esattamente come nei portali di Azure.
 
- <!-- 2202621 --> **Monitoraggio di Azure** -aggiunge Azure Stack [monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) ai portali di amministratore e utente. Sono inclusi nuovi strumenti di esplorazione per le metriche e i log attività. Per accedere a questo monitoraggio di Azure alle reti esterne, la porta **13012** deve essere aperta in configurazioni del firewall. Per altre informazioni sulle porte richieste da Azure Stack, vedere [integrazione di Data Center di Azure Stack - pubblicano endpoint](azure-stack-integrate-endpoints.md).

   Anche come parte di questa modifica, in **altri servizi**, *log di controllo* appare ora come *log attività*. La funzionalità è ora coerenza con il portale di Azure. 

- <!-- 1664791 --> **I file sparse** : quando si aggiunge una nuova immagine per Azure Stack, o aggiungere un'immagine tramite diffusione di marketplace, l'immagine viene convertito in un file sparse. Impossibile convertire immagini che sono stati aggiunti prima di usare Azure Stack versione 1803. In alternativa, è necessario utilizzare diffusione di marketplace per inviare di nuovo tali immagini per sfruttare i vantaggi di questa funzionalità. 
 
   I file sparse sono un formato di file efficiente utilizzato per ridurre l'uso di spazio di archiviazione e migliorare il / o.  Per altre informazioni, vedere [Fsutil sparse](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) per Windows Server. 

### <a name="fixed-issues"></a>Problemi risolti

- <!-- 1739988 --> Bilanciamento di carico interno (ILB) ora gestisce correttamente gli indirizzi MAC per le macchine virtuali back-end, in modo che ILB per scartare i pacchetti per la rete back-end quando si usano istanze di Linux nella rete back-end. Bilanciamento del carico interno funziona correttamente con le istanze di Windows in rete back-end. 

- <!-- 1805496 --> Un problema in cui verrebbe vengono disconnesse le connessioni VPN tra Azure Stack a causa di Azure Stack tramite impostazioni diverse per i criteri IKE di Azure. I valori per SALifetime (ora) e SALiftetime (byte) non sono compatibili con Azure e sono stati modificati in 1803 per la corrispondenza le impostazioni di Azure. Il valore di SALifetime (secondi) prima di 1803 era 14.400 e a questo punto le modifiche apportate a 27.000 della versione 1803. Il valore di SALifetime (byte) prima di 1803 era 819,200 e le modifiche apportate a 33,553,408 della versione 1803.

- <!-- 2209262 --> Il problema IP in cui le connessioni VPN è stata precedentemente visibili nel portale. abilitazione o attivare e disattivare l'inoltro IP ha tuttavia alcun effetto. La funzionalità è attivata per impostazione predefinita e la possibilità di modificare questa impostazione non è ancora supportata.  Il controllo è stato rimosso dal portale. 

- <!-- 1766332 --> Azure Stack non supporta i gateway VPN basato su criteri, anche se l'opzione viene visualizzata nel portale.  L'opzione è stata rimossa dal portale. 

- <!-- 1868283 --> Azure Stack ora impedisce il ridimensionamento di una macchina virtuale creata con i dischi dinamici. 

- <!-- 1756324 --> I dati di utilizzo per le macchine virtuali sono ora separati a intervalli di ore. Ciò è coerenza con Azure. 

- <!--  2253274 --> Il problema in cui nei portali di amministratore e utente, il pannello delle impostazioni per subnet rete virtuale non riesce a caricare. In alternativa, usare PowerShell e il [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet per visualizzare e gestire queste informazioni.

- Quando si crea una macchina virtuale, il messaggio *non è possibile visualizzare i prezzi* non viene più visualizzata quando si sceglie una dimensione per le dimensioni VM.

- Varie correzioni per prestazioni, stabilità, sicurezza e il sistema operativo che viene usato da Azure Stack.


### <a name="changes"></a>Modifiche
- Il modo per modificare lo stato di un'offerta appena creato dal *privati* a *pubblico* o *autorizzazioni rimosse* è stato modificato. Per altre informazioni, vedere [creare un'offerta](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento    
<!-- 2328416 --> Durante l'installazione dell'aggiornamento 1803, possono essere presenti i tempi di inattività del servizio blob e dei servizi interni che utilizzano il servizio blob. Sono incluse alcune operazioni di macchina virtuale. Questo tempo di inattività può causare errori del tenant di operazioni o gli avvisi dai servizi che non è possibile accedere ai dati. Questo problema si risolverà automaticamente al termine dell'aggiornamento installazione. 



### <a name="post-update-steps"></a>Passaggi di post-aggiornamento
- Dopo l'installazione di 1803, installare eventuali hotfix applicabili. Per altre informazioni, visualizzare i seguenti articoli della knowledge base, nonché nostri [criteri per la manutenzione](azure-stack-servicing-policy.md).

  - [KB 4344115 - aggiornamento rapido di Azure Stack 1.0.180427.15](https://support.microsoft.com/help/4344115).

- Dopo aver installato questo aggiornamento, esaminare la configurazione del firewall per garantire [porte necessarie](azure-stack-integrate-endpoints.md) siano aperte. Ad esempio, questo aggiornamento introduce *monitoraggio di Azure* che include una modifica del log di controllo per i log attività. Con questa modifica, la porta 13012 viene ora usata e deve anche essere aperta.  


### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)
Di seguito sono riportati problemi noti di post-installazione per la compilazione **20180323.2**.

#### <a name="portal"></a>Portale
- <!-- 2332636 - IS -->  Quando si usa AD FS per il sistema di identità di Azure Stack e l'aggiornamento a questa versione di Azure Stack, il proprietario predefinito della sottoscrizione del provider predefinito viene reimpostato per l'oggetto incorporato **CloudAdmin** utente.  
  Soluzione alternativa: Per risolvere questo problema dopo aver installato questo aggiornamento, passaggio 3 da usare la [attivare l'automazione per configurare attestazioni attendibilità del provider in Azure Stack](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedura per reimpostare il proprietario della sottoscrizione del provider predefinito.   

- La possibilità [per aprire una nuova richiesta di supporto nell'elenco a discesa](azure-stack-manage-portals.md#quick-access-to-help-and-support) all'interno di amministratore del portale non è disponibile. In alternativa, usare il collegamento seguente:     
    - Per Azure Stack i sistemi integrati, usare https://aka.ms/newsupportrequest.

- <!-- 2050709 --> Nel portale di amministrazione, non è possibile modificare le metriche di archiviazione per il servizio Blob, servizio tabelle o servizio di Accodamento. Quando si accede all'archiviazione e quindi seleziona il blob, tabella o coda service riquadro, viene aperto un nuovo pannello che visualizza un grafico delle metriche per il servizio. Se si seleziona Modifica dalla parte superiore del riquadro del grafico delle metriche, il pannello Modifica grafico si apre, ma non visualizza le opzioni per modificare le metriche.

- Potrebbe non essere possibile visualizzare le risorse di calcolo o archiviazione nel portale di amministrazione. La causa del problema è un errore durante l'installazione dell'aggiornamento che causa l'aggiornamento da segnalare in modo errato come esito positivo. Se si verifica questo problema, contattare il supporto tecnico clienti Microsoft per assistenza.

- È possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, selezionare l'icona a forma di ingranaggio in alto a destra del portale e quindi selezionare **ripristinare le impostazioni predefinite**.

- Eliminazione utente diverso, le sottoscrizioni le risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- Non è possibile visualizzare le autorizzazioni per la sottoscrizione tramite il portale di Azure Stack. In alternativa, usare PowerShell per verificare le autorizzazioni.

- Nel dashboard del portale di amministrazione, il riquadro di aggiornamento non riesce a visualizzare le informazioni sugli aggiornamenti. Per risolvere questo problema, fare clic sul riquadro per aggiornarlo.

- Nel portale di amministrazione, è possibile notare un avviso critico per la *Microsoft.Update.Admin* componente. Il nome dell'avviso, descrizione e correzione tutti visualizzati come:  
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


#### <a name="marketplace"></a>Marketplace
- Gli utenti possono esplorare il marketplace completo senza una sottoscrizione e possono visualizzare amministrativi elementi quali offerte e piani. Questi elementi sono non funzionale per gli utenti.



#### <a name="compute"></a>Calcolo
- Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile usare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario usare il `-Name` parametro anziché `-VMScaleSetName`.

- Quando si crea un set di disponibilità nel portale, dovrà **New** > **calcolo** > **set di disponibilità**, è possibile creare solo un set di disponibilità con un dominio di errore e dominio di aggiornamento 1. Creare set tramite PowerShell, interfaccia della riga di comando, o dall'interno di disponibilità come soluzione alternativa, quando si crea una nuova macchina virtuale, il portale.

- Quando si creano macchine virtuali nel portale utenti Azure Stack, il portale Visualizza un numero errato di dischi dati che è possibile collegare a una VM serie D. Serie D supportate tutte le macchine virtuali è possono soddisfare tutti i dischi dati come la configurazione di Azure.

- Quando un'immagine di macchina virtuale ha esito negativo deve essere creato, un elemento non riuscito che non è possibile eliminare potrebbe essere aggiunto al pannello calcolo immagini di macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo dovrebbe risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale che in precedenza non è riuscita.

-  Se il provisioning di un'estensione in una distribuzione della VM impiega troppo tempo, gli utenti devono consentire il timeout provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 --> Diagnostica delle VM di Linux non è supportata in Azure Stack. Quando si distribuisce una VM Linux con abilitata la diagnostica della macchina virtuale, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilitano le metriche di base della VM Linux tramite le impostazioni di diagnostica.  


#### <a name="networking"></a>Rete
- Dopo che una macchina virtuale viene creata e associata a un indirizzo IP pubblico, si non è possibile annullare l'associazione di tale macchina virtuale da quell'indirizzo IP. Dissociazione sembra funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato alla VM originale.

  Attualmente, è necessario usare solo nuovi indirizzi IP pubblici per nuove VM eventualmente create.

  Questo comportamento si verifica anche se si riassegna una nuova macchina virtuale l'indirizzo IP (noto come un *scambio VIP*). Tutti i successivi tentativi di connettersi tramite il risultato di indirizzi IP in una connessione alla macchina virtuale di origine associata e non a quello nuovo.



- Azure Stack supporta un'unica *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni tenant. Dopo la creazione della prima rete locale connessione gateway, successivi tentativi di creare una risorsa gateway di rete locale con lo stesso indirizzo IP vengono bloccate.

- In una rete virtuale che è stato creato con l'impostazione Server DNS *automatica*, la modifica di un errore di Server DNS personalizzato. Le impostazioni aggiornate non vengono inviate alle macchine virtuali in tale rete virtuale.

- Azure Stack non supporta l'aggiunta di interfacce di rete aggiuntiva a un'istanza di macchina virtuale dopo aver distribuita la macchina virtuale. Se la macchina virtuale richiede più di un'interfaccia di rete, devono essere definite in fase di distribuzione.

- <!-- 2096388 --> È possibile usare il portale di amministrazione per aggiornare le regole per un gruppo di sicurezza di rete. 

    Soluzione alternativa per il servizio App: se è necessario per desktop remoto alle istanze del Controller, si modificano le regole di sicurezza all'interno dei gruppi di sicurezza di rete con PowerShell.  Come negli esempi seguenti viene *consentire*e quindi ripristinare la configurazione per *negare*:  
    
    - *Consenti:*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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
- Prima di procedere, rivedere la nota importante in [prima di iniziare](#before-you-begin) all'inizio di queste note sulla versione.

- Possono volerci fino a un'ora prima che gli utenti possono creare i database in una nuova distribuzione di SQL o MySQL.

- È supportato solo il provider di risorse per creare gli elementi nei server di tale host SQL o MySQL. Gli elementi creati in un server host che non vengono creati dal provider di risorse potrebbe essere in uno stato non corrispondente.  

- <!-- IS, ASDK --> Caratteri speciali, tra cui spazi e i periodi, non sono supportati nel **famiglia** nome quando si crea uno SKU per i provider di risorse SQL e MySQL.

> [!NOTE]  
> Dopo aver aggiornato per Azure Stack 1803, è possibile continuare a usare i provider di risorse MySQL e SQL distribuito in precedenza.  È consigliabile che aggiornare SQL e MySQL quando diventa disponibile una nuova versione. Come Azure Stack, applicare gli aggiornamenti per i provider di risorse SQL e MySQL in modo sequenziale.  Ad esempio, se si usa la versione 1711, applicare prima versione 1712, quindi 1802 e quindi aggiornare a 1803.      
>   
> L'installazione dell'aggiornamento 1803 non interessa l'utilizzo corrente di provider di risorse SQL o MySQL dagli utenti.
> Indipendentemente dalla versione dei provider di risorse che è usare, i dati degli utenti nei relativi database non viene aggiornati e rimangono accessibili.    



#### <a name="app-service"></a>Servizio app
- Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- Per ridimensionare l'infrastruttura (ruoli di lavoro, gestione, i ruoli front-end), è necessario usare PowerShell come descritto nelle note sulla versione per il calcolo.


#### <a name="usage"></a>Uso  
- Dati misuratore utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* timestamp indicante quando il record è stato creato. Attualmente, è possibile usare questi dati per eseguire l'accounting accurato dell'utilizzo di indirizzi IP pubblico.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Download degli strumenti di Azure Stack da GitHub
- Quando si usa la *invoke-webrequest* degli strumenti cmdlet di PowerShell per scaricare lo Stack di Azure da Github, viene visualizzato un errore:     
    -  *Invoke-webrequest: richiesta annullata: Impossibile creare un canale sicuro SSL/TLS.*     

  Questo errore si verifica a causa di un recente relativo al supporto GitHub deprecato il Tlsv1 e Tlsv1.1 standard di crittografia (impostazione predefinita per PowerShell). Per altre informazioni, vedere [si noti che la rimozione di standard di crittografia debole](https://githubengineering.com/crypto-removal-notice/).

  Per risolvere questo problema, aggiungere `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` all'inizio dello script per forzare la console di PowerShell di usare TLSv1.2 durante il download dal repository GitHub.


## <a name="download-the-update"></a>Scaricare l'aggiornamento
È possibile scaricare il pacchetto di aggiornamento di Azure Stack 1803 dal [qui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Vedere anche 
- Per usare il punto finale con privilegi (PEP) per monitorare e riprendere gli aggiornamenti, vedere [monitorare gli aggiornamenti in Azure Stack tramite l'endpoint con privilegi](azure-stack-monitor-update.md).
- Per una panoramica di gestione degli aggiornamenti in Azure Stack, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md).
- Per altre informazioni su come applicare gli aggiornamenti con Azure Stack, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).
