

Con le estensioni VM è possibile:

* Modificare le funzionalità di sicurezza e identità, ad esempio la reimpostazione dei valori dell'account e l'uso di soluzioni antimalware.
* Avviare, arrestare o configurare il monitoraggio e la diagnostica.
* Reimpostare o installare funzionalità di connettività, ad esempio RDP e SSH.
* Eseguire la diagnosi, il monitoraggio e la gestione delle macchine virtuali.

Sono disponibili anche molte altre funzionalità. Periodicamente vengono rilasciate nuove funzionalità delle estensioni VM. Questo articolo fornisce una descrizione degli agenti VM di Azure per Windows e Linux e illustra come tali agenti supportano le estensioni VM. Per un elenco di estensioni VM raggruppate per categoria di funzionalità, vedere [Estensioni e funzionalità delle macchine virtuali](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="azure-vm-agents-for-windows-and-linux"></a>Agenti VM di Azure per Windows e Linux
L'agente della macchina virtuale di Azure (agente VM) è un processo protetto, con requisiti di risorse limitati, che installa, configura e rimuove estensioni VM su istanze di macchine virtuali di Azure. L'agente VM svolge la funzione di servizio di controllo locale sicuro per la macchina virtuale di Azure. Le estensioni caricate dall'agente offrono funzionalità specifiche che consentono di aumentare la produttività.

Esistono due agenti VM di Azure, rispettivamente per le macchine virtuali Windows e Linux.

Se si vuole che un'istanza di macchina virtuale usi una o più estensioni VM, l'istanza deve disporre di un agente VM installato. Un'immagine di macchina virtuale creata tramite il portale di Azure e un'immagine del **Marketplace** installano automaticamente un agente VM nel processo di creazione. Se un'istanza di macchina virtuale non dispone di un agente VM, è possibile installare l'agente VM dopo aver creato l'istanza di macchina virtuale. In alternativa, è possibile installare l'agente in un'immagine di macchina virtuale personalizzata da caricare successivamente.

> [!IMPORTANT]
> Gli agenti VM sono servizi con requisiti di risorse limitati che consentono l'amministrazione protetta delle istanze di macchine virtuali. Se non si vuole usare l'agente VM, assicurarsi di creare macchine virtuali in cui l'agente VM non è installato usando l'interfaccia della riga di comando di Azure o PowerShell. Anche se l'agente VM può essere rimosso fisicamente, il comportamento delle estensioni VM nell'istanza non è definito. Di conseguenza, la rimozione di un agente VM installato non è supportata.
>

L'agente VM è abilitato nelle seguenti situazioni:

* Quando si crea un'istanza di una macchina virtuale usando il portale di Azure e selezionando un'immagine dal **Marketplace**.
* Quando si crea un'istanza di una macchina virtuale con il cmdlet [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) o [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx). È possibile creare una macchina virtuale senza un agente VM aggiungendo il parametro **–DisableGuestAgent** al cmdlet [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

* Quando si scarica e si installa manualmente l'agente VM in un'istanza di macchina virtuale esistente e si imposta il valore di **ProvisionGuestAgent** su **true**. È possibile usare questa tecnica per gli agenti Windows e Linux tramite un comando di PowerShell o una chiamata REST. Se il valore **ProvisionGuestAgent** non viene impostato dopo l'installazione manuale dell'agente VM, l'aggiunta dell'agente VM non viene rilevata correttamente. Il codice di esempio seguente illustra come eseguire questa operazione tramite PowerShell. In questo caso, gli argomenti `$svc` e `$name` sono già stati determinati:

      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

* Quando si crea un'immagine di macchina virtuale che include un agente VM installato. Una volta creata l'immagine con l'agente VM, è possibile caricarla in Azure. Per una macchina virtuale Windows, scaricare il [file con estensione .msi dell'agente VM di Windows](http://go.microsoft.com/fwlink/?LinkID=394789) e installare l'agente VM. Per una macchina virtuale Linux, installare l'agente VM dal repository GitHub all'indirizzo <https://github.com/Azure/WALinuxAgent>. Per altre informazioni sull'installazione dell'agente VM in Linux, vedere [Azure Linux VM Agent User Guide](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Guida per l'utente dell'agente VM Linux di Azure).

> [!NOTE]
> Nelle soluzioni PaaS l'agente di macchine virtuali è denominato **WindowsAzureGuestAgent**ed è sempre disponibile nelle macchine virtuali con ruoli Web e di lavoro. Per altre informazioni, vedere il post di blog relativo all'[architettura dei ruoli di Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx). L'agente VM per le macchine virtuali del ruolo può ora aggiungere estensioni alle macchine virtuali del servizio cloud in modo analogo alle macchine virtuali persistenti. La differenza principale tra le estensioni VM su macchine virtuali del ruolo e su macchine virtuali persistenti è data dal momento in cui vengono aggiunte le estensioni VM. Con le macchine virtuali del ruolo, le estensioni vengono aggiunte prima al servizio cloud, quindi alle distribuzioni all'interno di tale servizio cloud.
>
> Usare il cmdlet [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) per elencare tutte le estensioni VM del ruolo disponibili.
>
>

## <a name="find-add-update-and-remove-vm-extensions"></a>Trovare, aggiungere, aggiornare e rimuovere estensioni VM
Per informazioni dettagliate sulle attività, vedere come [aggiungere, trovare, aggiornare e rimuovere estensioni di VM di Azure](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
