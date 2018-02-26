## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

Questa sezione illustra la configurazione dell'ambiente di sviluppo. Include la creazione di un'app ASP.NET MVC, l'aggiunta di una connessione di Servizi connessi, l'aggiunta di un controller e la specifica delle direttive degli spazi dei nomi necessarie.

### <a name="create-an-aspnet-mvc-app-project"></a>Creare un progetto di app ASP.NET MVC

1. Aprire Visual Studio.

1. Nel menu principale, selezionare **File** > **Nuovo** > **Progetto**.

1. Nella finestra di dialogo **Nuovo progetto** selezionare **Web** > **Applicazione Web ASP.NET (.NET Framework)**. Nel campo **Nome** specificare **StorageAspNet**. Selezionare **OK**.

    ![Screenshot della finestra di dialogo Nuovo progetto](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Nella finestra di dialogo **Nuova applicazione Web ASP.NET** selezionare **MVC**, quindi selezionare **OK**.

    ![Screenshot della finestra di dialogo Nuova applicazione Web ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Usare Servizi connessi per connettersi a un account di archiviazione di Azure

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto.

2. Nel menu di scelta rapida selezionare **Aggiungi** > **Servizio connesso**.

1. Nella finestra di dialogo **Servizi connessi** selezionare **Archiviazione cloud con Archiviazione di Azure**, quindi selezionare **Configura**.

    ![Screenshot della finestra di dialogo Servizi connessi](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Nella finestra di dialogo **Archiviazione di Azure** selezionare l'account di archiviazione di Azure da usare per questa esercitazione. Per creare un nuovo account di archiviazione di Azure, selezionare **Crea un nuovo Account di archiviazione** e completare il modulo. Dopo aver selezionato un account di archiviazione esistente o averne creato uno nuovo, selezionare **Aggiungi**. Visual Studio installa il pacchetto NuGet per Archiviazione di Azure e una stringa di connessione di archiviazione a **Web.config**.

> [!TIP]
> Per informazioni su come creare un account di archiviazione con il [portale di Azure](https://portal.azure.com), vedere [Creare un account di archiviazione](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Si può creare un account di archiviazione anche tramite [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), l'[interfaccia della riga di comando di Azure](../articles/storage/common/storage-azure-cli.md) o [Azure Cloud Shell](../articles/cloud-shell/overview.md).

