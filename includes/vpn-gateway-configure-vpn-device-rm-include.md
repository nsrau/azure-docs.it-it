
Per configurare il dispositivo VPN locale, è necessario l'indirizzo IP pubblico del gateway di rete virtuale. Contattare il produttore del dispositivo per informazioni specifiche sulla configurazione del dispositivo. Per informazioni sui dispositivi VPN compatibili con Azure, vedere [Informazioni sui dispositivi VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md).

Per trovare l'indirizzo IP pubblico del gateway di rete virtuale con PowerShell, usare l'esempio seguente:

	Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

È possibile visualizzare l'indirizzo IP pubblico del gateway di rete virtuale anche tramite il portale di Azure. Passare a **Gateway di rete virtuale** e quindi fare clic sul nome del gateway.

<!---HONumber=AcomDC_0406_2016-->