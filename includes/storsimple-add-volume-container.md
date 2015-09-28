<!--author=SharS last changed: 9/16/15-->

#### Per aggiungere un contenitore di volumi

1. Nella pagina **Dispositivi**, selezionare il dispositivo facendo doppio clic, quindi selezionare la scheda **Contenitori di volumi**.

2. Fare clic su **Aggiungi** nella parte inferiore della pagina. Nella finestra di dialogo **Crea contenitore di volumi**, effettuare le seguenti operazioni:

  1. Fornire un **Nome** univoco per il contenitore di volumi. Questo nome può contenere un massimo di 32 caratteri.
  2. Selezionare un **Account di archiviazione** da associare a questo contenitore di volumi. È possibile scegliere un account di archiviazione esistente all'interno della stessa sottoscrizione o selezionare **Aggiungi altri** per selezionare un account di archiviazione da un'altra sottoscrizione. Inoltre, è possibile scegliere l'account di archiviazione generato inizialmente quando il servizio è stato creato.
  3. Specificare una larghezza di banda **Illimitata** se si vuole usare tutta la larghezza di banda disponibile o **Personalizzata** per usare i controlli della larghezza di banda. Per una larghezza di banda personalizzata, è necessario fornire un valore compreso tra 1 e 1000 Mbps. Per allocare una larghezza di banda in base a una pianificazione è possibile selezionare **Seleziona un modello di larghezza di banda**.
  4. È consigliabile mantenere **Abilita crittografia di archiviazione cloud** selezionato per crittografare i dati che verranno inseriti nel cloud. Disabilitare la crittografia solo se si usano altri mezzi per crittografare i dati.
  5. Fornire una **chiave di crittografia di archiviazione cloud** contenente da 8 a 32 caratteri. Il dispositivo usa questa chiave per accedere ai dati crittografati. Nel campo **Conferma chiave di crittografia di archiviazione cloud** immettere la chiave di crittografia di archiviazione cloud per confermarla.
  6. Fare clic sulla freccia per passare alla pagina successiva.

    ![Creare il contenitore di volumi con il modello di larghezza di banda 1](./media/storsimple-add-volume-container/HCS_CreateVCBT1-include.png)

3. Se è stato specificato **Selezionare un modello di larghezza di banda**, scegliere dall'elenco a discesa uno dei modelli di larghezza di banda esistenti. Controllare le impostazioni di pianificazione e fare clic sull'icona del segno di spunta ![icona del segno di spunta](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png).

    ![Creare il contenitore di volumi con il modello di larghezza di banda 2](./media/storsimple-add-volume-container/HCS_CreateVCBT2-include.png)

Il contenitore di volumi verrà salvato e il contenitore di volumi appena creato verrà elencato nella pagina **Contenitori di volumi**.
 

<!---HONumber=Sept15_HO3-->