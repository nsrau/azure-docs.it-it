---
title: Note sulla versione di Microsoft Azure Stack Development Kit | Microsoft Docs
description: I miglioramenti, correzioni e problemi noti per Azure Stack Development Kit.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
git ms.author: sethm
ms.reviewer: misainat
ms.openlocfilehash: ad596e2f63d3ef5fb82fe52699fef3a79e835274
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634813"
---
# <a name="azure-stack-development-kit-release-notes"></a>Note sulla versione di Azure Stack Development Kit  
Questo articolo fornisce informazioni sui miglioramenti e correzioni di problemi noti in Azure Stack Development Kit. Se non si conosce quale versione in esecuzione, è possibile [usare il portale per controllare](.\.\azure-stack-updates.md#determine-the-current-version).

> Rimanere aggiornati sulle novità di ASDK sottoscrivendo il [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11808097"></a>Compilazione 1.1808.0.97

### <a name="new-features"></a>Nuove funzionalità
Questa build include i seguenti miglioramenti e correzioni per Azure Stack.  

- <!-- 2682594   | ASDK  -->   **Tutti gli ambienti Azure Stack, ora, usare il formato di fuso orario Coordinated Universal Time (UTC).**  Tutti i dati di log e informazioni correlate a questo punto viene visualizzato in formato UTC. 

- <!-- 2437250  | IS  ASDK --> **Sono supportati i dischi gestiti.** È ora possibile usare Managed Disks in macchine virtuali di Azure Stack e set di scalabilità di macchine virtuali. Per altre informazioni, vedere [Managed Disks di Azure Stack: differenze e considerazioni](/azure/azure-stack/user/azure-stack-managed-disk-considerations).
 
- <!-- 2563799  | IS  ASDK -->  **Monitoraggio di Azure**. Ad esempio monitoraggio di Azure in Azure, monitoraggio di Azure in Azure Stack offre log e metriche dell'infrastruttura di livello di base per la maggior parte dei servizi. Per altre informazioni, vedere [monitoraggio di Azure in Azure Stack](/azure/azure-stack/user/azure-stack-metrics-azure-data).

- <!-- ASDK --> **Gli elementi della raccolta per il set di scalabilità di macchine virtuali sono ora incorporati**.  Elementi della raccolta di Set di scalabilità di macchine virtuali sono ora reso disponibili nei portali di utenti e amministratori senza la necessità di eseguirne il download. 

- <!-- IS, ASDK --> **Set di scalabilità di macchine virtuali ridimensionamento**.  È possibile usare il portale per [scalare un Set di scalabilità di macchine virtuali](/azure/azure-stack/azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).   

- <!-- 2489570 | IS ASDK--> **Supporto per le configurazioni di criteri IPSec/IKE personalizzati** per [i gateway VPN di Azure Stack](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).

- <!-- | IS ASDK--> **Elemento del marketplace Kubernetes**. È ora possibile distribuire cluster Kubernetes usando il [elemento del Marketplace di Kubernetes](/azure/azure-stack/azure-stack-solution-template-kubernetes-cluster-add). Gli utenti possono selezionare l'elemento di Kubernetes e inserire alcuni parametri per distribuire un cluster Kubernetes in Azure Stack. Lo scopo dei modelli è renderlo semplice per gli utenti alle distribuzioni di Kubernetes installazione sviluppo/test in pochi passaggi.

- <!-- | IS ASDK--> **I modelli di Blockchain**. È ora possibile eseguire [distribuzioni consortium Ethereum](/azure/azure-stack/azure-stack-ethereum) in Azure Stack. È possibile trovare tre nuovi modelli nel [i modelli di avvio rapido di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates). Consentono all'utente di distribuire e configurare una rete Ethereum multimembro consorzio con una minima conoscenza di Azure ed Ethereum. Lo scopo dei modelli è renderlo semplice per gli utenti alle distribuzioni di Blockchain installazione sviluppo/test in pochi passaggi.


### <a name="fixed-issues"></a>Problemi risolti
- <!-- IS ASDK--> È stato risolto il problema per la creazione di un set di disponibilità nel portale e ciò ha provocato il set che presenta un dominio di errore e dominio di aggiornamento 1.

- <!-- IS ASDK --> Le impostazioni di scalabilità a set di scalabilità di macchine virtuali sono ora disponibili nel portale.  

- <!-- 2494144- IS, ASDK --> È stato risolto il problema che impediva che viene visualizzato quando la selezione di una dimensione di VM per la distribuzione di alcune dimensioni delle macchine virtuali serie F. 

- <!-- IS, ASDK --> Miglioramenti delle prestazioni durante la creazione di macchine virtuali e altro ancora ottimizzato l'uso di archiviazione sottostante.

- **Varie correzioni di** per le prestazioni, stabilità, sicurezza e il sistema operativo che viene usato da Azure Stack


### <a name="changes"></a>Modifiche
- <!-- 1697698  | IS, ASDK --> *Esercitazioni introduttive* nel collegamento utente del dashboard del portale ora articoli pertinenti nella documentazione online di Azure Stack.

- <!-- 2515955   | IS ,ASDK--> *Tutti i servizi* sostituisce *altri servizi* nei portali di amministratore e utente di Azure Stack. È ora possibile usare *tutti i servizi* alternativa per esplorare i portali di Azure Stack gli esattamente come nei portali di Azure.

- <!-- TBD | IS, ASDK --> *+ Crea una risorsa* sostituisce *+ nuovo* nei portali di amministratore e utente di Azure Stack.  È ora possibile usare *+ crea una risorsa* alternativa per esplorare i portali di Azure Stack gli esattamente come nei portali di Azure. 

- <!--  TBD – IS, ASDK --> *Una base* dimensioni delle macchine virtuali sono in pensione per [creazione di set di scalabilità di macchine virtuali](.\.\azure-stack-compute-add-scalesets.md) (VMSS) tramite il portale. Per creare un set di scalabilità con queste dimensioni, usare PowerShell o un modello. 

### <a name="known-issues"></a>Problemi noti

#### <a name="portal"></a>Portale  
- <!-- 2967387 – IS, ASDK --> L'account usato per accedere al portale di amministratore o utente di Azure Stack viene visualizzato come **utente non identificato**. Ciò si verifica quando l'account non dispone di uno una *primo* oppure *ultima* nome specificato. Per risolvere questo problema, modificare l'account utente per fornire il primo o ultimo nome. È necessario quindi disconnettersi e quindi accedere al portale. 

-  <!--  2873083 - IS ASDK --> Quando si usa il portale per creare un set di scalabilità di macchine virtuali (VMSS), di impostare il *dimensioni istanze* elenco a discesa non viene caricato correttamente quando si usa Internet Explorer. Per risolvere questo problema, usare un altro browser quando si usa il portale per creare un set di scalabilità.  

- <!-- TBD  ASDK --> Il fuso orario predefinito per tutte le distribuzioni di Azure Stack sono ora impostati per Coordinated Universal Time (UTC). È possibile selezionare un fuso orario durante l'installazione di Azure Stack, tuttavia, la raccomandazione Annulla automaticamente all'ora UTC come impostazione predefinita durante l'installazione.

- <!-- 2931230 – IS  ASDK --> Impossibile eliminare i piani che vengono aggiunti a una sottoscrizione utente come un piano del componente aggiuntivo, anche quando si rimuove il piano dalla sottoscrizione utente. Il piano rimarrà fino a quando non vengono eliminate anche le sottoscrizioni che fanno riferimento il piano aggiuntivo. 

- <!--2760466 – IS  ASDK --> Quando si installa un nuovo ambiente di Azure Stack che esegue questa versione, l'avviso che indica *attivazione obbligatoria* potrebbe non essere visualizzato. [Attivazione](.\.\azure-stack-registration.md) è necessario prima di poter usare diffusione di marketplace. 

- <!-- TBD - IS ASDK --> I due tipi di sottoscrizione amministrativo che erano [introdotta con la versione 1804](.\.\azure-stack-update-1804.md#new-features) non deve essere utilizzato. I tipi di sottoscrizione siano **sottoscrizione di misurazione**, e **sottoscrizione consumo**. Questi tipi di sottoscrizione vengono **sottoscrizione di misurazione**, e **sottoscrizione consumo**. Questi tipi di sottoscrizione sono visibili in nuovi ambienti Azure Stack partire dalla versione 1804 ma non sono ancora pronti per l'uso. È consigliabile continuare a usare il **sottoscrizione del Provider predefinito** tipo.

- <!-- TBD -  IS ASDK --> Eliminazione utente diverso, le sottoscrizioni le risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- <!-- TBD -  IS ASDK --> Non è possibile visualizzare le autorizzazioni per la sottoscrizione tramite il portale di Azure Stack. In alternativa, usare PowerShell per verificare le autorizzazioni.



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

  Entrambi gli avvisi possono essere tranquillamente ignorati e si chiudono automaticamente nel corso del tempo.  

- <!-- 2368581 - IS. ASDK --> Un operatore di Azure Stack, se si riceve un avviso di memoria insufficiente e le macchine virtuali tenant non riuscire a distribuire con un *errore di creazione della macchina virtuale di Fabric*, è possibile che l'indicatore di Azure Stack ha esaurito la memoria disponibile. Usare la [Capacity Planner di Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) per comprendere meglio la capacità disponibile per i carichi di lavoro.


#### <a name="compute"></a>Calcolo  
- <!-- 2869209 – IS, ASDK --> Quando si usa la [ **Add-AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), è necessario usare il **- OsUri** parametro come URI in cui è stato caricato il disco dell'account di archiviazione. Se si usa il percorso locale del disco, il cmdlet non riesce con l'errore seguente: *operazione a esecuzione prolungata non è riuscita con stato 'Non riuscita'*. 

- <!--  2966665 – IS, ASDK --> Collegamento di dischi dati unità SSD alle dimensioni premium gestiti a dischi delle macchine virtuali (DS, DSv2, Fs, Fs_V2) ha esito negativo con un errore: *non è stato possibile aggiornare i dischi della macchina virtuale 'vmname' errore: non è possibile eseguire l'operazione perché il tipo di account di archiviazione richiesta ' Premium_LRS' non è supportata per dimensioni della macchina virtuale ' Standard_DS/Ds_V2/ADFS/Fs_v2)*

   Per risolvere questo problema, usare *Standard_LRS* dischi dati anziché *dischi Premium_LRS*. Sfrutta *Standard_LRS* dischi dati non cambia IOPs o i costi di fatturazione.  

- <!--  2795678 – IS, ASDK --> Quando si usa il portale per creare macchine virtuali (VM) in una dimensione di VM premium (DS, Ds_v2, ADFS, FSv2), viene creata la macchina virtuale in un account di archiviazione standard. La creazione di un account di archiviazione standard non influisce sul livello funzionale, numero di IOPs, o fatturazione. 

   È possibile ignorare l'avviso con la dicitura: *si è scelto di usare un disco standard in una dimensione che supporta i dischi premium. Ciò potrebbe influire sulle prestazioni del sistema operativo e non è consigliato. Provare a utilizzare archiviazione premium (SSD).*

- <!-- 2967447 - IS, ASDK --> Esperienza di creazione di set di scalabilità di macchine virtuali (VMSS) fornisce basata su CentOS 7.2 come un'opzione per la distribuzione. Poiché tale immagine non è disponibile in Azure Stack, selezionare un altro sistema operativo per la distribuzione o usare un modello ARM che specifica un'altra immagine CentOS scaricata prima della distribuzione dal marketplace dall'operatore.

- <!-- TBD -  IS ASDK --> Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile usare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario usare il `-Name` parametro anziché `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Quando si creano macchine virtuali nel portale utenti Azure Stack, il portale Visualizza un numero errato di dischi dati che possono collegare a una VM serie D. Serie D supportate tutte le macchine virtuali è possono soddisfare tutti i dischi dati come la configurazione di Azure.

- <!-- TBD -  IS ASDK --> Quando un'immagine di macchina virtuale ha esito negativo deve essere creato, un elemento non riuscito che non è possibile eliminare potrebbe essere aggiunto al pannello calcolo immagini di macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo dovrebbe risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile riprovare a eseguire il download dell'immagine di macchina virtuale che in precedenza non è riuscita.

- <!-- TBD -  IS ASDK --> Se il provisioning di un'estensione in una distribuzione della VM impiega troppo tempo, gli utenti devono consentire il timeout provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 - IS ASDK --> Diagnostica delle VM di Linux non è supportata in Azure Stack. Quando si distribuisce una VM Linux con abilitata la diagnostica della macchina virtuale, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilitano le metriche di base della VM Linux tramite le impostazioni di diagnostica.

- <!-- 2724961- IS ASDK --> Quando si registra il **Microsoft.Insight** provider di risorse nelle impostazioni di sottoscrizione e creare una VM Windows con Guest OS diagnostica è abilitata, il grafico della percentuale di CPU nella pagina di panoramica della macchina virtuale non sarà in grado di mostrare i dati di metrica.
 
  Per trovare il grafico della percentuale di CPU per la macchina virtuale, andare alla **metriche** pannello e Mostra tutte le macchine Virtuali di Windows supportate guest metriche.

 

#### <a name="networking"></a>Rete
- <!-- 1766332 - IS, ASDK --> Sotto **Networking**, se si fa clic su **creare Gateway VPN** per configurare una connessione VPN, **basata su criteri** viene elencato come un tipo di VPN. Non selezionare questa opzione. Solo le **basato su Route** opzione è supportata in Azure Stack.

- <!-- 1902460 -  IS ASDK --> Azure Stack supporta un'unica *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni tenant. Dopo la creazione della prima rete locale connessione gateway, successivi tentativi di creare una risorsa gateway di rete locale con lo stesso indirizzo IP vengono bloccate.

- <!-- 16309153 -  IS ASDK --> In una rete virtuale che è stato creato con l'impostazione Server DNS *automatica*, la modifica di un errore di Server DNS personalizzato. Le impostazioni aggiornate non vengono inviate alle macchine virtuali in tale rete virtuale.

- <!-- 2702741 -  IS ASDK --> Non è garantito che gli indirizzi IP pubblici che vengono distribuiti tramite il metodo di allocazione dinamica mantenute dopo l'esecuzione di un arresto-deallocazione.

- <!-- 2529607 - IS ASDK --> Durante l'Azure Stack *rotazione segreta*, è previsto un periodo in cui gli indirizzi IP pubblici non sono raggiungibili da due a cinque minuti.

-   <!-- 2664148 - IS ASDK --> Negli scenari in cui il tenant è l'accesso alle proprie macchine virtuali tramite un tunnel VPN S2S, si può verificare uno scenario in cui i tentativi di connessione non riuscire se la subnet locale è stata aggiunta al Gateway di rete locale dopo gateway è già stato creato. 


<!--  #### SQL and MySQL  -->


#### <a name="app-service"></a>Servizio app
- <!-- 2352906 - IS ASDK --> Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- <!-- TBD - IS ASDK --> Per ridimensionare l'infrastruttura (ruoli di lavoro, gestione, i ruoli front-end), è necessario usare PowerShell come descritto nelle note sulla versione per il calcolo.  


#### <a name="usage"></a>Uso  
- <!-- TBD -  IS ASDK --> Dati misuratore utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* timestamp indicante quando il record è stato creato. Attualmente, è possibile usare questi dati per eseguire l'accounting accurato dell'utilizzo di indirizzi IP pubblico.

<!-- #### Identity -->




## <a name="build-11807076"></a>Compilazione 1.1807.0.76

### <a name="new-features"></a>Nuove funzionalità
Questa build include i seguenti miglioramenti e correzioni per Azure Stack.  

- <!-- 1658937 | ASDK, IS --> **Avviare i backup in una pianificazione predefinita** -come appliance, Azure Stack possono ora attiva automaticamente i backup infrastructure periodicamente per evitare l'intervento dell'utente. Azure Stack automaticamente anche consentirà di pulire la condivisione esterna per i backup precedenti rispetto al periodo di memorizzazione definito. Per altre informazioni, vedere [abilitare il Backup per Azure Stack con PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).

- <!-- 2496385 | ASDK, IS -->  **Nel tempo di backup totale tempo di trasferimento dei dati aggiunti.** Per altre informazioni, vedere [abilitare il Backup per Azure Stack con PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).

-   <!-- 1702130 | ASDK, IS -->  **Backup esterna capacità Mostra ora la capacità corretta della condivisione esterna.** (In precedenza questo era hardcoded a 10 GB.) Per altre informazioni, vedere [abilitare il Backup per Azure Stack con PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).
 
- <!-- 2753130 |  IS, ASDK   -->  **Modelli di Resource Manager di Azure supportano ora l'elemento di condizione** -è ora possibile distribuire una risorsa in un modello di gestione risorse di Azure usando una condizione. È possibile progettare il modello per distribuire una risorsa basata su una condizione, ad esempio determinare se è presente un valore di parametro. Per informazioni sull'utilizzo di un modello come una condizione, vedere [distribuire in modo condizionale una risorsa](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) e [sezione Variables dei modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) nella documentazione di Azure. 

   È anche possibile usare i modelli per [distribuire risorse in più di una sottoscrizione o gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

- <!--2753073 | IS, ASDK -->  **È stato aggiornato il supporto della versione API di Microsoft. Network resource** per includere il supporto per l'API versione 2017-10-01 da 2015-06-15 per le risorse di rete di Azure Stack.  Supporto per le versioni di risorse tra 15-06-2015 e 2017-10-01 non è incluso in questa versione, ma verrà incluso in una versione futura.  Consultare [considerazioni per la rete di Azure Stack](.\.\user\azure-stack-network-differences.md) le differenze di funzionalità.

- <!-- 2272116 | IS, ASDK   -->  **Azure Stack ha aggiunto il supporto per le ricerche DNS inverse per con connessione esterna endpoint dell'infrastruttura di Azure Stack** (che è per portale, adminportal, gestione e adminmanagement). In questo modo i nomi degli endpoint esterni di Azure Stack essere risolto da un indirizzo IP.

- <!-- 2780899 |  IS, ASDK   --> **Azure Stack supporta ora l'aggiunta di interfacce di rete aggiuntiva in una VM esistente.**  Questa funzionalità è disponibile tramite il portale, PowerShell e CLI. Per altre informazioni, vedere [aggiungere o rimuovere interfacce di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) nella documentazione di Azure. 

- <!-- 2222444 | IS, ASDK   -->  **Sono stati apportati miglioramenti nell'accuratezza e la resilienza per i contatori di utilizzo di rete**. Contatori di utilizzo di rete sono ora più accurati e prendono in sottoscrizioni account sospeso, i periodi di interruzione e le race condition.

- <!-- 2297790 | IS, ASDK -->  **Miglioramenti al client di syslog (funzionalità di anteprima) di Azure Stack**. Questa consente l'inoltro di controllo e i log relativi all'infrastruttura di Azure Stack a un syslog server o della sicurezza le informazioni ed eventi (SIEM) software di gestione esterno ad Azure Stack. Il client di syslog supporta ora il protocollo TCP con testo normale o la crittografia TLS 1.2, il secondo è la configurazione predefinita. È possibile configurare la connessione TLS con l'autenticazione solo server o reciproco.

  Per configurare il modo in cui il client di syslog comunica (ad esempio protocollo, la crittografia e autenticazione) con il server syslog, usare il cmdlet Set-SyslogServer. Questo cmdlet è disponibile dall'endpoint con privilegi (PEP). 

  Per aggiungere il certificato sul lato client per l'autenticazione reciproca del client TLS 1.2 syslog, usare il cmdlet Set-SyslogClient nel PEP.

  Con questa versione di anteprima, è possibile visualizzare un numero molto maggiore di controlli e avvisi. 

  Poiché questa funzionalità è ancora in anteprima, non affidarsi a essa in ambienti di produzione.

  Per altre informazioni, vedere [l'inoltro di syslog di Azure Stack](.\.\azure-stack-integrate-security.md).

- <!-- ####### | IS, ASDK -->  **Azure Resource Manager include il nome dell'area.** Con questa versione, gli oggetti recuperati da Azure Resource Manager include ora l'attributo del nome di area. Se uno script di PowerShell esistente, l'oggetto viene passato direttamente a un altro cmdlet, lo script potrebbe produrre un errore e non riuscire. Questo è il comportamento conforme di Azure Resource Manager e richiede che il client chiama da cui sottrarre l'attributo di area. Per altre informazioni su Azure Resource Manager vedere [documentazione di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

- <!-- TBD | IS, ASDK -->  **Spostare sottoscrizioni tra i provider di delegato.** È ora possibile spostare le sottoscrizioni tra sottoscrizioni del Provider delegata nuove o esistenti che appartengono allo stesso tenant di Directory. È inoltre possibile spostare le sottoscrizioni appartenenti alla sottoscrizione Provider predefinito per le sottoscrizioni del Provider delegata nello stesso tenant di Directory. Per altre informazioni, vedere [delegare offerte in Azure Stack](.\.\azure-stack-delegated-provider.md).
 
- <!-- 2536808 IS ASDK --> **Migliorata l'ora di creazione della macchina virtuale** per le macchine virtuali che vengono create con immagini Scarica dal marketplace di Azure.

### <a name="fixed-issues"></a>Problemi risolti

- <!-- TBD | ASDK, IS --> Il processo di aggiornamento sono stati apportati miglioramenti vari per renderlo più affidabile. Inoltre, le correzioni sono state apportate all'infrastruttura sottostante, che migliora svuotamento del nodo, riducendo così al minimo eventuali tempi di inattività per i carichi di lavoro durante l'aggiornamento.

-   <!--2292271 | ASDK, IS --> È stato risolto un problema in cui un limite di Quota modificato non riguarda le sottoscrizioni esistenti.  A questo punto, quando si aumenta un limite di Quota per una risorsa di rete che fa parte di un'offerta e piano associato a una sottoscrizione utente, il nuovo limite si applica a sottoscrizioni esistenti, nonché le nuove sottoscrizioni.

- <!-- 2448955 | IS ASDK --> È possibile ora correttamente le query in log attività per i sistemi distribuiti in un fuso orario UTC + N.    

- <!-- 2319627 |  ASDK, IS --> Verifica preliminare per i parametri di configurazione del backup (chiave di crittografia/percorso/nome utente/Password) non è più imposta impostazioni non corrette per la configurazione del backup. (In precedenza, non corretta delle impostazioni sono state impostate nel backup e backup avrà quindi esito negativo quando attivato).

- <!-- 2215948 |  ASDK, IS --> A questo punto l'elenco dei backup viene aggiornato quando si elimina manualmente il backup dalla condivisione esterna.

- <!-- 2360715 |  ASDK, IS -->  Quando si configura integrazione di Data Center, è accedere non è più il file di metadati di AD FS da una condivisione. Per altre informazioni, vedere [impostazione dell'integrazione di AD FS, fornendo il file di metadati di federazione](.\.\azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

- <!-- 2388980 | ASDK, IS --> È stato risolto un problema che ha impedito agli utenti di assegnato un indirizzo IP pubblico esistente che è stata precedentemente assegnato a un'interfaccia di rete o servizio di bilanciamento del carico a una nuova interfaccia di rete o servizio di bilanciamento del carico.  

- <!-- 2551834 - IS, ASDK --> Quando si seleziona *Overview* per un account di archiviazione nei portali di amministratore o utente, il *Essentials* riquadro ora consente di visualizzare tutte le informazioni previste correttamente. 

- <!-- 2551834 - IS, ASDK --> Quando si seleziona *tag* per un account di archiviazione nei portali di amministratore o utente, le informazioni ora visualizza correttamente.

- <!-- TBD - IS ASDK --> Questa versione di Azure Stack consente di correggere il problema che impediva l'applicazione degli aggiornamenti di driver dai pacchetti di estensione OEM.

-   <!-- 2055809- IS ASDK --> È stato risolto un problema che si è autorizzato a eliminare le macchine virtuali nel pannello calcolo quando non viene creata la macchina virtuale.  

- <!--  2643962 IS ASDK -->  Avviso relativo alla *bassa capacità di memoria* non viene più visualizzata in modo non corretto.

- <!--  TBD ASDK --> La macchina virtuale che ospita l'endpoint con privilegi (PEP) è stata aumentata a 4GB. In ASDK, questa macchina virtuale è denominata AzS-ERCS01.

- **Varie correzioni di** per le prestazioni, stabilità, sicurezza e il sistema operativo che viene usato da Azure Stack


<!-- ### Changes  -->
<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Problemi noti

#### <a name="portal"></a>Portale  
- <!-- 2931230 – IS  ASDK --> Impossibile eliminare i piani che vengono aggiunti a una sottoscrizione utente come un piano del componente aggiuntivo, anche quando si rimuove il piano dalla sottoscrizione utente. Il piano rimarrà fino a quando non vengono eliminate anche le sottoscrizioni che fanno riferimento il piano aggiuntivo. 

- <!--2760466 – IS  ASDK --> Quando si installa un nuovo ambiente di Azure Stack che esegue questa versione, l'avviso che indica *attivazione obbligatoria* potrebbe non essere visualizzato. [Attivazione](.\.\azure-stack-registration.md) è necessario prima di poter usare diffusione di marketplace. 

- <!-- TBD - IS ASDK --> I due tipi di sottoscrizione amministrativo che erano [introdotta con la versione 1804](.\.\azure-stack-update-1804.md#new-features) non deve essere utilizzato. I tipi di sottoscrizione siano **sottoscrizione di misurazione**, e **sottoscrizione consumo**. Questi tipi di sottoscrizione vengono **sottoscrizione di misurazione**, e **sottoscrizione consumo**. Questi tipi di sottoscrizione sono visibili in nuovi ambienti Azure Stack partire dalla versione 1804 ma non sono ancora pronti per l'uso. È consigliabile continuare a usare il **sottoscrizione del Provider predefinito** tipo.

- <!-- 2403291 - IS ASDK --> Non si dispone di utilizzo della barra di scorrimento orizzontale nella parte inferiore di portali di amministratore e utente. Se non è possibile accedere la barra di scorrimento orizzontale, usare i percorsi di navigazione per passare al pannello precedente nel portale selezionando il nome del pannello si desidera visualizzare nell'elenco di navigazione nella parte superiore sinistra del portale.
  ![Barra di navigazione](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Eliminazione utente diverso, le sottoscrizioni le risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- <!-- TBD -  IS ASDK --> Non è possibile visualizzare le autorizzazioni per la sottoscrizione tramite il portale di Azure Stack. In alternativa, usare PowerShell per verificare le autorizzazioni.

- <!--  TBD | ASDK -->  Il fuso orario predefinito per la distribuzione di Azure Stack verrà ora ottenere impostato su UTC. È possibile selezionare un fuso orario durante l'installazione di Azure Stack, ma verrà automaticamente ripristinata in ora UTC come impostazione predefinita durante l'installazione.

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

  Entrambi gli avvisi possono essere tranquillamente ignorati e si chiudono automaticamente nel corso del tempo.  

- <!-- 2368581 - IS. ASDK --> Un operatore di Azure Stack, se si riceve un avviso di memoria insufficiente e le macchine virtuali tenant non riuscire a distribuire con un *errore di creazione della macchina virtuale di Fabric*, è possibile che l'indicatore di Azure Stack ha esaurito la memoria disponibile. Usare la [Capacity Planner di Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) per comprendere meglio la capacità disponibile per i carichi di lavoro.

- <!-- TBD - IS. ASDK --> Quando si esegue il cmdlet Test-AzureStack nell'endpoint con privilegi (PEP), il test verrà generato un messaggio di avviso/non superato per la VM ERCS. È possibile continuare a usare il ASDK.

#### <a name="compute"></a>Calcolo
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


- <!-- TBD -  IS ASDK --> Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile usare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario usare il `-Name` parametro anziché `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Quando si creano macchine virtuali nel portale utenti Azure Stack, il portale Visualizza un numero errato di dischi dati che possono collegare a una VM serie D. Serie D supportate tutte le macchine virtuali è possono soddisfare tutti i dischi dati come la configurazione di Azure.

- <!-- TBD -  IS ASDK --> Quando un'immagine di macchina virtuale ha esito negativo deve essere creato, un elemento non riuscito che non è possibile eliminare potrebbe essere aggiunto al pannello calcolo immagini di macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo dovrebbe risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile riprovare a eseguire il download dell'immagine di macchina virtuale che in precedenza non è riuscita.

- <!-- TBD -  IS ASDK --> Se il provisioning di un'estensione in una distribuzione della VM impiega troppo tempo, gli utenti devono consentire il timeout provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 - IS ASDK --> Diagnostica delle VM di Linux non è supportata in Azure Stack. Quando si distribuisce una VM Linux con abilitata la diagnostica della macchina virtuale, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilitano le metriche di base della VM Linux tramite le impostazioni di diagnostica.

- <!-- 2724961- IS ASDK --> Quando si registra il **Microsoft.Insight** provider di risorse nelle impostazioni di sottoscrizione e creare una VM Windows con Guest OS diagnostica abilitata, la pagina di panoramica della macchina virtuale non mostra i dati delle metriche. 

   Per trovare i dati delle metriche, ad esempio il grafico della percentuale di CPU per la macchina virtuale, passare al **metriche** pannello e Mostra tutte le macchine Virtuali di Windows supportate guest metriche.

#### <a name="networking"></a>Rete
- <!-- 1766332 - IS, ASDK --> Sotto **Networking**, se si fa clic su **creare Gateway VPN** per configurare una connessione VPN, **basata su criteri** viene elencato come un tipo di VPN. Non selezionare questa opzione. Solo le **basato su Route** opzione è supportata in Azure Stack.

- <!-- 1902460 -  IS ASDK --> Azure Stack supporta un'unica *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni tenant. Dopo la creazione della prima rete locale connessione gateway, successivi tentativi di creare una risorsa gateway di rete locale con lo stesso indirizzo IP vengono bloccate.

- <!-- 16309153 -  IS ASDK --> In una rete virtuale che è stato creato con l'impostazione Server DNS *automatica*, la modifica di un errore di Server DNS personalizzato. Le impostazioni aggiornate non vengono inviate alle macchine virtuali in tale rete virtuale.

- <!-- 2702741 -  IS ASDK --> Non è garantito che gli indirizzi IP pubblici che vengono distribuiti tramite il metodo di allocazione dinamica mantenute dopo l'esecuzione di un arresto-deallocazione.

- <!-- 2529607 - IS ASDK --> Durante l'Azure Stack *rotazione segreta*, è previsto un periodo in cui gli indirizzi IP pubblici non sono raggiungibili da due a cinque minuti.

-   <!-- 2664148 - IS ASDK --> Negli scenari in cui il tenant è l'accesso alle proprie macchine virtuali tramite un tunnel VPN S2S, si può verificare uno scenario in cui i tentativi di connessione non riuscire se la subnet locale è stata aggiunta al Gateway di rete locale dopo gateway è già stato creato. 


#### <a name="sql-and-mysql"></a>SQL e MySQL
- <!-- TBD - ASDK --> Il database che ospita i server deve essere dedicato per l'utilizzo di risorse utente e i provider dei carichi di lavoro. È possibile usare un'istanza che è usata da qualsiasi altro consumer, inclusi i servizi App.

- <!-- IS, ASDK --> Caratteri speciali, tra cui spazi e i periodi, non sono supportati nel **famiglia** nome quando si crea uno SKU per i provider di risorse SQL e MySQL.

#### <a name="app-service"></a>Servizio app
- <!-- 2352906 - IS ASDK --> Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- <!-- TBD - IS ASDK --> Per ridimensionare l'infrastruttura (ruoli di lavoro, gestione, i ruoli front-end), è necessario usare PowerShell come descritto nelle note sulla versione per il calcolo.  

- <!-- TBD - IS ASDK --> Servizio App può essere distribuito solo nel *sottoscrizione del Provider predefinito* in questo momento. In un aggiornamento futuro, servizio App verrà distribuito nel nuovo *misurazione sottoscrizione* che è stata introdotta in Azure Stack 1804. Quando l'analisi sono supportato l'utilizzo, tutte le distribuzioni esistenti verranno migrate a questo nuovo tipo di sottoscrizione.

#### <a name="usage"></a>Uso  
- <!-- TBD -  IS ASDK --> Dati misuratore utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* timestamp indicante quando il record è stato creato. Attualmente, è possibile usare questi dati per eseguire l'accounting accurato dell'utilizzo di indirizzi IP pubblico.

<!-- #### Identity -->






## <a name="build-11805147"></a>Compilazione 1.1805.1.47

> [!TIP]  
> In base ai suggerimenti dei clienti, è un aggiornamento allo schema di versione in uso per Microsoft Azure Stack. A partire da questo aggiornamento, 1805, il nuovo schema rappresenta meglio la versione cloud corrente.  
>
> Lo schema della versione è ora *Version.YearYearMonthMonth.MinorVersion.BuildNumber* dove i set di secondo e terzi indicano la versione e rilascio. Ad esempio, 1805.1 rappresenta il *rilasciare in fase di produzione* versione (rilasciata RTM) di 1805.  


### <a name="new-features"></a>Nuove funzionalità
Questa build include i seguenti miglioramenti e correzioni per Azure Stack.  

- <!-- 2297790 - IS, ASDK --> **Azure Stack include ora un *Syslog* client** come un *funzionalità in anteprima*. Questa consente l'inoltro di log di controllo e sicurezza relativi all'infrastruttura di Azure Stack a un Syslog server o della sicurezza le informazioni ed eventi (SIEM) software di gestione esterno ad Azure Stack. Il client di Syslog attualmente supporta solo le connessioni non autenticate UDP sulla porta predefinita 514. Il payload di ogni messaggio Syslog è formattato in comune Event Format (CEF).

  Per configurare il client di Syslog, usare il **Set-SyslogServer** cmdlet nell'Endpoint con privilegi.

  Con questa versione di anteprima, è possibile visualizzare i seguenti tre avvisi. Quando viene visualizzata da Azure Stack, questi avvisi includono *descrizioni* e *monitoraggio e aggiornamento* indicazioni.
  - TITOLO: Integrità del codice esterno  
  - TITOLO: Integrità del codice in modalità di controllo
  - TITOLO: L'Account utente creato

  Quando questa funzionalità è disponibile in anteprima, non deve essere affidabile in ambienti di produzione.   


### <a name="fixed-issues"></a>Problemi risolti
- È stato risolto il problema che bloccati [aprendo una nuova richiesta di supporto nell'elenco a discesa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) all'interno del portale di amministrazione. Questa opzione ora funziona come previsto.

- <!--  TBD ASDK --> La macchina virtuale che ospita l'endpoint con privilegi (PEP) è stata aumentata a 4GB. In ASDK, questa macchina virtuale è denominata AzS-ERCS01.

- **Varie correzioni di** per le prestazioni, stabilità, sicurezza e il sistema operativo che viene usato da Azure Stack


<!-- ### Changes  -->


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Problemi noti

#### <a name="portal"></a>Portale
- <!-- 2931230 – IS  ASDK --> Impossibile eliminare i piani che vengono aggiunti a una sottoscrizione utente come un piano del componente aggiuntivo, anche quando si rimuove il piano dalla sottoscrizione utente. Il piano rimarrà fino a quando non vengono eliminate anche le sottoscrizioni che fanno riferimento il piano aggiuntivo. 

- <!-- 2551834 - IS, ASDK --> Quando si seleziona **Overview** per un account di archiviazione nei portali di amministratore o utente, le informazioni dalle *Essentials* riquadro non viene visualizzata.  Il riquadro Essentials Visualizza informazioni sull'account, ad esempio relativi *gruppo di risorse*, *posizione*, e *Subscription ID*.  Altre opzioni per la panoramica sono accessibili, ad esempio *Services* e *Monitoring*, nonché le opzioni per *Apri in Esplora* o a *Elimina account di archiviazione* .  

  Per visualizzare le informazioni non disponibili, usare il [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) cmdlet di PowerShell.

- <!-- 2551834 - IS, ASDK --> Quando si seleziona **tag** per un account di archiviazione nei portali di amministratore o utente non è possibile caricare le informazioni e non vengono visualizzati.  

  Per visualizzare le informazioni non disponibili, usare il [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) cmdlet di PowerShell.

- <!-- TBD - IS ASDK --> Non usare i nuovi tipi di sottoscrizione amministrativi di *sottoscrizione di misurazione*, e *sottoscrizione consumo*. Questi nuovi tipi di sottoscrizione sono state introdotte con la versione 1804 ma non sono ancora pronti per l'uso. È consigliabile continuare a usare il *Provider predefinito* tipo di sottoscrizione.  
- <!-- TBD - IS ASDK --> È possibile applicare gli aggiornamenti dei driver tramite un pacchetto di estensione OEM con questa versione di Azure Stack.  Non è disponibile alcuna soluzione per questo problema.
 
- <!-- TBD - IS ASDK --> La possibilità [per aprire una nuova richiesta di supporto nell'elenco a discesa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) all'interno di amministratore del portale non è disponibile. In alternativa, usare il collegamento seguente:     
    - Per Azure Stack Development Kit, usare https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Non si dispone di utilizzo della barra di scorrimento orizzontale nella parte inferiore di portali di amministratore e utente. Se non è possibile accedere la barra di scorrimento orizzontale, usare i percorsi di navigazione per passare al pannello precedente nel portale selezionando il nome del pannello si desidera visualizzare nell'elenco di navigazione nella parte superiore sinistra del portale.
  ![Barra di navigazione](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Eliminazione utente diverso, le sottoscrizioni le risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- <!-- TBD -  IS ASDK --> Non è possibile visualizzare le autorizzazioni per la sottoscrizione tramite il portale di Azure Stack. In alternativa, usare PowerShell per verificare le autorizzazioni.


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

    Entrambi gli avvisi 1 # e #2 possono essere tranquillamente ignorati e si chiude automaticamente nel corso del tempo. 

  È anche possibile visualizzare il seguente avviso per *capacità*. Per questo avviso, la percentuale di memoria disponibile identificata nella descrizione può variare:  

  Avviso #3:
   - NOME: Capacità di memoria insufficiente
   - GRAVITÀ: critico
   - COMPONENTE: capacità
   - Descrizione: L'area è utilizzato più di 80,00% della memoria disponibile. Creazione di macchine virtuali con grandi quantità di memoria potrebbe non riuscire.  

  In questa versione di Azure Stack, questo avviso può essere generato in modo non corretto. Se le macchine virtuali tenant continuano a essere distribuito correttamente, è possibile ignorare questo avviso. 
  
  Avviso #3 non verrà chiuso automaticamente. Se si chiude l'avviso Azure Stack creerà lo stesso avviso entro 15 minuti.  

- <!-- 2368581 - IS. ASDK --> Un operatore di Azure Stack, se si riceve un avviso di memoria insufficiente e le macchine virtuali tenant non riuscire a distribuire con un *errore di creazione della macchina virtuale di Fabric*, è possibile che l'indicatore di Azure Stack ha esaurito la memoria disponibile. Usare la [Capacity Planner di Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) per comprendere meglio la capacità disponibile per i carichi di lavoro.

- <!-- TBD - IS. ASDK --> Quando si esegue il cmdlet Test-AzureStack nell'endpoint con privilegi (PEP), verrà generato un messaggio di avviso per la VM ERCS. È possibile continuare a usare il ASDK. 


#### <a name="compute"></a>Calcolo
- <!-- TBD - IS, ASDK --> Quando si seleziona una dimensione di macchina virtuale per una distribuzione della macchina virtuale, alcune dimensioni di VM serie F non sono visibili nell'ambito del selettore di dimensione quando si crea una macchina virtuale. Le dimensioni delle macchine Virtuali seguenti non vengono visualizzati nel selettore: *F8s_v2*, *F16s_v2*, *F32s_v2*, e *F64s_v2*.  
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


- <!-- TBD -  IS ASDK --> Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile usare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario usare il `-Name` parametro anziché `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Quando si creano macchine virtuali nel portale utenti Azure Stack, il portale Visualizza un numero errato di dischi dati che possono collegare a una VM serie D. Serie D supportate tutte le macchine virtuali è possono soddisfare tutti i dischi dati come la configurazione di Azure.

- <!-- TBD -  IS ASDK --> Quando un'immagine di macchina virtuale ha esito negativo deve essere creato, un elemento non riuscito che non è possibile eliminare potrebbe essere aggiunto al pannello calcolo immagini di macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo dovrebbe risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile riprovare a eseguire il download dell'immagine di macchina virtuale che in precedenza non è riuscita.

- <!-- TBD -  IS ASDK --> Se il provisioning di un'estensione in una distribuzione della VM impiega troppo tempo, gli utenti devono consentire il timeout provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 - IS ASDK --> Diagnostica delle VM di Linux non è supportata in Azure Stack. Quando si distribuisce una VM Linux con abilitata la diagnostica della macchina virtuale, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilitano le metriche di base della VM Linux tramite le impostazioni di diagnostica.

#### <a name="networking"></a>Rete
- <!-- TBD - IS ASDK --> Non è possibile creare route definite dall'utente nel portale di amministratore o utente. In alternativa, usare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS, ASDK --> Sotto **Networking**, se si fa clic su **creare Gateway VPN** per configurare una connessione VPN, **basata su criteri** viene elencato come un tipo di VPN. Non selezionare questa opzione. Solo le **basato su Route** opzione è supportata in Azure Stack.

- <!-- 2388980 -  IS ASDK --> Dopo che una macchina virtuale viene creata e associata a un indirizzo IP pubblico, si non è possibile annullare l'associazione di tale macchina virtuale da quell'indirizzo IP. Dissociazione sembra funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato alla VM originale.

  Attualmente, è necessario usare solo nuovi indirizzi IP pubblici per nuove VM eventualmente create.

  Questo comportamento si verifica anche se si riassegna una nuova macchina virtuale l'indirizzo IP (noto come un *scambio VIP*). Tutti i successivi tentativi di connettersi tramite il risultato di indirizzi IP in una connessione alla macchina virtuale originale e non a quello nuovo.

- <!-- 2292271 - IS ASDK --> Se si aumenta un limite di Quota per una risorsa di rete che fa parte di un'offerta e il piano associato a una sottoscrizione tenant, il nuovo limite non viene applicato a tale sottoscrizione. Tuttavia, il nuovo limite è applicabile per i nuovi abbonamenti creati dopo che la quota viene aumentata.

  Per risolvere questo problema, utilizzare un piano del componente aggiuntivo per aumentare una Quota di rete quando il piano è già associato a una sottoscrizione. Per altre informazioni, vedere come [rendere disponibile un piano aggiuntivo](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> È possibile eliminare una sottoscrizione con risorse di zona DNS o le risorse di tabella di Route associate. Per eliminare correttamente la sottoscrizione, è innanzitutto necessario eliminare le risorse di zona DNS e tabella di Route nella sottoscrizione del tenant.


- <!-- 1902460 -  IS ASDK --> Azure Stack supporta un'unica *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni tenant. Dopo la creazione della prima rete locale connessione gateway, successivi tentativi di creare una risorsa gateway di rete locale con lo stesso indirizzo IP vengono bloccate.

- <!-- 16309153 -  IS ASDK --> In una rete virtuale che è stato creato con l'impostazione Server DNS *automatica*, la modifica di un errore di Server DNS personalizzato. Le impostazioni aggiornate non vengono inviate alle macchine virtuali in tale rete virtuale.

- <!-- TBD -  IS ASDK --> Azure Stack non supporta l'aggiunta di interfacce di rete aggiuntiva a un'istanza di macchina virtuale dopo aver distribuita la macchina virtuale. Se la macchina virtuale richiede più di un'interfaccia di rete, devono essere definite in fase di distribuzione.


#### <a name="sql-and-mysql"></a>SQL e MySQL
- <!-- TBD - ASDK --> Il database che ospita i server deve essere dedicato per l'utilizzo di risorse utente e i provider dei carichi di lavoro. È possibile usare un'istanza che è usata da qualsiasi altro consumer, inclusi i servizi App.

- <!-- IS, ASDK --> Caratteri speciali, tra cui spazi e i periodi, non sono supportati nel **famiglia** nome quando si crea uno SKU per i provider di risorse SQL e MySQL.

#### <a name="app-service"></a>Servizio app
- <!-- 2352906 - IS ASDK --> Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- <!-- TBD - IS ASDK --> Per ridimensionare l'infrastruttura (ruoli di lavoro, gestione, i ruoli front-end), è necessario usare PowerShell come descritto nelle note sulla versione per il calcolo.  

- <!-- TBD - IS ASDK --> Servizio App può essere distribuito solo nel *sottoscrizione del Provider predefinito* in questo momento. <!-- In a future update, App Service will deploy into the new *Metering subscription* that was introduced in Azure Stack 1804. When Metering is supported for use, all existing deployments will be migrated to this new subscription type. -->

#### <a name="usage"></a>Uso  
- <!-- TBD -  IS ASDK --> Dati misuratore utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* timestamp indicante quando il record è stato creato. Attualmente, è possibile usare questi dati per eseguire l'accounting accurato dell'utilizzo di indirizzi IP pubblico.

<!-- #### Identity -->

