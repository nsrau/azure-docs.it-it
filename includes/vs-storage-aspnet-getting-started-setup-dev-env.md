## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

Questa sezione illustra la configurazione dell'ambiente di sviluppo, incluse la creazione di un'app ASP.NET MVC, l'aggiunta di una connessione di Servizi connessi, l'aggiunta di un controller e la specifica delle direttive degli spazi dei nomi necessarie.

### <a name="create-an-aspnet-mvc-app-project"></a>Creare un progetto di app ASP.NET MVC

1. Aprire Visual Studio.

1. Scegliere **File->Nuovo->Progetto** dal menu principale.

1. Nella finestra di dialogo **Nuovo progetto** specificare le opzioni evidenziate nella figura seguente:

    ![Creazione di un progetto ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Selezionare **OK**.

1. Nella finestra di dialogo **Nuovo progetto ASP.NET** specificare le opzioni evidenziate nella figura seguente:

    ![Specificare MVC](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

1. Selezionare **OK**.

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Usare Servizi connessi per connettersi a un account di archiviazione di Azure

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi > Servizi connessi** dal menu di scelta rapida.

1. Nella finestra di dialogo **Aggiungi servizio connesso** scegliere **Archiviazione di Azure**, quindi fare clic su **Configura**.

    ![Finestra di dialogo Servizi connessi](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Nella finestra di dialogo **Archiviazione di Azure** selezionare l'account di archiviazione di Azure desiderato e selezionare **Aggiungi**.


<!--HONumber=Dec16_HO3-->


