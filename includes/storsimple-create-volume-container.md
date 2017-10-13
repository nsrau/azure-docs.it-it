<!--author=SharS last changed: 9/17/15-->

#### <a name="to-create-a-volume-container"></a>Per creare un contenitore di volumi
1. Nella pagina **Avvio rapido** del dispositivo fare clic su **Aggiungere un contenitore di volumi**. Verrà visualizzata la finestra di dialogo **Crea contenitore di volumi** .
   
    ![Crea contenitore di volumi](./media/storsimple-create-volume-container/HCS_CreateVolumeContainerM-include.png)
2. Nella finestra di dialogo **Crea contenitore di volumi** :
   
   1. Fornire un **Nome** per il contenitore di volumi. Il nome deve essere lungo da 3 a 32 caratteri.
   2. Selezionare un **Account di archiviazione** da associare a questo contenitore di volumi. È possibile scegliere l'account predefinito generato al momento della creazione del servizio. È inoltre possibile utilizzare l’opzione **Aggiungi nuovo** per specificare un account di archiviazione non collegato a questa sottoscrizione al servizio.
   3. Selezionare **Abilita crittografia archiviazione cloud** per attivare la crittografia dei dati inviati dal dispositivo al cloud.
   4. Fornire e confermare una **Chiave di crittografia archiviazione cloud** lunga da 8 a 32 caratteri. Questa chiave viene utilizzata dal dispositivo per accedere ai dati crittografati.
   5. Selezionare **Illimitata** nell'elenco a discesa **Specifica larghezza di banda** se si desidera utilizzare tutta la larghezza di banda disponibile. È inoltre possibile impostare questa opzione su **Personalizzata** per utilizzare i controlli della larghezza di banda e specificare un valore compreso tra 1 e 1.000 Mbps. 
      Se si dispone delle informazioni sulla larghezza di banda, è possibile allocare la larghezza di banda in base a una pianificazione specificando **Seleziona un modello di larghezza di banda**. Per una procedura dettagliata, vedere [Aggiunta di un modello di larghezza di banda](../articles/storsimple/storsimple-manage-bandwidth-templates.md#add-a-bandwidth-template).
   6. Fare clic sull'icona del segno di spunta  ![icona del segno di spunta](./media/storsimple-create-volume-container/HCS_CheckIcon-include.png) per salvare questo contenitore di volumi e chiudere la procedura guidata. 
   
   Il contenitore di volumi appena creato verrà elencato nella pagina **Contenitori di volumi** .

![Video disponibile](./media/storsimple-create-volume-container/Video_icon.png)**Video disponibile**

Per guardare un video che illustra come creare un nuovo contenitore di volumi per la soluzione StorSimple, fare clic [qui](https://azure.microsoft.com/documentation/videos/create-a-volume-container-in-your-storsimple-solution/).

