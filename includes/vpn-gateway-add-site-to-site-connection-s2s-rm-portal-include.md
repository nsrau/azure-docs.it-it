1. Individuare e aprire il pannello relativo al gateway di rete virtuale. Questa operazione può essere eseguita in vari modi. In questo esempio è stato aperto il gateway "VNet1GW" selezionando **TestVNet1 -> Panoramica -> Dispositivi connessi -> VNet1GW**.
2. Nel pannello di VNet1GW fare clic su **Connessioni**. Nella parte superiore del pannello Connessioni fare clic su **+Aggiungi** per aprire il pannello **Aggiungi connessione**.

    ![Configurare una connessione da sito a sito](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection1.png)

3. Nel pannello **Aggiungi connessione** compilare i valori per creare la connessione.

  - **Nome:** assegnare un nome alla connessione. In questo esempio verrà usato il nome **VNet1toSite2**.
  - **Tipo di connessione**: selezionare **Da sito a sito (IPSec)**.
  - **Gateway di rete virtuale:** questo valore è fisso perché la connessione viene eseguita da questo gateway.
  - **Gateway di rete locale:** fare clic su **Scegli un gateway di rete locale** e selezionare quello che si vuole usare. In questo esempio verrà usato il gateway **Site2**.
  - **Chiave condivisa:** il valore di questo campo deve corrispondere a quello che si usa per il dispositivo VPN locale. Per questo esempio è stato usato "abc123", ma è possibile e consigliabile usare un elemento più complesso. È importante che il valore specificato qui sia lo stesso valore specificato durante la configurazione del dispositivo VPN.
  - I restanti valori di **Sottoscrizione**, **Gruppo di risorse** e **Località** sono fissi.

4. Fare clic su **OK** per creare la connessione. Sullo schermo lampeggerà il messaggio *Creazione della connessione* .
5. È possibile visualizzare la connessione nel pannello **Connessioni** relativo al gateway di rete virtuale. Lo stato passa da *Sconosciuto* a *Connessione* e quindi a *Operazione riuscita*.