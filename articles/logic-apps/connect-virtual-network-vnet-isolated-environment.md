---
title: Connettersi alle reti virtuali di Azure con ISE-app per la logica di Azure
description: Creare un ambiente del servizio di integrazione (ISE) in grado di accedere alle reti virtuali di Azure (reti virtuali) da app per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 883778360bd2315e1424f9f207cbfd994ec1a373
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73901186"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Connettere le reti virtuali di Azure da App per la logica di Azure usando un ambiente del servizio di integrazione (ISE)

Per gli scenari in cui le app per la logica e gli account di integrazione devono accedere a una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md), creare un [*ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE è un ambiente privato e isolato che usa risorse di archiviazione dedicate e altre risorse che vengono mantenute separate dal servizio app per la logica pubblico o "globale". Questa separazione riduce anche qualsiasi impatto che altri tenant di Azure possono avere sulle prestazioni delle app create.

Quando si crea un ISE, Azure *inserisce* tale ISE nella rete virtuale di Azure, che quindi distribuisce il servizio app per la logica nella rete virtuale. Quando si crea un'app per la logica o un account di integrazione, selezionare ISE come percorso. L'app per la logica o l'account di integrazione può quindi accedere direttamente alle risorse, ad esempio alle macchine virtuali (VM), ai server, ai sistemi e ai servizi della rete virtuale in uso.

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Per interagire con le app per la logica e gli account di integrazione in un ISE, è necessario che entrambi usino lo *stesso ISE* della relativa posizione.

ISE ha aumentato i limiti di durata dell'esecuzione, conservazione dell'archiviazione, velocità effettiva, timeout di richiesta e risposta HTTP, dimensioni dei messaggi e richieste di connettori personalizzati. Per altre informazioni, vedere [limiti e configurazione per app per la logica di Azure](logic-apps-limits-and-config.md). Per altre informazioni su ISEs, vedere [accesso alle risorse di rete virtuale di Azure da app per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Questo articolo descrive come portare a termine le attività seguenti:

* Verificare che tutte le porte necessarie nella rete virtuale siano aperte in modo che il traffico possa spostarsi attraverso l'ISE tra le subnet della rete virtuale.

* Creazione di ISE.

* Aggiungere capacità aggiuntiva a ISE.

> [!IMPORTANT]
> Le app per la logica, i trigger incorporati, le azioni predefinite e i connettori eseguiti in ISE usano un piano tariffario diverso dal piano tariffario in base al consumo. Per informazioni sul funzionamento dei prezzi e della fatturazione per ISEs, vedere il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md#fixed-pricing)per la logica. Per informazioni sui prezzi, vedere [prezzi di app](../logic-apps/logic-apps-pricing.md)per la logica.

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Se non si dispone di una rete virtuale, vedere l'articolo su come [creare una rete virtuale di Azure](../virtual-network/quick-create-portal.md). 

  * La rete virtuale deve avere quattro subnet *vuote* per la creazione e la distribuzione di risorse in ISE. È possibile creare queste subnet in anticipo, oppure è possibile attendere fino a quando non si crea ISE in cui è possibile creare le subnet nello stesso momento. Altre informazioni sui [requisiti](#create-subnet)per le subnet.

  * I nomi delle subnet devono iniziare con un carattere alfabetico o un carattere di sottolineatura e non possono usare questi caratteri: `<`, `>`, `%`, `&`, `\\`, `?``/`. 
  
  * Se si vuole distribuire ISE tramite un modello di Azure Resource Manager, assicurarsi prima di tutto di delegare una subnet vuota a Microsoft. Logic/integrationServiceEnvironment. Non è necessario eseguire questa delega quando si esegue la distribuzione tramite il portale di Azure.

  * Assicurarsi che la rete virtuale [renda disponibili queste porte](#ports) in modo che ISE funzioni correttamente e rimanga accessibile.

  * Se si usa [ExpressRoute](../expressroute/expressroute-introduction.md), che fornisce una connessione privata ai servizi cloud Microsoft, è necessario [creare una tabella di route](../virtual-network/manage-route-table.md) con la route seguente e collegare tale tabella a ogni subnet usata da ISE:

    **Nome**: <*route-name*><br>
    **Prefisso indirizzo**: 0.0.0.0/0<br>
    **Hop successivo**: Internet

* Se si vuole usare server DNS personalizzati per la rete virtuale di Azure, [configurare questi server seguendo questa procedura](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) prima di distribuire ISE nella rete virtuale. In caso contrario, ogni volta che si modifica il server DNS, è necessario riavviare anche ISE.

  > [!IMPORTANT]
  > Se si modificano le impostazioni del server DNS dopo aver creato ISE, assicurarsi di riavviare ISE. Per ulteriori informazioni sulla gestione delle impostazioni del server DNS, vedere [creare, modificare o eliminare una rete virtuale](../virtual-network/manage-virtual-network.md#change-dns-servers).

<a name="ports"></a>

## <a name="check-network-ports"></a>Controllare le porte di rete

Quando si usa ISE con una rete virtuale di Azure, un problema di installazione comune è la presenza di una o più porte bloccate. I connettori usati per la creazione di connessioni tra ISE e il sistema di destinazione potrebbero avere anche requisiti di porta specifici. Se, ad esempio, si comunica con un sistema FTP utilizzando il connettore FTP, assicurarsi che la porta utilizzata sul sistema FTP sia disponibile, ad esempio la porta 21 per l'invio di comandi. Per assicurarsi che ISE rimanga accessibile e possa funzionare correttamente, aprire le porte specificate nella tabella seguente. In caso contrario, se non sono disponibili porte obbligatorie, ISE smette di funzionare.

> [!IMPORTANT]
> Le porte di origine sono effimere, quindi assicurarsi di impostarle su `*` per tutte le regole.
> Per la comunicazione interna all'interno delle subnet, ISE richiede di aprire tutte le porte all'interno di tali subnet.

* Se sono state create una nuova rete virtuale e le subnet senza vincoli, non è necessario configurare i [gruppi di sicurezza di rete (gruppi)](../virtual-network/security-overview.md#network-security-groups) nella rete virtuale per controllare il traffico tra le subnet.

* In una rete virtuale esistente è possibile impostare *facoltativamente* gruppi [filtrando il traffico di rete tra le subnet](../virtual-network/tutorial-filter-network-traffic.md). Se si sceglie questa route, nella rete virtuale in cui si vuole configurare il gruppi, assicurarsi di aprire le porte specificate nella tabella seguente. Se si usano [le regole di sicurezza di NSG](../virtual-network/security-overview.md#security-rules), sono necessari entrambi i protocolli TCP e UDP.

* Se sono già presenti gruppi o firewall nella rete virtuale, assicurarsi di aprire le porte specificate nella tabella seguente. Se si usano [le regole di sicurezza di NSG](../virtual-network/security-overview.md#security-rules), sono necessari entrambi i protocolli TCP e UDP.

Di seguito è riportata la tabella che descrive le porte nella rete virtuale utilizzate da ISE e in cui vengono utilizzate tali porte. I [tag del servizio Gestione risorse](../virtual-network/security-overview.md#service-tags) rappresentano un gruppo di prefissi di indirizzi IP che consentono di ridurre al minimo la complessità durante la creazione delle regole di sicurezza.

| Scopo | Direzione | Porte di destinazione | Tag del servizio di origine | Tag del servizio di destinazione | note |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Comunicazione dalle App per la logica di Azure | In uscita | 80, 443 | VirtualNetwork | Internet | La porta dipende dal servizio esterno con cui comunica il servizio app per la logica |
| Azure Active Directory | In uscita | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Dipendenza da Archiviazione di Azure | In uscita | 80, 443 | VirtualNetwork | Archiviazione | |
| Comunicazione tra subnet | In ingresso e in uscita | 80, 443 | VirtualNetwork | VirtualNetwork | Per la comunicazione tra subnet |
| Comunicazione alle App per la logica di Azure | In ingresso | 443 | Endpoint di accesso interno: <br>VirtualNetwork <p><p>Endpoint di accesso esterno: <br>Internet <p><p>**Nota**: questi endpoint fanno riferimento all'impostazione dell'endpoint [selezionata durante la creazione di ISE](#create-environment). Per ulteriori informazioni, vedere [endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). | VirtualNetwork | Indirizzo IP del computer o del servizio che chiama qualsiasi trigger di richiesta o webhook presente nell'app per la logica. La chiusura o il blocco di questa porta impedisce le chiamate HTTP alle app per la logica con trigger di richiesta. |
| Cronologia di esecuzione dell'app per la logica | In ingresso | 443 | Endpoint di accesso interno: <br>VirtualNetwork <p><p>Endpoint di accesso esterno: <br>Internet <p><p>**Nota**: questi endpoint fanno riferimento all'impostazione dell'endpoint [selezionata durante la creazione di ISE](#create-environment). Per ulteriori informazioni, vedere [endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). | VirtualNetwork | Indirizzo IP del computer da cui si visualizza la cronologia di esecuzione dell'app per la logica. Sebbene la chiusura o il blocco di questa porta non impedisca la visualizzazione della cronologia di esecuzione, non è possibile visualizzare gli input e gli output per ogni passaggio della cronologia di esecuzione. |
| Gestione delle connessioni | In uscita | 443 | VirtualNetwork  | AppService | |
| Pubblicare i log di diagnostica e metriche | In uscita | 443 | VirtualNetwork  | AzureMonitor | |
| Comunicazione da Gestione traffico di Azure | In ingresso | 443 | AzureTrafficManager | VirtualNetwork | |
| Progettazione di App per la logica - proprietà dinamiche | In ingresso | 454 | Vedere la colonna Note per gli indirizzi IP da consentire | VirtualNetwork | Le richieste provengono dagli indirizzi IP in [ingresso](../logic-apps/logic-apps-limits-and-config.md#inbound) dell'endpoint di accesso delle app per la logica per tale area. |
| Controllo integrità rete | In ingresso | 454 | Vedere la colonna Note per gli indirizzi IP da consentire | VirtualNetwork | Le richieste provengono dall'endpoint di accesso delle app per la logica per gli indirizzi IP in [ingresso](../logic-apps/logic-apps-limits-and-config.md#inbound) e in [uscita](../logic-apps/logic-apps-limits-and-config.md#outbound) per tale area. |
| Dipendenza da Gestione del servizio app | In ingresso | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Distribuzione dei connettori | In ingresso | 454 | AzureConnectors | VirtualNetwork | Necessario per la distribuzione e l'aggiornamento dei connettori. La chiusura o il blocco di questa porta causa la mancata riuscita delle distribuzioni di ISE e impedisce aggiornamenti o correzioni del connettore. |
| Distribuzione dei criteri del connettore | In ingresso | 3443 | Internet | VirtualNetwork | Necessario per la distribuzione e l'aggiornamento dei connettori. La chiusura o il blocco di questa porta causa la mancata riuscita delle distribuzioni di ISE e impedisce aggiornamenti o correzioni del connettore. |
| Dipendenza SQL di Azure | In uscita | 1433 | VirtualNetwork | SQL | |
| Integrità risorse di Azure | In uscita | 1886 | VirtualNetwork | AzureMonitor | Per pubblicare lo stato di integrità Integrità risorse |
| Gestione API - endpoint di gestione | In ingresso | 3443 | APIManagement | VirtualNetwork | |
| Dipendenza dal criterio Registra a Hub eventi e dall'agente di monitoraggio | In uscita | 5672 | VirtualNetwork | Hub eventi | |
| Istanze di accesso Cache Azure per Redis tra Role Instances | In ingresso <br>In uscita | 6379-6383 | VirtualNetwork | VirtualNetwork | Inoltre, per usare ISE con cache di Azure per Redis, è necessario aprire le [porte in uscita e in ingresso descritte nelle domande frequenti su cache di Azure per Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Servizio di bilanciamento del carico di Azure | In ingresso | * | AzureLoadBalancer | VirtualNetwork | |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Creare l'ISE

Per creare un ambiente del servizio di integrazione (ISE), seguire questa procedura:

1. Scegliere [Crea una risorsa](https://portal.azure.com) dal menu principale di Azure nel **portale di Azure**.
Nella casella di ricerca, digitare "ambiente del servizio di integrazione" come filtro.

   ![Create new resource (Crea nuova risorsa)](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Nel riquadro Creazione ambiente del servizio di integrazione scegliere **Crea**.

   ![Scegliere "Creare"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Specificare questi dettagli per l'ambiente, quindi scegliere **Rivedi e crea**, ad esempio:

   ![Fornire i dettagli dell'ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Proprietà | obbligatori | Valore | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **Sottoscrizione** | Sì | <*nome sottoscrizione di Azure*> | Sottoscrizione di Azure da usare per l'ambiente |
   | **Gruppo di risorse** | Sì | <*Azure-resource-group-name*> | Il gruppo di risorse di Azure in cui si desidera creare l'ambiente |
   | **Nome dell'ambiente del servizio di integrazione** | Sì | <*Nome ambiente*> | Il nome ISE, che può contenere solo lettere, numeri, trattini (`-`), caratteri di sottolineatura (`_`) e punti (`.`). |
   | **Località** | Sì | <*Azure-datacenter-region*> | L'area del datacenter di Azure in cui distribuire l'ambiente |
   | **SKU** | Sì | **Premium** o **Developer (nessun contratto di contratto)** | SKU ISE da creare e usare. Per le differenze tra questi SKU, vedere [SKU di ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Importante**: questa opzione è disponibile solo in fase di creazione di ISE e non può essere modificata in un secondo momento. |
   | **Capacità aggiuntiva** | Premium: <br>Sì <p><p>Developer: <br>Non applicabile | Premium: <br>da 0 a 10 <p><p>Developer: <br>Non applicabile | Il numero di unità di elaborazione aggiuntive da usare per questa risorsa ISE. Per aggiungere capacità dopo la creazione, vedere [aggiungere la capacità di ISE](#add-capacity). |
   | **Endpoint di accesso** | Sì | **Interno** o **esterno** | Il tipo di endpoint di accesso da usare per ISE, che determina se i trigger di richiesta o webhook nelle app per la logica in ISE possono ricevere chiamate dall'esterno della rete virtuale. Il tipo di endpoint influiscono anche sull'accesso a input e output nella cronologia delle esecuzioni dell'app per la logica. Per ulteriori informazioni, vedere [endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Importante**: questa opzione è disponibile solo in fase di creazione di ISE e non può essere modificata in un secondo momento. |
   | **Rete virtuale** | Sì | <*Azure-virtual-network-name*> | La rete virtuale di Azure in cui si desidera collegare l'ambiente in modo che le app per la logica in quell'ambiente possano accedere alla rete virtuale. Se non si ha una rete, [creare prima di tutto una rete virtuale di Azure](../virtual-network/quick-create-portal.md). <p>**Importante**: è possibile eseguire questo Injection *solo* quando si crea ISE. |
   | **Subnet** | Sì | <*subnet-resource-list*> | Un ISE richiede quattro subnet *vuote* per la creazione e la distribuzione di risorse nell'ambiente. Per creare ciascuna subnet, [seguire i passaggi descritti in questa tabella](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Creare una subnet**

   Per creare e distribuire le risorse nell'ambiente, ISE necessita di quattro subnet *vuote* che non sono delegate ad alcun servizio. *Non è possibile* modificare questi indirizzi subnet dopo aver creato l'ambiente.
   
   > [!IMPORTANT]
   > 
   > I nomi delle subnet devono iniziare con un carattere alfabetico o un carattere di sottolineatura (nessun numero), né usare i caratteri seguenti: `<`, `>`, `%`, `&`, `\\`, `?``/`.
   
   Inoltre, ogni subnet deve soddisfare questi requisiti:

   * Usa il [formato CIDR (Inter-Domain Routing) con classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e uno spazio di indirizzi della classe B.

   * USA almeno un `/27` nello spazio degli indirizzi, perché ogni subnet *deve avere almeno* 32 indirizzi come *minimo*. Ad esempio:

     * `10.0.0.0/27` ha 32 indirizzi, perché 2<sup>(32-27)</sup> è 2<sup>5</sup> o 32.

     * `10.0.0.0/24` ha 256 indirizzi, perché 2<sup>(32-24)</sup> è 2<sup>8</sup> o 256.

     * `10.0.0.0/28` dispone solo di 16 indirizzi ed è troppo piccolo perché 2<sup>(32-28)</sup> è 2<sup>4</sup> o 16.

     Per ulteriori informazioni sul calcolo degli indirizzi, vedere la pagina relativa ai [blocchi CIDR IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Se si usa [ExpressRoute](../expressroute/expressroute-introduction.md), è necessario [creare una tabella di route](../virtual-network/manage-route-table.md) con la route seguente e collegare la tabella a ogni subnet usata da ISE:

     **Nome**: <*route-name*><br>
     **Prefisso indirizzo**: 0.0.0.0/0<br>
     **Hop successivo**: Internet

   1. Nell'elenco **Subnet**, scegliere **Configurazione delle subnet di gestione**.

      ![Gestisci configurazione subnet](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Nel riquadro **Subnet**, scegliere **Subnet**.

      ![Aggiungi subnet](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Nel riquadro **Aggiungi subnet**, specificare queste informazioni.

      * **Nome**: nome della subnet
      * **Intervallo di indirizzi (blocco CIDR)** : l'intervallo della subnet nella rete virtuale e in formato CIDR

      ![Aggiungere i dettagli della subnet](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Al termine dell'operazione, scegliere **OK**.

   1. Ripetere questi passaggi per altre tre subnet.

      > [!NOTE]
      > Se le subnet che si tenta di creare non sono valide, il portale di Azure Visualizza un messaggio, ma non blocca lo stato di avanzamento.

   Per ulteriori informazioni sulla creazione di subnet, vedere [aggiungere una subnet di rete virtuale](../virtual-network/virtual-network-manage-subnet.md).

1. Dopo l'avvenuta convalida da parte di Azure delle informazioni dell'ISE, scegliere **Crea**, ad esempio:

   ![Al termine della convalida, scegliere "Crea"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure avvia la distribuzione dell'ambiente, ma questo processo *potrebbe* richiedere fino a due ore per essere completato. Per controllare lo stato della distribuzione, sulla barra degli strumenti di Azure, scegliere l'icona delle notifiche, che consente di aprire il riquadro notifiche.

   ![Controllare lo stato della distribuzione](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se la distribuzione avviene con successo, Azure Mostra questa notifica:

   ![Distribuzione completata](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   In caso contrario, seguire le istruzioni portale di Azure per la risoluzione dei problemi di distribuzione.

   > [!NOTE]
   > Se la distribuzione non riesce o si elimina ISE, Azure potrebbe richiedere fino a un'ora prima di rilasciare le subnet. Questo ritardo significa che potrebbe essere necessario attendere prima di riutilizzare tali subnet in un altro ISE.
   >
   > Se si elimina la rete virtuale, Azure richiede in genere fino a due ore prima di rilasciare le subnet, ma questa operazione potrebbe richiedere più tempo. 
   > Quando si eliminano le reti virtuali, assicurarsi che non ci siano risorse ancora connesse. 
   > Vedere [eliminare la rete virtuale](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Per visualizzare l'ambiente, scegliere **Vai alla risorsa** se Azure non mostra automaticamente l'ambiente al termine della distribuzione.

1. Per verificare lo stato di integrità della rete per ISE, vedere [gestire l'ambiente del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Per iniziare a creare app per la logica e altri artefatti in ISE, vedere [aggiungere elementi agli ambienti del servizio di integrazione](../logic-apps/add-artifacts-integration-service-environment-ise.md).

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Aggiungere la capacità di ISE

L'unità di base Premium ISE ha una capacità fissa, quindi se è necessaria una velocità effettiva maggiore, è possibile aggiungere altre unità di scala, durante la creazione o in seguito. È possibile applicare la scalabilità automatica in base alle metriche delle prestazioni o in base a una serie di unità di elaborazione aggiuntive. Se si sceglie la scalabilità automatica in base alle metriche, è possibile scegliere tra diversi criteri e specificare le condizioni di soglia per la riunione di questi criteri. Lo SKU Developer non include la possibilità di aggiungere unità di scala.

1. Nella portale di Azure trovare ISE.

1. Per esaminare le metriche di utilizzo e delle prestazioni per ISE, scegliere **Panoramica**dal menu principale di ISE.

   ![Visualizzazione dell'utilizzo per ISE](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Per configurare la scalabilità automatica, in **Impostazioni**selezionare **scale out (scalabilità orizzontale**). Nella scheda **Configura** scegliere **Abilita scalabilità**automatica.

   ![Attivare la scalabilità automatica](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Per **Nome impostazione di scalabilità**automatica specificare un nome per l'impostazione.

1. Nella sezione **predefinita** scegliere **scalabilità in base a una metrica** o **Ridimensiona a un numero di istanze specifico**.

   * Se si sceglie basato su istanza, immettere il numero di unità di elaborazione comprese tra 0 e 10.

   * Se si sceglie basato su metrica, attenersi alla procedura seguente:

     1. Nella sezione **regole** scegliere **Aggiungi una regola**.

     1. Nel riquadro **regola di scalabilità** impostare i criteri e l'azione da intraprendere quando la regola viene attivata.

     1. Al termine, scegliere **Aggiungi**.

1. Al termine delle impostazioni di scalabilità automatica, salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere elementi agli ambienti del servizio di integrazione](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Controllare l'integrità della rete per gli ambienti del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Altre informazioni su [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* Informazioni sull'[integrazione della rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
