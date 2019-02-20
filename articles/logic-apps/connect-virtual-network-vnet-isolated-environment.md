---
title: Connettere le reti virtuali di Azure da App per la logica di Azure attraverso un ambiente del servizio di integrazione (ISE)
description: Creare un ambiente del servizio di integrazione (ISE) in modo che le app per la logica e gli account di integrazione possano accedere alle reti virtuali (VNET) di Azure, rimanendo al contempo private e isolate da Azure pubblico o "globale"
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 02/12/2019
ms.openlocfilehash: 8d7fc6d8f581c3ad0e0f3266ea615acadcb7bc25
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176204"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Connettere le reti virtuali di Azure da App per la logica di Azure usando un ambiente del servizio di integrazione (ISE)

> [!NOTE]
> Questa funzionalità è in *anteprima privata*. Per partecipare all'anteprima privata, [creare la richiesta qui](https://aka.ms/iseprivatepreview).

Per gli scenari in cui le app per la logica e gli account di integrazione devono accedere a una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md), creare un [*ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Un ISE è un ambiente isolato e privato che usa una risorsa di archiviazione dedicata e altre risorse separate dal servizio App per la logica pubblico o "globale". Questa separazione riduce anche qualsiasi impatto che altri tenant di Azure possono avere sulle prestazioni delle app create. L'ISE è *collegato* alla rete virtuale di Azure, che quindi distribuisce il servizio App per la logica nella rete virtuale. Quando si crea un'app per la logica o un account di integrazione, selezionare questo ISE come posizione per l'app o l'account. L'app per la logica o l'account di integrazione può quindi accedere direttamente alle risorse, ad esempio alle macchine virtuali (VM), ai server, ai sistemi e ai servizi della rete virtuale in uso.

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Questo articolo descrive come portare a termine le attività seguenti:

* Configurare le porte sulla rete virtuale di Azure in modo che il traffico possa essere trasmesso tramite l'ambiente del servizio di integrazione (ISE) su più subnet nella rete virtuale.

* Configurare le autorizzazioni per la rete virtuale di Azure in modo che l'istanza privata delle app per la logica possa accedere alla rete virtuale.

* Creare un ambiente del servizio di integrazione (ISE).

* Creare un'app per la logica che possa essere eseguita nell'ISE.

* Creare un account di integrazione e collegarlo alle app per la logica presenti nell'ISE.

Per altre informazioni sugli ambienti del servizio di integrazione, vedere [Accedere alle risorse della rete virtuale di Azure tramite App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>.

  > [!IMPORTANT]
  > Le app per la logica, le azioni predefinite e i connettori eseguiti nell'ambiente del servizio di integrazione usano un piano tariffario diverso, non quello con pagamento in base al consumo. Per altre informazioni, vedere [Prezzi di App per la logica](../logic-apps/logic-apps-pricing.md).

* Una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Se non si dispone di una rete virtuale, vedere l'articolo su come [creare una rete virtuale di Azure](../virtual-network/quick-create-portal.md). Sono necessarie anche le subnet nella rete virtuale per la distribuzione di ISE. È possibile creare queste subnet in anticipo, o attendere fino a quando non si crea l'ISE in cui è possibile creare subnet contemporaneamente. Inoltre, [assicurarsi che la rete virtuale renda disponibili queste porte](#ports) in modo che l'ISE funzioni correttamente e rimanga accessibile.

* Per consentire alle app per la logica un accesso diretto alla rete virtuale di Azure [configurare le autorizzazioni del Controllo degli accessi in base al ruolo (RBAC) della propria rete](#vnet-access) in modo che il servizio delle App per la logica disponga delle autorizzazioni di accesso alla rete virtuale.

* Per usare uno o più server DNS personalizzati per la distribuzione della rete virtuale di Azure [configurare tali server seguendo questo materiale sussidiario](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) prima di distribuire l'ISE alla rete virtuale. In caso contrario, ogni volta che si modifica il server DNS, è necessario riavviare l'ISE, una funzionalità disponibile nell'anteprima pubblica di ISE.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="set-up-network-ports"></a>Configurare le porte di rete

Per funzionare correttamente ed essere sempre accessibile, l'ambiente del servizio di integrazione (ISE) deve avere porte specifiche disponibili nella propria rete virtuale. In caso contrario, se una di queste porte non è disponibile, si potrebbe perdere l'accesso per l'ISE, che potrebbe smettere di funzionare. Quando si usa un ISE in una rete virtuale, un problema di configurazione comune è la presenza di una o più porte bloccate. Per le connessioni tra l'ISE e il sistema di destinazione, il connettore che si usa potrebbe avere requisiti propri per le porte. Ad esempio, se si comunica con un sistema FTP usando il connettore FTP, assicurarsi che la porta utilizzata nel sistema FTP, ad esempio la porta 21 per l'invio di comandi, sia disponibile.

Per controllare il traffico in ingresso e in uscita tra le subnet della rete virtuale in cui si distribuisce l'ISE, è possibile configurare [gruppi di sicurezza di rete](../virtual-network/security-overview.md) per queste subnet apprendendo [come filtrare il traffico di rete tra subnet](../virtual-network/tutorial-filter-network-traffic.md). Le tabelle seguenti descrivono le porte usate dall'ISE nella rete virtuale e dove tali porte vengono usate. L'asterisco (*) rappresenta qualsiasi origine del traffico. Un [tag di servizio](../virtual-network/security-overview.md#service-tags) rappresenta un gruppo di prefissi di indirizzi IP che consente di ridurre la complessità nella creazione di regole di sicurezza.

| Scopo | Direzione | Porta di origine <br>Porta di destinazione | Tag del servizio di origine <br>Tag del servizio di destinazione |
|---------|-----------|---------------------------------|-----------------------------------------------|
| Comunicazione alle App per la logica di Azure <br>Comunicazione dalle App per la logica di Azure | In ingresso <br>In uscita | * <br>80 e 443 | INTERNET <br>VIRTUAL_NETWORK |
| Azure Active Directory | In uscita | * <br>80 e 443 | VIRTUAL_NETWORK <br>AzureActiveDirectory |
| Dipendenza da Archiviazione di Azure | In uscita | * <br>80 e 443 | VIRTUAL_NETWORK <br>Archiviazione |
| Gestione delle connessioni | In uscita | * <br>443 | VIRTUAL_NETWORK <br>INTERNET |
| Pubblicare i log di diagnostica e metriche | In uscita | * <br>443 | VIRTUAL_NETWORK <br>AzureMonitor |
| Progettazione di App per la logica - proprietà dinamiche <br>Cronologia di esecuzione dell'app per la logica <br>Distribuzione dei connettori <br>Endpoint per il trigger di richiesta | In ingresso | * <br>454 | INTERNET <br>VIRTUAL_NETWORK |
| Dipendenza da Gestione del servizio app | In ingresso | * <br>454 e 455 | AppServiceManagement <br>VIRTUAL_NETWORK |
| Gestione API - endpoint di gestione | In ingresso | * <br>3443 | APIManagement <br>VIRTUAL_NETWORK |
| Dipendenza dal criterio Registra a Hub eventi e dall'agente di monitoraggio | In uscita | * <br>5672 | VIRTUAL_NETWORK <br>Hub eventi |
| Istanze di accesso Cache Azure per Redis tra Role Instances | In ingresso <br>In uscita | * <br>6381-6383 | VIRTUAL_NETWORK <br>VIRTUAL_NETWORK |
|||||

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>Configurare le autorizzazioni della rete virtuale

Quando si crea un ambiente del servizio di integrazione in Azure, si seleziona una rete virtuale di Azure dove si *collega* l'ambiente. Tuttavia, prima di poter selezionare la rete virtuale da collegare all'ambiente, è necessario configurare le autorizzazioni del Controllo degli accessi in base al ruolo (RBAC) nella rete virtuale. Per impostare le autorizzazioni, assegnare questi ruoli specifici al servizio App per la logica di Azure:

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare la rete virtuale in questione.

1. Nel menu della rete virtuale, selezionare **Controllo dei diritti di accesso (IAM)**.

1. In **Controllo di accesso (IAM)**, scegliere **Aggiungi un'assegnazione di ruolo**.

   ![Aggiungere ruoli](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. Nel riquadro **Aggiungi un'assegnazione di ruolo**, aggiungere il ruolo necessario al servizio App per la logica di Azure, come indicato.

   1. In **Ruolo**, selezionare **Collaboratore di rete**.

   1. In **Assegna accesso a** selezionare **Utente, gruppo o entità servizio di Azure AD**.

   1. In **Seleziona**, immettere **App per la logica di Azure**.

   1. Quando viene visualizzato l'elenco dei membri, selezionare **App per la logica di Azure**.

      > [!TIP]
      > Se non è possibile trovare questo servizio, immettere l'ID del servizio delle App per la logica di Azure: `7cd684f4-8a78-49b0-91ec-6a35d38739ba`

   1. Al termine dell'operazione, scegliere **Salva**.

   Ad esempio: 

   ![Aggiungi un'assegnazione di ruolo](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

Per altre informazioni, consultare [Autorizzazioni per l'accesso alla rete virtuale](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Creare l'ISE

Per creare un ambiente del servizio di integrazione (ISE), seguire questa procedura:

1. Scegliere [Crea una risorsa](https://portal.azure.com) dal menu principale di Azure nel **portale di Azure**.

   ![Create new resource (Crea nuova risorsa)](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Nella casella di ricerca, digitare "ambiente del servizio di integrazione" come filtro.
Nell'elenco dei risultati, selezionare **ambiente del servizio di integrazione (anteprima)**, quindi scegliere **Creare**.

   ![Selezionare "Ambiente del servizio di integrazione"](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Scegliere "Creare"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Specificare questi dettagli per l'ambiente, quindi scegliere **Rivedi e crea**, ad esempio:

   ![Fornire i dettagli dell'ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Proprietà | Obbligatoria | Value | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **Sottoscrizione** | Sì | <*nome sottoscrizione di Azure*> | Sottoscrizione di Azure da usare per l'ambiente |
   | **Gruppo di risorse** | Sì | <*Azure-resource-group-name*> | Il gruppo di risorse di Azure in cui si desidera creare l'ambiente |
   | **Nome dell'ambiente del servizio di integrazione** | Sì | <*Nome ambiente*> | Il nome da assegnare all'ambiente |
   | **Posizione** | Sì | <*Azure-datacenter-region*> | L'area del datacenter di Azure in cui distribuire l'ambiente |
   | **Capacità aggiuntiva** | Sì | 0, 1, 2, 3 | Il numero di unità di elaborazione da usare per questa risorsa ISE |
   | **Rete virtuale** | Sì | <*Azure-virtual-network-name*> | La rete virtuale di Azure in cui si desidera collegare l'ambiente in modo che le app per la logica in quell'ambiente possano accedere alla rete virtuale. Se non è disponibile una rete, è possibile crearne una qui. <p>**Importante**: È possibile seguire questo collegamento *solo* quando si crea l'ISE. Tuttavia, prima di creare questa relazione, assicurarsi che nella rete virtuale sia già [configurato il controllo degli accessi in base al ruolo per le App per la logica di Azure](#vnet-access). |
   | **Subnet** | Sì | <*subnet-resource-list*> | Un ISE richiede quattro subnet *vuote* per la creazione di risorse nell'ambiente. Verificare quindi che queste subnet *non siano delegate* a nessun servizio. *Non è possibile modificare* gli indirizzi di queste subnet dopo aver creato l'ambiente. <p><p>Per creare ciascuna subnet, [seguire i passaggi descritti in questa tabella](#create-subnet). Ogni subnet deve soddisfare questi criteri: <p>- Deve essere vuota. <br>- Usa un nome che non inizi con un numero o un trattino. <br>- Usa il [formato CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e uno spazio indirizzi di classe B. <br>- Include almeno un elemento `/27` nello spazio indirizzi, quindi la subnet ottiene almeno 32 indirizzi. Per informazioni sul calcolo del numero di indirizzi, vedere [Blocchi CIDR IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks). Ad esempio:  <p>- `10.0.0.0/24` ha 256 indirizzi perché 2<sup>(32-24)</sup> è 2<sup>8</sup> o 256. <br>- `10.0.0.0/27` ha 32 indirizzi perché 2<sup>(32-27)</sup> è 2<sup>5</sup> o 32. <br>- `10.0.0.0/28` ha solo 16 indirizzi perché 2<sup>(32-28)</sup> è 2<sup>4</sup> o 16. |
   |||||

   <a name="create-subnet"></a>

   **Creare una subnet**

   1. Nell'elenco **Subnet**, scegliere **Configurazione delle subnet di gestione**.

      ![Gestisci configurazione subnet](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Nel riquadro **Subnet**, scegliere **Subnet**.

      ![Aggiungi subnet](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Nel riquadro **Aggiungi subnet**, specificare queste informazioni.

      * **Nome**: Nome per la subnet
      * **Intervallo di indirizzi (blocco CIDR)**: L'intervallo di subnet nella rete virtuale e nel formato CIDR

      ![Aggiungere i dettagli della subnet](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Al termine dell'operazione, scegliere **OK**.

   1. Ripetere questi passaggi per altre tre subnet.

1. Dopo l'avvenuta convalida da parte di Azure delle informazioni dell'ISE, scegliere **Crea**, ad esempio:

   ![Al termine della convalida, scegliere "Crea"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure avvia la distribuzione dell'ambiente, ma questo processo *potrebbe* richiedere fino a due ore per essere completato. 
   Per controllare lo stato della distribuzione, sulla barra degli strumenti di Azure, scegliere l'icona delle notifiche, che consente di aprire il riquadro notifiche.

   ![Controllare lo stato della distribuzione](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se la distribuzione avviene con successo, Azure Mostra questa notifica:

   ![Distribuzione completata](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   > [!NOTE]
   > Se si verifica un errore di distribuzione o si elimina l'ISE, Azure *potrebbe* richiedere fino a un'ora prima di rilasciare le subnet. Pertanto, potrebbe essere necessario attendere prima di riusare tali subnet in un altro ISE.

1. Per visualizzare l'ambiente, scegliere **Vai alla risorsa** se Azure non mostra automaticamente l'ambiente al termine della distribuzione.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Creare un'app per la logica - ISE

Per creare app per la logica che usano l'ambiente del servizio di integrazione (ISE), seguire la procedura illustrata sotto la sezione [come creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md), ma con le seguenti differenze: 

* Quando si crea l'app per la logica, nella proprietà **Posizione**, selezionare l'ISE nella sezione **Ambienti del servizio di integrazione**, ad esempio:

  ![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* È possibile usare gli stessi trigger e azioni incorporati, come il trigger HTTP, che vengono eseguiti nello stesso ISE dell'app per la logica. Anche i connettori con l'etichetta **ISE** vengono eseguiti nello stesso ISE dell'app per la logica. I connettori senza l'etichetta **ISE** vengono eseguiti nel servizio App per la logica globale.

  ![Selezionare i connettori ISE](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Dopo aver collegato l'ISE in una rete virtuale di Azure, le app per la logica nell'ISE possono accedere direttamente alle risorse di quella rete virtuale. Nei sistemi locali connessi a una rete virtuale, collegare un ISE in suddetta rete per consentire l'accesso diretto delle app per la logica a tali sistemi tramite uno di questi elementi: 

  * Connettore ISE per tale sistema, ad esempio, SQL Server
  
  * Azione HTTP 
  
  * Connettore personalizzato

  Nei sistemi locali non inclusi in una rete virtuale o senza connettori ISE, [configurare il gateway dati locale](../logic-apps/logic-apps-gateway-install.md) come prima cosa.

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Creare account di integrazione - ISE

Per usare un account di integrazione con app per la logica in un ambiente del servizio di integrazione (ISE), l'account di integrazione deve usare lo *stesso ambiente* usato dalle app per la logica. Le app per la logica in un ISE possono fare riferimento solo agli account di integrazione che sono nello stesso ISE. 

Per creare un account di integrazione che usa un ISE, seguire la procedura riportata nella sezione su [come creare gli account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), con la differenza della sezione **Ambienti del servizio di integrazione** ora presente nella proprietà **Posizione**. Invece di un'area, selezionare l'ISE, ad esempio:

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum di App per la logica di Azure</a>.
* Per votare o inviare idee relative alle funzionalità, visitare il <a href="https://aka.ms/logicapps-wish" target="_blank">sito dei commenti e suggerimenti degli utenti di App per la logica</a>.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* Informazioni sull'[integrazione della rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
