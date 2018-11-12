---
title: 'Esercitazione: Integrare una singola foresta Active Directory in Azure tramite la sincronizzazione degli hash delle password | Microsoft Docs'
description: Viene illustrato come configurare un ambiente ibrido di gestione delle identità usando la sincronizzazione degli hash delle password.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f830a27e9caf032443c2b27bdd2f95fa8069bcf2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247280"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>Esercitazione: Integrare una singola foresta Active Directory tramite la sincronizzazione degli hash delle password

![Create](media/tutorial-password-hash-sync/diagram.png)

L'esercitazione seguente illustrerà come creare un ambiente ibrido di gestione delle identità usando la sincronizzazione degli hash delle password.  Questo ambiente può essere usato a scopo di test oppure per acquisire familiarità con il funzionamento di una soluzione ibrida di gestione delle identità.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione sono necessari i requisiti seguenti
- Un computer in cui è installato [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview).  È consigliabile installarlo in un computer con [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) o in un computer con [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- Una [scheda di rete esterna](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) per consentire alla macchina virtuale di comunicare con Internet.
- Una [sottoscrizione di Azure](https://azure.microsoft.com/free)
- Una copia di Windows Server 2016

> [!NOTE]
> Questa esercitazione usa script di PowerShell in modo che sia possibile creare l'ambiente dell'esercitazione molto velocemente.  Ogni script usa variabili dichiarate all'inizio degli script.  È possibile ed è consigliabile modificare le variabili in base al proprio ambiente.
>
>L'uso degli script consente di creare un ambiente Active Directory generale prima di installare Azure AD Connect.  Sono rilevanti per tutte le esercitazioni.
>
> Le copie degli script di PowerShell usati in questa esercitazione sono disponibili in Github selezionando [questo collegamento](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
Per poter attivare e usare un ambiente ibrido di gestione delle identità, è prima necessario creare una macchina virtuale che servirà come server Active Directory locale.  Eseguire le operazioni seguenti:

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
3.  Sarà richiesto di premere un tasto qualsiasi per avviare il CD o il DVD. Procedere.
4. Nella schermata di avvio di Windows Server selezionare la lingua e fare clic su **Avanti**.
5. Fare clic su **Installa**.
6. Immettere il codice di licenza e fare clic su **Avanti**.
7. Accettare le condizioni di licenza e fare clic su **Avanti**.
8. Selezionare **Custom:  Install Windows Only (Advanced)** (Personalizzato: installare solo Windows (Avanzato))
9. Fare clic su **Avanti**
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

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $Password -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>Creare un utente di Active Directory per Windows Server
Ora che si ha l'ambiente di Active Directory, è necessario un account di test.  Questo account verrà creato nell'ambiente di Active Directory locale, poi verrà sincronizzato in Azure AD.  Eseguire le operazioni seguenti:

1. Aprire PowerShell ISE come amministratore.
2. Eseguire lo script seguente.

```powershell 
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
A questo punto è necessario creare un tenant di Azure Active Directory in modo da sincronizzare gli utenti nel cloud.  Per creare un nuovo tenant di Azure Active Directory Azure, eseguire le operazioni seguenti.

1. Selezionare il [portale di Azure](https://portal.azure.com) e accedere con un account che dispone di una sottoscrizione di Azure.
2. Selezionare l'**icona +** e cercare **Azure Active Directory**.
3. Selezionare **Azure Active Directory** dai risultati della ricerca.
4. Selezionare **Create**.</br>
![Creare](media/tutorial-password-hash-sync/create1.png)</br>
5. Specificare un **nome per l'organizzazione** e il **nome di dominio iniziale**. Selezionare quindi **Crea**. Verrà così creata la directory.
6. A questo punto, selezionare il collegamento **qui** per gestire la directory.

## <a name="create-a-global-administrator-in-azure-ad"></a>Creare un amministratore globale in Azure AD
Ora che si ha un tenant di Azure AD, si creerà un account di amministratore globale.  Questo account viene usato per creare l'account del connettore di Azure AD durante l'installazione di Azure AD Connect.  L'account del connettore di Azure AD viene usato per scrivere informazioni in Azure AD.   Per creare l'account di amministratore globale, eseguire le operazioni seguenti.

1.  In **Gestisci** selezionare **Utenti**.</br>
![Creare](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Selezionare **Tutti gli utenti** e selezionare **+Nuovo utente**.
3.  Specificare un nome e un nome utente per questo utente. Sarà l'amministratore globale per il tenant. Se necessario, è anche possibile modificare **Ruolo directory** in **Amministratore globale**. È anche possibile visualizzare la password temporanea. Al termine, seleziona **Crea**.</br>
![Creare](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Al termine dell'operazione, aprire un nuovo Web browser e accedere a myapps.microsoft.com usando il nuovo account di amministratore globale e la password temporanea.
5. Modificare la password dell'amministratore globale in una da ricordare.

## <a name="download-and-install-azure-ad-connect"></a>Scaricare e installare Azure AD Connect
A questo punto è possibile scaricare e installare Azure AD Connect.  Dopo averlo installato, si passerà all'installazione rapida.  Eseguire le operazioni seguenti:

1. Scaricare [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Trovare il file **AzureADConnect.msi**e farvi doppio clic.
3. Nella schermata iniziale, selezionare la casella che consente di accettare le condizioni di licenza e fare clic su **Continua**.  
4. Nella schermata Impostazioni rapide fare clic su **Usa impostazioni rapide**.</br>  
![Creare](media/tutorial-password-hash-sync/express1.png)</br>
5. Nella schermata Connessione ad Azure AD immettere il nome utente e la password dell'amministratore globale per Azure AD. Fare clic su **Avanti**.  
6. Nella schermata Connessione a Servizi di dominio Active Directory immettere il nome utente e la password di un account amministratore dell'organizzazione. Fare clic su **Avanti**.  
7. Nella schermata Pronto per la configurazione fare clic su **Installa**.
8. Al termine dell'installazione, fare clic su **Esci**.
9. Al termine dell'installazione, disconnettersi e accedere nuovamente prima di usare Synchronization Service Manager o l'editor delle regole di sincronizzazione.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verificare che gli utenti siano stati creati e che la sincronizzazione sia in esecuzione
A questo punto è necessario verificare che gli utenti presenti nella directory locale siano stati sincronizzati e che ora siano esistenti nel tenant di Azure AD.  Per essere completata, questa operazione può richiedere alcune ore.  Per verificare che gli utenti siano sincronizzati, eseguire le operazioni seguenti.


1. Selezionare il [portale di Azure](https://portal.azure.com) e accedere con un account che dispone di una sottoscrizione di Azure.
2. A sinistra selezionare **Azure Active Directory**
3. In **Gestisci** selezionare **Utenti**.
4. Verificare che i nuovi utenti siano visualizzati nel tenant</br>
![Sincronizzazione](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Testare l'accesso con uno degli utenti

1.  Passare a [http://myapps.microsoft.com](https://myapps.microsoft.com)
2. Accedere con uno degli account utente creati nel nuovo tenant.  Per accedere, sarà necessario usare il formato seguente: (user@domain.onmicrosoft.com). Usare la stessa password che l'utente usa per accedere in locale.</br>
![Verificare](media/tutorial-password-hash-sync/verify1.png)</br>

La configurazione di un ambiente ibrido di gestione delle identità è stata completata. A questo punto è possibile usare questo ambiente a scopo di test o per acquisire familiarità con le funzionalità di Azure.

## <a name="next-steps"></a>Passaggi successivi


- [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) 
- [Impostazioni rapide](how-to-connect-install-express.md)
- [Sincronizzazione degli hash delle password](how-to-connect-password-hash-synchronization.md)|
