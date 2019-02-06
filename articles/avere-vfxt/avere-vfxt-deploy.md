---
title: Distribuire Avere vFXT per Azure
description: Procedura per la distribuzione del cluster Avere vFXT in Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: da329b5c50fe7c39d9773743b40c2f990e298963
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296376"
---
# <a name="deploy-the-vfxt-cluster"></a>Distribuire il cluster vFXT

Questo articolo illustra come usare la procedura guidata per la distribuzione disponibile in Azure Marketplace. La procedura guidata distribuisce automaticamente il cluster usando un modello di Azure Resource Manager. Dopo che si sono immessi i parametri nel modulo e si è fatto clic su **Crea**, Azure completa automaticamente questi passaggi: 

* Crea il controller del cluster, ovvero una macchina virtuale di base contenente il software necessario per distribuire e gestire il cluster.
* Configura il gruppo di risorse e l'infrastruttura di rete virtuale e, se necessario, crea nuovi elementi.
* Crea le macchine virtuali del nodo del cluster e le configura come il cluster Avere.
* Se necessario, crea un nuovo contenitore BLOB di Azure e lo configura come un core filer del cluster.

Dopo aver seguito le istruzioni in questo documento, si disporrà di una rete virtuale, una subnet, un controller e un cluster vFXT, come illustrato nel diagramma seguente:

![Diagramma che mostra la rete virtuale contenente l'archiviazione BLOB facoltativa e una subnet contenente tre macchine virtuali raggruppate etichettate come nodi vFXT/cluster vFXT e una macchina virtuale etichettata come controller del cluster](media/avere-vfxt-deployment.png)

Se si usa l'archiviazione BLOB, dopo aver creato il cluster, è necessario [creare un endpoint di archiviazione](#create-a-storage-endpoint-if-using-azure-blob) nella propria rete virtuale. 

Prima di usare il modello di creazione, verificare che siano soddisfatti i prerequisiti seguenti:  

1. [Nuova sottoscrizione](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Autorizzazioni di proprietario della sottoscrizione](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Quota per il cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Ruoli personalizzati di accesso](avere-vfxt-prereqs.md#create-access-roles): è necessario creare un ruolo di controllo degli accessi in base al ruolo da assegnare ai nodi del cluster. È possibile anche creare un ruolo personalizzato di accesso per il controller del cluster, ma la maggior parte degli utenti sceglierà il ruolo predefinito di proprietario, che concede sul controller i privilegi corrispondenti al proprietario di un gruppo di risorse. Per altre informazioni, vedere [Ruoli predefiniti per le risorse di Azure](../role-based-access-control/built-in-roles.md#owner).

Per altre informazioni sulla procedura di distribuzione e sulla pianificazione del cluster, vedere [Pianificare il sistema Avere vFXT](avere-vfxt-deploy-plan.md) e [Panoramica della distribuzione](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Creare Avere vFXT per Azure

Accedere al modello di creazione nel portale di Azure cercando Avere e selezionando "Avere vFXT for Azure Deployment". <!-- xxx update if that name changes xxx --> 

<!-- **[XXX need production image of template deploy in search and/or entry page of template deploy XXX]** -->

Fare clic su **Crea** per iniziare. 

![Azure Marketplace con la prima pagina del modello di distribuzione](media/avere-vfxt-deploy-first.png)

Il modello è suddiviso in quattro parti: due pagine per la raccolta di informazioni e due passaggi di convalida e di conferma. 

* La prima pagina è dedicata alle impostazioni della macchina virtuale controller del cluster. 
* La seconda pagina raccoglie i parametri per la creazione del cluster e delle risorse associate, tra cui le subnet e le risorse di archiviazione. 
* La terza pagina riepiloga le impostazioni e convalida la configurazione. 
* La quarta pagina illustra le condizioni di utilizzo del software e consente di avviare il processo di creazione del cluster. 

## <a name="page-one-parameters---cluster-controller-information"></a>Parametri della prima pagina - Informazioni sul controller del cluster

La prima pagina del modello di distribuzione raccoglie le informazioni sul controller del cluster. 

![Prima pagina del modello di distribuzione](media/avere-vfxt-deploy-1.png)

Specificare le informazioni seguenti:

* **Cluster controller name** (Nome controller del cluster): specificare il nome della macchina virtuale controller del cluster.

* **Controller username** (Nome utente del controller): specificare il nome utente radice della macchina virtuale controller del cluster. 

* **Tipo di autenticazione**: scegliere se usare una password o l'autenticazione con chiave pubblica SSH per la connessione al controller. È consigliabile usare la chiave pubblica SSH. Per informazioni, vedere [Creare e usare chiavi SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

* **Password** o **Chiave pubblica SSH**: a seconda del tipo di autenticazione selezionato, nei campi successivi è necessario specificare una chiave pubblica RSA o una password. Queste credenziali devono essere usate con il nome utente specificato in precedenza.

* **Avere cluster create role ID** (ID del ruolo di creazione del cluster di Avere): usare questo campo per specificare il ruolo di controllo degli accessi relativo al controller del cluster. Il valore predefinito è il ruolo [Proprietario](../role-based-access-control/built-in-roles.md#owner). Per il controller del cluster, i privilegi di proprietario sono limitati al gruppo di risorse del cluster. 

  È necessario usare l'identificatore univoco globale corrispondente al ruolo scelto. Per il valore predefinito (Proprietario), il GUID è 8e3af657-a8ff-443c-a75c-2fe8c4bcb635. Per trovare il GUID di un ruolo personalizzato, usare questo comando: 

  ```azurecli
  az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
  ```

* **Sottoscrizione**: selezionare la sottoscrizione per Avere vFXT. 

* **Gruppo di risorse**: selezionare il gruppo di risorse per il cluster Avere vFXT o fare clic su "Crea nuovo" e immettere il nome di un nuovo gruppo di risorse. 

* **Località**: selezionare la località di Azure relativa al cluster e alle risorse.

Al termine, fare clic su **OK**. 

> [!NOTE]
> Se si preferisce che il controller del cluster abbia un indirizzo IP pubblico, creare una nuova rete virtuale per il cluster invece di selezionarne una esistente. Questa impostazione si trova nella seconda pagina.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Parametri della seconda pagina: informazioni sul cluster vFXT

La seconda pagina del modello di distribuzione consente di configurare le dimensioni del cluster, il tipo di nodo, le dimensioni della cache e i parametri di archiviazione, tra le altre impostazioni. 

![Seconda pagina del modello di distribuzione](media/avere-vfxt-deploy-2.png)

* **Avere vFXT cluster node count** (Numero di nodi del cluster Avere vFXT): scegliere il numero di nodi da usare nel cluster. Il valore minimo è tre nodi, quello massimo dodici nodi. 

* **Cluster administration password** (Password di amministrazione del cluster): creare la password per l'amministrazione del cluster. Questa password verrà usata con il nome utente ```admin``` per accedere al pannello di controllo del cluster e poter monitorare il cluster e configurare le impostazioni.

* **Avere cluster operations role** (Ruolo delle operazioni del cluster Avere): specificare il nome del ruolo di controllo degli accessi per i nodi del cluster. Si tratta di un ruolo personalizzato creato come passaggio preliminare. 

  Nell'esempio descritto in [Creare il ruolo di accesso dei nodi del cluster](avere-vfxt-prereqs.md#create-the-cluster-node-access-role) il file viene salvato come ```avere-operator.json``` e il nome del ruolo corrispondente è ```avere-operator```.

* **Avere vFXT cluster name** (Nome del cluster Avere vFXT): assegnare al cluster un nome univoco. 

* **Size** (Dimensione): specificare il tipo di macchina virtuale da usare durante la creazione dei nodi del cluster. 

* **Cache size per node** (Dimensione della cache per ogni nodo): la cache del cluster viene distribuita tra i nodi del cluster, in modo che la dimensione totale della cache disponibile nel cluster Avere vFXT possa essere ottenuta moltiplicando la dimensione della cache assegnata a ogni nodo per il numero di nodi. 

  La configurazione consigliata prevede di usare 1 TB per nodo se si usano nodi del cluster Standard_D16s_v3 e 4 TB per nodo se si usano nodi Standard_E32s_v3.

* **Virtual network** (Rete virtuale): selezionare una rete virtuale esistente in cui ospitare il cluster o definire una nuova rete virtuale da creare. 

  > [!NOTE]
  > Se si crea una nuova rete virtuale, al controller del cluster verrà assegnato un indirizzo IP pubblico in modo che sia possibile accedere alla nuova rete privata. Se invece si sceglie una rete virtuale esistente, il controller del cluster viene configurato senza un indirizzo IP pubblico. 
  > 
  > Se al controller del cluster viene assegnato un indirizzo IP visibile pubblicamente, è possibile accedere più facilmente al cluster vFXT ma si crea un piccolo rischio di sicurezza. 
  >  * Un indirizzo IP pubblico nel controller del cluster ne permette l'uso come host di collegamento, per la connessione al cluster Avere vFXT dall'esterno della subnet privata.
  >  * Se non si configura un indirizzo IP pubblico nel controller, è necessario usare un altro host di collegamento, una connessione VPN oppure ExpressRoute per accedere al cluster. È possibile, ad esempio, creare il controller all'interno di una rete virtuale con una connessione VPN già configurata.
  >  * Se si crea un controller con un indirizzo IP pubblico, è consigliabile proteggere la macchina virtuale controller con un gruppo di sicurezza di rete. Per impostazione predefinita, la distribuzione di Avere vFXT per Azure crea un gruppo di sicurezza di rete e limita l'accesso in ingresso alla porta 22 per i controller con indirizzi IP pubblici. È possibile proteggere ulteriormente il sistema limitando l'accesso al proprio intervallo di indirizzi di origine IP, ovvero consentendo solo connessioni provenienti dai computer che si intende usare per l'accesso al cluster.

* **Subnet**: scegliere una subnet da una rete virtuale esistente oppure crearne una nuova. 

* **Use blob storage** (Usa l'archiviazione BLOB): scegliere se creare o meno un nuovo contenitore BLOB di Azure e configurarlo come risorsa da archiviazione back-end per il nuovo cluster Avere vFXT. Se si sceglie di creare un nuovo contenitore, è necessario specificare l'account di archiviazione relativo al contenitore. Se invece si sceglie di non creare un nuovo contenitore BLOB, dopo aver creato il cluster è necessario collegare la risorsa di archiviazione (per informazioni, vedere [Configurare l'archivio](avere-vfxt-add-storage.md)). Impostare questo campo su **false** se non si vuole creare un nuovo contenitore.

* **Storage account** (Account di archiviazione): se si crea un nuovo contenitore BLOB di Azure, immettere il nome dell'account di archiviazione. L'account di archiviazione deve essere un account di tipo v2 standard per utilizzo generico, configurato con archiviazione con ridondanza locale e un livello di accesso frequente. L'articolo [Configurare l'archivio](avere-vfxt-add-storage.md#azure-storage-cloud-core-filer) presenta informazioni più dettagliate sui requisiti dell'account di archiviazione.

## <a name="validation-and-purchase"></a>Convalida e acquisto

La terza pagina offre un riepilogo della configurazione e consente di convalidare i parametri. Al termine del processo di convalida, fare clic sul pulsante **OK** per continuare. 

![Terza pagina del modello di distribuzione - convalida](media/avere-vfxt-deploy-3.png)

Nella quarta pagina fare clic sul pulsante **Crea** per accettare le condizioni e creare il cluster Avere vFXT per Azure. 

![Quarta pagina del modello di distribuzione - condizioni di utilizzo del software, pulsante di creazione](media/avere-vfxt-deploy-4.png)

La distribuzione del cluster richiede 15-20 minuti.

## <a name="gather-template-output"></a>Raccogliere l'output del modello

Quando il modello Avere vFXT termina la creazione del cluster, vengono restituite alcune informazioni importanti sul nuovo cluster. 

> [!TIP]
> Assicurarsi di copiare l'indirizzo IP di gestione dall'output del modello. Questo indirizzo sarà necessario per amministrare il cluster.

Per trovare queste informazioni, seguire questa procedura:

1. Passare al gruppo di risorse del controller cluster.

1. A sinistra fare clic su **Distribuzioni** e quindi su **microsoft-avere.vfxt-template**.

   ![Pagina del portale del gruppo di risorse con l'opzione Distribuzioni selezionata a sinistra e microsoft-avere.vfxt-template in una tabella nella colonna Nome distribuzione](media/avere-vfxt-outputs-deployments.png)

1. A sinistra fare clic su **Output**. Copiare i valori in ognuno dei campi. 

   ![Pagina dell'output con i valori SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs e MGMT_IP nei campi a destra delle etichette](media/avere-vfxt-outputs-values.png)


## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>Creare un endpoint di archiviazione (se si usa l'archiviazione BLOB di Azure)

Se si usa l'archiviazione BLOB di Azure per l'archivio dati back-end, è necessario creare un endpoint di servizio di archiviazione nella rete virtuale. Questo [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) mantiene il traffico BLOB di Azure in locale invece di instradarlo tramite Internet.

1. Nel portale fare clic su **Reti virtuali** a sinistra.
1. Selezionare la rete virtuale per il controller. 
1. Fare clic su **Endpoint servizio** a sinistra.
1. Fare clic su **Aggiungi** nella parte superiore.
1. Lasciare il servizio ``Microsoft.Storage`` e scegliere la subnet del controller.
1. Nella parte inferiore fare clic su **Aggiungi**.

  ![Screenshot del portale di Azure con annotazioni per la procedura di creazione dell'endpoint di servizio](media/avere-vfxt-service-endpoint.png)

## <a name="next-step"></a>Passaggio successivo

Ora che il cluster è in esecuzione e si conosce l'indirizzo IP di gestione, è possibile [connettersi allo strumento di configurazione del cluster](avere-vfxt-cluster-gui.md) per abilitare il supporto, aggiungere la risorsa di archiviazione, se necessaria, e personalizzare altre impostazioni del cluster.
