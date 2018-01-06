## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

In questa sezione viene illustrato come configurare l'ambiente di sviluppo. Ciò include la creazione di un'applicazione MVC ASP.NET, aggiunta di una connessione di servizi connessi, aggiunta di un controller e che specifica le direttive dello spazio dei nomi obbligatorio.

### <a name="create-an-aspnet-mvc-app-project"></a>Creare un progetto di app ASP.NET MVC

1. Aprire Visual Studio.

1. Nel menu principale, selezionare **File** > **New** > **progetto**.

1. Nel **nuovo progetto** nella finestra di dialogo **Web** > **applicazione Web ASP.NET (.NET Framework)**. Nel **nome** , specificare **StorageAspNet**. Selezionare **OK**.

    ![La finestra di dialogo di schermata del nuovo progetto](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Nel **nuova applicazione Web ASP.NET** nella finestra di dialogo **MVC**, quindi selezionare **OK**.

    ![La finestra di dialogo di schermata della nuova applicazione Web ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Utilizzare i servizi connessi per connettersi a un account di archiviazione di Azure

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto.

2. Dal menu di scelta rapida, selezionare **Aggiungi** > **servizio connesso**.

1. Nel **servizi connessi** nella finestra di dialogo **archiviazione Cloud con l'archiviazione di Azure**, quindi selezionare **configura**.

    ![La finestra di dialogo schermata di servizi connessi](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Nel **di archiviazione di Azure** nella finestra di dialogo account di archiviazione di Azure da utilizzare per questa esercitazione. Per creare un nuovo account di archiviazione di Azure, selezionare **creare un nuovo Account di archiviazione**e completare il modulo. Dopo aver selezionato un account archiviazione esistente o crearne uno nuovo, selezionare **Aggiungi**. Visual Studio installa il pacchetto NuGet per l'archiviazione di Azure e una stringa di connessione di archiviazione per **Web. config**.

> [!TIP]
> Per informazioni su come creare un account di archiviazione con il [portale di Azure](https://portal.azure.com), vedere [creare un account di archiviazione](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> È anche possibile creare un account di archiviazione tramite [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [CLI di Azure](../articles/storage/common/storage-azure-cli.md), o [Azure Cloud Shell](../articles/cloud-shell/overview.md).

