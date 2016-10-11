1. Nel portale passare alla rete virtuale a cui si vuole connettere un gateway.

2. Nella sezione **Impostazioni** del pannello della rete virtuale fare clic su **Subnet** per espandere il pannello Subnet.

3. Nel pannello **Subnet** fare clic su **+Subnet del gateway** nella parte superiore. Verrà aperto il pannello **Aggiungi subnet**.

	![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gwsubnet-rm-portal-include/newgwsubnet450.png "Aggiungere la subnet del gateway")  

4. Il **Nome** per la subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Questo valore è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway. Modificare i valori di **Intervallo di indirizzi** compilati automaticamente in modo che corrispondano ai requisiti di configurazione.

	![Aggiunta della subnet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsubnet300.png "Aggiunta della subnet")  

6. Fare clic su **OK** nella parte inferiore del pannello per creare la subnet.

<!---HONumber=AcomDC_1005_2016-->