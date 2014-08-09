<a id="what-are-service-bus-queues" ></a>

## Informazioni sulle code del bus di servizio

Le code del bus di servizio supportano un modello di **comunicazione con messaggistica negoziata**. Quando si utilizzano le code, i componenti di un'applicazione distribuita non comunicano direttamente l'uno con l'altro ma scambiano messaggi tramite una coda, che agisce da intermediario. Un producer di messaggi (mittente) invia un messaggio alla coda e quindi prosegue con la relativa elaborazione. In modo asincrono, il consumer di messaggi (ricevitore) recupera il messaggio dalla coda e lo elabora. Il producer non deve attendere la risposta del consumer per continuare a elaborare e inviare ulteriori messaggi. Le code consentono un recapito dei messaggi di tipo **FIFO (First In, First Out)** a uno o più consumer concorrenti. In base a questo metodo, in genere i messaggi vengono ricevuti ed elaborati nell'ordine temporale in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuto ed elaborato da un solo consumer.

![Concetti relativi alle
code](./media/howto-service-bus-queues/sb-queues-08.png)

Le code del bus di servizio sono una tecnologia di carattere generale che può essere utilizzata in numerosi scenari:

* Comunicazione tra ruoli Web e di lavoro in un'applicazione Azure
  multilivello
* Comunicazione tra app locali e app ospitate in Azure in una soluzione
  ibrida
* Comunicazione tra componenti di un'applicazione distribuita in
  esecuzione in locale in organizzazioni diverse o in reparti diversi
  della stessa organizzazione

L'utilizzo delle code consente la scalabilità orizzontale delle applicazioni e garantisce maggiore resilienza all'architettura.

<a id="create-a-service-namespace" ></a>

<h2>Creazione di uno spazio dei nomi servizio</h2>


Per iniziare a utilizzare le code del bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi servizio che fornisce un contenitore di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione.

Per creare uno spazio dei nomi servizio:

1.  Accedere al [portale di gestione di Azure][1].

2.  Nel pannello di navigazione sinistro del portale di gestione fare
    clic su **Service Bus**.

3.  Nel riquadro inferiore del portale di gestione fare clic su
    **Create**.   
     ![](./media/howto-service-bus-queues/sb-queues-03.png)

4.  Nella finestra di dialogo **Add a new namespace** immettere un nome
    per lo spazio dei nomi. Verrà effettuato immediatamente un controllo
    sulla disponibilità del nome.   
     ![](./media/howto-service-bus-queues/sb-queues-04.png)

5.  Dopo avere verificato che lo spazio dei nomi è disponibile,
    scegliere il paese o l'area in cui dovrà essere ospitato.
    Assicurarsi di utilizzare lo stesso paese/area in cui verranno
    distribuite le risorse di calcolo.
    
    IMPORTANTE: selezionare la **stessa area** che si intende scegliere
    per la distribuzione dell'applicazione. In questo modo sarà
    possibile ottenere prestazioni ottimali.

6.  Fare clic sul segno di spunta. A questo punto, lo spazio dei nomi
    servizio verrà creato e abilitato nel sistema. Potrebbero essere
    necessari alcuni minuti per consentire al sistema di effettuare il
    provisioning delle risorse per lo spazio dei nomi creato.
    
    ![](./media/howto-service-bus-queues/getting-started-multi-tier-27.png)

Lo spazio dei nomi creato verrà quindi visualizzato nel portale di gestione e sarà necessario attendere qualche istante affinché venga attivato. Prima di continuare, attendere che lo stato sia **Active**.

<a id="obtain-default-credentials" ></a>

<h2>Recupero delle credenziali di gestione predefinite per lo spazio dei nomi</h2>


Per poter eseguire le operazioni di gestione, ad esempio creare una coda, nel nuovo spazio dei nomi, è necessario ottenere le credenziali di gestione per lo spazio dei nomi. È possibile ottenerle dal portale di gestione oppure da Esplora server di Visual Studio.

### Per ottenere le credenziali di gestione dal portale

1.  Nel pannello di navigazione sinistro fare clic sul nodo **Service
    Bus** per visualizzare l'elenco degli spazi dei nomi disponibili:  
    
     ![](./media/howto-service-bus-queues/sb-queues-13.png)

2.  Selezionare lo spazio dei nomi appena creato nell'elenco
    visualizzato:   
     ![](./media/howto-service-bus-queues/sb-queues-09.png)

3.  Fare clic su **Connection Information**.   
     ![](./media/howto-service-bus-queues/sb-queues-06.png)

4.  Nella finestra di dialogo **Access connection information**
    individuare le voci **Default Issuer** e **Default Key**. Prendere
    nota di questi valori, in quanto dovranno essere utilizzati per
    eseguire operazioni con lo spazio dei nomi.

### Per ottenere le credenziali di gestione da Esplora server

Per ottenere le informazioni di connessione tramite Visual Studio anziché dal portale di gestione, attenersi alla procedura descritta
[qui][2] nella sezione **Per connettersi ad Azure da Visual Studio**.
Quando si accede ad Axure, il nodo **Service Bus** nell'albero
**Microsoft Azure** in Esplora server viene automaticamente popolato con
gli eventuali spazi dei nomi creati. Fare clic con il pulsante destro del mouse su uno spazio dei nomi, quindi scegliere **Proprietà** per visualizzare la stringa di connessione e altri metadati associati allo spazio dei nomi nel riquadro **Proprietà** di Visual Studio.

Prendere nota del valore **SharedAccessKey** oppure copiarlo negli Appunti:

![](./media/howto-service-bus-queues/VSProperties.png)



[1]: http://manage.windowsazure.com
[2]: http://http://msdn.microsoft.com/it-it/library/windowsazure/ff687127.aspx