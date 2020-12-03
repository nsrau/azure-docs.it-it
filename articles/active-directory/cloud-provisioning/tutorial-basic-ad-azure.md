---
title: 'Esercitazione: Ambiente Azure AD e Active Directory locale di base.'
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b6acaa76d21cb1321feb5466c4fb7a9bacfa846
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348110"
---
# <a name="tutorial-basic-active-directory-environment"></a>Esercitazione: Ambiente Active Directory di base

Questa esercitazione illustra come creare un ambiente Active Directory di base. 

![Diagramma che mostra un ambiente di base di Azure AD.](media/tutorial-single-forest/diagram1.png)

È possibile usare l'ambiente creato nell'esercitazione per testare vari aspetti degli scenari di identità ibrida e tale ambiente sarà un prerequisito per alcune delle esercitazioni.  Se si dispone già di un ambiente Active Directory esistente, è possibile usarlo come sostituto.  Queste informazioni sono destinate a singoli utenti che iniziano da zero.

Sezioni dell'esercitazione
## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione sono necessari i requisiti seguenti
- Un computer in cui è installato [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview).  È consigliabile installarlo in un computer con [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) o in un computer con [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- Una [scheda di rete esterna](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) per consentire alla macchina virtuale di comunicare con Internet.
- Una [sottoscrizione di Azure](https://azure.microsoft.com/free)
- Una copia di Windows Server 2016
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> Questa esercitazione usa script di PowerShell in modo che sia possibile creare l'ambiente dell'esercitazione molto velocemente.  Ogni script usa variabili dichiarate all'inizio degli script.  È possibile ed è consigliabile modificare le variabili in base al proprio ambiente.
>
>L'uso degli script consente di creare un ambiente Active Directory generale prima dell'installazione dell'agente di provisioning cloud di Azure AD Connect.  Sono rilevanti per tutte le esercitazioni.
>
> Le copie degli script di PowerShell usati in questa esercitazione sono disponibili in GitHub [qui](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
Per configurare un ambiente ibrido di gestione delle identità operativo, è prima necessario creare una macchina virtuale che servirà come server Active Directory locale.  Eseguire le operazioni seguenti:

1. Aprire PowerShell ISE come amministratore.
2. Eseguire lo script seguente.

    ```powershell
    #Declare variables
    $VMName = 'DC1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\DC1\DC1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
    ```

## <a name="complete-the-operating-system-deployment"></a>Completare la distribuzione del sistema operativo
Per concludere la creazione della macchina virtuale, è necessario completare l'installazione del sistema operativo.

1. Nella console di gestione di Hyper-V fare doppio clic sulla macchina virtuale
2. Fare clic sul pulsante Start.
3. Sarà richiesto di premere un tasto qualsiasi per avviare il CD o il DVD. Procedere.
4. Nella schermata di avvio di Windows Server selezionare la lingua e fare clic su **Avanti**.
5. Fare clic su **Installa**.
6. Immettere il codice di licenza e fare clic su **Avanti**.
7. Accettare le condizioni di licenza e fare clic su **Avanti**.
8. Selezionare **Custom:  Install Windows Only (Advanced)** (Personalizzato: installare solo Windows - Avanzato)
9. Scegliere **Avanti**
10. Dopo aver completato l'installazione, riavviare la macchina virtuale, accedere ed eseguire gli aggiornamenti di Windows per assicurarsi che la macchina virtuale sia aggiornata.  Installare gli aggiornate più recenti.

## <a name="install-active-directory-prerequisites"></a>Installare i prerequisiti di Active Directory
Ora che la macchina virtuale è disponibile, è necessario eseguire alcune operazioni prima di installare Active Directory.  È necessario rinominare la macchina virtuale, impostare un indirizzo IP statico e le informazioni DNS e installare gli strumenti di amministrazione remota del server.   Eseguire le operazioni seguenti:

1. Aprire PowerShell ISE come amministratore.
2. Eseguire lo script seguente.

    ```powershell
    #Declare variables
    $ipaddress = "10.0.1.117" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.117"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "DC1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    # Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```

## <a name="create-a-windows-server-ad-environment"></a>Creare un ambiente di Active Directory per Windows Server
Dopo che la macchina virtuale è stata creata e rinominata e ha un indirizzo IP statico, è possibile installare e configurare Active Directory Domain Services.  Eseguire le operazioni seguenti:

1. Aprire PowerShell ISE come amministratore.
2. Eseguire lo script seguente.

    ```powershell 
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "contoso.com"
    $DomaninNetBIOSName = "CONTOSO"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

## <a name="create-a-windows-server-ad-user"></a>Creare un utente di Active Directory per Windows Server
Ora che l'ambiente Active Directory è pronto, è necessario un account di test.  Questo account verrà creato nell'ambiente di Active Directory locale, poi verrà sincronizzato in Azure AD.  Eseguire le operazioni seguenti:

1. Aprire PowerShell ISE come amministratore.
2. Eseguire lo script seguente.

    ```powershell 
    # Filename:    4_CreateUser.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Allie"
    $Surname = "McCray"
    $Displayname = "Allie McCray"
    $Name = "amccray"
    $Password = "Pass1w0rd"
    $Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```


## <a name="create-an-azure-ad-tenant"></a>Come creare un tenant di Azure Active Directory
A questo punto occorre creare un tenant di Azure Active Directory per poter sincronizzare gli utenti nel cloud.  Per creare un nuovo tenant di Azure Active Directory Azure, eseguire le operazioni seguenti.

1. Selezionare il [portale di Azure](https://portal.azure.com) e accedere con un account che dispone di una sottoscrizione di Azure.
2. Selezionare l'**icona +** e cercare **Azure Active Directory**.
3. Selezionare **Azure Active Directory** dai risultati della ricerca.
4. Selezionare **Crea**.</br>
![Screenshot che mostra la pagina di Azure Active Directory nel portale di Azure.](media/tutorial-single-forest/create1.png)</br>
5. Specificare un **nome per l'organizzazione** e il **nome di dominio iniziale**. Selezionare quindi **Crea**. Verrà così creata la directory.
6. A questo punto, selezionare il collegamento **qui** per gestire la directory.

## <a name="create-a-global-administrator-in-azure-ad"></a>Creare un amministratore globale in Azure AD
Ora che si dispone di un tenant di Azure AD, verrà creato un account amministratore globale.  Per creare l'account di amministratore globale, eseguire le operazioni seguenti.

1.  In **Gestisci** selezionare **Utenti**.</br>
![Screenshot che mostra il menu "Panoramica" con l'opzione "Utenti" selezionata.](media/tutorial-single-forest/administrator1.png)</br>
2.  Selezionare **Tutti gli utenti** e selezionare **+Nuovo utente**.
3.  Specificare un nome e un nome utente per questo utente. Sarà l'amministratore globale per il tenant. Se necessario, è anche possibile modificare **Ruolo directory** in **Amministratore globale**. È anche possibile visualizzare la password temporanea. Al termine, seleziona **Crea**.</br>
![Creare](media/tutorial-single-forest/administrator2.png)</br>
4. Al termine dell'operazione, aprire un nuovo Web browser e accedere a myapps.microsoft.com usando il nuovo account di amministratore globale e la password temporanea.
5. Modificare la password dell'amministratore globale in una da ricordare.

## <a name="optional--additional-server-and-forest"></a>Facoltativo:  Server e foresta aggiuntivi
Di seguito è riportata una sezione facoltativa che illustra la procedura per la creazione di un server e/o di una foresta aggiuntiva,  che potrebbero essere necessari in alcune delle esercitazioni più avanzate, ad esempio [Creare un progetto pilota per il provisioning cloud di Azure AD Connect](tutorial-pilot-aadc-aadccp.md).

Se è necessario solo un server aggiuntivo, è possibile fermarsi dopo il passaggio **Creare la macchina virtuale** e aggiungere il server al dominio esistente creato in precedenza.  

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Aprire PowerShell ISE come amministratore.
2. Eseguire lo script seguente.

    ```powershell
    # Filename:    1_CreateVM_CP.ps1
    # Description: Creates a VM to be used in the tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. #This script is made available to you without any express, implied or statutory warranty, not even the implied warranty of merchantability or fitness for a particular purpose, or the warranty of title or non-infringement. The entire risk of the use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $VMName = 'CP1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\CP1\CP1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive
    ```

### <a name="complete-the-operating-system-deployment"></a>Completare la distribuzione del sistema operativo
Per concludere la creazione della macchina virtuale, è necessario completare l'installazione del sistema operativo.

1. Nella console di gestione di Hyper-V fare doppio clic sulla macchina virtuale
2. Fare clic sul pulsante Start.
3. Sarà richiesto di premere un tasto qualsiasi per avviare il CD o il DVD. Procedere.
4. Nella schermata di avvio di Windows Server selezionare la lingua e fare clic su **Avanti**.
5. Fare clic su **Installa**.
6. Immettere il codice di licenza e fare clic su **Avanti**.
7. Accettare le condizioni di licenza e fare clic su **Avanti**.
8. Selezionare **Custom:  Install Windows Only (Advanced)** (Personalizzato: installare solo Windows - Avanzato)
9. Scegliere **Avanti**
10. Dopo aver completato l'installazione, riavviare la macchina virtuale, accedere ed eseguire gli aggiornamenti di Windows per assicurarsi che la macchina virtuale sia aggiornata.  Installare gli aggiornate più recenti.

### <a name="install-active-directory-prerequisites"></a>Installare i prerequisiti di Active Directory
Ora che la macchina virtuale è disponibile, è necessario eseguire alcune operazioni prima di installare Active Directory.  È necessario rinominare la macchina virtuale, impostare un indirizzo IP statico e le informazioni DNS e installare gli strumenti di amministrazione remota del server.   Eseguire le operazioni seguenti:

1. Aprire PowerShell ISE come amministratore.
2. Eseguire lo script seguente.

    ```powershell
    # Filename:    2_ADPrep_CP.ps1
    # Description: Prepares your environment for Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $ipaddress = "10.0.1.118" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.118"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "CP1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    #Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```
### <a name="create-a-windows-server-ad-environment"></a>Creare un ambiente di Active Directory per Windows Server
Dopo che la macchina virtuale è stata creata e rinominata e ha un indirizzo IP statico, è possibile installare e configurare Active Directory Domain Services.  Eseguire le operazioni seguenti:

1. Aprire PowerShell ISE come amministratore.
2. Eseguire lo script seguente.

    ```powershell
    # Filename:    3_InstallAD_CP.ps1
    # Description: Creates an on-premises AD environment.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "fabrikam.com"
    $DomaninNetBIOSName = "FABRIKAM"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

### <a name="create-a-windows-server-ad-user"></a>Creare un utente di Active Directory per Windows Server
Ora che l'ambiente Active Directory è pronto, è necessario un account di test.  Questo account verrà creato nell'ambiente di Active Directory locale, poi verrà sincronizzato in Azure AD.  Eseguire le operazioni seguenti:

1. Aprire PowerShell ISE come amministratore.
2. Eseguire lo script seguente.

    ```powershell 
    # Filename:    4_CreateUser_CP.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Anna"
    $Surname = "Ringdal"
    $Displayname = "Anna Ringdal"
    $Name = "aringdal"
    $Password = "Pass1w0rd"
    $Identity = "CN=aringdal,CN=Users,DC=fabrikam,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```

## <a name="conclusion"></a>Conclusioni
A questo punto è stato creato un ambiente che può essere usato per le esercitazioni esistenti e per testare funzionalità aggiuntive offerte dal provisioning cloud.

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)