1. Nel portale passare alla rete virtuale per cui si vuole creare un gateway di rete virtuale.
2. Nella sezione **Impostazioni** della pagina della rete virtuale fare clic su **Subnet** per espandere la pagina Subnet.
3. Nella pagina **Subnet** fare clic su **+Subnet del gateway** in alto per aprire la pagina **Aggiungi subnet**.

  ![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/add-gw-subnet.png "Aggiungere la subnet del gateway")
4. Il **nome** della subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Il valore GatewaySubnet è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway. Modificare i valori di **Intervallo di indirizzi** compilati automaticamente in modo che corrispondano ai requisiti di configurazione.

  ![Aggiunta della subnet del gateway](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/gwsubnetip.png "Aggiunta della subnet del gateway")
5. Fare clic su **OK** nella parte inferiore della pagina per creare la subnet.