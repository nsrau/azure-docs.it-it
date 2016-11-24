## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Come creare una rete virtuale nel portale di Azure
Per creare una rete virtuale in base allo scenario precedente, seguire questa procedura.

1. In un browser passare a http://manage.windowsazure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **NUOVO** > **SERVIZI DI RETE** > **RETE VIRTUALE** > **CREAZIONE PERSONALIZZATA** come illustrato nella figura seguente.
   
    ![Creare reti virtuali nel portale](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)
3. Nella pagina **Dettagli della rete virtuale** digitare il **NOME** della rete virtuale, selezionare il relativo **PERCORSO**, quindi fare clic sulla freccia nell'angolo inferiore destro della pagina per procedere al passaggio 2. La figura seguente illustra le impostazioni per questo scenario.
   
    ![Pagina Dettagli della rete virtuale](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)
4. Nella pagina **Server DNS e connettività VPN** specificare il nome e l’indirizzo IP per un massimo di 9 server DNS da utilizzare. Se non si specifica un server DNS, la rete virtuale utilizzerà la funzionalità di risoluzione dei nomi interna fornita da Azure. Per questo scenario, non verrà configurato alcun server DNS.
5. Se si desidera offrire un accesso VPN da punto a sito (point-to-site) a una rete virtuale, abilitare la casella di controllo **Configurare una VPN point-to-site** . Se non si configura una VPN da punto a sito, sarà possibile aggiungerla alla rete virtuale in qualsiasi momento dopo la creazione. Per questo scenario, non verrà configurata alcuna VPN da punto a sito.
6. Per mettere a disposizione la connettività VPN da sito a sito tra la rete virtuale e un'altra rete virtuale o la rete locale, abilitare la casella di controllo **Configura una VPN Site-to-Site** e specificare se si intende usare o meno **ExpressRoute** e il nome della rete a cui connettersi. Se non si configura una VPN da sito a sito, sarà possibile aggiungerla alla rete virtuale in qualsiasi momento dopo la creazione. Per questo scenario, non verrà configurata alcuna VPN da sito a sito.
7. Fare clic sulla freccia nell'angolo inferiore destro della pagina per procedere al passaggio 3. Nella figura seguente vengono illustrate le impostazioni per questo scenario.
   
    ![Pagina Server DNS e connettività VPN](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)
8. Nella pagina **Spazi di indirizzi della rete virtuale**, in **IP INIZIALE** fare clic su *10.0.0.0* per modificare lo spazio degli indirizzi della rete virtuale e quindi digitare lo spazio degli indirizzi iniziale da usare. Per questo scenario digitare *192.168.0.0*. 
9. In **CIDR (CONTEGGIO INDIRIZZI)** selezionare il numero di bit per la subnet mask. Per questo scenario selezionare *16 (65536)*.
10. In **SUBNET**fare clic su *Subnet-1* e rinominare la subnet se necessario. Per questo scenario, rinominarla *FrontEnd*.
    
    > [!NOTE]
    > Se si fa clic all'esterno della casella di testo nome per una subnet, non sarà possibile modificare di nuovo il nome della subnet. Per correggere questo errore, è necessario rimuovere la subnet facendo clic sul pulsante X a destra e quindi aggiungere una nuova subnet come descritto nel passaggio 13 seguente.
    > 
    > 
11. In **IP INIZIALE** per la prima subnet specificare l'indirizzo IP iniziale per la subnet. Per questo scenario, digitare *192.168.1.0*.
12. In **CIDR (CONTEGGIO INDIRIZZI)** selezionare il numero di bit della subnet mask per la prima subnet. Per questo scenario, selezionare *24 (256)*.
13. Fare clic su **Aggiungi subnet** per aggiungere una nuova subnet, se necessario. Per questo scenario, aggiungere una subnet e ripetere i passaggi da 10 a 12 per configurare la rete virtuale, come illustrato nella figura seguente.
    
    ![Spazi di indirizzi della rete virtuale](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)
14. Fare clic sul pulsante con il segno di spunta nell'angolo inferiore destro della pagina per creare la rete virtuale. Dopo alcuni secondi, la rete virtuale verrà visualizzata nell'elenco di reti virtuali disponibili, come illustrato nella figura seguente.
    
    ![Nuova rete virtuale](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)



<!--HONumber=Nov16_HO3-->


