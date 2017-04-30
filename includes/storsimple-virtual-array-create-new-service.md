#### <a name="to-create-a-new-service"></a>Per creare un nuovo servizio

1.  Usare le credenziali dell'account Microsoft per accedere al portale di Azure all'URL seguente: <https://portal.azure.com/>. Se si esegue la distribuzione del dispositivo nel portale per enti pubblici, accedere a: <https://portal.azure.us/>

2.  Nel portale di Azure fare clic su **+ Nuovo** &gt; **Archiviazione** &gt; **Serie di dispositivi virtuali StorSimple**.

    ![Creare un nuovo servizio](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  Nel pannello **Gestione dispositivi StorSimple** visualizzato eseguire queste operazioni:

    1.  Specificare un **Nome della risorsa** univoco per il servizio. Il nome della risorsa è un nome descrittivo che può essere usato per identificare il servizio. Il nome può contenere da 2 a 50 caratteri che possono essere lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero.

    2.  Scegliere una **Sottoscrizione** dall'elenco a discesa. La sottoscrizione viene collegata all'account di fatturazione. Questo campo non è presente se si dispone di una sola sottoscrizione.

    3.  Per **Gruppo di risorse** selezionare un gruppo esistente o creare un nuovo gruppo. Per altre informazioni, vedere [Azure resource groups](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/) (Gruppi di risorse di Azure).

    4.  Fornire una **Località** per il servizio. Per altre informazioni sui servizi disponibili in aree specifiche, vedere [Aree di Azure](https://azure.microsoft.com/regions/#services) . In generale, scegliere la **posizione** più vicina all'area geografica in cui si vuole distribuire il dispositivo. È inoltre possibile tenere in considerazione quanto segue:

        -   Se sono presenti altri carichi di lavoro in Azure che si intende distribuire con il dispositivo StorSimple, è consigliabile usare il data center specifico.

        -   Gestione dispositivi StorSimple e Archiviazione di Azure possono trovarsi in due posizioni distinte. In tal caso, è necessario creare l'account di Gestione dispositivi StorSimple e di Archiviazione di Azure separatamente. Per creare un account di archiviazione di Azure, passare al servizio Archiviazione di Azure nel portale di Azure e seguire la procedura descritta in [Creare un account di archiviazione](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account). Dopo aver creato l'account, aggiungerlo al servizio Gestione dispositivi StorSimple seguendo la procedura descritta in [Configurare un nuovo account di archiviazione per il servizio](https://azure.microsoft.com/en-us/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service).

        -   Se si esegue la distribuzione del dispositivo virtuale nel portale per enti pubblici, il servizio Gestione dispositivi StorSimple è disponibile nelle località statunitensi dell'Iowa e della Virginia.

    5.  Selezionare **Crea un nuovo account di archiviazione di Azure** per creare automaticamente un account di archiviazione con il servizio. Specificare un **Nome dell'account di archiviazione**. Se è necessario che i dati siano in un percorso diverso, deselezionare questa casella.

    6.  Selezionare **Aggiungi al dashboard** se si vuole inserire un collegamento rapido al servizio nel dashboard.

    7.  Fare clic su **Crea** per creare il servizio Gestione dispositivi StorSimple.

        ![Creare un nuovo servizio](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Si viene indirizzati alla pagina di destinazione **Servizio**. La creazione del servizio richiede alcuni minuti. Dopo che il servizio è stato creato, l'utente verrà informato in modo appropriato e lo stato del servizio verrà modificato in **Attivo**.


