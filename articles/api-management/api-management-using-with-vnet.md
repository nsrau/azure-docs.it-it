---
title: Come usare Gestione API di Azure con le reti virtuali
description: Informazioni su come configurare una connessione a una rete virtuale in Gestione API di Azure e usarla per accedere ai servizi Web.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/22/2020
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 67b855beae4ea36bf4ef7e6bb396c6b7ae1d89e3
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874340"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Come usare Gestione API di Azure con le reti virtuali
Le reti virtuali di Azure (VNET) consentono di posizionare le risorse di Azure in una rete instradabile non Internet a cui si controlla l'accesso. Queste reti possono quindi essere connesse alle reti locali usando diverse tecnologie VPN. Per altre informazioni sulle reti virtuali di Azure, è possibile iniziare dalla [Panoramica sulla rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

Gestione API di Azure può essere distribuito all'interno della rete virtuale (VNET) in modo che possa accedere ai servizi di back-end all'interno della rete. Il portale per sviluppatori e il gateway dell'API possono essere configurati in modo che siano accessibili da Internet o solo all'interno della rete virtuale.

> [!NOTE]
> L'URL del documento di importazione API deve essere ospitato in un indirizzo Internet accessibile pubblicamente.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi descritti in questo articolo, è necessario disporre di:

+ Una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Un'istanza di Gestione API. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>Abilitare la connessione VNET

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Abilitare la connettività di rete virtuale usando il portale di Azure

1. Passare al [portale di Azure](https://portal.azure.com) per trovare l'istanza di Gestione API. Cercare e selezionare **Servizi Gestione API**.

2. Scegliere l'istanza di Gestione API.

3. Selezionare **Rete virtuale**.
4. Configurare l'istanza di Gestione API da distribuire all'interno di una rete virtuale.

    ![Menu della rete virtuale di Gestione API][api-management-using-vnet-menu]
5. Selezionare il tipo di accesso da usare:

    * **Off**: Questa è la modalità predefinita. Gestione API non viene distribuito in una rete virtuale.

    * **Esterna**: il gateway di Gestione API e il portale per gli sviluppatori sono accessibili dalla rete internet pubblica tramite un servizio di bilanciamento del carico esterno. Il gateway può accedere alle risorse all'interno della rete virtuale.

        ![Peering pubblico][api-management-vnet-public]

    * **Interna**: il gateway di Gestione API e il portale per gli sviluppatori sono accessibili soltanto dalla rete virtuale tramite un servizio di bilanciamento del carico interno. Il gateway può accedere alle risorse all'interno della rete virtuale.

        ![Peering privato][api-management-vnet-private]

6. Se si seleziona l'opzione **Esterna** o **Interna**, verrà visualizzato un elenco di tutte le aree in cui viene eseguito il provisioning del servizio Gestione API. Scegliere una **Posizione** e quindi selezionare la **rete virtuale** e la **subnet** corrispondenti. L'elenco di reti virtuali viene popolato con le reti virtuali classiche e Resource Manager disponibili nelle sottoscrizioni di Azure, impostate nell'area che si sta configurando.

    > [!IMPORTANT]
    > Quando si distribuisce un'istanza di gestione API di Azure a una rete virtuale Resource Manager, il servizio deve essere in una subnet dedicata che non contiene altre risorse, a eccezione di istanze di gestione API di Azure. Se si tenta di distribuire un'istanza di gestione API di Azure a una subnet della rete virtuale Resource Manager contenente altre risorse, la distribuzione avrà esito negativo.

    Selezionare **Applica**. La pagina **Rete virtuale** dell'istanza di Gestione API viene aggiornata con le nuove opzioni della rete virtuale e della subnet.

    ![Selezionare una VPN][api-management-setup-vpn-select]

7. Nella barra di spostamento superiore selezionare **Salva** e quindi selezionare **Applica configurazione di rete**.

> [!NOTE]
> L'indirizzo VIP dell'istanza di Gestione API può cambiare ogni volta che la rete virtuale viene abilitata o disabilitata.
> L'indirizzo VIP verrà modificato anche quando Gestione API passa da **Esterna** a **Interna** o viceversa.
>

> [!IMPORTANT]
> Se si rimuove Gestione API da una rete virtuale o si modifica quella in cui è distribuito, la rete virtuale usata in precedenza può rimanere bloccata fino a sei ore. Durante questo periodo non sarà possibile eliminare la rete virtuale o distribuirvi una nuova risorsa. Questo comportamento è valido per i client che usano api-version 2018-01-01 e versioni precedenti. Per i client che usano api-version 2019-01-01 e versioni successive, la rete virtuale viene liberata non appena viene eliminato il servizio Gestione API associato.

## <a name="enable-vnet-connection-using-powershell-cmdlets"></a><a name="enable-vnet-powershell"> </a>Abilitare la connessione della rete virtuale usando i cmdlet di PowerShell
È inoltre possibile abilitare la connettività della rete virtuale utilizzando i cmdlet di PowerShell.

* **Creare un servizio Gestione API all'interno di una rete virtuale**: usare il cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) per creare un servizio Gestione API di Azure all'interno di una rete virtuale.

* **Distribuire un servizio Gestione API esistente all'interno di una rete virtuale**: usare il cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) per spostare un servizio Gestione API di Azure esistente all'interno di una rete virtuale.

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Connettersi a un servizio Web ospitato all'interno di una rete virtuale
Dopo che il servizio Gestione API è stato connesso alla VNET, l'accesso ai servizi di back-end all'interno della rete virtuale non è diverso dall'accesso ai servizi pubblici. È sufficiente digitare l'indirizzo locale o il nome host (se è stato configurato un server DNS per la VNET) del servizio Web nel campo **URL del servizio Web** quando si crea una nuova API o se ne modifica una esistente.

![Aggiungere un'API dalla VPN][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Problemi comuni di configurazione di rete
Di seguito è riportato un elenco di problemi di configurazione comuni che possono verificarsi durante la distribuzione del servizio Gestione API in una rete virtuale.

* **Installazione di server DNS personalizzata**: il servizio Gestione API dipende da vari servizi di Azure. Quando Gestione API è ospitata in una rete virtuale con un server DNS personalizzato, deve risolvere i nomi host dei servizi di Azure. Vedere [queste](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) informazioni aggiuntive sulla configurazione del DNS personalizzato. Vedere la tabella delle porte e altri requisiti di rete per riferimento.

> [!IMPORTANT]
> Se si intende usare un server DNS personalizzato per la rete virtuale, è consigliabile impostarlo **prima** di distribuirvi un servizio Gestione API. In caso contrario è necessario aggiornare il servizio Gestione API ogni volta che si modifica il server DNS eseguendo [l'operazione di applicazione della configurazione di rete](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates)

* **Porte necessarie per il servizio Gestione API**: il traffico in ingresso e in uscita nella subnet in cui viene distribuito Gestione delle API può essere controllato usando il [gruppo di sicurezza di rete][Network Security Group]. Se una qualsiasi di queste porte non è disponibile, Gestione API potrebbe non funzionare correttamente e potrebbe diventare inaccessibile. Il blocco di una o più di tali porte è un problema di configurazione comune nell'uso di Gestione API in una rete virtuale.

<a name="required-ports"> </a> Quando un'istanza del servizio Gestione API è ospitata in una rete virtuale, vengono usate le porte indicate nella tabella seguente.

| Porte di origine/destinazione | Direction          | Protocollo di trasporto |   [Tag di servizio](../virtual-network/security-overview.md#service-tags) <br> Origine/Destinazione   | Scopo (\*)                                                 | Tipo di rete virtuale |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | In ingresso            | TCP                | INTERNET / VIRTUAL_NETWORK            | Comunicazione tra client e Gestione API                      | Esterno             |
| */3443                     | In ingresso            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Endpoint di gestione per il portale di Azure e PowerShell         | Esterno e interno  |
| * / 443                  | In uscita           | TCP                | VIRTUAL_NETWORK / Storage             | **Dipendenza da Archiviazione di Azure**                             | Esterno e interno  |
| * / 443                  | In uscita           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) (dove applicabile)                   | Esterno e interno  |
| * / 1433                     | In uscita           | TCP                | VIRTUAL_NETWORK/SQL                 | **Accesso agli endpoint SQL di Azure**                           | Esterno e interno  |
| * / 5671, 5672, 443          | In uscita           | TCP                | VIRTUAL_NETWORK / EventHub            | Dipendenza per il criterio [Registra a Hub eventi](api-management-howto-log-event-hubs.md) e l'agente di monitoraggio | Esterno e interno  |
| * / 445                      | In uscita           | TCP                | VIRTUAL_NETWORK / Storage             | Dipendenza dalla condivisione file di Azure per [GIT](api-management-configuration-repository-git.md)                      | Esterno e interno  |
| */443, 12000                     | In uscita           | TCP                | VIRTUAL_NETWORK / AzureCloud            | Estensione Health and Monitoring         | Esterno e interno  |
| */1886, 443                     | In uscita           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Pubblicare [log e metriche di diagnostica](api-management-howto-use-azure-monitor.md), [integrità risorse](../service-health/resource-health-overview.md) e [Application Insights](api-management-howto-app-insights.md)                   | Esterno e interno  |
| */25, 587, 25028                       | In uscita           | TCP                | VIRTUAL_NETWORK / INTERNET            | Connessione al server di inoltro SMTP per l'invio di messaggi di posta elettronica                    | Esterno e interno  |
| * / 6381 - 6383              | In ingresso e in uscita | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Accedere al servizio Redis per i criteri di [cache](api-management-caching-policies.md) tra computer         | Esterno e interno  |
| */4290              | In ingresso e in uscita | UDP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Contatori di sincronizzazione per i criteri relativi ai [limiti di frequenza](api-management-access-restriction-policies.md#LimitCallRateByKey) tra computer         | Esterno e interno  |
| * / *                        | In ingresso            | TCP                | VIRTUAL_NETWORK/AZURE_LOADBALANCER | Bilanciamento del carico di infrastruttura di Azure                          | Esterno e interno  |

>[!IMPORTANT]
> Le porte per cui *Scopo* è **grassetto** sono necessarie per la corretta distribuzione del servizio Gestione API. Bloccando le altre porte, tuttavia, si verificherà una **riduzione** della capacità di utilizzo e **monitoraggio del servizio in esecuzione e di fornire il contratto di servizio con commit**.

+ **Funzionalità TLS**: per abilitare la creazione e la convalida della catena di certificati TLS/SSL, il servizio Gestione API richiede la connettività di rete in uscita a ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com. Questa dipendenza non è necessaria se un certificato caricato in Gestione API contiene l'intera catena per la radice dell'autorità di certificazione.

+ **Accesso a DNS**: L'accesso in uscita sulla porta 53 è necessario per la comunicazione con i server DNS. Se è presente un server DNS personalizzato all'altra estremità di un gateway VPN, il server DNS deve essere raggiungibile dalla subnet che ospita Gestione API.

+ **Metriche e monitoraggio dell'integrità**: la connettività di rete in uscita agli endpoint di Monitoraggio di Azure, che si risolve nei domini seguenti. Come illustrato nella tabella, questi URL sono rappresentati nel tag del servizio AzureMonitor per l'uso con i gruppi di sicurezza di rete.

    | Ambiente Azure | Endpoint                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>gcs.prod.monitoring.core.windows.net(**nuovo**)</li><li>prod.warmpath.msftcloudes.com(**da deprecare**)</li><li>global.prod.microsoftmetrics.com(**nuovo**)</li><li>global.metrics.nsatc.net(**da deprecare**)</li><li>shoebox2.prod.microsoftmetrics.com(**nuovo**)</li><li>shoebox2.metrics.nsatc.net(**da deprecare**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**nuovo**)</li><li>prod3.metrics.nsatc.net(**da deprecare**)</li><li>prod3-black.prod.microsoftmetrics.com(**nuovo**)</li><li>prod3-black.prod3.metrics.nsatc.net(**da deprecare**)</li><li>prod3-red.prod.microsoftmetrics.com(**nuovo**)</li><li>prod3-red.prod3.metrics.nsatc.net(**da deprecare**)</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com(**nuovo**)</li><li>global.metrics.nsatc.net(**da deprecare**)</li><li>shoebox2.prod.microsoftmetrics.com(**nuovo**)</li><li>shoebox2.metrics.nsatc.net(**da deprecare**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**nuovo**)</li><li>prod3.metrics.nsatc.net(**da deprecare**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | 21Vianet per Azure Cina     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com(**nuovo**)</li><li>global.metrics.nsatc.net(**da deprecare**)</li><li>shoebox2.prod.microsoftmetrics.com(**nuovo**)</li><li>shoebox2.metrics.nsatc.net(**da deprecare**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**nuovo**)</li><li>prod3.metrics.nsatc.net(**da deprecare**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > La modifica dei cluster sopra indicati con zona DNS **.nsatc.net** a **.microsoftmetrics.com** è principalmente una modifica DNS. L'indirizzo IP del cluster non verrà modificato.

+ **Tag del servizio locale**: le regole NSG che consentono la connettività in uscita ai tag di servizio di archiviazione, SQL e hub eventi possono usare le versioni locali dei tag corrispondenti all'area che contiene l'istanza di Gestione API (ad esempio, Storage.WestUS per un'istanza di Gestione API nell'area Stati Uniti occidentali). Nelle distribuzioni in più aree, il gruppo di sicurezza di rete in ogni area deve consentire il traffico ai tag del servizio per tale area e l'area primaria.

+ **Inoltro SMTP**: connettività di rete in uscita per il server di inoltro SMTP, che si risolve nell'host `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` e `ies.global.microsoft.com`

+ **CAPTCHA del portale per sviluppatori**: connettività di rete in uscita per il CAPTCHA del portale degli sviluppatori, che si risolve negli host `client.hip.live.com` e `partner.hip.live.com`.

+ **Diagnostica del portale di Azure**: per abilitare il flusso dei log di diagnostica dal portale di Azure quando si usa l'estensione di Gestione API dall'interno di una rete virtuale, è richiesto l'accesso in uscita a `dc.services.visualstudio.com` sulla porta 443. Ciò consente di risolvere eventuali problemi che potrebbero verificarsi quando si usa l'estensione.

+ **Azure Load Balancer**: consentire le richieste in ingresso dal tag di servizio `AZURE_LOAD_BALANCER` non è un requisito per lo SKU `Developer`, poiché viene distribuita solo un'unità di calcolo. Tuttavia, consentire le richieste in ingresso da [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) diventa cruciale quando si passa a uno SKU superiore, ad esempio `Premium`, perché in caso di errore di un probe di integrità da Load Balancer, la distribuzione ha esito negativo.

+ **Application Insights**: se il monitoraggio di [applicazione Azure Insights](api-management-howto-app-insights.md) è abilitato in gestione API, è necessario consentire la connettività in uscita all' [endpoint di telemetria](/azure/azure-monitor/app/ip-addresses#outgoing-ports) dalla rete virtuale. 

+ **Forzare il tunneling del traffico al firewall locale usando ExpressRoute o un'appliance virtuale di rete**: Una configurazione comune dei clienti prevede la definizione di una route predefinita (0.0.0.0/0) personalizzata che forza tutto il traffico dalla subnet delegata di Gestione API a passare attraverso un firewall locale o a un'appliance virtuale di rete. Questo flusso di traffico interrompe sempre la connettività con Gestione API di Azure perché il traffico in uscita è bloccato in locale o convertito tramite NAT in un set non riconoscibile di indirizzi che non usano più i diversi endpoint di Azure. Per la soluzione è necessario eseguire alcune operazioni:

  * Abilitare gli endpoint di servizio nella subnet in cui è distribuito il servizio Gestione API. Gli [endpoint di servizio][ServiceEndpoints] devono essere abilitati per SQL di Azure, Archiviazione di Azure, Hub eventi di Azure e Bus di servizio di Azure. L'abilitazione di endpoint direttamente dalla subnet delegata di Gestione API per questi servizi consente loro di usare la rete backbone di Microsoft Azure che fornisce il routing ottimale per il traffico del servizio. Se si usano gli endpoint servizio con Gestione API con tunneling forzato, il tunneling del traffico dei servizi di Azure sopra indicati non viene forzato. L'altro traffico relativo alle dipendenze del servizio Gestione API viene sottoposto a tunneling forzato e non può andare perso. In caso contrario, il servizio Gestione API non funzionerà correttamente.
    
  * Tutto il traffico del piano di controllo da Internet all'endpoint di gestione del servizio Gestione API viene instradato attraverso un set specifico di indirizzi IP in ingresso ospitati da Gestione API. In caso di tunneling forzato del traffico, le risposte non verranno mappate in modo simmetrico a questi indirizzi IP di origine in ingresso. Per ovviare alla limitazione, è necessario aggiungere le route definite dall'utente ([UDR][UDRs]) seguenti per indirizzare il traffico ad Azure impostando la destinazione di queste route host su "Internet". Il set di indirizzi IP in ingresso per il traffico del piano di controllo è documentato in [Indirizzi IP del piano di controllo](#control-plane-ips)

  * Per le altre dipendenze del servizio Gestione API con tunneling forzato, è necessario un modo per risolvere il nome host e raggiungere l'endpoint. I metodi disponibili includono i seguenti:
      - Metriche e monitoraggio dell'integrità
      - Diagnostica del portale di Azure
      - SMTP Relay
      - CAPTCHA del portale per sviluppatori

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Risoluzione dei problemi
* **Installazione iniziale**: quando la distribuzione iniziale del servizio Gestione API in una subnet non ha esito positivo, è consigliabile distribuire prima una macchina virtuale nella stessa subnet. Successivamente, eseguire il desktop remoto nella macchina virtuale e verificare che sia presente la connettività a una delle risorse seguenti nella sottoscrizione di Azure
    * BLOB di Archiviazione di Azure
    * database SQL di Azure
    * Tabella di archiviazione di Azure

  > [!IMPORTANT]
  > Dopo aver convalidato la connettività, assicurarsi di rimuovere tutte le risorse distribuite nella subnet, prima di distribuire Gestione API nella subnet.

* **Aggiornamenti incrementali**: quando si apportano modifiche alla rete, fare riferimento all'[API NetworkStatus](/rest/api/apimanagement/2019-12-01/networkstatus) per verificare che il servizio Gestione API non abbia perso l'accesso ad alcuna delle risorse critiche da cui dipende. Lo stato della connettività dovrebbe essere aggiornato ogni 15 minuti.

* **Collegamenti di navigazione delle risorse**: quando si esegue la distribuzione in una subnet di macchina virtuale in stile Resource Manager, Gestione API riserva la subnet, creando un collegamento di navigazione delle risorse. Se la subnet contiene già una risorsa da un provider diverso, la distribuzione ha **esito negativo**. Quando, analogamente, si sposta un servizio Gestione API in una subnet diversa o lo si elimina, viene rimosso il collegamento di navigazione delle risorse.

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> Requisito per le dimensioni della subnet
Azure riserva alcuni indirizzi IP all'interno di ogni subnet e questi indirizzi non possono essere usati. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per motivi di conformità al protocollo, insieme ad altri tre indirizzi usati per i servizi di Azure. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oltre agli indirizzi IP usati dall'infrastruttura di rete virtuale di Azure, ogni istanza di Gestione API nella subnet usa due indirizzi IP per ogni unità dello SKU Premium o un indirizzo IP per lo SKU Developer. Ogni istanza riserva un indirizzo IP aggiuntivo per il bilanciamento del carico esterno. Quando si esegue la distribuzione in una rete virtuale interna, è necessario un indirizzo IP aggiuntivo per il bilanciamento del carico interno.

Nel caso del calcolo precedente, le dimensioni minime della subnet in cui è possibile distribuire Gestione API sono pari a /29, ovvero tre indirizzi IP utilizzabili.

Ogni unità di scala aggiuntiva di Gestione API richiede altri due indirizzi IP.

## <a name="routing"></a><a name="routing"> </a> Routing
+ Un indirizzo IP pubblico con bilanciamento del carico (VIP) viene riservato per consentire l'accesso a tutti gli endpoint di servizio.
+ Verranno usati un indirizzo IP di un intervallo IP di subnet (DIP) per accedere alle risorse all'interno della rete rituale e un indirizzo IP pubblico (VIP) per accedere alle risorse esterne alla rete virtuale.
+ L'indirizzo IP pubblico con bilanciamento del carico è disponibile nel pannello Panoramica/Informazioni di base del portale di Azure.

## <a name="limitations"></a><a name="limitations"> </a>Limitazioni
* Una subnet contenente le istanze di Gestione API non può contenere altri tipi di risorse di Azure.
* La subnet e il servizio di Gestione API devono essere nella stessa sottoscrizione.
* Una subnet contenente le istanze di gestione API non può essere spostata da una sottoscrizione all'altra.
* Per le distribuzioni di Gestione API su più aree in modalità rete virtuale interna configurata, gli utenti sono responsabili della gestione del bilanciamento del carico poiché sono titolari del routing.
* La connettività da una risorsa in una rete virtuale con peering a livello globale in un'altra area al servizio Gestione API in modalità interna non funzionerà a causa delle limitazioni della piattaforma. Per altre informazioni, vedere [Le risorse in una rete virtuale non possono comunicare con il servizio di bilanciamento del carico interno di Azure nella rete virtuale con peering](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a> Indirizzi IP del piano di controllo

Gli indirizzi IP sono divisi per **ambiente di Azure**. Quando si consente le richieste in ingresso, l'indirizzo IP contrassegnato con **globale** deve essere consentito insieme all'indirizzo IP specifico dell' **area** .

| **Ambiente di Azure**|   **Area**|  **Indirizzo IP**|
|-----------------|-------------------------|---------------|
| Azure Public| Stati Uniti centro-meridionali (globale)| 104.214.19.224|
| Azure Public| Stati Uniti centro-settentrionali (globale)| 52.162.110.80|
| Azure Public| Stati Uniti centro-occidentali| 52.253.135.58|
| Azure Public| Corea centrale| 40.82.157.167|
| Azure Public| Regno Unito occidentale| 51.137.136.0|
| Azure Public| Giappone occidentale| 40.81.185.8|
| Azure Public| Stati Uniti centro-settentrionali| 40.81.47.216|
| Azure Public| Regno Unito meridionale| 51.145.56.125|
| Azure Public| India occidentale| 40.81.89.24|
| Azure Public| Stati Uniti orientali| 52.224.186.99|
| Azure Public| Europa occidentale| 51.145.179.78|
| Azure Public| Giappone orientale| 52.140.238.179|
| Azure Public| Francia centrale| 40.66.60.111|
| Azure Public| Canada orientale| 52.139.80.117|
| Azure Public| Emirati Arabi Uniti settentrionali| 20.46.144.85|
| Azure Public| Brasile meridionale| 191.233.24.179|
| Azure Public| Brasile sudorientale| 191.232.18.181|
| Azure Public| Asia sud-orientale| 40.90.185.46|
| Azure Public| Sudafrica settentrionale| 102.133.130.197|
| Azure Public| Canada centrale| 52.139.20.34|
| Azure Public| Corea meridionale| 40.80.232.185|
| Azure Public| India centrale| 13.71.49.1|
| Azure Public| Stati Uniti occidentali| 13.64.39.16|
| Azure Public| Australia sud-orientale| 20.40.160.107|
| Azure Public| Australia centrale| 20.37.52.67|
| Azure Public| India meridionale| 20.44.33.246|
| Azure Public| Stati Uniti centrali| 13.86.102.66|
| Azure Public| Australia orientale| 20.40.125.155|
| Azure Public| Stati Uniti occidentali 2| 51.143.127.203|
| Azure Public| Stati Uniti orientali 2 EUAP| 52.253.229.253|
| Azure Public| Stati Uniti centrali EUAP| 52.253.159.160|
| Azure Public| Stati Uniti centro-meridionali| 20.188.77.119|
| Azure Public| Stati Uniti orientali 2| 20.44.72.3|
| Azure Public| Europa settentrionale| 52.142.95.35|
| Azure Public| Asia orientale| 52.139.152.27|
| Azure Public| Francia meridionale| 20.39.80.2|
| Azure Public| Svizzera occidentale| 51.107.96.8|
| Azure Public| Australia centrale 2| 20.39.99.81|
| Azure Public| Emirati Arabi Uniti centrali| 20.37.81.41|
| Azure Public| Svizzera settentrionale| 51.107.0.91|
| Azure Public| Sudafrica occidentale| 102.133.0.79|
| Azure Public| Germania centro-occidentale| 51.116.96.0|
| Azure Public| Germania settentrionale| 51.116.0.0|
| Azure Public| Norvegia orientale| 51.120.2.185|
| Azure Public| Norvegia occidentale| 51.120.130.134|
| 21Vianet per Azure Cina| Cina settentrionale (globale)| 139.217.51.16|
| 21Vianet per Azure Cina| Cina orientale (globale)| 139.217.171.176|
| 21Vianet per Azure Cina| Cina settentrionale| 40.125.137.220|
| 21Vianet per Azure Cina| Cina orientale| 40.126.120.30|
| 21Vianet per Azure Cina| Cina settentrionale 2| 40.73.41.178|
| 21Vianet per Azure Cina| Cina orientale 2| 40.73.104.4|
| Azure Government| US Gov Virginia (globale)| 52.127.42.160|
| Azure Government| Governo degli Stati Uniti - Texas (globale)| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Texas| 52.243.154.118|
| Azure Government| Stati Uniti centrali DoD| 52.182.32.132|
| Azure Government| Stati uniti orientali DoD| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Contenuto correlato
* [Connessione di una rete virtuale al back-end tramite Gateway VPN](../vpn-gateway/design.md#s2smulti)
* [Connessione di una rete virtuale da modelli di distribuzione differenti](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Come usare Controllo API per tenere traccia delle chiamate in Gestione API di Azure](api-management-howto-api-inspector.md)
* [Domande frequenti sulla rete virtuale](../virtual-network/virtual-networks-faq.md)
* [Tag di servizio](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
