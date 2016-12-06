<!--author=v-sharos last changed: 11/06/15-->

#### <a name="to-add-a-storsimple-backup-policy"></a>Per aggiungere un criterio di backup per StorSimple
1. Nella pagina **Avvio rapido** fare clic sulla scheda **Criteri di backup**. Verrà visualizzata la pagina **Criteri di backup** .
2. Nella parte inferiore della pagina, fare clic su **Aggiungi** per avviare la procedura guidata Aggiungi criteri di Backup.
   
    ![Aggiungere un criterio di backup 1](./media/storsimple-add-backup-policy-u2/AddBackupPolicy1.png)
3. Nella finestra di dialogo **Aggiungi un criterio di backup** visualizzata in **Definire i criteri di backup** eseguire questa procedura:
   
   1. Specificare un nome per il criterio di backup che contenga tra i 3 e i 150 caratteri.
   2. Fare clic su una o più caselle di controllo per assegnare uno o più volumi a questo criterio di backup. Si noti che non è possibile selezionare volumi che utilizzano provider di servizi cloud diversi. Se si utilizzano più provider di servizi cloud, in base alla prima selezione, l'elenco visualizzerà i volumi appartenenti solo a tale provider di servizi cloud. In questo modo sarà possibile raggruppare i volumi appartenenti a un singolo provider di servizi cloud in uno snapshot.
   3. Fare clic sull'icona freccia  ![Icona freccia](./media/storsimple-add-backup-policy-u2/HCS_ArrowIcon-include.png)  per passare alla pagina successiva.
      
      ![Aggiungere un criterio di backup 2](./media/storsimple-add-backup-policy-u2/AddBackupPolicy2.png)
4. In **Definisci una pianificazione**, eseguire le operazioni seguenti:
   
   1. Nella casella **Tipo di backup** selezionare **Snapshot cloud** o **Snapshot locale** dall'elenco a discesa.
   2. Indicare la frequenza dei backup specificando un numero e quindi scegliendo **Giorni** o **Settimane** dall'elenco a discesa.
   3. Immettere una pianificazione di conservazione.
   4. Immettere una data e ora di inizio per il criterio di backup.  
   5. Fare clic sull’icona del segno del spunta  ![icona del segno di spunta](./media/storsimple-add-backup-policy-u2/HCS_CheckIcon-include.png)  per salvare il criterio.

Il criterio appena aggiunto verrà visualizzato nella visualizzazione tabulare nella pagina **Criteri di backup** .



<!--HONumber=Nov16_HO3-->


