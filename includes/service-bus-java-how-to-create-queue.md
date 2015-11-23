<a id="what-are-service-bus-queues"></a>
## Informazioni sulle code del bus di servizio

Le code del bus di servizio supportano un modello di comunicazione con **messaggistica negoziata**. Quando si usano le code, i componenti di un'applicazione distribuita non comunicano direttamente l'uno con l'altro, ma scambiano messaggi tramite una coda, che agisce da intermediario (broker). Un producer di messaggi (mittente) invia un messaggio alla coda e quindi prosegue con la relativa elaborazione. In modo asincrono, il consumer di messaggi (ricevitore) recupera il messaggio dalla coda e lo elabora. Il producer non deve attendere la risposta del consumer per continuare a elaborare e inviare ulteriori messaggi. Le code consentono un recapito dei messaggi di tipo **FIFO (First In, First Out)** a uno o più consumer concorrenti. In base a questo metodo, in genere i messaggi vengono ricevuti ed elaborati nell'ordine temporale in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuto ed elaborato da un solo consumer.

![Concetti relativi alle code](./media/service-bus-java-how-to-create-queue/sb-queues-08.png)

Le code del bus di servizio sono una tecnologia di carattere generale che può essere usata in numerosi scenari:

-   Comunicazione tra ruoli Web e di lavoro in un'applicazione Azure multilivello.
-   Comunicazione tra app locali e app ospitate in Azure in una soluzione ibrida.
-   Comunicazione tra componenti di un'applicazione distribuita in esecuzione in locale in organizzazioni diverse o in reparti diversi della stessa organizzazione.

L'uso delle code consente la scalabilità orizzontale delle applicazioni e garantisce maggiore resilienza all'architettura.

## Creare uno spazio dei nomi del servizio

Per iniziare a usare le code del bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi del servizio Uno spazio dei nomi fornisce un contenitore di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione.

Per creare uno spazio dei nomi del servizio:

1.  Accedere al [portale di Azure][].

2.  Nel pannello di navigazione sinistro del portale fare clic su **Bus di servizio**.

3.  Nel riquadro inferiore del portale fare clic su **Crea**. ![](./media/service-bus-java-how-to-create-queue/sb-queues-03.png)

4.  Nella finestra di dialogo **Add a new namespace** immettere un nome per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.![](./media/service-bus-java-how-to-create-queue/sb-queues-04.png)

5.  Dopo avere verificato che lo spazio dei nomi è disponibile, scegliere il paese o l'area in cui dovrà essere ospitato. Assicurarsi di usare lo stesso paese/area in cui verranno distribuite le risorse di calcolo.

	IMPORTANTE: selezionare la **stessa area** che si intende scegliere per la distribuzione dell'applicazione. In questo modo sarà possibile ottenere prestazioni ottimali.

6. 	Non modificare i valori predefiniti negli altri campi della finestra di dialogo (**Messaggistica** e **Livello Standard**), quindi fare clic sul segno di spunta. A questo punto, lo spazio dei nomi verrà creato e abilitato nel sistema. Potrebbero essere necessari alcuni minuti per consentire al sistema di effettuare il provisioning delle risorse per lo spazio dei nomi creato.

	![](./media/service-bus-java-how-to-create-queue/getting-started-multi-tier-27.png)

Sarà necessario attendere qualche istante affinché venga attivato lo spazio dei nomi creato che verrà quindi visualizzato nel portale di Azure. Prima di continuare, attendere che lo stato dello spazio dei nomi sia **Attivo**.

## Recuperare le credenziali di gestione predefinite per lo spazio dei nomi

Per poter eseguire le operazioni di gestione, ad esempio creare una coda, nel nuovo spazio dei nomi, è necessario ottenere le credenziali di gestione per lo spazio dei nomi. È possibile ottenere questi valori nel portale di Azure.

###Per ottenere le credenziali di gestione dal portale

1.  Nel riquadro di navigazione sinistro fare clic sul nodo **Bus di servizio** per visualizzare l'elenco degli spazi dei nomi disponibili: ![](./media/service-bus-java-how-to-create-queue/sb-queues-13.png)

2.  Selezionare lo spazio dei nomi appena creato nell'elenco visualizzato.

3.  Fare clic su **Configura** per visualizzare i criteri di accesso condivisi per lo spazio dei nomi. ![](./media/service-bus-java-how-to-create-queue/sb-queues-14.png)

4.  Prendere nota del valore della chiave o copiarlo negli Appunti.

  [portale di Azure]: http://manage.windowsazure.com

  [34]: ./media/service-bus-java-how-to-create-queue/VSProperties.png

<!---HONumber=Nov15_HO3-->