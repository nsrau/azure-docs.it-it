---
title: Aggiornamento di Azure Stack 1803 | Documenti Microsoft
description: Informazioni sulle novità nell'aggiornamento 1803 per lo Stack di Azure integrati sistemi, i problemi noti e come scaricare l'aggiornamento.
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
ms.date: 06/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: a74e77f84aa70519015a589cbc6e7478c0c41592
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318810"
---
# <a name="azure-stack-1803-update"></a>Aggiornamento dello Stack 1803 Azure

*Si applica a: Azure Stack integrati sistemi*

In questo articolo vengono descritti i miglioramenti e correzioni nel pacchetto di aggiornamento 1803, conosciuti per questa versione e come scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi direttamente correlati al processo di aggiornamento e con la compilazione (post-installazione).

> [!IMPORTANT]        
> Questo pacchetto di aggiornamento è solo per i sistemi Azure Stack integrato. Questo pacchetto di aggiornamento non si applicano al Kit di sviluppo dello Stack di Azure.

## <a name="build-reference"></a>Riferimento su build    
È il numero di build aggiornamento Azure Stack 1803 **20180329.1**.


## <a name="before-you-begin"></a>Prima di iniziare    
> [!IMPORTANT]    
> Non tentare di creare macchine virtuali durante l'installazione di questo aggiornamento. Per ulteriori informazioni sulla gestione degli aggiornamenti, vedere [gestire gli aggiornamenti in panoramica di Azure Stack](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>Prerequisiti
- Installare lo Stack di Azure [1802 aggiornare](azure-stack-update-1802.md) prima di applicare l'aggiornamento di Azure Stack 1803.   

- Installare **AzS Hotfix – 1.0.180312.1-compilare 20180222.2** prima di applicare l'aggiornamento di Azure Stack 1803. Questo aggiornamento rapido degli aggiornamenti di Windows Defender ed è disponibile quando si scaricano gli aggiornamenti per lo Stack di Azure.

  Per installare l'aggiornamento rapido, seguire le procedure normale per [installazione di aggiornamenti per Azure Stack](azure-stack-apply-updates.md). Il nome dell'aggiornamento viene visualizzato come **AzS Hotfix – 1.0.180312.1**e include i file seguenti: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Dopo aver caricato i file con un account di archiviazione e contenitore, eseguire l'installazione dal riquadro aggiornamento nel portale di amministrazione. 
  
  A differenza degli aggiornamenti allo Stack di Azure, installare questo aggiornamento non modifica la versione dello Stack di Azure. Per verificare questo aggiornamento è installato, visualizzare l'elenco degli **aggiornamenti installati**.



### <a name="new-features"></a>Nuove funzionalità 
Questo aggiornamento include i seguenti miglioramenti e correzioni per lo Stack di Azure.

- **Aggiornare i segreti Azure Stack** - (account e dei certificati). Per ulteriori informazioni sulla gestione dei segreti, vedere [ruotare i segreti nello Stack di Azure](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Reindirizzamento automatico a HTTPS** quando si utilizza HTTP per accedere ai portali di amministratore e utente. Questo miglioramento è stato effettuato in base alle [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) commenti e suggerimenti per lo Stack di Azure. 

- <!-- 2202621  --> **Accedere Marketplace** : È ora possibile aprire Azure Marketplace Stack utilizzando il [+ nuovo](https://ms.portal.azure.com/#create/hub) opzione all'interno di portali e utente di amministrazione la stessa modalità utilizzata nei portali di Azure.
 
- <!-- 2202621 --> **Monitoraggio di Azure** -lo Stack di Azure aggiunge [Monitor Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) ai portali amministratore e utente. Sono inclusi nuovi strumenti di esplorazione dei registri delle metriche e attività. Per accedere a questo monitoraggio di Azure da reti esterne, la porta **13012** deve essere aperta in configurazioni del firewall. Per ulteriori informazioni sulle porte necessario dallo Stack di Azure, vedere [integrazione di Data Center Azure Stack - pubblicare endpoint](azure-stack-integrate-endpoints.md).

   Anche come parte di questa modifica, sotto **altri servizi**, *log di controllo* viene visualizzato come *log attività*. La funzionalità è coerenza con il portale di Azure. 

- <!-- 1664791 --> **I file sparse** - quando si aggiunge una nuova immagine allo Stack di Azure, o aggiungere un'immagine tramite diffusione marketplace, l'immagine viene convertito in un file sparse. Le immagini che sono state aggiunte prima di utilizzare la versione Azure Stack 1803 non possono essere convertite. Al contrario, è necessario utilizzare diffusione marketplace per inviare di nuovo tali immagini per sfruttare i vantaggi di questa funzionalità. 
 
   I file sparse sono un formato di file efficiente utilizzato per ridurre l'uso di spazio di archiviazione e migliorare i/o.  Per altre informazioni, vedere [Fsutil sparse](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) per Windows Server. 

### <a name="fixed-issues"></a>Problemi risolti

- <!-- 1739988 --> Bilanciamento di carico interno (ILB) ora gestire correttamente gli indirizzi MAC per macchine virtuali di back-end, che comporta l'esecuzione ILB eliminare i pacchetti alla rete back-end quando si usa istanze di Linux nella rete back-end. Bilanciamento del carico interno funziona correttamente con le istanze di Windows in rete back-end. 

- <!-- 1805496 --> Un problema in cui le connessioni VPN tra Azure Stack verrà disconnessa a causa dello Stack di Azure utilizzando impostazioni diverse per i criteri IKE rispetto a Azure. I valori per SALifetime (ora) e SALiftetime (byte) non sono compatibili con Azure e sono stati modificati in 1803 corrispondere le impostazioni di Azure. Il valore per SALifetime (secondi) prima di 1803 era 14400 e ora le modifiche ai 27.000 in 1803. Il valore di SALifetime (byte) prima di 1803 era 819,200 e le modifiche ai 33,553,408 in 1803.

- <!-- 2209262 --> Il problema IP in cui le connessioni VPN è stata precedentemente visibile nel portale; attivare o attivare e disattivare l'inoltro IP ha tuttavia alcun effetto. La funzionalità è attivata per impostazione predefinita e la possibilità di modificare questa impostazione non ancora supportato.  Il controllo è stato rimosso dal portale. 

- <!-- 1766332 --> Stack di Azure non supporta i gateway VPN basato su criteri, anche se l'opzione viene visualizzata nel portale.  L'opzione è stato rimosso dal portale. 

- <!-- 1868283 --> Stack Azure ora impedisce il ridimensionamento di una macchina virtuale creata con i dischi dinamici. 

- <!-- 1756324 --> I dati di utilizzo per le macchine virtuali sono ora separati a intervalli di ore. Ciò è coerenza con Azure. 

- <!--  2253274 --> Il problema in cui nei portali di amministratore e utente, il pannello delle impostazioni per la subnet rete virtuale non è possibile caricare. In alternativa, usare PowerShell e il [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet per visualizzare e gestire queste informazioni.

- Quando si crea una macchina virtuale, il messaggio *non è possibile visualizzare i prezzi* non viene più visualizzata quando si sceglie una dimensione per le dimensioni della VM.

- Varie correzioni per le prestazioni, stabilità, sicurezza e il sistema operativo che viene utilizzato dallo Stack di Azure.


### <a name="changes"></a>Modifiche
- Il modo per modificare lo stato di un'offerta appena creato da *privato* a *pubblica* o *rimosse* è stato modificato. Per altre informazioni, vedere [creare un'offerta](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento    
<!-- 2328416 --> Durante l'installazione dell'aggiornamento 1803, possono essere presenti i tempi di inattività del servizio blob e dei servizi interni che utilizzano il servizio blob. Sono incluse alcune operazioni di macchina virtuale. Ciò tempi di inattività può causare errori di tenant operazioni o gli avvisi dai servizi che non è possibile accedere ai dati. Questo problema si risolve termine dell'installazione dell'aggiornamento. 



### <a name="post-update-steps"></a>Passaggi di post-aggiornamento
- Dopo l'installazione di 1803, installare gli aggiornamenti rapidi applicabili. Per ulteriori informazioni, vedere i seguenti articoli della knowledge base, nonché il nostro [manutenzione criteri](azure-stack-servicing-policy.md).

  - [KB 4341390 - Stack Azure Hotfix 1.0.180424.12](https://support.microsoft.com/en-us/help/4341390).

- Dopo aver installato questo aggiornamento, esaminare la configurazione del firewall per garantire [porte necessarie](azure-stack-integrate-endpoints.md) sono aperte. Ad esempio, questo aggiornamento introduce *Monitor Azure* che include una modifica dei registri di controllo per i log di attività. Con questa modifica, porta 13012 viene ora usata e deve essere aperta anche.  


### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)
Di seguito sono i problemi noti di post-installazione per la compilazione **20180323.2**.

#### <a name="portal"></a>Portale
- <!-- 2332636 - IS -->  Quando si usa ADFS per il sistema di identità di Azure Stack e l'aggiornamento alla versione corrente dello Stack di Azure, il proprietario predefinito della sottoscrizione del provider predefinito viene reimpostato su incorporati **CloudAdmin** utente.  
  Soluzione alternativa: Per risolvere questo problema dopo aver installato questo aggiornamento, passaggio 3 da utilizzare il [automazione Trigger per configurare le attestazioni trust del provider in Stack Azure](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedure per reimpostare il proprietario della sottoscrizione del provider predefinito.   

- La capacità [per aprire una nuova richiesta di supporto nell'elenco a discesa](azure-stack-manage-portals.md#quick-access-to-help-and-support) da entro l'amministratore del portale non è attualmente disponibile. Usare invece il collegamento seguente:     
    - Per lo Stack di Azure usare sistemi integrati, https://aka.ms/newsupportrequest.

- <!-- 2050709 --> Nel portale di amministrazione, non è possibile modificare le metriche di archiviazione per il servizio Blob, del servizio tabelle o servizio di Accodamento. Quando si accede alla risorsa di archiviazione e quindi seleziona il riquadro servizio coda, tabella o blob, viene aperto un nuovo pannello che visualizza un grafico delle metriche per il servizio. Se si seleziona quindi modifica dalla parte superiore del riquadro del grafico delle metriche, nel Pannello di modifica grafico viene visualizzata ma opzioni per modificare le metriche non visualizzate.

- Potrebbe non essere possibile visualizzare le risorse di calcolo o di archiviazione nel portale di amministrazione. La causa di questo problema è un errore durante l'installazione dell'aggiornamento che fa sì che l'aggiornamento da segnalare in modo non corretto come esito positivo. Se si verifica questo problema, contattare il supporto tecnico clienti Microsoft per assistenza.

- È possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, selezionare l'icona a forma di ingranaggio in alto a destra del portale e quindi selezionare **ripristinare le impostazioni predefinite**.

- Se si elimina utente sottoscrizioni nelle risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse, quindi eliminare le sottoscrizioni dell'utente.

- Non è possibile visualizzare le autorizzazioni per la sottoscrizione utilizzando i portali di Stack di Azure. In alternativa, utilizzare PowerShell per verificare le autorizzazioni.

- Nel dashboard del portale di amministrazione, il riquadro di aggiornamento non riesce a visualizzare le informazioni sugli aggiornamenti. Per risolvere questo problema, fare clic sul riquadro per aggiornarla.

- Nel portale di amministrazione, è possibile notare un avviso critico per la *Microsoft.Update.Admin* componente. Il nome dell'avviso, la descrizione e monitoraggio e aggiornamento tutti visualizzati come:  
    - *Errore: modello per FaultType ResourceProviderTimeout mancante.*

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


#### <a name="marketplace"></a>Marketplace
- Gli utenti possono esplorare il marketplace completo senza una sottoscrizione e possono vedere gli elementi di amministrazione, ad esempio i piani e alle offerte. Questi elementi sono non funzionale agli utenti.



#### <a name="compute"></a>Calcolo
- Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile utilizzare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario usare il `-Name` parametro anziché `-VMScaleSetName`.

- Quando si crea un set di disponibilità in del portale, passare a **New** > **calcolo** > **set di disponibilità**, è possibile creare solo un set di disponibilità con un dominio di errore e dominio di aggiornamento pari a 1. Creare il set tramite PowerShell, CLI, o dall'interno di disponibilità come soluzione alternativa, quando si crea una nuova macchina virtuale, il portale.

- Quando si creano macchine virtuali nel portale per gli utenti dello Stack di Azure, il portale consente di visualizzare un numero errato di dischi dati che è possibile collegare a una VM di serie D. Tutte le serie D supportate macchine virtuali possono contenere tutti i dischi di dati come la configurazione di Azure.

- Quando un'immagine di macchina virtuale ha esito negativo deve essere creato, un elemento non riuscito che è possibile eliminare potrebbe essere aggiunti al pannello di calcolo immagini della macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo deve risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarla.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale precedentemente non riuscito.

-  Se il provisioning di un'estensione in una distribuzione della macchina virtuale è troppo lunga, gli utenti devono consentire il timeout provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 --> Diagnostica Linux VM non è supportato nello Stack di Azure. Quando si distribuisce una VM Linux con diagnostica di macchina virtuale abilitata, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilita la metrica di base VM Linux tramite le impostazioni di diagnostica.  


#### <a name="networking"></a>Rete
- Dopo che una macchina virtuale viene creata e associata a un indirizzo IP pubblico, è non è possibile eliminare l'associazione di tale macchina virtuale da tale indirizzo IP. Disassociazione sembra funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato alla macchina virtuale originale.

  Attualmente, è necessario utilizzare solo indirizzi IP pubblici nuovo per nuove macchine virtuali create.

  Questo comportamento si verifica anche se si riassegna l'indirizzo IP da una nuova macchina virtuale (conosciuta come un *scambio VIP*). Tutti i successivi tenta di connettersi tramite il risultato di indirizzi IP in una connessione alla macchina virtuale originariamente associata e non a quello nuovo.



- Stack di Azure supporta una singola *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni di tenant. Dopo la creazione della prima rete locale gateway connessione, successiva tentativi di creare una risorsa di gateway di rete locale con lo stesso indirizzo IP vengono bloccate.

- In una rete virtuale che è stato creato con un'impostazione del Server DNS *automatico*, la modifica di guasto a un Server DNS personalizzato. Le impostazioni aggiornate non vengono inserite da macchine virtuali in tale rete virtuale.

- Stack di Azure non supporta l'aggiunta di interfacce di rete aggiuntiva a un'istanza di macchina virtuale dopo aver distribuita la macchina virtuale. Se la macchina virtuale richiede più di un'interfaccia di rete, devono essere definiti in fase di distribuzione.

- <!-- 2096388 --> È possibile utilizzare il portale di amministrazione per aggiornare le regole per un gruppo di sicurezza di rete. 

    Soluzione alternativa per il servizio App: se è necessario per desktop remoto alle istanze del Controller, si modificano le regole di sicurezza all'interno dei gruppi di sicurezza di rete con PowerShell.  Di seguito sono esempi di come *consentire*e quindi ripristinare la configurazione per *negare*:  
    
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

    - *Nega:*

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
- Prima di procedere, rivedere la nota importante [prima di iniziare](#before-you-begin) all'inizio di queste note sulla versione.

- Può richiedere fino a un'ora prima che gli utenti possono creare database in una nuova distribuzione di SQL o MySQL.

- È supportato solo il provider di risorse per creare gli elementi nei server di tale host SQL o MySQL. Gli elementi creati in un server host che non vengono creati dal provider di risorse potrebbero causare uno stato non corrispondente.  

- <!-- IS, ASDK --> Caratteri speciali, inclusi gli spazi e i periodi di, non sono supportati nel **famiglia** nome quando si crea un'unità SKU per i provider di risorse MySQL e SQL Server.

> [!NOTE]  
> Dopo l'aggiornamento alla 1803 dello Stack di Azure, è possibile continuare a utilizzare i provider di risorse MySQL e SQL distribuito in precedenza.  È consigliabile che aggiornare MySQL e SQL Server quando diventa disponibile una nuova versione. Come Azure Stack, applicare gli aggiornamenti ai provider di risorse MySQL e SQL Server in modo sequenziale.  Ad esempio, se si utilizza versione 1711, applicare prima versione 1712 quindi 1802 e quindi aggiornare a 1803.      
>   
> L'installazione dell'aggiornamento 1803 non influenza l'uso del provider di risorse SQL o MySQL dagli utenti.
> Indipendentemente dalla versione dei provider di risorse che è usare, agli utenti di dati nei database non interessati e rimangono accessibili.    



#### <a name="app-service"></a>Servizio app
- Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- Per applicare la scalabilità orizzontale l'infrastruttura (worker, gestione, i ruoli front-end), è necessario usare PowerShell, come descritto nelle note sulla versione per il calcolo.


#### <a name="usage"></a>Uso  
- Dati misuratore sull'utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* stamp che indica quando è stato creato il record. Questi dati non è attualmente utilizzati per eseguire accounting accurata di utilizzo degli indirizzi IP pubblico.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Download degli strumenti di Azure Stack da GitHub
- Quando si utilizza il *invoke-webrequest* degli strumenti cmdlet di PowerShell per scaricare lo Stack di Azure da Github, viene visualizzato un errore:     
    -  *Invoke-webrequest: la richiesta è stata interrotta: non è possibile creare canale sicuro SSL/TLS.*     

  Questo errore si verifica a causa di una recente deprecazione di supporto GitHub degli Tlsv1 e Tlsv1.1 crittografia standard (impostazione predefinita per PowerShell). Per altre informazioni, vedere [si noti la rimozione di standard di crittografia debole](https://githubengineering.com/crypto-removal-notice/).

  Per risolvere questo problema, aggiungere `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` all'inizio dello script per forzare la console di PowerShell da utilizzare TLSv1.2 durante il download da repository GitHub.


## <a name="download-the-update"></a>Scaricare l'aggiornamento
È possibile scaricare il pacchetto di aggiornamento di Azure Stack 1803 dal [qui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Vedere anche 
- Per usare il punto finale con privilegi (PEP) per monitorare e riprendere gli aggiornamenti, vedere [monitorare gli aggiornamenti nello Stack di Azure utilizzando l'endpoint con privilegi](azure-stack-monitor-update.md).
- Per una panoramica del processo di gestione nello Stack di Azure, vedere [gestire gli aggiornamenti in panoramica di Azure Stack](azure-stack-updates.md).
- Per ulteriori informazioni su come applicare gli aggiornamenti con lo Stack di Azure, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).
