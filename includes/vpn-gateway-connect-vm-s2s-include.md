È possibile connettersi a una VM distribuita nella rete virtuale creando una connessione Desktop remoto alla VM. Il modo migliore per verificare inizialmente che è possibile connettersi alla VM consiste nel connettersi usando il rispettivo indirizzo IP privato, invece del nome del computer. Ciò consente di verificare se è possibile connettersi, non se la risoluzione dei nomi è configurata correttamente.

1. Individuare l'indirizzo IP privato. È possibile trovare l'indirizzo IP privato di una macchina virtuale in più modi. Di seguito sono illustrate le procedure da seguire se si usa il portale di Azure o PowerShell.

  - Portale di Azure: individuare la macchina virtuale nel portale di Azure. Visualizzare le proprietà per la VM. Viene elencato l'indirizzo IP.

  - PowerShell: usare l'esempio per visualizzare un elenco di macchine virtuali e di indirizzi IP privati dai gruppi di risorse. Non è necessario modificare questo esempio prima di usarlo.

    ```powershell
    $VMs = Get-AzureRmVM
    $Nics = Get-AzureRmNetworkInterface | Where VirtualMachine -ne $null

    foreach($Nic in $Nics)
    {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
    }
    ```

2. Verificare di essere connessi alla rete virtuale usando la connessione VPN.
3. Aprire la **connessione Desktop remoto** digitando "RDP" o "Connessione Desktop remoto" nella casella di ricerca sulla barra delle applicazioni, quindi selezionare Connessione Desktop remoto. È anche possibile aprire una connessione Desktop remoto usando il comando "mstsc" in PowerShell. 
4. In Connessione Desktop remoto immettere l'indirizzo IP privato della VM. È possibile fare clic su "Mostra opzioni" per modificare altre impostazioni e quindi connettersi.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Per risolvere i problemi di una connessione RDP a una VM

Se si verificano problemi di connessione a una macchina virtuale tramite la connessione VPN, controllare gli elementi seguenti:

- Verificare che la connessione VPN sia attiva.
- Verificare che venga effettuata la connessione all'indirizzo IP privato per la VM.
- Se è possibile connettersi alla VM usando l'indirizzo IP privato, ma non il nome del computer, verificare di avere configurato correttamente il valore per DNS. Per altre informazioni sul funzionamento della risoluzione dei nomi per le macchine virtuali, vedere [Risoluzione dei nomi per le macchine virtuali](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Per altre informazioni sulle connessioni RDP, vedere [Risolvere i problemi delle connessioni Desktop remoto a una macchina virtuale](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).