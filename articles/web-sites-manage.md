<properties urlDisplayName="How to manage" pageTitle="Come gestire i siti Web - Gestione dei servizi Microsoft Azure" metaKeywords="Azure portal website management" description="Un riferimento per le pagine di gestione del sito Web del portale in Microsoft Azure. Vengono fornite informazioni dettagliate per ogni pagina di gestione del sito Web." metaCanonical="" services="web-sites" documentationCenter="" title="How to Manage Websites" authors="cephalin"  solutions="" writer="mwasson" manager="wpickett" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/18/2014" ms.author="mwasson" />

#<a name="howtomanage"></a>Gestire i siti Web tramite il portale di gestione di Azure

Nel portale di Azure è disponibile un set di pagine o "schede" che consentono di gestire i siti Web. Ogni pagina di gestione del sito Web è descritta di seguito.

## Guida introduttiva ##
Nella pagina di gestione **Guida introduttiva** sono incluse le sezioni seguenti:

- **Scarica gli strumenti**: include i collegamenti a [Install WebMatrix][mswebmatrix] e [Microsoft Azure SDK][azuresdk].
- **Pubblica l'app**: include i collegamenti per scaricare il profilo di pubblicazione del sito Web, reimpostare le credenziali di distribuzione per il sito Web, aggiungere uno slot di pubblicazione (distribuzione) di gestione temporanea a un sito non di gestione temporanea e acquisire altre informazioni sulla pubblicazione di gestione temporanea.
- **Integra controllo del codice sorgente**: consente di configurare e gestire la distribuzione da strumenti di controllo del codice sorgente o siti Web quali TFS, CodePlex, GitHub, Dropbox, Bitbucket o Git locale.

## Dashboard ##
Nella pagina di gestione **Dashboard** sono inclusi gli elementi seguenti:

Un grafico in cui è riportato un riepilogo dell'uso del sito Web come misure di determinate metriche.

 - **Tempo CPU**: misura dell'uso del sito Web della CPU.
 - **Dati in entrata**: misura dei dati ricevuti dal sito Web dai client.
 - **Dati in uscita**: misura dei dati inviati dal sito Web ai client.
 - **Errori server HTTP**: numero di messaggi di errore del server HTTP 5xx inviati.
 - **Richieste**: conteggio di tutte le richieste client al sito Web.

**Nota:** per aggiungere ulteriori metriche delle prestazioni, passare alla pagina di gestione **Monitora** e scegliere **Aggiungi metriche** nella parte inferiore della pagina. Per altre informazioni, vedere [Come monitorare Siti Web][Monitor].

**Stato endpoint Web**: elenco degli endpoint Web configurati per il monitoraggio. Se non sono stati configurati endpoint, fare clic su **Configura monitoraggio endpoint Web** e passare alla sezione **Monitoraggio** della pagina di gestione **Configura**. Per altre informazioni, vedere [Come monitorare Siti Web][Monitor].

**Stato scalabilità automatica**: nella modalità standard è disponibile la scalabilità automatica delle risorse in modo da spendere solo per l'uso delle risorse necessarie. Per abilitare la scalabilità automatica, scegliere **Configura scalabilità automatica** per passare alla pagina **Scala**. Se il sito Web è in modalità Gratuito o Condiviso, sarà necessario passare alla modalità Standard (dalla pagina **Scala**) prima di poter configurare la scalabilità automatica. **Log operazioni scalabilità automatica**: reindirizza l'utente al portale **Servizi di gestione** in cui è possibile visualizzare la cronologia di scalabilità automatica del sito Web. La query prevede solo le ultime 24 ore, ma è possibile modificarla.

**Panoramica sull'utilizzo**: in questa sezione sono visualizzate le statistiche relative all'uso di CPU, file system e memoria del sito Web.

**Risorse collegate**: in questa sezione viene visualizzato un elenco di risorse, ad esempio un database SQL o MySQL o un account di archiviazione di Microsoft Azure, che sono collegate al sito Web. Fare clic sul nome della risorsa per gestirla. Se si dispone di un database MySQL, facendo clic sul nome si verrà reindirizzati alla pagina di gestione ClearDB. Da cui sarà possibile visualizzare le metriche delle prestazioni o passare al dashboard ClearDB che consente di aggiornare il database MySQL, se necessario. Se non sono presenti risorse nell'elenco, fare clic su **Gestisci risorse collegate** per passare alla pagina **Risorse collegate** da cui è possibile aggiungere un collegamento a una risorsa per il sito Web.

Una sezione **Riepilogo rapido** in cui sono incluse le informazioni di riepilogo e i collegamenti seguenti (a seconda delle impostazioni, alcune delle opzioni riportate di seguito potrebbero non essere visualizzate):

 - **Visualizza componenti aggiuntivi disponibili**: consente di aprire la finestra di dialogo **Acquista dal negozio** in cui è possibile acquistare componenti aggiuntivi che offrono ulteriori funzionalità per il sito Web. Alcuni componenti aggiuntivi potrebbero non essere disponibili nell'area geografica dell'utente o per l'ambiente in uso.
 - **Visualizza stringhe di connessione**: consente di visualizzare le stringhe di connessione del database del sito Web.
 - **Scaricare il profilo di pubblicazione**: fare clic su questo collegamento per scaricare il profilo di pubblicazione per il sito Web. Il profilo di pubblicazione contiene le credenziali dell'utente (nome utente e password) e gli URL per caricare il contenuto nel sito Web tramite FTP e Git. Il file del profilo è in formato XML e può essere visualizzato in un editor di testo.
 - **Imposta credenziali di distribuzione**: fare clic su questa opzione per creare un nome utente e una password allo scopo di caricare il contenuto nel sito Web tramite FTP o Git. È possibile usare queste credenziali per effettuare il push del contenuto in qualsiasi sito Web della sottoscrizione (vedere [Credenziali FTP]). **Nota**: l'autenticazione a un host FTP o a un repository Git mediante l'uso delle credenziali dell'account Microsoft (Live ID) non è supportata.
 - **Reimpostare le proprie credenziali del profilo di pubblicazione**: consente di reimpostare il profilo di pubblicazione per il sito Web. I profili di pubblicazione scaricati in precedenza diventeranno non validi.
 - **Imposta distribuzione dal controllo del codice sorgente**: consente di visualizzare una finestra di dialogo in cui è possibile configurare la pubblicazione continua da Team Foundation Service, CodePlex, GitHub, Dropbox, Bitbucket o Local Git.
 - **Aggiungi nuovo slot di distribuzione**: per i siti nella modalità standard, è possibile usare questa funzionalità per creare uno slot di gestione temporanea per il sito Web. Lo slot di gestione temporanea (sito di gestione temporanea) consente di convalidare il contenuto e la configurazione del sito prima di passare all'ambiente di produzione. È inoltre possibile usare la versione di gestione temporanea del sito per aggiungere gradualmente aggiornamenti del contenuto e quindi effettuare lo scambio alla produzione dopo che gli aggiornamenti nello slot di gestione temporanea sono stati completati. Non è possibile aggiungere uno slot a un sito già in gestione temporanea.
 - **Modifica in Visual Studio Online**: fare clic su questo collegamento per modificare il sito Web direttamente online tramite Visual Studio Online dal portale di Microsoft Azure. Questa opzione non verrà visualizzata se non viene abilitata nella pagina **Configura**.
 - **Esci da Dropbox**: se è stata configurata una connessione a Dropbox ai fini della distribuzione, questo collegamento consente di effettuare la disconnessione.
 - **Elimina archivio GIT**: se è stato configurato un repository Git, questo collegamento consente di eliminarlo.
 - **Stato**: indica se il sito Web è in esecuzione.
 - **Servizi di gestione**: fare clic sul collegamento **Log operazioni** per visualizzare i log delle operazioni per il sito Web dal portale dei servizi di gestione di Microsoft Azure.
 - **Indirizzo IP virtuale**: consente di visualizzare l'indirizzo IP virtuale del sito Web se è stata configurata un'associazione SSL basata su IP per il sito Web nella sezione **Associazioni SSL** della scheda **Configura**. 
 - **URL sito**: specifica l'indirizzo pubblicamente accessibile del sito Web in Internet.
 - **Modalità di calcolo**: specifica se il sito Web è in esecuzione in modalità Gratuito, Condiviso, Basica o Standard. Per altre informazioni sulle modalità dei gruppi di scala Web, vedere [Come scalare un sito Web][Scale].
 - **Nome host FTP**: specifica l'URL da usare per la pubblicazione nel sito Web tramite FTP (vedere [Credenziali FTP]).
 - **Nome host FTPS**: specifica l'URL da usare per la pubblicazione nel sito Web tramite FTPS (vedere [Credenziali FTP]).
 - **Utente FTP/distribuzione**: indica l'account usato per la distribuzione del sito Web in Microsoft Azure tramite FTP o Git (vedere [Credenziali FTP]).
 - **Registri di diagnostica FTP**: specifica il percorso FTP dei log di diagnostica del sito Web se è stata abilitata la registrazione diagnostica nella pagina di gestione **Configura**.
 - **Registri di diagnostica FTP**: specifica il percorso FTP dei log di diagnostica del sito Web se è stata abilitata la registrazione diagnostica nella pagina di gestione **Configura**.
 - **Località**: specifica l'area geografica del data center che ospita il sito Web.
 - **Nome sottoscrizione**: specifica il nome della sottoscrizione a cui è associato il sito Web.
 - **ID sottoscrizione**: specifica l'ID sottoscrizione univoco (GUID) della sottoscrizione a cui è associato il sito Web.


##Distribuzioni##
 Questa scheda viene visualizzata solo se è stata impostata la distribuzione dal controllo del codice sorgente. Nella pagina di gestione **Distribuzioni** viene visualizzato un riepilogo di tutte le distribuzioni effettuate nel sito Web usando il metodo di pubblicazione scelto. Se per il sito Web è stata configurata la pubblicazione Git ma non è stata effettuata alcuna distribuzione, nella pagina di gestione **Distribuzioni** vengono fornite informazioni su come usare Git per distribuire l'applicazione Web nel sito Web.

##Monitora##
Nella pagina di gestione **Monitora** è disponibile un grafico in cui sono visualizzate le informazioni sull'uso per il sito Web. Per impostazione predefinita, questo grafico visualizza le stesse metriche del grafico nella pagina **Dashboard**, come descritto in precedenza nella sezione Dashboard. È inoltre possibile configurare il grafico per visualizzare metriche quali operazioni HTTP completate, reindirizzamenti HTTP, errori HTTP 401, errori HTTP 403, errori HTTP 404 ed errori HTTP 406. Per altre informazioni su queste metriche, vedere [Come monitorare i siti Web][Monitor].

##WebJobs##
La pagina di gestione WebJobs consente di creare attività su richiesta, pianificate o con esecuzione continua per il sito Web. Per altre informazioni, vedere [Come usare la funzionalità WebJobs in Siti Web di Microsoft Azure](http://www.windowsazure.com/it-it/documentation/articles/web-sites-create-web-jobs/).

##Configura##
È possibile usare la pagina di gestione **Configura** per configurare le impostazioni specifiche dell'applicazione.

Per informazioni dettagliate, vedere [Come configurare i siti Web][Configure].


##Scala##
Nella pagina di gestione **Scala** è possibile specificare la modalità del gruppo di scala Web (**Gratuito**, **Condiviso**, **Basic** o **Standard**). **Le modalità Condiviso**, **Basic** e **Standard** offrono prestazioni e velocità effettiva ottimizzate. **Le modalità Condiviso**, **Basic** e **Standard** consentono di aumentare il valore di **Conteggio istanze**, ovvero il numero di macchine virtuali usate dal sito Web e da altri siti Web nello stesso gruppo di scala Web.
 
In modalità **Standard** è inoltre possibile aumentare il numero di core e la capacità di memoria di ogni istanza modificando il valore in **Dimensioni istanze**.  Ai fini della convenienza, è possibile scegliere l'opzione **Scalabilità automatica** per consentire a Microsoft Azure di allocare le risorse per il sito Web in modo dinamico. 

Per altre informazioni sulla configurazione delle opzioni di scala per un sito Web, vedere [Come aggiungere risorse a un sito Web][Scale].

##Risorse collegate##
Nella pagina di gestione **Risorse collegate** è disponibile un elenco di risorse di Microsoft Azure in uso nel sito Web, tra cui database SQL, database MySQL e account di archiviazione di Azure. Fare clic sul nome della risorsa per gestirla.

##Backup##
La pagina di gestione **Backup** consente di creare backup automatici o manuali per il sito Web, ripristinare il sito Web a uno stato precedente o creare un nuovo sito Web in base a uno dei backup esistenti. Per altre informazioni, vedere [Backup di siti Web di Microsoft Azure](http://www.windowsazure.com/it-it/documentation/articles/web-sites-backup/) e [Ripristino di un sito Web di Microsoft Azure](http://www.windowsazure.com/it-it/documentation/articles/web-sites-restore/).

##Icone delle pagine di gestione##
Le icone sono visualizzate nella parte inferiore delle pagine di gestione del sito Web. La maggior parte delle icone è visualizzata in più pagine mentre alcune sono visualizzate solo in pagine specifiche.  Le icone seguenti sono visualizzate nella parte inferiore della pagina di gestione **Dashboard**:

- **Sfoglia**: apre la pagina predefinita per il sito Web.
- **Arresta**: arresta il sito Web.
- **Riavvia**: riavvia il sito Web.
- **Gestisci domini**: mappa un dominio a questo sito Web. Non disponibile per i siti in modalità di scala **Gratuito**
- **Elimina**: elimina il sito Web.
- **WebMatrix**: apre i siti Web supportati in WebMatrix, consentendo la modifica del sito Web e la pubblicazione delle modifiche apportate al sito Web in Microsoft Azure.

Le icone seguenti non sono visualizzate nella parte inferiore della pagina di gestione **Dashboard**, ma sono disponibili nella parte inferiore di altre pagine di gestione per lo svolgimento di attività specifiche:

- **Aggiungi metriche**: è disponibile nella parte inferiore della pagina di gestione **Monitora** e consente di aggiungere metriche al grafico visualizzato nella pagina di gestione Monitor.
- **Collegamento**: è disponibile nella parte inferiore della pagina **Risorse collegate** e consente di creare collegamenti di gestione ad altre
-  risorse di Microsoft Azure. Se ad esempio il sito Web accede a un database SQL, è possibile creare un collegamento di gestione alla risorsa di database facendo clic su **Collegamento**.


## Credenziali FTP

È possibile usare due set di credenziali FTP: credenziali di *distribuzione* e credenziali del *profilo di pubblicazione*.  Le principali differenze sono le seguenti:

**Credenziali di distribuzione**

- Associate a un account Microsoft. 
- Possono essere usate per distribuire su qualsiasi sito Web, in tutte le sottoscrizioni associate all'account. 
- L'utente sceglie il nome utente/la password.
- Sono in genere usate per la distribuzione Git o FTP.

	 
**Credenziali del profilo di pubblicazione**

- Associate a un singolo sito Web. 
- L'utente non sceglie il nome utente o la password.
- Sono in genere usate per la distribuzione Web, ma possono essere usate anche per FTP.


È possibile usare un set di credenziali o l'altro. I nomi host FTP e FTPS sono indicati nel dashboard, in **Riepilogo rapido**.


### Uso delle credenziali di distribuzione

Per configurare le credenziali di distribuzione: 

1.	Nel portale di gestione, passare alla pagina **Dashboard del sito Web**.
2.	Fare clic su **Imposta credenziali di distribuzione**.
3.	Nella finestra di dialogo, immettere un nome utente e la password.

Nota: nel passaggio 2, se si dispone già di credenziali di distribuzione, verrà visualizzata l'opzione **Reimposta credenziali di distribuzione**. Fare clic su questa opzione per impostare una nuova password o modificare il nome utente.

Le credenziali di distribuzione sono associate a un account Microsoft. Se si modifica il nome utente o la password, la modifica viene applicata a tutti i siti Web associati all'account. Se una sottoscrizione di Azure ha più amministratori, ogni persona avrà le proprie credenziali. 
Il nome utente FTP completo è "sitoWeb\nomeutente".  Questo nome è riportato in **Riepilogo rapido**, come **Utente FTP/distribuzione**.


### Uso delle credenziali del profilo di pubblicazione

Ogni sito Web presenta proprie credenziali del profilo di pubblicazione. Per visualizzare queste credenziali:

1.	Nel portale di gestione, passare alla pagina **Dashboard del sito Web**.
2.	Fare clic su **Scaricare il profilo di pubblicazione**.

Il profilo di pubblicazione è un file XML. Contiene due profili, uno per la distribuzione Web e l'altro per FTP.

<pre>
&lt;publishData&gt;
  &lt;publishProfile
    profileName="contoso - Web Deploy"
    publishMethod="MSDeploy"
    publishUrl="contoso.scm.azurewebsites.net:443"
    msdeploySite="contoso"
    userName="$contoso"
    userPWD="abc1234..."
    destinationAppUrl="http://contoso.azurewebsites.net"
    SQLServerDBConnectionString=""
    mySQLDBConnectionString=""
    hostingProviderForumLink="" 
    controlPanelLink="http://windows.azure.com"&gt;
    &lt;databases/&gt;
  &lt;/publishProfile&gt;
  &lt;publishProfile 
    profileName="contoso - FTP" 
    <mark>publishMethod="FTP"</mark> 
    publishUrl="ftp://waws-prod-bay-003.ftp.azurewebsites.windows.net/site/wwwroot" 
    ftpPassiveMode="True" 
    <mark>userName="contoso\$contoso"</mark> 
    <mark>userPWD=" abc1234..."</mark>  
    destinationAppUrl="http://contoso.azurewebsites.net" 
    SQLServerDBConnectionString="" 
    mySQLDBConnectionString="" 
    hostingProviderForumLink="" 
    controlPanelLink="http://windows.azure.com"&gt;
    &lt;databases/&gt;
  &lt;/publishProfile&gt;
&lt;/publishData&gt;
</pre>

Cercare il profilo con <code>publishMethod="FTP"</code>.  Il nome utente è elencato sotto <code>userName</code>e la password è elencata sotto <code>userPWD</code>.

Per reimpostare la password, fare clic su **Reimpostare le proprie credenziali del profilo di pubblicazione**. Per ottenere le nuove credenziali, scaricare di nuovo il profilo di pubblicazione. Le credenziali del profilo di pubblicazione sono associate al sito Web. Ogni sito Web presenta il proprio profilo di pubblicazione.



<!-- LINKS -->
[mswebmatrix]:http://go.microsoft.com/fwlink/?LinkID=226244

[azuresdk]:http://go.microsoft.com/fwlink/?LinkId=246928

[Configura]: http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-configure-websites

[Monitora]: http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-monitor-websites/

[Scala]: http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-scale-websites


<!-- Anchors. -->
[Credenziali FTP]: #ftp-credentials

