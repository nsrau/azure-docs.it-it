---
title: Creare un pool host per desktop virtuali Windows PowerShell-Azure
description: Come creare un pool host in un desktop virtuale Windows con i cmdlet di PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: a50a7966af8f6453441ac19c9dafac064015f9a2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607091"
---
# <a name="create-a-host-pool-with-powershell"></a>Creare un pool di host con PowerShell

I pool host sono una raccolta di una o più macchine virtuali identiche all'interno di ambienti tenant di desktop virtuali Windows. Ogni pool di host può contenere un gruppo di app con cui gli utenti possono interagire come farebbero in un desktop fisico.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Usare il client di PowerShell per creare un pool di host

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto.

Eseguire il cmdlet seguente per accedere all'ambiente desktop virtuale di Windows

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Eseguire quindi questo cmdlet per creare un nuovo pool di host nel tenant del desktop virtuale Windows:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Eseguire il cmdlet Next per creare un token di registrazione per autorizzare un host sessione ad aggiungere il pool host e salvarlo in un nuovo file nel computer locale. È possibile specificare il periodo di validità del token di registrazione utilizzando il parametro-ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

Eseguire questo cmdlet per aggiungere Azure Active Directory utenti al gruppo di app desktop predefinito per il pool host.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

Il cmdlet **Add-RdsAppGroupUser** non supporta l'aggiunta di gruppi di sicurezza e aggiunge un solo utente alla volta al gruppo di app. Se si desidera aggiungere più utenti al gruppo di app, eseguire di nuovo il cmdlet con i nomi dell'entità utente appropriati.

Eseguire il cmdlet seguente per esportare il token di registrazione in una variabile, che sarà utilizzata più avanti in [registrare le macchine virtuali nel pool host del desktop virtuale di Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Creazione di macchine virtuali per il pool host

A questo punto è possibile creare una macchina virtuale di Azure che può essere unita in join al pool di host per desktop virtuali Windows.

È possibile creare una macchina virtuale in diversi modi:

- [Creare una macchina virtuale da un'immagine della raccolta di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Creare una macchina virtuale da un'immagine gestita](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Creare una macchina virtuale da un'immagine non gestita](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>Se si distribuisce una macchina virtuale usando Windows 7 come sistema operativo host, il processo di creazione e distribuzione sarà leggermente diverso. Per ulteriori informazioni, vedere [la pagina relativa alla distribuzione di una macchina virtuale Windows 7 sul desktop virtuale di Windows](deploy-windows-7-virtual-machine.md).

Dopo aver creato le macchine virtuali host sessione, [applicare una licenza Windows a una VM host sessione](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) per eseguire le macchine virtuali Windows o Windows Server senza pagare per un'altra licenza. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Preparare le macchine virtuali per le installazioni di agenti desktop virtuali Windows

Per preparare le macchine virtuali, è necessario eseguire le operazioni seguenti prima di installare gli agenti desktop virtuali di Windows e registrare le macchine virtuali nel pool host del desktop virtuale Windows:

- È necessario aggiungere il computer a un dominio. Ciò consente di eseguire il mapping degli utenti di desktop virtuali Windows in ingresso dal proprio account di Azure Active Directory al proprio account di Active Directory e di consentire l'accesso alla macchina virtuale.
- Se la macchina virtuale esegue un sistema operativo Windows Server, è necessario installare il ruolo host sessione Desktop remoto (RDSH). Il ruolo RDSH consente di installare correttamente gli agenti desktop virtuali di Windows.

Per aggiungere correttamente un dominio, eseguire le operazioni seguenti in ogni macchina virtuale:

1. [Connettersi alla macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) con le credenziali fornite durante la creazione della macchina virtuale.
2. Nella macchina virtuale avviare il **Pannello di controllo** e selezionare **sistema**.
3. Selezionare **nome computer**, fare clic su **Modifica impostazioni**e quindi selezionare **modifica...**
4. Selezionare **dominio** e quindi immettere il dominio Active Directory nella rete virtuale.
5. Eseguire l'autenticazione con un account di dominio che disponga dei privilegi per i computer aggiunti al dominio.

    >[!NOTE]
    > Se si intende aggiungere le macchine virtuali a un ambiente Azure AD Domain Services (Azure AD DS), assicurarsi che l'utente di aggiunta a un dominio sia anche membro del [gruppo AAD DC Administrators](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registrare le macchine virtuali nel pool host del desktop virtuale Windows

La registrazione delle macchine virtuali in un pool host di desktop virtuali Windows è semplice quanto l'installazione degli agenti desktop virtuali di Windows.

Per registrare gli agenti desktop virtuali Windows, eseguire le operazioni seguenti in ogni macchina virtuale:

1. [Connettersi alla macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) con le credenziali fornite durante la creazione della macchina virtuale.
2. Scaricare e installare l'agente desktop virtuale di Windows.
   - Scaricare l' [agente desktop virtuale di Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Fare clic con il pulsante destro del mouse sul programma di installazione scaricato, scegliere **Proprietà**, selezionare **Sblocca**e quindi fare clic su **OK**. Questo consentirà al sistema di considerare attendibile il programma di installazione.
   - Eseguire il programma di installazione. Quando il programma di installazione richiede il token di registrazione, immettere il valore ottenuto dal cmdlet **Export-RdsRegistrationInfo** .
3. Scaricare e installare il bootloader dell'agente desktop virtuale di Windows.
   - Scaricare il [bootloader dell'agente desktop virtuale di Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Fare clic con il pulsante destro del mouse sul programma di installazione scaricato, scegliere **Proprietà**, selezionare **Sblocca**e quindi fare clic su **OK**. Questo consentirà al sistema di considerare attendibile il programma di installazione.
   - Eseguire il programma di installazione.

>[!IMPORTANT]
>Per proteggere l'ambiente di Desktop virtuale Windows in Azure, è consigliabile non aprire la porta 3389 in ingresso nelle macchine virtuali. Desktop virtuale Windows non richiede una porta in ingresso 3389 per permettere agli utenti di accedere alle macchine virtuali del pool di host. Se è necessario aprire la porta 3389 per la risoluzione dei problemi, è consigliabile usare l'[accesso Just-In-Time alla VM](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato un pool di host, è possibile popolarlo con RemoteApp. Per altre informazioni su come gestire le app in Desktop virtuale Windows, vedere l'esercitazione Gestire i gruppi di app.

> [!div class="nextstepaction"]
> [Esercitazione: Gestire i gruppi di app](./manage-app-groups.md)
