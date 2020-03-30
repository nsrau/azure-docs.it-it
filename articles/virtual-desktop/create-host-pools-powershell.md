---
title: Creare il pool host di Windows Virtual Desktop PowerShell - AzureCreate Windows Virtual Desktop host pool PowerShell - Azure
description: Come creare un pool host in Windows Virtual Desktop con i cmdlet di PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b390c0beb20b7557294c18f889a0f41023513e2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246960"
---
# <a name="create-a-host-pool-with-powershell"></a>Creare un pool di host con PowerShell

I pool di host sono una raccolta di una o più macchine virtuali identiche all'interno di ambienti tenant di Desktop virtuale Windows. Ogni pool di host può contenere un gruppo di app con cui gli utenti possono interagire come farebbero in un desktop fisico.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Usare il client PowerShell per creare un pool hostUse your PowerShell client to create a host pool

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](/powershell/windows-virtual-desktop/overview/) da usare nella sessione di PowerShell, se non è già stato fatto.

Eseguire il cmdlet seguente per accedere all'ambiente Windows Virtual Desktop

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Eseguire quindi questo cmdlet per creare un nuovo pool host nel tenant di Windows Virtual Desktop:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Eseguire il cmdlet successivo per creare un token di registrazione per autorizzare un host di sessione a unirsi al pool host e salvarlo in un nuovo file nel computer locale. È possibile specificare per quanto tempo il token di registrazione è valido utilizzando il parametro -ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Successivamente, eseguire questo cmdlet per aggiungere gli utenti di Azure Active Directory al gruppo di app desktop predefinito per il pool host.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

Il cmdlet **Add-RdsAppGroupUser** non supporta l'aggiunta di gruppi di sicurezza e aggiunge un solo utente alla volta al gruppo di app. Se si desidera aggiungere più utenti al gruppo di app, eseguire nuovamente il cmdlet con i nomi principali utente appropriati.

Eseguire il cmdlet seguente per esportare il token di registrazione in una variabile, che verrà usata in seguito in Registrare le macchine virtuali nel [pool host di Windows Virtual Desktop.](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Creare macchine virtuali per il pool hostCreate virtual machines for the host pool

A questo punto è possibile creare una macchina virtuale di Azure che può essere aggiunta al pool host di Windows Virtual Desktop.Now you can create an Azure virtual machine that can be joined to your Windows Virtual Desktop host pool.

È possibile creare una macchina virtuale in diversi modi:You can create a virtual machine in multiple ways:

- [Creare una macchina virtuale da un'immagine della raccolta di AzureCreate a virtual machine from an Azure Gallery image](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Creare una macchina virtuale da un'immagine gestitaCreate a virtual machine from a managed image](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Creare una macchina virtuale da un'immagine non gestitaCreate a virtual machine from an unmanaged image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Se si distribuisce una macchina virtuale usando Windows 7 come sistema operativo host, il processo di creazione e distribuzione sarà leggermente diverso. Per ulteriori informazioni, vedere [Distribuire una macchina virtuale Windows 7 in Windows Virtual Desktop](deploy-windows-7-virtual-machine.md).

Dopo aver creato le macchine virtuali host della sessione, applicare una licenza di [Windows a una macchina virtuale host di sessione](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) per eseguire le macchine virtuali Windows o Windows Server senza pagare per un'altra licenza. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Preparare le macchine virtuali per le installazioni di agenti desktop virtuali windowsPrepare the virtual machines for Windows Virtual Desktop agent installations

È necessario eseguire le operazioni seguenti per preparare le macchine virtuali prima di installare gli agenti di Windows Virtual Desktop e registrare le macchine virtuali nel pool host di Windows Virtual Desktop:

- È necessario aggiungere il dominio al computer. In questo modo gli utenti di Windows Virtual Desktop in ingresso possono essere mappati dal proprio account Azure Active Directory al proprio account di Active Directory e di poter accedere correttamente alla macchina virtuale.
- È necessario installare il ruolo Host sessione Desktop remoto (RDSH) se la macchina virtuale esegue un sistema operativo Windows Server.You must install the Remote Desktop Session Host (RDSH) role if the virtual machine is running a Windows Server OS. Il ruolo RDSH consente agli agenti di Windows Virtual Desktop di eseguire correttamente l'installazione.

Per aggiungere correttamente un dominio, eseguire le operazioni seguenti in ogni macchina virtuale:To successfully domain-join, do the following things on each virtual machine:

1. [Connettersi alla macchina virtuale](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) con le credenziali fornite durante la creazione della macchina virtuale.
2. Nella macchina virtuale avviare **il Pannello di controllo** e selezionare **Sistema**.
3. Selezionare **Nome computer**, **Modifica impostazioni**e quindi **Cambia...**
4. Selezionare **Dominio** e quindi immettere il dominio di Active Directory nella rete virtuale.
5. Eseguire l'autenticazione con un account di dominio con privilegi per i computer di aggiunta al dominio.

    >[!NOTE]
    > Se si intende aggiungere le macchine virtuali a un ambiente Azure AD Domain Services (Azure AD DS), assicurarsi che l'utente di aggiunta a un dominio sia anche membro del [gruppo AAD DC Administrators](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registrare le macchine virtuali nel pool host di Windows Virtual Desktop

La registrazione delle macchine virtuali in un pool host di Windows Virtual Desktop è semplice come l'installazione degli agenti di Windows Virtual Desktop.

Per registrare gli agenti di Windows Virtual Desktop, eseguire le operazioni seguenti in ogni macchina virtuale:

1. [Connettersi alla macchina virtuale](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) con le credenziali fornite durante la creazione della macchina virtuale.
2. Scaricare e installare L'agente desktop virtuale di Windows.
   - Scaricare [L'agente desktop virtuale di Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Fare clic con il pulsante destro del mouse sul programma di installazione scaricato, scegliere **Proprietà**, **Sblocca**, quindi **OK**. Ciò consentirà al sistema di considerare attendibile il programma di installazione.
   - Eseguire il programma di installazione. Quando il programma di installazione richiede il token di registrazione, immettere il valore ottenuto dal cmdlet **Export-RdsRegistrationInfo.**
3. Scaricare e installare il bootloader di Windows Virtual Desktop Agent.
   - Scaricare il [bootloader di Windows Virtual Desktop Agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Fare clic con il pulsante destro del mouse sul programma di installazione scaricato, scegliere **Proprietà**, **Sblocca**, quindi **OK**. Ciò consentirà al sistema di considerare attendibile il programma di installazione.
   - Eseguire il programma di installazione.

>[!IMPORTANT]
>Per proteggere l'ambiente di Desktop virtuale Windows in Azure, è consigliabile non aprire la porta 3389 in ingresso nelle macchine virtuali. Desktop virtuale Windows non richiede una porta in ingresso 3389 per permettere agli utenti di accedere alle macchine virtuali del pool di host. Se è necessario aprire la porta 3389 per la risoluzione dei problemi, è consigliabile usare l'[accesso Just-In-Time alla VM](../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un pool host, è possibile popolarlo con RemoteApps.Now that you've made a host pool, you can populate it with RemoteApps. Per altre informazioni su come gestire le app in Desktop virtuale Windows, vedere l'esercitazione Gestire i gruppi di app.

> [!div class="nextstepaction"]
> [Esercitazione: Gestire i gruppi di app](./manage-app-groups.md)
