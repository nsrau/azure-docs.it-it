---
title: Creare un pool di host in Desktop virtuale Windows con PowerShell - Azure
description: Come creare un pool di host in Desktop virtuale Windows con i cmdlet di PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 621d654e54d1f42a8714605c0c864b7653524f05
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046280"
---
# <a name="create-a-windows-virtual-desktop-host-pool-with-powershell"></a>Creare un pool host di desktop virtuali Windows con PowerShell

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Primavera 2020 con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa la versione Autunno 2019 di Desktop virtuale Windows senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md).
>
> L'aggiornamento di Primavera 2020 di Desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I pool di host sono una raccolta di una o più macchine virtuali identiche all'interno di ambienti tenant di Desktop virtuale Windows. Ogni pool di host può essere associato a più gruppi RemoteApp, a un gruppo di app desktop e a più host di sessione.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che siano già state seguite le istruzioni riportate in [Configurare il modulo di PowerShell](powershell-module.md).

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Usare il client di PowerShell per creare un pool di host

Eseguire il cmdlet seguente per accedere all'ambiente di Desktop virtuale Windows:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname>
```

Questo cmdlet creerà il pool di host, l'area di lavoro e il gruppo di app desktop. Registrerà anche il gruppo di app desktop nell'area di lavoro. È possibile creare un'area di lavoro con questo cmdlet o usare un'area di lavoro esistente.

Eseguire il cmdlet seguente per creare un token di registrazione che autorizza un host di sessione a unirsi al pool di host e lo salva in un nuovo file nel computer locale. È possibile specificare il periodo di validità del token di registrazione usando il parametro -ExpirationHours.

>[!NOTE]
>La durata per la scadenza del token deve essere non inferiore a un'ora e non superiore a un mese. Se si imposta *-ExpirationTime* al di fuori di tali limiti, il cmdlet non creerà il token.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Se ad esempio si vuole creare un token che scade tra due ore, eseguire questo cmdlet:

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Quindi eseguire questo cmdlet per aggiungere utenti di Azure Active Directory al gruppo di app desktop predefinito per il pool di host.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Eseguire il cmdlet seguente per aggiungere gruppi di utenti di Azure Active Directory al gruppo di app desktop predefinito per il pool di host:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+“-DAG”> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Eseguire il cmdlet seguente per esportare il token di registrazione in una variabile, che verrà usata più avanti in [Registrare le macchine virtuali nel pool di host di Desktop virtuale Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname>
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Creare macchine virtuali per il pool di host

A questo punto è possibile creare una macchina virtuale di Azure che può essere unita al pool di host di Desktop virtuale Windows.

È possibile creare una macchina virtuale in diversi modi:

- [Creare una macchina virtuale da un'immagine della raccolta di Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Creare una macchina virtuale da un'immagine gestita](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Creare una macchina virtuale da un'immagine non gestita](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Se si distribuisce una macchina virtuale usando Windows 7 come sistema operativo host, il processo di creazione e distribuzione sarà leggermente diverso. Per altre informazioni, vedere [Distribuire una macchina virtuale Windows 7 in Desktop virtuale Windows](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

Dopo aver creato le macchine virtuali dell'host di sessione, [applicare una licenza Windows a una macchina virtuale dell'host di sessione](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) per eseguire le macchine virtuali Windows o Windows Server senza pagare un licenza aggiuntiva.

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Preparare le macchine virtuali per le installazioni di agenti Desktop virtuale Windows

È necessario eseguire le operazioni seguenti per preparare le macchine virtuali prima di installare gli agenti Desktop virtuale Windows e registrare le macchine virtuali nel pool di host di Desktop virtuale Windows:

- È necessario aggiungere il computer a un dominio. Questo consente agli utenti di Desktop virtuale Windows in ingresso di essere mappati dal loro account di Azure Active Directory al loro account di Active Directory, e di ottenere l'accesso alla macchina virtuale.
- Se la macchina virtuale esegue un sistema operativo Windows Server, è necessario installare il ruolo Host sessione Desktop remoto (RDSH). Il ruolo Host sessione Desktop remoto consente l'installazione corretta degli agenti di Desktop virtuale Windows.

Per aggiungere correttamente il computer a un dominio, eseguire le operazioni seguenti in ogni macchina virtuale:

1. [Connettersi alla macchina virtuale](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) con le credenziali specificate durante la creazione della macchina virtuale.
2. Nella macchina virtuale aprire il **Pannello di controllo** e selezionare **Sistema**.
3. Selezionare **Nome computer**, selezionare **Modifica impostazioni** e quindi selezionare **Modifica**.
4. Selezionare **Dominio** e quindi immettere il dominio di Active Directory nella rete virtuale.
5. Eseguire l'autenticazione con un account di dominio che dispone dei privilegi per l'aggiunta di computer al dominio.

    >[!NOTE]
    > Se si intende aggiungere le macchine virtuali a un ambiente Azure AD Domain Services (Azure AD DS), assicurarsi che l'utente di aggiunta a un dominio sia anche membro del [gruppo AAD DC Administrators](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registrare le macchine virtuali nel pool di host di Desktop virtuale Windows

La registrazione delle macchine virtuali in un pool di host di Desktop virtuale Windows è semplice quanto l'installazione degli agenti Desktop virtuale Windows.

Per registrare gli agenti Desktop virtuale Windows, eseguire le operazioni seguenti in ogni macchina virtuale:

1. [Connettersi alla macchina virtuale](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) con le credenziali specificate durante la creazione della macchina virtuale.
2. Scaricare e installare l'agente Desktop virtuale Windows.
   - Scaricare l'[agente Desktop virtuale Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Eseguire il programma di installazione. Quando il programma di installazione richiede il token di registrazione, immettere il valore ottenuto dal cmdlet **Get-AzWvdRegistrationInfo** .
3. Scaricare e installare il bootloader dell'agente Desktop virtuale Windows.
   - Scaricare il [bootloader dell'agente Desktop virtuale Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Eseguire il programma di installazione.

>[!IMPORTANT]
>Per proteggere l'ambiente di Desktop virtuale Windows in Azure, è consigliabile non aprire la porta 3389 in ingresso nelle macchine virtuali. Desktop virtuale Windows non richiede una porta in ingresso 3389 per permettere agli utenti di accedere alle macchine virtuali del pool di host. Se è necessario aprire la porta 3389 per la risoluzione dei problemi, è consigliabile usare l'[accesso Just-In-Time alla VM](../security-center/security-center-just-in-time.md). È anche consigliabile non assegnare le macchine virtuali a un indirizzo IP pubblico.

## <a name="next-steps"></a>Passaggi successivi

Dopo la creazione del pool di host è possibile popolarlo con RemoteApp. Per altre informazioni su come gestire le app in Desktop virtuale Windows, vedere l'esercitazione Gestire i gruppi di app.

> [!div class="nextstepaction"]
> [Esercitazione: Gestire i gruppi di app](./manage-app-groups.md)
