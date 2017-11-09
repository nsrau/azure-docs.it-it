1. Nel [portale](http://portal.azure.com) passare alla rete virtuale di Resource Manager per cui si vuole creare un gateway di rete virtuale.
2. Nella sezione **Impostazioni** della pagina della rete virtuale fare clic su **Subnet** per espandere la pagina Subnet.
3. Nella pagina **Subnet** fare clic su **+Subnet del gateway** per aprire la pagina **Aggiungi subnet**.

  ![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Aggiungere la subnet del gateway")
4. Il **nome** della subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Questo valore è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway. Modificare i valori di **Intervallo di indirizzi** inseriti automaticamente in modo che corrispondano ai requisiti della configurazione e quindi fare clic su **OK** nella parte inferiore della pagina per creare la subnet.

  ![Aggiunta della subnet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Aggiunta della subnet")