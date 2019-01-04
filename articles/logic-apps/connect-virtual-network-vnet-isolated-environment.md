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
ms.date: 12/06/2018
ms.openlocfilehash: b0fd2466d72b1aae65a54b9e9813a5af51bf1672
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997522"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-through-an-integration-service-environment-ise"></a>Connettere le reti virtuali di Azure da App per la logica di Azure attraverso un ambiente del servizio di integrazione (ISE)

> [!NOTE]
> Questa funzionalità è in *anteprima privata*. Per richiedere l'accesso, [creare la richiesta di iscrizione qui](https://aka.ms/iseprivatepreview).

Per gli scenari in cui le app per la logica e gli account di integrazione devono accedere a una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md), creare un [*ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Un ISE è un ambiente isolato e privato che usa una risorsa di archiviazione dedicata e altre risorse separate dal servizio di App per la logica di Azure pubblico o *globale*. Questa separazione riduce anche qualsiasi impatto che altri tenant di Azure possono avere sulle prestazioni delle app create. L'ISE è *collegato* alla rete virtuale di Azure, che quindi distribuisce il servizio App per la logica nella rete virtuale. Quando si crea un'app per la logica o un account di integrazione, selezionare questo ISE come posizione per l'app o l'account. L'app per la logica o l'account di integrazione può quindi accedere direttamente alle risorse, ad esempio alle macchine virtuali (VM), ai server, ai sistemi e ai servizi della rete virtuale in uso. 

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Questo articolo descrive come portare a termine le attività seguenti:

* Configurare le autorizzazioni per la rete virtuale di Azure in modo che l'istanza privata delle app per la logica possa accedere alla rete virtuale.

* Creare un ambiente del servizio di integrazione (ISE). 

* Creare un'app per la logica che possa essere eseguita nell'ISE. 

* Creare un account di integrazione e collegarlo alle app per la logica presenti nell'ISE.

Per altre informazioni sugli ambienti del servizio di integrazione, vedere [Accedere alle risorse della rete virtuale di Azure tramite App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* Una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Se non si dispone di una rete virtuale, vedere l'articolo su come [creare una rete virtuale di Azure](../virtual-network/quick-create-portal.md). 

* Per consentire alle app per la logica un accesso diretto alla rete virtuale di Azure [configurare le autorizzazioni del Controllo degli accessi in base al ruolo (RBAC)](#vnet-access) in modo che il servizio delle app per la logica disponga delle autorizzazioni di accesso alla rete virtuale. 

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

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

   | Proprietà | Obbligatoria | Valore | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **Sottoscrizione** | Yes | <*nome sottoscrizione di Azure*> | Sottoscrizione di Azure da usare per l'ambiente | 
   | **Gruppo di risorse** | Yes | <*Azure-resource-group-name*> | Il gruppo di risorse di Azure in cui si desidera creare l'ambiente |
   | **Nome dell'ambiente del servizio di integrazione** | Yes | <*Nome ambiente*> | Il nome da assegnare all'ambiente | 
   | **Posizione** | Yes | <*Azure-datacenter-region*> | L'area del datacenter di Azure in cui distribuire l'ambiente | 
   | **Capacity** | Yes | 0, 1, 2, 3 | Il numero di unità di elaborazione da usare per questa risorsa ISE | 
   | **Rete virtuale** | Yes | <*Azure-virtual-network-name*> | La rete virtuale di Azure in cui si desidera collegare l'ambiente in modo che le app per la logica in quell'ambiente possano accedere alla rete virtuale. Se non è disponibile una rete, è possibile crearne una qui. <p>**Importante**: È possibile seguire questo collegamento *solo* quando si crea l'ISE. Tuttavia, prima di creare questa relazione, assicurarsi che nella rete virtuale sia già [configurato il controllo degli accessi in base al ruolo per le App per la logica di Azure](#vnet-access). | 
   | **Subnet** | Yes | <*IP-address-range*> | Un ISE richiede quattro subnet *vuote*. Queste subnet non sono delegate a nessun servizio e vengono usate per la creazione di risorse nell'ambiente in uso. *Non è possibile modificare* questi intervalli IP dopo aver creato l'ambiente. <p><p>Per creare ciascuna subnet, [seguire i passaggi descritti in questa tabella](#create-subnet). Ogni subnet deve soddisfare questi criteri: <p>- Non deve essere presente nello stesso intervallo di indirizzi per la rete virtuale selezionata, né in nessun altro indirizzo IP privato in cui la rete virtuale è connessa. <br>- Usa un nome che non inizi con un numero o un trattino. <br>- Usa il [formato CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). <br>- Richiede uno spazio di indirizzi di classe B. <br>- Include un `/27`. Ad esempio, le subnet seguenti specificano un intervallo di indirizzi a 32 bit: `10.0.0.0/27`, `10.0.0.32/27`, `10.0.0.64/27`, e `10.0.0.96/27`. <br>- Deve essere vuota. |
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
