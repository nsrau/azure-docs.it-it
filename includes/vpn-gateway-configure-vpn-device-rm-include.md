Le connessioni da sito a sito verso una rete locale richiedono un dispositivo VPN. È possibile usare diversi dispositivi VPN con Azure. Per informazioni sui dispositivi VPN e sulle impostazioni di configurazione, vedere [Dispositivi VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). Prima di configurare il dispositivo VPN, verificare eventuali [Problemi noti di compatibilità del dispositivo](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known) per il dispositivo VPN da usare. Per informazioni specifiche sulla configurazione del dispositivo VPN, contattare il produttore del dispositivo.

Durante la configurazione del dispositivo VPN, sono necessari gli elementi seguenti:

- **Indirizzo IP pubblico** del gateway di rete virtuale.

    -  Per trovare l'indirizzo IP pubblico usando il portale di Azure, passare a **Gateway di rete virtuale**, quindi fare clic sul nome del gateway. 

    - Per trovare l'indirizzo IP pubblico del gateway di rete virtuale tramite PowerShell, usare l'esempio seguente, sostituendo i valori con quelli personalizzati.

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- **Chiave condivisa**. Si tratta della stessa chiave condivisa che verrà specificata durante la creazione della connessione VPN da sito a sito. In questi esempi viene usata una chiave condivisa molto semplice. È necessario generare una chiave più complessa per l'uso effettivo.




