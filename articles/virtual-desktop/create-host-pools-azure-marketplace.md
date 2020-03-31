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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79222288"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Esercitazione: Creare un pool di host con Azure Marketplace

Questa esercitazione descrive come creare un pool di host all'interno di un tenant di Desktop virtuale Windows usando un'offerta di Microsoft Azure Marketplace.

I pool di host sono una raccolta di una o più macchine virtuali identiche all'interno di ambienti tenant di Desktop virtuale Windows. Ogni pool di host può contenere un gruppo di app con cui gli utenti possono interagire come farebbero in un desktop fisico.

Le attività di questa esercitazione includono:

> [!div class="checklist"]
>
> * Creare un pool di host in Desktop virtuale Windows.
> * Creare un gruppo di risorse con macchine virtuali nella sottoscrizione di Azure.
> * Aggiungere le macchine virtuali al dominio di Active Directory.
> * Registrare le macchine virtuali con Desktop virtuale Windows.

## <a name="prerequisites"></a>Prerequisiti

* Un tenant in Desktop virtuale. Il tenant viene creato in un'[esercitazione](tenant-setup-azure-active-directory.md) precedente.
* [Modulo PowerShell con Desktop virtuale Windows](/powershell/windows-virtual-desktop/overview/).

Una volta ottenuto questo modulo, eseguire il cmdlet seguente per accedere al proprio account:

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

Successivamente, seguire le istruzioni riportate nella sezione successiva per immettere le informazioni per le schede appropriate.

### <a name="basics"></a>Nozioni di base

Per la scheda **Informazioni di base** procedere come segue:

1. Selezionare una **Sottoscrizione**.
1. Per **Gruppo di risorse** selezionare **Crea nuovo** e specificare un nome per il nuovo gruppo di risorse.
1. Selezionare un'**area**.
1. Per il pool di host immettere un nome che sia univoco all'interno del tenant di Desktop virtuale Windows.
1. Selezionare il **Tipo di desktop**. Se si seleziona **Personale**, ogni utente che si connette a questo pool di host verrà assegnato in modo permanente a una macchina virtuale.
1. Specificare gli utenti che possono accedere ai client Desktop virtuale Windows e accedere a un desktop. Usare un elenco delimitato da virgole. Se ad esempio si vuole assegnare l'accesso a `user1@contoso.com` e a `user2@contoso.com`, immettere *`user1@contoso.com,user2@contoso.com`*
1. Per **Service metadata location** (Località metadati servizio) selezionare la stessa località della rete virtuale con connettività al server Active Directory.

   >[!IMPORTANT]
   >Se si usa una soluzione Azure Active Directory Domain Services (Azure AD DS) e Azure Active Directory (Azure AD) pura, assicurarsi di distribuire il pool host nella stessa area di Azure Active Directory Domain Services per evitare errori di aggiunta al dominio e credenziali.

1. Selezionare **Avanti: Configurare le macchine virtuali**.

### <a name="configure-virtual-machines"></a>Configurare le macchine virtuali

Per la scheda **Configurare le macchine virtuali**:

1. Accettare i valori predefiniti oppure personalizzare il numero e le dimensioni delle macchine virtuali.

    >[!NOTE]
    >Se le dimensioni specifiche della macchina virtuale desiderate non vengono visualizzate nel selettore di dimensioni, significa che non ne è stato ancora eseguito l'onboarding nello strumento di Azure Marketplace. Per richiedere le dimensioni, creare una richiesta oppure aggiornarne una esistente nel [forum UserVoice di Desktop virtuale Windows](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

1. Immettere un prefisso per i nomi delle macchine virtuali. Ad esempio, se si immette il *prefisso*, le macchine virtuali saranno denominate **prefisso-0**, **prefisso-1** e così via.
1. Selezionare **Avanti: Impostazioni della macchina virtuale**.

### <a name="virtual-machine-settings"></a>Impostazioni della macchina virtuale

Per la scheda **Impostazioni macchina virtuale**:

1. Per **Origine immagine** selezionare l'origine e immettere le informazioni appropriate per trovarla e archiviarla. Le opzioni disponibili sono diverse per l'archiviazione BLOB, l'immagine gestita e la raccolta.

   Se si è scelto di non usare dischi gestiti, selezionare l'account di archiviazione contenente il file con estensione *vhd*.
1. Specificare il nome dell'entità utente e la password. Questo account deve essere l'account di dominio che aggiungerà le macchine virtuali al dominio di Active Directory. Gli stessi valori di nome utente e password verranno creati come account locali nelle macchine virtuali. È possibile reimpostare questi account locali in seguito.

   >[!NOTE]
   > Se si stanno aggiungendo le macchine virtuali a un ambiente Azure Active Directory Domain Services, assicurarsi che l'utente di aggiunta a un dominio sia anche un membro del [gruppo AAD DC Administrators](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
   >
   > Verificare che l'account faccia parte del dominio gestito di Azure Active Directory Domain Services o del tenant di Azure AD. Gli account di directory esterne associate al tenant di Azure AD non possono eseguire correttamente l'autenticazione durante il processo di aggiunta al dominio.

1. Selezionare la **Rete virtuale** che ha connettività al server Active Directory e quindi scegliere una subnet in cui ospitare le macchine virtuali.
1. Selezionare **Avanti: Informazioni sul tenant di Desktop virtuale Windows**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informazioni sul tenant di Desktop virtuale Windows

Per la scheda **Windows Virtual Desktop tenant information** (Informazioni tenant Desktop virtuale Windows):

1. Per **Windows Virtual Desktop tenant group name** (Nome gruppo di tenant Desktop virtuale Windows), immettere il nome del gruppo di tenant che contiene il tenant. Lasciare il valore predefinito se non è stato fornito un nome specifico per il gruppo di tenant.
1. Per **Windows Virtual Desktop tenant name** (Nome tenant Desktop virtuale Windows) immettere il nome del tenant in cui verrà creato il pool di host.
1. Specificare il tipo di credenziali da usare per l'autenticazione come proprietario di Servizi Desktop remoto del tenant di Desktop virtuale Windows. Immettere il nome dell'entità utente o l'entità servizio e una password.

   Se è stata completata l'esercitazione [Creare entità servizio e assegnazioni di ruolo con PowerShell](./create-service-principal-role-powershell.md), selezionare **Entità servizio**.

1. In **Entità servizio** per **ID tenant di Azure AD** immettere l'account amministratore tenant per l'istanza di Azure AD che contiene l'entità servizio. Sono supportate solo entità servizio con una credenziale password.
1. Selezionare **Avanti: Rivedi e crea**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Completare la configurazione e creare la macchina virtuale

In **Rivedi e crea** esaminare le informazioni di configurazione. Se è necessario apportare modifiche, tornare indietro. Quando si è pronti, selezionare **Crea** per distribuire il pool di host.

A seconda del numero di macchine virtuali create, questo processo può richiedere almeno 30 minuti.

>[!IMPORTANT]
> Per proteggere l'ambiente Desktop virtuale Windows in Azure, è consigliabile non aprire la porta in ingresso 3389 nelle macchine virtuali. Desktop virtuale Windows non richiede una porta in ingresso 3389 per consentire agli utenti di accedere alle macchine virtuali del pool di host.
>
> Se è necessario aprire la porta 3389 per la risoluzione dei problemi, è consigliabile usare l'accesso JIT. Per altre informazioni, vedere [Proteggere le porte di gestione con l'accesso JIT](../security-center/security-center-just-in-time.md).

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Facoltativo) Assegnare altri utenti al gruppo di applicazioni desktop

Non appea Azure Marketplace completa la creazione del pool, è possibile assegnare altri utenti al gruppo di applicazioni desktop. Se non si vuole aggiungerne altri, ignorare questa sezione.

Per assegnare gli utenti al gruppo di applicazioni desktop:

1. Aprire una finestra di PowerShell.

1. Eseguire il comando seguente per accedere all'ambiente Desktop virtuale Windows:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Usare questo comando per aggiungere gli utenti al gruppo di applicazioni desktop:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   Il nome dell'entità utente deve corrispondere all'identità dell'utente in Azure AD, ad esempio *user1@contoso.com* . Per aggiungere più utenti, eseguire questo comando per ogni utente.

Gli utenti aggiunti al gruppo di applicazioni desktop possono accedere a Desktop virtuale Windows con i client Desktop remoto supportati e visualizzare una risorsa per un desktop di sessione.

Ecco i client attualmente supportati:

* [Client di Desktop remoto per Windows 7 e Windows 10](connect-windows-7-and-10.md)
* [Client Web di Desktop virtuale Windows](connect-web.md)

## <a name="next-steps"></a>Passaggi successivi

È stato creato un pool di host e sono stati assegnati gli utenti per accedere al desktop corrispondente. È possibile popolare il pool di host con i programmi RemoteApp. Per altre informazioni su come gestire le app in Desktop virtuale Windows, vedere questa esercitazione:

> [!div class="nextstepaction"]
> [Esercitazione: Gestire i gruppi di app](./manage-app-groups.md)
