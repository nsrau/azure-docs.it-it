Per usare il servizio di archiviazione di Azure, è necessario un account di archiviazione. Per
creare un account di archiviazione, attenersi alla procedura seguente. È anche possibile
creare un account di archiviazione usando la libreria client di Gestione servizi di Azure o Gestione servizi [API REST].

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Nella parte inferiore del pannello di navigazione fare clic su **NEW**.

    ![+new][+new]

3.  Fare clic su **DATA SERVICES**, quindi su **STORAGE** e infine su **QUICK CREATE**.

    ![Finestra di dialogo Creazione rapida][Finestra di dialogo Creazione rapida]

4.  Nel campo URL digitare un nome di sottodominio da usare nell'URI per l'
    account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole
    e numeri. Questo valore diventa il nome host all'interno dell'URI
    usato per fare riferimento a risorse BLOB, di accodamento o tabelle per la
    sottoscrizione.

5.  Scegliere una regione o un gruppo di affinità per l'
    archiviazione. Se si userà l'archiviazione dalla propria applicazione
    Azure, selezionare la stessa area in cui verrà distribuita l'
    applicazione.

6.  Facoltativamente, è possibile scegliere il tipo di replica desiderato per l'account. Per impostazione predefinita, è selezionata la replica con ridondanza geografica che offre la massima durabilità. Per informazioni dettagliate sulle opzioni di replica, vedere [Opzioni di ridondanza di Archiviazione di Azure][Opzioni di ridondanza di Archiviazione di Azure] e il [Blog del team di Archiviazione di Azure][Blog del team di Archiviazione di Azure]

7.  Fare clic su **CREATE STORAGE ACCOUNT**.

  [portale di gestione di Azure]: http://manage.windowsazure.com
  [+new]: ./media/create-storage-account/plus-new.png
  [Finestra di dialogo Creazione rapida]: ./media/create-storage-account/quick-storage-2.png
  [Opzioni di ridondanza di Archiviazione di Azure]: http://msdn.microsoft.com/it-it/library/azure/dn727290.aspx
  [Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
