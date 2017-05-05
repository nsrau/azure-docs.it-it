---
title: "Proprietà del ruolo di Azure"
description: Informazioni su come usare Azure Toolkit per Eclipse per configurare le impostazioni di ruolo di Azure.
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 5c0ec412-5702-465a-8f47-87a8ce99a267
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: cd734c64ba6d1394cb261bace92dee9dd579dd08
ms.lasthandoff: 04/22/2017


---
# <a name="azure-role-properties"></a>Proprietà del ruolo di Azure
In Azure Toolkit for Eclipse è possibile specificare diverse impostazioni di configurazione per il ruolo di Azure.

## <a name="configuring-azure-role-properties"></a>Configurazione delle proprietà del ruolo di Azure
La configurazione delle proprietà del ruolo di Azure viene eseguita tramite le finestre di dialogo delle proprietà per il ruolo di lavoro. Aprire il menu di scelta rapida per il ruolo nel riquadro Project Explorer di Eclipse e selezionare il sottomenu **Azure** . Se il ruolo non viene visualizzato in Eclipse Project Explorer, espandere il progetto di Azure in Project Explorer.

![][ic789599]

Questo argomento descrive le diverse proprietà che è possibile impostare nelle finestre di dialogo **Properties** (Proprietà). Si noti che molte proprietà vengono compilate automaticamente quando si crea un nuovo progetto di distribuzione di Azure.

Per i ruoli di Azure sono disponibili le pagine delle proprietà seguenti.

* [Proprietà della macchina virtuale](#virtual_machine_properties)
* [Proprietà di memorizzazione nella cache](#caching_properties)
* [Proprietà dei certificati](#certificates_properties)
* [Proprietà dei componenti](#components_properties)
<!-- * [Debugging properties](#debugging_properties) -->
* [Proprietà di endpoint](#endpoints_properties)
* [Proprietà di variabili di ambiente](#environment_variables_properties)
* [Proprietà di bilanciamento del carico/affinità di sessione (dette anche "sessioni permanenti")](#session_affinity_properties)
* [Proprietà di archiviazione locale](#local_storage_properties)
* [Proprietà di configurazione del server](#server_configuration_properties)
* [Proprietà di offload SSL](#ssl_offloading_properties)

<a name="virtual_machine_properties"></a>

### <a name="virtual-machine-properties"></a>Proprietà della macchina virtuale
Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer (Esplora progetti) di Eclipse, scegliere **Azure** e quindi fare clic su **Properties** (Proprietà). In questa finestra di dialogo è possibile modificare le dimensioni della macchina virtuale e il numero di istanze, come illustrato nell'immagine seguente.

![][ic719499]

> [!NOTE]
> Solo Windows: quando si imposta il numero di istanze su un valore maggiore di 1 e si configura un server applicazioni, il toolkit consentirà l'esecuzione di una sola istanza del ruolo nell'emulatore, indipendentemente da questa impostazione. In questo modo si evitano conflitti di binding delle porte tra le diverse istanze del server (ad esempio, tutte le istanze che provano a eseguire il binding alla porta 8080) quando vengono eseguite nello stesso computer. L'impostazione del numero di istanze specificata viene mantenuta, ma diventa effettiva solo in caso di distribuzione nel cloud.
> 
> 

<a name="caching_properties"></a> 

### <a name="caching-properties"></a>Proprietà di memorizzazione nella cache
Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer (Esplora progetti) di Eclipse, scegliere **Azure** e quindi fare clic su **Caching** (Memorizzazione nella cache). In questa finestra di dialogo è possibile abilitare cache denominate con risorse condivise e compatibili con Memcache, che consentono di velocizzare le applicazioni Web.

![][ic719483]

Nella pagina delle proprietà **Caching** è possibile specificare impostazioni globali per le opzioni seguenti:

* Se è abilitata la memorizzazione nella cache con risorse condivise.
* Dimensioni della cache espresse come percentuale della memoria.
* Nome dell'account di archiviazione per salvare lo stato della cache quando l'applicazione viene eseguita come servizio cloud o nessuno se non si vuole salvare lo stato della cache. Il nome dell'account di archiviazione non viene usato quando si esegue l'applicazione nell'emulatore di calcolo. Se si imposta il nome dell'account di archiviazione su **(auto)**, che corrisponde al valore predefinito, la configurazione della memorizzazione nella cache userà automaticamente lo stesso account di archiviazione selezionato nella finestra di dialogo **Publish to Azure** (Pubblica in Azure).

> [!NOTE]
> L'impostazione **(auto)** avrà l'effetto desiderato solo se si pubblica la distribuzione usando la procedura di pubblicazione guidata del toolkit di Eclipse. Se invece si pubblica manualmente il file cspkg tramite un meccanismo esterno, ad esempio il [portale di gestione di Azure][Azure Management Portal], la distribuzione non funzionerà correttamente.
> 
> 

La finestra di dialogo seguente mostra le proprietà di una cache.

![][ic719501]

* **Name:** nome della cache con risorse condivise.
* **Port number:** numero di porta da usare per la cache.
* **Expiration policy:** uno dei valori seguenti che specificano la scadenza di una chiave nella cache.
  * **Absolute** (Assoluta): la chiave scade al raggiungimento del periodo di tempo specificato in **Minutes to live** (Minuti di durata).
  * **NeverExpires:** alla chiave non è associata un'ora di scadenza.
  * **SlidingWindow** (Finestra temporale scorrevole): la chiave scade se non è stata usata per il periodo di tempo specificato in **Minutes to live** (Minuti di durata). Ogni volta che si accede alla chiave, la scadenza viene reimpostata.
* **Minutes to live:** numero massimo di minuti di durata della chiave Memcache, in base ai criteri di scadenza.
* **La disponibilità elevata con backup replicati in istanze del ruolo diverse:** se abilitata, questa opzione fornisce disponibilità elevata usando backup replicati in diverse istanze del ruolo. Si noti che per l'uso di questa funzionalità, devono essere attive almeno due istanze del ruolo per la distribuzione.

Per aggiungere una nuova cache, fare clic sul pulsante **Add** (Aggiungi) nella pagina delle proprietà **Caching** (Memorizzazione nella cache). Verrà visualizzata la finestra di dialogo **Configure Named Cache** (Configura cache denominata). Specificare i valori per le proprietà descritte sopra.

Per modificare una cache denominata, selezionarla e fare clic sul pulsante **Edit** (Modifica) nella pagina delle proprietà **Caching** (Memorizzazione nella cache). Verrà aperta una finestra di dialogo in cui è possibile modificare le proprietà della cache. Fare clic su **OK** per salvare i valori della cache.

Per eliminare una cache, selezionarla e fare clic sul pulsante **Remove** (Rimuovi) nella pagina delle proprietà **Caching** (Memorizzazione nella cache) e quindi su **Yes** (Sì) per confermare l'eliminazione.

Per altre informazioni sull'uso della funzionalità di memorizzazione nella cache, vedere [Come usare la memorizzazione nella cache con risorse condivise][How to Use Co-located Caching].

<a name="certificates_properties"></a> 

### <a name="certificates-properties"></a>Proprietà dei certificati
Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer (Esplora progetti) di Eclipse, scegliere **Azure** e quindi fare clic su **Certificates** (Certificati).

![][ic710964]

In questa finestra di dialogo è possibile aggiungere o rimuovere i certificati a cui fa riferimento il progetto Eclipse. Si noti che i certificati elencati in questa finestra di dialogo non vengono archiviati automaticamente in un archivio delle chiavi Java e quindi non sono disponibili automaticamente per l'uso in un'applicazione Java. Vengono solo registrati con Azure per poter essere precaricati nell'archivio certificati di Windows delle macchine virtuali che eseguono la distribuzione e usati successivamente con altro software Windows. Attualmente, la sola funzionalità del toolkit che usa i certificati a cui viene fatto riferimento in questo modo nella finestra di dialogo **Certificates** (Certificati) è l'[offload SSL][SSL Offloading], per il fatto che dipende da Internet Information Services (IIS) e Application Request Routing (ARR), i quali richiedono che il certificato appropriato sia reso disponibile in questo modo.

Quando si distribuisce il progetto in Azure tramite la pubblicazione guidata, verrà richiesto di puntare ai file PFX (Personal Information Exchange) corrispondenti a questi certificati, insieme alle relative password, per poterli caricare automaticamente nel servizio di Azure, ma solo se non sono già stati caricati in precedenza.

<a name="components_properties"></a> 

### <a name="components-properties"></a>Proprietà dei componenti
Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer (Esplora progetti) di Eclipse, scegliere **Azure** e quindi fare clic su **Components** (Componenti). In questa finestra di dialogo è possibile aggiungere, modificare o rimuovere i componenti del ruolo e modificare l'ordine in cui vengono elaborati.

![][ic719502]

La funzionalità Components consente di aggiungere dipendenze al progetto di distribuzione di Azure, ad esempio progetti di applicazioni Java, file speciali e istruzioni della riga di comando eseguibili necessari per la distribuzione.

Per ogni componente, è possibile specificare:

* Il passaggio da eseguire quando si importa il componente nel progetto di distribuzione di Azure al momento della compilazione.
* Il passaggio da eseguire quando si distribuisce il componente nel cloud di Azure.

> [!NOTE]
> Quando si specificano i file dei componenti o le righe di comando, tenere presente che la distribuzione verrà pubblicata in una macchina virtuale di Windows, quindi i passaggi personalizzati devono essere validi per un sistema operativo Windows. 
> 
> 

Di seguito sono illustrate le proprietà di Components:

* **Import:** metodo che indica la modalità di importazione del componente nel progetto al momento della compilazione. Può essere uno dei valori seguenti:
  * **copy** (copia): il componente viene copiato dal percorso locale specificato nella proprietà **From** (Da) alla directory **approot** del ruolo.
  * **EAR**: il componente è un file EAR (Enterprise ARchive) Java importato da un progetto di applicazione aziendale nel percorso locale specificato nella proprietà **From** (Da). Viene rilevato automaticamente dal toolkit in base alla natura del progetto in quella posizione.
  * **JAR**: il componente è un file JAR (Java ARchive) e viene importato da un progetto Java nel percorso locale specificato nella proprietà **From** (Da). Viene rilevato automaticamente dal toolkit in base alla natura del progetto in quella posizione.
  * **none:** non viene eseguita alcuna azione per importare il componente. Questo valore è applicabile quando si presuppone che il componente sia già presente nella directory **approot** del ruolo o quando il componente è semplicemente un'istruzione della riga di comando eseguibile, secondo quanto specificato nella proprietà **As** (Come) quando il metodo **Deploy** (Distribuzione) è **exec** (esecuzione).
  * **WAR**: il componente è un file WAR (Web Application ARchive) Java e viene importato da un progetto Web dinamico nel percorso locale specificato nella proprietà **From** (Da). Viene rilevato automaticamente dal toolkit in base alla natura del progetto in quella posizione.
  * **zip**: il componente è un file ZIP e viene importato comprimendo la directory o il file specificato nella proprietà **From** (Da).
* **From:** percorso di origine nel computer locale alla cartella o al file che rappresenta l'elemento o gli elementi da importare nella distribuzione. In questa proprietà è possibile usare variabili di ambiente di Windows. Tutti i componenti importabili saranno importati nella directory **approot** del ruolo al momento della compilazione del progetto.
  
    Si noti che è possibile distribuire un componente da un download quando si esegue la distribuzione nel cloud (non nell'emulatore di calcolo). Vedere le informazioni correlate seguenti sull'aggiunta di un componente.    
* **As** (Come): nome file con cui il componente verrà importato nella directory **approot** del ruolo e quindi distribuito nel cloud di Azure. Per mantenere lo stesso nome presente nel computer locale, lasciare vuota questa proprietà. Per i componenti eseguibili, ovvero i componenti con il metodo **Deploy** (Distribuzione) impostato su **exec** (esecuzione), può essere un'istruzione arbitraria della riga di comando di Windows.
  
  > [!IMPORTANT]
  > Se per questo valore si usano spazi, saranno gestiti diversamente a seconda del metodo di distribuzione. Se il metodo di distribuzione è **exec**, gli spazi saranno interpretati come separatori di argomenti della riga di comando e non come parte del nome file. Per tutti gli altri metodi di distribuzione, gli spazi saranno interpretati come parte del nome file.
  > 
  > 
* **Deploy:** metodo che indica l'azione applicata al componente quando viene avviata la distribuzione. Può essere uno dei valori seguenti:
  
  * **copy** (copia): il componente viene copiato nel percorso di destinazione specificato nella proprietà **To** (A).
  * **exec** (esecuzione): il componente è un'istruzione eseguibile della riga di comando di Windows eseguita nel contesto del percorso specificato nella proprietà **To** (A), all'avvio della distribuzione.
  * **none:** non viene applicata alcuna azione al componente all'avvio della distribuzione.
  * **zip**: il componente viene decompresso nel percorso di destinazione specificato nella proprietà **To** (A). Questo metodo è disponibile solo quando la proprietà **Import** (Importazione) è **zip**.
* **To:** percorso di destinazione nella macchina virtuale in cui verrà distribuito il componente. In questa proprietà è possibile usare le variabili di ambiente Windows e i percorsi di file sono relativi alla directory **approot**.

Per aggiungere un nuovo componente, fare clic sul pulsante **Add** (Aggiungi) nella pagina delle proprietà **Components** (Componenti). Verrà visualizzata la finestra di dialogo **Azure Role Component** (Componente ruolo Azure). Specificare i valori per le proprietà descritte sopra. 

L'esempio seguente mostra come aggiungere un nuovo componente WAR.

![][ic719503]

Quando si esegue la distribuzione nel cloud (non nell'emulatore di calcolo), se si vuole distribuire il componente da un download verificare che l'opzione **When in cloud, instead of including in the package, deploy from** (Nel cloud, anziché includere nel pacchetto distribuisci da) sia selezionata. Se si vuole effettuare il download dall'account di archiviazione di Azure, selezionarlo nell'elenco a discesa **Storage account** (Account di archiviazione). È possibile modificare il contenuto dell'elenco facendo clic sul collegamento **Accounts** (Account). Verrà così compilato parzialmente il campo **URL**. Compilare quindi la parte rimanente dell'URL. Se non si vuole usare l'archiviazione di Azure, selezionare **(none)** (nessuno) nell'elenco a discesa **Storage account** (Account di archiviazione) e quindi immettere l'URL del componente nel campo **URL**. Specificare uno dei metodi seguenti:

* **copy** (copia): il componente scaricato viene copiato nel percorso di destinazione specificato in **To Directory** (Nella directory).
* **same** (stesso metodo): stesso metodo usato per **Deploy from download** (Distribuisci da download) e per **Deploy from package** (Distribuisci da pacchetto).
* **zip**: il componente scaricato viene decompresso nel percorso di destinazione specificato in **To Directory** (Nella directory).

Per modificare un componente, selezionarlo e fare clic sul pulsante **Edit** (Modifica) nella pagina delle proprietà **Components** (Componenti). Verrà aperta una finestra di dialogo in cui è possibile modificare le proprietà del componente. Fare clic su **OK** per salvare i valori del componente.

Per eliminare un componente, selezionarlo e fare clic sul pulsante **Remove** (Rimuovi) nella pagina delle proprietà **Components** (Componenti) e quindi su **Yes** (Sì) per confermare l'eliminazione.

I componenti vengono elaborati nell'ordine elencato. Usare i pulsanti **Move Up** (Sposta su) e **Move Down** (Sposta giù) per modificare l'ordine.

> [!NOTE]
> La funzionalità di configurazione del server si basa anche sui componenti. Questi componenti non possono essere rimossi o modificati senza rimuovere la configurazione del server corrispondente. Quando si prova ad apportare modifiche a tali componenti, verrà visualizzato un avviso a questo proposito.
> 
> 

<!-- <a name="debugging_properties"></a> -->

<!-- ### Debugging properties -->
<!-- Open the context menu for the role in Eclipse's Project Explorer pane, click **Azure**, and then click **Debugging**. Within this dialog, you have the ability to enable or disable remote debugging, as well as create debug configurations, as shown in the following image. -->

<!-- ![][ic719504] -->

<!-- For related information about debugging, see [Debugging Azure Applications in Eclipse][Debugging Azure Applications in Eclipse]. -->

<a name="endpoints_properties"></a> 

### <a name="endpoints-properties"></a>Proprietà di endpoint
Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer (Esplora progetti) di Eclipse, scegliere **Azure** e quindi fare clic su **Endpoints** (Endpoint). In questa finestra di dialogo è possibile creare un endpoint e modificare o rimuovere un endpoint, come illustrato nell'immagine seguente.

![][ic719505]

Per aggiungere un endpoint, fare clic sul pulsante **Add** (Aggiungi) nella pagina delle proprietà **Endpoints** (Endpoint). Verrà aperta la finestra di dialogo **Add Endpoint** (Aggiungi endpoint).

![][ic710897]

Immettere un nome per l'endpoint, selezionare il tipo, **Input**, **Internal** (Interno) o **InstanceInput** (Input istanza), e specificare la porta privata e quella pubblica. Fare clic su **OK** per salvare i nuovi valori dell'endpoint.

A seconda del tipo di endpoint, è possibile usare intervalli di porte come indicato di seguito:

* Per un endpoint dell'istanza di input, la porta pubblica può essere un intervallo di porte, ad esempio **2000-2010**, e la porta privata è un valore fisso.
* Per un endpoint interno, la porta pubblica non viene usata e la porta privata può essere un intervallo o essere lasciata vuota o impostata su un asterisco per indicare che viene impostata automaticamente da Azure.
* Per gli endpoint di input, la porta pubblica può essere solo un valore fisso e la porta privata può essere un valore fisso o essere lasciata vuota o impostata su un asterisco per indicare che viene impostata automaticamente da Azure.

Se si vuole usare un singolo numero di porta anziché un intervallo, lasciare vuota la casella di testo relativa alla fine dell'intervallo.

Per le porte impostate su automatico, se è necessario determinare quale porta viene effettivamente usata in fase di esecuzione, l'applicazione può usare l'API di runtime del servizio Azure, documentata nel [riepilogo del pacchetto com.microsoft.windowsazure.serviceruntime][com.microsoft.windowsazure.serviceruntime package summary].

<!-- To see how instance input endpoints can be used to help with debugging a multi-instance deployment, see [Debugging a specific role instance in a multi-instance deployment][Debugging a specific role instance in a multi-instance deployment]. -->

Per modificare un endpoint, selezionarlo e fare clic sul pulsante **Edit** (Modifica) nella pagina delle proprietà **Endpoints** (Endpoint). Verrà aperta una finestra di dialogo in cui è possibile modificare il nome dell'endpoint, il tipo e le porte pubblica e privata. Fare clic su **OK** per salvare i valori dell'endpoint modificati.

Per eliminare un endpoint, selezionarlo e fare clic sul pulsante **Remove** (Rimuovi) nella pagina delle proprietà **Endpoints** (Endpoint) e quindi su **Yes** (Sì) per confermare l'eliminazione.

Per configurare correttamente alcune funzionalità, ad esempio memorizzazione nella cache, affinità di sessione o ripartizione del carico SSL, abilitate dall'utente in un ruolo, il toolkit è in grado di configurare automaticamente endpoint speciali che verranno elencati insieme a quelli definiti dall'utente. Il toolkit impedisce all'utente di modificare o eliminare gli endpoint generati automaticamente finché è abilitata la funzionalità associata.

<a name="environment_variables_properties"></a> 

### <a name="environment-variables-properties"></a>Proprietà di variabili di ambiente
Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer (Esplora progetti) di Eclipse, scegliere **Azure** e quindi fare clic su **Environment Variables** (Variabili di ambiente). In questa finestra di dialogo è possibile creare una variabile di ambiente e modificare o rimuovere una variabile di ambiente, come illustrato nell'immagine seguente.

![][ic719506]

Le variabili di ambiente sono disponibili per lo script di avvio all'avvio del ruolo.

> [!NOTE]
> Quando si specificano variabili di ambiente, tenere presente che la distribuzione verrà pubblicata in una macchina virtuale di Windows, quindi tali variabili di ambiente devono essere valide per un sistema operativo Windows.
> 
> 

Per un esempio di variabile di ambiente disponibile all'avvio del ruolo, creare una nuova variabile di ambiente facendo clic sul pulsante **Add** . Di seguito è illustrata la creazione di una variabile di ambiente denominata **MyRoleVersion** a cui viene assegnato il valore **1.0**.

![][ic659268]

Nel codice jsp code è possibile visualizzare il valore usando il metodo `System.getenv` :

    <body>
      <b> Hello World!</b>
      <p>Running role version: <%= System.getenv("MyRoleVersion") %></p>
    </body>

Questo è l'output risultante quando viene eseguita l'applicazione:

![][ic552233]

Per modificare una variabile di ambiente, selezionarla e fare clic sul pulsante **Edit** (Modifica) nella pagina delle proprietà **Environment Variables** (Variabili di ambiente). Verrà aperta una finestra di dialogo in cui è possibile modificare le proprietà della variabile di ambiente. Fare clic su **OK** per salvare i valori delle variabili di ambiente.

Per eliminare una variabile di ambiente, selezionarla e fare clic sul pulsante **Remove** (Rimuovi) nella pagina delle proprietà **Environment Variables** (Variabili di ambiente) e quindi su **Yes** (Sì) per confermare l'eliminazione.

Per configurare correttamente alcune funzionalità (ad esempio, Server Configuration, Remote Debugging o Local Storage) abilitate dall'utente in un ruolo, il toolkit è in grado di configurare automaticamente variabili di ambiente speciali che verranno elencate insieme a quelle definite dall'utente. Il toolkit impedisce all'utente di modificare o eliminare le variabili di ambiente generate automaticamente finché è abilitata la funzionalità associata.

<a name="session_affinity_properties"></a> 

### <a name="load-balancing--session-affinity-aka-sticky-sessions-properties"></a>Proprietà di bilanciamento del carico/affinità di sessione (dette anche "sessioni permanenti")
Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer (Esplora progetti) di Eclipse, scegliere **Azure** e quindi fare clic su **Load Balancing** (Bilanciamento del carico). In questa finestra di dialogo è possibile abilitare o disabilitare l'affinità di sessione, come illustrato nell'immagine seguente.

![][ic719492]

Per informazioni correlate, vedere [Affinità di sessione][Session Affinity]. Si noti anche il comportamento di questa funzionalità nel contesto dell'offload SSL, come descritto in [Offload SSL][SSL Offloading].

<a name="local_storage_properties"></a> 

### <a name="local-storage-properties"></a>Proprietà di archiviazione locale
Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer (Esplora progetti) di Eclipse, scegliere **Azure** e quindi fare clic su **Local Storage** (Archiviazione locale). In questa finestra di dialogo è possibile creare, modificare o rimuovere lo spazio di archiviazione locale temporaneo per la macchina virtuale che esegue l'applicazione. È possibile impostare valori specifici per le dimensioni dello spazio di archiviazione locale e specificare se il contenuto viene mantenuto quando il ruolo viene riciclato, come illustrato nella figura seguente.

![][ic719508]

È anche possibile specificare facoltativamente una variabile di ambiente corrispondente all'archiviazione locale.

Per impostazione predefinita, tutto ciò che si distribuisce in Azure viene decompresso e inserito nella cartella **approot** dell'istanza del ruolo. Anche se le dimensioni della cartella sono sufficienti per la maggior parte delle distribuzioni semplici anche dopo la decompressione, lo spazio allocato per la directory **approot** è limitato e non ben definito (meno di 1 GB è una regola generale ragionevole). Per assicurare quindi che in Azure venga allocato spazio su disco sufficiente per distribuzioni estese che potrebbero non rientrare nella cartella **approot**, è consigliabile configurare una risorsa di archiviazione locale usando la finestra di dialogo **Local Storage** (Archiviazione locale). Per informazioni su un modo semplice per eseguire questa operazione, vedere [Distribuzione di distribuzioni di grandi dimensioni][Deploying Large Deployments].

È possibile fare riferimento facilmente alla risorsa di archiviazione degli script di avvio, ad esempio **startup.cmd**, usando la variabile di ambiente associata automaticamente alla risorsa dal toolkit per Eclipse, come illustrato nella finestra di dialogo **Local Storage** (Archiviazione locale). Tale variabile di ambiente conterrà il percorso completo della risorsa locale configurata al momento dell'esecuzione dello script di avvio. 

Per modificare una risorsa di archiviazione locale, selezionarla e fare clic sul pulsante **Edit** (Modifica) nella pagina delle proprietà **Local Storage** (Archiviazione locale). Verrà aperta una finestra di dialogo in cui è possibile modificare le proprietà della risorsa di archiviazione locale. Fare clic su **OK** per salvare i valori della risorsa di archiviazione locale.

Per eliminare una risorsa di archiviazione locale, selezionarla e fare clic sul pulsante **Remove** (Rimuovi) nella pagina delle proprietà **Local Storage** (Archiviazione locale) e quindi su **Yes** (Sì) per confermare l'eliminazione.

<a name="server_configuration_properties"></a> 

### <a name="server-configuration-properties"></a>Proprietà di configurazione del server
Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer (Esplora progetti) di Eclipse, scegliere **Azure** e quindi fare clic su **Server Configuration** (Configurazione server). In questa finestra di dialogo è possibile aggiungere, rimuovere e modificare JDK e il server applicazioni Java usato nella distribuzione e aggiungere o rimuovere le applicazioni, ad esempio file WAR, JAR o EAR, usate nella distribuzione.

### <a name="jdk-configuration"></a>Configurazione di JDK
Questa finestra di dialogo consente di specificare il pacchetto JDK da usare per la distribuzione. Se si usa Eclipse in Windows, è possibile specificare il pacchetto JDK da usare in locale durante l'esecuzione nell'emulatore di Azure e scegliere di distribuire tale installazione locale in Azure. Nei sistemi operativi non Windows non è applicabile l'impostazione JDK dell'emulatore e non è possibile distribuire il pacchetto JDK installato localmente perché non è compatibile con Windows. Tuttavia, indipendentemente dal sistema operativo in uso, è possibile scegliere tra i pacchetti JDK di terze parti per la distribuzione in Azure o selezionare il proprio pacchetto JDK compatibile con Windows da un percorso di download alternativo.

Di seguito è riportato un esempio di come è possibile specificare un pacchetto JDK in Windows:

![][ic780647]

Se si usa Eclipse in Windows, è possibile specificare un pacchetto JDK da usare con l'emulatore di calcolo. A questo scopo, verificare che l'opzione **Use the JDK from this file path for testing locally** (Usa JDK da questo percorso di file per il test in locale) sia selezionata nella sezione **Emulator deployment** (Distribuzione emulatore). Specificare quindi il percorso locale del pacchetto JDK. È possibile cercare un JDK diverso se quello che si vuole usare non viene selezionato automaticamente. È anche possibile distribuire il proprio JDK nel servizio cloud di Azure. A questo scopo, selezionare l'opzione **Deploy my local JDK (auto-upload to cloud storage)** (Distribuisci JDK locale - caricamento automatico in archiviazione cloud) nella sezione **Cloud deployment** (Distribuzione cloud).

Nota: nei sistemi operativi non Windows, le impostazioni **Emulator deployment** (Distribuzione emulatore) e l'opzione **Deploy my local JDK** (Distribuisci JDK locale) non sono disponibili. L'esempio seguente illustra come specificare un pacchetto JDK in un Mac o in un altro sistema operativo non Windows supportato:

![][ic789643]

Indipendentemente dal sistema operativo in uso, sono disponibili le due opzioni **Cloud deployment** seguenti per l'origine e il tipo di pacchetto JDK:

* **Deploy a 3rd party JDK package available on Azure** 
* **Deploy from a custom download** 

Se si usa l'opzione **Deploy a 3rd party JDK package available from Azure** :

1. Selezionare la casella di controllo denominata **Deploy a 3rd party JDK package available from Azure**.
2. Nell'elenco a discesa selezionare il pacchetto JDK di terze parti disponibile in Azure.
3. In Windows, la scheda **JDK** avrà un aspetto simile al seguente:  ![][ic780648]
    In Mac OS o negli altri sistemi operativi non Windows supportati, sarà simile alla seguente:  ![][ic789643]
4. Fare clic su **OK** per salvare le modifiche.
5. Quando viene richiesto di accettare il contratto di licenza del provider del pacchetto JDK di terze parti, leggere le condizioni di licenza. Presupponendo che si accettino le condizioni, fare clic su **Yes** (Sì) per chiudere la finestra di dialogo **Accept license agreement** (Accetta contratto di licenza).
    Si noti che è possibile modificare la logica sottostante relativa agli elementi da visualizzare nell'elenco a discesa dell'opzione **Deploy a 3rd party JDK package available from Azure** . Per personalizzare gli elementi, nella finestra di dialogo **JDK** fare clic sul collegamento **Customize** (Personalizza). Verrà chiusa la pagina delle proprietà **JDK** e in Eclipse verrà aperto il file **componentsets.xml**, che potrà quindi essere modificato in base alle esigenze. La documentazione per **componentsets.xml** è inclusa nel file **componentsets.xml**.

Se si usa l'opzione **Deploy a JDK from a custom download** :

1. Creare un file ZIP della directory di installazione di JDK, assicurandosi che il nodo stesso della directory sia l'elemento figlio della struttura dello ZIP e non il suo contenuto. Prendere nota del nome della directory, che sarà necessario in seguito, e tenere presente che questa installazione di JDK verrà distribuita in una macchina virtuale di Windows.
2. Caricare il file ZIP nell'account di archiviazione di Azure come BLOB. È possibile eseguire questa operazione con uno strumento disponibile esternamente per caricare BLOB nell'archiviazione di Azure. È consigliabile usare un BLOB privato. Prendere nota dell'URL del BLOB del contenuto del file ZIP.
3. Selezionare la casella di controllo denominata **Deploy a JDK from a custom download**.
    Se si vuole effettuare il download dall'account di archiviazione di Azure, selezionarlo nell'elenco a discesa **Storage account** (Account di archiviazione). È possibile modificare il contenuto dell'elenco facendo clic sul collegamento **Accounts** (Account). Verrà così compilato parzialmente il campo **URL**. Compilare quindi la parte rimanente dell'URL. Se non si vuole usare l'archiviazione di Azure, selezionare **(none)** (nessuno) nell'elenco a discesa **Storage account** (Account di archiviazione) e quindi immettere l'URL del download di JDK nel campo **URL**. Se si usa l'archiviazione di Azure, è necessario che i nomi di BLOB nell'URL siano specificati in lettere minuscole.
4. Verificare che la casella di testo **JAVA_HOME** faccia riferimento al nome della directory corretta. Per impostazione predefinita, farà riferimento al nome della directory JDK con lo stesso valore scelto per l'uso locale. Se tuttavia la directory contenuta nel file ZIP ha un nome diverso, ad esempio a causa dell'uso di una versione diversa, aggiornare di conseguenza il nome della directory nella casella di testo **JAVA_HOME**, perché questa impostazione verrà usata nel cloud (non nell'emulatore di calcolo).
5. Fare clic su **OK** per salvare le modifiche.

È tutto. A questo punto, quando si compila per il cloud, si noterà che le dimensioni del pacchetto saranno più ridotte, il processo di compilazione richiederà in genere meno tempo e anche la pubblicazione nel cloud della distribuzione stessa richiederà meno tempo. Si noti che l'opzione **Deploy my local JDK (auto-upload to cloud storage)** (Distribuisci JDK locale - caricamento automatico in archiviazione cloud) o **Deploy a JDK from a custom download** (Distribuisci JDK da download personalizzato) è effettiva solo quando l'applicazione viene distribuita nel cloud. Queste opzioni non hanno alcun effetto sull'esperienza dell'emulatore di calcolo. La versione locale dei componenti verrà comunque usata durante la distribuzione nell'emulatore di calcolo. 

### <a name="server-configuration"></a>Configurazione del server
Di seguito è riportato un esempio di come è possibile specificare un server applicazioni.

![][ic796926]

Verificare che la casella di controllo **Deploy a server of this type** sia selezionate e quindi scegliere il tipo di server applicazioni che si vuole usare.

Per specificare un server da usare per la distribuzione nel cloud, è possibile usare le seguenti opzioni:

1. **Deploy a 3rd party server available on Azure** : questa opzione è applicabile in particolare in scenari di sviluppo e/o test in cui l'efficienza e la semplicità di distribuzione costituiscono le priorità e il server non richiede una configurazione personalizzata. L'opzione è applicabile anche se si vuole usare uno di questi server come punto di partenza, includendo passaggi appropriati di personalizzazione del server nella logica di avvio della distribuzione.
2. **Deploy from a custom download** : questa opzione è applicabile in particolare negli scenari di produzione, quando si ha un server appositamente preparato e configurato che si vuole usare nel cloud.
3. **Deploy my local server installation** : questa opzione è applicabile in particolare se l'installazione del server locale è già configurata in modo personalizzato per l'uso. Se si sceglie questa opzione, è necessario specificare anche il percorso del server locale nella casella di testo **Local server path** seguente.

Se si usa l'opzione **Deploy a 3rd party server available on Azure** :

1. Selezionare la casella di controllo denominata **Deploy a 3rd party server available on Azure**.
2. Nel menu a discesa selezionare il software server desiderato da usare con la distribuzione nel cloud. Se in precedenza è già stato specificato un tipo di server da usare, si potrà scegliere solo un server cloud appartenente alla stessa famiglia di quel tipo di server. Ma se non si è scelto un tipo di server, è possibile scegliere un server qualsiasi tra quelli attualmente disponibili in Azure. Il tipo di server verrà selezionato automaticamente.
3. Fare clic su **OK** per salvare le modifiche.

Se si usa l'opzione **Deploy from a custom download** :

1. Assicurarsi di avere già selezionato un tipo di server seguendo i passaggi precedenti. In questo modo verrà indicata al plug-in la modalità di distribuzione del server dal download personalizzato, che deve appartenere alla stessa famiglia del tipo di server selezionato.
2. Selezionare la casella di controllo denominata **Deploy from a custom download**.
    Se si vuole effettuare il download dall'account di archiviazione di Azure, selezionarlo nell'elenco a discesa **Storage account** (Account di archiviazione). È possibile modificare il contenuto dell'elenco facendo clic sul collegamento **Accounts** (Account). Verrà così compilato parzialmente il campo **URL**. Compilare quindi la parte rimanente dell'URL del file ZIP di download del server. Quando si usa l'archiviazione di Azure, i nomi di BLOB nell'URL devono essere specificati in lettere minuscole. Se non si vuole usare l'archiviazione di Azure, selezionare **(none)** (nessuno) nell'elenco a discesa **Storage account** (Account di archiviazione) e quindi immettere l'URL del file ZIP di download del server nel campo **URL**. Il file ZIP contiene una cartella figlio che rappresenta la directory di installazione del server applicazioni. Se si usa un file ZIP per Apache Tomcat 7.0.35, ad esempio, il file conterrà una cartella figlio che rappresenta la directory di installazione, ad esempio **apache-tomcat-7.0.35**. 
3. Specificare il valore per la variabile di ambiente della home directory. Per impostazione predefinita, verrà usato il valore del server applicazioni locale, se disponibile, ma è possibile specificare un valore diverso se il server applicazioni cloud è diverso da quello locale. Tuttavia, è necessario assicurarsi che il server applicazioni cloud sia della stessa famiglia del tipo di server selezionato in precedenza.
    Se in futuro si aggiorna il file ZIP del server applicazioni cloud, è possibile modificare manualmente l'impostazione della home directory oppure fare in modo che corrisponda di nuovo all'impostazione locale, se è stato modificato anche il server applicazioni locale.
4. Fare clic su **OK** per salvare le modifiche.

La logica sottostante relativa agli elementi visualizzati nella scheda **Server** della pagina delle proprietà **Server Configuration** (Configurazione server) può essere personalizzata. Si tratta di una funzionalità avanzata che potrebbe essere utile se le proprie esigenze si estendono oltre i valori predefiniti o se si vuole aggiungere altri server. Per personalizzare gli elementi, nella finestra di dialogo **Server** fare clic sul collegamento **Customize** (Personalizza). Verrà chiusa la pagina delle proprietà **Server Configuration** (Configurazione server) e in Eclipse verrà aperto il file **componentsets.xml**, che potrà quindi essere modificato in base alle esigenze per estendere il modello di configurazione del server. La documentazione per **componentsets.xml** è inclusa nel file **componentsets.xml**.

Se si usa l'opzione il **Deploy my local server (auto-upload to cloud storage)** :

1. Selezionare la casella di controllo denominata **Deploy my local server (auto-upload to cloud storage)**.
2. Nell'elenco a discesa **Storage account** (Account di archiviazione) selezionare **(auto)**. Se si specifica **(auto)**, il toolkit per Eclipse userà per il server lo stesso account di archiviazione selezionato per la distribuzione nella finestra di dialogo **Publish to Azure** (Pubblica in Azure).
3. Fare clic su **OK** per salvare le modifiche.

Selezionare un percorso di installazione del server in questo computer nella casella di testo **Local server path** , se viene soddisfatta una delle seguenti condizioni:

* Si vuole testare la distribuzione nell'emulatore (solo per Windows).
* Si vuole distribuire il server installato localmente nel cloud.
* Si vuole usare un download di server personalizzato nel cloud. In questo caso verificare anche che sopra sia selezionata l'opzione **Deploy my local server (auto-upload to cloud storage)** (Distribuisci server locale - caricamento automatico in archiviazione cloud).

Se nessuna delle opzioni precedenti è applicabile, l'impostazione del server locale è facoltativa.

### <a name="applications-configuration"></a>Configurazione di applicazioni
Di seguito è riportato un esempio di come è possibile specificare un'applicazione.

![][ic719512]

Fare clic su **Add** (Aggiungi) per aggiungere un'altra applicazione o su **Remove** (Rimuovi) per rimuovere un'applicazione. Ai fini dell'efficienza, se si vuole usare un download per l'origine di un'applicazione in caso di distribuzione nel cloud, usare le [proprietà dei componenti](#components_properties) per specificare un URL, l'account di archiviazione e così via. 

A partire dalla versione di aprile 2014, le applicazioni vengono caricate automaticamente nello stesso account di archiviazione (nel contenitore **eclipsedeploy**) selezionato per la distribuzione. La logica di avvio della distribuzione include un passaggio che prevede prima di tutto il download di tali applicazioni dall'account di archiviazione. Questo significa che si possono aggiornare le applicazioni nella distribuzione senza dover ricompilare e ridistribuire l'intero pacchetto. A questo scopo caricare manualmente le versioni più recenti dell'applicazione direttamente in tale account di archiviazione, ad esempio tramite il portale di Azure, e sostituire i file WAR originariamente caricati dal toolkit. Iniziare quindi semplicemente a riciclare tutte le istanze del ruolo usando di nuovo il portale di gestione di Azure oppure tramite le utilità della riga di comando. L'attivazione del riciclo del ruolo direttamente dal toolkit per Eclipse non è attualmente supportata.

### <a name="notes-about-server-configuration"></a>Note sulla configurazione del server
Le modifiche apportate nella pagina delle proprietà **Server configuration** (Configurazione server) vengono riflesse negli elementi `<component>` del file package.xml.

Quando si usa l'opzione **Automatically upload** (Carica automaticamente) o **Deploy from download** (Distribuisci da download) per JDK o per il server applicazioni durante la compilazione per il cloud (non l'emulatore di calcolo) e si è connessi alla rete, nell'output della console potrebbero essere visualizzati messaggi di compilazione come il seguente, perché il compilatore Ant verifica la disponibilità del download:

`[windowsazurepackage] Verifying blob availability (https://example.blob.core.windows.net/temp/tomcat6.zip)...` 

Se è stata selezionata l'opzione **Deploy from download...** , è possibile che venga visualizzato il seguente avviso, ma la compilazione continuerà:

`[windowsazurepackage] warning: Failed to confirm blob availability! Make sure the URL and/or the access key is correct (https://example.blob.core.windows.net/temp/tomcat6.zip).` 

Questo avviso è la sola indicazione che la disponibilità del download non è stata verificata. Di conseguenza, se la distribuzione nel cloud non riesce per un motivo qualsiasi, verificare se è stato visualizzato questo avviso.

Per disabilitare la verifica del download, ad esempio se si ritiene che rallenti inutilmente la compilazione, impostare l'attributo `verifydownloads` su `false` nell'elemento `<windowsazurepackage>` del file package.xml: 

`<windowsazurepackage verifydownloads="false" ...>` 

Se è stata selezionata l'opzione **Automatically upload...** , nella finestra della console verranno visualizzati messaggi di compilazione che indicano lo stato di avanzamento del caricamento ogni 5 secondi, ogni volta che è necessaria un'operazione di caricamento.

<a name="ssl_offloading_properties"></a> 

### <a name="ssl-offloading-properties"></a>Proprietà di offload SSL
Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer (Esplora progetti) di Eclipse, scegliere **Azure** e quindi fare clic su **SSL Offloading** (Offload SSL). 

![][ic719481]

In questa finestra di dialogo è possibile abilitare l'offload SSL, che consente di abilitare facilmente il supporto del protocollo HTTPS ( Hypertext Transfer Protocol Secure) nella distribuzione Java in Azure, senza dover configurare SSL nel server applicazioni Java. Per altre informazioni, vedere l'articolo relativo all'[offload SSL][SSL Offloading] e l'articolo su [come usare l'offload SSL][How to Use SSL Offloading].

## <a name="see-also"></a>Vedere anche
[Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]

[Installazione di Azure Toolkit for Eclipse][Installing the Azure Toolkit for Eclipse]

[Creare un'applicazione Hello World per Azure in Eclipse][Creating a Hello World Application for Azure in Eclipse]

[Proprietà del progetto Azure][Azure Project Properties]

[Elenco di account di archiviazione di Azure][Azure Storage Account List]

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure][Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Project Properties]: http://go.microsoft.com/fwlink/?LinkID=699524
[Azure Storage Account List]: http://go.microsoft.com/fwlink/?LinkID=699528
[com.microsoft.windowsazure.serviceruntime package summary]: http://azure.github.io/azure-sdk-for-java/com/microsoft/windowsazure/serviceruntime/package-summary.html
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Debugging a specific role instance in a multi-instance deployment]: http://go.microsoft.com/fwlink/?LinkID=699535#debugging_specific_role_instance
[Debugging Azure Applications in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Deploying Large Deployments]: http://go.microsoft.com/fwlink/?LinkID=699536
[How to Use Co-located Caching]: http://go.microsoft.com/fwlink/?LinkID=699542
[How to Use SSL Offloading]: http://go.microsoft.com/fwlink/?LinkID=699545
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Session Affinity]: http://go.microsoft.com/fwlink/?LinkID=699548
[SSL Offloading]: http://go.microsoft.com/fwlink/?LinkID=699549

<!-- IMG List -->

[ic789599]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789599.png
[ic719499]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719499.png
[ic719483]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719483.png
[ic719501]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719501.png
[ic710964]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710964.png
[ic719502]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719502.png
[ic719503]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719503.png
[ic719504]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719504.png
[ic719505]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719505.png
[ic710897]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710897.png
[ic719506]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719506.png
[ic659268]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic659268.png
[ic552233]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic552233.png
[ic719492]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719492.png
[ic719508]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719508.png
[ic780647]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780647.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic780648]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780648.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic796926]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic796926.png
[ic719512]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719512.png
[ic719481]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719481.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690945.aspx -->

