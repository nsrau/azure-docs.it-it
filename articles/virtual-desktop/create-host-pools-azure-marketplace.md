---
title: Pool di host di Desktop virtuale Windows in Azure Marketplace - Azure
description: Come creare un pool di host di Desktop virtuale Windows tramite Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d5165b160ffc196416052a56aaa0d93c05db56bc
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128003"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Esercitazione: creare un pool di host usando Azure Marketplace

In questa esercitazione si apprenderà come creare un pool di host all'interno di un tenant di desktop virtuale Windows usando un'offerta Microsoft Azure Marketplace.

I pool di host sono una raccolta di una o più macchine virtuali identiche all'interno di ambienti tenant di Desktop virtuale Windows. Ogni pool di host può contenere un gruppo di app con cui gli utenti possono interagire come farebbero in un desktop fisico.

Le attività di questa esercitazione includono:

> [!div class="checklist"]
>
> * Creare un pool di host in Desktop virtuale Windows.
> * Creare un gruppo di risorse con macchine virtuali nella sottoscrizione di Azure.
> * Aggiungere le macchine virtuali al dominio di Active Directory.
> * Registrare le macchine virtuali con Desktop virtuale Windows.

## <a name="prerequisites"></a>Prerequisites

* Un tenant in un desktop virtuale. Un' [esercitazione](tenant-setup-azure-active-directory.md) precedente crea un tenant.
* [Modulo PowerShell per desktop virtuale di Windows](/powershell/windows-virtual-desktop/overview/).

Dopo aver creato questo modulo, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Eseguire l'offerta di Azure Marketplace per effettuare il provisioning di un nuovo pool di host

Per eseguire l'offerta di Azure Marketplace ed effettuare il provisioning di un nuovo pool di host:

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.
1. Immettere **Desktop virtuale Windows** nella finestra di ricerca del Marketplace.
1. Selezionare **Windows Virtual Desktop - Provision a host pool** (Desktop virtuale Windows - Provisioning di un pool di host) e quindi **Crea**.

Successivamente, seguire le istruzioni nella sezione successiva per immettere le informazioni relative alle schede appropriate.

### <a name="basics"></a>Nozioni di base

Ecco le operazioni da eseguire per la scheda **nozioni di base** :

1. Selezionare una **Sottoscrizione**.
1. Per **gruppo di risorse**selezionare **Crea nuovo** e specificare un nome per il nuovo gruppo di risorse.
1. Selezionare un' **area**.
1. Immettere un nome per il pool host univoco all'interno del tenant di desktop virtuale di Windows.
1. Selezionare **Desktop Type**. Se si seleziona **personale**, ogni utente che si connette a questo pool host viene assegnato in modo permanente a una macchina virtuale.
1. Immettere gli utenti che possono accedere ai client desktop virtuali di Windows e accedere a un desktop. Usare un elenco delimitato da virgole. Se ad esempio si vuole assegnare l'accesso `user1@contoso.com` e `user2@contoso.com`, immettere *`user1@contoso.com,user2@contoso.com`*
1. Per **percorso metadati del servizio**, selezionare lo stesso percorso della rete virtuale che dispone di connettività al server Active Directory.

   >[!IMPORTANT]
   >Se si usa una soluzione di Azure Active Directory Domain Services puri (Azure AD DS) e Azure Active Directory (Azure AD), assicurarsi di distribuire il pool host nella stessa area del Azure AD DS per evitare errori di aggiunta al dominio e credenziali.

1. Selezionare **Avanti: Configura macchine virtuali**.

### <a name="configure-virtual-machines"></a>Configurare le macchine virtuali

Per la scheda **Configura macchine virtuali** :

1. Accettare le impostazioni predefinite o personalizzare il numero e le dimensioni delle macchine virtuali.

    >[!NOTE]
    >Se le dimensioni specifiche della macchina virtuale che si sta cercando non vengono visualizzate nel selettore delle dimensioni, ciò è dovuto al fatto che non è stato ancora caricato nello strumento di Azure Marketplace. Per richiedere una dimensione, creare una richiesta o votare una richiesta esistente nel forum di [UserVoice per desktop virtuale di Windows](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

1. Immettere un prefisso per i nomi delle macchine virtuali. Se ad esempio si immette il *prefisso*, le macchine virtuali verranno denominate **Prefix-0**, **Prefix-1**e così via.
1. Selezionare **Avanti: impostazioni macchina virtuale**.

### <a name="virtual-machine-settings"></a>Impostazioni della macchina virtuale

Per la scheda **Impostazioni macchina virtuale** :

1. Per **Origine immagine** selezionare l'origine e immettere le informazioni appropriate per trovarla e archiviarla. Le opzioni disponibili sono diverse per l'archiviazione BLOB, l'immagine gestita e la raccolta.

   Se si sceglie di non usare Managed disks, selezionare l'account di archiviazione che contiene il file con estensione *VHD* .
1. Immettere il nome e la password dell'entità utente. Questo account deve essere l'account di dominio che aggiungerà le macchine virtuali al dominio Active Directory. Gli stessi valori di nome utente e password verranno creati come account locali nelle macchine virtuali. È possibile reimpostare questi account locali in seguito.

   >[!NOTE]
   > Se si uniscono le macchine virtuali a un ambiente Azure AD DS, assicurarsi che l'utente di aggiunta al dominio sia un membro del [gruppo di amministratori di AAD DC](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
   >
   > È necessario che l'account faccia parte anche del dominio gestito Azure AD DS o del tenant di Azure AD. Gli account di directory esterne associate al tenant di Azure AD non possono eseguire correttamente l'autenticazione durante il processo di aggiunta al dominio.

1. Selezionare la **rete virtuale** con connettività al server Active Directory, quindi scegliere una subnet per ospitare le macchine virtuali.
1. Selezionare **Avanti: informazioni sul desktop virtuale di Windows**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informazioni sul tenant di Desktop virtuale Windows

Per la scheda **informazioni sul tenant del desktop virtuale Windows** :

1. Per **Windows Virtual Desktop tenant group name** (Nome gruppo di tenant Desktop virtuale Windows), immettere il nome del gruppo di tenant che contiene il tenant. Lasciare il valore predefinito se non è stato fornito un nome specifico per il gruppo di tenant.
1. Per **Windows Virtual Desktop tenant name** (Nome tenant Desktop virtuale Windows) immettere il nome del tenant in cui verrà creato il pool di host.
1. Specificare il tipo di credenziali da usare per l'autenticazione come proprietario di Servizi Desktop remoto del tenant di Desktop virtuale Windows. Immettere l'UPN o l'entità servizio e una password.

   Se è stata completata l'esercitazione [Creare entità servizio e assegnazioni di ruolo con PowerShell](./create-service-principal-role-powershell.md), selezionare **Entità servizio**.

1. Per l' **entità servizio**, per **Azure ad ID tenant**, immettere l'account amministratore tenant per l'istanza di Azure ad che contiene l'entità servizio. Sono supportate solo entità servizio con una credenziale password.
1. Selezionare **Avanti: verifica + crea**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Completare la configurazione e creare la macchina virtuale

In **revisione e creazione**esaminare le informazioni di installazione. Se è necessario modificare un elemento, tornare indietro e apportare modifiche. Quando si è pronti, selezionare **Crea** per distribuire il pool host.

A seconda del numero di macchine virtuali che si sta creando, il completamento di questo processo può richiedere oltre 30 minuti.

>[!IMPORTANT]
> Per proteggere l'ambiente di desktop virtuale Windows in Azure, si consiglia di non aprire la porta in ingresso 3389 nelle macchine virtuali. Per desktop virtuale Windows non è necessaria una porta di ingresso aperta 3389 per consentire agli utenti di accedere alle macchine virtuali del pool host.
>
> Se è necessario aprire la porta 3389 per la risoluzione dei problemi, è consigliabile usare l'accesso just-in-time. Per altre informazioni, vedere [proteggere le porte di gestione con accesso just-in-Time](../security-center/security-center-just-in-time.md).

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Facoltativo) Assegnare altri utenti al gruppo di applicazioni desktop

Al termine della creazione del pool da parte di Azure Marketplace, è possibile assegnare altri utenti al gruppo di applicazioni desktop. Se non si vuole aggiungere altro, ignorare questa sezione.

Per assegnare gli utenti al gruppo di applicazioni desktop:

1. Aprire una finestra di PowerShell.

1. Eseguire il comando seguente per accedere all'ambiente desktop virtuale di Windows:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Aggiungere gli utenti al gruppo di applicazioni desktop usando questo comando:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   L'UPN dell'utente deve corrispondere all'identità dell'utente in Azure AD, ad esempio *user1@contoso.com* . Se si desidera aggiungere più utenti, eseguire il comando per ogni utente.

Gli utenti aggiunti al gruppo di applicazioni desktop possono accedere a desktop virtuale Windows con client di Desktop remoto supportati e visualizzare una risorsa per un desktop della sessione.

Ecco i client attualmente supportati:

* [Client di Desktop remoto per Windows 7 e Windows 10](connect-windows-7-and-10.md)
* [Client Web di Desktop virtuale Windows](connect-web.md)

## <a name="next-steps"></a>Passaggi successivi

È stato creato un pool host e gli utenti sono stati assegnati per accedere al desktop. È possibile popolare il pool host con i programmi RemoteApp. Per altre informazioni su come gestire le app in Desktop virtuale Windows, vedere questa esercitazione:

> [!div class="nextstepaction"]
> [Esercitazione: Gestire i gruppi di app](./manage-app-groups.md)
