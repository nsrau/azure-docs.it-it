---
title: Connettere le reti virtuali di Azure da App per la logica di Azure attraverso un ambiente del servizio di integrazione (ISE)
description: Creare un ambiente del servizio di integrazione (ISE) in modo che le app per la logica e gli account di integrazione possano accedere alle reti virtuali (VNET) di Azure, rimanendo al contempo private e isolate da Azure pubblico o "globale"
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: b48257cc8e10deb1ec922806f62a6c435069f66f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467085"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Connettere le reti virtuali di Azure da App per la logica di Azure usando un ambiente del servizio di integrazione (ISE)

Per gli scenari in cui le app per la logica e gli account di integrazione devono accedere a una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md), creare un [*ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Un'istanza di ISE è un ambiente privato e isolato che Usa archiviazione dedicata e altre risorse che vengono mantenuti separati dal servizio App per la logica "globale" o pubblico. Questa separazione riduce anche qualsiasi impatto che altri tenant di Azure possono avere sulle prestazioni delle app create. L'ISE è *collegato* alla rete virtuale di Azure, che quindi distribuisce il servizio App per la logica nella rete virtuale. Quando si crea un'app per la logica o un account di integrazione, selezionare questo ISE come posizione per l'app o l'account. L'app per la logica o l'account di integrazione può quindi accedere direttamente alle risorse, ad esempio alle macchine virtuali (VM), ai server, ai sistemi e ai servizi della rete virtuale in uso.

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Questo articolo descrive come portare a termine le attività seguenti:

* Assicurarsi che le porte necessarie in una rete virtuale sono aperte in modo che il traffico possono essere trasmessi tramite l'ambiente del servizio di integrazione (ISE) tra le subnet nella rete virtuale.

* Creare un ambiente del servizio di integrazione (ISE).

* Creare un'app per la logica che possa essere eseguita nell'ISE.

* Creare un account di integrazione e collegarlo alle app per la logica presenti nell'ISE.

Per altre informazioni sugli ambienti del servizio di integrazione, vedere [Accedere alle risorse della rete virtuale di Azure tramite App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>.

  > [!IMPORTANT]
  > App per la logica, incorporato trigger, azioni predefinite e i connettori eseguiti per l'utilizzo ISE un piano tariffario diverso dal piano tariffario in base al consumo. Per altre informazioni, vedere [Prezzi di App per la logica](../logic-apps/logic-apps-pricing.md).

* Una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Se non si dispone di una rete virtuale, vedere l'articolo su come [creare una rete virtuale di Azure](../virtual-network/quick-create-portal.md). 

  * La rete virtuale deve avere quattro *vuoto* subnet per la distribuzione e creazione di risorse nell'ISE. È possibile creare in anticipo queste subnet oppure è possibile attendere finché non si crea l'ISE in cui è possibile creare subnet contemporaneamente. Altre informazioni sulle [requisiti di subnet](#create-subnet). 
  
    > [!NOTE]
    > Se si usa [ExpressRoute](../expressroute/expressroute-introduction.md), che fornisce una connessione privata a servizi cloud Microsoft, devi [creare una tabella di route](../virtual-network/manage-route-table.md) indirizzare e collegare tale tabella con ogni subnet usata per l'ISE, che prevede i seguenti:
    > 
    > **Nome**: < *-nome della route*><br>
    > **Prefisso dell'indirizzo**: 0.0.0.0/0<br>
    > **Hop successivo**: Internet

  * Assicurarsi che la rete virtuale [rende disponibili queste porte](#ports) in modo che l'ISE funziona correttamente e rimane accessibile.

* Se si desidera usare server DNS personalizzati per la rete virtuale di Azure [configurare tali server seguendo questa procedura](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) prima di distribuire l'ISE alla rete virtuale. In caso contrario, ogni volta che si modifica il server DNS, è necessario riavviare l'ISE, una funzionalità disponibile nell'anteprima pubblica di ISE.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="check-network-ports"></a>Controllare le porte di rete

Quando si usa un ambiente del servizio di integrazione (ISE) con una rete virtuale, un problema di configurazione comune è la presenza di uno o più porte bloccate. I connettori utilizzabili per la creazione di connessioni tra l'ISE e il sistema di destinazione potrebbero essere anche i propri requisiti delle porte. Ad esempio, se si comunica con un sistema FTP usando il connettore FTP, assicurarsi che la porta utilizzata nel sistema FTP, ad esempio la porta 21 per l'invio di comandi, sia disponibile.

Per controllare il traffico tra subnet della rete virtuale in cui si distribuisce l'ISE, è possibile facoltativamente configurare [(Nsg) di gruppi di sicurezza di rete](../virtual-network/security-overview.md) in una rete virtuale da [filtraggio del traffico di rete tra subnet](../virtual-network/tutorial-filter-network-traffic.md). Se si sceglie questa route, assicurarsi che l'ISE consente di aprire determinate porte, come descritto nella tabella seguente, nella rete virtuale che usa il Nsg. Se si dispone di firewall o Nsg esistenti in una rete virtuale, assicurarsi che aprono queste porte. In questo modo, l'ISE rimane accessibile e può funzionare correttamente in modo da non perdere l'accesso per l'ISE. In caso contrario, se tutte le porte richieste siano disponibili, l'ISE smette di funzionare.

Le tabelle seguenti descrivono le porte usate dall'ISE nella rete virtuale e dove tali porte vengono usate. Il [tag di servizio di gestione risorse](../virtual-network/security-overview.md#service-tags) rappresenta un gruppo di prefissi di indirizzo IP che consentono di ridurre la complessità durante la creazione di regole di sicurezza.

> [!IMPORTANT]
> Per le comunicazioni interne all'interno di subnet, ISE è necessario aprire tutte le porte all'interno di tali subnet.

| Scopo | Direction | Porte | Tag del servizio di origine | Tag del servizio di destinazione | Note |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Comunicazione dalle App per la logica di Azure | In uscita | 80 e 443 | VirtualNetwork | Internet | La porta dipende dal servizio esterno con cui comunica il servizio App per la logica |
| Azure Active Directory | In uscita | 80 e 443 | VirtualNetwork | AzureActiveDirectory | |
| Dipendenza da Archiviazione di Azure | In uscita | 80 e 443 | VirtualNetwork | Archiviazione | |
| Comunicazione intersubnet | In ingresso e in uscita | 80 e 443 | VirtualNetwork | VirtualNetwork | Per la comunicazione tra le subnet |
| Comunicazione alle App per la logica di Azure | In ingresso | 443 | Internet  | VirtualNetwork | L'indirizzo IP per il computer o servizio che chiama qualsiasi trigger di richiesta o di un webhook che esiste nell'app per la logica. La chiusura o bloccare questa porta impedisce che le chiamate HTTP all'App per la logica con trigger di richiesta.  |
| App per la logica della cronologia di esecuzione | In ingresso | 443 | Internet  | VirtualNetwork | L'indirizzo IP per il computer in cui è visualizzare l'app per la logica della cronologia di esecuzione. Sebbene la chiusura o bloccare questa porta non impedisce di visualizzare la cronologia di esecuzione, non è possibile visualizzare gli input e output per ogni passaggio nella cronologia di esecuzione. |
| Gestione delle connessioni | In uscita | 443 | VirtualNetwork  | Internet | |
| Pubblicare i log di diagnostica e metriche | In uscita | 443 | VirtualNetwork  | AzureMonitor | |
| Comunicazioni da Gestione traffico di Azure | In ingresso | 443 | AzureTrafficManager | VirtualNetwork | |
| Progettazione di App per la logica - proprietà dinamiche | In ingresso | 454 | Internet  | VirtualNetwork | Le richieste provengano da App per la logica [endpoint di accesso in ingresso di indirizzi IP in tale area](../logic-apps/logic-apps-limits-and-config.md#inbound). |
| Dipendenza da Gestione del servizio app | In ingresso | 454 e 455 | AppServiceManagement | VirtualNetwork | |
| Distribuzione dei connettori | In ingresso | 454 & 3443 | Internet  | VirtualNetwork | Necessario per la distribuzione e aggiornamento dei connettori. La chiusura o bloccare questa porta fa sì che le distribuzioni di ISE a non riuscire e impedisce connettore aggiornamenti o correzioni. |
| Dipendenza di SQL Azure | In uscita | 1433 | VirtualNetwork | SQL |
| Integrità risorse di Azure | In uscita | 1886 | VirtualNetwork | Internet | Per pubblicare lo stato di integrità in integrità risorse |
| Gestione API - endpoint di gestione | In ingresso | 3443 | APIManagement  | VirtualNetwork | |
| Dipendenza dal criterio Registra a Hub eventi e dall'agente di monitoraggio | In uscita | 5672 | VirtualNetwork  | Hub eventi | |
| Istanze di accesso Cache Azure per Redis tra Role Instances | In ingresso <br>In uscita | 6379-6383 | VirtualNetwork  | VirtualNetwork | Inoltre, per ISE funzionare con Cache di Azure per Redis, è necessario aprire tali [le porte in ingresso e in uscita descritti nella Cache di Azure per Redis domande frequenti](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | In ingresso | * | AzureLoadBalancer | VirtualNetwork |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Creare l'ISE

Per creare un ambiente del servizio di integrazione (ISE), seguire questa procedura:

1. Scegliere [Crea una risorsa](https://portal.azure.com) dal menu principale di Azure nel **portale di Azure**.
Nella casella di ricerca, digitare "ambiente del servizio di integrazione" come filtro.

   ![Create new resource (Crea nuova risorsa)](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Nel riquadro di creazione ambiente del servizio di integrazione, scegliere **Create**.

   ![Scegliere "Creare"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Specificare questi dettagli per l'ambiente, quindi scegliere **Rivedi e crea**, ad esempio:

   ![Fornire i dettagli dell'ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Proprietà | Obbligatorio | Value | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **Sottoscrizione** | Yes | <*nome sottoscrizione di Azure*> | Sottoscrizione di Azure da usare per l'ambiente |
   | **Gruppo di risorse** | Yes | <*Azure-resource-group-name*> | Il gruppo di risorse di Azure in cui si desidera creare l'ambiente |
   | **Nome dell'ambiente del servizio di integrazione** | Yes | <*Nome ambiente*> | Il nome da assegnare all'ambiente |
   | **Location** | Yes | <*Azure-datacenter-region*> | L'area del datacenter di Azure in cui distribuire l'ambiente |
   | **Capacità aggiuntiva** | Yes | da 0 a 10 | Il numero di unità di elaborazione aggiuntivi da usare per questa risorsa ISE. Per aggiungere capacità dopo la creazione, vedere [capacità di aggiungere ISE](#add-capacity). |
   | **Rete virtuale** | Yes | <*Azure-virtual-network-name*> | La rete virtuale di Azure in cui si desidera collegare l'ambiente in modo che le app per la logica in quell'ambiente possano accedere alla rete virtuale. Se non si dispone di una rete [creare una rete virtuale di Azure prima di tutto](../virtual-network/quick-create-portal.md). <p>**Importante**: È possibile seguire questo collegamento *solo* quando si crea l'ISE. |
   | **Subnet** | Yes | <*subnet-resource-list*> | Un ISE richiede quattro subnet *vuote* per la creazione di risorse nell'ambiente. Per creare ciascuna subnet, [seguire i passaggi descritti in questa tabella](#create-subnet).  |
   |||||

   <a name="create-subnet"></a>

   **Creare una subnet**

   Per creare risorse nell'ambiente in uso, l'ISE richiede quattro *vuoto* subnet che non sono delegate a qualsiasi servizio. 
   Si *non è possibile* modificare questi indirizzi di subnet dopo aver creato l'ambiente. Ogni subnet deve soddisfare questi criteri:

   * Ha un nome che inizia con un carattere alfabetico o un carattere di sottolineatura e non ha questi caratteri: `<`, `>`, `%`, `&`, `\\`, `?`, `/`

   * Usa il [formato Classless Interdomain Routing (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e uno spazio di indirizzi B di classe.

   * Viene utilizzato almeno una `/27` nell'indirizzo spazio perché ogni subnet devono avere 32 indirizzi come i *minimo*. Ad esempio:

     * `10.0.0.0/27` ha 32 indirizzi perché 2<sup>(32-27)</sup> è 2<sup>5</sup> o 32.

     * `10.0.0.0/24` con 256 indirizzi perché 2<sup>(32-24)</sup> è 2<sup>8</sup> o 256.

     * `10.0.0.0/28` ha solo 16 indirizzi ed è troppo piccolo perché 2<sup>(32-28)</sup> è 2<sup>4</sup> o 16.

     Per altre informazioni sul calcolo degli indirizzi, vedere [blocchi CIDR IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Se si usa [ExpressRoute](../expressroute/expressroute-introduction.md), ricordarsi di [creare una tabella di route](../virtual-network/manage-route-table.md) indirizzare e collegare tale tabella con ogni subnet usata per l'ISE, che prevede i seguenti:

     **Nome**: < *-nome della route*><br>
     **Prefisso dell'indirizzo**: 0.0.0.0/0<br>
     **Hop successivo**: Internet

   1. Nell'elenco **Subnet**, scegliere **Configurazione delle subnet di gestione**.

      ![Gestisci configurazione subnet](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Nel riquadro **Subnet**, scegliere **Subnet**.

      ![Aggiungi subnet](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Nel riquadro **Aggiungi subnet**, specificare queste informazioni.

      * **Nome**: Nome per la subnet
      * **Intervallo di indirizzi (blocco CIDR)** : L'intervallo di subnet nella rete virtuale e nel formato CIDR

      ![Aggiungere i dettagli della subnet](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Al termine dell'operazione, scegliere **OK**.

   1. Ripetere questi passaggi per altre tre subnet.

      > [!NOTE]
      > Se si prova a creare subnet non sono valide, il portale di Azure viene visualizzato un messaggio, ma non blocca lo stato di avanzamento.

1. Dopo l'avvenuta convalida da parte di Azure delle informazioni dell'ISE, scegliere **Crea**, ad esempio:

   ![Al termine della convalida, scegliere "Crea"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure avvia la distribuzione dell'ambiente, ma questo processo *potrebbe* richiedere fino a due ore per essere completato. 
   Per controllare lo stato della distribuzione, sulla barra degli strumenti di Azure, scegliere l'icona delle notifiche, che consente di aprire il riquadro notifiche.

   ![Controllare lo stato della distribuzione](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se la distribuzione avviene con successo, Azure Mostra questa notifica:

   ![Distribuzione completata](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   In caso contrario, seguire le istruzioni del portale di Azure per la risoluzione dei problemi di distribuzione.

   > [!NOTE]
   > Se si verifica un errore di distribuzione o si elimina l'ISE, Azure potrebbe richiedere fino a un'ora prima di rilasciare le subnet. Questo ritardo, significa che potrebbe essere necessario attendere prima di riusare tali subnet in ISE un altro. 
   >
   > Se si elimina la rete virtuale, Azure richiede in genere fino a due ore prima di rilasciare backup le subnet, ma questa operazione potrebbe richiedere più tempo. 
   > Quando si eliminano le reti virtuali, assicurarsi che nessuna risorsa è ancora connessi. Visualizzare [Elimina rete virtuale](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Per visualizzare l'ambiente, scegliere **Vai alla risorsa** se Azure non mostra automaticamente l'ambiente al termine della distribuzione.  

Per altre informazioni sulla creazione di subnet, vedere [aggiungere una subnet di rete virtuale](../virtual-network/virtual-network-manage-subnet.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Creare un'app per la logica - ISE

Per creare App per la logica da eseguire nell'ambiente del servizio di integrazione (ISE), [creare le App per la logica nel modo consueto](../logic-apps/quickstart-create-first-logic-app-workflow.md) tranne quando si imposta la **posizione** proprietà, selezionare l'ISE dal  **Gli ambienti del servizio integrazione** sezione, ad esempio:

  ![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

Le differenze nel modo in cui i trigger e azioni lavoro e come essi è contrassegnato come quando si usa un'istanza di ISE rispetto al servizio di App per la logica globale, vedere [isolati rispetto a globale nella panoramica ISE](connect-virtual-network-vnet-isolated-environment-overview.md#difference).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Creare account di integrazione - ISE

Se si desidera usare un account di integrazione con App per la logica in un ambiente del servizio di integrazione (ISE), tale account di integrazione è necessario usare il *stesso ambiente* come le App per la logica. Le app per la logica in un ISE possono fare riferimento solo agli account di integrazione che sono nello stesso ISE.

Per creare un account di integrazione che usa un'istanza di ISE, [creare l'account di integrazione nel modo consueto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) tranne quando si imposta la **posizione** proprietà, selezionare l'ISE dal **integrazione gli ambienti del servizio** sezione, ad esempio:

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Aggiungere capacità ISE

L'unità di base di ISE ha corretto la capacità, pertanto se è necessaria una velocità effettiva maggiore, è possibile aggiungere altre unità di scala. È possibile la scalabilità automatica basate sulle metriche delle prestazioni o basato su un numero di unità di elaborazione aggiuntivi. Se si sceglie la scalabilità automatica basata sulle metriche, è possibile scegliere tra diversi criteri e specificare le condizioni di soglia per soddisfare i relativi criteri.

1. Nel portale di Azure, individuare l'ISE.

1. Per esaminare le metriche di utilizzo e sulle prestazioni per l'ISE, nel menu principale di ISE, selezionare **Panoramica**.

   ![Visualizzare l'utilizzo per ISE](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Per configurare la scalabilità automatica, in **le impostazioni**, selezionare **scalare in orizzontale**. Nel **Configure** scheda, scegliere **Abilita scalabilità automatica**.

   ![Attivare la scalabilità automatica](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Per la **nome dell'impostazione di scalabilità automatica**, specificare un nome per l'impostazione.

1. Nel **predefinito** keychains uno **scalabilità basata su una metrica** oppure **scala a un numero di istanze specifico**.

   * Se si sceglie basati sull'istanza, immettere il numero di unità di elaborazione, inclusi tra 0 e 10.

   * Se si sceglie basate sulle metriche, seguire questa procedura:

     1. Nel **regole** keychains **aggiungere una regola**.

     1. Nel **regole di scalabilità** riquadro impostato i criteri e l'azione da intraprendere quando la regola attiva.

     1. Al termine, scegliere **Add**.

1. Quando hai finito con le impostazioni di scalabilità automatica, salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* Informazioni sull'[integrazione della rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
