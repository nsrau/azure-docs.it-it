---
title: Rimuovere server e disabilitare la protezione | Documentazione Microsoft
description: Questo articolo descrive come annullare la registrazione di server da un insieme di credenziali di Site Recovery e per disabilitare la protezione per le macchine virtuali e i server fisici.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: ef1f31d5-285b-4a0f-89b5-0123cd422d80
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/28/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: f57c88cbace41af233f542880c6199b3e278700e
ms.openlocfilehash: c8d893dbac1a4f6cb3f05f857e186bca155e5865


---

# <a name="remove-servers-and-disable-protection"></a>Rimuovere server e disabilitare la protezione

Il servizio Azure Site Recovery contribuisce al miglioramento della strategia di continuità aziendale e ripristino di emergenza (BCDR), coordinando la replica, il failover e il ripristino di macchine virtuali e server fisici. È possibile replicare i computer in Azure o in un data center locale secondario. Per una rapida panoramica, leggere [Che cos'è Azure Site Recovery?](site-recovery-overview.md)

Questo articolo descrive come annullare la registrazione di server dall'insieme di credenziali di Servizi di ripristino nel portale di Azure e come disabilitare la protezione per le macchine virtuali protette da Site Recovery.

Per inviare commenti o domande, è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="unregister-a-connected-configuration-server"></a>Annullare la registrazione di un server di configurazione connesso

Se si replicano macchine virtuali VMware o server fisici Windows/Linux in Azure, è possibile annullare la registrazione di un server di configurazione connesso da un insieme di credenziali come indicato di seguito:

1. Disabilitare la protezione delle macchine. In **Elementi protetti** > **Elementi replicati** fare clic sulla macchina > **Elimina**.
2. Annullare l'associazione di tutti i criteri. In **Infrastruttura di Site Recovery** > **Per VMWare e computer fisici** > **Criteri di replica** fare doppio clic sui criteri associati. Fare doppio clic su server di configurazione > **Annulla associazione**.
3. Rimuovere qualsiasi server di destinazione master o di elaborazione locale aggiuntivo. In **Infrastruttura di Site Recovery** > **Per VMWare e computer fisici** > **Server di configurazione** fare clic con il pulsante destro del mouse sul server > **Elimina**.
4. Eliminare il server di configurazione.
5. Disinstallare manualmente il servizio di mobilità in esecuzione nel server di destinazione master che sarà un server separato o in esecuzione nel server di configurazione.
6. Disinstallare eventuali server di elaborazione aggiuntivi.
7. Disinstallare il server di configurazione.
8. Nel server di configurazione disinstallare l'istanza di MySQL installata da Site Recovery.
9. Eliminare la chiave ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery`` nel Registro di sistema del server di configurazione.

## <a name="unregister-a-unconnected-configuration-server"></a>Annullare la registrazione di un server di configurazione non connesso

Se si replicano macchine virtuali VMware o server fisici Windows/Linux in Azure, è possibile annullare la registrazione di un server di configurazione non connesso da un insieme di credenziali come indicato di seguito:

1. Disabilitare la protezione delle macchine. In **Elementi protetti** > **Elementi replicati** fare clic sulla macchina > **Elimina**. Selezionare **Interrompi gestione della macchina virtuale**.
2. Rimuovere qualsiasi server di destinazione master o di elaborazione locale aggiuntivo. In **Infrastruttura di Site Recovery** > **Per VMWare e computer fisici** > **Server di configurazione** fare clic con il pulsante destro del mouse sul server > **Elimina**.
3. Eliminare il server di configurazione.
4. Disinstallare manualmente il servizio di mobilità in esecuzione nel server di destinazione master che sarà un server separato o in esecuzione nel server di configurazione.
5. Disinstallare eventuali server di elaborazione aggiuntivi.
6. Disinstallare il server di configurazione.
7. Nel server di configurazione disinstallare l'istanza di MySQL installata da Site Recovery.
8. Eliminare la chiave ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery`` nel Registro di sistema del server di configurazione.

## <a name="unregister-a-connected-vmm-server"></a>Annullare la registrazione di un server VMM connesso

Come procedura consigliata, annullare la registrazione del server VMM quando è connesso ad Azure. In questo modo le impostazioni nei server VMM e in altri server VMM con cloud abbinati vengono rimosse correttamente. Si consiglia di rimuovere un server non connesso solo in caso di problema di connettività permanente. Se il server VMM non è connesso, è necessario eseguire manualmente uno script per rimuovere le impostazioni.

1. Arrestare la replica delle VM nei cloud presenti sul server VMM da rimuovere.
2. Eliminare i mapping di rete usati dai cloud nel server VMM da eliminare. In **Infrastruttura di Site Recovery** > **Per System Center VMM** > **Mapping di rete** fare clic con il pulsante destro del mouse sul mapping di rete > **Elimina**.
3. Annullare l'associazione dei criteri di replica dai cloud nel server VMM da rimuovere.  In **Infrastruttura di Site Recovery** > **Per System Center VMM** >  **Criteri di replica** fare doppio clic sui criteri associati. Fare clic con il pulsante destro del mouse sul cloud > **Annulla associazione**.
4. Eliminare il server VMM o il nodo VMM attivo. In **Infrastruttura di Site Recovery** > **Per System Center VMM** > **Server VMM** fare clic con il pulsante destro del mouse sul server > **Elimina**.
5. Disinstallare manualmente il Provider nel server VMM. Se si dispone di un cluster, eseguire la rimozione da tutti i nodi.
6. Se si esegue la replica in Azure, rimuovere manualmente l'agente di Servizi di ripristino di Microsoft dagli host Hyper-V nei cloud eliminati.



### <a name="unregister-an-unconnected-vmm-server"></a>Annullare la registrazione di un server VMM non connesso

1. Arrestare la replica delle VM nei cloud presenti sul server VMM da rimuovere.
2. Eliminare i mapping di rete usati dai cloud nel server VMM da eliminare. In **Infrastruttura di Site Recovery** > **Per System Center VMM** > **Mapping di rete** fare clic con il pulsante destro del mouse sul mapping di rete > **Elimina**.
3. Prendere nota dell'ID del server VMM.
4. Annullare l'associazione dei criteri di replica dai cloud nel server VMM da rimuovere.  In **Infrastruttura di Site Recovery** > **Per System Center VMM** >  **Criteri di replica** fare doppio clic sui criteri associati. Fare clic con il pulsante destro del mouse sul cloud > **Annulla associazione**.
5. Eliminare il server VMM o il nodo attivo. In **Infrastruttura di Site Recovery** > **Per System Center VMM** > **Server VMM** fare clic con il pulsante destro del mouse sul server > **Elimina**.
6. Nel server VMM scaricare ed eseguire lo [script di pulizia](http://aka.ms/asr-cleanup-script-vmm). Aprire PowerShell con l'opzione **Esegui come amministratore** per modificare i criteri di esecuzione per l'ambito predefinito (LocalMachine). Nello script specificare l'ID del server VMM da rimuovere. Lo script rimuove le informazioni sulla registrazione e l'associazione del cloud dal server.
5. Eseguire lo script di pulizia in tutti gli altri server VMM che contengono cloud associati ai cloud nel server VMM da rimuovere.
6. Eseguire lo script di pulizia in qualsiasi altro nodo cluster VMM passivo in cui è installato il Provider.
7. Disinstallare manualmente il Provider nel server VMM. Se si dispone di un cluster, eseguire la rimozione da tutti i nodi.
8. Se si esegue la replica in Azure, è possibile rimuovere manualmente l'agente di Servizi di ripristino di Microsoft dagli host Hyper-V nei cloud eliminati.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Annullare la registrazione di un host Hyper-V in un sito di Hyper-V

Gli host Hyper-V non gestiti da VMM vengono raccolti in un sito di Hyper-V. Rimuovere un host in un sito di Hyper-V nel modo seguente:

1. Disabilitare la replica per le VM Hyper-V presenti nell'host.
2. Annullare l'associazione dei criteri per il sito Hyper-V. In **Infrastruttura di Site Recovery** > **Per siti Hyper-V** >  **Criteri di replica** fare doppio clic sui criteri associati. Fare clic con il pulsante destro del mouse sul cloud > **Annulla associazione**.
3. Eliminare gli host Hyper-V. In **Infrastruttura di Site Recovery** > **Per System Center VMM** > **Host Hyper-V** fare clic con il pulsante destro del mouse sul server > **Elimina**.
4. Eliminare il sito Hyper-V dopo che sono stati rimossi tutti gli host dal sito. In **Infrastruttura di Site Recovery** > **Per System Center VMM** > **Siti Hyper-V** fare clic con il pulsante destro del mouse sul sito > **Elimina**.
5. Eseguire lo script seguente in ogni host Hyper-V che è stato rimosso. Lo script pulisce le impostazioni del server e ne annulla la registrazione dall'insieme di credenziali.


        `` pushd .
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
        popd``



## <a name="disable-protection-for-a-vmware-vm-or-physical-server"></a>Disabilitare la protezione per una VM VMware o un server fisico

1. In **Elementi protetti** > **Elementi replicati** fare clic sulla macchina > **Elimina**.
2. In **Rimuovi macchina virtuale** selezionare una delle opzioni seguenti:
    - **Disabilita protezione per la macchina virtuale (impostazione consigliata)**. Usare questa opzione per arrestare la replica della macchina. Le impostazioni di Site Recovery verranno rimosse automaticamente. Questa opzione verrà visualizzata nelle circostanze seguenti:
        - **Il volume della VM è stato ridimensionato**: quando si ridimensiona un volume, la macchina virtuale entra in uno stato critico. Selezionare questa opzione per disabilitare la protezione mantenendo i punti di ripristino in Azure. Quando si riabilita la protezione per la macchina virtuale, i dati per il volume ridimensionato verranno trasferiti in Azure.
        - **È stato eseguito recentemente un failover**: dopo avere eseguito un failover per testare l'ambiente, selezionare questa opzione per ripristinare la protezione delle macchine locali. Viene disabilitata ogni macchina virtuale ed è quindi necessario riabilitare la protezione. La disabilitazione della macchina con questa impostazione non riguarda la macchina virtuale di replica in Azure. Non disinstallare il servizio Mobility dalla macchina virtuale.
    - **Interrompi gestione della macchina virtuale**. Se si seleziona questa opzione, la macchina virtuale verrà rimossa solo dall'insieme di credenziali. Le impostazioni di protezione locali per la macchina virtuale non saranno interessate. Per rimuovere le impostazioni nella macchina e per rimuovere la macchina dalla sottoscrizione di Azure, è necessario rimuovere le impostazioni tramite la disinstallazione del servizio Mobility.

## <a name="disable-protection-for-a-hyper-v-vm-in-a-vmm-cloud"></a>Disabilitare la protezione per una VM Hyper-V in un cloud VMM

1. In **Elementi protetti** > **Elementi replicati** fare clic sulla macchina > **Elimina**.
2. In **Rimuovi macchina virtuale** selezionare una delle opzioni seguenti:

    - **Disabilita protezione per la macchina virtuale (impostazione consigliata)**. Usare questa opzione per arrestare la replica della macchina. Le impostazioni di Site Recovery verranno rimosse automaticamente.
    - **Interrompi gestione della macchina virtuale**. Se si seleziona questa opzione, la macchina virtuale verrà rimossa solo dall'insieme di credenziali. Le impostazioni di protezione locali per la macchina virtuale non saranno interessate. Per rimuovere le impostazioni nella macchina e per rimuovere la macchina dalla sottoscrizione di Azure è necessario pulire manualmente le impostazioni usando le istruzioni seguenti. Si noti che se si sceglie di eliminare la macchina virtuale e i relativi dischi rigidi, questi verranno rimossi dal percorso di destinazione.

### <a name="clean-up-protection-settings---replication-to-a-secondary-vmm-site"></a>Eseguire la pulizia delle impostazioni di protezione: replica in un sito VMM secondario

Se è stato selezionato **Interrompi gestione della macchina virtuale** e si sta eseguendo la replica in un sito secondario, eseguire questo script nel server primario per rimuovere le impostazioni per la macchina virtuale primaria. Nella console VMM fare clic sul pulsante PowerShell per aprire la console PowerShell di VMM. Sostituire SQLVM1 con il nome della macchina virtuale.

         ``$vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. Nel server VMM secondario eseguire questo script per pulire le impostazioni per la macchina virtuale secondaria:

        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force``
3. Nel server VMM secondario aggiornare le macchine virtuali nel server host Hyper-V in modo che la VM secondaria venga rilevata di nuovo nella console VMM.
4. I passaggi sopra descritti cancellano le impostazioni di replica nel server VMM. Se si vuole arrestare la replica per la macchina virtuale, eseguire lo script seguente nelle VM primarie e secondarie. Sostituire SQLVM1 con il nome della macchina virtuale.

        ``Remove-VMReplication –VMName “SQLVM1”``

### <a name="clean-up-protection-settings---replication-to-azure"></a>Eseguire la pulizia delle impostazioni di protezione: replica in Azure

1. Se è stato selezionato **Interrompi gestione della macchina virtuale** e si esegue la replica in Azure, eseguire questo script nel server VMM di origine usando PowerShell dalla console VMM.
        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. I passaggi sopra descritti cancellano le impostazioni di replica del server VMM. Per arrestare la replica per la macchina virtuale in esecuzione nel server host Hyper-V, eseguire questo script. Sostituire SQLVM1 con il nome della macchina virtuale e host01.contoso.com con il nome del server host Hyper-V.

        ``$vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)``


## <a name="disable-protection-for-a-hyper-v-vm-in-a-hyper-v-site"></a>Disabilitare la protezione per una VM Hyper-V in un sito Hyper-V

Usare questa procedura se si esegue la replica di VM Hyper-V in Azure senza un server VMM.

1. In **Elementi protetti** > **Elementi replicati** fare clic sulla macchina > **Elimina**.
2. In **Rimuovi macchina virtuale** è possibile selezionare le opzioni seguenti:

   - **Disabilita protezione per la macchina virtuale (impostazione consigliata)**. Usare questa opzione per arrestare la replica della macchina. Le impostazioni di Site Recovery verranno rimosse automaticamente.
   - **Interrompi gestione della macchina virtuale**. Se si seleziona questa opzione, la macchina virtuale verrà rimossa solo dall'insieme di credenziali. Le impostazioni di protezione locali per la macchina virtuale non saranno interessate. Per rimuovere le impostazioni nella macchina e per rimuovere la macchina virtuale dalla sottoscrizione di Azure, è necessario pulire manualmente le impostazioni. Se si sceglie di eliminare la macchina virtuale e i relativi dischi rigidi, essi verranno rimossi dal percorso di destinazione.
3. Se è stato selezionato **Interrompi gestione della macchina virtuale**, eseguire questo script nel server host Hyper-V di origine per rimuovere la replica per la macchina virtuale. Sostituire SQLVM1 con il nome della macchina virtuale.

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)



<!--HONumber=Jan17_HO1-->


