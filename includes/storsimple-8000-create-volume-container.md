<!--author=alkohli last changed: 06/22/17-->

#### <a name="to-create-a-volume-container"></a>Per creare un contenitore di volumi
1. Passare al servizio Gestione dispositivi StorSimple e fare clic su **Dispositivi**. Nell'elenco tabulare dei dispositivi selezionare e fare clic su un dispositivo. 

    ![Pannello Contenitore del volume](./media/storsimple-8000-create-volume-container/createvolumecontainer1.png)

2. Nel dashboard del dispositivo fare clic su **+ Aggiungi contenitore del volume**

    ![Pannello Contenitore del volume](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

3. Nel pannello **Aggiungi contenitore del volume**:
   
   1. Il dispositivo viene selezionato automaticamente.
   2. Fornire un **Nome** per il contenitore di volumi. Il nome deve essere lungo da 3 a 32 caratteri. Non è possibile rinominare un contenitore del volume dopo che è stato creato.
   3. Selezionare **Abilita crittografia archiviazione cloud** per attivare la crittografia dei dati inviati dal dispositivo al cloud.
   4. Fornire e confermare una **Chiave di crittografia archiviazione cloud** lunga da 8 a 32 caratteri. Questa chiave viene utilizzata dal dispositivo per accedere ai dati crittografati.
   5. Selezionare un **Account di archiviazione** da associare a questo contenitore di volumi. È possibile scegliere un account di archiviazione predefinito o l'account predefinito generato al momento della creazione del servizio. È inoltre possibile utilizzare l’opzione **Aggiungi nuovo** per specificare un account di archiviazione non collegato a questa sottoscrizione al servizio.
   6. Selezionare **Illimitata** nell'elenco a discesa **Specifica larghezza di banda** se si desidera utilizzare tutta la larghezza di banda disponibile. È anche possibile impostare questa opzione su **Personalizzata** per utilizzare i controlli della larghezza di banda e specificare un valore compreso tra 1 Mbps e 1.000 Mbps.
      Se si dispone delle informazioni sulla larghezza di banda, è possibile allocare la larghezza di banda in base a una pianificazione specificando **Seleziona un modello di larghezza di banda**. Per una procedura dettagliata, vedere [Aggiunta di un modello di larghezza di banda](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template).

      ![Pannello Contenitore del volume](./media/storsimple-8000-create-volume-container/createvolumecontainer6b.png)
   7. Fare clic su **Crea**.

        ![Pannello Contenitore del volume](./media/storsimple-8000-create-volume-container/createvolumecontainer6.png)
   
       Dopo la creazione del contenitore del volume si riceve una notifica.

       ![Notifica di creazione del contenitore del volume](./media/storsimple-8000-create-volume-container/createvolumecontainer8.png)

   Il nuovo contenitore del volume creato viene visualizzato nell'elenco di contenitori dei volumi per il dispositivo.

   ![Pannello Aggiungi contenitore del volume](./media/storsimple-8000-create-volume-container/createvolumecontainer9.png)


