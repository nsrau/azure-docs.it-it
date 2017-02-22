#### <a name="to-install-mpio-on-the-host"></a>Per installare MPIO nell'host
1. Aprire Server Manager nell'host Windows Server. Per impostazione predefinita, quando un membro del gruppo Administrators accede a un computer che esegue Windows Server 2012 R2 o Windows Server 2012 viene avviato Server Manager. Se Server Manager non è già aperto, fare clic su **Start > Server Manager**.
   
    ![Server Manager](./media/storsimple-install-mpio-windows-server/IC740997.png)
2. Fare clic su **Server Manager > Dashboard > Aggiungi ruoli e funzionalità**. Verrà avviata la procedura guidata **Aggiungi ruoli e funzionalità** .
   
    ![Aggiunta guidata ruoli e funzionalità 1](./media/storsimple-install-mpio-windows-server/IC740998.png)
3. Nella procedura guidata **Aggiungi ruoli e funzionalità** , effettuare le seguenti operazioni:
   
   * Nella pagina **Prima di iniziare** fare clic su **Avanti**.
   * Nella pagina **Seleziona tipo di installazione** accettare l'impostazione predefinita di **Installazione basata su ruoli o basata su funzionalità**. Fare clic su **Avanti**.
     
       ![Aggiunta guidata ruoli e funzionalità 2](./media/storsimple-install-mpio-windows-server/IC740999.png)
   * Nella pagina **Selezione server di destinazione** scegliere **Selezionare un server dal pool di server**. Il server host deve essere rilevato automaticamente. Fare clic su **Avanti**.
   * Nella pagina **Selezione ruoli server** fare clic su **Avanti**.
   * Nella pagina **Selezione funzionalità** selezionare **Multipath I/O** e fare clic su **Avanti**.
     
       ![Aggiunta guidata ruoli e funzionalità 5](./media/storsimple-install-mpio-windows-server/IC741000.png)
   * Nella pagina **Conferma selezioni per l'installazione** confermare la selezione e quindi scegliere **Riavvia automaticamente il server di destinazione se necessario**, come mostrato di seguito. Fare clic su **Installa**.
     
       ![Aggiunta guidata ruoli e funzionalità 8](./media/storsimple-install-mpio-windows-server/IC741001.png)
   * Al termine dell'installazione si riceverà una notifica. Fare clic su **Chiudi** per chiudere la procedura guidata.
     
       ![Aggiunta guidata ruoli e funzionalità 9](./media/storsimple-install-mpio-windows-server/IC741002.png)



<!--HONumber=Nov16_HO3-->


