---
title: Aggiungere l'archiviazione back-end al cluster Microsoft Azure FXT Edge Filer
description: Informazioni su come configurare l'archiviazione back-end e lo pseudo spazio dei nomi lato client per Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 4a69aa7838e08c83b47c5f0248e821edf86b3990
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543296"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Esercitazione: Aggiungere l'archiviazione back-end e configurare lo spazio dei nomi virtuale 

Questa esercitazione illustra come aggiungere l'archiviazione back-end per la cache e configurare il file system virtuale lato client. 

Il cluster si connette a sistemi di archiviazione back-end per accedere alla richiesta dei client di dati e per archiviare le modifiche in modo più definitivo rispetto alla cache. 

Lo spazio dei nomi è lo pseudo file system lato client che consente di sostituire l'archiviazione back-end senza modificare i flussi di lavoro lato client. 

In questa esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Aggiungere l'archiviazione back-end al cluster Azure FXT Edge Filer 
> * Definire il percorso per l'archiviazione lato client

## <a name="about-back-end-storage"></a>Informazioni sull'archiviazione back-end

Il cluster Azure FXT Edge Filer usa una definizione di *core filer* per collegare un sistema di archiviazione back-end al cluster FXT.

Azure FXT Edge Filer è compatibile con diversi tra i sistemi hardware NAS più diffusi e può usare contenitori vuoti dell'archiviazione BLOB di Azure o di altre risorse di archiviazione nel cloud. 

Quando vengono aggiunti, i contenitori di archiviazione nel cloud devono essere vuoti in modo che il sistema operativo FXT possa gestire completamente tutti i dati nel volume di archiviazione nel cloud. È possibile spostare i dati esistenti nel contenitore cloud dopo aver aggiunto il contenitore al cluster come core filer.

Usare il Pannello di controllo per aggiungere un core filer al sistema.

> [!NOTE]
> 
> Se si vuole usare l'archiviazione Amazon AWS o Google Cloud, è necessario installare una licenza per funzionalità FlashCloud<sup>TM</sup>. Contattare il rappresentante Microsoft per richiedere una chiave di licenza e quindi seguire le istruzioni nella guida alla configurazione legacy per l'[aggiunta o la rimozione delle licenze per funzionalità](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
> 
> Il supporto per l'archiviazione BLOB di Azure è incluso nella licenza software di Azure FXT Edge Filer. 

Per informazioni più dettagliate sull'aggiunta di core filer, leggere queste sezioni della guida alla configurazione del cluster:

* Per altre informazioni sulla selezione e le operazioni preliminari per l'aggiunta di un core filer, leggere l'articolo sull'[uso dei core filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Per tutti i prerequisiti e le istruzioni dettagliate, leggere questi articoli:

  * [Aggiunta di un nuovo core filer NAS](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Aggiunta di un nuovo core filer cloud](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Dopo aver aggiunto un core filer, è possibile aggiornarne le impostazioni nella pagina delle impostazioni dei dettagli del core filer.

## <a name="add-a-core-filer"></a>Aggiungere un core filer

Per definire un core filer, fare clic sul pulsante **Create** (Crea) nella pagina delle impostazioni **Core Filer** > **Manage Core Filers** (Core filer > Gestisci core filer).

![Selezione del pulsante Create (Crea) sopra l'elenco dei core filer nella pagina Manage Core Filers (Gestisci core filer)](media/fxt-cluster-config/create-core-filer-button.png)

La procedura guidata **Add New Core Filer** (Aggiungi nuovo core filer) consente di creare un core filer che si collega all'archiviazione back-end. La guida alla configurazione del cluster include le descrizioni dettagliate del processo, che è diverso a seconda che si tratti dell'archiviazione NFS/NAS o dell'archiviazione nel cloud (vedere i collegamenti riportati in precedenza). 

Le attività secondarie da eseguire includono:

* Specificare il tipo di core filer (NAS o cloud)

  ![Prima pagina della procedura guidata per l'aggiunta di un nuovo core filer per NAS hardware. L'opzione per il core filer cloud è disabilitata e viene visualizzato un messaggio di errore relativo alla licenza mancante.](media/fxt-cluster-config/new-nas-1.png)

* Impostare il nome del core filer. Scegliere un nome che aiuti gli amministratori di cluster a comprendere il sistema di archiviazione rappresentato.

* Per i core filer NAS specificare il nome di dominio completo (FQDN) o l'indirizzo IP. Il nome di dominio completo è consigliato per tutti i core filer e obbligatorio per l'accesso SMB.

* Selezionare un criterio di cache. Nella seconda pagina della procedura guidata sono elencati i criteri di cache disponibili per il nuovo core filer. Per informazioni dettagliate, leggere la [sezione relativa ai criteri della guida alla configurazione del cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Seconda pagina della procedura guidata per l'aggiunta di un nuovo core filer per NAS hardware. Il menu a discesa Cache Policy (Criteri cache) è aperto e mostra diverse opzioni disabilitate, oltre a tre opzioni valide di criteri cache, ovvero il criterio per ignorare, quello per la memorizzazione nella cache di lettura e quello per la memorizzazione nella cache di lettura/scrittura.](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Per l'archiviazione nel cloud è necessario specificare altri parametri, tra cui le credenziali di accesso e del servizio cloud. Per informazioni dettagliate, leggere la sezione su [protocollo e servizio cloud](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) nella guida alla configurazione del cluster.

  ![Informazioni sul core filer cloud nella procedura guidata per l'aggiunta di un nuovo core filer](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Se le credenziali dell'accesso cloud sono già state aggiunte per questo cluster, verranno visualizzate nell'elenco. Aggiornare e aggiungere le credenziali nella pagina delle impostazioni **Cluster** > **Cloud Credentials** (Cluster > Credenziali cloud). 

Dopo aver inserito tutte le impostazioni obbligatorie nella procedura guidata, fare clic sul pulsante **Add Filer** (Aggiungi filer) per inviare la modifica.

Dopo qualche istante il sistema di archiviazione viene visualizzato nell'elenco dei core filer nel **Dashboard** ed è accessibile tramite le pagine delle impostazioni dei core filer.

![Core filer "Flurry-NAS" nella pagina delle impostazioni Manage Core Filers (Gestisci core filer) con la visualizzazione dei dettagli filer espansa](media/fxt-cluster-config/core-filer-in-manage-page.png)

Nel core filer di questo screenshot manca un vserver. È necessario collegare il core filer a un vserver e creare una giunzione in modo che i client possano accedere all'archiviazione. Questa procedura è descritta nella sezione successiva [Configurare lo spazio dei nomi](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Configurare lo spazio dei nomi

Il cluster Azure FXT Edge Filer crea un file system virtuale denominato *spazio dei nomi cluster* che semplifica l'accesso client ai dati archiviati in diversi sistemi back-end. Dal momento che i client richiedono i file usando un percorso virtuale, è possibile aggiungere o sostituire i sistemi di archiviazione senza dover modificare il flusso di lavoro dei client. 

Lo spazio dei nomi cluster consente inoltre di presentare i sistemi di archiviazione cloud e NAS in una struttura di file simile. 

I vserver del cluster gestiscono lo spazio dei nomi e forniscono il contenuto ai client. La creazione dello spazio dei nomi cluster richiede due passaggi: 

1. Creare un vserver 
1. Configurare le giunzioni tra i sistemi di archiviazione back-end e i percorsi del file system lato client 

### <a name="create-a-vserver"></a>Creare un vserver

I vserver sono file server virtuali che consentono di controllare il flusso di dati tra il client e i core filer del cluster:

* I vserver ospitano gli indirizzi IP lato client
* I vserver consentono di creare lo spazio dei nomi e definire le giunzioni per il mapping tra la struttura di directory virtuali lato client e le esportazioni nell'archiviazione back-end
* I vserver applicano i controlli di accesso ai file, inclusi i criteri di esportazione dei core filer e i sistemi di autenticazione utente
* I vserver forniscono l'infrastruttura SMB

Prima di iniziare a configurare un vserver per il cluster, leggere la documentazione collegata e rivolgersi al rappresentante Microsoft per maggiori informazioni su spazio dei nomi e vserver. Se si usano VLAN, [crearle](fxt-configure-network.md#adjust-network-settings) prima di creare il vserver. 

Queste sezioni della guida alla configurazione del cluster consentono di acquisire familiarità con le funzionalità del vserver FXT e dello spazio dei nomi globale:

* [Creazione e uso di vserver](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Uso di uno spazio dei nomi globale](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Creazione di un vserver](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

È necessario almeno un vserver per il cluster. 

Per creare un nuovo vserver, sono necessarie le informazioni seguenti:

* Nome da impostare per il vserver

* Intervallo di indirizzi IP lato client che verranno gestiti dal server

  Quando si crea il vserver, è necessario specificare un singolo intervallo di indirizzi IP contigui. Per aggiungere altri indirizzi in un secondo momento, usare la pagina delle impostazioni **Client Facing Network** (Rete lato client).

* Se la rete include VLAN, è necessario indicare la VLAN da usare per questo vserver

Per creare un nuovo vserver, usare la pagina delle impostazioni **VServer** > **Manage VServers** (Vserver > Gestisci vserver). Per informazioni dettagliate, leggere la sezione sulla [creazione di un vserver](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) nella guida alla configurazione del cluster. 

![Finestra popup per la creazione di un nuovo vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Creare una giunzione

Una *giunzione* consente di eseguire il mapping di un percorso di archiviazione back-end allo spazio dei nomi visibile ai client.

È possibile usare questo sistema per semplificare il percorso usato nei punti di montaggio client, nonché per ridimensionare facilmente la capacità dal momento che un unico percorso virtuale può supportare l'archiviazione di più core filer.

![Pagina della procedura guidata Add New Junction (Aggiungi nuova giunzione) con le impostazioni inserite](media/fxt-cluster-config/add-junction-full.png)

Per tutti i dettagli sulla creazione di una giunzione dello spazio dei nomi, vedere la sezione su [**vserver** > **spazio dei nomi**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) nella guida alla configurazione del cluster.

![Pagina delle impostazioni VServer > Namespace (Vserver > Spazio dei nomi) in cui sono visualizzati i dettagli relativi a una giunzione](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Configurare regole di esportazione

Dopo aver definito un vserver e un core filer, è necessario personalizzare le regole e i criteri di esportazione che controllano le modalità in cui i client possono accedere ai file nelle esportazioni dei core filer.

Usare innanzitutto la pagina **VServer** > **Export Rules** (Vserver > Regole di esportazione) per aggiungere nuove regole, modificare i criteri predefiniti o creare criteri di esportazione personalizzati.

Usare quindi la pagina **VServer** > **Export Policies** (Vserver > Criteri di esportazione) per applicare i criteri personalizzati alle esportazioni del core filer quando l'accesso viene effettuato tramite tale vserver.

Per informazioni dettagliate, leggere l'articolo relativo a come [controllare l'accesso alle esportazioni del core filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) nella guida alla configurazione del cluster.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver aggiunto l'archiviazione e aver configurato lo spazio dei nomi lato client, completare la configurazione iniziale del cluster: 

> [!div class="nextstepaction"]
> [Configurare le impostazioni di rete del cluster](fxt-configure-network.md)
