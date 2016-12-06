



Con le estensioni VM è possibile:

* Modificare le funzionalità di sicurezza e identità, ad esempio la reimpostazione dei valori dell'account e l'uso di soluzioni antimalware.
* Avviare, arrestare o configurare il monitoraggio e la diagnostica.
* Reimpostare o installare funzionalità di connettività, ad esempio RDP e SSH.
* Eseguire la diagnosi, il monitoraggio e la gestione delle macchine virtuali.

Sono disponibili anche molte altre funzionalità. Periodicamente vengono rilasciate nuove funzionalità delle estensioni VM. Questo articolo fornisce una descrizione degli agenti VM di Azure per Windows e Linux e illustra come tali agenti supportano le estensioni VM. Per un elenco di estensioni VM raggruppate per categoria di funzionalità, vedere [Estensioni e funzionalità delle macchine virtuali](../articles/virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="azure-vm-agents-for-windows-and-linux"></a>Agenti VM di Azure per Windows e Linux
L'agente VM è un processo protetto, con requisiti di risorse limitati, che installa, configura e rimuove estensioni VM su istanze di macchine virtuali di Azure dalla Raccolta immagini e su istanze di macchine virtuali personalizzate in cui è installato l'agente VM. L'agente VM svolge la funzione di servizio di controllo locale sicuro per la macchina virtuale di Azure. Le estensioni caricate dall'agente offrono funzionalità specifiche che consentono di aumentare la produttività.

Esistono due agenti VM di Azure, rispettivamente per le macchine virtuali Windows e Linux. Per impostazione predefinita, l'agente VM viene installato automaticamente al momento della creazione di una VM dalla Raccolta immagini, ma è possibile anche installare l'agente VM dopo la creazione dell'istanza oppure in un'immagine di VM personalizzata da caricare manualmente.

> [!IMPORTANT]
> Gli agenti VM sono servizi con requisiti di risorse limitati che consentono l'amministrazione protetta delle istanze di macchine virtuali. Se non si vuole usare l'agente VM, assicurarsi di creare macchine virtuali in cui l'agente VM non è installato. Anche se l'agente VM può essere rimosso fisicamente, il comportamento delle estensioni VM nell'istanza non è definito. Di conseguenza, la rimozione dell'agente VM dopo l'installazione non è al momento supportata.
> 
> 

L'agente VM è abilitato nelle seguenti situazioni:

* Quando si crea un'istanza di una macchina virtuale usando il metodo **Creazione rapida** o **Creazione personalizzata** nel portale di Azure classico, con la casella di controllo **Installa l'agente di macchine virtuali** selezionata, come illustrato nell'immagine seguente. Per altre informazioni, vedere [Come creare una macchina virtuale personalizzata](../articles/virtual-machines/virtual-machines-windows-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  
  ![Casella di controllo dell'agente VM](./media/virtual-machines-common-classic-agents-and-extensions/IC719409.png)
* Quando si crea un'istanza di una macchina virtuale con il cmdlet [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) o [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx). È possibile creare una macchina virtuale senza l'agente VM installato aggiungendo il parametro **–DisableGuestAgent** al cmdlet [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) .
* Quando si scarica e installa manualmente l'agente VM (versione per Windows o Linux) in un'istanza di macchina virtuale esistente e quindi si imposta il valore di **ProvisionGuestAgent** su **true** tramite PowerShell o una chiamata REST. Se questo valore non viene impostato dopo l'installazione manuale dell'agente VM, l'aggiunta dell'agente VM non viene rilevata correttamente. Il seguente codice di esempio illustra come eseguire questa operazione tramite PowerShell. In questo caso, gli argomenti `$svc` e `$name` sono già stati determinati.
  
      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc
* Quando si crea un'immagine di macchina virtuale con l'agente VM installato prima del caricamento in Azure. Per una macchina virtuale Windows, scaricare il [file con estensione .msi dell'agente VM di Windows](http://go.microsoft.com/fwlink/?LinkID=394789) e installare l'agente VM. Per una VM Linux, il componente verrà installato dal repository GitHub all'indirizzo <https://github.com/Azure/WALinuxAgent>. Per altre informazioni sull'installazione dell'agente VM in Linux, vedere [Azure Linux VM Agent User Guide](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Guida per l'utente dell'agente VM Linux di Azure).

> [!NOTE]
> Nelle soluzioni PaaS l'agente di macchine virtuali è denominato **WindowsAzureGuestAgent**ed è sempre disponibile nelle macchine virtuali con ruoli Web e di lavoro. Per altre informazioni, vedere il post di blog relativo all'[architettura dei ruoli di Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx). L'agente VM per le macchine virtuali del ruolo può ora aggiungere estensioni alle macchine virtuali del servizio cloud in modo analogo alle macchine virtuali persistenti. La differenza principale tra le estensioni VM su macchine virtuali del ruolo e quelle su macchine virtuali persistenti è data dal fatto che nel primo caso le estensioni vengono aggiunte prima al servizio cloud e dopo le distribuzioni all'interno di tale servizio.
> 
> Usare il cmdlet [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) per elencare tutte le estensioni VM del ruolo disponibili.
> 
> 

## <a name="find-add-update-and-remove-vm-extensions"></a>Trovare, aggiungere, aggiornare e rimuovere estensioni VM
Per informazioni dettagliate sulle attività, vedere come [aggiungere, trovare, aggiornare e rimuovere estensioni di VM di Azure](../articles/virtual-machines/virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).



<!--HONumber=Nov16_HO3-->


