1. Nel portale passare alla rete virtuale per cui si vuole creare un gateway di rete virtuale.
2. Nella sezione **Impostazioni** del pannello della rete virtuale fare clic su **Subnet** per espandere il pannello Subnet.
3. Nel pannello **Subnet** fare clic su **+Subnet gateway** nella parte superiore. Verrà aperto il pannello **Aggiungi subnet** .

  ![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/add-gw-subnet.png "Aggiungere la subnet del gateway")
4. Il **Nome** per la subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Questo valore è obbligatorio per consentire ad Azure di riconoscere la subnet come GatewaySubnet. Modificare i valori di **Intervallo di indirizzi** compilati automaticamente in modo che corrispondano ai requisiti di configurazione.

  ![Aggiunta della subnet del gateway](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/gwsubnetip.png "Aggiunta della subnet del gateway")
5. Fare clic su **OK** nella parte inferiore del pannello per creare la subnet.