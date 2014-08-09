<h2><a name="what-are-service-bus-topics"></a>Informazioni su argomenti e sottoscrizioni del bus di servizio</h2>


Gli argomenti e le code del bus di servizio supportano un modello di
**comunicazione con messaggistica di pubblicazione-sottoscrizione**.
Quando si utilizzano gli argomenti e le sottoscrizioni, i componenti di un'applicazione distribuita non comunicano direttamente l'uno con l'altro ma scambiano messaggi tramite un argomento, che agisce da intermediario.

![Concetti relativi agli
argomenti](./media/howto-service-bus-topics/sb-topics-01.png)

Diversamente dalle code del bus di servizio, in cui ogni messaggio viene elaborato da un unico consumer, gli argomenti e le sottoscrizioni offrono una forma di comunicazione **uno a molti** tramite un modello di pubblicazione-sottoscrizione. È possibile registrare più sottoscrizioni a un argomento. Quando un messaggio viene inviato a un argomento, viene reso disponibile affinché ogni sottoscrizione possa gestirlo o elaborarlo in modo indipendente.

La sottoscrizione a un argomento è simile a una coda virtuale che riceve copie dei messaggi che sono stati inviati all'argomento. È possibile registrare regole di filtro per un argomento in base alla sottoscrizione in modo da poter filtrare/limitare i messaggi a un argomento ricevuti dalle diverse sottoscrizioni degli argomenti.

Gli argomenti e le sottoscrizioni del bus di servizio garantiscono scalabilità, consentendo di elaborare grandi quantità di messaggi tra un numero elevato di utenti e applicazioni.

<h2><a name="create-a-service-namespace"></a>Creazione di uno spazio dei nomi servizio</h2>


Per iniziare a utilizzare gli argomenti e le sottoscrizioni del bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi servizio che fornisce un contenitore di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione.

Per creare uno spazio dei nomi servizio:

1.  Accedere al [portale di gestione di Azure][1].

2.  Nel pannello di navigazione sinistro del portale di gestione fare
    clic su **Service Bus**.

3.  Nel riquadro inferiore del portale di gestione fare clic su
    **Create**.   
     ![](./media/howto-service-bus-topics/sb-queues-13.png)

4.  Nella finestra di dialogo **Add a new namespace** immettere un nome
    per lo spazio dei nomi. Verrà effettuato immediatamente un controllo
    sulla disponibilità del nome.   
     ![](./media/howto-service-bus-topics/sb-queues-04.png)

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
    
    ![](./media/howto-service-bus-topics/getting-started-multi-tier-27.png)

<h2><a name="obtain-default-credentials"></a>Recupero delle credenziali di gestione predefinite per lo spazio dei nomi</h2>


Per poter eseguire le operazioni di gestione nel nuovo spazio dei nomi, ad esempio creare un argomento o una sottoscrizione, è necessario ottenere le credenziali di gestione per lo spazio dei nomi. È possibile ottenerle dal portale di gestione oppure da Esplora server di Visual Studio.

### Per ottenere le credenziali di gestione dal portale

1.  Nel pannello di navigazione sinistro fare clic sul nodo **Service
    Bus** per visualizzare l'elenco degli spazi dei nomi disponibili:  
    
     ![](./media/howto-service-bus-topics/sb-queues-13.png)

2.  Selezionare lo spazio dei nomi appena creato nell'elenco
    visualizzato:   
     ![](./media/howto-service-bus-topics/sb-queues-09.png)

3.  Fare clic su **Connection Information**.   
     ![](./media/howto-service-bus-topics/sb-queues-06.png)

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

![](./media/howto-service-bus-topics/VSProperties.png)



[1]: http://manage.windowsazure.com
[2]: http://http://msdn.microsoft.com/it-it/library/windowsazure/ff687127.aspx