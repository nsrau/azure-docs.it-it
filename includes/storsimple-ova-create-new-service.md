#### Per creare un nuovo servizio

1.  Usare le credenziali dell'account Microsoft per accedere al portale di Azure classico all'URL seguente: [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

2.  Nel portale, fare clic su **Nuovo > Servizi dati > StorSimple Manager > Creazione rapida**.

3.  Nel modulo visualizzato, procedere come segue:

	1.  Fornire un **Nome** univoco per il servizio. Si tratta di un nome descrittivo che può essere utilizzato per identificare il servizio. Il nome può contenere da 2 a 50 caratteri che possono essere lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero.

	2.  Per il servizio di gestione di un dispositivo virtuale StorSimple, dall'elenco a discesa **Tipo dei dispositivi gestiti**, scegliere **Serie di dispositivi virtuali**.

	3.  Fornire una **Località** per il servizio. Per Località si intende l'area geografica in cui si desidera distribuire il dispositivo.

	 -   Se si dispone di altri carichi di lavoro in Azure che si intende distribuire con il dispositivo StorSimple, si consiglia l'uso di quel data center.

   	 -   StorSimple Manager e Archiviazione di Azure possono trovarsi in due posizioni distinte. In tal caso, è necessario creare il servizio StorSimple Manager e l'account di archiviazione di Azure separatamente. Per creare un account Archiviazione di Azure, andare al servizio Archiviazione di Azure nel portale e seguire i passaggi relativi alla [creazione di un account di Archiviazione di Azure](storage-create-storage-account.md#create-a-storage-account). Dopo aver creato questo account, aggiungerlo al servizio StorSimple Manager seguendo i passaggi relativi alla [configurazione di un nuovo account di archiviazione per il servizio](#optional-step-configure-a-new-storage-account-for-the-service).
   	 
   	 	
	1.  Scegliere una **Sottoscrizione** dall'elenco a discesa. La sottoscrizione viene collegata all'account di fatturazione. Questo campo non è presente se si dispone di una sola sottoscrizione.

	1.  Selezionare **Crea un nuovo account di archiviazione di Azure** per creare automaticamente un account di archiviazione con il servizio. Questo account di archiviazione avrà un nome speciale, ad esempio "storsimplebwv8c6dcnf". Se è necessario che i dati siano in una posizione diversa, deselezionare questa casella di controllo.

	1.  Fare clic su **Crea StorSimple Manager** per creare il servizio.

		![](./media/storsimple-ova-create-new-service/image1m-include.png)

	Si verrà indirizzati alla pagina di destinazione del **Servizio**. La creazione del servizio richiederà alcuni minuti. Dopo aver creato correttamente il servizio, si riceverà la relativa notifica.

	![](./media/storsimple-ova-create-new-service/image2-include.png)

	Lo stato del servizio si trasformerà in **Attivo**.

<!---HONumber=AcomDC_0309_2016-->