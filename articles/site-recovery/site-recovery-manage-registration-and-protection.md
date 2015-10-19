<properties
	pageTitle="Gestire registrazione e protezione" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali ubicate nei server locali in Azure o in un data center secondario. Utilizzare questo articolo per annullare la registrazione di server da un insieme di credenziali di Site Recovery e per disabilitare la protezione per le macchine virtuali e i server fisici." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="10/07/2015" 
	ms.author="raynew"/>

# Gestire registrazione e protezione

In questo articolo viene descritto come annullare la registrazione di server dall'insieme di credenziali di Site Recovery e come disabilitare la protezione per le macchine virtuali protette da Site Recovery. In caso di domande dopo la lettura di questo articolo, è possibile pubblicarle nel [forum relativo a Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Annullare la registrazione di un server VMM

Per annullare la registrazione di un server VMM da un insieme di credenziali, eliminare il server nella scheda **Server** del portale di Azure Site Recovery. Si noti che:

-  **Server VMM connesso**: è consigliabile annullare la registrazione del server VMM quando è connesso ad Azure. Ciò garantisce che le impostazioni nel server VMM locale e i server ad esso VMM associati (i server VMM che contengono cloud mappati ai cloud sul server da eliminare) vengano rimosse correttamente. Si consiglia di rimuovere un server non connesso solo in caso di problema di connettività permanente.
- **Server VMM non connesso**: se il server VMM non è connesso quando viene eliminato, è necessario eseguire uno script per eseguire la pulizia manualmente. Lo script è disponibile nella [Raccolta Microsoft](https://gallery.technet.microsoft.com/scriptcenter/Cleanup-Script-for-Windows-95101439). Prendere nota dell'ID di VMM del server per completare il processo di pulizia manuale.
- **Server VMM in cluster**: se si desidera annullare la registrazione di un server VMM distribuito in un cluster, procedere nel modo seguente:

	- Se il server è connesso, eliminare il server VMM connesso nella scheda **Server**. Per disinstallare il Provider nel server, accedere a ogni nodo del cluster e disinstallarlo dal Pannello di controllo. Eseguire lo script di pulizia a cui si fa riferimento nella sezione precedente in tutti i nodi passivi del cluster per eliminare le voci di registrazione.
	- Se il server non è connesso è necessario eseguire lo script di pulizia in tutti i nodi del cluster.

### Annullare la registrazione di un server VMM non connesso

Nel server VMM che si desidera rimuovere:

1. Annullare la registrazione del server VMM dal portale di Azure.
2. Nel server VMM, scaricare lo script di pulizia.
3. Aprire PowerShell con l'opzione di esecuzione come amministratore per modificare i criteri di esecuzione per l'ambito predefinito (LocalMachine).
4. Seguire le istruzioni contenute nello script. 

Nei server VMM con cloud associati ai cloud nel server da rimuovere:

1. Eseguire lo script di pulizia e seguire i passaggi da 2 a 4.
2. Specificare l'ID di VMM per il server VMM di cui è stata annullata la registrazione. 
3. Questo script rimuoverà le informazioni di registrazione per il Server VMM e le informazioni di associazione al cloud.


## Annullare la registrazione di un server Hyper-V in un sito di Hyper-V

Quando Azure Site Recovery viene distribuito per proteggere le macchine virtuali presenti in un server Hyper-V in un sito di Hyper-V (con nessun server VMM) è possibile annullare la registrazione di un server Hyper-V da un insieme di credenziali come indicato di seguito:

1. Disabilitare la protezione per le macchine virtuali presenti nel server Hyper-V.
2. Nella scheda **Server** nel portale di Azure Site Recovery, selezionare il server > Elimina. Il server non deve necessariamente essere connesso ad Azure quando si esegue questa operazione.
3. Eseguire lo script seguente per pulire le impostazioni del server e annullare la registrazione dall'insieme di credenziali. 

	    pushd .
	    try
	    {
		     $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
		     $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
		     $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
		     $isAdmin=$principal.IsInRole($administrators)
		     if (!$isAdmin)
		     {
		        "Please run the script as an administrator in elevated mode."
		        $choice = Read-Host
		        return;       
		     }
	
		    $error.Clear()    
			"This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
			$choice =  Read-Host
		
			if (!($choice -eq 'Y' -or $choice -eq 'y'))
			{
			"Stopping cleanup."
			return;
			}
		
			$serviceName = "dra"
			$service = Get-Service -Name $serviceName
			if ($service.Status -eq "Running")
		    {
				"Stopping the Azure Site Recovery service..."
				net stop $serviceName
			}
		
		    $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
			$registrationPath = $asrHivePath + '\Registration'
			$proxySettingsPath = $asrHivePath + '\ProxySettings'
		    $draIdvalue = 'DraID'
	    
		    if (Test-Path $asrHivePath)
		    {
		        if (Test-Path $registrationPath)
		        {
		            "Removing registration related registry keys."	
			        Remove-Item -Recurse -Path $registrationPath
		        }
	
		        if (Test-Path $proxySettingsPath)
	        {
			        "Removing proxy settings"
			        Remove-Item -Recurse -Path $proxySettingsPath
		        }
	
		        $regNode = Get-ItemProperty -Path $asrHivePath
		        if($regNode.DraID -ne $null)
		        {            
		            "Removing DraId"
		            Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
		        }
			    "Registry keys removed."
		    }
	
		    # First retrive all the certificates to be deleted
			$ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
			# Open a cert store object
			$store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
			$store.Open('ReadWrite')
			# Delete the certs
			"Removing all related certificates"
		    foreach ($cert in $ASRcerts)
		    {
			    $store.Remove($cert)
		    }
	    }catch
	    {	
		    [system.exception]
		    Write-Host "Error occured" -ForegroundColor "Red"
		    $error[0] 
		    Write-Host "FAILED" -ForegroundColor "Red"
	    }
	    popd


## Arrestare la protezione di una macchina virtuale Hyper-V

Se si desidera arrestare la protezione di una macchina virtuale Hyper-V è necessario rimuovere la protezione. A seconda come si rimuove la protezione potrebbe essere necessario rimuovere le impostazioni di protezione manualmente nel computer.

### Rimuovere la protezione

1. Nella scheda **Macchine virtuali** delle proprietà del cloud, selezionare la macchina virtuale > **Rimuovi**.
2. Nella pagina **Conferma rimozione della macchina virtuale** sono disponibili due opzioni:

	- **Disabilitare la protezione**: se si abilita e si salva questa opzione la macchina virtuale non sarà protetta da Ripristino sito. Le impostazioni di protezione per la macchina virtuale verranno pulire automaticamente.
	- **Rimuovere dall'insieme di credenziali**: se si seleziona questa opzione la macchina virtuale verrà rimossa solo dall'insieme di credenziali di Ripristino sito. Le impostazioni di protezione locali per la macchina virtuale non saranno interessate. È necessario eliminare le impostazioni manualmente per rimuovere le impostazioni di protezione, rimuovere la macchina virtuale da una sottoscrizione di Azure e rimuovere le impostazioni di protezione che dovranno essere eliminate manualmente utilizzando le istruzioni seguenti.

Se si sceglie di eliminare la macchina virtuale e i relativi dischi rigidi, essi verranno rimossi dal percorso di destinazione.

### Eseguire la pulizia manuale delle impostazioni di protezione (tra i siti VMM)

Se si seleziona **Interrompere la gestione della macchina virtuale**, rimuovere manualmente le impostazioni:

1. Nel server primario eseguire questo script dalla console VMM per pulire le impostazioni per la macchina virtuale primaria. Nella console VMM fare clic sul pulsante PowerShell per aprire la console PowerShell di VMM. Sostituire SQLVM1 con il nome della macchina virtuale.

	     $vm = get-scvirtualmachine -Name "SQLVM1"
	     Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. Nel server VMM secondario eseguire questo script per pulire le impostazioni per la macchina virtuale secondaria:

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Remove-SCVirtualMachine -VM $vm -Force

3. Nel server VMM secondario, dopo aver eseguito lo script è necessario aggiornare le macchine virtuali nel server host Hyper-V in modo che la macchina virtuale secondaria venga rilevata di nuovo nella console VMM.
4. I passaggi sopra descritti cancelleranno solo le impostazioni di replica del server VMM. Se si vuole rimuovere la replica della macchina virtuale, è necessario eseguire i passaggi seguenti per le macchine virtuali primarie e secondarie. Eseguire lo script seguente per rimuovere la replica e sostituire SQLVM1 con il nome della macchina virtuale.

	    Remove-VMReplication –VMName “SQLVM1”


### Eseguire la pulizia manuale delle impostazioni di protezione (tra i siti VMM locali e Azure)

1. Nel server VMM di origine eseguire questo script per pulire le impostazioni per la macchina virtuale secondaria:

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. I passaggi sopra descritti cancelleranno solo le impostazioni di replica del server VMM. Dopo aver rimosso la replica dal server VMM, assicurarsi di rimuovere la replica per la macchina virtuale in esecuzione nel server host di Hyper-V di origine con questo script. Sostituire SQLVM1 con il nome di macchina virtuale e host01.contoso.com con il nome del server host Hyper-V.

	    $vmName = "SQLVM1"
	    $hostName  = "host01.contoso.com"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

### Eseguire la pulizia manuale delle impostazioni di protezione (tra i siti Hyper-V e Azure)

1. Nel server host Hyper-V di origine, per rimuovere la replica per la macchina virtuale usare questo script. Sostituire SQLVM1 con il nome della macchina virtuale.

	    $vmName = "SQLVM1"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

## Arrestare la protezione di una macchina virtuale VMware o un server fisico

Se si desidera arrestare la protezione di una macchina virtuale VMware o un server fisico è necessario rimuovere la protezione. A seconda come si rimuove la protezione potrebbe essere necessario rimuovere le impostazioni di protezione manualmente nel computer.

### Rimuovere la protezione

1. Nella scheda **Macchine virtuali** delle proprietà del cloud, selezionare la macchina virtuale > **Rimuovi**.
2. In **Rimuovi macchina virtuale** selezionare una delle opzioni:

	- **Disabilitare la protezione (utilizzare per l’analisi del ripristino e il ridimensionamento del volume)**: l’opzione è visibile e selezionabile solo se:
		- **Il volume della macchina virtuale è stato ridimensionato**: quando si ridimensiona un volume, la macchina virtuale passa in uno stato critico. In questo caso è necessario selezionare questa opzione che disabilita la protezione mantenendo i punti di ripristino in Azure. Quando si riabilita la protezione per la macchina virtuale, i dati per il volume ridimensionato verranno trasferiti in Azure.
		- **Un failover è stato eseguito**: dopo aver testato l'ambiente eseguendo un failover da macchine virtuali VMware locali o di server fisici in Azure, selezionare questa opzione per iniziare a proteggere le macchine virtuali locali nuovamente. Questa opzione disabilita ogni macchina virtuale e quindi sarà necessario riabilitare la protezione. Si noti che:
			- La disabilitazione della macchina virtuale con questa impostazione non riguarda la macchina virtuale di replica in Azure.
			- Non si deve disinstallare il servizio Mobility dalla macchina virtuale.
	
	- **Disabilitare la protezione**: se si abilita e si salva questa opzione la macchina virtuale non sarà protetta da Ripristino sito. Le impostazioni di protezione per la macchina virtuale verranno pulite automaticamente.
	- **Rimuovere dall'insieme di credenziali**: se si seleziona questa opzione la macchina virtuale verrà rimossa solo dall'insieme di credenziali di Ripristino sito. Le impostazioni di protezione locali per la macchina virtuale non saranno interessate. Per rimuovere le impostazioni nel computer e per rimuovere la macchina virtuale dalla sottoscrizione di Azure ed è necessario pulire le impostazioni tramite la disinstallazione del servizio Mobility. ![Rimuovere le opzioni](./media/site-recovery-manage-registration-and-protection/RegistrationProtection_RemoveVM.png)

<!---HONumber=Oct15_HO2-->