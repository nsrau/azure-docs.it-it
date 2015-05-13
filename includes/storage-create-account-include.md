## Creare un account di archiviazione di Azure

Per usare il servizio di archiviazione di Azure, è necessario un account di archiviazione. Per creare un account di archiviazione, attenersi alla procedura riportata di seguito \(è anche possibile creare un account di archiviazione utilizzando la libreria client di gestione dei servizi di Azure o l’[API REST] di gestione dei servizi\).

1.  Accedere al [portale di gestione di Azure].

2.  Nella parte inferiore del pannello di navigazione fare clic su **NEW**.

	![\+nuovo][plus-new]

3.  Fare clic su **DATA SERVICES**, quindi su **STORAGE** e infine su **QUICK CREATE**.

	![Finestra di dialogo Creazione rapida][quick-create-storage]

4.  Nel campo URL, digitare un nome di sottodominio da utilizzare nell'URI per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo valore diventa il nome host all'interno dell'URI utilizzato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.

5.  Scegliere una regione o un gruppo di affinità in cui situare l'archiviazione. Se si utilizzerà l'archiviazione dalla propria applicazione Azure, selezionare la stessa area in cui verrà distribuita l'applicazione.

6. Facoltativamente, è possibile scegliere il tipo di replica desiderato per l'account. Per impostazione predefinita, è selezionata la replica con ridondanza geografica che offre la massima durabilità. Per informazioni dettagliate sulle opzioni di replica, vedere [Opzioni di ridondanza di Archiviazione di Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx) e il [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)

6.  Fare clic su **CREATE STORAGE ACCOUNT**.

[API REST]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[portale di gestione di Azure]: http://manage.windowsazure.com
[plus-new]: ./media/storage-create-account-include/plus-new.png
[quick-create-storage]: ./media/storage-create-account-include/quick-storage-2.png

<!--HONumber=52-->
