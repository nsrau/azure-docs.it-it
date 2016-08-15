<properties
	pageTitle="Eseguire la migrazione da Servizi mobili a un'app per dispositivi mobili del servizio app"
	description="Informazioni su come eseguire facilmente la migrazione dell'applicazione Servizi mobili a un'app per dispositivi mobili del servizio app"
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="adrianhall"/>

# <a name="article-top"></a>Eseguire la migrazione del servizio mobile di Azure esistente al servizio app di Azure

Con la [disponibilità generale del servizio app di Azure], è possibile eseguire facilmente la migrazione sul posto dei siti di Servizi mobili di Azure per sfruttare appieno tutte le funzionalità del servizio app di Azure. Questo documento illustra cosa accade durante la migrazione del sito da Servizi mobili di Azure al servizio app di Azure.

## <a name="what-does-migration-do"></a>Effetti della migrazione sul sito

La migrazione trasforma il servizio mobile di Azure in un'app del [servizio app di Azure] senza influire minimamente sul codice. Gli hub di notifica, la connessione dati SQL, le impostazioni di autenticazione, i processi pianificati e il nome di dominio rimarranno invariati. I client mobili che usano il servizio mobile di Azure continueranno a funzionare normalmente. Al termine del trasferimento nel servizio app di Azure, la migrazione comporterà il riavvio del servizio.

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Perché eseguire la migrazione del sito

È consigliabile eseguire la migrazione da Servizi mobili di Azure per sfruttare appieno tutte le funzionalità del servizio app di Azure, tra cui:

  *  Nuove funzionalità host, come [Processi Web] e [nomi di dominio personalizzati].
  *  Connettività alle risorse locali attraverso [reti virtuali] e [connessioni ibride].
  *  Monitoraggio e risoluzione dei problemi con New Relic o [Application Insights].
  *  Strumenti DevOps incorporati, tra cui [slot di staging], rollback e test nell'ambiente di produzione.
  *  [Scalabilità automatica], bilanciamento del carico e [monitoraggio delle prestazioni].

Per altre informazioni sui vantaggi del servizio app di Azure, vedere [Confronto tra Servizi mobili e il servizio app].

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare qualsiasi attività importante nel sito, è consigliabile [eseguire un backup degli script e del database SQL di Servizi mobili].

Se si vuole testare il processo di migrazione prima di eseguire la migrazione del sito di produzione, duplicare Servizi mobili di Azure di produzione in una nuova [area di Azure] con una copia dell'origine dati e testare la migrazione rispetto al nuovo URL. Per verificare correttamente il sito di cui è stata eseguita la migrazione è anche necessaria un'implementazione client di test che punta al sito di test.

## <a name="migrating-site"></a>Migrazione dei siti

Il processo di migrazione esegue la migrazione di tutti i siti all'interno di una singola area di Azure.

Per eseguire la migrazione del sito:

  1.  Accedere al [portale di Azure classico].
  2.  Selezionare un servizio mobile nell'area di cui si vuole eseguire la migrazione.
  3.  Fare clic sul pulsante **Esegui la migrazione al servizio app**.

    ![Pulsante di migrazione][0]

  4.  Leggere il contenuto della finestra di dialogo Esegui la migrazione al servizio app.
  5.  Immettere il nome del servizio mobile nell'apposita casella. Ad esempio, se il nome di dominio è contoso.azure-mobile.net, immettere _contoso_ nell'apposita casella.
  6.  Fare clic sul pulsante con il segno di spunta.

È possibile controllare lo stato della migrazione nel monitoraggio attività. La *migrazione* del sito sarà riportata nel portale di Azure classico.

  ![Monitoraggio dell'attività di migrazione][1]

Per ogni servizio mobile di cui viene eseguita la migrazione possono essere necessari dai 3 ai 15 minuti. Durante la migrazione il sito rimane disponibile, ma viene riavviato al termine della migrazione. Durante il processo di riavvio il sito non sarà disponibile per pochi secondi.

## <a name="finalizing-migration"></a>Finalizzazione della migrazione

È consigliabile pianificare il test del sito da un client mobile al termine del processo di migrazione. Assicurarsi che sia possibile eseguire tutte le normali azioni client senza apportare modifiche al client mobile. Assicurarsi anche che le modifiche apportate per rendere effettiva la migrazione, ad esempio la modifica temporanea del piano tariffario, vengano ripristinate se necessario.

### <a name="update-app-service-tier"></a>Selezionare un piano tariffario appropriato per il servizio app

La migrazione al servizio app di Azure offre una maggiore flessibilità dei prezzi.

  1.  Accedere al [portale di Azure].
  2.  Selezionare **Tutte le risorse** o **Servizi app** e quindi fare clic sul nome del servizio mobile di cui è stata eseguita la migrazione.
  3.  Verrà aperto il pannello Impostazioni per impostazione predefinita. In caso contrario, fare clic su **Impostazioni**.
  4.  Fare clic su **Piano di servizio app** nel menu Impostazioni.
  5.  Fare clic sul riquadro **Piano tariffario**.
  6.  Fare clic sul riquadro del piano tariffario più adatto alle proprie esigenze e quindi fare clic su **Seleziona**. Potrebbe essere necessario fare clic su **Visualizza tutto** per visualizzare i piani tariffari disponibili.

Come punto di partenza, si consiglia quanto segue:

| Piano tariffario del servizio mobile | Piano tariffario del servizio app |
| :-------------------------- | :----------------------- |
| Free | F1 Gratuito |
| Basic | B1 Basic |
| Standard | S1 Standard |

Si noti che c'è una notevole flessibilità nella scelta del piano tariffario per l'applicazione. Per informazioni dettagliate sui prezzi del nuovo servizio app, vedere [Prezzi di Servizio app].

> [AZURE.TIP] Il livello Standard del servizio app include l'accesso a molte funzionalità utili, come ad esempio [slot di staging], backup automatico e ridimensionamento automatico. Sono anche disponibili nuove funzionalità interessanti.

### <a name="review-migration-scheduler-jobs"></a>Esaminare i processi dell'utilità di pianificazione di cui è stata eseguita la migrazione

I processi dell'utilità di pianificazione non saranno visibili fino a circa 30 minuti dopo la migrazione. L'esecuzione dei processi pianificati continuerà in background. Per visualizzare i processi pianificati:

  1.  Accedere al [portale di Azure].
  2.  Selezionare **Sfoglia>**, immettere **Pianificazione** nella casella _Filtro_ e quindi selezionare **Raccolte dell'Utilità di pianificazione**.

Dopo la migrazione è disponibile un numero limitato di processi dell'utilità di pianificazione gratuiti. È consigliabile esaminare l'utilizzo e i [piani dell'utilità di pianificazione di Azure].

### <a name="configure-cors"></a>Configurare la condivisione CORS se necessario

La condivisione di risorse tra le origini è una tecnica che consente a un sito Web di accedere a un'API Web in un dominio diverso. Se si usa Servizi mobili di Azure con un sito Web associato è necessario configurare la condivisione CORS nell'ambito della migrazione. Se l'accesso a Servizi mobili di Azure avviene esclusivamente da dispositivi mobili, non è necessario configurare la condivisione CORS se non in alcuni casi specifici.

Le impostazioni CORS di cui è stata eseguita la migrazione sono disponibili come impostazione app **MS\_CrossDomainWhitelist**. Per eseguire la migrazione del sito alla funzionalità CORS del servizio app:

  1.  Accedere al [portale di Azure].
  2.  Selezionare **Tutte le risorse** o **Servizi app** e quindi fare clic sul nome del servizio mobile di cui è stata eseguita la migrazione.
  3.  Verrà aperto il pannello Impostazioni per impostazione predefinita. In caso contrario, fare clic su **Impostazioni**.
  4.  Fare clic su **CORS** nel menu dell'API.
  5.  Immettere tutte le origini consentite nell'apposita casella, premendo INVIO dopo ogni immissione.
  6.  Dopo aver compilato correttamente l'elenco di origini consentite, fare clic sul pulsante Salva.

Questa attività è facoltativa, ma consente di migliorare l'esperienza di gestione successiva.

> [AZURE.TIP]  Uno dei vantaggi dell'uso di un servizio app di Azure è che è possibile eseguire il sito Web e il servizio mobile nello stesso sito. Per altre informazioni, vedere la sezione [Passaggi successivi](#next-steps).

### <a name="download-publish-profile"></a>Scaricare un nuovo profilo di pubblicazione

Il profilo di pubblicazione del sito viene modificato durante la migrazione al Servizio app di Azure. Se si intende pubblicare il sito da Visual Studio, sarà necessario un nuovo profilo di pubblicazione. Per scaricare il nuovo profilo di pubblicazione:

  1.  Accedere al [portale di Azure].
  2.  Selezionare **Tutte le risorse** o **Servizi app** e quindi fare clic sul nome del servizio mobile di cui è stata eseguita la migrazione.
  3.  Fare clic su **Recupera profilo**.

Il file PublishSettings verrà scaricato nel computer. In genere verrà chiamato _nomesito_.PublishSettings. È quindi possibile importare le impostazioni di pubblicazione nel progetto esistente:

  1.  Aprire Visual Studio e il progetto di Servizi mobili di Azure.
  2.  Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e selezionare **Pubblica**.
  3.  Fare clic su **Importa**
  4.  Fare clic su **Sfoglia** e selezionare il file delle impostazioni di pubblicazione scaricato. Fare clic su **OK**.
  5.  Fare clic su **Convalida connessione** per verificare il corretto funzionamento delle impostazioni di pubblicazione.
  6.  Fare clic su **Pubblica** per pubblicare il sito.


## <a name="working-with-your-site"></a>Uso del sito dopo la migrazione

Dopo la migrazione sarà possibile iniziare a usare il nuovo servizio app nel [portale di Azure]. Di seguito sono riportate informazioni sulle operazioni specifiche che era possibile eseguire nel [portale di Azure classico], con i rispettivi equivalenti nel servizio app.

### <a name="publishing-your-site"></a>Download e pubblicazione del sito di cui è stata eseguita la migrazione

Il sito è disponibile tramite Git o FTP e può essere pubblicato nuovamente con diversi meccanismi, inclusi WebDeploy, TFS, Mercurial, GitHub e FTP. La migrazione delle credenziali di distribuzione viene eseguita con il resto del sito. Se le credenziali di distribuzione non sono state impostate o non sono disponibili, è possibile reimpostarle:

  1. Accedere al [portale di Azure].
  2. Selezionare **Tutte le risorse** o **Servizi app** e quindi fare clic sul nome del servizio mobile di cui è stata eseguita la migrazione.
  3. Verrà aperto il pannello Impostazioni per impostazione predefinita. In caso contrario, fare clic su **Impostazioni**.
  4. Fare clic su **Credenziali di distribuzione** nel menu PUBBLICAZIONE.
  5. Immettere le nuove credenziali di distribuzione nelle apposite caselle e quindi fare clic sul pulsante Salva.

È possibile usare queste credenziali per clonare il sito con Git o configurare distribuzioni automatizzate da GitHub, TFS o Mercurial. Per altre informazioni, vedere [Documentazione sulla distribuzione del servizio app di Azure].

### <a name="appsettings"></a>Impostazioni dell'applicazione

La maggior parte delle impostazioni di un servizio mobile di cui è stata eseguita la migrazione è disponibile in Impostazioni app. Nel [portale di Azure] è possibile ottenere un elenco delle impostazioni app. Per visualizzare o modificare le impostazioni app:

  1. Accedere al [portale di Azure].
  2. Selezionare **Tutte le risorse** o **Servizi app** e quindi fare clic sul nome del servizio mobile di cui è stata eseguita la migrazione.
  3. Verrà aperto il pannello Impostazioni per impostazione predefinita. In caso contrario, fare clic su **Impostazioni**.
  4. Fare clic su **Impostazioni dell'applicazione** nel menu GENERALE.
  5. Scorrere fino alla sezione Impostazioni app e trovare l'impostazione dell'app.
  6. Fare clic sul valore dell'impostazione dell'app per modificarlo. Fare clic su **Salva** per salvare il valore.

È possibile aggiornare le impostazioni di più app contemporaneamente.

> [AZURE.TIP]  Si noterà che sono disponibili due impostazioni dell'applicazione con lo stesso valore. Ad esempio, _ApplicationKey_ e _MS\_ApplicationKey_. È sufficiente modificare l'impostazione dell'app preceduta dal prefisso **MS\_**. È tuttavia consigliabile aggiornare entrambe le impostazioni app nello stesso momento.

### <a name="authentication"></a>Autenticazione

Tutte le impostazioni di autenticazione sono disponibili come impostazioni app nel sito di cui è stata eseguita la migrazione. Per aggiornare le impostazioni di autenticazione, è necessario modificare le impostazioni app appropriate. La tabella seguente riporta le impostazioni app appropriate per il provider di autenticazione:

| Provider | ID client | Client Secret | Altre impostazioni |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Account Microsoft | **MS\_MicrosoftClientID** | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook | **MS\_FacebookAppID** | **MS\_FacebookAppSecret** | |
| Twitter | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** | |
| Google | **MS\_GoogleClientID** | **MS\_GoogleClientSecret** | |
| Azure AD | **MS\_AadClientID** | | **MS\_AadTenants** |

Nota: l'impostazione **MS\_AadTenants** viene archiviata come elenco delimitato da virgole dei domini del tenant, corrispondente al campo "Tenant consentiti" del portale di Servizi mobili.

> [AZURE.WARNING] **Non usare i meccanismi di autenticazione nel menu Impostazioni**
>
> Il servizio app di Azure fornisce un sistema di autenticazione e autorizzazione senza codice a parte nel menu Impostazioni di _Autenticazione/Autorizzazione_ e l'opzione _Autenticazione per dispositivi mobili_ (deprecata) nel menu Impostazioni. Queste opzioni non sono compatibili con un servizio mobile di Azure di cui è stata eseguita la migrazione. È possibile [aggiornare il sito](app-service-mobile-net-upgrading-from-mobile-services.md) per sfruttare i vantaggi dell'autenticazione del servizio app di Azure.

### <a name="easytables"></a>Dati

La scheda _Dati_ in Servizi mobili è stata sostituita da _Easy Tables_ nel portale di Azure. Per accedere a Easy Tables:

  1. Accedere al [portale di Azure].
  2. Selezionare **Tutte le risorse** o **Servizi app** e quindi fare clic sul nome del servizio mobile di cui è stata eseguita la migrazione.
  3. Verrà aperto il pannello Impostazioni per impostazione predefinita. In caso contrario, fare clic su **Impostazioni**.
  4. Fare clic su **Easy Tables** nel menu MOBILE.

È possibile aggiungere una nuova tabella facendo clic sul pulsante **Aggiungi** o accedere alle tabelle esistenti facendo clic sul nome della tabella. Da questo pannello è possibile eseguire una serie di operazioni, tra cui:

  * Modifica delle autorizzazioni tabella
  * Modifica degli script operativi
  * Gestione dello schema di tabella
  * Eliminazione della tabella
  * Cancellazione del contenuto della tabella
  * Eliminazione di righe specifiche della tabella

### <a name="easyapis"></a>API

La scheda _API_ in Servizi mobili è stata sostituita da _Easy APIs_ nel portale di Azure. Per accedere a Easy APIs:

  1. Accedere al [portale di Azure].
  2. Selezionare **Tutte le risorse** o **Servizi app** e quindi fare clic sul nome del servizio mobile di cui è stata eseguita la migrazione.
  3. Verrà aperto il pannello Impostazioni per impostazione predefinita. In caso contrario, fare clic su **Impostazioni**.
  4. Fare clic su **Easy APIs** nel menu MOBILE.

Le API di cui è stata eseguita la migrazione sono già elencate nel pannello. Dal pannello è anche possibile aggiungere una nuova API. Per gestire un'API specifica, fare clic sull'API. Dal nuovo pannello è possibile modificare le autorizzazioni e gli script per l'API.

### <a name="on-demand-jobs"></a>Processi dell'Utilità di pianificazione

Tutti i processi dell'Utilità di pianificazione sono disponibili tramite la sezione relativa alle raccolte dei processi dell'Utilità di pianificazione. Per accedere ai processi dell'Utilità di pianificazione:

  1. Accedere al [portale di Azure].
  2. Selezionare **Sfoglia>**, immettere **Pianificazione** nella casella _Filtro_ e quindi selezionare **Raccolte dell'Utilità di pianificazione**.
  3. Selezionare la raccolta di processi per il sito. Verrà denominata _nomesito_-Processi.
  4. Fare clic su **Impostazioni**.
  5. Fare clic su **Processi dell'Utilità di pianificazione** in GESTISCI.

Verranno elencati i processi pianificati con la frequenza specificata prima della migrazione. I processi su richiesta verranno disabilitati. Per eseguire un processo su richiesta:

  1. Selezionare il processo che si desidera eseguire.
  2. Se necessario, fare clic su **Abilita** per abilitare il processo.
  3. Fare clic su **Impostazioni** e quindi su **Pianifica**.
  4. Selezionare la ricorrenza **Una sola volta** e quindi fare clic su **Salva**

I processi su richiesta si trovano in `App_Data/config/scripts/scheduler post-migration`. Si consiglia di convertire tutti i processi su richiesta in [processi Web]. È consigliabile scrivere i nuovi processi dell'Utilità di pianificazione come [processi Web].

### <a name="notification-hubs"></a>Hub di notifica

Servizi mobili usa Hub di notifica per le notifiche push. Le impostazioni app riportate di seguito vengono usate per collegare l'hub di notifica al servizio mobile dopo la migrazione:

| Impostazione dell'applicazione | Descrizione |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace** | Spazio dei nomi dell'hub di notifica |
| **MS\_NotificationHubName** | Nome dell'hub di notifica |
| **MS\_NotificationHubConnectionString** | Stringa di connessione dell'hub di notifica |
| **MS\_NamespaceName** | Alias per MS\_PushEntityNamespace |

L'hub di notifica viene gestito attraverso il [portale di Azure]. Prendere nota del nome dell'hub di notifica. Per trovarlo è possibile usare le impostazioni app:

  1. Accedere al [portale di Azure].
  2. Selezionare **Sfoglia>** e quindi **Hub di notifica**.
  3. Fare clic sul nome dell'hub di notifica associato al servizio mobile.

> [AZURE.NOTE] L'hub di notifica non sarà visibile se è di tipo "Misto". Gli hub di notifica di tipo "Misto" utilizzano sia Hub di notifica che le funzionalità legacy del bus di servizio. È necessario [convertire gli spazi dei nomi di tipo Misto]. Al termine della conversione, l'hub di notifica verrà visualizzato nel [portale di Azure].

Per altre informazioni, vedere la documentazione relativa a [Hub di notifica].

> [AZURE.TIP] Le funzionalità di gestione di Hub di notifica nel [portale di Azure] sono ancora in anteprima. Il [portale di Azure classico] rimane disponibile per la gestione di tutti gli hub di notifica.

### <a name="legacy-push"></a>Impostazioni push legacy

Se si è configurata la funzione di push per il servizio mobile prima dell'introduzione negli Hub di notifica, si sta usando la funzione di _push legacy_. Se si usa la funzione di push e nella configurazione non è presente un Hub di notifica, è probabile che si stia usando la funzione di _push legacy_. Questa funzionalità sarà ancora disponibile dopo la migrazione, analogamente a tutte le altre. È tuttavia consigliabile eseguire l'aggiornamento a Hub di notifica subito dopo il completamento della migrazione.

Nel frattempo tutte le impostazioni push legacy, con l'importante eccezione del certificato del servizio APN, sono disponibili in Impostazioni app. Il certificato del servizio APN può essere sostituito sostituendo il file appropriato nel sito. Questa operazione può essere eseguita tramite una delle opzioni di distribuzione disponibili per Servizio app di Azure.

### <a name="app-settings"></a>Altre impostazioni app

Per le impostazioni app aggiuntive riportate di seguito viene eseguita la migrazione dal servizio mobile e sono disponibili in *Impostazioni* > *Impostazioni App*:

| Impostazione dell'applicazione | Descrizione |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName** | Nome dell'app |
| **MS\_MobileServiceDomainSuffix** | Prefisso del dominio, ad esempio azure-mobile.net |
| **MS\_ApplicationKey** | Chiave applicazione |
| **MS\_MasterKey** | Chiave master dell'app |

La chiave applicazione e la chiave master devono essere identiche alle chiavi applicazione del servizio mobile di origine. In particolare, la chiave applicazione viene inviata dai client mobili per convalidare l'uso dell'API per dispositivi mobili.

### <a name="cliequivalents"></a>Equivalenti della riga di comando

Non sarà più possibile usare il comando _azure mobile_ per gestire il sito di Servizi mobili di Azure. Molte funzioni sono state sostituite con il comando _azure site_. Usare la tabella seguente per trovare gli equivalenti dei comandi più comuni:

| Comando _azure mobile_ | Comando _azure site_ equivalente |
| :----------------------------------------- | :----------------------------------------- |
| mobile locations | site location list |
| mobile list | site list |
| mobile show _nome_ | site show _nome_ |
| mobile restart _nome_ | site restart _nome_ |
| mobile redeploy _nome_ | site deployment redeploy _commitId_ _nome_ |
| mobile key set _nome_ _tipo_ _valore_ | site appsetting delete _chiave_ _nome_ <br/> site appsetting add _chiave_=_valore_ _nome_ |
| mobile config list _nome_ | site appsetting list _nome_ |
| mobile config get _nome_ _chiave_ | site appsetting show _chiave_ _nome_ |
| mobile config set _nome_ _chiave_ | site appsetting delete _chiave_ _nome_ <br/> site appsetting add _chiave_=_valore_ _nome_ |
| mobile domain list _nome_ | site domain list _nome_ |
| mobile domain add _nome_ _dominio_ | site domain add _dominio_ _nome_ |
| mobile domain delete _nome_ | site domain delete _dominio_ _nome_ |
| mobile scale show _nome_ | site show _nome_ |
| mobile scale change _nome_ | site scale mode _modalità_ _nome_ <br /> site scale instances _istanze_ _nome_ |
| mobile appsetting list _nome_ | site appsetting list _nome_ |
| mobile appsetting add _nome_ _chiave_ _valore_ | site appsetting add _chiave_=_valore_ _nome_ |
| mobile appsetting delete _nome_ _chiave_ | site appsetting delete _chiave_ _nome_ |
| mobile appsetting show _nome_ _chiave_ | site appsetting delete _chiave_ _nome_ |

Aggiornare le impostazioni di autenticazione o di notifica push aggiornando l'impostazione dell'applicazione appropriata. Modificare i file e pubblicare il sito tramite Git o FTP.

### <a name="diagnostics"></a>Diagnostica e registrazione

Nel servizio app di Azure la registrazione diagnostica è generalmente disabilitata. Per abilitare la registrazione diagnostica:

  1. Accedere al [portale di Azure].
  2. Selezionare **Tutte le risorse** o **Servizi app** e quindi fare clic sul nome del servizio mobile di cui è stata eseguita la migrazione.
  3. Verrà aperto il pannello Impostazioni per impostazione predefinita. In caso contrario, fare clic su **Impostazioni**.
  4. Selezionare **Log di diagnostica** nel menu FUNZIONALITÀ.
  5. Fare clic su **Attivato** per i log seguenti: **Registri applicazione (File System)**, **Messaggi di errore dettagliati** e **Traccia delle richieste non riuscite**.
  6. Fare clic su **File System** per la registrazione del server Web.
  7. Fare clic su **Salva**.

Per visualizzare i log:

  1. Accedere al [portale di Azure].
  2. Selezionare **Tutte le risorse** o **Servizi app** e quindi fare clic sul nome del servizio mobile di cui è stata eseguita la migrazione.
  3. Fare clic sul pulsante **Strumenti**.
  4. Selezionare **Flusso di registrazione** nel menu OSSERVAZIONE.

Il flusso dei log verrà visualizzato nella finestra fornita, man mano che questi vengono generati. È anche possibile scaricare i log per analizzarli in seguito usando le credenziali di distribuzione. Per altre informazioni, vedere la documentazione relativa alla [registrazione].

## <a name="known-issues"></a>Problemi noti

### L'eliminazione del clone di un'app per dispositivi mobili di cui è stata eseguita la migrazione causerà un'interruzione del sito

Se si clona il servizio mobile di cui è stata eseguita la migrazione usando Azure PowerShell e si elimina il clone, verrà rimossa la voce DNS per il servizio di produzione. Per questa ragione, il sito non sarà più accessibile da Internet.

Risoluzione: Microsoft sta lavorando per risolvere il problema. Se si vuole clonare il sito, effettuare questa operazione tramite il portale.

### Le modifiche a web.config non funzionano

Se si usa un sito ASP.NET, le modifiche al file `Web.config` non funzioneranno. Il servizio app di Azure compila un file `Web.config` appropriato durante l'avvio per supportare il runtime di Servizi mobili. È possibile eseguire l'override di alcune impostazioni, ad esempio le intestazioni personalizzate, tramite un file di trasformazione XML. Creare un file denominato `applicationHost.xdt`. Questo file deve essere salvato nella directory `D:\home\site` del servizio di Azure. Questa operazione può essere eseguita tramite uno script di distribuzione personalizzato o direttamente tramite Kudu. Di seguito è riportato un documento di esempio:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Per altre informazioni, vedere la documentazione [XDT transform samples] (Esempi di trasformazioni XDT) in GitHub.

### Non è possibile aggiungere a Gestione traffico servizi mobili di cui è stata eseguita la migrazione

Quando si crea un profilo di Gestione traffico, non è possibile scegliere direttamente per il profilo un servizio mobile di cui è stata eseguita la migrazione. È necessario usare un "endpoint esterno". L'endpoint esterno può essere aggiunto solo tramite PowerShell. Per altre informazioni, vedere [Azure Traffic Manager External Endpoints and Weighted Round Robin via PowerShell](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) (Endpoint esterni di Gestione traffico di Azure e round robin ponderato tramite PowerShell).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito la migrazione dell'applicazione nel servizio app, è possibile sfruttare molte altre funzionalità:

  * Gli [slot di staging] della distribuzione consentono lo staging delle modifiche al sito e di eseguire un test A/B.
  * [Processi Web] consente di sostituire i processi pianificati su richiesta.
  * La [distribuzione continua] del sito è resa possibile collegando il sito a GitHub, TFS o Mercurial.
  * È possibile usare [Application Insights] per monitorare il sito.
  * È possibile gestire un sito Web e un'API per dispositivi mobili dallo stesso codice.

### <a name="upgrading-your-site"></a>Aggiornamento del sito di Servizi mobili ad Azure Mobile App SDK

  * Per i progetti server basati su Node.js, il nuovo [Mobile Apps Node.js SDK] fornisce numerose nuove funzionalità. Ad esempio, è possibile ora eseguire sviluppi e debug locali, utilizzare qualsiasi versione Node.js successiva alla 0.10 e personalizzare con qualsiasi middleware Express.js.

  * Per i progetti server basati su .NET, il nuovo [pacchetto NuGet per Mobile Apps SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) ha una maggiore flessibilità sulle dipendenze NuGet, supporta le nuove funzionalità di autenticazione del servizio app e compone con qualsiasi progetto ASP.NET, incluso MVC. Per altre informazioni sull'aggiornamento, vedere [Aggiornamento del servizio mobile .NET di Azure esistente al Servizio app](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Prezzi di Servizio app]: https://azure.microsoft.com/it-IT/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[Scalabilità automatica]: ../app-service-web/web-sites-scale.md
[servizio app di Azure]: ../app-service/app-service-value-prop-what-is.md
[Documentazione sulla distribuzione del servizio app di Azure]: ../app-service-web/web-sites-deploy.md
[portale di Azure classico]: https://manage.windowsazure.com
[portale di Azure]: https://portal.azure.com
[area di Azure]: https://azure.microsoft.com/it-IT/regions/
[piani dell'utilità di pianificazione di Azure]: ../scheduler/scheduler-plans-billing.md
[distribuzione continua]: ../app-service-web/app-service-continuous-deployment.md
[convertire gli spazi dei nomi di tipo Misto]: https://azure.microsoft.com/it-IT/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[nomi di dominio personalizzati]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[disponibilità generale del servizio app di Azure]: /blog/announcing-general-availability-of-app-service-mobile-apps/
[connessioni ibride]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[registrazione]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Mobile Apps Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[Confronto tra Servizi mobili e il servizio app]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Hub di notifica]: ../notification-hubs/notification-hubs-overview.md
[monitoraggio delle prestazioni]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[eseguire un backup degli script e del database SQL di Servizi mobili]: ../mobile-services/mobile-services-disaster-recovery.md
[slot di staging]: ../app-service-web/web-sites-staged-publishing.md
[reti virtuali]: ../app-service-web/web-sites-integrate-with-vnet.md
[Processi Web]: ../app-service-web/websites-webjobs-resources.md
[XDT Transform Samples]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples

<!---HONumber=AcomDC_0803_2016-->