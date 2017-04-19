È possibile usare `Get-AzureVNetConnection` per verificare la connessione per un gateway di rete virtuale classica. 

1. Per configurare i valori in modo che corrispondano ai propri, usare l'esempio di cmdlet seguente. Il nome della rete virtuale deve essere tra virgolette se contiene spazi.

  ```powershell
  Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
  ```
2. Al termine dell'esecuzione del cmdlet, visualizzare i valori. Nell'esempio seguente lo stato di connettività risulta "Connesso" ed è possibile visualizzare i byte in ingresso e in uscita.

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal
