---
title: "Configurare gruppi di disponibilità Always On in macchine virtuali di Azure con PowerShell | Microsoft Docs"
description: "Questa esercitazione usa risorse che sono state create con il modello di distribuzione classico. Usare PowerShell per creare un gruppo di disponibilità Always On in Azure."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 73ee330c276263a21931a7b9a16cc33f86c58a26
ms.openlocfilehash: fa5467f38944435cc21d4d3ebd5a645c4f268b9b
ms.lasthandoff: 04/05/2017


---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Configurare gruppi di disponibilità Always On in macchine virtuali di Azure con PowerShell
> [!div class="op_single_selector"]
> * [Classica: interfaccia utente](../classic/portal-sql-alwayson-availability-groups.md)
> * [Classica: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Prima di iniziare, considerare che ora è possibile completare questa attività nel modello di gestione risorse di Azure. Per le nuove distribuzioni è consigliabile usare il modello di Azure Resource Manager. Vedere [gruppi di disponibilità Always On di SQL Server in macchine virtuali di Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Per le distribuzioni più recenti si consiglia di usare il modello di Resource Manager. Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica.

Le macchine virtuali di Azure possono consentire agli amministratori di database di abbassare i costi di un sistema di SQL Server a disponibilità elevata. Questa esercitazione illustra come implementare un gruppo di disponibilità tramite un end-to-end di SQL Server Always On in un ambiente Azure. Al termine dell'esercitazione la soluzione SQL Server AlwaysOn in Azure sarà composta dagli elementi seguenti:

* Una rete virtuale contenente più subnet, tra cui una subnet front-end e una back-end.
* Un controller di dominio con un dominio di Active Directory.
* Due macchine virtuali di SQL Server distribuite nella subnet di back-end e aggiunte al dominio di Active Directory.
* Un cluster di failover di Windows a 3 nodi con il modello di quorum Maggioranza dei nodi.
* Un gruppo di disponibilità con due repliche con commit sincrono di un database di disponibilità.

Questo scenario viene scelto per la semplicità, non per la convenienza o altri fattori di Azure. È possibile, ad esempio, ridurre il numero di macchine virtuali per un gruppo di disponibilità con due repliche per risparmiare ore di calcolo in Azure, usando il controller di dominio come condivisione file di controllo del quorum in un cluster di failover a 2 nodi. Questo metodo consente di ridurre di un'unità il numero di macchine virtuali rispetto alla configurazione precedente.

Questa esercitazione ha lo scopo di illustrare la procedura necessaria per configurare la soluzione descritta in precedenza senza approfondire i dettagli di ogni passaggio. Pertanto, anziché illustrare i passaggi di configurazione a livello di interfaccia utente grafica, vengono usati gli script di PowerShell per eseguire rapidamente ogni passaggio. Nell’esercitazione si presuppongono le condizioni seguenti:

* Si dispone già di un account Azure con la sottoscrizione della macchina virtuale.
* Sono stati installati i [cmdlet di Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Si ha già una conoscenza approfondita dei gruppi di disponibilità Always On per soluzioni locali. Per altre informazioni, vedere [Gruppi di disponibilità AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Connettersi alla sottoscrizione di Azure e creare la rete virtuale
1. In una finestra di PowerShell nel computer locale importare il modulo Azure, scaricare il file di impostazioni di pubblicazione nel computer e connettere la sessione di PowerShell alla sottoscrizione di Azure importando le impostazioni di pubblicazione scaricate.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    Il comando **Get-AzurePublishSettingsFile** genera automaticamente un certificato di gestione con Azure e lo scarica nel computer. Viene aperto un browser in automatico e viene richiesto di immettere le credenziali dell'account Microsoft per la sottoscrizione di Azure. Il file **.publishsettings** scaricato contiene tutte le informazioni necessarie per gestire la sottoscrizione di Azure. Dopo aver salvato il file in una directory locale, importarlo usando il comando **Import-AzurePublishSettingsFile**.

   > [!NOTE]
   > Il file .publishsettings contiene le credenziali (non codificate) usate per amministrare le sottoscrizioni e i servizi di Azure. La procedura consigliata di sicurezza per questo file consiste nell'archiviarlo temporaneamente all'esterno delle directory di origine, ad esempio nella cartella Raccolte\Documenti, e quindi eliminarlo al termine dell'importazione. Un utente malintenzionato che riesce ad accedere al file .publishsettings può modificare, creare ed eliminare i servizi di Azure.

2. Definire una serie di variabili con cui si creerà l'infrastruttura IT cloud.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Prestare attenzione a quanto segue per assicurarsi che i comandi funzionino correttamente in un secondo momento:

   * Le variabili **$storageAccountName** e **$dcServiceName** devono essere univoche perché vengono usate per identificare rispettivamente l'account di archiviazione cloud e il server cloud su Internet.
   * I nomi specificati per le variabili **$affinityGroupName** e **$virtualNetworkName** sono configurati nel documento di configurazione della rete virtuale che verrà usato in seguito.
   * **$sqlImageName** viene specificato il nome aggiornato dell'immagine della macchina virtuale in cui è contenuto SQL Server 2012 Service Pack 1 Enterprise Edition.
   * Per semplicità, **Contoso!000** corrisponde alla stessa password usata nel corso dell'esercitazione.

3. Creare un set di affinità.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Creare una rete virtuale importando un file di configurazione.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Il file di configurazione contiene il documento XML seguente. In breve, specifica una rete virtuale denominata **ContosoNET** nel gruppo di affinità denominato **ContosoAG**. Include lo spazio degli indirizzi **10.10.0.0/16** e dispone di due subnet, **10.10.1.0/24** e **10.10.2.0/24**, che sono rispettivamente la subnet anteriore e la subnet posteriore. Nella subnet anteriore è possibile inserire applicazioni client quali Microsoft SharePoint. Nella subnet posteriore è possibile inserire le macchine virtuali di SQL Server. Se si modificano le variabili **$affinityGroupName** e **$virtualNetworkName**, è necessario modificare anche i nomi corrispondenti.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. Creare un account di archiviazione associato al gruppo di affinità creato e impostarlo come account di archiviazione corrente nella sottoscrizione.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Creare il server controller di dominio nel nuovo servizio cloud e nel set di disponibilità.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Questi comandi inoltrati tramite pipe consente di eseguire le operazioni seguenti:

   * **New-AzureVMConfig** consente di creare una configurazione di macchina virtuale.
   * **Add-AzureProvisioningConfig** fornisce i parametri di configurazione di un server Windows autonomo.
   * **Add-AzureDataDisk** consente di aggiungere il disco dati che verrà usato per l'archiviazione dei dati di Active Directory, con l'opzione di memorizzazione nella cache impostata su Nessuna.
   * **New-AzureVM** consente di creare un nuovo servizio cloud e la nuova macchina virtuale di Azure nel nuovo servizio cloud.

7. Al termine del provisioning della nuova macchina virtuale, scaricare il file del desktop remoto nella directory di lavoro. Poiché il provisioning della nuova macchina virtuale di Azure richiede molto tempo, tramite il ciclo `while` verrà continuata l'esecuzione del polling della nuova macchina virtuale finché non è pronta per l'uso.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

A questo punto, il provisioning del server del controller di dominio è completato. Si configurerà quindi il dominio di Active Directory nel server del controller di dominio. Uscire dalla finestra di PowerShell aperta nel computer locale, che verrà usata di nuovo in un secondo momento per creare le due macchine virtuali di SQL Server.

## <a name="configure-the-domain-controller"></a>Configurare il controller di dominio
1. Connettersi al server del controller di dominio avviando il file del desktop remoto. Usare il nome utente dell'amministratore della macchina, AzureAdmin e la password **Contoso!000**specificata durante la creazione della nuova macchina virtuale.
2. Aprire una finestra di PowerShell in modalità amministratore.
3. Eseguire il comando **DCPROMO.EXE** per installare il dominio **corp.contoso.com**, con le directory dei dati nell'unità M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Al termine dell'esecuzione del comando, la macchina virtuale viene riavviata automaticamente.

4. Connettersi di nuovo al server del controller di dominio avviando il file del desktop remoto. Questa volta accedere come **CORP\Administrator**.
5. Aprire una finestra di PowerShell in modalità amministratore e importare il modulo Active Directory PowerShell usando il comando seguente:

        Import-Module ActiveDirectory

6. Eseguire i comandi indicati di seguito per aggiungere tre utenti al dominio.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** viene usato per configurare tutti gli elementi correlati alle istanze del servizio SQL Server, al cluster di failover e al gruppo di disponibilità. **CORP\SQLSvc1** e **CORP\SQLSvc2** vengono usati come account del servizio SQL Server per le due macchine virtuali di SQL Server.
7. Eseguire quindi i comandi indicati di seguito per concedere a **CORP\Install** le autorizzazioni per creare oggetti computer nel dominio.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Il GUID specificato in precedenza è il GUID per il tipo di oggetto computer. L'account **CORP\Install** deve disporre delle autorizzazioni **Leggi tutte le proprietà** e **Crea oggetti computer** per creare gli oggetti Active Directory per il cluster di failover. L'autorizzazione **Leggi tutte le proprietà** è già assegnata a CORP\Install per impostazione predefinita, quindi non è necessario concederla in modo esplicito. Per altre informazioni sulle autorizzazioni necessarie per creare il cluster di failover, vedere [Failover Cluster Step-by-Step Guide: Configuring Accounts in Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx) (Guida dettagliata del cluster di failover relativa alla configurazione di account in Active Directory).

    Dopo aver completato la configurazione di Active Directory e degli oggetti utente, si procederà alla creazione di due macchine virtuali di SQL Server che verranno aggiunte al dominio.

## <a name="create-the-sql-server-vms"></a>Creare le macchine virtuali di SQL Server
1. Continuare a usare la finestra di PowerShell aperta sul computer locale. Definire le variabili aggiuntive seguenti:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    L'indirizzo IP **10.10.0.4** viene in genere assegnato alla prima macchina virtuale creata nella subnet **10.10.0.0/16** della rete virtuale di Azure. È necessario verificare che corrisponda all'indirizzo del server del controller di dominio eseguendo **IPCONFIG**.
2. Eseguire i comandi seguenti inoltrati tramite pipe per creare la prima VM nel cluster di failover, denominata **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Si noti quanto segue riguardo al comando precedente:

   * **New-AzureVMConfig** consente di creare una configurazione di macchina virtuale con il nome del set di disponibilità desiderato. Le macchine virtuali successive verranno create con lo stesso nome del set di disponibilità, affinché siano aggiunte allo stesso set di disponibilità.
   * **Add-AzureProvisioningConfig** consente di aggiungere la macchina virtuale al dominio di Active Directory creato.
   * **Set AzureSubnet** consente di inserire la macchina virtuale nella subnet posteriore.
   * **New-AzureVM** consente di creare un nuovo servizio cloud e la nuova macchina virtuale di Azure nel nuovo servizio cloud. Il parametro **DnsSettings** specifica che l'indirizzo IP del server DNS per i server nel nuovo servizio cloud è **10.10.0.4**, ovvero l'indirizzo IP del server del controller di dominio. Questo parametro è necessario per consentire di aggiungere correttamente le nuove macchine virtuali nel servizio cloud al dominio di Active Directory. Senza questo parametro, è necessario configurare manualmente le impostazioni IPv4 nella macchina virtuale per usare il server del controller di dominio come server DNS primario dopo il provisioning della macchina virtuale e aggiungere quindi tale macchina virtuale al dominio di Active Directory.
3. Eseguire i comandi inoltrati tramite pipe indicati di seguito per creare le macchine virtuali di SQL Server, denominate **ContosoSQL1** e **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Si noti quanto segue riguardo i comandi precedenti:

   * **New-AzureVMConfig** usa lo stesso nome del set di disponibilità del server del controller di dominio e l'immagine di SQL Server 2012 Service Pack 1 Enterprise Edition nella raccolta di macchine virtuali. Consente inoltre di impostare il disco del sistema operativo sulla modalità sola lettura della cache (scrittura non consentita). È consigliabile eseguire la migrazione dei file di database in un disco dati separato collegato alla macchina virtuale e configurarlo senza lettura né scrittura nella cache. Il passaggio successivo consigliato consiste tuttavia nel rimuovere la scrittura nella cache sul disco del sistema operativo, non essendo possibile rimuovere la lettura della cache su tale disco.
   * **Add-AzureProvisioningConfig** consente di aggiungere la macchina virtuale al dominio di Active Directory creato.
   * **Set AzureSubnet** consente di inserire la macchina virtuale nella subnet posteriore.
   * **Add-AzureEndpoint** consente di aggiungere endpoint di accesso in modo da consentire alle applicazioni client di accedere a tali istanze dei servizi SQL Server su Internet. A ContosoSQL1 e ContosoSQL2 vengono assegnate porte diverse.
   * **New-AzureVM** consente di creare la nuova macchina virtuale di SQL Server nello stesso servizio cloud di ContosoQuorum. È necessario inserire le macchine virtuali nello stesso servizio cloud se si desidera includerle nello stesso set di disponibilità.
4. Attendere il provisioning di ogni macchina virtuale e per ciascuna macchina virtuale scaricare il relativo file del desktop remoto nella directory di lavoro. Il ciclo `for` si ripete nelle tre nuove macchine virtuali ed esegue i comandi all'interno delle parentesi graffe di livello principale per ognuna di esse.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    Completato il provisioning, le macchine virtuali di SQL Server sono in esecuzione, ma vengono installate con SQL Server usando le opzioni predefinite.

## <a name="initialize-the-failover-cluster-vms"></a>Inizializzare le macchine virtuali del cluster di failover
In questa sezione è necessario modificare i tre server da usare nel cluster di failover e nell'installazione di SQL Server. In particolare:

* Tutti i server: è necessario installare la funzionalità **Failover Clustering**.
* Tutti i server: è necessario aggiungere **CORP\Install** come **amministratore** del computer.
* Solo ContosoSQL1 e ContosoSQL2: è necessario aggiungere **CORP\Install** come ruolo **sysadmin** nel database predefinito.
* Solo ContosoSQL1 e ContosoSQL2: è necessario aggiungere **NT AUTHORITY\System** come account di accesso con le autorizzazioni seguenti:

  * Alterare eventuali gruppi di disponibilità
  * Connettersi a SQL
  * Visualizzare lo stato del server
* Solo ContosoSQL1 e ContosoSQL2: l protocollo **TCP** è già abilitato nella VM di SQL Server. Sarà tuttavia necessario aprire il firewall per l'accesso remoto di SQL Server.

A questo punto è possibile iniziare. A partire da **ContosoQuorum**, seguire questa procedura:

1. Connettersi a **ContosoQuorum** avviando i file desktop remoto. Usare il nome utente dell'amministratore della macchina, **AzureAdmin** e la password **Contoso!000** specificata durante la creazione della macchina virtuale.
2. Verificare che i computer siano stati aggiunti correttamente a **corp.contoso.com**.
3. Prima di procedere, attendere la fine dell'esecuzione delle attività di inizializzazione automatiche nell'ambito dell'installazione di SQL Server.
4. Aprire una finestra di PowerShell in modalità amministratore.
5. Installare la funzionalità Clustering di failover di Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Aggiungere **CORP\Install** come amministratore locale.

        net localgroup administrators "CORP\Install" /Add
7. Disconnettersi da ContosoQuorum. Le operazioni relative a questo server sono state completate.

        logoff.exe

Inizializzare quindi **ContosoSQL1** e **ContosoSQL2**. Seguire la procedura descritta di seguito, che è identica per entrambe le macchine virtuali di SQL Server.

1. Connettersi alle due macchine virtuali di SQL Server avviando i file del desktop remoto. Usare il nome utente dell'amministratore della macchina, **AzureAdmin** e la password **Contoso!000** specificata durante la creazione della macchina virtuale.
2. Verificare che i computer siano stati aggiunti correttamente a **corp.contoso.com**.
3. Prima di procedere, attendere la fine dell'esecuzione delle attività di inizializzazione automatiche nell'ambito dell'installazione di SQL Server.
4. Aprire una finestra di PowerShell in modalità amministratore.
5. Installare la funzionalità Clustering di failover di Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Aggiungere **CORP\Install** come amministratore locale.

        net localgroup administrators "CORP\Install" /Add
7. Importare il provider PowerShell per SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Aggiungere **CORP\Install** come ruolo sysadmin per l'istanza predefinita di SQL Server.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Aggiungere **NT AUTHORITY\System** come account di accesso con le tre autorizzazioni descritte in precedenza.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Aprire il firewall per l'accesso remoto di SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Disconnettersi da entrambe le macchine virtuali.

         logoff.exe

A questo punto, è possibile procedere con la configurazione del gruppo di disponibilità. Il provider PowerShell per SQL Server verrà usato per eseguire tutte le operazioni in **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Configurare il gruppo di disponibilità
1. Connettersi di nuovo a **ContosoSQL1** avviando i file del desktop remoto. Anziché accedere con l'account del computer, eseguire l'accesso con **CORP\Install**.
2. Aprire una finestra di PowerShell in modalità amministratore.
3. Definire le variabili seguenti:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. Importare il provider PowerShell per SQL Server.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Modificare l'account del servizio SQL Server per ContosoSQL1 in CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Modificare l'account del servizio SQL Server per ContosoSQL2 in CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Scaricare **CreateAzureFailoverCluster.ps1** dalla pagina [Create Failover Cluster for Always On Availability Groups in Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) (Creare il cluster di failover per i gruppi di disponibilità AlwaysOn in una VM di Azure) nella directory di lavoro locale. Usare lo script per creare un cluster di failover funzionale. Per informazioni importanti sull'interazione del servizio Clustering di failover di Windows con la rete di Azure, vedere [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Passare alla directory di lavoro e creare il cluster di failover con lo script scaricato.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Abilitare i gruppi di disponibilità Always On per le istanze predefinite di SQL Server in **ContosoSQL1** e **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. Creare una directory di backup e concedere le autorizzazioni per gli account del servizio SQL Server. Usare questa directory per preparare il database di disponibilità nella replica secondaria.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Creare un database in **ContosoSQL1** denominato **MyDB1**, eseguire un backup completo e un backup dei log e ripristinarli in **ContosoSQL2** con l'opzione **WITH NORECOVERY**.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Creare gli endpoint dei gruppi di disponibilità nelle macchine virtuali di SQL Server e impostare le autorizzazioni appropriate negli endpoint.

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. Creare le repliche di disponibilità.

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. Creare infine il gruppo di disponibilità e creare un join tra la replica secondaria e il gruppo di disponibilità.

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>Passaggi successivi
SQL Server Always On è stato correttamente implementato mediante la creazione di un gruppo di disponibilità in Azure. Per configurare un listener per questo gruppo di disponibilità, vedere [Configurare un listener ILB per gruppi di disponibilità Always On in Azure](../classic/ps-sql-int-listener.md).

Per altre informazioni sull'uso di SQL Server in Azure, vedere [Panoramica di SQL Server in macchine virtuali di Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

