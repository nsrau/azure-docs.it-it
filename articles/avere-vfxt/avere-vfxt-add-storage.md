---
title: Configurare l'archiviazione di Avere vFXT - Azure
description: Come aggiungere un sistema di archiviazione back-end ad Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: procedural
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d38fe1cab27cfade3e6e4d2f6764f455896ac470
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001962"
---
# <a name="configure-storage"></a>Configurare l'archivio

Questo passaggio configura il sistema di archiviazione back-end per il cluster vFXT.

> [!TIP]
> Se è stato usato lo script prototipo `create-cloudbacked-cluster` per creare un nuovo contenitore BLOB insieme al cluster Avere vFXT, quel contenitore è già configurato per l'uso e non occorre aggiungere risorse di archiviazione.
>
> Tuttavia, se il nuovo contenitore Blob è stato crittografato con una chiave di crittografia predefinita, è necessario scaricare il file di ripristino della chiave dal cluster o sostituire la chiave predefinita con una nuova chiave prima di archiviare i dati. La chiave predefinita viene salvata solo nel cluster e non può essere recuperata se il cluster viene perso o non è più disponibile.
>
> Dopo la connessione al pannello di controllo di Avere, fare clic sulla scheda **Impostazioni**, quindi scegliere **Archiviatore principale** > **Impostazioni di crittografia cloud**. Nella sezione **Archivio chiavi locale**, scegliere una delle opzioni seguenti: 
> * Usare il pulsante **Scarica di nuovo file di ripristino** per ottenere il file di ripristino della chiave esistente. Il file di ripristino è crittografato con la password amministrativa del cluster. Assicurarsi di salvare il file in un percorso affidabile. 
> * Seguire le istruzioni della sezione **Generare una nuova chiave principale** per creare una nuova chiave di crittografia controllata. Questa opzione consente di specificare una passphrase univoca e richiede il caricamento e il nuovo download del file di ripristino per convalidare la coppia file-passphrase.

Seguire queste istruzioni se è stato usato lo script di prototipo `create-minimal-cluster` per il cluster o se si vuole aggiungere un altro sistema di archiviazione, hardware o basato sul cloud.

Le attività principali da eseguire sono due:

1. [Creare un core filer](#create-a-core-filer), che connette il cluster vFXT a un sistema di archiviazione esistente o un account di archiviazione di Azure.

1. [Creare una giunzione di spazi dei nomi](#create-a-junction), che definisce il percorso che sarà montato dai client.

Questa procedura usa il pannello di controllo di Avere. Leggere [Accedere al cluster vFXT](avere-vfxt-cluster-gui.md) per imparare a usarlo.

## <a name="create-a-core-filer"></a>Creare un core filer

"Core filer" è un termine di vFXT che indica un sistema di archiviazione back-end. Il sistema di archiviazione può essere un'appliance NAS hardware, ad esempio NetApp o Isilon, oppure può essere un archivio di oggetti cloud. Altre informazioni sui core filer sono disponibili nella [guida alle impostazioni del cluster Avere](http://library.averesystems.com/ops_guide/4_7/settings_overview.html#managing-core-filers).

Per aggiungere un core filer, sceglierne uno dei due tipi principali:

  * [Core filer NAS ](#nas-core-filer): descrive come aggiungere un core filer NAS 
  * [Core filer cloud account di archiviazione di Azure](#azure-storage-account-cloud-core-filer): descrive come aggiungere un account di archiviazione di Azure come core filer cloud

### <a name="nas-core-filer"></a>Core filer NAS

Un core filer NAS può essere un'appliance NetApp o Isilon locale oppure un endpoint NAS nel cloud.  
Il sistema di archiviazione deve avere una connessione ad alta velocità affidabile al cluster Avere vFXT, ad esempio una connessione ExpressRoute (non VPN) da 1 Gbps, e deve fornire al cluster l'accesso radice alle esportazioni NAS usate.

Seguire questa procedura per aggiungere un core filer NAS:

1. Nel pannello di controllo di Avere fare clic sulla scheda **Settings** (Impostazioni) nella parte superiore.

1. Fare clic su **Core Filer** > **Manage Core Filers** (Gestisci core filer) a sinistra.

1. Fare clic su **Create**(Crea).

   ![Screenshot della pagina per l'aggiunta di un nuovo core filer con il puntatore sul pulsante Create (Crea)](media/avere-vfxt-add-core-filer-start.png)

1. Immettere le informazioni richieste nella procedura guidata: 

   * Assegnare un nome al core filer.
   * Fornire un nome di dominio completo (FQDN), se disponibile. In alternativa, specificare un indirizzo IP o un nome host che viene risolto nel core filer.
   * Scegliere la classe del filer dall'elenco. In caso di dubbi, scegliere **Other** (Altro).

     ![Screenshot della pagina di aggiunta core filer con il nome e il nome di dominio completo corrispondenti](media/avere-vfxt-add-core-filer.png)
  
   * Fare clic su **Next** (Avanti) e scegliere i criteri della cache. 
   * Fare clic su **Add Filer** (Aggiungi filer).
   * Per informazioni più dettagliate, vedere [Adding a new NAS core filer](http://library.averesystems.com/ops_guide/4_7/new_core_filer_nas.html) (Aggiunta di un nuovo core filer NAS) nella guida alle impostazioni del cluster Avere.

A questo punto, passare a [Creare una giunzione](#create-a-junction).  

### <a name="azure-storage-cloud-core-filer"></a>Core filer cloud di Archiviazione di Azure

Per usare l'archivio BLOB di Azure come risorsa di archiviazione back-end del cluster vFXT, è necessario un contenitore vuoto da aggiungere come core filer.

> [!TIP] 
> Lo script di esempio ``create-cloudbacked-cluster`` crea un contenitore di archiviazione, lo definisce come core filer e crea la giunzione di spazi dei nomi nell'ambito della creazione del cluster vFXT. Lo script di esempio ``create-minimal-cluster`` non crea un contenitore di archiviazione di Azure. Per evitare di dover creare e configurare un core filer di Archiviazione di Azure dopo la creazione del cluster, usare lo script ``create-cloudbacked-cluster`` per distribuire il cluster vFXT.

L'aggiunta del servizio di archiviazione BLOB al cluster richiede queste attività:

* Creare un account di archiviazione (passaggio 1 sotto)
* Creare un contenitore BLOB vuoto (passaggi 2 e 3)
* Aggiungere la chiave di accesso alle risorse di archiviazione come credenziale cloud per il cluster vFXT (passaggi da 4 a 6)
* Aggiungere il contenitore BLOB come core filer per il cluster vFXT (passaggi da 7 a 9)
* Creare una giunzione di spazi dei nomi che i client useranno per montare il core filer ([Creare una giunzione](#create-a-junction), stessa procedura per archiviazione hardware e cloud)

Per aggiungere il servizio di archiviazione BLOB dopo la creazione del cluster, seguire questa procedura. 

1. Creare un account di archiviazione per utilizzo generico v2 con queste impostazioni:

   * **Sottoscrizione**: la stessa del cluster vFXT
   * **Gruppo di risorse**: lo stesso del gruppo di cluster vFXT (facoltativo)
   * **Posizione**: la stessa del cluster vFXT
   * **Prestazioni**: Standard (l'archiviazione Premium non è supportata)
   * **Tipo di account**: Utilizzo generico v2 (Archiviazione v2)
   * **Replica**: Archiviazione con ridondanza locale
   * **Livello di accesso**: Frequente
   * **Trasferimento sicuro obbligatorio**: disabilitare questa opzione (valore non predefinito)
   * **Reti virtuali**: non obbligatorio

   È possibile usare il portale di Azure o fare clic sul pulsante "Distribuisci in Azure" in basso.

   [![pulsante per creare l'account di archiviazione](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Dopo la creazione dell'account, passare alla pagina dell'account di archiviazione.

   ![Nuovo account di archiviazione nel portale di Azure](media/avere-vfxt-new-storage-acct.png)

1. Creare un contenitore BLOB facendo clic su **BLOB** nella pagina di panoramica e quindi su **+Contenitore**. Usare un qualsiasi nome di contenitore e verificare che l'accesso sia impostato su **Privato**.

   ![Pagina dei BLOB di archiviazione senza contenitori esistenti](media/avere-vfxt-blob-no-container.png)

1. Ottenere la chiave dell'account di archiviazione di Azure facendo clic su **Chiavi di accesso** sotto **Impostazioni**:

   ![GUI del portale di Azure per copiare la chiave](media/avere-vfxt-copy-storage-key.png) 

1. Aprire il pannello di controllo di Avere per il cluster. Fare clic su **Impostazioni**, quindi aprire **Cluster** > **Cloud Credentials** (Credenziali cloud) nel riquadro di spostamento a sinistra. Nella pagina Cloud Credentials (Credenziali cloud) fare clic su **Add Credential** (Aggiungi credenziali).

   ![Fare clic sul pulsante Add Credential (Aggiungi credenziali) nella pagina di configurazione delle credenziali cloud](media/avere-vfxt-new-credential-button.png)

1. Specificare le informazioni seguenti per creare le credenziali per il core filer cloud: 

   | Campo | Valore |
   | --- | --- |
   | Credential name (Nome credenziali) | qualsiasi nome descrittivo |
   | Tipo di servizio | selezionare Azure Storage Access Key (Chiave di accesso di Archiviazione di Azure) |
   | Tenant | nome dell'account di archiviazione |
   | Sottoscrizione | ID sottoscrizione |
   | Storage Access Key (Chiave di accesso alle risorse di archiviazione) | chiave di accesso di Archiviazione di Azure (copiata nel passaggio precedente) | 

   Fare clic su **Submit**.

   ![Modulo delle credenziali cloud completato nel pannello di controllo di Avere](media/avere-vfxt-new-credential-submit.png)

1. A questo punto, creare il core filer. Sul lato sinistro del pannello di controllo di Avere, fare clic su **Core Filer** >  **Manage Core Filers** (Gestisci core filer). 

1. Fare clic sul pulsante **Create** (Crea) nella pagine delle impostazioni **Manage Core Filers** (Gestisci core filer).

1. Compilare i valori nella procedura guidata come indicato di seguito:

   * Selezionare il tipo di filer **Cloud**. 
   * Assegnare un nome al nuovo core filer e fare clic su **Next** (Avanti).
   * Accettare i criteri cache predefiniti e passare alla terza pagina.
   * In **Service type** (Tipo di servizio) scegliere **Azure storage** (Archiviazione di Azure). 
   * Selezionare le credenziali create in precedenza.
   * Impostare **Bucket contents** (Contenuto contenitore) su **Empty** (Vuoto)
   * Impostare **Certificate verification** (Verifica certificato) su **Disabled** (Disabilitata)
   * Impostare **Compression mode** (Modalità compressione) su **None** (Nessuna)  
   * Fare clic su **Avanti**.
   * Nella quarta pagina immettere il nome del contenitore in **Bucket name** (Nome contenitore) nel formato *nome_account_archiviazione*/*nome_contenitore*.
   * Facoltativamente, impostare **Encryption type** (Tipo di crittografia) su **None** (Nessuna).  Archiviazione di Azure viene crittografato per impostazione predefinita.
   * Fare clic su **Add Filer** (Aggiungi filer).

  Per informazioni più dettagliate, vedere [Adding a new cloud core filer](<http://library.averesystems.com/ops_guide/4_7/new_core_filer_cloud.html>) (Aggiunta di un nuovo core filer cloud) nella guida alla configurazione del cluster Avere. 

La pagina verrà aggiornata oppure è possibile aggiornare la pagina per visualizzare il nuovo core filer.

A questo punto è necessario [Creare una giunzione](#create-a-junction).

## <a name="create-a-junction"></a>Creare una giunzione

Una giunzione è un percorso che si crea per i client. I client montano il percorso e arrivano alla destinazione scelta.

Ad esempio, è possibile creare `/avere/files` per eseguire il mapping all'esportazione `/vol0/data` del core filer NetApp e alla sottodirectory `/project/resources`.

Altre informazioni sulle giunzioni sono disponibili nella [sezione relativa agli spazi dei nomi della guida alla configurazione del cluster Avere](http://library.averesystems.com/ops_guide/4_7/gui_namespace.html).

Seguire questi passaggi nell'interfaccia delle impostazioni del pannello di controllo di Avere:

* Fare clic su **VServer** > **Namespace** (Spazio dei nomi) in alto a sinistra.
* Fornire un percorso dello spazio dei nomi che inizi con una barra (/), ad esempio ``/avere/data``.
* Scegliere il core filer.
* Scegliere l'esportazione del core filer.
* Fare clic su **Avanti**.

  ![Screenshot della pagina di aggiunta nuova giunzione con i campi compilati per giunzione, core filer ed esportazione](media/avere-vfxt-add-junction.png)

La giunzione verrà visualizzata dopo pochi secondi. Creare altre giunzioni in base alle esigenze.

Una volta creata la giunzione, i client possono [Montare il cluster Avere vFXT](avere-vfxt-mount-clients.md) per accedere al file system.