---
title: Note sulla versione di Microsoft Azure Stack Development Kit | Microsoft Docs
description: I miglioramenti, correzioni e problemi noti per Azure Stack Development Kit.
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
ms.date: 07/30/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 2f732dfdfe9bf4aff2753114c3041f8f646421c2
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344733"
---
# <a name="azure-stack-development-kit-release-notes"></a>Note sulla versione di Azure Stack Development Kit
Queste note sulla versione offrono informazioni sui miglioramenti e correzioni di problemi noti in Azure Stack Development Kit. Se non si conosce quale versione in esecuzione, è possibile [usare il portale per controllare](.\.\azure-stack-updates.md#determine-the-current-version).

> Rimanere aggiornati sulle novità di ASDK sottoscrivendo il [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11805142"></a>Compilazione 1.1805.1.42

<!-- ### New features 
This build includes the following improvements and fixes for Azure Stack.  
-->


### <a name="fixed-issues"></a>Problemi risolti

- **Varie correzioni di** per le prestazioni, stabilità, sicurezza e il sistema operativo che viene usato da Azure Stack


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Problemi noti
 
#### <a name="portal"></a>Portale
- <!-- TBD - IS ASDK --> È possibile applicare gli aggiornamenti dei driver tramite un pacchetto di estensione OEM con questa versione di Azure Stack.  Non è disponibile alcuna soluzione per questo problema.
 
- <!-- TBD - IS ASDK --> Non usare i nuovi tipi di sottoscrizione amministrativi di *sottoscrizione di misurazione*, e *sottoscrizione consumo*. Questi nuovi tipi di sottoscrizione sono state introdotte con la versione 1804 ma non sono ancora pronti per l'uso. È consigliabile continuare a usare il *Provider predefinito* tipo di sottoscrizione.  

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

  Entrambi gli avvisi possono essere tranquillamente ignorati. Verranno chiusi automaticamente nel corso del tempo.  

#### <a name="compute"></a>Calcolo
- <!-- TBD -  IS ASDK --> Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile usare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario usare il `-Name` parametro anziché `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Quando si creano macchine virtuali nel portale utenti Azure Stack, il portale Visualizza un numero errato di dischi dati che possono collegare a una VM serie D. Serie D supportate tutte le macchine virtuali è possono soddisfare tutti i dischi dati come la configurazione di Azure.

- <!-- TBD -  IS ASDK --> Quando un'immagine di macchina virtuale ha esito negativo deve essere creato, un elemento non riuscito che non è possibile eliminare potrebbe essere aggiunto al pannello calcolo immagini di macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo dovrebbe risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale che in precedenza non è riuscita.

- <!-- TBD -  IS ASDK --> Se il provisioning di un'estensione in una distribuzione della VM impiega troppo tempo, gli utenti devono consentire il timeout provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 - IS ASDK --> Diagnostica delle VM di Linux non è supportata in Azure Stack. Quando si distribuisce una VM Linux con abilitata la diagnostica della macchina virtuale, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilitano le metriche di base della VM Linux tramite le impostazioni di diagnostica. 

#### <a name="networking"></a>Rete
- <!-- 1766332 - IS, ASDK --> Sotto **Networking**, se si fa clic su **creare Gateway VPN** per configurare una connessione VPN, **basata su criteri** viene elencato come un tipo di VPN. Non selezionare questa opzione. Solo le **basato su Route** opzione è supportata in Azure Stack.

- <!-- 2388980 -  IS ASDK --> Dopo che una macchina virtuale viene creata e associata a un indirizzo IP pubblico, si non è possibile annullare l'associazione di tale macchina virtuale da quell'indirizzo IP. Dissociazione sembra funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato alla VM originale.

  Attualmente, è necessario usare solo nuovi indirizzi IP pubblici per nuove VM eventualmente create.

  Questo comportamento si verifica anche se si riassegna una nuova macchina virtuale l'indirizzo IP (noto come un *scambio VIP*). Tutti i successivi tentativi di connettersi tramite il risultato di indirizzi IP in una connessione alla macchina virtuale di origine associata e non a quello nuovo.

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

- <!-- TBD - IS ASDK --> Servizio App può essere distribuito solo nel *sottoscrizione del Provider predefinito* in questo momento. In un aggiornamento futuro, servizio App verrà distribuito nel nuovo *misurazione sottoscrizione* che è stata introdotta in Azure Stack 1804. Quando l'analisi sono supportato l'utilizzo, tutte le distribuzioni esistenti verranno migrate a questo nuovo tipo di sottoscrizione.

#### <a name="usage"></a>Uso  
- <!-- TBD -  IS ASDK --> Dati misuratore utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* timestamp indicante quando il record è stato creato. Attualmente, è possibile usare questi dati per eseguire l'accounting accurato dell'utilizzo di indirizzi IP pubblico.

<!-- #### Identity -->



## <a name="build-201805131"></a>Compilazione 20180513.1

### <a name="new-features"></a>Nuove funzionalità 
Questa build include i seguenti miglioramenti e correzioni per Azure Stack.  

- <!-- 1759172 - IS, ASDK --> **Le nuove sottoscrizioni amministrative**. Con 1804 sono disponibili due nuovi tipi di sottoscrizione nel portale. Questi nuovi tipi di sottoscrizione sono oltre la sottoscrizione del Provider predefinito e visibili le nuove installazioni di Azure Stack partire dalla versione 1804. *Non usare questi nuovi tipi di sottoscrizione con questa versione di Azure Stack*. Verranno annunciati la disponibilità per usare questi tipi di sottoscrizione con un aggiornamento futuro. 

  Questi nuovi tipi di sottoscrizione sono visibili, ma fa parte di una modifica di dimensioni maggiori per proteggere la sottoscrizione del Provider predefinito e per renderne più semplice distribuire le risorse condivise, come i server di Hosting SQL. 

  I tipi di tre sottoscrizione ora disponibili sono:  
  - Sottoscrizione del Provider predefinito: continuare a usare questo tipo di sottoscrizione. 
  - Sottoscrizione di controllo: *non usare questo tipo di sottoscrizione.*
  - Sottoscrizione di consumo: *non usare questo tipo di sottoscrizione*

### <a name="fixed-issues"></a>Problemi risolti
- <!-- IS, ASDK -->  Nel portale di amministrazione, è necessario non è più aggiornare il riquadro di aggiornamento prima di visualizzare le informazioni. 

- <!-- 2050709 - IS, ASDK -->  È ora possibile usare il portale di amministrazione per modificare le metriche di archiviazione per il servizio Blob, servizio tabelle e servizio di Accodamento.

- <!-- IS, ASDK --> Sotto **Networking**quando fa clic su **connessione** per configurare una connessione VPN, **Site-to-site (IPsec)** è a questo punto l'unica opzione disponibile. 

- **Varie correzioni di** per le prestazioni, stabilità, sicurezza e il sistema operativo che viene usato da Azure Stack

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Altri rilasci programmati con questo aggiornamento  
Di seguito è ora disponibili, ma non richiede l'aggiornamento di Azure Stack 1804.
- **Aggiornamento di Microsoft Azure Stack System Center Operations Manager Monitoring Pack**. È disponibile per una nuova versione (1.0.3.0) di Microsoft System Center Operations Manager Monitoring Pack per Azure Stack [scaricare](https://www.microsoft.com/download/details.aspx?id=55184). Con questa versione, è possibile usare le entità servizio quando si aggiunge una distribuzione di Azure Stack connesso. Questa versione offre anche un'esperienza di gestione degli aggiornamenti che consente di eseguire l'azione di correzione direttamente dall'interno di Operations Manager. Sono inoltre disponibili nuovi dashboard che visualizzano i provider di risorse, unità di scala e ridimensionare i nodi di unità.

- **Nuovo di Azure Stack Admin PowerShell versione 1.3.0**.  Azure Stack PowerShell 1.3.0 è ora disponibile per l'installazione. Questa versione sono disponibili comandi per tutti i provider di risorse di amministratore per la gestione di Azure Stack.  Con questa versione, verranno deprecato alcuni contenuti da GitHub gli strumenti di Azure Stack [repository](https://github.com/Azure/AzureStack-Tools). 

   Per informazioni dettagliate di installazione, seguire le [istruzioni](.\.\azure-stack-powershell-install.md) o il [Guida](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) contenuto per Azure Stack modulo 1.3.0. 

- **Versione iniziale del riferimento all'API Rest di Azure Stack**. Il [riferimento API per tutti i provider di risorse di Azure Stack Admin](https://docs.microsoft.com/rest/api/azure-stack/) viene pubblicato. 

### <a name="known-issues"></a>Problemi noti
 
#### <a name="portal"></a>Portale
- <!-- TBD - IS ASDK --> È possibile applicare gli aggiornamenti dei driver tramite un pacchetto di estensione OEM con questa versione di Azure Stack.  Non è disponibile alcuna soluzione per questo problema.
 
- <!-- TBD - IS ASDK --> La possibilità [per aprire una nuova richiesta di supporto nell'elenco a discesa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) all'interno di amministratore del portale non è disponibile. In alternativa, usare il collegamento seguente:     
    - Per Azure Stack Development Kit, usare https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Non si dispone di utilizzo della barra di scorrimento orizzontale nella parte inferiore di portali di amministratore e utente. Se non è possibile accedere la barra di scorrimento orizzontale, usare i percorsi di navigazione per passare al pannello precedente nel portale selezionando il nome del pannello si desidera visualizzare nell'elenco di navigazione nella parte superiore sinistra del portale.
  ![Barra di navigazione](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Eliminazione utente diverso, le sottoscrizioni le risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- <!-- TBD -  IS ASDK --> Non è possibile visualizzare le autorizzazioni per la sottoscrizione tramite il portale di Azure Stack. In alternativa, usare PowerShell per verificare le autorizzazioni.

-   <!-- TBD -  IS ASDK --> Nel portale di amministrazione, è possibile notare un avviso critico per il componente Microsoft.Update.Admin. Il nome dell'avviso, descrizione e correzione tutti visualizzati come:  
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
- Gli utenti possono esplorare il marketplace completo senza una sottoscrizione ed è possono visualizzare elementi amministrativi quali offerte e piani. Questi elementi sono non funzionale per gli utenti.
 
#### <a name="compute"></a>Calcolo
- <!-- TBD -  IS ASDK --> Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile usare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario usare il `-Name` parametro anziché `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Quando si creano macchine virtuali nel portale utenti Azure Stack, il portale Visualizza un numero errato di dischi dati che è possibile collegare a una VM serie DS. Le VM serie DS può supportare tutti i dischi dati come la configurazione di Azure.

- <!-- TBD -  IS ASDK --> Quando un'immagine di macchina virtuale ha esito negativo deve essere creato, un elemento non riuscito che non è possibile eliminare potrebbe essere aggiunto al pannello calcolo immagini di macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo dovrebbe risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale che in precedenza non è riuscita.

- <!-- TBD -  IS ASDK --> Se il provisioning di un'estensione in una distribuzione della VM impiega troppo tempo, gli utenti devono consentire il timeout provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 - IS ASDK --> Diagnostica delle VM di Linux non è supportata in Azure Stack. Quando si distribuisce una VM Linux con abilitata la diagnostica della macchina virtuale, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilitano le metriche di base della VM Linux tramite le impostazioni di diagnostica. 

#### <a name="networking"></a>Rete
- <!-- 1766332 - IS, ASDK --> Sotto **Networking**, se si fa clic su **creare Gateway VPN** per configurare una connessione VPN, **basata su criteri** viene elencato come un tipo di VPN. Non selezionare questa opzione. Solo le **basato su Route** opzione è supportata in Azure Stack.

- <!-- 2388980 -  IS ASDK --> Dopo che una macchina virtuale viene creata e associata a un indirizzo IP pubblico, si non è possibile annullare l'associazione di tale macchina virtuale da quell'indirizzo IP. Dissociazione sembra funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato alla VM originale.

  Attualmente, è necessario usare solo nuovi indirizzi IP pubblici per nuove VM eventualmente create.

  Questo comportamento si verifica anche se si riassegna una nuova macchina virtuale l'indirizzo IP (noto come un *scambio VIP*). Tutti i successivi tentativi di connettersi tramite il risultato di indirizzi IP in una connessione alla macchina virtuale di origine associata e non a quello nuovo.

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
- <!-- TBD -  IS ASDK --> Gli utenti devono registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.

- <!-- TBD -  IS ASDK --> Per ridimensionare l'infrastruttura (ruoli di lavoro, gestione, i ruoli front-end), è necessario usare PowerShell come descritto nelle note sulla versione per il calcolo.
 
#### <a name="usage"></a>Uso  
- <!-- TBD -  IS ASDK --> Dati misuratore utilizzo degli indirizzi IP pubblici dell'utilizzo viene mostrata la stessa *EventDateTime* valore per ogni record anziché il *TimeDate* timestamp indicante quando il record è stato creato. Attualmente, è possibile usare questi dati per eseguire l'accounting accurato dell'utilizzo di indirizzi IP pubblico.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Download degli strumenti di Azure Stack da GitHub
- Quando si usa la *invoke-webrequest* degli strumenti cmdlet di PowerShell per scaricare lo Stack di Azure da Github, viene visualizzato un errore:     
    -  *Invoke-webrequest: richiesta annullata: Impossibile creare un canale sicuro SSL/TLS.*     

  Questo errore si verifica a causa di un recente relativo al supporto GitHub deprecato il Tlsv1 e Tlsv1.1 standard di crittografia (impostazione predefinita per PowerShell). Per altre informazioni, vedere [si noti che la rimozione di standard di crittografia debole](https://githubengineering.com/crypto-removal-notice/).

<!-- #### Identity -->



## <a name="build-201803021"></a>Compilazione 20180302.1

### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni
Le nuove funzionalità e correzioni rilasciate per i sistemi integrati versione 1803 applicabile per Azure Stack Development Kit di Azure Stack. Vedere le [nuove funzionalità](.\.\azure-stack-update-1803.md#new-features) e [problemi risolti](.\.\azure-stack-update-1803.md#fixed-issues) sezioni di 1803 di Stack di Azure Aggiorna le note sulla versione per informazioni dettagliate.  
> [!IMPORTANT]    
> Alcuni degli elementi elencati nel **nuove funzionalità** e **problemi risolti** sezioni sono rilevanti solo per i sistemi integrati di Azure Stack.

### <a name="changes"></a>Modifiche
- Il modo per modificare lo stato di un'offerta appena creato dal *privati* a *pubblico* o *autorizzazioni rimosse* è stato modificato. Per altre informazioni, vedere [creare un'offerta](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Problemi noti
 
#### <a name="portal"></a>Portale
- La possibilità [per aprire una nuova richiesta di supporto nell'elenco a discesa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) all'interno di amministratore del portale non è disponibile. In alternativa, usare il collegamento seguente:     
    - Per Azure Stack Development Kit, usare https://aka.ms/azurestackforum.    

- <!-- 2050709 --> Nel portale di amministrazione, non è possibile modificare le metriche di archiviazione per il servizio Blob, servizio tabelle o servizio di Accodamento. Quando si accede all'archiviazione e quindi seleziona il blob, tabella o coda service riquadro, viene aperto un nuovo pannello che visualizza un grafico delle metriche per il servizio. Se si seleziona Modifica dalla parte superiore del riquadro del grafico delle metriche, il pannello Modifica grafico si apre, ma non visualizza le opzioni per modificare le metriche.  

- Viene visualizzato un **attivazione obbligatoria** messaggio di avviso che richiede di eseguire la registrazione di Azure Stack Development Kit. Questo comportamento è previsto.

- Eliminazione utente diverso, le sottoscrizioni le risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.

- Non è possibile visualizzare le autorizzazioni per la sottoscrizione tramite il portale di Azure Stack. In alternativa, usare PowerShell per verificare le autorizzazioni.

- Nel dashboard del portale di amministrazione, il riquadro di aggiornamento non riesce a visualizzare le informazioni sugli aggiornamenti. Per risolvere questo problema, fare clic sul riquadro per aggiornarlo.

-   Nel portale di amministrazione, è possibile notare un avviso critico per il componente Microsoft.Update.Admin. Il nome dell'avviso, descrizione e correzione tutti visualizzati come:  
    - *Errore: modello per FaultType ResourceProviderTimeout è manca.*

    Questo avviso può essere tranquillamente ignorato. 

- Portale di amministrazione sia dal portale per gli utenti, nel pannello Panoramica caricamento non riesce quando si seleziona il pannello Panoramica per gli account di archiviazione creati con una versione precedente dell'API (esempio: 2015-06-15). 

  In alternativa, usare PowerShell per eseguire la **Start-ResourceSynchronization.ps1** script per ripristinare l'accesso ai dettagli dell'account di archiviazione. [Lo script è disponibile da GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)e deve eseguire con le credenziali di amministratore del servizio nell'host di kit di sviluppo, se si utilizza il ASDK.  

- Il **integrità dei servizi** pannello non viene caricato. Quando si apre il pannello del servizio integrità nel portale di amministratore o utente, Azure Stack viene visualizzato un errore e non carica informazioni. Si tratta di un comportamento previsto. Anche se è possibile selezionare e aprire l'integrità del servizio, questa funzionalità non è ancora disponibile, ma verrà implementata in una futura versione di Azure Stack.


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

- Nel portale di amministrazione di Azure Stack, è possibile notare un avviso critico con il nome **scadenza certificato esterno imminente**.  Questo avviso può essere tranquillamente ignorato e sulle operazioni di Azure Stack Development Kit. 


#### <a name="marketplace"></a>Marketplace
- Gli utenti possono esplorare il marketplace completo senza una sottoscrizione ed è possono visualizzare elementi amministrativi quali offerte e piani. Questi elementi sono non funzionale per gli utenti.
 
#### <a name="compute"></a>Calcolo
- Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile usare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario usare il `-Name` parametro anziché `-VMScaleSetName`.

- Quando si creano macchine virtuali nel portale utenti Azure Stack, il portale Visualizza un numero errato di dischi dati che è possibile collegare a una VM serie DS. Le VM serie DS può supportare tutti i dischi dati come la configurazione di Azure.

- Quando un'immagine di macchina virtuale ha esito negativo deve essere creato, un elemento non riuscito che non è possibile eliminare potrebbe essere aggiunto al pannello calcolo immagini di macchina virtuale.

  In alternativa, creare una nuova immagine di macchina virtuale con un disco rigido virtuale fittizio che può essere creato tramite Hyper-V (New-VHD-percorso C:\dummy.vhd-predefinito - SizeBytes 1 GB). Questo processo dovrebbe risolvere il problema che impedisce l'eliminazione dell'elemento non riuscito. Quindi, 15 minuti dopo la creazione dell'immagine fittizio, è possibile correttamente eliminarlo.

  È quindi possibile provare a scaricare di nuovo l'immagine di macchina virtuale che in precedenza non è riuscita.

-  Se il provisioning di un'estensione in una distribuzione della VM impiega troppo tempo, gli utenti devono consentire il timeout provisioning anziché tentare di arrestare il processo per deallocare o eliminare la macchina virtuale.  

- <!-- 1662991 --> Diagnostica delle VM di Linux non è supportata in Azure Stack. Quando si distribuisce una VM Linux con abilitata la diagnostica della macchina virtuale, la distribuzione ha esito negativo. La distribuzione ha esito negativo anche se si abilitano le metriche di base della VM Linux tramite le impostazioni di diagnostica. 


#### <a name="networking"></a>Rete
- Sotto **Networking**, se si fa clic su **connessione** per configurare una connessione VPN, **VNet-to-VNet** viene elencato come un tipo di connessione disponibili. Non selezionare questa opzione. Attualmente, solo il **Site-to-site (IPsec)** opzione è supportata.

- Dopo che una macchina virtuale viene creata e associata a un indirizzo IP pubblico, si non è possibile annullare l'associazione di tale macchina virtuale da quell'indirizzo IP. Dissociazione sembra funzionare, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato alla VM originale.

  Attualmente, è necessario usare solo nuovi indirizzi IP pubblici per nuove VM eventualmente create.

  Questo comportamento si verifica anche se si riassegna una nuova macchina virtuale l'indirizzo IP (noto come un *scambio VIP*). Tutti i successivi tentativi di connettersi tramite il risultato di indirizzi IP in una connessione alla macchina virtuale di origine associata e non a quello nuovo.



- Azure Stack supporta un'unica *gateway di rete locale* per ogni indirizzo IP. Questo vale per tutte le sottoscrizioni tenant. Dopo la creazione della prima rete locale connessione gateway, successivi tentativi di creare una risorsa gateway di rete locale con lo stesso indirizzo IP vengono bloccate.

- In una rete virtuale che è stato creato con l'impostazione Server DNS *automatica*, la modifica di un errore di Server DNS personalizzato. Le impostazioni aggiornate non vengono inviate alle macchine virtuali in tale rete virtuale.
 
- Azure Stack non supporta l'aggiunta di interfacce di rete aggiuntiva a un'istanza di macchina virtuale dopo aver distribuita la macchina virtuale. Se la macchina virtuale richiede più di un'interfaccia di rete, devono essere definite in fase di distribuzione.



#### <a name="sql-and-mysql"></a>SQL e MySQL 
- Possono volerci fino a un'ora prima che gli utenti possono creare i database in un nuovo SKU MySQL o SQL.

- Il database che ospita i server deve essere dedicato per l'utilizzo di risorse utente e i provider dei carichi di lavoro. È possibile usare un'istanza che è usata da qualsiasi altro consumer, inclusi i servizi App.

- <!-- IS, ASDK --> Caratteri speciali, tra cui spazi e i periodi, non sono supportati nel **famiglia** oppure **livello** nomi quando si crea uno SKU per i provider di risorse SQL e MySQL.

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
