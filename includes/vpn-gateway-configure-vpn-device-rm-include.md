Le connessioni da sito a sito verso una rete locale richiedono un dispositivo VPN. Anche se non viene specificata la procedura di configurazione per tutti i dispositivi VPN, le informazioni contenute nei collegamenti seguenti potrebbero risultare utili:

- Per informazioni sui dispositivi VPN compatibili, vedere l'articolo relativo ai [dispositivi VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). 
- Per collegamenti alle impostazioni di configurazione dei dispositivi, vedere [Dispositivi VPN convalidati](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable). I collegamenti alla configurazione del dispositivo vengono forniti nel modo più efficiente possibile. È sempre consigliabile rivolgersi al produttore del dispositivo per le informazioni di configurazione più aggiornate.
- Per informazioni sulla modifica degli esempi, vedere [Modifica degli esempi di configurazione di dispositivo](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#editing).
- Per informazioni sui parametri IPsec/IKE, vedere [Parametri](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec).
- Prima di configurare il dispositivo VPN, verificare eventuali [Problemi noti di compatibilità del dispositivo](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known) per il dispositivo VPN da usare.

Durante la configurazione del dispositivo VPN, sono necessari gli elementi seguenti:

- Indirizzo IP pubblico del gateway di rete virtuale.

    -  Per trovare l'indirizzo IP pubblico usando il portale di Azure, passare a **Gateway di rete virtuale**, quindi fare clic sul nome del gateway. 
    - Per trovare l'indirizzo IP pubblico del gateway di rete virtuale tramite PowerShell, usare l'esempio seguente, sostituendo i valori con quelli personalizzati.

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- Chiave condivisa. Si tratta della stessa chiave condivisa che viene specificata durante la creazione della connessione VPN da sito a sito. In questi esempi viene usata una chiave condivisa molto semplice. È necessario generare una chiave più complessa per l'uso effettivo.




