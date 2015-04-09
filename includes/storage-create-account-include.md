## Creare un account di archiviazione di Azure

Per usare il servizio di archiviazione di Azure, è necessario un account di archiviazione. Per creare un account di archiviazione, attenersi alla procedura seguente. È anche possibile creare un account di archiviazione usando la libreria client di Gestione servizi di Azure o Gestione servizi [API REST].

1.  Accedere al [Portale di gestione di Azure].

2.  Nella parte inferiore del riquadro di spostamento fare clic su **NUOVO**.

	![+new][plus-new]

3.  Fare clic su **SERVIZI DATI**, quindi su **ARCHIVIAZIONE** e infine su **CREAZIONE RAPIDA**.

	![Quick create dialog][quick-create-storage]

4.  Nel campo URL digitare un nome di sottodominio da usare nell'URI per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo valore diventa il nome host all'interno dell'URI usato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.

5.  Scegliere un'area o un gruppo di affinità per l'archiviazione. Se si userà l'archiviazione dalla propria applicazione Azure, selezionare la stessa area in cui verrà distribuita Hello World.

6. Facoltativamente, è possibile scegliere il tipo di replica desiderato per l'account. Per impostazione predefinita, è selezionata la replica con ridondanza geografica che offre la massima durabilità. Per informazioni dettagliate sulle opzioni di replica, vedere [Opzioni di ridondanza di Archiviazione di Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx) e il [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/).

6.  Fare clic su **CREA ACCOUNT DI ARCHIVIAZIONE**.

[API REST]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Portale di gestione di Azure]: http://manage.windowsazure.com
[plus-new]: ./media/storage-create-account-include/plus-new.png
[quick-create-storage]: ./media/storage-create-account-include/quick-storage-2.png

<!--HONumber=49-->