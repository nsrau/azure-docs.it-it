1. Nel [portale](http://portal.azure.com) passare alla rete virtuale di Resource Manager per cui si vuole creare un gateway di rete virtuale.
2. Nella sezione **Impostazioni** del pannello della rete virtuale fare clic su **Subnet** per espandere il pannello Subnet.
3. Nel pannello **Subnet** fare clic su **Subnet del gateway** per aprire il pannello **Aggiungi subnet**. 
   
    ![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsubnet.png "Aggiungere la subnet del gateway")
4. Il **nome** della subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Questo valore è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway. Modificare i valori di **Intervallo di indirizzi** compilati automaticamente in modo che corrispondano ai requisiti di configurazione.

    ![Aggiunta della subnet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Aggiunta della subnet")
5. Fare clic su **OK** nella parte inferiore del pannello per creare la subnet.

