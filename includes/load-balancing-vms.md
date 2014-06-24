<properties  writer="josephd" editor="tysonn" manager="dongill" />
# Bilanciamento del carico delle macchine virtuali

Tutte le macchine virtuali create in Azure possono comunicare
automaticamente mediante un canale di rete privato con altre macchine
virtuali dello stesso servizio cloud o nella stessa rete virtuale. Per
tutte le altre comunicazioni in ingresso, ad esempio il traffico avviato
da host Internet o macchine virtuali di altri servizi cloud o in altre
reti virtuali, è necessario un endpoint.

Gli endpoint possono essere utilizzati per diversi scopi. Per
impostazione predefinita, gli endpoint di una macchina virtuale creata
con il portale di gestione di Azure vengono utilizzati e configurati per
il traffico delle sessioni remote di Windows PowerShell e per il
protocollo Remote Desktop Protocol (RDP). Questi endpoint consentono di
amministrare la macchina virtuale in remoto tramite Internet.

Gli endpoint vengono inoltre utilizzati per configurare il servizio di
bilanciamento del carico di Azure e distribuire un tipo specifico di
traffico tra più macchine virtuali o servizi. È ad esempio possibile
dividere il carico del traffico delle richieste Web tra più server Web o
ruoli Web.

A ogni endpoint definito per una macchina virtuale vengono assegnate una
porta pubblica e una privata, con protocollo TCP o UDP. Gli host
Internet inviano il traffico in ingresso all'indirizzo IP pubblico del
servizio cloud e a una porta pubblica. Le macchine virtuali e i servizi
all'interno del servizio cloud restano in ascolto sul rispettivo
indirizzo IP privato e sulla porta privata. Il servizio di bilanciamento
del carico mappa l'indirizzo IP pubblico e il numero di porta del
traffico in ingresso con l'indirizzo IP privato e il numero di porta
della macchina virtuale e viceversa per il traffico di risposta
proveniente dalla macchina virtuale.

Quando si configura il bilanciamento del carico del traffico tra più
macchine virtuali o servizi, Azure fornisce una distribuzione casuale
del traffico in ingresso.

Per un servizio cloud contenente istanze di ruoli Web o ruoli di lavoro,
è possibile definire un endpoint pubblico nella definizione del
servizio. Per un servizio cloud contenente macchine virtuali, è
possibile aggiungere un endpoint a una macchina virtuale durante la
creazione oppure in seguito.

Nella figura seguente è illustrato un endpoint con carico bilanciato per
il traffico Web (non crittografato) condiviso tra tre macchine virtuali
per la porta TCP 80, pubblica e privata. Queste tre macchine virtuali
appartengono a un set con carico bilanciato.

![bilanciamento del
carico](./media/load-balancing-vms/LoadBalancing.png)

Quando i client Internet inviano richieste di pagine Web all'indirizzo
IP pubblico del servizio cloud e alla porta TCP 80, il servizio di
bilanciamento del carico esegue un bilanciamento casuale di queste
richieste tra le tre macchine virtuali del set con carico bilanciato.

Per creare un set di macchine virtuali di Azure con carico bilanciato,
attenersi alla procedura seguente:

* [Passaggio 1: Creare la prima macchina virtuale](#firstmachine)
* [Passaggio 2: Creare ulteriori macchine virtuali nello stesso servizio
  cloud](#addmachines)
* [Passaggio 3: Creare un set con carico bilanciato con la prima
  macchina virtuale](#loadbalance)
* [Passaggio 4: Aggiungere le macchine virtuali al set con carico
  bilanciato](#addtoset)
## <a  id="firstmachine"> </a>Passaggio 1: Creare la prima macchina virtuale

Accedere al [portale di gestione di Azure][1] se questa operazione non è
già stata eseguita. È possibile creare la prima macchina virtuale con il
metodo From Gallery o Quick Create.

* **From Gallery**: il metodo **From Gallery** consente di creare gli
  endpoint contestualmente alla macchina virtuale, nonché di specificare
  un nome per il servizio cloud creato durante la creazione della
  macchina virtuale. Per le istruzioni, vedere [Creazione di una
  macchina virtuale che esegue
  Linux](../virtual-machines-linux-tutorial) o [Creazione di una
  macchina virtuale che esegue Windows
  Server](../virtual-machines-windows-tutorial).

* **Quick Create**: consente di creare una macchina virtuale scegliendo
  un'immagine dalla Raccolta immagini e specificando le informazioni di
  base. Se si utilizza questo metodo, è necessario aggiungere
  l'endpoint dopo avere creato la macchina virtuale. Con questo metodo
  viene inoltre creato un servizio cloud con un nome predefinito. Per
  ulteriori informazioni, vedere [Come creare rapidamente una macchina
  virtuale](../virtual-machines-quick-create).

**Nota**: dopo aver creato la macchina virtuale con il metodo Quick
Create, nella pagina Cloud Services del portale di gestione viene
visualizzato il nome del servizio cloud insieme ad altre informazioni
correlate.
## <a  id="addmachines"> </a>Passaggio 2: Creare ulteriori macchine virtuali nello stesso servizio cloud

Creare altre macchine virtuali nello stesso servizio cloud della prima
utilizzando il metodo From Gallery.
## <a  id="loadbalance"> </a>Passaggio 3: Creare un set con carico bilanciato con la prima macchina virtuale

1.  Nel portale di gestione di Azure fare clic su **Virtual Machines** e
    quindi sul nome della prima macchina virtuale.

2.  Far clic su **Endpoints**, quindi su **Add**.

3.  Nella pagina Add an endpoint to a virtual machine fare clic sulla
    freccia destra.

4.  Nella pagina Specify the details of the endpoint eseguire le
    operazioni seguenti:
    
    * In **Name** digitare un nome per l'endpoint o selezionarne uno
      dall'elenco di endpoint predefiniti per i protocolli comuni.
    * In **Protocol** selezionare il protocollo richiesto dal tipo di
      endpoint, ossia TCP o UDP.
    * In **Public Port** e **Private Port** digitare il numero di porta
      da utilizzare per la macchina virtuale. È possibile utilizzare le
      regole relative a porte private e firewall sulla macchina virtuale
      per reindirizzare il traffico in modo appropriato per
      l'applicazione. La porta privata può essere uguale alla porta
      pubblica. Per un endpoint per il traffico Web (HTTP), è ad esempio
      possibile assegnare la porta 80 sia come porta pubblica che
      privata.

5.  Selezionare **Create a load-balanced set** e quindi fare clic sulla
    freccia destra.

6.  Nella pagina Configure the load-balanced set immettere un nome per
    il set con carico bilanciato e quindi assegnare i valori per il
    comportamento del probe del servizio di bilanciamento del carico di
    Azure. Il servizio di bilanciamento del carico utilizza i probe per
    determinare se le macchine virtuali del set con carico bilanciato
    sono disponibili a ricevere traffico in ingresso.

7.  Fare clic sul segno di spunta per creare l'endpoint con carico
    bilanciato. Nella colonna **Load-balanced set name** della pagina
    **Endpoints** relativa alla macchina virtuale verrà visualizzata
    l'indicazione **Yes**.
## <a  id="addtoset"> </a>Passaggio 4: Aggiungere le macchine virtuali al set con carico bilanciato

Dopo avere creato il set con carico bilanciato, aggiungervi le altre
macchine virtuali. Per ogni macchina virtuale dello stesso servizio
cloud, eseguire le operazioni seguenti:

1.  Nel portale di gestione fare clic su **Virtual Machines**, quindi
    sul nome della macchina virtuale, su **Endpoints** e infine su
    **Add**.

2.  Nella pagina Add an endpoint to a virtual machine page fare clic su
    **Add endpoint to an existing load-balanced set**, selezionare il
    nome del set con carico bilanciato e quindi fare clic sulla freccia
    destra.

3.  Nella pagina Specify the details of the endpoint page digitare un
    nome per l'endpoint e quindi fare clic sul segno di spunta.

<!-- LINKS -->



[1]: http://manage.windowsazure.com
