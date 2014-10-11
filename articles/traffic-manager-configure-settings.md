<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Configure Traffic Manager Settings" authors="" solutions="" manager="" editor="" />

<tags ms.service="traffic-manager" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Come configurare le impostazioni di Gestione traffico

Gestione traffico di Azure consente di controllare la distribuzione del traffico utente nei servizi ospitati di Azure.

Gestione traffico applica un motore dei criteri intelligente alle query DNS sul nome di dominio aziendale principale. Aggiornare i record di risorse di proprietà della società affinché puntino ai domini di Gestione traffico. I criteri di Gestione traffico associati a tali domini risolvono quindi le query DNS nel nome di dominio aziendale principale agli indirizzi IP di servizi ospitati di Azure specifici contenuti nei criteri di Gestione traffico. Per ulteriori informazioni, vedere [Panoramica di Gestione traffico di Azure][].

## Sommario

-   [Procedura: Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico][]
-   [Procedura: Testare un criterio][]
-   [Procedura: Disabilitare temporaneamente criteri e servizi ospitati][]
-   [Procedura: Modificare un criterio][]
-   [Procedura: Bilanciare il carico del traffico in modo uniforme tra un set di servizi ospitati][]
-   [Procedura: Creare un criterio di failover][]
-   [Procedura: Indirizzare il traffico in ingresso ai servizi ospitati in base alle prestazioni della rete][]

## <span id="howto_point_company"></span></a>Procedura: Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico

Affinché il nome di dominio aziendale punti a un dominio di Gestione traffico, è necessario modificare il record di risorsa DNS nel server DNS tramite CNAME.

Ad esempio, per impostare il dominio aziendale principale **www.contoso.com** in modo che punti a un dominio di Gestione traffico denominato **contoso.trafficmanager.net**, aggiornare il record di risorsa DNS come illustrato di seguito:
`www.contoso.com IN CNAME contoso.trafficmanager.net`

Tutto il traffico indirizzato a *www.contoso.com* verrà ora reindirizzato a *contoso.trafficmanager.net*. Assicurarsi di utilizzare un dominio per il quale si desidera che tutto il traffico venga reindirizzato a Gestione traffico.

## <span id="howto_test"></span></a>Procedura: Testare un criterio

Il modo migliore per testare un criterio è impostare un determinato numero di client e quindi arrestare i servizi nel criterio uno alla volta. Per testare i criteri di Gestione traffico, seguire i suggerimenti riportati di seguito:

-   **Impostare una durata (TTL) DNS molto bassa** in modo che le modifiche si propaghino velocemente, ad esempio 30 secondi.

-   **Prendere nota degli indirizzi IP dei servizi ospitati di Azure** nel criterio sottoposto a test. È possibile ottenere queste informazioni nel portale di gestione di Azure. Fare clic sulla distribuzione di produzione del servizio. Nel pannello Proprietà a destra l'ultima voce sarà costituita dall'indirizzo VIP, ovvero l'indirizzo IP virtuale del servizio ospitato.

    ![Posizione dell'indirizzo IP del servizio ospitato.][]

    **Figura 1** - Posizione dell'indirizzo IP del servizio ospitato.

-   **Utilizzare strumenti che consentono di risolvere un nome DNS in un indirizzo IP** e visualizzare l'indirizzo. È necessario verificare che il nome di dominio aziendale si risolva negli indirizzi IP dei servizi ospitati nel criterio. Questi devono risolversi in modo coerente con il metodo di bilanciamento del carico del criterio di Gestione traffico. Se si utilizza un sistema Windows, è possibile utilizzare nslookup da una finestra di comando (illustrato sotto). Altri strumenti disponibili pubblicamente che consentono di visualizzare i dettagli di un indirizzo IP sono disponibili su Internet.

-   **Verificare il criterio di Gestione traffico** utilizzando *nslookup*.

> **Per verificare il criterio di Gestione traffico tramite nslookup**

> 1.  Avviare una finestra di comando facendo clic su **Start-Esegui** e digitando **CMD**.

> 1.  Per scaricare la cache del resolver DNS, digitare `ipconfig /flushdns`.

> 1.  Digitare il comando `nslookup <your traffic manager domain>`.
>      Il comando seguente, ad esempio, controlla il dominio con il prefisso *myapp.contoso* `nslookup myapp.contoso.trafficmanager.net`

> > Il risultato tipico sarà simile al seguente:

> > -   Nome DNS e indirizzo IP del server DNS a cui si accedere per risolvere questo dominio di Gestione traffico.

> > -   Nome di dominio di Gestione traffico digitato nella riga di comando dopo "nslookup" e indirizzo IP in cui si risolve il dominio di Gestione traffico.
> >     Il secondo indirizzo IP è quello più importante da verificare. Deve corrispondere a un indirizzo VIP per uno dei servizi ospitati nel criterio di Gestione traffico sottoposto a test.

> > ![Esempio di comando nslookup][]

> > **Figura 2** - Esempio di comando nslookup

-   **Utilizzare uno dei metodi di test aggiuntivi indicati di seguito.** Selezionare il metodo appropriato per il tipo di bilanciamento del carico che si sta testando.

### Criteri delle prestazioni

Per testare il dominio in modo efficace, è necessario disporre di client in varie parti del mondo. In Azure è possibile creare client che tentino di chiamare i servizi tramite il nome di dominio aziendale. In alternativa, se l'azienda dispone di più sedi a livello globale, è possibile accedere ai client in altre parti del paese in modalità remota ed eseguire i test da tali client.

Sono disponibili servizi di analisi approfondita e nslookup basati su Web gratuiti. Alcuni di essi consentono di verificare la risoluzione del nome DNS da località diverse. Eseguire una ricerca in nslookup per visualizzare gli esempi. Un'altra opzione prevede l'utilizzo di una soluzione di terze parti, come ad esempio Gomez o Keynote, per verificare che i criteri distribuiscano il traffico come previsto.

### Criteri di failover

1.  Lasciare tutti i servizi attivi.

2.  Utilizzare un singolo client.

3.  Richiedere la risoluzione DNS per il dominio aziendale tramite ping o un'utilità simile.

4.  Verificare che l'indirizzo IP ottenuto sia quello del servizio ospitato primario.

5.  Arrestare il servizio primario o rimuovere il file di monitoraggio affinché Gestione traffico ritenga che sia stato arrestato.

6.  Attendere almeno due minuti.

7.  Richiedere la risoluzione del DNS.

8.  Verificare che l'indirizzo IP ottenuto sia quello del servizio ospitato secondario, come illustrato dall'ordine dei servizi nella finestra di dialogo di modifica del criterio di Gestione traffico.

9.  Ripetere il processo, arrestando il servizio secondario, quindi il terziario e così via. Ogni volta, accertarsi che la risoluzione DNS restituisca l'indirizzo IP del servizio successivo nell'elenco. Quando tutti i servizi sono arrestati, si dovrebbe ottenere di nuovo l'indirizzo IP del servizio ospitato primario.

### Criteri round robin

1.  Lasciare tutti i servizi attivi.

2.  Utilizzare un singolo client.

3.  Richiedere la risoluzione DNS per il dominio di livello principale.

4.  Verificare che l'indirizzo IP ottenuto corrisponda a uno di quelli presenti nell'elenco.

5.  Ripetere il processo lasciando scadere la durata (TTL) DNS in modo da ricevere un nuovo indirizzo IP. Per ognuno dei servizi ospitati dovrebbe essere restituito l'indirizzo IP. Il processo verrà quindi ripetuto.

## <span id="howto_temp_disable"></span></a>Procedura: Disabilitare temporaneamente criteri e servizi ospitati

È possibile disabilitare i criteri di Gestione traffico creati in precedenza affinché il traffico non venga indirizzato. Quando si disabilita un criterio di Gestione traffico, le informazioni del criterio rimarranno invariate e modificabili nell'interfaccia di Gestione traffico. È possibile abilitare di nuovo il criterio per riprendere il routing del traffico.

È inoltre possibile disabilitare singoli servizi ospitati che fanno parte di un criterio di Gestione traffico. Questa azione non elimina il servizio ospitato dal criterio, ma il criterio opera come se il servizio ospitato non vi fosse incluso. Si tratta di un'azione utile quando si desidera rimuovere un servizio ospitato che si trova in modalità di manutenzione o in fase di ridistribuzione e pertanto instabile. Quando il servizio ospitato è di nuovo operativo, è possibile riabilitarlo.

**Nota**
 La disabilitazione di un servizio ospitato non fa riferimento in alcun modo al relativo stato di distribuzione in Azure. Un servizio integro rimane operativo e in grado di ricevere il traffico anche se è disabilitato in Gestione traffico. Inoltre, la disabilitazione di un servizio ospitato in un criterio non influisce sul relativo stato in un altro criterio.

### Per disabilitare un criterio

1.  Selezionare un criterio abilitato nell'albero dell'interfaccia di Gestione traffico.

2.  Fare clic su **Disable Policy** nella barra degli strumenti superiore. Si noti che il pulsante risulterà disattivato se si evidenzia un criterio già disabilitato.

### Per abilitare un criterio

1.  Selezionare un criterio disabilitato nell'albero dell'interfaccia di Gestione traffico.

2.  Fare clic su **Enable Policy** nella barra degli strumenti superiore. Si noti che il pulsante risulterà disattivato se si evidenzia un criterio già disabilitato.

### Per disabilitare un servizio ospitato

1.  Selezionare un servizio ospitato abilitato in un criterio nell'interfaccia di Gestione traffico. Per visualizzare i servizi contenuti all'interno di un criterio, sarà necessario espanderlo.

2.  Fare clic su **Disable Service Policy** nella barra degli strumenti superiore. Si noti che il pulsante risulterà disattivato se si evidenzia un servizio ospitato già disabilitato.

3.  Il traffico non verrà più indirizzato al servizio ospitato in base alla durata TTL DNS impostata per il dominio di Gestione traffico che fa parte del criterio. Per ulteriori informazioni, vedere [Panoramica di Gestione traffico di Azure][1] e scorrere fino alla sezione "Monitoraggio di servizi ospitati in Gestione traffico di Azure".

### Per abilitare un servizio ospitato

1.  Selezionare un servizio ospitato disabilitato in un criterio nell'interfaccia di Gestione traffico. Per visualizzare i servizi contenuti all'interno di un criterio, sarà necessario espanderlo.

2.  Fare clic su **Enable Service Policy** nella barra degli strumenti superiore. Si noti che il pulsante risulterà disattivato se si evidenzia un servizio ospitato già abilitato.

3.  Il traffico verrà di nuovo indirizzato al servizio ospitato come previsto dal criterio.

## <span id="howto_edit_policy"></span></a>Procedura: Modificare un criterio

Se è necessario disabilitare temporaneamente un criterio o servizi ospitati specifici nel criterio, è possibile disabilitarli senza modificare il criterio.

Per cambiare un criterio e impostarlo su un tipo diverso, eseguire la procedura seguente:

1.  **Accedere all'area Gestione traffico nel portale di gestione** all'indirizzo [][]<http://manage.windowsazure.com></a>. Fare clic su **Virtual Network** nella parte inferiore sinistra delle pagine del portale e quindi scegliere **Gestione traffico** dalle opzioni visualizzate nel pannello sinistro.

2.  **Selezionare il criterio** da modificare nella schermata di Gestione traffico nel portale di gestione.

3.  **Fare clic su** Configure nella barra dei menu superiore.

4.  **Modificare il criterio in base alle esigenze.** Si noti che se si modifica il metodo di bilanciamento del carico, a seconda dell'opzione selezionata l'ordine dei servizi ospitati nell'elenco **Selected hosted services** potrebbe essere rilevante o meno.

5.  Fare clic su **Save**.

## <span id="howto_load_balance"></span></a>Procedura: Bilanciare il carico del traffico in modo uniforme tra un set di servizi ospitati

In base a un modello di bilanciamento del carico comune, viene fornito un set di servizi ospitati identici e il traffico viene inviato a ognuno di essi in modalità round robin. In questo articolo vengono illustrate le procedure per configurare un dominio e un criterio di Gestione traffico per implementare questo tipo di bilanciamento del carico.

Per ulteriori informazioni su vari metodi di bilanciamento del carico disponibili in Gestione traffico, vedere [Panoramica di Gestione traffico di Azure][] e scorrere fino alla sezione "Metodi di bilanciamento del carico in Gestione traffico di Azure".

1.  **Distribuire i servizi ospitati** nell'ambiente di produzione. Per ulteriori informazioni sullo sviluppo e la distribuzione di servizi ospitati, vedere [Servizi ospitati di Azure][].

2.  **Accedere all'area Gestione traffico nel portale di gestione** all'indirizzo [][]<http://manage.windowsazure.com></a>. Fare clic su **Virtual Network** nella parte inferiore sinistra delle pagine del portale e quindi scegliere **Gestione traffico** dalle opzioni visualizzate nel pannello sinistro.

3.  **Scegliere Policies e quindi fare clic su "Create".** Scegliere la cartella **Policies** nell'albero di navigazione sinistro per abilitare **Create** nella barra degli strumenti superiore. Scegliere **Create**. Verrà visualizzata la finestra di dialogo **Create Traffic Manager policy**.

    ![Creazione del pulsante per i criteri][]

    **Figura 1** - Creazione del pulsante per i criteri

4.  **Scegliere una sottoscrizione.** Criteri e domini sono associati a una singola sottoscrizione.

5.  **Selezionare il metodo di bilanciamento del carico con criterio round robin.** Per ulteriori informazioni su vari metodi di bilanciamento del carico disponibili in Gestione traffico, vedere [Panoramica di Gestione traffico di Azure][] e scorrere fino alla sezione "Metodi di bilanciamento del carico in Gestione traffico di Azure".

6.  **Individuare i servizi ospitati e aggiungerli al criterio.** Utilizzare il filtro per individuare i servizi ospitati contenenti la stringa digitata nella casella. Deselezionare la casella per visualizzare tutti i servizi ospitati in produzione per la sottoscrizione selezionata nel passaggio 4. Utilizzare i pulsanti freccia per aggiungerli al criterio. L'ordine nella casella **Selected DNS names** non è rilevante per questo metodo di bilanciamento del carico.

7.  **Configurare il monitoraggio.** Il monitoraggio garantisce che ai servizi ospitati offline non venga inviato traffico. È necessario impostare un percorso e un nome file specifici.
    Pr altre informazioni, vedere [Panoramica di Gestione traffico di Azure][1] e scorrere fino alla sezione "Monitoraggio di servizi ospitati in Gestione traffico di Azure".

8.  **Assegnare un nome al dominio di Gestione traffico.** Assegnare al dominio un nome univoco. È possibile specificare solo il prefisso per il dominio. Non modificare il valore predefinito per Durata (TTL) DNS.
    Per altre informazioni sull'effetto di questa impostazione, vedere [Panoramica di Gestione traffico di Azure][1] e scorrere fino alla sezione "Procedure consigliate per servizi ospitati e criteri in Gestione traffico di Azure".

    La finestra di dialogo **Create Traffic Manager policy** visualizzata dovrebbe avere un aspetto simile a quello riportato di seguito.

    ![Finestra di dialogo per il metodo di bilanciamento del carico con criterio round robin][]

    **Figura 2** - Finestra di dialogo per il metodo di bilanciamento del carico con criterio round robin

9.  **Testare il dominio e il criterio di Gestione traffico.** Per le istruzioni, vedere [Procedura: Testare un criterio di Gestione traffico di Azure][Procedura: Testare un criterio].

10. **Impostare il server DNS in modo che punti al dominio di Gestione traffico.**
     Dopo avere configurato il dominio di Gestione traffico e verificato che funzioni, modificare il record DNS nel server DNS autorevole in modo che il dominio aziendale punti al dominio di Gestione traffico. 
    Per altre informazioni, vedere [Procedura: Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico di Azure][Procedura: Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico].
	Ad esempio, il comando seguente indirizza tutto il traffico diretto a **www.contoso.com** al dominio di Gestione traffico **contoso.trafficmanager.net**:
    `www.contoso.com IN CNAME contoso.trafficmanager.net`


## <span id="howto_create_failover"></span></a>Procedura: Creare un criterio di failover

In genere le organizzazioni desiderano offrire la massima affidabilità per i servizi erogati. A questo scopo, è necessario fornire servizi di backup in caso di inattività del servizio primario. In base a un modello di failover del servizio comune, viene fornito un set di servizi ospitati identici e il traffico viene inviato a un servizio primario, con un elenco di uno o più backup. In questo articolo vengono illustrate le procedure per configurare un criterio di Gestione traffico per implementare questo tipo di backup per il failover.

Per ulteriori informazioni su vari metodi di bilanciamento del carico disponibili in Gestione traffico, vedere [Panoramica di Gestione traffico di Azure][] e scorrere fino alla sezione "Metodi di bilanciamento del carico in Gestione traffico di Azure".

1.  **Distribuire i servizi ospitati** nell'ambiente di produzione. Per ulteriori informazioni sullo sviluppo e la distribuzione di servizi ospitati, vedere [Servizi ospitati di Azure][].

2.  **Accedere all'area Gestione traffico nel portale di gestione** all'indirizzo [][]<http://manage.windowsazure.com></a>. Fare clic su **Virtual Network** nella parte inferiore sinistra delle pagine del portale e quindi scegliere **Gestione traffico** dalle opzioni visualizzate nel pannello sinistro.

3.  **Scegliere Policies e quindi fare clic su "Create".** Scegliere la cartella **Policies** nell'albero di navigazione sinistro per abilitare **Create** nella barra degli strumenti superiore. Scegliere **Create**. Verrà visualizzata la finestra di dialogo **Create Traffic Manager policy**.

    ![Creazione del pulsante per i criteri][]

    **Figura 1** - Creazione del pulsante per i criteri

4.  **Scegliere una sottoscrizione.** Criteri e domini sono associati a una singola sottoscrizione.

5.  **Selezionare il metodo di bilanciamento del carico con criterio di failover.** Per ulteriori informazioni su vari metodi di bilanciamento del carico disponibili in Gestione traffico, vedere [Panoramica di Gestione traffico di Azure][] e scorrere fino alla sezione "Metodi di bilanciamento del carico in Gestione traffico di Azure".

6.  **Individuare i servizi ospitati e aggiungerli al criterio.** Utilizzare il filtro per individuare i servizi ospitati contenenti la stringa digitata nella casella. Deselezionare la casella per visualizzare tutti i servizi ospitati in produzione per la sottoscrizione selezionata nel passaggio 4. Utilizzare i pulsanti freccia per aggiungerli al criterio. Quando si seleziona il metodo di bilanciamento del carico **Failover**, l'ordine dei servizi selezionati è rilevante. Il servizio ospitato primario è il primo. Utilizzare le frecce verso l'alto e verso il basso per modificare l'ordine in base alle esigenze.

7.  Il monitoraggio garantisce che ai servizi ospitati offline non venga inviato traffico. L'utilizzo di un criterio di failover senza configurare il monitoraggio risulta inutile in quanto il traffico verrà indirizzato al servizio ospitato primario anche se tale servizio ospitato è visualizzato come offline. Per monitorare i servizi ospitati, è necessario specificare un nome file e un percorso specifici.
    Per altre informazioni, vedere [Panoramica di Gestione traffico di Azure][1] e scorrere fino alla sezione "Monitoraggio di servizi ospitati in Gestione traffico di Azure".

8.  **Assegnare un nome al dominio di Gestione traffico.** Assegnare al dominio un nome univoco. È possibile specificare solo il prefisso per il dominio. Non modificare il valore predefinito di **Durata (TTL) DNS**.
    Per altre informazioni sull'effetto di questa impostazione, vedere [Panoramica di Gestione traffico di Azure][1] e scorrere fino alla sezione "Procedure consigliate per servizi ospitati e criteri in Gestione traffico di Azure".

    La finestra di dialogo **Create Traffic Manager policy** visualizzata dovrebbe avere un aspetto simile a quello riportato di seguito.

    ![Finestra di dialogo per il metodo di bilanciamento del carico con criterio di failover][]

    **Figura 2** - Finestra di dialogo per il metodo di bilanciamento del carico con criterio di failover

9.  **Testare il dominio e il criterio di Gestione traffico.** Per ulteriori informazioni, vedere [Procedura: Testare un criterio di Gestione traffico di Azure][Procedura: Testare un criterio].

10. **Impostare il server DNS in modo che punti al dominio di Gestione traffico.** Dopo avere configurato il dominio di Gestione traffico e verificato che funzioni, modificare il record DNS nel server DNS autorevole in modo che il dominio aziendale punti al dominio di Gestione traffico.
    Per altre informazioni, vedere [Come impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico][Procedura: Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico].
    Ad esempio, il comando seguente indirizza tutto il traffico diretto a **www.contoso.com** al dominio di Gestione traffico **contoso.trafficmanager.net**
    `www.contoso.com IN CNAME contoso.trafficmanager.net`

## <span id="howto_direct"></span></a>Procedura: Indirizzare il traffico in ingresso ai servizi ospitati in base alle prestazioni della rete

Per bilanciare il carico dei servizi ospitati situati in diversi database dislocati in tutto il mondo, è possibile indirizzare il traffico in ingresso al servizio ospitato più vicino. Sebbene "più vicino" possa corrispondere direttamente alla distanza geografica, potrebbe anche corrispondere alla località con la latenza più bassa per soddisfare la richiesta. Il metodo di bilanciamento del carico basato sulle prestazioni consente di distribuire il carico in base alla posizione e alla latenza, ma non può tenere conto delle modifiche in tempo reale della configurazione della rete o del carico. Per ulteriori informazioni su vari metodi di bilanciamento del carico disponibili in Gestione traffico, vedere [Panoramica di Gestione traffico di Azure][] e scorrere fino alla sezione "Metodi di bilanciamento del carico in Gestione traffico di Azure".

Attenersi alla procedura seguente:

1.  **Distribuire i servizi ospitati** nell'ambiente di produzione. Per altre informazioni, vedere [Creazione di un servizio ospitato per Azure][].
    Fare inoltre riferimento alle "Procedure consigliate per servizi ospitati e criteri" illustrate in [Panoramica di Gestione traffico di Azure][1].

2.  **Accedere all'area Gestione traffico nel portale di gestione** all'indirizzo [][]<http://manage.windowsazure.com></a>. Fare clic su **Virtual Network** nella parte inferiore sinistra delle pagine del portale e quindi scegliere **Gestione traffico** dalle opzioni visualizzate nel pannello sinistro.

3.  **Scegliere Policies e quindi fare clic su "Create".** Scegliere la cartella **Policies** nell'albero di navigazione sinistro per abilitare **Create** nella barra degli strumenti superiore. Scegliere **Create**. Verrà visualizzata la finestra di dialogo **Create Traffic Manager policy**.

    ![Creazione del pulsante per i criteri][]

    **Figura 1** - Creazione del pulsante per i criteri

4.  **Scegliere una sottoscrizione.** Criteri e domini sono associati a una singola sottoscrizione.

5.  **Selezionare il metodo di bilanciamento del carico basato sulle prestazioni.** Per ulteriori informazioni su vari metodi di bilanciamento del carico disponibili in Gestione traffico, vedere [Panoramica di Gestione traffico di Azure][] e scorrere fino alla sezione "Metodi di bilanciamento del carico in Gestione traffico di Azure".

6.  **Individuare i servizi ospitati e aggiungerli al criterio.** Utilizzare il filtro per individuare i servizi ospitati contenenti la stringa digitata nella casella. Deselezionare la casella per visualizzare tutti i servizi ospitati in produzione per la sottoscrizione selezionata nel passaggio 4. Utilizzare i pulsanti freccia per aggiungerli al criterio. L'ordine in **Selected DNS names** non è rilevante per questo metodo di bilanciamento del carico.

7.  **Configurare il monitoraggio.** Il monitoraggio garantisce che ai servizi ospitati offline non venga inviato traffico. È necessario impostare un percorso e un nome file specifici.
    Pr altre informazioni, vedere [Panoramica di Gestione traffico di Azure][1] e scorrere fino alla sezione "Monitoraggio di servizi ospitati in Gestione traffico di Azure".

8.  **Assegnare un nome al dominio di Gestione traffico.** Assegnare al dominio un nome univoco. È possibile specificare solo il prefisso per il dominio. Non modificare il valore predefinito di **Durata (TTL) DNS**.
    Per altre informazioni sull'effetto di questa impostazione, vedere [Panoramica di Gestione traffico di Azure][1] e scorrere fino alla sezione "Procedure consigliate per servizi ospitati e criteri in Gestione traffico di Azure".

    La finestra di dialogo **Create Traffic Manager policy** visualizzata dovrebbe avere un aspetto simile a quello riportato di seguito.

    ![Finestra di dialogo per il metodo di bilanciamento del carico basato sulle prestazioni][]

    **Figura 2** - Finestra di dialogo per il metodo di bilanciamento del carico basato sulle prestazioni

9.  **Testare il dominio e il criterio di Gestione traffico.** Per ulteriori informazioni sull'esecuzione dei test, vedere [Procedura: Testare un criterio di Gestione traffico di Azure][Procedura: Testare un criterio].

10. **Impostare il server DNS in modo che punti al dominio di Gestione traffico.** Dopo avere configurato il dominio di Gestione traffico e verificato che funzioni, modificare il record DNS nel server DNS autorevole in modo che il dominio aziendale punti al dominio di Gestione traffico.
    Ad esempio, il comando seguente indirizza tutto il traffico diretto a **www.contoso.com** al dominio di Gestione traffico **contoso.trafficmanager.net**.
    `www.contoso.com IN CNAME contoso.trafficmanager.net`
    Per altre informazioni, vedere [Procedura: Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico di Azure][Procedura: Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico].

  [Panoramica di Gestione traffico di Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/hh744833.aspx
  [Procedura: Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico]: #howto_point_company
  [Procedura: Testare un criterio]: #howto_test
  [Procedura: Disabilitare temporaneamente criteri e servizi ospitati]: #howto_temp_disable
  [Procedura: Modificare un criterio]: #howto_edit_policy
  [Procedura: Bilanciare il carico del traffico in modo uniforme tra un set di servizi ospitati]: #howto_load_balance
  [Procedura: Creare un criterio di failover]: #howto_create_failover
  [Procedura: Indirizzare il traffico in ingresso ai servizi ospitati in base alle prestazioni della rete]: #howto_direct
  [Posizione dell'indirizzo IP del servizio ospitato.]: ./media/traffic-manager-configure-settings/hosted_service_IP_location.png
  [Esempio di comando nslookup]: ./media/traffic-manager-configure-settings/nslookup_command_example.png
  [1]: http://msdn.microsoft.com/en-us/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring
  []: http://manage.windowsazure.com
  [Servizi ospitati di Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Creazione del pulsante per i criteri]: ./media/traffic-manager-configure-settings/Create_button_for_policies.png
  [Finestra di dialogo per il metodo di bilanciamento del carico con criterio round robin]: ./media/traffic-manager-configure-settings/Dialog_box_for_Round_Robin_load_balancing_method.png
  [Finestra di dialogo per il metodo di bilanciamento del carico con criterio di failover]: ./media/traffic-manager-configure-settings/Dialog_box_for_Failover_load_balancing_method.png
  [Creazione di un servizio ospitato per Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg432967.aspx
  [Finestra di dialogo per il metodo di bilanciamento del carico basato sulle prestazioni]: ./media/traffic-manager-configure-settings/Dialog_box_for_Performance_load_balancing_method.png
