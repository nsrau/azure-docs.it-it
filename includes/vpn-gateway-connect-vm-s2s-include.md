È possibile connettersi a una VM distribuita nella rete virtuale creando una connessione Desktop remoto alla VM. Il modo migliore per verificare inizialmente che è possibile connettersi alla VM consiste nel connettersi usando il rispettivo indirizzo IP privato, invece del nome del computer. Ciò consente di verificare se è possibile connettersi, non se la risoluzione dei nomi è configurata correttamente.

1. **Individuare l'indirizzo IP privato.** È possibile trovare l'indirizzo IP privato di una VM esaminando le proprietà per la VM nel portale di Azure oppure usando PowerShell.

  - Portale di Azure: individuare la macchina virtuale nel portale di Azure. Visualizzare le proprietà per la VM. Viene elencato l'indirizzo IP.

  - PowerShell: usare l'esempio per visualizzare un elenco di macchine virtuali e di indirizzi IP privati dai gruppi di risorse. Non è necessario modificare questo esempio prima di usarlo.

    ```powershell
    $vms = get-azurermvm
    $nics = get-azurermnetworkinterface | where VirtualMachine -NE $null

    foreach($nic in $nics)
    {
      $vm = $vms | where-object -Property Id -EQ $nic.VirtualMachine.id
      $prv = $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAddress
      $alloc = $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($vm.Name): $prv,$alloc"
    }
    ```

2. **Connettersi alla VM.** Verificare di essere connessi alla rete virtuale usando la connessione VPN. Aprire la connessione Desktop remoto digitando "RDP" o "Connessione Desktop remoto" nella casella di ricerca sulla barra delle applicazioni, quindi selezionare Connessione Desktop remoto. È anche possibile aprire una connessione Desktop remoto usando il comando `mstsc` in PowerShell. In Connessione Desktop remoto immettere l'indirizzo IP privato della VM. È possibile fare clic su "Mostra opzioni" per modificare altre impostazioni e quindi connettersi.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Per risolvere i problemi di una connessione RDP a una VM

Se si verificano problemi di connessione a una macchina virtuale tramite la connessione VPN, è possibile controllare alcuni elementi.

- Verificare che la connessione VPN sia attiva.
- Verificare che venga effettuata la connessione all'indirizzo IP privato per la VM.
- Se è possibile connettersi alla VM usando l'indirizzo IP privato, ma non il nome del computer, verificare di avere configurato correttamente il valore per DNS.
- Per altre informazioni, vedere [Risolvere i problemi delle connessioni Desktop remoto a una VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).