<!--author=alkohli last changed:02/10/2017-->


#### <a name="to-create-a-new-service"></a>Per creare un nuovo servizio

1. Usare le credenziali dell'account Microsoft per accedere al [portale di Azure](https://portal.azure.com/).

2. Nel Portale di Azure fare clic su **+** e quindi nel marketplace fare clic su **Vedi tutto**.

    ![Creare Gestione dispositivi StorSimple](./media/storsimple-8000-create-new-service/createssdevman1.png)

    Cercare _StorSimple Physical_ (Dispositivi fisici StorSimple). Selezionare e fare clic su **Serie di dispositivi fisici StorSimple** e quindi fare clic su **Crea**. In alternativa, nel portale di Azure fare clic su **+** e quindi in **Archiviazione** fare clic su **Serie di dispositivi fisici StorSimple**.

    ![Creare Gestione dispositivi StorSimple](./media/storsimple-8000-create-new-service/createssdevman11.png)

3. Nel pannello **Gestione dispositivi StorSimple** seguire questa procedura:
   
   1. Specificare un **Nome della risorsa** univoco per il servizio. Si tratta di un nome descrittivo che può essere usato per identificare il servizio. Il nome può contenere da 2 a 50 caratteri che possono essere lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero.

   2. Scegliere una **Sottoscrizione** dall'elenco a discesa. La sottoscrizione viene collegata all'account di fatturazione. Questo campo non è presente se si dispone di una sola sottoscrizione.

   3. Per **Gruppo di risorse**, selezionare **Usa esistente** o **Crea nuovo**. Per altre informazioni, vedere [Azure resource groups](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/) (Gruppi di risorse di Azure).
   
   4. Fornire una **Località** per il servizio. In generale, scegliere un percorso più vicino all'area geografica in cui si vuole distribuire il dispositivo. È anche possibile tenere in considerazione quanto segue: 
      
      * Se si dispone di carichi di lavoro esistenti in Azure che si intende distribuire con il dispositivo StorSimple, usare tale data center.
      * Il servizio Gestione dispositivi StorSimple e la risorsa di archiviazione di Azure possono trovarsi in due posizioni distinte. In tal caso, è necessario creare l'account di Gestione dispositivi StorSimple e di Archiviazione di Azure separatamente. Per creare un account di archiviazione di Azure, passare al servizio Archiviazione di Azure nel portale di Azure e seguire la procedura descritta in [Creare un account di archiviazione](../articles/storage/storage-create-storage-account.md#create-a-storage-account). Dopo aver creato l'account, aggiungerlo al servizio Gestione dispositivi StorSimple seguendo la procedura descritta in [Configurare un nuovo account di archiviazione per il servizio](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#configure-a-new-storage-account-for-the-service).

   5. Selezionare **Crea un nuovo account di archiviazione** per creare automaticamente un account di archiviazione con il servizio. Specificare un nome per questo account di archiviazione. Se è necessario che i dati siano in un percorso diverso, deselezionare questa casella.

   6. Selezionare **Aggiungi al dashboard** se si vuole inserire un collegamento rapido al servizio nel dashboard.
      
   7. Fare clic su **Crea** per creare il servizio Gestione dispositivi StorSimple.

       ![Creare Gestione dispositivi StorSimple](./media/storsimple-8000-create-new-service/createssdevman2.png)
   
La creazione del servizio richiede alcuni minuti. Al termine della creazione del servizio, verrà visualizzata una notifica e verrà aperto il pannello del nuovo servizio.
   
![Creare Gestione dispositivi StorSimple](./media/storsimple-8000-create-new-service/createssdevman5.png)


