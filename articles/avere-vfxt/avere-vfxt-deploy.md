---
title: Distribuire Avere vFXT per Azure
description: Procedura per la distribuzione del cluster Avere vFXT in Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: c8bad3642f1e98cac3857d536f539554235e1a51
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578638"
---
# <a name="deploy-the-vfxt-cluster"></a>Distribuire il cluster vFXT

Il modo più semplice per creare un cluster vFXT in Azure è usare un controller del cluster. Il controller del cluster è una macchina virtuale che dispone degli script, dei modelli e dell'infrastruttura software necessari per creare e gestire il cluster vFXT.

La distribuzione di un nuovo cluster vFXT include questi passaggi:

1. [Creare il controller del cluster](#create-the-cluster-controller-vm).
1. Se si usa l'archiviazione BLOB di Azure, [creare un endpoint di archiviazione](#create-a-storage-endpoint-if-using-azure-blob) nella rete virtuale.
1. [Connettersi al controller del cluster](#access-the-controller). I passaggi rimanenti vengono eseguiti dalla macchina virtuale controller del cluster. 
1. [Creare il ruolo di accesso](#create-the-cluster-node-access-role) dei nodi del cluster. Viene fornito un prototipo.
1. [Personalizzare lo script di creazione del cluster](#edit-the-deployment-script) per il tipo di cluster vFXT che si vuole creare.
1. [Eseguire lo script di creazione del cluster](#run-the-script).

Per altre informazioni sulla procedura di distribuzione e sulla pianificazione del cluster, vedere [Pianificare il sistema Avere vFXT](avere-vfxt-deploy-plan.md) e [Panoramica della distribuzione](avere-vfxt-deploy-overview.md). 

Dopo aver seguito le istruzioni in questo documento, si disporrà di una rete virtuale, una subnet, un controller e un cluster vFXT, come illustrato nel diagramma seguente:

![Diagramma che mostra la rete virtuale contenente l'archiviazione BLOB facoltativa e una subnet contenente tre macchine virtuali raggruppate etichettate come nodi vFXT/cluster vFXT e una macchina virtuale etichettata come controller del cluster](media/avere-vfxt-deployment.png)

Prima di iniziare, assicurarsi che siano soddisfatti i prerequisiti seguenti:  

1. [Nuova sottoscrizione](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Autorizzazioni di proprietario della sottoscrizione](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Quota per il cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)

Facoltativamente, è possibile creare il ruolo del nodo cluster [prima](avere-vfxt-pre-role.md) di creare il controller del cluster, ma è più semplice farlo in un secondo momento.

## <a name="create-the-cluster-controller-vm"></a>Creare la macchina virtuale controller del cluster

Il primo passaggio consiste nel creare la macchina virtuale che verrà usata per creare e configurare i nodi del cluster vFXT. 

Il controller del cluster è una macchina virtuale Linux con il software e gli script di creazione del cluster Avere vFXT preinstallati. Non è necessaria una quantità elevata di potenza di elaborazione o spazio di archiviazione, quindi è possibile scegliere le opzioni più economiche. Questa macchina virtuale verrà usata occasionalmente durante il ciclo di vita del cluster vFXT.

Per creare la macchina virtuale controller del cluster, ci sono due metodi. Un [modello di Azure Resource Manager](#create-controller---arm-template) viene fornito [di seguito](#create-controller---arm-template) per semplificare il processo, ma è anche possibile creare il controller dall'[immagine di Azure Marketplace](#create-controller---azure-marketplace-image). 

È possibile creare un nuovo gruppo di risorse come parte della creazione del controller.

> [!TIP]
>
> Scegliere se usare o meno un indirizzo IP pubblico nel controller del cluster. Un indirizzo IP pubblico consente di semplificare l'accesso al cluster vFXT, ma comporta un rischio minimo per la sicurezza.
>
>  * Un indirizzo IP pubblico nel controller del cluster ne permette l'uso come host di collegamento, per la connessione al cluster Avere vFXT dall'esterno della subnet privata.
>  * Se non si configura un indirizzo IP pubblico nel controller, è necessario usare un altro host di collegamento, una connessione VPN oppure ExpressRoute per accedere al cluster. Ad esempio, creare il controller all'interno di una rete virtuale con una connessione VPN configurata.
>  * Se si crea un controller con un indirizzo IP pubblico, è consigliabile proteggere la macchina virtuale controller con un gruppo di sicurezza di rete. Consentire inoltre l'accesso solo tramite la porta 22, per fornire accesso a Internet.

### <a name="create-controller---resource-manager-template"></a>Creare il controller - Modello di Resource Manager

Per creare il nodo del controller del cluster dal portale, fare clic sul pulsante per la distribuzione in Azure qui sotto. Questo modello di distribuzione crea la macchina virtuale che verrà usata per creare e gestire il cluster Avere vFXT.

[![Pulsante per la creazione del controller](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fazuredeploy.json)

Fornire le informazioni seguenti.

Nella sezione **DI BASE**:  

* **Sottoscrizione** per il cluster
* **Gruppo di risorse** per il cluster 
* **Posizione** 

Nella sezione **IMPOSTAZIONI**:

* Indicare se creare o meno una nuova rete virtuale

  * Se si crea una nuova rete virtuale, al controller del cluster verrà assegnato un indirizzo IP pubblico in modo che sia possibile raggiungerlo. Viene anche creato un gruppo di sicurezza di rete per la rete virtuale, che limita il traffico in ingresso solo alla porta 22.
  * Se si vuole usare una rete VPN o ExpressRoute per connettersi al controller del cluster, impostare questo valore su `false` e specificare una rete virtuale esistente nei campi rimanenti. Il controller del cluster userà tale rete virtuale per le comunicazioni di rete. 

* Nome e gruppo di risorse della rete virtuale e nome della subnet: digitare i nomi delle risorse esistenti (se si usa una rete virtuale esistente) oppure digitare i nuovi nomi se crea una nuova rete virtuale
* **Nome controller**: impostare un nome per la macchina virtuale controller
* Nome utente dell'amministratore del controller: il valore predefinito è `azureuser`
* Chiave SSH: incollare la chiave pubblica da associare al nome utente dell'amministratore. Per informazioni, vedere [Creare e usare chiavi SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

In **CONDIZIONI**: 

* Leggere le condizioni d'uso e selezionare la casella di controllo per accettarle. 

  > [!NOTE] 
  > Se non si è proprietari di una sottoscrizione, chiedere a un proprietario di accettare le condizioni seguendo i passaggi relativi ai prerequisiti in [Accettare preventivamente le condizioni software](avere-vfxt-prereqs.md#accept-software-terms-in-advance). 


Al termine, fare clic su **Acquista**. Dopo cinque o sei minuti, il nodo del controller sarà operativo.

Visitare la pagina degli output per raccogliere le informazioni sul controller necessarie per creare il cluster. Per altre informazioni, vedere [Informazioni necessarie per la creazione del cluster](#information-needed-to-create-the-cluster).

### <a name="create-controller---azure-marketplace-image"></a>Creare il controller - Immagine di Azure Marketplace

Trovare il modello del controller eseguendo una ricerca di ``Avere`` in Azure Marketplace. Selezionare il modello **Avere vFXT for Azure Controller**.

Se non è già stato fatto, accettare le condizioni e abilitare l'accesso a livello di codice all'immagine del Marketplace facendo clic sul collegamento "Per eseguire la distribuzione a livello di codice" sotto il pulsante **Crea**.

![Screenshot di un collegamento per l'accesso a livello di codice, sotto il pulsante Crea](media/avere-vfxt-deploy-programmatically.png)

Fare clic sul pulsante **Abilita** e salvare l'impostazione.

![Screenshot che mostra dove fare clic per abilitare l'accesso a livello di codice](media/avere-vfxt-enable-program.png)

Tornare alla pagina principale del modello **Avere vFXT for Azure Controller** e fare clic su **Crea**. 

Nel primo pannello specificare o confermare queste opzioni di base:

* **Sottoscrizione**
* **Gruppo di risorse** (immettere un nuovo nome se si vuole creare un nuovo gruppo)
* **Nome macchina virtuale**: nome del controller
* **Area**
* **Opzioni di disponibilità**: non è necessaria la ridondanza
* **Immagine**: immagine del nodo del controller Avere vFXT
* **Dimensione**: lasciare l'impostazione predefinita o scegliere un altro tipo economico
* **Account amministratore**: specificare come accedere al controller del cluster: 
  * Scegliere tra nome utente/password o chiave pubblica SSH (opzione consigliata).
  
    > [!TIP] 
    > Una chiave SSH è più sicura. Per informazioni, vedere [Creare e usare chiavi SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows). 
  * Specificare il nome utente 
  * Incollare la chiave SSH oppure immettere e confermare la password
* **Regole porta in ingresso**: se si usa un indirizzo IP pubblico, aprire la porta 22 (SSH)

Fare clic su **Avanti** per impostare le opzioni del disco:

* **Tipo di disco del sistema operativo**: il valore predefinito HDD è sufficiente
* **Usa dischi non gestiti**: non necessario
* **Dischi dati**: non usare

Fare clic su **Avanti** per le opzioni di rete:

* **Rete virtuale**: selezionare la rete virtuale per il controller oppure immettere un nome per creare una nuova rete virtuale. Se non si vuole usare un indirizzo IP pubblico nel controller, valutare se posizionarlo in una rete virtuale con ExpressRoute o un altro metodo di accesso già configurato.
* **Subnet**: selezionare una subnet all'interno della rete virtuale (facoltativo). Se si crea una nuova rete virtuale, è possibile creare contemporaneamente una nuova subnet.
* **IP pubblico**: se si vuole usare un indirizzo IP pubblico, è possibile specificarlo qui. 
* **Gruppo di sicurezza di rete**: lasciare l'impostazione predefinita (**Base**) 
* **Porte in ingresso pubbliche**: se si usa un indirizzo IP pubblico, usare questo controllo per consentire l'accesso dal traffico SSH. 
* **Rete accelerata**: questa opzione non è disponibile per questa macchina virtuale.

Fare clic su **Avanti** per impostare le opzioni di gestione:

* **Diagnostica di avvio**: modificare l'impostazione in **No**
* **Diagnostica sistema operativo guest**: lasciare disabilitata l'opzione
* **Account di archiviazione di diagnostica**: facoltativamente, selezionare o specificare un nuovo account per la conservazione delle informazioni di diagnostica.
* **Identità del servizio gestita**: modificare l'opzione impostandola su **Sì** per creare un'entità servizio Azure AD per il controller del cluster.
* **Arresto automatico**: lasciare disattivata l'opzione 

A questo punto, è possibile fare clic su **Rivedi e crea** se non si vogliono usare tag di istanza. In caso contrario, fare clic su **Avanti** due volte per ignorare la pagina **Configurazione guest** e passare alla pagina Tag. Al termine, fare clic su **Rivedi e crea**. 

Dopo aver verificato le selezioni, fare clic sul pulsante **Crea**.  

Per la creazione sono necessari cinque o sei minuti.

## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>Creare un endpoint di archiviazione (se si usa l'archiviazione BLOB di Azure)

Se si usa l'archiviazione BLOB di Azure per l'archivio dati back-end, è necessario creare un endpoint di servizio di archiviazione nella rete virtuale. Questo [endpoint di servizio](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) mantiene il traffico BLOB di Azure in locale invece di instradarlo tramite Internet.

1. Nel portale fare clic su **Reti virtuali** a sinistra.
1. Selezionare la rete virtuale per il controller. 
1. Fare clic su **Endpoint servizio** a sinistra.
1. Fare clic su **Aggiungi** nella parte superiore.
1. Lasciare il servizio ``Microsoft.Storage`` e scegliere la subnet del controller.
1. Nella parte inferiore fare clic su **Aggiungi**.

  ![Screenshot del portale di Azure con annotazioni per la procedura di creazione dell'endpoint di servizio](media/avere-vfxt-service-endpoint.png)

## <a name="information-needed-to-create-the-cluster"></a>Informazioni necessarie per la creazione del cluster

Al termine della creazione del controller del cluster, verificare di avere le informazioni necessarie per i passaggi successivi. 

Informazioni necessarie per la connessione al controller: 

* Nome utente del controller e chiave SSH (o password)
* Indirizzo IP del controller o altro metodo per la connessione alla macchina virtuale controller

Informazioni necessarie per il cluster: 

* Nome del gruppo di risorse
* Località di Azure 
* Nome della rete virtuale
* Nome della subnet
* Nome del ruolo del nodo del cluster: questo nome viene impostato durante la creazione del ruolo, come descritto [di seguito](#create-the-cluster-node-access-role)
* Nome dell'account di archiviazione se si crea un contenitore BLOB

Se il nodo del controller è stato creato usando il modello di Resource Manager, è possibile ottenere le informazioni dall'[output del modello](#find-template-output). 

Se è stata usata l'immagine di Azure Marketplace per creare il controller, è stata fornita direttamente la maggior parte di questi elementi. 

Trovare gli elementi mancanti passando alla pagina delle informazioni della macchina virtuale controller. Ad esempio, fare clic su **Tutte le risorse** e cercare il nome del controller, quindi fare clic sul nome del controller per visualizzare i dettagli.

### <a name="find-template-output"></a>Trovare l'output del modello

Per trovare le informazioni dall'output del modello di Resource Manager, seguire questa procedura:

1. Passare al gruppo di risorse del controller cluster.

1. A sinistra fare clic su **Distribuzioni** e quindi su **Microsoft.Template**.

   ![Pagina del portale del gruppo di risorse con l'opzione Distribuzioni selezionata a sinistra e Microsoft.Template in una tabella nella colonna Nome distribuzione](media/avere-vfxt-deployment-template.png)

1. A sinistra fare clic su **Output**. Copiare i valori in ognuno dei campi. 

   ![Pagina degli output con i valori relativi a stringa SSH, gruppo di risorse, località, rete, subnet e ID subnet a destra delle etichette](media/avere-vfxt-template-outputs.png)

## <a name="access-the-controller"></a>Accedere al controller

Per eseguire i rimanenti passaggi di distribuzione, è necessario connettersi al controller del cluster.

1. Il metodo di connessione al controller del cluster dipende dalla configurazione.

   * Se il controller ha un indirizzo IP pubblico, connettersi tramite SSH all'indirizzo IP del controller con il nome utente amministratore impostato (ad esempio, ``ssh azureuser@40.117.136.91``).
   * Se il controller non ha un indirizzo IP pubblico, usare una connessione VPN o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) alla rete virtuale.

1. Dopo avere effettuato l'accesso al controller, eseguire l'autenticazione tramite `az login`. Copiare il codice di autenticazione fornito nella shell e quindi usare un Web browser per caricare [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) ed eseguire l'autenticazione con il sistema Microsoft. Tornare alla shell per la conferma.

   ![Output della riga di comando del comando "AZ login" con il codice di autenticazione e il collegamento del browser](media/avere-vfxt-azlogin.png)

1. Aggiungere la sottoscrizione eseguendo questo comando con il proprio ID sottoscrizione: ```az account set --subscription YOUR_SUBSCRIPTION_ID```

## <a name="create-the-cluster-node-access-role"></a>Creare il ruolo di accesso dei nodi del cluster

> [!NOTE] 
> * Se non si è proprietari della sottoscrizione e il ruolo non è già stato creato, chiedere a un proprietario della sottoscrizione di seguire questi passaggi o usare la procedura descritta in [Creare il ruolo di accesso del runtime del cluster di Avere vFXT senza controller](avere-vfxt-pre-role.md).
> 
> * Gli utenti Microsoft interni devono usare il ruolo esistente denominato "Avere Cluster Runtime Operator" invece di tentare di crearne uno. 

Il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/) fornisce l'autorizzazione ai nodi del cluster vFXT per eseguire le attività necessarie.  

Come parte del normale funzionamento del cluster vFXT, i singoli nodi vFXT devono eseguire operazioni come la lettura delle proprietà delle risorse di Azure, la gestione dell'archiviazione e il controllo delle impostazioni dell'interfaccia di rete degli altri nodi. 

1. Nel controller aprire il file ``/avere-cluster.json`` in un editor.

   ![Console che mostra un comando di elenco e "vi /avere-cluster.json"](media/avere-vfxt-open-role.png)

1. Modificare il file per includere l'ID sottoscrizione ed eliminare la riga precedente. Salvare il file con il nome ``avere-cluster.json``.

   ![Editor di testo della console che mostra l'ID sottoscrizione e la riga da rimuovere selezionata per l'eliminazione](media/avere-vfxt-edit-role.png)

1. Usare questo comando per creare il ruolo:  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

Si passerà il nome del ruolo allo script di creazione del cluster nel passaggio successivo. 

## <a name="create-nodes-and-configure-the-cluster"></a>Creare i nodi e configurare il cluster

Per creare il cluster Avere vFXT, modificare uno degli script di esempio inclusi nel controller ed eseguirlo. Gli script di esempio si trovano nella directory radice (`/`) nel controller del cluster.

* Se si vuole creare un contenitore BLOB da usare come sistema di archiviazione back-end di Avere vFXT, usare lo script ``create-cloudbacked-cluster``.

* Se si aggiungerà l'archiviazione in un secondo momento, usare lo script ``create-minimal-cluster``.

> [!TIP]
> Gli script prototipo per l'aggiunta di nodi e l'eliminazione del cluster vFXT sono inclusi anche nella directory `/` della macchina virtuale controller del cluster.

### <a name="edit-the-deployment-script"></a>Modificare lo script di distribuzione

Aprire lo script di esempio in un editor. È possibile salvare lo script personalizzato con un nome diverso per evitare di sovrascrivere l'esempio originale.

Immettere i valori per queste variabili dello script.

* Nome del gruppo di risorse

  * Se si usano componenti di rete o di archiviazione che si trovano in gruppi di risorse diversi, rimuovere il commento delle variabili e fornire anche i nomi. 

```python
# Resource groups
# At a minimum specify the resource group.  If the network resources live in a
# different group, specify the network resource group.  Likewise for the storage
# account resource group.
RESOURCE_GROUP=
#NETWORK_RESOURCE_GROUP=
#STORAGE_RESOURCE_GROUP=
```

* Nome della località
* Nome della rete virtuale
* Nome della subnet
* Nome del ruolo di runtime Azure Active Directory: se è stato seguito l'esempio in [Creare il ruolo di accesso dei nodi del cluster](#create-the-cluster-node-access-role), usare ``avere-cluster``. 
* Nome dell'account di archiviazione (se si crea un nuovo contenitore BLOB)
* Nome del cluster: non è possibile avere due cluster vFXT con lo stesso nome nello stesso gruppo di risorse. 
* Password amministrativa: scegliere una password sicura per il monitoraggio e l'amministrazione del cluster. Questa password è assegnata all'utente ``admin``. 
* Tipo di istanza del nodo: vedere [Dimensioni dei nodi vFXT](avere-vfxt-deploy-plan.md#vfxt-node-sizes) per informazioni
* Dimensione cache del nodo: vedere [Dimensioni dei nodi vFXT](avere-vfxt-deploy-plan.md#vfxt-node-sizes) per informazioni

Salvare il file e uscire.

### <a name="run-the-script"></a>Esecuzione dello script

Eseguire lo script digitando il nome file creato. Ad esempio: `./create-cloudbacked-cluster-west1`  

> [!TIP]
> Eseguire questo comando all'interno di un [terminal multiplexer](http://linuxcommand.org/lc3_adv_termmux.php) come `screen` o `tmux` nel caso in cui si perda la connessione.  

L'output viene registrato anche in `~/vfxt.log`.

Al termine dell'esecuzione dello script, copiare l'indirizzo IP di gestione, necessario per l'amministrazione del cluster.

![Output della riga di comando dello script che visualizza l'indirizzo IP di gestione verso la fine](media/avere-vfxt-mgmt-ip.png)

## <a name="next-step"></a>Passaggio successivo

Ora che il cluster è in esecuzione e si conosce l'indirizzo IP di gestione, è possibile [connettersi allo strumento di configurazione del cluster](avere-vfxt-cluster-gui.md) per abilitare il supporto e aggiungere l'archiviazione, se necessario.
