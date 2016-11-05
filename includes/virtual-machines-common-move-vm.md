

## Usare il portale per spostare una macchina virtuale in una sottoscrizione diversa
È possibile spostare una macchina virtuale e le relative risorse associate in una sottoscrizione diversa usando il portale.

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Fare clic su **Esplora** > **Macchine virtuali** e selezionare la VM da spostare dall'elenco.
   
    ![Schermata della sezione Essentials in cui si fa clic sull'icona a forma di matita per aprire il pannello Sposta risorse.](./media/virtual-machines-common-move-vm/move-button.png)  
3. Nella sezione **Informazioni di base** fare clic sull'icona a forma di matita **Cambia sottoscrizione** accanto al nome della sottoscrizione. Si aprirà il pannello **Sposta risorse**.
   
    ![Schermata del pannello Sposta risorse.](./media/virtual-machines-common-move-vm/move.png)
4. Selezionare ognuna delle risorse da spostare. Nella maggior parte dei casi, è necessario spostare tutte le risorse facoltative elencate.
5. Selezionare la **Sottoscrizione** in cui si vuole spostare la VM.
6. Selezionare un **Gruppo di risorse** esistente oppure digitare un nome per crearne uno nuovo.
7. Al termine, indicare che si è compreso che verranno creati nuovi ID risorsa da usare con la VM dopo lo spostamento, quindi fare clic su **OK**.

## Usare il portale per spostare una macchina virtuale in un altro gruppo di risorse
È possibile spostare una macchina virtuale e le relative risorse associate in un altro gruppo di risorse usando il portale.

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Fare clic su **Esplora** > **Gruppi di risorse** e selezionare il gruppo di risorse che contiene la macchina virtuale.
3. Nel pannello **Gruppo di risorse** selezionare **Sposta** dal menu.
   
    ![Screenshot del pulsante Sposta nel menu Gruppi di risorse.](./media/virtual-machines-common-move-vm/move-rg.png)  
4. Nel pannello **Sposta risorse** selezionare le risorse da spostare e quindi immettere il nome di un gruppo di risorse esistente o scegliere di creare un nuovo gruppo di risorse. Al termine, indicare che si è compreso che verranno creati nuovi ID risorsa da usare con la VM dopo lo spostamento, quindi fare clic su **OK**
   
    ![Schermata del pannello Sposta risorse.](./media/virtual-machines-common-move-vm/move-rg-list.png)

<!---HONumber=AcomDC_0810_2016-->