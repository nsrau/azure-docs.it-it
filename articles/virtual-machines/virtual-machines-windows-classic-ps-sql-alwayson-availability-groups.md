<properties
    pageTitle="Configurare gruppi di disponibilità AlwaysOn in macchine virtuali di Azure con PowerShell"
    description="Questa esercitazione usa le risorse create con il modello di distribuzione classica e usa PowerShell per creare un gruppo di disponibilità AlwaysOn in Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />


# <a name="configure-always-on-availability-group-in-azure-vm-with-powershell"></a>Configurare gruppi di disponibilità AlwaysOn in macchine virtuali di Azure con PowerShell

> [AZURE.SELECTOR]
- [Resource Manager: modello](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Resource Manager: manuale](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Classica: interfaccia utente](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Classica: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Le macchine virtuali di Azure possono consentire agli amministratori di database di implementare un sistema di SQL Server a disponibilità elevata con costi inferiori. Questa esercitazione illustra come implementare un gruppo di disponibilità end-to-end basato su SQL Server AlwaysOn in un ambiente Azure. Al termine dell'esercitazione la soluzione SQL Server AlwaysOn in Azure sarà composta dagli elementi seguenti:

- Una rete virtuale contenente più subnet, tra cui una subnet front-end e una back-end

- Un controller di dominio con un dominio di Active Directory (AD)

- Due macchine virtuali di SQL Server distribuite nella subnet di back-end e aggiunte al dominio AD

- Un cluster WSFC a 3 nodi con il modello di quorum Maggioranza dei nodi

- Un gruppo di disponibilità con due repliche con commit sincrono di un database di disponibilità

Questo scenario viene scelto per la semplicità, non per la convenienza o altri fattori di Azure. È possibile, ad esempio, ridurre il numero di macchine virtuali per un gruppo di disponibilità con due repliche per risparmiare ore di calcolo in Azure, usando il controller di dominio come condivisione file di controllo del quorum in un cluster WSFC a 2 nodi. Questo metodo consente di ridurre di un'unità il numero di macchine virtuali rispetto alla configurazione precedente.

Questa esercitazione ha lo scopo di illustrare la procedura necessaria per configurare la soluzione descritta in precedenza senza approfondire i dettagli di ogni passaggio. Pertanto, anziché illustrare i passaggi di configurazione a livello di interfaccia utente grafica, vengono usati gli script di PowerShell per eseguire rapidamente ogni passaggio. Si presuppone quanto segue:

- Si dispone già di un account Azure con la sottoscrizione della macchina virtuale.

- Sono stati installati i [cmdlet di Azure PowerShell](../powershell-install-configure.md).

- Si ha già una conoscenza approfondita dei gruppi di disponibilità AlwaysOn per soluzioni locali. Per altre informazioni, vedere [Gruppi di disponibilità AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Connettersi alla sottoscrizione di Azure e creare la rete virtuale

1. In una finestra di PowerShell nel computer locale importare il modulo Azure, scaricare un file di impostazioni di pubblicazione nel computer e connettere la sessione di PowerShell alla sottoscrizione di Azure importando le impostazioni di pubblicazione scaricate.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    Il comando **Get AzurePublishgSettingsFile** genera automaticamente un certificato di gestione con Azure e lo scarica nel computer. Verrà aperto un browser e verrà richiesto di immettere le credenziali dell'account Microsoft per la sottoscrizione di Azure. Il file **publishsettings** scaricato contiene tutte le informazioni necessarie per gestire la sottoscrizione di Azure. Dopo aver salvato il file in una directory locale, importarlo usando il comando **Import-AzurePublishSettingsFile** .

    >[AZURE.NOTE] Nel file publishsettings sono incluse le credenziali (non codificate) usate per amministrare i servizi e le sottoscrizioni di Azure. La procedura consigliata di sicurezza per questo file consiste nell'archiviarlo temporaneamente all'esterno delle directory di origine, ad esempio nella cartella Raccolte\Documenti, e quindi di eliminarlo al termine dell'importazione. Un utente malintenzionato che accede al file publishsettings può modificare, creare ed eliminare i servizi di Azure.

1. Definire una serie di variabili con cui si creerà l'infrastruttura IT cloud.

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

    - Le variabili **$storageAccountName** e **$dcServiceName** devono essere univoche perché vengono usate per identificare rispettivamente l'account di archiviazione cloud e il server cloud su Internet.

    - I nomi specificati per le variabili **$affinityGroupName** e **$virtualNetworkName** sono configurati nel documento di configurazione della rete virtuale che verrà usato in seguito.

    - **$sqlImageName** viene specificato il nome aggiornato dell'immagine della macchina virtuale in cui è contenuto SQL Server 2012 Service Pack 1 Enterprise Edition.

    - Per semplicità, **Contoso!000** corrisponde alla stessa password usata nel corso dell'esercitazione.

1. Creare un set di affinità.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

1. Creare una rete virtuale importando un file di configurazione.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Il file di configurazione contiene il documento XML seguente. In breve, specifica una rete virtuale denominata **ContosoNET** nel gruppo di affinità denominato **ContosoAG** con lo spazio di indirizzi **10.10.0.0/16** e due subnet, **10.10.1.0/24** e **10.10.2.0/24**, che corrispondono rispettivamente alle subnet front-end e back-end. Nella subnet font-end è possibile inserire applicazioni client, ad esempio Microsoft SharePoint, mentre nella subnet back-end si inseriranno le macchine virtuali di SQL Server. Se si modificano le variabili **$affinityGroupName** e **$virtualNetworkName**, è necessario modificare anche i nomi corrispondenti.

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

1. Creare un account di archiviazione associato al set di affinità creato e impostarlo come account di archiviazione corrente nella sottoscrizione.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

1. Creare il server controller di dominio nel nuovo servizio cloud e nel set di disponibilità.

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

    Questa serie di comandi inoltrati tramite pipe consente di eseguire queste le operazioni:

    - **New-AzureVMConfig** consente di creare una configurazione di macchina virtuale.

    - **Add-AzureProvisioningConfig** fornisce i parametri di configurazione di un server Windows autonomo.

    - **Add-AzureDataDisk** consente di aggiungere il disco dati che verrà usato per l'archiviazione dei dati di Active Directory, con l'opzione di memorizzazione nella cache impostata su None.

    - **New-AzureVM** consente di creare un nuovo servizio cloud e la nuova macchina virtuale di Azure nel nuovo servizio cloud.

1. Al termine del provisioning della nuova macchina virtuale, scaricare il file del desktop remoto nella directory di lavoro. Poiché il provisioning della nuova macchina virtuale di Azure richiede molto tempo, tramite il ciclo While verrà continuata l'esecuzione del polling della nuova macchina virtuale finché non è pronta per l'uso.

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

1. Connettersi al server del controller di dominio avviando il file del desktop remoto. Usare il nome utente dell'amministratore della macchina AzureAdmin e la password **Contoso!000**specificata durante la creazione della macchina virtuale.

1. Aprire una finestra di PowerShell in modalità amministratore.

1. Eseguire il comando **DCPROMO.EXE** per installare il dominio **corp.contoso.com**, con le directory dei dati nell'unità M.

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

1. Connettersi di nuovo al server del controller di dominio avviando il file del desktop remoto. Questa volta accedere come **CORP\Administrator**.

1. Aprire una finestra di PowerShell in modalità amministratore e importare il modulo Active Directory PowerShell usando il comando seguente:

        Import-Module ActiveDirectory

1. Eseguire i comandi indicati di seguito per aggiungere tre utenti al dominio.

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

    **CORP\Install** viene usato per configurare tutti gli elementi correlati alle istanze del servizio SQL Server, al cluster WSFC e al gruppo di disponibilità. **CORP\SQLSvc1** e **CORP\SQLSvc2** vengono usati come account del servizio SQL Server per le due macchine virtuali di SQL Server.

1. Eseguire quindi i comandi indicati di seguito per concedere a **CORP\Install** le autorizzazioni per creare oggetti computer nel dominio.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Il GUID specificato in precedenza è il GUID per il tipo di oggetto computer. L'account **CORP\Install** deve disporre delle autorizzazioni **Leggi tutte le proprietà** e **Crea oggetti Computer** per creare gli oggetti Active Directory per il cluster WSFC. L'autorizzazione **Leggi tutte le proprietà** è già assegnata a CORP\Install per impostazione predefinita, quindi non è necessario concederla in modo esplicito. Per altre informazioni sulle autorizzazioni necessarie per creare il cluster WSFC, vedere la [guida dettagliata del cluster di failover relativa alla configurazione di account in Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

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

1. Eseguire i comandi seguenti inoltrati tramite pipe per creare la prima macchina virtuale nel cluster WSFC, denominata **ContosoQuorum**:

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

    - **New-AzureVMConfig** consente di creare una configurazione di macchina virtuale con il nome del set di disponibilità desiderato. Le macchine virtuali successive verranno create con lo stesso nome del set di disponibilità, affinché siano aggiunte allo stesso set di disponibilità.

    - **Add-AzureProvisioningConfig** consente di aggiungere la macchina virtuale al dominio di Active Directory creato.

    - **Set AzureSubnet** consente di inserire la macchina virtuale nella subnet Back.

    - **New-AzureVM** consente di creare un nuovo servizio cloud e la nuova macchina virtuale di Azure nel nuovo servizio cloud. Il parametro **DnsSettings** specifica che l'indirizzo IP del server DNS per i server nel nuovo servizio cloud è **10.10.0.4**, ovvero l'indirizzo IP del server del controller di dominio. Questo parametro è necessario per consentire di aggiungere correttamente le nuove macchine virtuali nel servizio cloud al dominio di Active Directory. Senza questo parametro, è necessario configurare manualmente le impostazioni IPv4 nella macchina virtuale per usare il server del controller di dominio come server DNS primario dopo il provisioning della macchina virtuale e aggiungere quindi tale macchina virtuale al dominio di Active Directory.

1. Eseguire i comandi inoltrati tramite pipe indicati di seguito per creare le macchine virtuali di SQL Server, denominate **ContosoSQL1** e **ContosoSQL2**.

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

    - **New-AzureVMConfig** vengono usati lo stesso nome del set di disponibilità del server del controller di dominio e l'immagine di SQL Server 2012 Service Pack 1 Enterprise Edition nella raccolta di macchine virtuali. Consente inoltre di impostare il disco del sistema operativo sulla modalità sola lettura della cache (scrittura non consentita). È consigliabile eseguire la migrazione dei file di database in un disco dati separato collegato alla macchina virtuale e configurarlo senza lettura né scrittura nella cache. Il passaggio successivo consigliato consiste tuttavia nel rimuovere la scrittura nella cache sul disco del sistema operativo, non essendo possibile rimuovere la lettura della cache su tale disco.

    - **Add-AzureProvisioningConfig** consente di aggiungere la macchina virtuale al dominio di Active Directory creato.

    - **Set AzureSubnet** consente di inserire la macchina virtuale nella subnet Back.

    - **Add-AzureEndpoint** consente di aggiungere endpoint di accesso in modo da consentire alle applicazioni client di accedere a tali istanze dei servizi SQL Server su Internet. A ContosoSQL1 e ContosoSQL2 vengono assegnate porte diverse.

    - **New-AzureVM** consente di creare la nuova macchina virtuale di SQL Server nello stesso servizio cloud di ContosoQuorum. È necessario inserire le macchine virtuali nello stesso servizio cloud se si desidera includerle nello stesso set di disponibilità.

1. Al termine del provisioning di ogni macchina virtuale, scaricare il relativo file del desktop remoto nella directory di lavoro. Tramite i cicli for vengono scorse le tre nuove macchine virtuali e vengono eseguiti i comandi all'interno delle parentesi graffe di livello principale per ognuna di esse.

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

## <a name="initialize-the-wsfc-cluster-vms"></a>Inizializzare le macchine virtuali del cluster WSFC

In questa sezione è necessario modificare i tre server da usare nel cluster WSFC e nell'installazione di SQL Server. In particolare:

- (Tutti i server) È necessario installare la funzionalità **Clustering di failover** .

- (Tutti i server) È necessario aggiungere **CORP\Install** come **amministratore** del computer.

- (Solo ContosoSQL1 e ContosoSQL2) È necessario aggiungere **CORP\Install** come ruolo **sysadmin** nel database predefinito.

- (Solo ContosoSQL1 e ContosoSQL2) È necessario aggiungere **NT AUTHORITY\System** come account di accesso con le autorizzazioni seguenti:

    - Alterare eventuali gruppi di disponibilità

    - Connettersi a SQL

    - Visualizzare lo stato del server

- (Solo ContosoSQL1 e ContosoSQL2) Il protocollo **TCP** è già abilitato nella macchina virtuale SQL Server. Sarà tuttavia necessario aprire il firewall per l'accesso remoto di SQL Server.

A questo punto è possibile iniziare. A partire da **ContosoQuorum**, seguire questa procedura:

1. Connettersi a **ContosoQuorum** avviando i file desktop remoto. Usare il nome utente **AzureAdmin** dell'amministratore della macchina e la password **Contoso!000** specificata durante la creazione delle macchine virtuali.

1. Verificare che i computer siano stati aggiunti correttamente a **corp.contoso.com**.

1. Prima di procedere, attendere la fine dell'esecuzione delle attività di inizializzazione automatiche nell'ambito dell'installazione di SQL Server.

1. Aprire una finestra di PowerShell in modalità amministratore.

1. Installare la funzionalità Clustering di failover di Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. Aggiungere **CORP\Install** come amministratore locale.

        net localgroup administrators "CORP\Install" /Add

1. Disconnettersi da ContosoQuorum. Le operazioni relative a questo server sono state completate.

        logoff.exe

Inizializzare quindi **ContosoSQL1** e **ContosoSQL2**. Seguire la procedura descritta di seguito, che è identica per entrambe le macchine virtuali di SQL Server.

1. Connettersi alle due macchine virtuali di SQL Server avviando i file del desktop remoto. Usare il nome utente **AzureAdmin** dell'amministratore della macchina e la password **Contoso!000** specificata durante la creazione delle macchine virtuali.

1. Verificare che i computer siano stati aggiunti correttamente a **corp.contoso.com**.

1. Prima di procedere, attendere la fine dell'esecuzione delle attività di inizializzazione automatiche nell'ambito dell'installazione di SQL Server.

1. Aprire una finestra di PowerShell in modalità amministratore.

1. Installare la funzionalità Clustering di failover di Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. Aggiungere **CORP\Install** come amministratore locale.

        net localgroup administrators "CORP\Install" /Add

1. Importare il provider PowerShell per SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking

1. Aggiungere **CORP\Install** come ruolo sysadmin per l'istanza predefinita di SQL Server.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."

1. Aggiungere **NT AUTHORITY\System** come account di accesso con le tre autorizzazioni descritte in precedenza.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."

1. Aprire il firewall per l'accesso remoto di SQL Server.

        netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP

1. Disconnettersi da entrambe le macchine virtuali.

        logoff.exe

A questo punto, è possibile procedere con la configurazione del gruppo di disponibilità. Il provider PowerShell per SQL Server verrà usato per eseguire tutte le operazioni in **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Configurare il gruppo di disponibilità

1. Connettersi di nuovo a **ContosoSQL1** avviando i file del desktop remoto. Anziché accedere con l'account del computer, eseguire l'accesso con **CORP\Install**.

1. Aprire una finestra di PowerShell in modalità amministratore.

1. Definire le variabili seguenti:

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

1. Importare il provider PowerShell per SQL Server.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking

1. Modificare l'account del servizio SQL Server per ContosoSQL1 in CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Modificare l'account del servizio SQL Server per ContosoSQL2 in CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Scaricare **CreateAzureFailoverCluster.ps1** dalla pagina [Create WSFC Cluster for AlwaysOn Availability Groups in Windows Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) (Creare il cluster WSFC per i gruppi di disponibilità AlwaysOn in una VM di Azure) nella directory di lavoro locale. Usare lo script per creare un cluster WSFC funzionale. Per informazioni importanti sull'interazione di WSFC con la rete di Azure, vedere [Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md).

1. Passare alla directory di lavoro e creare il cluster WSFC con lo script scaricato.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"

1. Abilitare i gruppi di disponibilità AlwaysOn per le istanze predefinite di SQL Server in **ContosoSQL1** e **ContosoSQL2**.

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

1. Creare una directory di backup e concedere le autorizzazioni per gli account del servizio SQL Server. Usare questa directory per preparare il database di disponibilità nella replica secondaria.

        $backup = "C:\backup"
        New-Item $backup -ItemType directory
        net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
        icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")

1. Creare un database in **ContosoSQL1** denominato **MyDB1**, eseguire un backup completo e un backup dei log e ripristinarli in **ContosoSQL2** con l'opzione **WITH NORECOVERY**.

        Invoke-SqlCmd -Query "CREATE database $db"
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery

1. Creare gli endpoint dei gruppi di disponibilità nelle macchine virtuali di SQL Server e impostare le autorizzazioni appropriate negli endpoint.

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

1. Creare le repliche di disponibilità.

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

1. Creare infine il gruppo di disponibilità e creare un join tra la replica secondaria e il gruppo di disponibilità.

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
SQL Server AlwaysOn è stato correttamente implementato mediante la creazione di un gruppo di disponibilità in Azure. Per configurare un listener per questo gruppo di disponibilità, vedere [Configurare un listener ILB per gruppi di disponibilità AlwaysOn in Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Per altre informazioni sull'uso di SQL Server in Azure, vedere [SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).



<!--HONumber=Oct16_HO2-->


