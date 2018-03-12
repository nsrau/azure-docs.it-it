---
title: Aggiornamento di Azure Stack 1802 | Documenti Microsoft
description: "Informazioni sulle novità nell'aggiornamento 1802 per Azure Stack integrate di sistemi, i problemi noti e come scaricare l'aggiornamento."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 247f13717971d3660b3ec0ee94821bd593c5fed0
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="azure-stack-1802-update"></a>Aggiornamento dello Stack 1802 Azure

*Si applica a: Azure Stack integrate di sistemi*

In questo articolo vengono descritti i miglioramenti e correzioni nel pacchetto di aggiornamento 1802, conosciuti per questa versione e come scaricare l'aggiornamento. Problemi noti sono suddivisi in problemi direttamente correlati al processo di aggiornamento e con la compilazione (post-installazione).

> [!IMPORTANT]        
> Questo pacchetto di aggiornamento è solo per i sistemi Azure Stack integrato. Questo pacchetto di aggiornamento non si applicano al Kit di sviluppo dello Stack di Azure.

## <a name="build-reference"></a>Compilazione di riferimento    
È il numero di build di aggiornamento di Azure Stack 1802 **20180302.1**.  


## <a name="before-you-begin"></a>Prima di iniziare    
> [!IMPORTANT]    
> Non tentare di creare macchine virtuali durante l'installazione dell'aggiornamento. Per ulteriori informazioni sulla gestione degli aggiornamenti, vedere [gestire gli aggiornamenti in panoramica di Azure Stack](/azure-stack-updates#plan-for-updates).


### <a name="prerequisites"></a>Prerequisiti
- Installare lo Stack di Azure [1712 aggiornare](azure-stack-update-1712.md) prima di applicare l'aggiornamento di Azure Stack 1802.    

- Installare **AzS Hotfix – 1.0.180312.1-compilare 20180222.2** prima di applicare l'aggiornamento di Azure Stack 1802. Questo aggiornamento rapido gli aggiornamenti di Windows Defender ed è disponibile quando si scaricano gli aggiornamenti per lo Stack di Azure.

  Per installare l'aggiornamento rapido, seguire le procedure normale per [l'installazione degli aggiornamenti per Azure Stack](azure-stack-apply-updates.md). Il nome dell'aggiornamento viene visualizzato come **AzS Hotfix: 1.0.180312.1**e include i file seguenti: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Dopo aver caricato i file a un account di archiviazione e contenitore, eseguire l'installazione dal riquadro aggiornamento nel portale di amministrazione. 
  
  A differenza degli aggiornamenti allo Stack di Azure, installare questo aggiornamento non modifica la versione dello Stack di Azure.  Per confermare l'installazione di questo aggiornamento, visualizzare l'elenco di **aggiornamenti installati**.
 


### <a name="post-update-steps"></a>Passaggi di post-aggiornamento
*Non sono passaggi post-aggiornamento per aggiornamento 1802.*


### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni
Questo aggiornamento include i seguenti miglioramenti e correzioni per lo Stack di Azure.

- **Viene aggiunto il supporto per le seguenti versioni di API del servizio di archiviazione Azure**:
    - 2017-04-17 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    Per ulteriori informazioni, vedere [dello Stack di archiviazione di Azure: considerazioni e le differenze](/azure/azure-stack/user/azure-stack-acs-differences).

- **Supporto per più grande [BLOB in blocchi](azure-stack-acs-differences.md)**:
    - Le dimensioni massime consentite del blocco sono aumentata da 4 MB a 100 MB.
    - Le dimensioni massime del blob viene aumentata da 195 GB a 4,75 TB.  

- **Backup di infrastruttura** ora viene visualizzato nel riquadro del provider di risorse e avvisi per i backup sono abilitati. Per ulteriori informazioni sul servizio di Backup di infrastruttura, vedere [Backup e ripristino dei dati per lo Stack di Azure con il servizio di Backup di infrastruttura](azure-stack-backup-infrastructure-backup.md).

- **Aggiornare il *Test AzureStack* cmdlet** per migliorare la diagnostica per l'archiviazione. Per ulteriori informazioni su questo cmdlet, vedere [convalida per Azure Stack](azure-stack-diagnostic-test.md).

- **Miglioramenti di controllo di accesso (RBAC) basata su ruoli** -è ora possibile usare RBAC per delegare le autorizzazioni per i gruppi universali utente quando viene distribuito Azure Stack con AD FS. Per ulteriori informazioni sui ruoli, vedere [gestire RBAC](azure-stack-manage-permissions.md).

- **Viene aggiunto il supporto per più domini di errore**.  Per ulteriori informazioni, vedere [la disponibilità elevata per Azure Stack](azure-stack-key-features.md#high-availability-for-azure-stack).

- **Varie correzioni** per le prestazioni, stabilità, sicurezza e il sistema operativo che viene utilizzato dallo stack di Azure.

<!--
#### New features
-->


<!--
#### Fixes
-->


### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento    
*Non siano presenti problemi noti per l'installazione dell'aggiornamento 1802.*


### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)
Di seguito sono problemi noti di post-installazione per la compilazione **20180302.1**

#### <a name="portal"></a>Portale
- Il possibilità [per aprire una nuova richiesta di supporto nell'elenco a discesa](azure-stack-manage-portals.md#quick-access-to-help-and-support) all'interno di amministratore del portale non è disponibile. In alternativa, usare il collegamento seguente:     
    - Per lo Stack di Azure sistemi integrati, utilizzare https://aka.ms/newsupportrequest.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- Che non sia possibile visualizzare le risorse di calcolo o di archiviazione nel portale di amministrazione. La causa del problema è un errore durante l'installazione dell'aggiornamento che causa l'aggiornamento da segnalare in modo non corretto come completata correttamente. Se si verifica questo problema, contattare il supporto tecnico clienti Microsoft per assistenza.

- È possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, selezionare l'icona dell'ingranaggio in alto a destra del portale e quindi selezionare **ripristinare le impostazioni predefinite**.

- Quando si visualizzano le proprietà di una risorsa o un gruppo di risorse, il **spostare** pulsante è disabilitato. Questo comportamento è previsto. Lo spostamento di risorse o gruppi di risorse tra gruppi di risorse o le sottoscrizioni non è attualmente supportato.

- Se si elimina utente sottoscrizioni nelle risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- È possibile visualizzare le autorizzazioni per la sottoscrizione utilizzando i portali di Stack di Azure. In alternativa, utilizzare PowerShell per verificare le autorizzazioni.

- Nel dashboard del portale di amministrazione, il riquadro di aggiornamento non riesce a visualizzare informazioni sugli aggiornamenti. Per risolvere questo problema, fare clic sul riquadro per aggiornarla.

-   Nel portale di amministrazione è possibile visualizzare un avviso critico per il componente Microsoft.Update.Admin. Il nome dell'avviso, la descrizione e correzione tutti visualizzati come:  
    - *Errore: modello per FaultType ResourceProviderTimeout è manca.*

    Questo avviso può essere tranquillamente ignorato. 

#### <a name="health-and-monitoring"></a>Monitoraggio dell'integrità e
Non siano presenti problemi noti dopo l'aggiornamento a 1802.

#### <a name="marketplace"></a>Marketplace
- Gli utenti di sfogliare il marketplace completo senza una sottoscrizione e possono vedere gli elementi di amministrazione come piani e le offerte. Questi elementi sono non funzionale agli utenti.

#### <a name="compute"></a>Calcolo
- Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile utilizzare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario utilizzare il `-Name` parametro anziché `-VMScaleSetName`.

- Stack di Azure supporta l'utilizzo solo dischi rigidi virtuali di tipo fisso. Alcune immagini offerti tramite il marketplace nello Stack di Azure usano dischi rigidi virtuali dinamici, ma quelli sono state rimosse. Ridimensionamento di una macchina virtuale (VM) con un disco dinamico collegato lascia la macchina virtuale in uno stato di errore.

  Per evitare questo problema, eliminare la macchina virtuale senza eliminare disco della macchina virtuale, un blob del disco rigido virtuale in un account di archiviazione. Quindi convertire il disco rigido virtuale da un disco dinamico in un disco fisso e quindi ricreare la macchina virtuale.

- Quando si crea un set di disponibilità in del portale, passare a **New** > **calcolo** > **set di disponibilità**, è possibile creare solo un set di disponibilità con un dominio di errore e dominio di aggiornamento 1. In alternativa, quando si crea una nuova macchina virtuale, creare il set tramite l'interfaccia CLI, PowerShell o dall'interno di disponibilità il portale.

- Quando si creano macchine virtuali nel portale per gli utenti dello Stack di Azure, il portale consente di visualizzare un numero errato di dischi dati che è possibile collegare a una VM di serie DS. Macchine virtuali di serie DS possono contenere tutti i dischi di dati come la configurazione di Azure.

- Quando non è stato possibile creare un'immagine di macchina virtuale, un elemento non riuscito che è possibile eliminare potrebbe essere aggiunti al pannello di calcolo di immagini della macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo deve risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale che precedentemente non riuscito.

-  Se il provisioning di un'estensione in una distribuzione di macchina virtuale è troppo lunga, gli utenti devono consentire il timeout di provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  




#### <a name="networking"></a>Rete
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
 - Prima di procedere, rivedere la nota importante in [prima di iniziare](#before-you-begin) all'inizio di queste note sulla versione.
- Può richiedere fino a un'ora prima che gli utenti possono creare database in una nuova distribuzione di SQL o MySQL.

- Per creare gli elementi nei server di tale host SQL o MySQL, è supportato solo il provider di risorse. Gli elementi creati in un server host che non vengono creati dal provider di risorse potrebbe essere in uno stato non corrispondente.  


> [!NOTE]  
> Dopo l'aggiornamento alla 1802 dello Stack di Azure, è possibile continuare a utilizzare i provider di risorse MySQL e SQL distribuito in precedenza.  È consigliabile che aggiornare MySQL e SQL Server quando diventa disponibile una nuova versione. Come Stack di Azure, applicare gli aggiornamenti in sequenza per i provider di risorse MySQL e SQL Server.  Ad esempio, se si utilizza una versione 1710, applicare prima versione 1711 quindi 1712 e quindi aggiornare a 1802.      
>   
> L'installazione dell'aggiornamento 1802 non influenza l'uso del provider di risorse SQL o MySQL dagli utenti.
> Indipendentemente dalla versione dei provider di risorse a cui che si utilizza, agli utenti di dati nei database non sono interessati e rimangono accessibili.    


#### <a name="app-service"></a>Servizio app
- Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- Per applicare la scalabilità orizzontale l'infrastruttura (Gestione processi di lavoro, i ruoli front-end), è necessario usare PowerShell, come descritto nelle note sulla versione per il calcolo.

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Download degli strumenti di Azure Stack da GitHub
- Quando si utilizza il *invoke-webrequest* da Github degli strumenti cmdlet di PowerShell per scaricare lo Stack di Azure, viene visualizzato un errore:     
    -  *Invoke-webrequest: la richiesta è stata interrotta: Impossibile creare un canale protetto SSL/TLS.*     

  Questo errore si verifica a causa di un recente deprecazione di supporto di GitHub degli Tlsv1 e Tlsv1.1 crittografia standard (impostazione predefinita per PowerShell). Per ulteriori informazioni, vedere [si noti la rimozione di standard di crittografia debole](https://githubengineering.com/crypto-removal-notice/).

  Per risolvere questo problema, aggiungere `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` all'inizio dello script per forzare la console di PowerShell per utilizzare TLSv1.2 durante il download dal repository di GitHub.


## <a name="download-the-update"></a>Scaricare l'aggiornamento
È possibile scaricare il pacchetto di aggiornamento da Azure Stack 1802 [qui](https://aka.ms/azurestackupdatedownload).


## <a name="more-information"></a>Altre informazioni
Microsoft ha fornito un modo per monitorare e riprendere gli aggiornamenti utilizzando i privilegi punto finale (PEP) installati con aggiornamento 1710.

- Vedere il [monitorare gli aggiornamenti nello Stack di Azure utilizzando la documentazione di endpoint con privilegi](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update).

## <a name="see-also"></a>Vedere anche 

- Per una panoramica del processo di gestione nello Stack di Azure, vedere [gestire gli aggiornamenti in panoramica di Azure Stack](azure-stack-updates.md).
- Per ulteriori informazioni su come applicare gli aggiornamenti con lo Stack di Azure, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).
