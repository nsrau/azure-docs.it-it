## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

Questa sezione vengono illustrate l'impostazione dell'ambiente di sviluppo, tra cui la creazione di un'applicazione MVC ASP.NET, aggiunta di una connessione di servizi connessi, aggiunta di un controller e che specifica le direttive dello spazio dei nomi obbligatorio.

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

1. Nel **Aggiungi servizio connesso** finestra di dialogo Seleziona **archiviazione Cloud con l'archiviazione di Azure**, quindi selezionare **configura**.

    ![Finestra di dialogo Servizi connessi](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Nel **di archiviazione di Azure** finestra di dialogo, Seleziona account di archiviazione di Azure da utilizzare per questa esercitazione.  Per creare un nuovo account di archiviazione di Azure, fare clic su **creare un nuovo Account di archiviazione** e completare il modulo.  Dopo aver selezionato un account archiviazione esistente o crearne uno nuovo, fare clic su **Aggiungi**.  Visual Studio verrà installato il pacchetto NuGet per l'archiviazione di Azure e una stringa di connessione di archiviazione per **Web. config**.

> [!TIP]
> Per informazioni su come creare un account di archiviazione con il [portale di Azure](https://portal.azure.com), vedere [creare un account di archiviazione](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Un account di archiviazione di Azure può essere creato anche usando [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [CLI di Azure](../articles/storage/common/storage-azure-cli.md), o [Azure Cloud Shell](../articles/cloud-shell/overview.md).

