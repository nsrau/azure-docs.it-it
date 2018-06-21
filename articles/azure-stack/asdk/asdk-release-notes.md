---
title: Note sulla versione di Microsoft Azure Stack Development Kit | Documenti Microsoft
description: Miglioramenti, correzioni e problemi noti per Azure Stack Development Kit.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: bbd9bb0d56dd61fd0a32531ac425a1dbc1aa8923
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295789"
---
# <a name="azure-stack-development-kit-release-notes"></a>Note sulla versione di Azure Stack Development Kit  
Queste note sulla versione offrono informazioni sui miglioramenti e correzioni di problemi noti in Azure Stack Development Kit. Se non si è certi quale versione è in esecuzione, è possibile [utilizzare il portale per controllare](.\.\azure-stack-updates.md#determine-the-current-version).

> Rimanere aggiornati sulle novità di ASDK sottoscrivendo il [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11805147"></a>Compilazione 1.1805.1.47

> [!TIP]  
> In base alle opinioni dei clienti, è un aggiornamento allo schema di versione in uso per lo Stack di Microsoft Azure. A partire da questo aggiornamento, 1805, il nuovo schema rappresenti meglio la versione corrente di cloud.  
> 
> Lo schema di versione è ora *Version.YearYearMonthMonth.MinorVersion.BuildNumber* in cui i set di secondo e terzi indicano la versione e rilascio. Ad esempio, 1805.1 rappresenta il *release alla produzione* (RTM) versione 1805.  


### <a name="new-features"></a>Nuove funzionalità 
Questa compilazione include i seguenti miglioramenti e correzioni per lo Stack di Azure.  

- <!-- 2297790 - IS, ASDK --> **Stack di Azure include ora un *Syslog* client** come una *funzionalità di anteprima*. Questa consente l'inoltro dei registri di controllo e sicurezza correlata all'infrastruttura di Azure Stack un Syslog server o la sicurezza informazioni ed eventi (SIEM) software di gestione che è esterno allo Stack di Azure. Attualmente, il client Syslog supporta solo le connessioni non autenticate UDP sulla porta predefinita 514. Il payload di ogni messaggio Syslog è formattato in comune formato dell'evento (CEF). 

  Per configurare il client di Syslog, utilizzare il **Set-SyslogServer** cmdlet esposta nell'Endpoint con privilegi. 

  Con questa versione di anteprima, è possibile visualizzare gli avvisi di tre seguenti. Quando verrà visualizzata dallo Stack di Azure, questi avvisi includono *descrizioni* e *correzione* informazioni aggiuntive. 
  - TITOLO: Integrità del codice Off  
  - TITOLO: Integrità del codice in modalità di controllo 
  - TITOLO: L'Account utente creato

  Anche se questa funzionalità è disponibile in anteprima, è necessario non è affidabile in ambienti di produzione.   


### <a name="fixed-issues"></a>Problemi risolti
- Il problema è risolto bloccati [aperta una nuova richiesta di supporto nell'elenco a discesa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) all'interno del portale di amministrazione. Questa opzione funziona come previsto. 

- **Varie correzioni** per le prestazioni, stabilità, sicurezza e il sistema operativo che viene utilizzato dallo Stack di Azure


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Problemi noti
 
#### <a name="portal"></a>Portale
- <!-- 2551834 - IS, ASDK --> Quando si seleziona **Overview** per un account di archiviazione nei portali di amministratore o utente, le informazioni dal *Essentials* riquadro non viene visualizzata.  Nel riquadro di Essentials consente di visualizzare informazioni sull'account, ad esempio relativi *gruppo di risorse*, *percorso*, e *ID sottoscrizione*.  Altre opzioni relative a panoramica sono accessibili, come *Services* e *monitoraggio*, nonché come opzioni per *Apri in Esplora* oppure a *eliminare account di archiviazione* .  

  Per visualizzare le informazioni non disponibili, usare il [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) cmdlet di PowerShell. 

- <!-- 2551834 - IS, ASDK --> Quando si seleziona **tag** per un account di archiviazione nei portali l'amministratore o utente, le informazioni sul caricamento non riesce e non vengono visualizzati.  

  Per visualizzare le informazioni non disponibili, usare il [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) cmdlet di PowerShell.

- <!-- TBD - IS ASDK --> Non utilizzare i nuovi tipi di sottoscrizione amministrativi di *misurazione sottoscrizione*, e *sottoscrizione consumo*. Questi nuovi tipi di sottoscrizione sono stati introdotti con versione 1804 ma non sono ancora pronti per l'utilizzo. È consigliabile continuare a usare il *Provider predefinito* tipo di sottoscrizione.  

- <!-- 2403291 - IS ASDK --> Non si dispone di utilizzo della barra di scorrimento orizzontale nella parte inferiore dei portali di amministratore e utente. Se non hai accesso la barra di scorrimento orizzontale, utilizzare il percorso di navigazione per passare a un pannello precedente nel portale di selezionando il nome del pannello si desidera visualizzare nell'elenco di navigazione nella parte superiore sinistra del portale.
  ![Barra di navigazione](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Se si elimina utente sottoscrizioni nelle risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- <!-- TBD -  IS ASDK --> È possibile visualizzare le autorizzazioni per la sottoscrizione utilizzando i portali di Stack di Azure. In alternativa, utilizzare PowerShell per verificare le autorizzazioni.


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

  Entrambi gli avvisi possono essere tranquillamente ignorati e verranno chiuso automaticamente nel corso del tempo.  

- <!-- 2392907 – ASDK -->   Si noterà una *critici* avviso per **capacità di memoria insufficiente**. Questo avviso è la seguente descrizione: *l'area è utilizzato più di 95,00% della memoria disponibile. Creazione di macchine virtuali con grandi quantità di memoria potrebbe non riuscire.*

  Questo avviso può essere generato quando Azure Stack di account in modo non corretto per l'utilizzo di memoria nel kit di sviluppo dello Stack di Azure.  

  Questo avviso può essere ignorato e il problema non influisce sul posizionamento delle macchine virtuali. 

- <!-- 2368581 - IS. ASDK --> Un operatore di Stack di Azure, se si riceve un avviso di memoria insufficiente e le macchine virtuali tenant non distribuita con un *errore di creazione dell'infrastruttura VM*, è possibile che l'indicatore di Azure Stack ha esaurito la memoria disponibile. Usare la [Azure Stack capacità Planne](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) per meglio comprendere la capacità disponibile per i carichi di lavoro. 


#### <a name="compute"></a>Calcolo
- <!-- TBD - IS, ASDK --> Quando si seleziona una dimensione di macchina virtuale per una distribuzione della macchina virtuale, alcune dimensioni di macchina virtuale della serie F non sono visibili nell'ambito del selettore di dimensioni quando si crea una macchina virtuale. Le dimensioni delle macchine Virtuali seguenti non vengono visualizzati nel selettore: *F8s_v2*, *F16s_v2*, *F32s_v2*, e *F64s_v2*.  
  In alternativa, usare uno dei metodi seguenti per distribuire una macchina virtuale. Ogni metodo, è necessario specificare la dimensione di macchina virtuale che si desidera utilizzare.

  - **Modello di gestione risorse di Azure:** quando si utilizza un modello, impostare il *vmSize* nel modello per la dimensione di macchina virtuale da usare è uguale. Ad esempio, la voce seguente consente di distribuire una macchina virtuale che utilizza il *F32s_v2* dimensioni:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **CLI di Azure:** è possibile usare il [creare vm az](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) comando e specificare le dimensioni della VM come parametro, simile a `--size "Standard_F32s_v2"`.

  - **PowerShell:** con PowerShell è possibile utilizzare [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) con il parametro che specifica le dimensioni della VM, simile a `-VMSize "Standard_F32s_v2"`.


- <!-- TBD -  IS ASDK --> Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile utilizzare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario utilizzare il `-Name` parametro anziché `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Quando si creano macchine virtuali nel portale per gli utenti dello Stack di Azure, il portale consente di visualizzare un numero errato di dischi dati che è possibile collegare una serie D macchina virtuale. Tutte le serie D supportate macchine virtuali possono contenere tutti i dischi di dati come la configurazione di Azure.

- <!-- TBD -  IS ASDK --> Quando non è stato possibile creare un'immagine di macchina virtuale, un elemento non riuscito che è possibile eliminare potrebbe essere aggiunti al pannello di calcolo di immagini della macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo deve risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale che precedentemente non riuscito.

- <!-- TBD -  IS ASDK --> Se il provisioning di un'estensione in una distribuzione di macchina virtuale è troppo lunga, gli utenti devono consentire il timeout di provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 - IS ASDK --> Diagnostica Linux VM non è supportato nello Stack di Azure. Quando si distribuisce una VM Linux con diagnostica di macchina virtuale abilitata, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilita la metrica di base VM Linux tramite le impostazioni di diagnostica. 

#### <a name="networking"></a>Rete
- <!-- TBD - IS ASDK --> Non è possibile creare le route definite dall'utente nel portale di amministrazione o utente. In alternativa, usare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS, ASDK --> Sotto **Networking**, se si fa clic su **crea Gateway VPN** per configurare una connessione VPN, **basata su criteri** è elencato come un tipo di VPN. Non selezionare questa opzione. Solo il **basato su Route** opzione è supportata nello Stack di Azure.

- <!-- 2388980 -  IS ASDK --> Dopo aver creata e associata a un indirizzo IP pubblico di una macchina virtuale, è non è possibile annullare l'associazione di tale macchina virtuale da tale indirizzo IP. Disassociazione sembra funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato con la macchina virtuale originale.

  Attualmente, è necessario utilizzare solo indirizzi IP pubblici nuovo per nuove macchine virtuali create.

  Questo comportamento si verifica anche se si riassegna l'indirizzo IP per una nuova macchina virtuale (conosciuta come un *scambio VIP*). Tutti i successivi tenta di connettersi tramite il risultato di indirizzi IP in una connessione alla macchina virtuale originale e non a quello nuovo.


- <!-- 2292271 - IS ASDK --> Se si genera un limite di Quota per una risorsa di rete che fa parte di un piano che è associato a una sottoscrizione tenant e l'offerta, il nuovo limite non viene applicato a tale sottoscrizione. Tuttavia, il nuovo limite viene applicato a nuove sottoscrizioni creati dopo che la quota viene aumentata. 

  Per risolvere questo problema, utilizzare un piano del componente aggiuntivo per aumentare la Quota di rete quando il piano è già associato a una sottoscrizione. Per altre informazioni, vedere come [rendere disponibile un piano del componente aggiuntivo](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> È possibile eliminare una sottoscrizione con risorse di zona DNS o tabella di Route è associato. Per eliminare correttamente la sottoscrizione, è innanzitutto necessario eliminare le risorse di zona DNS e tabella di Route dalla sottoscrizione tenant. 


- <!-- 1902460 -  IS ASDK --> Stack di Azure supporta una singola *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni di tenant. Dopo la creazione della prima rete locale gateway connessione, le successive tenta di creare una risorsa di gateway di rete locale con lo stesso indirizzo IP sono bloccati.

- <!-- 16309153 -  IS ASDK --> In una rete virtuale che è stato creato con un'impostazione del Server DNS *automatico*, la modifica di guasto a un Server DNS personalizzato. Le impostazioni aggiornate non vengono inviate a macchine virtuali in tale rete virtuale.
 
- <!-- TBD -  IS ASDK --> Stack di Azure non supporta l'aggiunta di interfacce di rete aggiuntiva a un'istanza di macchina virtuale dopo aver distribuita la macchina virtuale. Se la macchina virtuale richiede più di un'interfaccia di rete, che devono essere definite in fase di distribuzione.


#### <a name="sql-and-mysql"></a>SQL e MySQL 
- <!-- TBD - ASDK --> Il database server di hosting deve essere dedicato per l'utilizzo di risorse utente e i provider dei carichi di lavoro. È possibile utilizzare un'istanza che è utilizzata da qualsiasi altro consumer, inclusi i servizi di App.

- <!-- IS, ASDK --> Caratteri speciali, inclusi gli spazi e i periodi di, non sono supportati nel **famiglia** nome quando si crea un'unità SKU per i provider di risorse MySQL e SQL Server. 

#### <a name="app-service"></a>Servizio app
- <!-- 2352906 - IS ASDK --> Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- <!-- TBD - IS ASDK --> Per applicare la scalabilità orizzontale l'infrastruttura (Gestione processi di lavoro, i ruoli front-end), è necessario usare PowerShell, come descritto nelle note sulla versione per il calcolo.  

- <!-- TBD - IS ASDK --> Servizio App può essere distribuito solo nel *sottoscrizione Provider predefinito* in questo momento. In un futuro aggiornamento servizio App verrà distribuito nel nuovo *misurazione sottoscrizione* che è stata introdotta in Azure Stack 1804. Quando il controllo è supportato l'utilizzo, tutte le distribuzioni esistenti verranno migrate a questo nuovo tipo di sottoscrizione.

#### <a name="usage"></a>Uso  
- <!-- TBD -  IS ASDK --> Dati misuratore sull'utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* stamp che indica quando è stato creato il record. Attualmente è possibile utilizzare questi dati per eseguire contabilità precisa l'utilizzo degli indirizzi IP pubblico.

<!-- #### Identity -->



## <a name="build-201805131"></a>Compilazione 20180513.1

### <a name="new-features"></a>Nuove funzionalità 
Questa compilazione include i seguenti miglioramenti e correzioni per lo Stack di Azure.  

- <!-- 1759172 - IS, ASDK --> **Le nuove sottoscrizioni amministrative**. Con 1804 sono disponibili due nuovi tipi di sottoscrizione nel portale. Questi nuovi tipi di sottoscrizione sono oltre la sottoscrizione del Provider predefinito ed è visibile con le nuove installazioni Azure Stack partire dalla versione 1804. *Non utilizzare questi nuovi tipi di sottoscrizione con questa versione di Azure Stack*. Si annuncerà la disponibilità per l'utilizzo di questi tipi di sottoscrizione con un aggiornamento futuro. 

  Questi nuovi tipi di sottoscrizione sono visibili, ma fa parte di una modifica di dimensioni maggiore per proteggere la sottoscrizione del Provider predefinito e per semplificare la distribuzione di risorse condivise, come i server che ospita SQL. 

  I tipi di tre sottoscrizione ora disponibili sono:  
  - Sottoscrizione di Provider predefinito: continuare a utilizzare questo tipo di sottoscrizione. 
  - Sottoscrizione di controllo: *non utilizzano questo tipo di sottoscrizione.*
  - Sottoscrizione di utilizzo: *non utilizzano questo tipo di sottoscrizione*

### <a name="fixed-issues"></a>Problemi risolti
- <!-- IS, ASDK -->  Nel portale di amministrazione, non è più necessario aggiornare il riquadro di aggiornamento prima di visualizzare informazioni. 

- <!-- 2050709 - IS, ASDK -->  È ora possibile usare il portale di amministrazione per modificare le metriche di archiviazione per il servizio Blob del servizio tabelle e servizio di Accodamento.

- <!-- IS, ASDK --> Sotto **Networking**, quando si fa clic su **connessione** per impostare una connessione VPN **Site-to-site (IPsec)** è l'unica opzione disponibile. 

- **Varie correzioni** per le prestazioni, stabilità, sicurezza e il sistema operativo che viene utilizzato dallo Stack di Azure

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Versioni aggiuntive programmate con questo aggiornamento  
Di seguito è ora disponibili, ma non richiede l'aggiornamento dello Stack Azure 1804.
- **Aggiornamento in Microsoft Azure Stack System Center Operations Manager Monitoring Pack**. È disponibile per una nuova versione (1.0.3.0) di Microsoft System Center Operations Manager Monitoring Pack per Azure Stack [scaricare](https://www.microsoft.com/download/details.aspx?id=55184). Con questa versione, è possibile utilizzare le entità servizio quando si aggiunge una distribuzione di Azure Stack connessa. Questa versione include anche un'esperienza di gestione degli aggiornamenti che consente di eseguire l'azione di correzione direttamente all'interno di Operations Manager. Sono inoltre disponibili nuovi dashboard che visualizzano i provider di risorse, unità di scala e ridimensionare i nodi di unità.

- **Nuova versione di Azure Stack amministrazione PowerShell versione 1.3.0**.  Azure PowerShell Stack 1.3.0 è ora disponibile per l'installazione. Questa versione sono disponibili comandi per tutti i provider di risorse di amministrazione gestire Azure Stack.  Con questa versione, alcuni contenuti verranno deprecati a partire da Azure Stack strumenti GitHub [repository](https://github.com/Azure/AzureStack-Tools). 

   Per informazioni dettagliate di installazione, seguire le [istruzioni](.\.\azure-stack-powershell-install.md) o il [Guida](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) contenuto per il modulo di Azure Stack 1.3.0. 

- **Versione del riferimento all'API Rest di Azure Stack iniziale**. Il [riferimento API per tutti i provider di risorse di Azure Stack Admin](https://docs.microsoft.com/rest/api/azure-stack/) ora viene pubblicato. 

### <a name="known-issues"></a>Problemi noti
 
#### <a name="portal"></a>Portale
- <!-- TBD - IS ASDK --> La capacità [per aprire una nuova richiesta di supporto nell'elenco a discesa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) da entro l'amministratore del portale non è attualmente disponibile. In alternativa, usare il collegamento seguente:     
    - Per i Kit di sviluppo dello Stack di Azure, utilizzare https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Non si dispone di utilizzo della barra di scorrimento orizzontale nella parte inferiore dei portali di amministratore e utente. Se non hai accesso la barra di scorrimento orizzontale, utilizzare il percorso di navigazione per passare a un pannello precedente nel portale di selezionando il nome del pannello si desidera visualizzare nell'elenco di navigazione nella parte superiore sinistra del portale.
  ![Barra di navigazione](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Se si elimina utente sottoscrizioni nelle risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- <!-- TBD -  IS ASDK --> È possibile visualizzare le autorizzazioni per la sottoscrizione utilizzando i portali di Stack di Azure. In alternativa, utilizzare PowerShell per verificare le autorizzazioni.

-   <!-- TBD -  IS ASDK --> Nel portale di amministrazione, è possibile visualizzare un avviso critico per il componente Microsoft.Update.Admin. Il nome dell'avviso, la descrizione e correzione tutti visualizzati come:  
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

#### <a name="marketplace"></a>Marketplace
- Gli utenti sfogliare il marketplace completo senza una sottoscrizione e di visualizzare gli elementi di amministrazione come piani e alle offerte. Questi elementi sono non funzionale agli utenti.
 
#### <a name="compute"></a>Calcolo
- <!-- TBD -  IS ASDK --> Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile utilizzare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario utilizzare il `-Name` parametro anziché `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Quando si creano macchine virtuali nel portale per gli utenti dello Stack di Azure, il portale consente di visualizzare un numero errato di dischi dati che è possibile collegare a una VM di serie DS. Macchine virtuali di serie DS possono contenere tutti i dischi di dati come la configurazione di Azure.

- <!-- TBD -  IS ASDK --> Quando non è stato possibile creare un'immagine di macchina virtuale, un elemento non riuscito che è possibile eliminare potrebbe essere aggiunti al pannello di calcolo di immagini della macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo deve risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale che precedentemente non riuscito.

- <!-- TBD -  IS ASDK --> Se il provisioning di un'estensione in una distribuzione di macchina virtuale è troppo lunga, gli utenti devono consentire il timeout di provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 - IS ASDK --> Diagnostica Linux VM non è supportato nello Stack di Azure. Quando si distribuisce una VM Linux con diagnostica di macchina virtuale abilitata, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilita la metrica di base VM Linux tramite le impostazioni di diagnostica. 

#### <a name="networking"></a>Rete
- <!-- 1766332 - IS, ASDK --> Sotto **Networking**, se si fa clic su **crea Gateway VPN** per configurare una connessione VPN, **basata su criteri** è elencato come un tipo di VPN. Non selezionare questa opzione. Solo il **basato su Route** opzione è supportata nello Stack di Azure.

- <!-- 2388980 -  IS ASDK --> Dopo aver creata e associata a un indirizzo IP pubblico di una macchina virtuale, è non è possibile annullare l'associazione di tale macchina virtuale da tale indirizzo IP. Disassociazione sembra funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato con la macchina virtuale originale.

  Attualmente, è necessario utilizzare solo indirizzi IP pubblici nuovo per nuove macchine virtuali create.

  Questo comportamento si verifica anche se si riassegna l'indirizzo IP per una nuova macchina virtuale (conosciuta come un *scambio VIP*). Tutti i successivi tenta di connettersi tramite il risultato di indirizzi IP in una connessione alla macchina virtuale originale e non a quello nuovo.

- <!-- 2292271 - IS ASDK --> Se si genera un limite di Quota per una risorsa di rete che fa parte di un piano che è associato a una sottoscrizione tenant e l'offerta, il nuovo limite non viene applicato a tale sottoscrizione. Tuttavia, il nuovo limite viene applicato a nuove sottoscrizioni creati dopo che la quota viene aumentata. 

  Per risolvere questo problema, utilizzare un piano del componente aggiuntivo per aumentare la Quota di rete quando il piano è già associato a una sottoscrizione. Per altre informazioni, vedere come [rendere disponibile un piano del componente aggiuntivo](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> È possibile eliminare una sottoscrizione con risorse di zona DNS o tabella di Route è associato. Per eliminare correttamente la sottoscrizione, è innanzitutto necessario eliminare le risorse di zona DNS e tabella di Route dalla sottoscrizione tenant. 


- <!-- 1902460 -  IS ASDK --> Stack di Azure supporta una singola *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni di tenant. Dopo la creazione della prima rete locale gateway connessione, le successive tenta di creare una risorsa di gateway di rete locale con lo stesso indirizzo IP sono bloccati.

- <!-- 16309153 -  IS ASDK --> In una rete virtuale che è stato creato con un'impostazione del Server DNS *automatico*, la modifica di guasto a un Server DNS personalizzato. Le impostazioni aggiornate non vengono inviate a macchine virtuali in tale rete virtuale.
 
- <!-- TBD -  IS ASDK --> Stack di Azure non supporta l'aggiunta di interfacce di rete aggiuntiva a un'istanza di macchina virtuale dopo aver distribuita la macchina virtuale. Se la macchina virtuale richiede più di un'interfaccia di rete, che devono essere definite in fase di distribuzione.


#### <a name="sql-and-mysql"></a>SQL e MySQL 
- <!-- TBD - ASDK --> Il database server di hosting deve essere dedicato per l'utilizzo di risorse utente e i provider dei carichi di lavoro. È possibile utilizzare un'istanza che è utilizzata da qualsiasi altro consumer, inclusi i servizi di App.

- <!-- IS, ASDK --> Caratteri speciali, inclusi gli spazi e i periodi di, non sono supportati nel **famiglia** nome quando si crea un'unità SKU per i provider di risorse MySQL e SQL Server. 

#### <a name="app-service"></a>Servizio app
- <!-- TBD -  IS ASDK --> Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- <!-- TBD -  IS ASDK --> Per applicare la scalabilità orizzontale l'infrastruttura (Gestione processi di lavoro, i ruoli front-end), è necessario usare PowerShell, come descritto nelle note sulla versione per il calcolo.
 
#### <a name="usage"></a>Uso  
- <!-- TBD -  IS ASDK --> Dati misuratore sull'utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* stamp che indica quando è stato creato il record. Attualmente è possibile utilizzare questi dati per eseguire contabilità precisa l'utilizzo degli indirizzi IP pubblico.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Download degli strumenti di Azure Stack da GitHub
- Quando si utilizza il *invoke-webrequest* da Github degli strumenti cmdlet di PowerShell per scaricare lo Stack di Azure, viene visualizzato un errore:     
    -  *Invoke-webrequest: la richiesta è stata interrotta: Impossibile creare un canale protetto SSL/TLS.*     

  Questo errore si verifica a causa di un recente deprecazione di supporto di GitHub degli Tlsv1 e Tlsv1.1 crittografia standard (impostazione predefinita per PowerShell). Per ulteriori informazioni, vedere [si noti la rimozione di standard di crittografia debole](https://githubengineering.com/crypto-removal-notice/).

<!-- #### Identity -->



## <a name="build-201803021"></a>Compilazione 20180302.1

### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni
Le nuove funzionalità e correzioni rilasciate per lo Stack di Azure versione sistemi integrati 1803 si applicano al Kit di sviluppo dello Stack di Azure. Vedere la [nuove funzionalità](.\.\azure-stack-update-1803.md#new-features) e [problemi risolti](.\.\azure-stack-update-1803.md#fixed-issues) sezioni del 1803 Stack Azure aggiornare note sulla versione per informazioni dettagliate.  
> [!IMPORTANT]    
> Alcuni degli elementi elencati nella **nuove funzionalità** e **problemi risolti** sezioni sono rilevanti solo per i sistemi Azure Stack integrato.

### <a name="changes"></a>Modifiche
- Il modo per modificare lo stato di un'offerta appena creato da *privato* a *pubblica* o *rimosse* è stato modificato. Per altre informazioni, vedere [creare un'offerta](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Problemi noti
 
#### <a name="portal"></a>Portale
- Il possibilità [per aprire una nuova richiesta di supporto nell'elenco a discesa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) all'interno di amministratore del portale non è disponibile. In alternativa, usare il collegamento seguente:     
    - Per i Kit di sviluppo dello Stack di Azure, utilizzare https://aka.ms/azurestackforum.    

- <!-- 2050709 --> Nel portale di amministrazione, non è possibile modificare le metriche di archiviazione per il servizio Blob, del servizio tabelle o servizio di Accodamento. Quando si accede alla risorsa di archiviazione e quindi seleziona il riquadro servizio coda, tabella o blob, viene aperto un nuovo pannello che visualizza un grafico delle metriche per il servizio. Se si seleziona quindi modifica dalla parte superiore del riquadro del grafico delle metriche, nel Pannello di modifica grafico viene visualizzata ma opzioni per modificare le metriche non visualizzate.  

- Viene visualizzato un **attivazione richiesto** messaggio di avviso che consiglia di registrare il Kit di sviluppo dello Stack di Azure. Questo comportamento è previsto.

- Se si elimina utente sottoscrizioni nelle risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- È possibile visualizzare le autorizzazioni per la sottoscrizione utilizzando i portali di Stack di Azure. In alternativa, utilizzare PowerShell per verificare le autorizzazioni.

- Nel dashboard del portale di amministrazione, il riquadro di aggiornamento non riesce a visualizzare informazioni sugli aggiornamenti. Per risolvere questo problema, fare clic sul riquadro per aggiornarla.

-   Nel portale di amministrazione, è possibile visualizzare un avviso critico per il componente Microsoft.Update.Admin. Il nome dell'avviso, la descrizione e correzione tutti visualizzati come:  
    - *Errore: modello per FaultType ResourceProviderTimeout è manca.*

    Questo avviso può essere tranquillamente ignorato. 

- Portale di amministrazione sia dal portale per gli utenti, il pannello Panoramica non riesce a caricare quando si seleziona il pannello della panoramica per gli account di archiviazione che sono stati creati con una versione precedente di API (esempio: 2015-06-15). 

  In alternativa, utilizzare PowerShell per eseguire la **inizio ResourceSynchronization.ps1** script per ripristinare l'accesso per l'account di archiviazione. [Lo script è disponibile da GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)e con le credenziali di amministratore nell'host kit sviluppo se si utilizza il ASDK.  

- Il **servizio integrità** blade non riesce a caricare. Quando si apre il pannello di integrità del servizio nel portale di amministrazione o di utente, Azure Stack viene visualizzato un errore e non caricare le informazioni. Si tratta di un comportamento previsto. Sebbene sia possibile selezionare e aprire l'integrità del servizio, questa funzionalità non è ancora disponibile, ma verrà implementata in una versione futura dello Stack di Azure.


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

- Nel portale di amministrazione di Stack di Azure, è possibile notare un avviso critico con il nome **in sospeso scadenza certificato esterno**.  Questo avviso può essere tranquillamente ignorato e sulle operazioni del Kit di sviluppo dello Stack di Azure. 


#### <a name="marketplace"></a>Marketplace
- Gli utenti sfogliare il marketplace completo senza una sottoscrizione e di visualizzare gli elementi di amministrazione come piani e alle offerte. Questi elementi sono non funzionale agli utenti.
 
#### <a name="compute"></a>Calcolo
- Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile utilizzare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario utilizzare il `-Name` parametro anziché `-VMScaleSetName`.

- Quando si creano macchine virtuali nel portale per gli utenti dello Stack di Azure, il portale consente di visualizzare un numero errato di dischi dati che è possibile collegare a una VM di serie DS. Macchine virtuali di serie DS possono contenere tutti i dischi di dati come la configurazione di Azure.

- Quando non è stato possibile creare un'immagine di macchina virtuale, un elemento non riuscito che è possibile eliminare potrebbe essere aggiunti al pannello di calcolo di immagini della macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo deve risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale che precedentemente non riuscito.

-  Se il provisioning di un'estensione in una distribuzione di macchina virtuale è troppo lunga, gli utenti devono consentire il timeout di provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 --> Diagnostica Linux VM non è supportato nello Stack di Azure. Quando si distribuisce una VM Linux con diagnostica di macchina virtuale abilitata, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilita la metrica di base VM Linux tramite le impostazioni di diagnostica. 


#### <a name="networking"></a>Rete
- In **rete**, se si fa clic su **connessione** per impostare una connessione VPN, **per rete virtuale a** è elencato come un tipo di connessione. Non selezionare questa opzione. Attualmente, solo il **Site-to-site (IPsec)** opzione è supportata.

- Dopo aver creata e associata a un indirizzo IP pubblico di una macchina virtuale, è non è possibile annullare l'associazione di tale macchina virtuale da tale indirizzo IP. Disassociazione sembra funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato con la macchina virtuale originale.

  Attualmente, è necessario utilizzare solo indirizzi IP pubblici nuovo per nuove macchine virtuali create.

  Questo comportamento si verifica anche se si riassegna l'indirizzo IP per una nuova macchina virtuale (conosciuta come un *scambio VIP*). Tutti i successivi tenta di connettersi tramite il risultato di indirizzi IP in una connessione alla macchina virtuale originale e non a quello nuovo.



- Stack di Azure supporta un singolo *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni di tenant. Dopo la creazione della prima rete locale gateway connessione, le successive tenta di creare una risorsa di gateway di rete locale con lo stesso indirizzo IP sono bloccati.

- In una rete virtuale che è stato creato con un'impostazione di Server DNS di *automatico*, la modifica di guasto a un Server DNS personalizzato. Le impostazioni aggiornate non vengono inviate a macchine virtuali in tale rete virtuale.
 
- Stack di Azure non supporta l'aggiunta di interfacce di rete aggiuntiva a un'istanza di macchina virtuale dopo aver distribuita la macchina virtuale. Se la macchina virtuale richiede più di un'interfaccia di rete, che devono essere definite in fase di distribuzione.



#### <a name="sql-and-mysql"></a>SQL e MySQL 
- Può richiedere fino a un'ora prima che gli utenti possono creare database in un nuovo SQL o MySQL SKU.

- Il database server di hosting deve essere dedicato per l'utilizzo di risorse utente e i provider dei carichi di lavoro. È possibile utilizzare un'istanza che è utilizzata da qualsiasi altro consumer, inclusi i servizi di App.

- <!-- IS, ASDK --> Caratteri speciali, inclusi gli spazi e i periodi di, non sono supportati nel **famiglia** oppure **livello** nomi quando si crea un'unità SKU per i provider di risorse MySQL e SQL Server.

#### <a name="app-service"></a>Servizio app
- Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- Per applicare la scalabilità orizzontale l'infrastruttura (Gestione processi di lavoro, i ruoli front-end), è necessario usare PowerShell, come descritto nelle note sulla versione per il calcolo.
 
#### <a name="usage"></a>Uso  
- Dati misuratore sull'utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* stamp che indica quando è stato creato il record. Attualmente è possibile utilizzare questi dati per eseguire contabilità precisa l'utilizzo degli indirizzi IP pubblico.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Download degli strumenti di Azure Stack da GitHub
- Quando si utilizza il *invoke-webrequest* da Github degli strumenti cmdlet di PowerShell per scaricare lo Stack di Azure, viene visualizzato un errore:     
    -  *Invoke-webrequest: la richiesta è stata interrotta: Impossibile creare un canale protetto SSL/TLS.*     

  Questo errore si verifica a causa di un recente deprecazione di supporto di GitHub degli Tlsv1 e Tlsv1.1 crittografia standard (impostazione predefinita per PowerShell). Per ulteriori informazioni, vedere [si noti la rimozione di standard di crittografia debole](https://githubengineering.com/crypto-removal-notice/).

  Per risolvere questo problema, aggiungere `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` all'inizio dello script per forzare la console di PowerShell per utilizzare TLSv1.2 durante il download dal repository di GitHub.





