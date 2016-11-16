### <a name="configure-a-dns-label-for-the-public-ip-address"></a>Configurare un'etichetta DNS per l'indirizzo IP pubblico
Per connettersi al motore di database di SQL Server da Internet, è innanzitutto necessario configurare un'etichetta DNS per l'indirizzo IP pubblico.

> [!NOTE]
> Le etichette DNS non sono necessarie se si intende connettersi solo all'istanza di SQL Server presente nella stessa rete virtuale o solo in locale.
> 
> 

Per creare un'etichetta DNS, selezionare prima di tutto **Macchine virtuali** nel portale. Selezionare la propria macchina virtuale di SQL Server per visualizzarne le proprietà.

1. Nel pannello della macchina virtuale selezionare l' **indirizzo IP pubblico.**
   
    ![indirizzo ip pubblico](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)
2. Nelle proprietà dell'indirizzo IP pubblico espandere **Configurazione**.
3. Immettere un nome per l'etichetta DNS. Il nome è un record A che consente di connettersi alla macchina virtuale di SQL Server usando il nome, anziché tramite l'indirizzo IP.
4. Fare clic sul pulsante **Salva** .
   
    ![etichetta dns](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Eseguire la connessione al motore di database da un altro computer
1. In un computer connesso a Internet aprire SQL Server Management Studio (SSMS).
2. Nella finestra di dialogo **Connetti al server** o **Connetti al motore di database** modificare il valore di **Nome server**. Immettere il nome DNS completo della macchina virtuale, determinato nell'attività precedente.
3. Nella casella **Autenticazione** selezionare **Autenticazione di SQL Server**.
4. Nella casella **Accesso** digitare il nome di un account di accesso SQL valido.
5. Nella casella **Password** digitare la password dell'account di accesso.
6. Fare clic su **Connect**.
   
    ![connessione a ssms](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)



<!--HONumber=Nov16_HO2-->


