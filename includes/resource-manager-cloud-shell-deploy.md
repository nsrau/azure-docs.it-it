## <a name="deploy-template-from-cloud-shell"></a>Distribuire il modello da Cloud Shell

È possibile usare [Cloud Shell](../articles/cloud-shell/overview.md) per distribuire il modello. Tuttavia, è prima necessario caricare il modello nella condivisione file per Cloud Shell. Per informazioni sulla configurazione di Cloud Shell per il primo utilizzo, vedere [Panoramica di Azure Cloud Shell](../articles/cloud-shell/overview.md).

1. Accedere al [Portale di Azure](https://portal.azure.com).

1. Selezionare il gruppo di risorse di Cloud Shell. Il modello del nome è `cloud-shell-storage-<region>`.

   ![Selezionare il gruppo di risorse](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Selezionare l'account di archiviazione per Cloud Shell.

   ![Selezionare l'account di archiviazione](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Selezionare **File**.

   ![Selezione dei file](./media/resource-manager-cloud-shell-deploy/select-files.png)

1. Selezionare la condivisione file per Cloud Shell. Il modello del nome è `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Selezionare la condivisione file](./media/resource-manager-cloud-shell-deploy/select-file-share.png)

1. Selezionare **Aggiungi directory**.

   ![Aggiungi directory](./media/resource-manager-cloud-shell-deploy/select-add-directory.png)

1. Assegnare il nome **templates** e scegliere **OK**.

   ![Assegnare il nome alla directory](./media/resource-manager-cloud-shell-deploy/name-templates.png)

1. Selezionare la nuova directory.

   ![Selezionare la directory](./media/resource-manager-cloud-shell-deploy/select-templates.png)

1. Selezionare **Carica**.

   ![Selezionare Carica](./media/resource-manager-cloud-shell-deploy/select-upload.png)

1. Trovare e caricare il modello.

   ![Caricare il file](./media/resource-manager-cloud-shell-deploy/upload-files.png)

1. Aprire il prompt.

   ![Aprire Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
