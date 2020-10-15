---
title: Distribuire Avere vFXT per Azure
description: Informazioni su come usare la distribuzione guidata disponibile in Azure Marketplace per distribuire un cluster con vFXT per Azure.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 31d12466186bb7f66197218fbb9675888a35fef3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88272774"
---
# <a name="deploy-the-vfxt-cluster"></a>Distribuire il cluster vFXT

Questo articolo illustra come usare la procedura guidata per la distribuzione disponibile in Azure Marketplace. La procedura guidata distribuisce automaticamente il cluster usando un modello di Azure Resource Manager. Dopo aver immesso i parametri nel modulo e fatto clic su **Crea**, Azure completa automaticamente le attività seguenti:

* Crea il controller del cluster, ovvero una macchina virtuale di base che contiene il software necessario per la distribuzione e la gestione del cluster.
* Configura il gruppo di risorse e l'infrastruttura di rete virtuale, inclusa la creazione di nuovi elementi.
* Consente di creare le macchine virtuali del nodo del cluster e di configurarle come cluster.
* Se richiesto, crea un nuovo contenitore BLOB di Azure e lo configura come un file di base del cluster.

Dopo aver seguito le istruzioni riportate in questo documento, si disporrà di una rete virtuale, una subnet, un controller cluster e un cluster vFXT, come illustrato nella figura seguente. Questo diagramma mostra il file di Azure BLOB Core facoltativo, che include un nuovo contenitore di archiviazione BLOB (in un nuovo account di archiviazione, non mostrato) e un endpoint di servizio per l'archiviazione Microsoft all'interno della subnet.

![diagramma che mostra tre rettangoli concentrici con i componenti del cluster. Il rettangolo esterno è denominato "gruppo di risorse" e contiene un esagono con etichetta "BLOB Storage (facoltativo)". Il rettangolo successivo in è denominato "rete virtuale: 10.0.0.0/16" e non contiene componenti univoci. Il rettangolo più interno è denominato "subnet: 10.0.0.0/24" e contiene una macchina virtuale denominata "controller cluster", uno stack di tre macchine virtuali con etichetta "vFXT nodes (vFXT cluster)" e un esagono con etichetta "service endpoint". È presente una freccia che connette l'endpoint del servizio (che si trova all'interno della subnet) e l'archiviazione BLOB (che si trova all'esterno della subnet e VNET nel gruppo di risorse). La freccia passa attraverso i limiti della subnet e della rete virtuale.](media/avere-vfxt-deployment.png)

Prima di usare il modello di creazione, verificare che siano soddisfatti i prerequisiti seguenti:  

* [Nuova sottoscrizione](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Autorizzazioni di proprietario della sottoscrizione](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Quota per il cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Endpoint del servizio di archiviazione (se necessario)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) : necessario per le distribuzioni che usano una rete virtuale esistente e creano l'archiviazione BLOB

Per altre informazioni sulla procedura di distribuzione e sulla pianificazione del cluster, vedere [Pianificare il sistema Avere vFXT](avere-vfxt-deploy-plan.md) e [Panoramica della distribuzione](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Creare Avere vFXT per Azure

Accedere al modello di creazione nel portale di Azure cercando di avere e selezionando "vFXT per Azure ARM template".

![Finestra del browser in cui è visualizzato il portale di Azure con il percorso di navigazione "Nuovo > Marketplace > Tutto". Nella pagina Everything il campo Search ha il termine "ha" e il secondo risultato, "vFXT per Azure ARM template" è indicato in rosso per evidenziarlo.](media/avere-vfxt-template-choose.png)

Dopo aver letto i dettagli nella pagina di vFXT per il modello ARM di Azure, fare clic sul pulsante **Crea** per iniziare.

![Azure Marketplace con la prima pagina del modello di distribuzione](media/avere-vfxt-deploy-first.png)

Il modello è suddiviso in quattro parti: due pagine per la raccolta di informazioni e due passaggi di convalida e di conferma.

* La pagina One raccoglie le impostazioni per la macchina virtuale del controller cluster.
* La pagina due raccoglie i parametri per la creazione del cluster e risorse aggiuntive, come subnet e archiviazione.
* La pagina tre riepiloga le scelte e convalida la configurazione.
* La quarta pagina illustra le condizioni di utilizzo del software e consente di avviare il processo di creazione del cluster.

## <a name="page-one-parameters---cluster-controller-information"></a>Parametri della prima pagina - Informazioni sul controller del cluster

La prima pagina del modello di distribuzione è incentrata sul controller del cluster.

![Prima pagina del modello di distribuzione](media/avere-vfxt-deploy-1.png)

Specificare le informazioni seguenti:

* **Cluster controller name** (Nome controller del cluster): specificare il nome della macchina virtuale controller del cluster.

* **Nome utente del controller** : impostare il nome utente radice per la macchina virtuale del controller cluster.

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

* Il conteggio dei nodi del **cluster vFXT** : scegliere il numero di nodi nel cluster. Il valore minimo è tre nodi, quello massimo dodici nodi.

* **Cluster administration password** (Password di amministrazione del cluster): creare la password per l'amministrazione del cluster. Questa password viene usata con il nome utente ```admin``` per accedere al pannello di controllo del cluster, in cui è possibile monitorare il cluster e configurare le impostazioni del cluster.

* **Avere vFXT cluster name** (Nome del cluster Avere vFXT): assegnare al cluster un nome univoco.

* **Dimensioni** : questa sezione Mostra il tipo di macchina virtuale che verrà usato per i nodi del cluster. Sebbene sia disponibile una sola opzione consigliata, il collegamento **modifica dimensioni** apre una tabella con i dettagli relativi a questo tipo di istanza e un collegamento a un calcolatore prezzi.

* **Dimensioni della cache per nodo** : la cache del cluster viene distribuita tra i nodi del cluster, quindi le dimensioni totali della cache nel cluster vFXT sono moltiplicate per il numero di nodi.

  Configurazione consigliata: usare 4 TB per nodo per Standard_E32s_v3 nodi.

* **Rete virtuale** : definire una nuova rete virtuale per ospitare il cluster oppure selezionare una rete esistente che soddisfi i prerequisiti descritti in [pianificare il sistema vFXT](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure).

  > [!NOTE]
  > Se si crea una nuova rete virtuale, il controller del cluster disporrà di un indirizzo IP pubblico per poter accedere alla nuova rete privata. Se si sceglie una rete virtuale esistente, il controller del cluster viene configurato senza un indirizzo IP pubblico.
  >
  > Se al controller del cluster viene assegnato un indirizzo IP visibile pubblicamente, è possibile accedere più facilmente al cluster vFXT ma si crea un piccolo rischio di sicurezza.
  >* Un indirizzo IP pubblico nel controller del cluster ne permette l'uso come host di collegamento, per la connessione al cluster Avere vFXT dall'esterno della subnet privata.
  >* Se non si dispone di un indirizzo IP pubblico sul controller, è necessario un altro host Jump, una connessione VPN o ExpressRoute per accedere al cluster. Ad esempio, usare una rete virtuale esistente per cui è già stata configurata una connessione VPN.
  >* Se si crea un controller con un indirizzo IP pubblico, è consigliabile proteggere la macchina virtuale controller con un gruppo di sicurezza di rete. Per impostazione predefinita, la distribuzione di vFXT per Azure crea un gruppo di sicurezza di rete che limita l'accesso in ingresso solo alla porta 22 per i controller con indirizzi IP pubblici. È possibile proteggere ulteriormente il sistema limitando l'accesso al proprio intervallo di indirizzi di origine IP, ovvero consentendo solo connessioni provenienti dai computer che si intende usare per l'accesso al cluster.

  Una nuova rete virtuale viene anche configurata con un endpoint del servizio di archiviazione per l'archiviazione BLOB di Azure e con controllo di accesso alla rete bloccato per consentire solo gli indirizzi IP dalla subnet del cluster.

* **Subnet** : scegliere una subnet o crearne una nuova.

* **Creare e usare l'archiviazione BLOB** : scegliere **true** per creare un nuovo contenitore BLOB di Azure e configurarlo come archiviazione back-end per il nuovo cluster vFXT. Questa opzione crea anche un nuovo account di archiviazione nel gruppo di risorse del cluster e crea un endpoint del servizio di archiviazione Microsoft all'interno della subnet del cluster.
  
  Se si specifica una rete virtuale esistente, è necessario che disponga di un endpoint del servizio di archiviazione prima di creare il cluster. Per altre informazioni, vedere [pianificare il sistema vFXT](avere-vfxt-deploy-plan.md).

  Impostare questo campo su **false** se non si vuole creare un nuovo contenitore. In questo caso, è necessario aggiungere e configurare l'archiviazione dopo aver creato il cluster. Per le istruzioni, leggere [Configurare l'archivio](avere-vfxt-add-storage.md).

* **(Nuovo) account di archiviazione** : se si crea un nuovo contenitore BLOB di Azure, immettere un nome per il nuovo account di archiviazione.

## <a name="validation-and-purchase"></a>Convalida e acquisto

Nella pagina tre viene riepilogata la configurazione e vengono convalidati i parametri. Una volta completata la convalida, controllare il riepilogo e fare clic sul pulsante **OK** .

> [!TIP]
> È possibile salvare le impostazioni di creazione del cluster facendo clic sul collegamento **Scarica modello e parametri** accanto al pulsante **OK** . Queste informazioni possono essere utili se è necessario creare un cluster simile in un secondo momento, ad esempio per creare un cluster sostitutivo in uno scenario di ripristino di emergenza. Per altre informazioni, vedere [indicazioni sul ripristino di emergenza](disaster-recovery.md) .

![Terza pagina del modello di distribuzione - convalida](media/avere-vfxt-deploy-3.png)

La quarta pagina fornisce le condizioni per l'utilizzo e i collegamenti a informazioni sulla privacy e sui prezzi.

Immettere le informazioni di contatto mancanti, quindi fare clic sul pulsante **Crea** per accettare i termini e creare il vFXT di ricerca per il cluster di Azure.

![Quarta pagina del modello di distribuzione - condizioni di utilizzo del software, pulsante di creazione](media/avere-vfxt-deploy-4.png)

La distribuzione del cluster richiede 15-20 minuti.

## <a name="gather-template-output"></a>Raccogliere l'output del modello

Al termine della creazione del cluster da parte del modello vFXT, vengono restituite informazioni importanti sul nuovo cluster.

> [!TIP]
> Assicurarsi di copiare l' **indirizzo IP di gestione** dall'output del modello. Questo indirizzo sarà necessario per amministrare il cluster.

Per trovare le informazioni:

1. Passare al gruppo di risorse del controller cluster.

1. A sinistra fare clic su **Distribuzioni** e quindi su **microsoft-avere.vfxt-template**.

   ![Pagina del portale del gruppo di risorse con l'opzione Distribuzioni selezionata a sinistra e microsoft-avere.vfxt-template in una tabella nella colonna Nome distribuzione](media/avere-vfxt-outputs-deployments.png)

1. A sinistra fare clic su **Output**. Copiare i valori in ognuno dei campi.

   ![Pagina dell'output con i valori SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs e MGMT_IP nei campi a destra delle etichette](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che il cluster è in esecuzione e si conosce l'indirizzo IP di gestione, [connettersi allo strumento di configurazione del cluster](avere-vfxt-cluster-gui.md).

Usare l'interfaccia di configurazione per personalizzare il cluster, incluse le attività di configurazione seguenti:

* [Abilitare il supporto](avere-vfxt-enable-support.md)
* [Aggiungi spazio di archiviazione](avere-vfxt-add-storage.md) (se necessario)
