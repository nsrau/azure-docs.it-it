1. Individuare il gateway di rete virtuale.
2. Fare clic su **Connessioni**. Nella parte superiore del pannello Connessioni fare clic su **+Aggiungi** per aprire il pannello **Aggiungi connessione**.
   
    ![Configurare una connessione da sito a sito](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection.png)
3. Nel pannello **Aggiungi connessione** assegnare un **nome** alla connessione. 
4. In **Tipo di connessione** selezionare **Da sito a sito (IPSec)**.
5. Il valore del campo **Gateway di rete virtuale**è fisso perché la connessione viene eseguita da questo gateway.
6. In **Gateway di rete locale** fare clic su **Scegli un gateway di rete locale** e selezionare quello che si vuole usare. 
7. Il valore del campo **Chiave condivisa** deve corrispondere a quello che si usa per il dispositivo VPN locale. Per questo esempio è stato usato "abc123", ma è possibile e consigliabile usare un elemento più complesso. È importante che il valore specificato qui sia lo stesso valore specificato durante la configurazione del dispositivo VPN.
8. I restanti valori di **Sottoscrizione**, **Gruppo di risorse** e **Località** sono fissi.
9. Fare clic su **OK** per creare la connessione. Sullo schermo lampeggerà il messaggio *Creazione della connessione* .
10. Dopo avere completato la connessione, questa viene visualizzata nel pannello **Connessioni** relativo al gateway di rete virtuale.
    
    ![Configurare una connessione da sito a sito](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connectionstatus450.png)

