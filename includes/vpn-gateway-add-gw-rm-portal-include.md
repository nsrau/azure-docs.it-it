1. Sul lato sinistro del portale fare clic su **+** e digitare "Gateway di rete virtuale" nella casella di ricerca. Individuare **Gateway di rete virtuale** nei risultati della ricerca e fare clic sulla voce. Nella parte inferiore del pannello **Gateway di rete virtuale** fare clic su **Crea**. Verrà aperto il pannello **Gateway di rete virtuale**.
2. Nel pannello **Crea gateway di rete virtuale** inserire i valori per il gateway di rete virtuale.

    ![Creare i campi nel pannello Gateway di rete virtuale](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Creare i campi nel pannello Gateway di rete virtuale")
3. **Nome**: assegnare un nome al gateway. Questa operazione non è come quella utilizzata per assegnare un nome alla subnet del gateway. Si tratta del nome dell'oggetto gateway che verrà creato.
4. **Tipo di gateway**: selezionare **VPN**. I gateway VPN usano il gateway di rete virtuale di tipo **VPN**. 
5. **Tipo VPN**: selezionare il tipo di VPN specificato per la configurazione. La maggior parte delle configurazioni richiede un tipo di VPN basato su route.
6. **SKU**: selezionare lo SKU del gateway dall'elenco a discesa. Gli SKU disponibili nell'elenco a discesa dipendono dal tipo di VPN selezionato.
7. **Località**: modificare il campo **Località** in modo che faccia riferimento alla località in cui si trova la rete virtuale. Se la località non fa riferimento all'area in cui si trova la rete virtuale, quest'ultima non verrà visualizzata nell'elenco a discesa "Scegliere una rete virtuale".
8. Scegliere la rete virtuale a cui si vuole aggiungere il gateway. Fare clic su **Rete virtuale** per aprire il pannello **Scegliere una rete virtuale**. Selezionare la rete virtuale. Se la rete virtuale non viene visualizzata, verificare che il campo **Località** faccia riferimento all'area in cui si trova la rete virtuale.
9. Definire un indirizzo IP pubblico. Fare clic su **Indirizzo IP pubblico** per aprire il pannello **Scegli indirizzo IP pubblico**. Fare clic su **+Crea nuovo** per aprire il pannello **Crea indirizzo IP pubblico**. Immettere un nome per l'indirizzo IP pubblico. Il pannello crea un oggetto indirizzo IP pubblico a cui verrà assegnato dinamicamente un indirizzo IP pubblico. Fare clic su **OK** per salvare le modifiche al pannello.
10. **Sottoscrizione**: verificare che sia selezionata la sottoscrizione corretta.
11. **Gruppo di risorse**: questa impostazione è determinata dalla rete virtuale selezionata.
12. Non modificare il valore di **Località** dopo aver specificato le impostazioni precedenti.
13. Verificare le impostazioni. È possibile selezionare **Aggiungi al dashboard** nella parte inferiore del pannello se si vuole che il gateway venga visualizzato nel dashboard.
14. Fare clic su **Crea** per iniziare a creare il gateway. Le impostazioni vengono convalidate e il gateway viene distribuito. La creazione di un gateway può richiedere fino a 45 minuti.
15. Dopo la creazione del gateway, è possibile visualizzare l'indirizzo IP assegnato esaminando la rete virtuale nel portale. Il gateway viene visualizzato come un dispositivo connesso. È possibile fare clic sul dispositivo connesso, ovvero il gateway di rete virtuale, per visualizzare altre informazioni.

