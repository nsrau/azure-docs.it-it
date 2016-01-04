<properties
    pageTitle="Debug delle applicazioni Azure in Eclipse"
    description="Informazioni sul debug delle applicazione Azure mediante Azure Toolkit per Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->

# Debug delle applicazioni Azure in Eclipse #

Azure Toolkit per Eclipse consente di eseguire il debug delle applicazioni, indipendentemente dal fatto che siano in esecuzione in Azure o nell'emulatore di calcolo se si usa Windows come sistema operativo. La figura seguente mostra la finestra di dialogo delle proprietà **Debugging** usata per abilitare il debug remoto:

![][ic719504]

In questa esercitazione si presuppone che sia già stata creata correttamente un'applicazione e che si abbia familiarità con l'emulatore di calcolo e con la distribuzione in Azure.

Come punto di partenza per l'argomento verrà usata l'applicazione creata nell'esercitazione [Uso della libreria di runtime del servizio Azure in JSP][]. Se non è stato ancora fatto, creare l'applicazione prima di continuare.

## Per eseguire il debug dell'applicazione durante l'esecuzione in Azure ##

>[AZURE.WARNING]Il supporto attuale del toolkit per il debug remoto Java è destinato principalmente alle distribuzioni in esecuzione nell'emulatore di calcolo di Azure. Poiché la connessione di debug non è sicura, è consigliabile non abilitare il debug remoto in distribuzioni di produzione. Se è necessario eseguire il debug di un'applicazione in esecuzione sul cloud di Azure, usare una distribuzione di gestione temporanea. Occorre tuttavia ricordare che l'accesso non autorizzato alla sessione di debug è possibile se altri utenti conoscono l'indirizzo IP della distribuzione cloud, anche se si tratta di una distribuzione di gestione temporanea.

1. Compilare il progetto per il test nell'emulatore: in Project Explorer di Eclipse fare clic con il pulsante destro del mouse su **MyAzureProject**, scegliere **Properties**, fare clic su **Azure** e impostare **Build for** su **Deployment to cloud**.
1. Ricompilare il progetto: scegliere **Project** dal menu di Eclipse, quindi fare cli su **Build All**.
1. Distribuire l'applicazione in *gestione temporanea* in Azure.

    >[AZURE.IMPORTANT]Come indicato in precedenza, è consigliabile eseguire il debug nell'emulatore di calcolo nella maggior parte dei casi, quindi eseguire il debug nell'ambiente di gestione temporanea solo se è necessario debug aggiuntivo. È consigliabile non eseguire il debug nell'ambiente di produzione.
1. Quando la distribuzione è pronta in Azure, ottenerne il nome DNS dal [portale di gestione di Azure][]. Il nome DNS di una distribuzione di gestione temporanea ha il formato http://*&lt;guid&gt;*.cloudapp.net, dove *&lt;guid&gt;* è un valore GUID assegnato da Azure.
1. In Project Explorer di Eclipse fare clic con il pulsante destro del mouse su **WorkerRole1**, scegliere **Azure** e quindi fare clic su **Debugging**.
1. Nella finestra di dialogo **Properties for WorkerRole1 Debugging**:
    1. Selezionare **Enable remote debugging for this role**.
    1. Per **Input endpoint to use** usare **Debugging (public:8090, private:8090)**.
    1. Assicurarsi che l'opzione **Start JVM in suspended mode, waiting for a debugger connection** sia deselezionata.

        >[AZURE.IMPORTANT]L'opzione **Start JVM in suspended mode, waiting for a debugger connection** è destinata solo a scenari di debug avanzati nell'emulatore di calcolo (non alle distribuzioni cloud). Se viene usata l'opzione **Start JVM in suspended mode, waiting for a debugger connection**, il processo di avvio del server verrà sospeso fino alla connessione del debugger Eclipse alla rispettiva JVM. Anche se è possibile usare questa opzione per una sessione di debug tramite l'emulatore di calcolo, è consigliabile non usarla per una sessione di debug in una distribuzione cloud. L'inizializzazione di un server viene eseguita in un'attività di avvio di Azure e il cloud di Azure rende disponibili gli endpoint pubblici solo dopo il completamento dell'attività di avvio. Un processo di avvio non verrà quindi completato correttamente se questa opzione viene abilitata in una distribuzione cloud, poiché non sarà in grado di ricevere una connessione da un client di Eclipse esterno.
    1. Fare clic su **Create Debug Configurations**.
1. Nella finestra di dialogo **Azure Debug Configuration**:
    1. Per **Java project to debug** selezionare il progetto **MyHelloWorld**.
    1. Per **Configure debugging for** selezionare **Azure cloud (staging)**.
    1. Assicurarsi che l'opzione **Azure compute emulator** sia deselezionata.
    1. Per **Host** immettere il nome DNS della distribuzione di gestione temporanea, senza il prefisso ****http://**. Ad esempio (usare il GUID specifico invece del GUID mostrato qui): **4e616d65-6f6e-6d65-6973-526f62657274.cloudapp.net**
1. Fare clic su **OK** per chiudere la finestra di dialogo **Azure Debug Configuration**.
1. Fare clic su **OK** per chiudere la finestra di dialogo **Properties for WorkerRole1 Debugging**.
1. Se non è stato ancora impostato un punto di interruzione in index.jsp, impostarlo:
    1. In Project Explorer di Eclipse espandere **MyHelloWorld**, quindi **WebContent** e infine fare doppio clic su **index.jsp**.
    1. In index.jsp fare clic con il pulsante destro del mouse sulla barra blu a destra del codice Java e quindi scegliere **Toggle Breakpoints**, come mostrato di seguito: ![][ic551537]
1. In Eclipse fare clic su **Run** e quindi su **Debug Configurations**.
1. Nella finestra di dialogo **Debug Configurations** espandere **Remote Java Application** nel riquadro di sinistra, selezionare **Azure Cloud (WorkerRole1)** e infine fare clic su **Debug**.
1. Nel browser eseguire l'applicazione di gestione temporanea, ****http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld**, sostituendo il GUID dal nome DNS per *&lt;guid&gt;*. Se richiesto da una finestra di dialogo **Confirm Perspective Switch**, fare clic su **Yes**. La sessione di debug dovrebbe ora essere eseguita fino alla riga di codice in cui è stato impostato il punto di interruzione.

>[AZURE.NOTE]Se si tenta di avviare una connessione di debug remoto in una distribuzione in cui sono in esecuzione più istanze del ruolo, non sarà possibile controllare l'istanza a cui il debugger verrà connesso inizialmente, poiché il bilanciamento del carico di Azure sceglierà un'istanza in modo casuale. Dopo la connessione all'istanza, si continuerà a eseguire il debug della stessa istanza. Si noti anche che in caso di un periodo di inattività superiore a 4 minuti (ad esempio in caso di arresto in corrispondenza di un punto di interruzione per troppo tempo) è possibile che Azure chiuda la connessione.

## Debug di un'istanza del ruolo specifica in una distribuzione a più istanze ##

Quando la distribuzione è in esecuzione nel cloud, è probabile che sia eseguita in più istanze di calcolo o del ruolo. Questo permette di sfruttare la garanzia di disponibilità di Azure per il 99,95% del tempo e di scalare orizzontalmente l'applicazione.

In questi scenari potrebbe essere necessario eseguire il debug remoto dell'applicazione Java in un'istanza del ruolo specifica. Se tuttavia si abilita solo un endpoint di input normale per il debug, il bilanciamento del carico di Azure renderà praticamente impossibile la connessione del debugger a un'istanza del ruolo specifica. Effettuerà invece la connessione a un'istanza del ruolo scelta casualmente.

In questo tipo di scenario l'uso degli endpoint di input dell'istanza semplificherà l'esecuzione del debug di un'istanza del ruolo specifica.

Si supponga di pianificare l'esecuzione di un massimo di 5 istanze del ruolo della distribuzione. Usando la pagina delle proprietà **Endpoints** nella finestra di dialogo delle proprietà del ruolo, creare un endpoint di input dell'istanza e assegnare all'endpoint un intervallo di porte pubbliche, invece di un singolo numero di porta. Ad esempio, nella casella di input **Public port** specificare **81-85**.

Dopo la distribuzione dell'applicazione con questo endpoint dell'istanza, Azure assegnerà un numero di porta univoco da questo intervallo a ogni istanza del ruolo. Per scoprire il numero di porta assegnato a ogni istanza, è possibile usare la variabile di ambiente *InstanceEndpointName***\_PUBLICPORT** (dove *InstanceEndpointName* è il nome assegnato durante la creazione dell'endpoint dell'istanza) configurata automaticamente dal toolkit nella distribuzione (ad esempio restituendone il valore nel piè di pagina di una pagina Web, in modo che sia possibile leggerlo quando si passa alla pagina).

Quando si conosce il numero di porta pubblica assegnato all'istanza, sarà possibile farvi riferimento nella configurazione di debug in Eclipse, aggiungendolo alla fine del nome host del servizio. Questo permetterà al debugger di Eclipse di connettersi a tale istanza specifica e non alle altre istanze.

## Solo Windows: per eseguire il debug dell'applicazione durante l'esecuzione nell'emulatore di calcolo ##

>[AZURE.NOTE]L'emulatore di Azure è disponibile solo per Windows. Ignorare questa sezione se si usa un sistema operativo diverso da Windows.

1. Compilare il progetto per il test nell'emulatore: in Project Explorer di Eclipse fare clic con il pulsante destro del mouse su **MyAzureProject**, scegliere **Properties**, fare clic su **Azure** e impostare **Build for** su **Testing in emulator**.
1. Ricompilare il progetto: scegliere **Project** dal menu di Eclipse, quindi fare cli su **Build All**.
1. In Project Explorer di Eclipse fare clic con il pulsante destro del mouse su **WorkerRole1**, scegliere **Azure** e quindi fare clic su **Debugging**.
1. Nella finestra di dialogo **Properties for WorkerRole1 Debugging**:
    1. Selezionare **Enable remote debugging for this role**.
    1. Per **Input endpoint to use**, usare l'endpoint predefinito generato automaticamente dal toolkit, elencato come **Debugging (public:8090, private:8090)**.
    1. Assicurarsi che l'opzione **Start JVM in suspended mode, waiting for a debugger connection** sia deselezionata.
        >[AZURE.IMPORTANT]L'opzione **Start JVM in suspended mode, waiting for a debugger connection** è destinata solo a scenari di debug avanzati nell'emulatore di calcolo (non alle distribuzioni cloud). Se viene usata l'opzione **Start JVM in suspended mode, waiting for a debugger connection**, il processo di avvio del server verrà sospeso fino alla connessione del debugger Eclipse alla rispettiva JVM. Anche se è possibile usare questa opzione per una sessione di debug tramite l'emulatore di calcolo, è consigliabile non usarla per una sessione di debug in una distribuzione cloud. L'inizializzazione di un server viene eseguita in un'attività di avvio di Azure e il cloud di Azure rende disponibili gli endpoint pubblici solo dopo il completamento dell'attività di avvio. Un processo di avvio non verrà quindi completato correttamente se questa opzione viene abilitata in una distribuzione cloud, poiché non sarà in grado di ricevere una connessione da un client di Eclipse esterno.
    1. Fare clic su **Create Debug Configurations**.
1. Nella finestra di dialogo **Azure Debug Configuration**:
    1. Per **Java project to debug** selezionare il progetto **MyHelloWorld**.
    1. Per **Configure debugging for** selezionare **Azure compute emulator**.
1. Fare clic su **OK** per chiudere la finestra di dialogo **Azure Debug Configuration**.
1. Fare clic su **OK** per chiudere la finestra di dialogo **Properties for WorkerRole1 Debugging**.
1. Impostare un punto di interruzione in index.jsp:
    1. In Project Explorer di Eclipse espandere **MyHelloWorld**, quindi **WebContent** e infine fare doppio clic su **index.jsp**.
    1. In index.jsp fare clic con il pulsante destro del mouse sulla barra blu a destra del codice Java e quindi scegliere **Toggle Breakpoints**, come mostrato di seguito: ![][ic551537] È stato impostato un punto di interruzione se viene visualizzata l'icona corrispondente sulla barra blu a sinistra del codice Java.
1. Avviare l'applicazione nell'emulatore di calcolo facendo clic sul pulsante **Run in Azure Emulator** sulla barra degli strumenti di Azure.
1. In Eclipse fare clic su **Run** e quindi su **Debug Configurations**.
1. Nella finestra di dialogo **Debug Configurations** espandere **Remote Java Application** nel riquadro di sinistra, selezionare **Azure Emulator (WorkerRole1)** e infine fare clic su **Debug**.
1. Quando l'emulatore di calcolo indica che l'applicazione è in esecuzione, nel browser eseguire ****http://localhost:8080/MyHelloWorld**. Se richiesto da una finestra di dialogo **Confirm Perspective Switch**, fare clic su **Yes**. La sessione di debug dovrebbe ora essere eseguita fino alla riga di codice in cui è stato impostato il punto di interruzione.

Questa sezione ha illustrato come eseguire il debug nell'emulatore di calcolo. La sezione successiva illustra come eseguire il debug di un'applicazione distribuita in Azure.

## Note sul debug ##

* Dopo il debug, è possibile passare dal **Debug** a **Java** tramite il menu di Eclipse, facendo clic su **Window**, **Open Perspective** e selezionando la prospettiva da usare.
* Per abilitare il debug remoto in GlassFish, non usare la funzionalità di configurazione del debug remoto di Azure Toolkit per Eclipse, ma configurare GlassFish manualmente. A causa del modo in cui GlassFish gestisce le opzioni Java predefinite nelle variabili di ambiente, la funzionalità di configurazione del debug remoto del toolkit non funziona correttamente con GlassFish. Se la funzionalità di configurazione del debug remoto del toolkit è abilitata, potrebbe impedire l'avvio di GlassFish.

## Vedere anche ##

[Toolkit di Azure per Eclipse][]

[Creazione di un'applicazione Hello World per Azure in Eclipse][]

[Installazione di Azure Toolkit per Eclipse][]

Per altre informazioni sull'uso di Azure con Java, visitare il [centro per sviluppatori Java di Azure][].

<!-- URL List -->

[centro per sviluppatori Java di Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[portale di gestione di Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Toolkit di Azure per Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creazione di un'applicazione Hello World per Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installazione di Azure Toolkit per Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Uso della libreria di runtime del servizio Azure in JSP]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png

<!----HONumber=AcomDC_1210_2015-->