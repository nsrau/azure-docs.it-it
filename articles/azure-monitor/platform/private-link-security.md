---
title: Usare il collegamento privato di Azure per connettere in modo sicuro le reti a Monitoraggio di Azure
description: Usare il collegamento privato di Azure per connettere in modo sicuro le reti a Monitoraggio di Azure
author: nkiest
ms.author: nikiest
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: 42419247de537f9a166c3cdca2fd5a832ade6a5f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461431"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Usare il collegamento privato di Azure per connettere in modo sicuro le reti a Monitoraggio di Azure

[Collegamento privato di Azure](../../private-link/private-link-overview.md) consente di collegare in modo sicuro i servizi PaaS di Azure alla rete virtuale usando endpoint privati. Per molti servizi, è sufficiente configurare un endpoint per ogni risorsa. Tuttavia, Monitoraggio di Azure è un insieme di diversi servizi interconnessi che interagiscono tra loro per monitorare i carichi di lavoro. Di conseguenza, è stata creata una risorsa denominata ambito collegamento privato di Monitoraggio di Azure (AMPLS, Azure Monitor Private Link Scope) che consente di definire i limiti della rete di monitoraggio e connettersi alla rete virtuale. Questo articolo illustra quando usare e come configurare un ambito collegamento privato di Monitoraggio di Azure.

## <a name="advantages"></a>Vantaggi

Con il collegamento privato è possibile:

- Connettersi privatamente a Monitoraggio di Azure senza aprire alcun accesso alla rete pubblica
- Assicurarsi che l'accesso ai dati di monitoraggio avvenga solo tramite reti private autorizzate
- Impedire l’esfiltrazione di dati dalle reti private definendo specifiche risorse di Monitoraggio di Azure che si connettono tramite l'endpoint privato
- Connettere in modo sicuro la rete locale privata a Monitoraggio di Azure usando ExpressRoute e il collegamento privato
- Mantenere tutto il traffico all'interno della rete backbone di Microsoft Azure

Per altre informazioni, vedere [Vantaggi principali del collegamento privato](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Funzionamento

L'ambito collegamento privato di Monitoraggio di Azure è una risorsa di raggruppamento per connettere uno o più endpoint privati (e quindi le reti virtuali in cui sono contenuti) a una o più risorse di Monitoraggio di Azure. Le risorse includono le aree di lavoro Log Analytics e i componenti di Application Insights.

![Diagramma della topologia delle risorse](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> Una singola risorsa di Monitoraggio di Azure può appartenere a più AMPLS, ma non è possibile connettere una singola rete virtuale a più di un AMPLS. 

## <a name="planning-based-on-your-network"></a>Pianificazione basata sulla rete

Prima di configurare le risorse AMPLS, prendere in considerazione i requisiti di isolamento rete. Valutare l'accesso delle reti virtuali all’Internet pubblico e le restrizioni di accesso di ognuna delle risorse di Monitoraggio di Azure, ovvero i componenti di Application Insights e le aree di lavoro Log Analytics.

> [!NOTE]
> Le reti hub e spoke, o qualsiasi altra topologia di reti con peering, possono configurare un collegamento privato tra VNet Hub (Main) e le risorse di monitoraggio di Azure pertinenti, anziché configurare un collegamento privato per ogni VNet. Questa operazione è particolarmente sensata se le risorse di monitoraggio di Azure usate da tali reti sono condivise. Tuttavia, se si vuole consentire a ogni VNet di accedere a un set separato di risorse di monitoraggio, creare un collegamento privato a un AMPLS dedicato per ogni rete.

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>Valutare quali reti virtuali devono connettersi a un collegamento privato

Per iniziare, valutare quali reti virtuali hanno accesso limitato a Internet. Le reti virtuali con Internet gratuito potrebbero non richiedere un collegamento privato per accedere alle risorse di Monitoraggio di Azure. Le risorse di monitoraggio a cui si connettono le reti virtuali possono limitare il traffico in ingresso e richiedere una connessione di collegamento privato (per query o inserimento log). In questi casi, anche una rete virtuale che ha accesso all’Internet pubblico deve connettersi a queste risorse con un collegamento privato e tramite un AMPLS.

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>Valutare quali risorse di Monitoraggio di Azure devono avere un collegamento privato

Esaminare ogni risorsa di Monitoraggio di Azure:

- La risorsa deve consentire l'inserimento di log solo da risorse posizionate su determinate reti virtuali?
- È necessario che la risorsa venga sottoposta a query solo da client posizionati su determinate reti virtuali?

Se la risposta a una di queste domande è affermativa, impostare le restrizioni come illustrato in [Configurazione di aree di lavoro Log Analytics](#configure-log-analytics) e [Configurazione di componenti di Application Insights](#configure-application-insights), quindi associare tali risorse a una o più risorse AMPLS. Le reti virtuali che devono accedere a queste risorse di monitoraggio devono disporre di un endpoint privato che si connette alla risorsa AMPLS pertinente.
Tenere presente che è possibile connettere le stesse aree di lavoro o applicazioni a più risorse AMPLS per fare in modo che vengano raggiunte da reti diverse.

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>Raggruppare le risorse di monitoraggio in base all'accessibilità di rete

Poiché ogni rete virtuale può connettersi a una sola risorsa AMPLS, è necessario raggruppare le risorse di monitoraggio che devono essere accessibili alle stesse reti. Il modo più semplice per gestire questo raggruppamento consiste nel creare una risorsa AMPLS per ogni rete virtuale e selezionare le risorse per connettersi alla rete. Tuttavia, per ridurre le risorse e migliorare la gestibilità, è consigliabile riutilizzare una risorsa AMPLS per le varie reti.

Ad esempio, se le reti virtuali interne VNet1 e VNet2 devono connettersi alle aree di lavoro Workspace1 e Workspace2 e al componente di Application Insights Application Insights 3, associare tutte e tre le risorse alla stessa risorsa AMPLS. Se VNet3 deve accedere solo a Workspace1, creare un'altra risorsa AMPLS, associarvi Workspace1 e connettere VNet3 come illustrato nei diagrammi seguenti:

![Diagramma della topologia AMPLS A](./media/private-link-security/ampls-topology-a-1.png)

![Diagramma della topologia AMPLS B](./media/private-link-security/ampls-topology-b-1.png)

### <a name="consider-limits"></a>Considerare i limiti

Quando si pianifica la configurazione del collegamento privato, è necessario prendere in considerazione alcuni limiti:

* Un VNet può connettersi solo a un oggetto AMPLS. Ciò significa che l'oggetto AMPLS deve fornire l'accesso a tutte le risorse di monitoraggio di Azure a cui VNet deve avere accesso.
* Una risorsa di monitoraggio di Azure (area di lavoro o componente Application Insights) può connettersi al massimo 5 AMPLSs.
* Un oggetto AMPLS può connettersi alla maggior parte delle risorse di monitoraggio di Azure 50.
* Un oggetto AMPLS può connettersi al massimo da 10 endpoint privati.

Nella topologia seguente:
* Ogni VNet si connette a un oggetto AMPLS, quindi non è in grado di connettersi ad altri AMPLSs.
* AMPLS B si connette a 2 reti virtuali: utilizzando 2/10 delle possibili connessioni agli endpoint privati.
* AMPLS A si connette a 2 aree di lavoro e 1 componente Application Insights: uso di 3/50 delle risorse di monitoraggio di Azure possibili.
* L'area di lavoro 2 si connette a AMPLS A e AMPLS B: utilizzando 2/5 delle connessioni AMPLS possibili.

![Diagramma dei limiti di AMPLS](./media/private-link-security/ampls-limits.png)

## <a name="example-connection"></a>Connessione di esempio

Per iniziare, creare una risorsa ambito collegamento privato di Monitoraggio di Azure.

1. Andare a **Crea una risorsa** nel portale di Azure e cercare **Ambito collegamento privato di Monitoraggio di Azure**.

   ![Trova ambito collegamento privato di monitoraggio di Azure](./media/private-link-security/ampls-find-1c.png)

2. Fare clic su **Create** (Crea).
3. Selezionare un gruppo di sottoscrizioni e risorse.
4. Assegnare un nome alla risorsa AMPLS. È preferibile usare un nome che indichi chiaramente lo scopo e il limite di sicurezza per cui verrà usato l'ambito, in modo che qualcuno non interrompa accidentalmente i limiti di sicurezza della rete. Ad esempio, "AppServerProdTelem".
5. Fare clic su **Rivedi e crea**. 

   ![Creare un ambito di collegamento privato di monitoraggio di Azure](./media/private-link-security/ampls-create-1d.png)

6. Superare la convalida, quindi fare clic su **Crea**.

## <a name="connect-azure-monitor-resources"></a>Connettere le risorse di Monitoraggio di Azure

È possibile connettere la risorsa AMPLS prima a endpoint privati e quindi alle risorse di Monitoraggio di Azure o viceversa, ma il processo di connessione è più rapido se si inizia con le risorse di Monitoraggio di Azure. La connessione delle aree di lavoro Log Analytics e dei componenti di Application Insights di Monitoraggio di Azure avviene nel seguente modo

1. Nell'ambito collegamento privato di Monitoraggio di Azure fare clic su **Risorse di Monitoraggio di Azure** nel menu a sinistra. Fare clic su **Add** .
2. Aggiungere l'area di lavoro o il componente. Facendo clic sul pulsante **Aggiungi** viene visualizzata una finestra di dialogo in cui è possibile selezionare le risorse di Monitoraggio di Azure. È possibile esplorare le sottoscrizioni e i gruppi di risorse oppure digitarne il nome per filtrarli. Selezionare l'area di lavoro o il componente e fare clic su **Applica** per aggiungerli all'ambito.

    ![Screenshot di selezione dell’esperienza utente di ambito](./media/private-link-security/ampls-select-2.png)

### <a name="connect-to-a-private-endpoint"></a>Connettersi a un endpoint privato

Ora che sono disponibili delle risorse connesse alla risorsa AMPLS, creare un endpoint privato per connettere la rete. È possibile eseguire questa attività nel [Centro collegamento privato del portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) o all'interno dell'ambito collegamento privato di Monitoraggio di Azure, come in questo esempio.

1. Nella risorsa ambito fare clic su **Connessioni a endpoint privati** nel menu delle risorse a sinistra. Fare clic su **Endpoint privato** per avviare il processo di creazione dell'endpoint. È anche possibile approvare le connessioni avviate nel Centro collegamento privato selezionandole e facendo clic su **Approva**.

    ![Screenshot dell’esperienza utente di connessioni all'endpoint privato](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Selezionare la sottoscrizione, il gruppo di risorse, il nome dell'endpoint e l'area in cui deve risiedere. L'area deve essere la stessa area della rete virtuale a cui verrà connesso.

3. Fare clic su **Avanti: Risorsa**. 

4. Nella schermata Risorsa,

   a. Selezionare la **Sottoscrizione** che contiene la risorsa ambito privato di Monitoraggio di Azure. 

   b. Per il **tipo di risorsa**, scegliere **Microsoft.insights/privateLinkScopes**. 

   c. Dal menu a discesa **Risorsa** scegliere l’ambito collegamento privato creato in precedenza. 

   d. Fare clic su **Avanti: Configurazione >** .
      ![Screenshot di selezione di Crea endpoint privato](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. Nel riquadro di configurazione,

   a.    Scegliere la **rete virtuale** e la **subnet** da connettere alle risorse di Monitoraggio di Azure. 
 
   b.    Scegliere **Sì** per **Integra con la zona DNS privato** e consentire la creazione automatica di una nuova zona DNS privato. Le zone DNS effettive potrebbero essere diverse da quelle illustrate nella schermata seguente. 
 
   c.    Fare clic su **Rivedi e crea**.
 
   d.    Superare la convalida. 
 
   e.    Fare clic su **Crea**. 

    ![Screenshot di selezione di Crea endpoint privato 2](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

A questo punto è stato creato un nuovo endpoint privato connesso a questo ambito collegamento privato di Monitoraggio di Azure.

## <a name="configure-log-analytics"></a>Configurare Log Analytics

Accedere al portale di Azure. Nella risorsa dell'area di lavoro Log Analytics è presente una voce di menu **isolamento rete** sul lato sinistro. Da questo menu è possibile controllare due stati diversi. 

![Isolamento rete LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

Innanzitutto, è possibile connettere questa risorsa Log Analytics a qualsiasi ambito collegamento privato di Monitoraggio di Azure a cui si ha accesso. Fare clic su **Aggiungi** e selezionare l'ambito collegamento privato di Monitoraggio di Azure.  Fare clic su **Applica** per connetterlo. Tutti gli ambiti connessi vengono visualizzati in questa schermata. Se si effettua questa connessione, il traffico di rete nelle reti virtuali connesse raggiungerà questa area di lavoro. Questa connessione ha lo stesso effetto della connessione dall'ambito effettuata in [Connessione delle risorse di Monitoraggio di Azure](#connect-azure-monitor-resources).  

In secondo luogo, è possibile controllare il modo in cui questa risorsa può essere raggiunta dall'esterno degli ambiti collegamento privato elencati in precedenza. Se si imposta **Consenti l'accesso alla rete pubblica per l’inserimento** su **No**, i computer esterni agli ambiti connessi non possono caricare dati in questa area di lavoro. Se si imposta **Consenti l'accesso alla rete pubblica per le query** su **No**, i computer esterni agli ambiti non possono accedere ai dati in questa area di lavoro. Questi dati includono l'accesso a cartelle di lavoro, dashboard, esperienze client basate su API di query, informazioni dettagliate nel portale di Azure e altro ancora. Le esperienze in esecuzione al di fuori dell'portale di Azure e la query Log Analytics dati devono essere in esecuzione anche all'interno di VNET collegati a privati.

La limitazione dell'accesso in questo modo non si applica al Azure Resource Manager e pertanto presenta le limitazioni seguenti:
* Accesso ai dati: quando si bloccano le query dalle reti pubbliche si applicano alla maggior parte dei Log Analytics esperienze, alcune esperienze eseguono query sui dati tramite Azure Resource Manager e pertanto non saranno in grado di eseguire query sui dati, a meno che non vengano applicate anche le impostazioni di collegamento privato alla Gestione risorse (la funzionalità sarà presto disponibile). Sono incluse, ad esempio, le soluzioni di monitoraggio di Azure, le cartelle di lavoro e le informazioni dettagliate e il connettore LogicApp.
* Gestione dell'area Azure Resource Manager di lavoro: le modifiche apportate alla configurazione e all'impostazione dell'area di lavoro (inclusa l'attivazione o la disattivazione delle impostazioni di accesso Limitare l'accesso alla gestione delle aree di lavoro utilizzando i ruoli, le autorizzazioni, i controlli di rete e il controllo appropriati. Per altre informazioni, vedere [Ruoli, autorizzazioni e sicurezza di Monitoraggio di Azure](roles-permissions-security.md).

> [!NOTE]
> I log e le metriche caricati in un'area di lavoro tramite [Impostazioni di diagnostica](diagnostic-settings.md) passano attraverso un canale Microsoft privato protetto e non sono controllati da queste impostazioni.

### <a name="log-analytics-solution-packs-download"></a>Download di Log Analytics Solution Pack

Per consentire all'agente di Log Analytics di scaricare i pacchetti di soluzioni, aggiungere i nomi di dominio completi appropriati all'elenco di indirizzi consentiti del firewall. 


| Ambiente cloud | Risorsa agente | Porte | Direction |
|:--|:--|:--|:--|
|Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | In uscita
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  In uscita
|21Vianet per Azure Cina      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | In uscita

## <a name="configure-application-insights"></a>Configura Application Insights

Accedere al portale di Azure. Nella risorsa componente di Application Insights di Monitoraggio di Azure è disponibile una voce di menu **Isolamento rete** sul lato sinistro. Da questo menu è possibile controllare due stati diversi.

![Isolamento rete intelligenza artificiale](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Innanzitutto, è possibile connettere questa risorsa Application Insights agli ambiti collegamento privato di Monitoraggio di Azure a cui si ha accesso. Fare clic su **Aggiungi** e selezionare l'**ambito collegamento privato di Monitoraggio di Azure**. Fare clic su Applica per connetterlo. Tutti gli ambiti connessi vengono visualizzati in questa schermata. Se si effettua questa connessione, il traffico di rete nelle reti virtuali connesse raggiungerà questo componente. Questa connessione ha lo stesso effetto della connessione dall'ambito effettuata in [Connessione delle risorse di Monitoraggio di Azure](#connect-azure-monitor-resources). 

In secondo luogo, è possibile controllare il modo in cui questa risorsa può essere raggiunta dall'esterno degli ambiti collegamento privato elencati in precedenza. Se si imposta **Consenti l'accesso alla rete pubblica per l’inserimento** su **No**, i computer o gli SDK esterni agli ambiti connessi non possono caricare dati in questo componente. Se si imposta **Consenti l'accesso alla rete pubblica per le query** su **No**, i computer esterni agli ambiti non possono accedere ai dati in questa risorsa Application Insights. Questi dati includono l'accesso ai log APM, alle metriche e al flusso metriche attive, oltre a esperienze integrate come cartelle di lavoro, dashboard, esperienze client basate su API di query, informazioni dettagliate nel portale di Azure e altro ancora. 

Si noti che le esperienze di utilizzo esterne al portale devono essere eseguite anche all'interno della rete virtuale con collegamento privato che include i carichi di lavoro monitorati. 

Sarà necessario aggiungere le risorse che eseguono l’hosting dei carichi di lavoro monitorati al collegamento privato. La [documentazione](../../app-service/networking/private-endpoint.md) che illustra come eseguire questa operazione per Servizi app è disponibile qui.

Questo tipo di limitazione dell'accesso si applica solo ai dati nella risorsa Application Insights. Le modifiche alla configurazione, inclusa l'attivazione o la disattivazione di queste impostazioni di accesso, vengono gestite da Azure Resource Manager. In alternativa, limitare l'accesso a Resource Manager utilizzando i ruoli, le autorizzazioni, i controlli di rete e il controllo appropriati. Per altre informazioni, vedere [Ruoli, autorizzazioni e sicurezza di Monitoraggio di Azure](roles-permissions-security.md).

> [!NOTE]
> Per proteggere in modo completo Application Insights basato sull'area di lavoro, è necessario bloccare l'accesso alla risorsa Application Insights e all'area di lavoro Log Analytics sottostante.
>
> Per la diagnostica a livello di codice (Profiler/Debugger) è necessario fornire un proprio account di archiviazione per il supporto del collegamento privato. Ecco la [documentazione](../app/profiler-bring-your-own-storage.md) per informazioni su come eseguire questa operazione.

## <a name="use-apis-and-command-line"></a>Usare le API e la riga di comando

È possibile automatizzare il processo descritto in precedenza utilizzando le interfacce della riga di comando e i modelli di Azure Resource Manager.

Per creare e gestire ambiti collegamento privato, usare [az monitor private-link-scope](/cli/azure/monitor/private-link-scope?view=azure-cli-latest). Con questo comando è possibile creare ambiti, associare aree di lavoro Log Analytics e componenti Application Insights, nonché aggiungere/rimuovere/approvare endpoint privati.

Per gestire l'accesso alla rete, usare i flag `[--ingestion-access {Disabled, Enabled}]` e `[--query-access {Disabled, Enabled}]` in [Aree di lavoro Log Analytics](/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest) o [Componenti Application Insights](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest).

## <a name="collect-custom-logs-over-private-link"></a>Raccogli log personalizzati sul collegamento privato

Gli account di archiviazione vengono usati nel processo di inserimento dei log personalizzati. Per impostazione predefinita, vengono usati gli account di archiviazione gestiti dal servizio. Tuttavia, per inserire i log personalizzati nei collegamenti privati, è necessario usare gli account di archiviazione personali e associarli alle aree di lavoro Log Analytics. Per ulteriori informazioni su come configurare tali account, utilizzare la [riga di comando](/cli/azure/monitor/log-analytics/workspace/linked-storage?view=azure-cli-latest).

Per altre informazioni sull'uso di account di archiviazione personali, vedere [Account di archiviazione di proprietà del cliente per l'inserimento dei log](private-storage.md)

## <a name="restrictions-and-limitations"></a>Restrizioni e limitazioni

### <a name="agents"></a>Agenti

Le versioni più recenti degli agenti Windows e Linux devono essere usate nelle reti private per consentire l'inserimento sicuro per le aree di lavoro Log Analytics. Le versioni precedenti non possono caricare i dati di monitoraggio in una rete privata.

**Agente Windows di Log Analytics**

Usare l'agente di Log Analytics versione 10.20.18038.0 o successiva.

**Agente Linux di Log Analytics**

Usare la versione dell'agente 1.12.25 o successiva. Se non è possibile, eseguire i comandi seguenti nella macchina virtuale.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Portale di Azure

Per usare le esperienze del portale di Monitoraggio di Azure, ad esempio Application Insights e Log Analytics, è necessario consentire l'accesso alle estensioni del portale di Azure e di Monitoraggio di Azure nelle reti private. Aggiungere i [tag del servizio](../../firewall/service-tags.md) **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor. FirstParty**e **AzureFrontDoor. frontend** al gruppo di sicurezza di rete.

### <a name="programmatic-access"></a>Accesso a livello di codice

Per usare l’API REST, la [CLI](/cli/azure/monitor?view=azure-cli-latest) o PowerShell con Monitoraggio di Azure nelle reti private,  aggiungere le [tag del servizio](../../virtual-network/service-tags-overview.md) **AzureActiveDirectory** e **AzureResourceManager** al firewall.

L'aggiunta di questi tag consente di eseguire azioni come l'esecuzione di query sui dati di log, la creazione e la gestione di aree di lavoro Log Analytics e di componenti di intelligenza artificiale.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Download di SDK di Application Insights da una rete per la distribuzione di contenuti

Integrare il codice JavaScript nello script in modo che il browser non tenti di scaricare il codice da una rete CDN. Un esempio è disponibile in [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Impostazioni DNS del browser

Se ci si connette alle risorse di monitoraggio di Azure tramite un collegamento privato, il traffico verso queste risorse deve passare attraverso l'endpoint privato configurato nella rete. Per abilitare l'endpoint privato, aggiornare le impostazioni DNS come illustrato in [connettersi a un endpoint privato](#connect-to-a-private-endpoint). Alcuni browser usano le proprie impostazioni DNS anziché quelle impostate. Il browser potrebbe tentare di connettersi agli endpoint pubblici di monitoraggio di Azure e ignorare completamente il collegamento privato. Verificare che le impostazioni del browser non sostituiscano o memorizzano nella cache le impostazioni DNS precedenti. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull' [archiviazione privata](private-storage.md)
