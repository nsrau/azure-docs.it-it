
In questo articolo viene illustrato come spostare una VM da una sottoscrizione all'altra. Questa procedura può essere pratica se in origine è stata creata una VM in una sottoscrizione personale e ora si desidera spostarla alla sottoscrizione dell'azienda per continuare il lavoro.

> [AZURE.NOTE] Nell'ambito dello spostamento verranno creati nuovi ID risorsa. Una volta spostata la VM, sarà necessario aggiornare strumenti e script in modo che utilizzino i nuovi ID risorsa.

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Fare clic su **Sfoglia** > **Macchine virtuali** e selezionare la VM da spostare dall'elenco.
	
	![Schermata della sezione Essentials in cui si fa clic sull'icona a forma di matita per aprire il pannello Sposta risorse.](./media/virtual-machines-common-move-vm/move-button.png)
	
3. Nella sezione **Essentials**, fare clic sull'icona **Cambia sottoscrizione** (a forma di matita) accanto al nome della sottoscrizione. Si aprirà il pannello **Sposta risorse**.
	
	![Schermata del pannello Sposta risorse.](./media/virtual-machines-common-move-vm/move.png)
	
4. Selezionare ognuna delle risorse da spostare. Nella maggior parte dei casi, è necessario spostare tutte le risorse facoltative elencate.
5. Selezionare la **sottoscrizione** in cui si desidera spostare la VM.
6. Selezionare un **gruppo di risorse** esistente oppure digitare un nome per crearne uno nuovo.
7. Al termine, indicare che si è compreso che verranno creati nuovi ID risorsa da usare con la VM una volta spostata, quindi fare clic su **OK**.



## Passaggi successivi

È possibile spostare molti tipi diversi di risorse tra gruppi di risorse e sottoscrizioni. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../articles/resource-group-move-resources.md).

<!---HONumber=AcomDC_0706_2016-->