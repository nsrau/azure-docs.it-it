---
title: Distribuire Avere vFXT per Azure
description: Procedura per la distribuzione del cluster Avere vFXT in Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: rohogue
ms.openlocfilehash: 6ddf950bf2d138a94675ee394109a0d227ea206b
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255462"
---
# <a name="deploy-the-vfxt-cluster"></a>Distribuire il cluster vFXT

Questo articolo illustra come usare la procedura guidata per la distribuzione disponibile in Azure Marketplace. La procedura guidata distribuisce automaticamente il cluster usando un modello di Azure Resource Manager. Dopo che si sono immessi i parametri nel modulo e si è fatto clic su **Crea**, Azure completa automaticamente questi passaggi:

* Crea il controller del cluster, ovvero una macchina virtuale di base che contiene il software necessario per la distribuzione e la gestione del cluster.
* Configura il gruppo di risorse e l'infrastruttura di rete virtuale, inclusa la creazione di nuovi elementi.
* Consente di creare le macchine virtuali del nodo del cluster e di configurarle come cluster.
* Se richiesto, crea un nuovo contenitore BLOB di Azure e lo configura come un file di base del cluster.

Dopo aver seguito le istruzioni riportate in questo documento, si disporrà di una rete virtuale, una subnet, un controller e un cluster vFXT, come illustrato nel diagramma seguente. Questo diagramma mostra il file di Azure BLOB Core facoltativo, che include un nuovo contenitore di archiviazione BLOB (in un nuovo account di archiviazione, non mostrato) e un endpoint di servizio per l'archiviazione Microsoft all'interno della subnet. 

![diagramma che mostra tre rettangoli concentrici con i componenti del cluster. Il rettangolo esterno è denominato "gruppo di risorse" e contiene un esagono con etichetta "BLOB Storage (facoltativo)". Il rettangolo successivo in è denominato "rete virtuale: 10.0.0.0/16' e non contiene componenti univoci. Il rettangolo più interno è denominato "subnet: 10.0.0.0/24" e contiene una macchina virtuale denominata "controller cluster", uno stack di tre macchine virtuali con etichetta "vFXT nodes (vFXT cluster)" e un esagono con etichetta "service endpoint". È presente una freccia che connette l'endpoint del servizio (che si trova all'interno della subnet) e l'archiviazione BLOB (che si trova all'esterno della subnet e VNET nel gruppo di risorse). La freccia passa attraverso i limiti della subnet e della rete virtuale.](media/avere-vfxt-deployment.png)  

Prima di usare il modello di creazione, verificare che siano soddisfatti i prerequisiti seguenti:  

1. [Nuova sottoscrizione](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Autorizzazioni di proprietario della sottoscrizione](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Quota per il cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Endpoint del servizio di archiviazione (se necessario)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) : necessario per le distribuzioni usando una rete virtuale esistente e la creazione dell'archiviazione BLOB

Per altre informazioni sulla procedura di distribuzione e sulla pianificazione del cluster, vedere [Pianificare il sistema Avere vFXT](avere-vfxt-deploy-plan.md) e [Panoramica della distribuzione](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Creare Avere vFXT per Azure

Accedere al modello di creazione nel portale di Azure cercando di avere e selezionando "vFXT per Azure ARM template". 

![Finestra del browser in cui è visualizzato il portale di Azure con il percorso di navigazione "Nuovo > Marketplace > Tutto". Nella pagina Everything il campo Search ha il termine "ha" e il secondo risultato, "vFXT per Azure ARM template" è indicato in rosso per evidenziarlo.](media/avere-vfxt-template-choose.png)

Dopo aver letto i dettagli nella pagina di vFXT per il modello ARM di Azure, fare clic su **Crea** per iniziare. 

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

* **Sottoscrizione**: selezionare la sottoscrizione per Avere vFXT. 

* **Gruppo di risorse**: selezionare un gruppo di risorse vuoto esistente per il cluster Avere vFXT oppure creare un nuovo gruppo di risorse facendo clic su "Crea nuovo" e immettendo un nome. 

* **Località**: selezionare la località di Azure relativa al cluster e alle risorse.

Al termine, fare clic su **OK**. 

> [!NOTE]
> Se si preferisce che il controller del cluster abbia un indirizzo IP pubblico, creare una nuova rete virtuale per il cluster invece di selezionarne una esistente. Questa impostazione si trova nella seconda pagina.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Parametri della seconda pagina: informazioni sul cluster vFXT

La seconda pagina del modello di distribuzione consente di configurare le dimensioni del cluster, il tipo di nodo, le dimensioni della cache e i parametri di archiviazione, tra le altre impostazioni. 

![Seconda pagina del modello di distribuzione](media/avere-vfxt-deploy-2.png)

* **Avere vFXT cluster node count** (Numero di nodi del cluster Avere vFXT): scegliere il numero di nodi da usare nel cluster. Il valore minimo è tre nodi, quello massimo dodici nodi. 

* **Cluster administration password** (Password di amministrazione del cluster): creare la password per l'amministrazione del cluster. Questa password verrà usata con il nome utente ```admin``` per accedere al pannello di controllo del cluster e poter monitorare il cluster e configurare le impostazioni.

* **Avere vFXT cluster name** (Nome del cluster Avere vFXT): assegnare al cluster un nome univoco. 

* **Dimensioni** : questa sezione Mostra il tipo di macchina virtuale che verrà usato per i nodi del cluster. Sebbene sia disponibile una sola opzione consigliata, il collegamento **modifica dimensioni** apre una tabella con i dettagli relativi a questo tipo di istanza e un collegamento a un calcolatore dei prezzi.  

* **Cache size per node** (Dimensione della cache per ogni nodo): la cache del cluster viene distribuita tra i nodi del cluster, in modo che la dimensione totale della cache disponibile nel cluster Avere vFXT possa essere ottenuta moltiplicando la dimensione della cache assegnata a ogni nodo per il numero di nodi. 

  La configurazione consigliata prevede l'uso di 4 TB per nodo per i nodi Standard_E32s_v3.

* **Rete virtuale** : definire un nuovo VNET per ospitare il cluster oppure selezionare un VNET esistente che soddisfi i prerequisiti descritti in [pianificare il sistema vFXT](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure). 

  > [!NOTE]
  > Se si crea una nuova rete virtuale, al controller del cluster verrà assegnato un indirizzo IP pubblico in modo che sia possibile accedere alla nuova rete privata. Se invece si sceglie una rete virtuale esistente, il controller del cluster viene configurato senza un indirizzo IP pubblico. 
  > 
  > Se al controller del cluster viene assegnato un indirizzo IP visibile pubblicamente, è possibile accedere più facilmente al cluster vFXT ma si crea un piccolo rischio di sicurezza. 
  >  * Un indirizzo IP pubblico nel controller del cluster ne permette l'uso come host di collegamento, per la connessione al cluster Avere vFXT dall'esterno della subnet privata.
  >  * Se non si configura un indirizzo IP pubblico nel controller, è necessario usare un altro host di collegamento, una connessione VPN oppure ExpressRoute per accedere al cluster. È possibile, ad esempio, creare il controller all'interno di una rete virtuale con una connessione VPN già configurata.
  >  * Se si crea un controller con un indirizzo IP pubblico, è consigliabile proteggere la macchina virtuale controller con un gruppo di sicurezza di rete. Per impostazione predefinita, la distribuzione di Avere vFXT per Azure crea un gruppo di sicurezza di rete e limita l'accesso in ingresso alla porta 22 per i controller con indirizzi IP pubblici. È possibile proteggere ulteriormente il sistema limitando l'accesso al proprio intervallo di indirizzi di origine IP, ovvero consentendo solo connessioni provenienti dai computer che si intende usare per l'accesso al cluster.

  Il modello di distribuzione configura anche il nuovo VNET con un endpoint del servizio di archiviazione per l'archiviazione BLOB di Azure e con controllo di accesso alla rete bloccato solo per gli indirizzi IP dalla subnet del cluster. 

* **Subnet**: scegliere una subnet da una rete virtuale esistente oppure crearne una nuova. 

* **Creare e usare l'archiviazione BLOB** : scegliere **true** per creare un nuovo contenitore BLOB di Azure e configurarlo come archiviazione back-end per il nuovo cluster vFXT. Questa opzione crea anche un nuovo account di archiviazione nello stesso gruppo di risorse del cluster e un endpoint del servizio di archiviazione Microsoft all'interno della subnet del cluster. 
  
  Se si specifica una rete virtuale esistente, è necessario che disponga di un endpoint del servizio di archiviazione prima di creare il cluster. Per altre informazioni, vedere [pianificare il sistema vFXT](avere-vfxt-deploy-plan.md).

  Impostare questo campo su **false** se non si vuole creare un nuovo contenitore. In questo caso è necessario associare e configurare la risorsa di archiviazione dopo la creazione del cluster. Per le istruzioni, leggere [Configurare l'archivio](avere-vfxt-add-storage.md). 

* **(Nuovo) account di archiviazione** : se si crea un nuovo contenitore BLOB di Azure, immettere un nome per il nuovo account di archiviazione. 

## <a name="validation-and-purchase"></a>Convalida e acquisto

Nella pagina tre viene riepilogata la configurazione e vengono convalidati i parametri. Al termine del processo di convalida, fare clic sul pulsante **OK** per continuare. 

![Terza pagina del modello di distribuzione - convalida](media/avere-vfxt-deploy-3.png)

Nella pagina quattro immettere le informazioni di contatto richieste e fare clic sul pulsante **Crea** per accettare i termini e creare il vFXT per il cluster di Azure. 

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

## <a name="next-step"></a>Passaggio successivo

Ora che il cluster è in esecuzione e si conosce l'indirizzo IP di gestione, è possibile [connettersi allo strumento di configurazione del cluster](avere-vfxt-cluster-gui.md) per abilitare il supporto, aggiungere la risorsa di archiviazione, se necessaria, e personalizzare altre impostazioni del cluster.
