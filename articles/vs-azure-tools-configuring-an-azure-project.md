<properties
   pageTitle="Configurazione di un progetto di servizio Cloud di Azure tramite più configurazioni del servizio | Microsoft Azure"
   description="Informazioni su come configurare un progetto di servizio cloud di Azure modificando i file ServiceDefinition.csdef e ServiceConfiguration.cscfg"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/08/2015"
   ms.author="kempb" />

# Configurazione di un progetto di servizio Cloud di Azure tramite più configurazioni del servizio

Un progetto di servizio cloud di Azure include due file di configurazione: ServiceDefinition.csdef e ServiceConfiguration.cscfg. Questi file sono inclusi nel pacchetto dell'applicazione di servizio cloud di Azure e vengono distribuiti in Azure.

- Il file **ServiceDefinition.csdef** contiene i metadati necessari all'ambiente Azure per i requisiti dell'applicazione del servizio cloud, inclusi i ruoli contenuti. Questo file include anche le impostazioni di configurazione applicabili a tutte le istanze, che possono essere lette in fase di esecuzione mediante l'API di runtime dell'hosting di servizi di Azure. Questo file non può essere aggiornato mentre il servizio è in esecuzione in Azure.

- Il file **ServiceConfiguration.cscfg** stabilisce i valori per le impostazioni di configurazione definite nel file di definizione del servizio e specifica il numero di istanze da eseguire per ogni ruolo. Questo file può essere aggiornato mentre il servizio cloud è in esecuzione in Azure.

Gli strumenti di Azure per Visual Studio forniscono le pagine delle proprietà da usare per stabilire le impostazioni di configurazione archiviate in questi file. Per accedere alle pagine delle proprietà, nel menu di scelta rapida di riferimento al ruolo in **Esplora soluzioni**, scegliere **Proprietà**, come illustrato nella figura riportata di seguito. In alternativa, fare doppio clic sul riferimento al ruolo.

![VS\_Solution\_Explorer\_Roles\_Properties](./media/vs-azure-tools-configuring-an-azure-project/IC784076.png)

Per informazioni sugli schemi sottostanti per i file di definizione e di configurazione del servizio, vedere [Guida di riferimento agli schemi](https://msdn.microsoft.com/library/azure/dd179398.aspx). Per ulteriori informazioni sulla configurazione del servizio, vedere [Come gestire i profili e le configurazioni del servizio](vs-azure-tools-service-configurations-and-profiles-how-to-manage.md).

## Configurazione delle proprietà del ruolo

Sebbene le pagine delle proprietà per un ruolo di lavoro e un ruolo Web siano simili, esistono alcune differenze descritte nelle sezioni seguenti. Nella pagina **Caching** è possibile configurare i servizi di caching di Azure.

### Pagina Configurazione

Nella pagina **Configurazione** è possibile impostare le seguenti proprietà:

**Istanze**

Impostare la proprietà **Conteggio istanze** sul numero di istanze che il servizio deve eseguire per questo ruolo.

Impostare la proprietà **Dimensioni macchina virtuale** su **Molto piccola**, **Piccola**, **Media**, **Grande** o **Molto grande**. Per altre informazioni, vedere [Configurare le dimensioni dei servizi cloud](https://msdn.microsoft.com/library/azure/ee814754.aspx).

**Azione di avvio** (solo ruolo Web)

Impostare questa proprietà per specificare che in Visual Studio deve essere avviato un Web browser per gli endpoint HTTP o HTTPS o entrambi quando si inizia il debug.

L'opzione **Endpoint HTTPS** è disponibile solo se è già stato definito un endpoint HTTPS per il ruolo. È possibile definire un endpoint HTTPS nella pagina delle proprietà **Endpoint**.

Se è già stato aggiunto un endpoint HTTPS, l'opzione **Endpoint HTTPS** è abilitata per impostazione predefinita e Visual Studio avvierà un browser per questo endpoint quando si inizia il debug, in aggiunta a un browser per l'endpoint HTTP. Ciò presuppone che siano abilitate entrambe le opzioni di avvio.

**Diagnostica**

Per impostazione predefinita, la diagnostica è abilitata per il ruolo Web. Il progetto di servizio cloud di Azure e l'account di archiviazione sono impostati per usare l'emulatore di archiviazione locale. Quando si è pronti a effettuare la distribuzione in Azure, è possibile fare clic sul pulsante del generatore (**…**) per aggiornare l'account di archiviazione in modo da usare l'archiviazione di Azure nel cloud. È possibile trasferire i dati di diagnostica nell'account di archiviazione a richiesta o a intervalli pianificati automaticamente. Per altre informazioni su Diagnostica di Azure, vedere [Raccogliere dati di registrazione usando Diagnostica di Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### Pagina Impostazioni

Nella pagina **Impostazioni** è possibile aggiungere le impostazioni di configurazione per il servizio. Tali impostazioni sono coppie nome/valore. Il codice che è in esecuzione nel ruolo può leggere i valori delle impostazioni di configurazione in fase di esecuzione usando le classi fornite dalla [Libreria gestita di Azure](http://go.microsoft.com/fwlink?LinkID=171026). In particolare, il metodo [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) restituisce il valore di un'impostazione di configurazione denominata in fase di esecuzione.

**Configurazione di una stringa di connessione in un account di archiviazione**

Una stringa di connessione è un'impostazione di configurazione che fornisce informazioni di connessione e autenticazione per l'emulatore di archiviazione o per un account di archiviazione di Azure. Quando il codice deve accedere ai dati dei servizi di archiviazione di Azure, ovvero i dati BLOB, della coda o della tabella, dal codice in esecuzione in un ruolo, sarà necessario definire una stringa di connessione per l'account di archiviazione.

Una stringa di connessione che punta a un account di archiviazione di Azure deve essere in un formato definito. Per informazioni sulla creazione di stringhe di connessione, vedere [Configurazione delle stringhe di connessione di Azure](https://msdn.microsoft.com/library/azure/ee758697.aspx).

Quando si è pronti a testare il servizio rispetto ai servizi di archiviazione di Azure o a distribuire il servizio cloud in Azure, è possibile modificare il valore di qualsiasi stringa di connessione in modo che punti all'account di archiviazione di Azure. Fare clic su (…), quindi selezionare Immettere le credenziali dell'account di archiviazione. Immettere le informazioni sull'account, inclusi il nome e la chiave dell'account. Nella finestra di dialogo Stringa di connessione a account di archiviazione è possibile indicare anche se si vogliono usare gli endpoint HTTPS predefiniti (opzione predefinita), gli endpoint HTTP predefiniti o gli endpoint personalizzati. Se si è registrato un nome di dominio personalizzato per il servizio, è possibile scegliere di usare endpoint personalizzati, come descritto in [Configurare un nome di dominio personalizzato per i dati BLOB in un account di archiviazione di Azure](storage-custom-domain-name.md).

>[AZURE.IMPORTANT]È necessario modificare le stringhe di connessione affinché puntino a un account di archiviazione di Azure prima di distribuire il servizio. Se non si esegue questa operazione, il ruolo potrebbe non essere avviato oppure potrebbe non passare per gli stati di inizializzazione, occupato e arresto.

### Pagina Endpoint

Un ruolo di lavoro può disporre di qualsiasi numero di endpoint HTTP, HTTPS o TCP. Gli endpoint possono essere endpoint di input, disponibili a client esterni, o endpoint interni, disponibili ad altri ruoli in esecuzione nel servizio.

- Per rendere un endpoint HTTP disponibile a client esterni e Web browser, cambiare il tipo di endpoint in input e specificare un nome e un numero di porta pubblico.

- Per rendere un endpoint HTTPS disponibile a client esterni e Web browser, cambiare il tipo di endpoint in input e specificare un nome, un numero di porta pubblico e un nome di certificato di gestione.

  Si noti che prima di poter specificare un certificato di gestione, è necessario definire il certificato nella pagina delle proprietà **Certificati**.

- Per rendere un endpoint disponibile per l'accesso interno da parte di altri ruoli nel servizio cloud, cambiare il tipo di endpoint in **interno** e specificare un nome e le possibili porte private per questo endpoint.

### Pagina Archiviazione locale

È possibile usare la pagina delle proprietà Archiviazione locale per riservare una o più risorse di archiviazione locali per un ruolo. Una risorsa di archiviazione locale è una directory riservata nel file system della macchina virtuale di Azure in cui è eseguita un'istanza di un ruolo. Per altre informazioni sull'utilizzo delle risorse di archiviazione locali, vedere [Configurare le risorse di archiviazione locale](../cloud-services/cloud-services-configure-local-storage-resources.md).

### Pagina Certificati

Nella pagina **Certificati** è possibile associare certificati al ruolo. I certificati che si aggiungono possono essere usati per configurare gli endpoint HTTPS nella pagina delle proprietà **Endpoint**.

La pagina delle proprietà **Certificati** consente di aggiungere informazioni sui certificati alla configurazione del servizio. Si noti che i certificati non sono inclusi con il servizio. È necessario caricarli separatamente in Azure tramite il [Portale di gestione di Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

Per associare un certificato al ruolo, fornire un nome per il certificato. Questo nome sarà usato per fare riferimento al certificato quando si configura un endpoint HTTPS nella pagina delle proprietà **Endpoint**. Specificare quindi se l'archivio certificati è **Computer locale** o **Utente corrente** e il nome dell'archivio. Infine, immettere l'identificazione personale del certificato. Se il certificato si trova nell'archivio **Utente corrente\\Personale**, è possibile immettere l'identificazione personale del certificato selezionandolo da un elenco popolato. Se si trova in qualsiasi altro percorso, immettere manualmente il valore dell'identificazione personale.

Quando si aggiunge un certificato dall'archivio certificati, qualsiasi certificato intermedio viene aggiunto automaticamente alle impostazioni di configurazione. Anche questi certificati intermedi devono essere caricati in Azure al fine di configurare correttamente il servizio per SSL.

Qualsiasi certificato di gestione associato al servizio si applica al servizio solo quando è in esecuzione nel cloud. Quando il servizio è in esecuzione nell'ambiente di sviluppo locale, viene usato un certificato standard gestito dall'emulatore di calcolo.

## Configurazione del progetto di servizio cloud di Azure

Per configurare le impostazioni di un intero progetto di servizio cloud di Azure, aprire prima di tutto il menu di scelta rapida del nodo del progetto e scegliere **Proprietà** per aprire le pagine delle proprietà. La tabella seguente mostra le pagine delle proprietà.

|Pagina Proprietà|Descrizione|
|---|---|
|Applicazione|In questa pagina sono visualizzate le informazioni sulla versione di Strumenti di Azure usata da questo progetto di servizio cloud. È inoltre possibile eseguire l'aggiornamento alla versione corrente degli strumenti.|
|Eventi di compilazione|In questa pagina è possibile impostare gli eventi di pre-compilazione e di post-compilazione.|
|Sviluppo|In questa pagina è possibile specificare le istruzioni per la configurazione della compilazione e le condizioni in cui sono eseguiti gli eventi di post-compilazione.|
|Web|In questa pagina è possibile configurare le impostazioni che sono correlate al server Web.|

## Passaggi successivi

Per ulteriori informazioni sulla configurazione di progetti di servizi cloud di Azure, vedere [Gestione dei ruoli nei progetti dei servizi cloud di Azure con Visual Studio](vs-azure-tools-cloud-service-project-managing-roles.md).

<!---HONumber=Sept15_HO4-->