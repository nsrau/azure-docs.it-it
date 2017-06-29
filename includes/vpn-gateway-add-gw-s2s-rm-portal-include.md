1. Nel lato sinistro della pagina del portale fare clic su **+** e digitare 'Gateway di rete virtuale' nella casella di ricerca. In **Risultati** individuare e selezionare **Gateway di rete virtuale**.
2. Nella parte inferiore del pannello "Gateway di rete virtuale" fare clic su **Crea**. Verrà aperto il pannello **Gateway di rete virtuale**.

    ![Campi del pannello Crea gateway di rete virtuale](./media/vpn-gateway-add-gw-s2s-rm-portal-include/vnet_gw.png "Nuovo gateway")

3. Nel pannello **Crea gateway di rete virtuale** specificare i valori per il gateway di rete virtuale.

  - **Nome**: assegnare un nome al gateway. Questa operazione non è come quella utilizzata per assegnare un nome alla subnet del gateway. Si tratta del nome dell'oggetto gateway che verrà creato.
  - **Tipo di gateway**: selezionare **VPN**. I gateway VPN usano il gateway di rete virtuale di tipo **VPN**. 
  - **Tipo VPN**: selezionare il tipo di VPN specificato per la configurazione. La maggior parte delle configurazioni richiede un tipo di VPN basato su route.
  - **SKU**: selezionare lo SKU del gateway dall'elenco a discesa. Gli SKU disponibili nell'elenco a discesa dipendono dal tipo di VPN selezionato. Per informazioni sugli SKU del gateway, vedere [SKU del gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
  - **Posizione**: potrebbe essere necessario scorrere la schermata per trovare la posizione. Modificare il campo **Località** in modo che faccia riferimento alla località in cui si trova la rete virtuale. Se la località non fa riferimento all'area in cui si trova la rete virtuale, quest'ultima non verrà visualizzata nell'elenco a discesa "Scegliere una rete virtuale" nel passaggio successivo.
  - **Rete virtuale**: scegliere la rete virtuale a cui si vuole aggiungere il gateway. Fare clic su **Rete virtuale** per aprire il pannello "Scegliere una rete virtuale". Selezionare la rete virtuale. Se la rete virtuale non viene visualizzata, verificare che il campo Località faccia riferimento all'area in cui si trova la rete virtuale.
  - **Indirizzo IP pubblico**: il pannello "Crea indirizzo IP pubblico" consente di creare un oggetto indirizzo IP pubblico. L'indirizzo IP pubblico viene assegnato dinamicamente durante la creazione del gateway VPN. Il gateway VPN supporta attualmente solo l'allocazione degli indirizzi IP pubblici *dinamici*. Ciò non significa tuttavia che l'indirizzo IP viene modificato dopo l'assegnazione al gateway VPN. L'indirizzo IP pubblico viene modificato solo quando il gateway viene eliminato e ricreato. Non viene modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway VPN.

    - Fare clic su **Indirizzo IP pubblico** per aprire il pannello "Scegli indirizzo IP pubblico" e quindi fare clic su **Crea nuovo** per aprire il pannello "Crea indirizzo IP pubblico".
    - Digitare quindi un **Nome** per l'indirizzo IP pubblico e fare clic su **OK** nella parte inferiore del pannello per salvare le modifiche.

      ![Crea indirizzo IP pubblico](./media/vpn-gateway-add-gw-s2s-rm-portal-include/pip.png "Crea indirizzo IP pubblico")

4. Verificare le impostazioni. È possibile selezionare **Aggiungi al dashboard** nella parte inferiore del pannello se si vuole che il gateway venga visualizzato nel dashboard. 
5. Fare clic su **Crea** per iniziare a creare il gateway VPN. Le impostazioni verranno convalidate e nel dashboard verrà visualizzato il riquadro relativo alla distribuzione del gateway di rete virtuale. La creazione di un gateway può richiedere fino a 45 minuti. Potrebbe essere necessario aggiornare la pagina del portale per visualizzare lo stato di completamento.

Dopo la creazione del gateway, è possibile visualizzare l'indirizzo IP assegnato esaminando la rete virtuale nel portale. Il gateway viene visualizzato come un dispositivo connesso. È possibile fare clic sul dispositivo connesso, ovvero il gateway di rete virtuale, per visualizzare altre informazioni.