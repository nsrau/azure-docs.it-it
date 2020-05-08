---
title: Creare un pool host per desktop virtuali Windows PowerShell-Azure
description: Come creare un pool host in un desktop virtuale Windows con i cmdlet di PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 474eb4f5247aeb77edce0ebfde1611bf2deef493
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930404"
---
# <a name="create-a-host-pool-with-powershell"></a>Creare un pool di host con PowerShell

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Spring 2020 con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa la versione 2019 del desktop virtuale di Windows senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md).
>
> L'aggiornamento di Spring 2020 per desktop virtuale di Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile usarla per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I pool di host sono una raccolta di una o più macchine virtuali identiche all'interno di ambienti tenant di Desktop virtuale Windows. Ogni pool di host può essere associato a più gruppi RemoteApp, a un gruppo di app desktop e a più host di sessione.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che sia già stata seguita la procedura [illustrata in configurare il modulo PowerShell](powershell-module.md).

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Usare il client di PowerShell per creare un pool di host

Eseguire il cmdlet seguente per accedere all'ambiente di Desktop virtuale Windows:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname> 
```

Questo cmdlet creerà il pool host, l'area di lavoro e il gruppo di app desktop. Inoltre, registrerà il gruppo di app desktop nell'area di lavoro. È possibile creare un'area di lavoro con questo cmdlet o usare un'area di lavoro esistente. 

Eseguire il cmdlet Next per creare un token di registrazione per autorizzare un host sessione ad aggiungere il pool host e salvarlo in un nuovo file nel computer locale. È possibile specificare il periodo di validità del token di registrazione utilizzando il parametro-ExpirationHours.

>[!NOTE]
>La data di scadenza del token non può essere inferiore a un'ora e non più di un mese. Se si imposta *-expirationTime* al di fuori di tale limite, il cmdlet non creerà il token.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Se ad esempio si vuole creare un token che scade tra due ore, eseguire questo cmdlet: 

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ')) 
```

Eseguire questo cmdlet per aggiungere Azure Active Directory utenti al gruppo di app desktop predefinito per il pool host.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

Eseguire questo cmdlet successivo per aggiungere Azure Active Directory gruppi di utenti al gruppo di app desktop predefinito per il pool host:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+“-DAG”> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Eseguire il cmdlet seguente per esportare il token di registrazione in una variabile, che sarà utilizzata più avanti in [registrare le macchine virtuali nel pool host del desktop virtuale di Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> 
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Creazione di macchine virtuali per il pool host

A questo punto è possibile creare una macchina virtuale di Azure che può essere unita in join al pool di host per desktop virtuali Windows.

È possibile creare una macchina virtuale in diversi modi:

- [Creare una macchina virtuale da un'immagine della raccolta di Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Creare una macchina virtuale da un'immagine gestita](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Creare una macchina virtuale da un'immagine non gestita](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Se si distribuisce una macchina virtuale usando Windows 7 come sistema operativo host, il processo di creazione e distribuzione sarà leggermente diverso. Per ulteriori informazioni, vedere [la pagina relativa alla distribuzione di una macchina virtuale Windows 7 sul desktop virtuale di Windows](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

Dopo aver creato le macchine virtuali host sessione, [applicare una licenza Windows a una VM host sessione](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) per eseguire le macchine virtuali Windows o Windows Server senza pagare per un'altra licenza. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Preparare le macchine virtuali per le installazioni di agenti desktop virtuali Windows

Per preparare le macchine virtuali, è necessario eseguire le operazioni seguenti prima di installare gli agenti desktop virtuali di Windows e registrare le macchine virtuali nel pool host del desktop virtuale Windows:

- È necessario aggiungere il computer a un dominio. Ciò consente di eseguire il mapping degli utenti di desktop virtuali Windows in ingresso dal proprio account di Azure Active Directory al proprio account di Active Directory e di consentire l'accesso alla macchina virtuale.
- Se la macchina virtuale esegue un sistema operativo Windows Server, è necessario installare il ruolo host sessione Desktop remoto (RDSH). Il ruolo RDSH consente di installare correttamente gli agenti desktop virtuali di Windows.

Per aggiungere correttamente un dominio, eseguire le operazioni seguenti in ogni macchina virtuale:

1. [Connettersi alla macchina virtuale](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) con le credenziali fornite durante la creazione della macchina virtuale.
2. Nella macchina virtuale avviare il **Pannello di controllo** e selezionare **sistema**.
3. Selezionare **nome computer**, fare clic su **Modifica impostazioni**e quindi selezionare **modifica...**
4. Selezionare **dominio** e quindi immettere il dominio Active Directory nella rete virtuale.
5. Eseguire l'autenticazione con un account di dominio che disponga dei privilegi per i computer aggiunti al dominio.

    >[!NOTE]
    > Se si intende aggiungere le macchine virtuali a un ambiente Azure AD Domain Services (Azure AD DS), assicurarsi che l'utente di aggiunta a un dominio sia anche membro del [gruppo AAD DC Administrators](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registrare le macchine virtuali nel pool host del desktop virtuale Windows

La registrazione delle macchine virtuali in un pool host di desktop virtuali Windows è semplice quanto l'installazione degli agenti desktop virtuali di Windows.

Per registrare gli agenti desktop virtuali Windows, eseguire le operazioni seguenti in ogni macchina virtuale:

1. [Connettersi alla macchina virtuale](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) con le credenziali fornite durante la creazione della macchina virtuale.
2. Scaricare e installare l'agente desktop virtuale di Windows.
   - Scaricare l' [agente desktop virtuale di Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Eseguire il programma di installazione. Quando il programma di installazione richiede il token di registrazione, immettere il valore ottenuto dal cmdlet **Export-RdsRegistrationInfo** .
3. Scaricare e installare il bootloader dell'agente desktop virtuale di Windows.
   - Scaricare il [bootloader dell'agente desktop virtuale di Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Eseguire il programma di installazione.

>[!IMPORTANT]
>Per proteggere l'ambiente di Desktop virtuale Windows in Azure, è consigliabile non aprire la porta 3389 in ingresso nelle macchine virtuali. Desktop virtuale Windows non richiede una porta in ingresso 3389 per permettere agli utenti di accedere alle macchine virtuali del pool di host. Se è necessario aprire la porta 3389 per la risoluzione dei problemi, è consigliabile usare l'[accesso Just-In-Time alla VM](../security-center/security-center-just-in-time.md). Si consiglia anche di non assegnare le VM a un indirizzo IP pubblico.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato un pool di host, è possibile popolarlo con RemoteApp. Per altre informazioni su come gestire le app in Desktop virtuale Windows, vedere l'esercitazione Gestire i gruppi di app.

> [!div class="nextstepaction"]
> [Esercitazione: Gestire i gruppi di app](./manage-app-groups.md)
