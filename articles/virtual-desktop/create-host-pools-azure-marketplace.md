---
title: Creare un pool di host dell'anteprima di Desktop virtuale Windows tramite Azure Marketplace - Azure
description: Come creare un pool di host dell'anteprima di Desktop virtuale Windows tramite Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: helohr
ms.openlocfilehash: d6628f1522880f650bfd8c728fe46fd050a8e6a0
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208376"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Esercitazione: Creare un pool di host con Azure Marketplace

I pool di host sono una raccolta di una o più macchine virtuali identiche all'interno di ambienti tenant dell'anteprima di Desktop virtuale Windows. Ogni pool di host può contenere un gruppo di app con cui gli utenti possono interagire come farebbero in un desktop fisico.

Questa esercitazione descrive come creare un pool di host all'interno di un tenant di Desktop virtuale Windows usando un'offerta di Microsoft Azure Marketplace. Le attività includono:

> [!div class="checklist"]
> * Creare un pool di host in Desktop virtuale Windows.
> * Creare un gruppo di risorse con macchine virtuali nella sottoscrizione di Azure.
> * Aggiungere le macchine virtuali al dominio di Active Directory.
> * Registrare le macchine virtuali con Desktop virtuale Windows.

Prima di iniziare, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Eseguire l'offerta di Azure Marketplace per effettuare il provisioning di un nuovo pool di host

Per eseguire l'offerta di Azure Marketplace ed effettuare il provisioning di un nuovo pool di host:

1. Selezionare **+** oppure **+ Crea una risorsa**.
2. Immettere **Desktop virtuale Windows** nella finestra di ricerca del Marketplace.
3. Selezionare **Windows Virtual Desktop - Provision a host pool** (Desktop virtuale Windows - Provisioning di un pool di host) e quindi **Crea**.

Successivamente, seguire le istruzioni riportate nella sezione successiva per immettere le informazioni per i pannelli appropriati.

### <a name="basics"></a>Nozioni di base

Nel pannello **Generale** procedere come segue:

1. Immettere un nome per il pool di host che sia univoco all'interno del tenant di Desktop virtuale Windows.
2. Selezionare l'opzione appropriata per un desktop personale. Se si seleziona **Sì**, ogni utente che si connette a questo pool di host verrà assegnato in modo permanente a una macchina virtuale.
3. Immettere un elenco di voci delimitate da virgole corrispondenti agli utenti che possono accedere ai client di Desktop virtuale Windows e a un desktop quando l'offerta di Azure Marketplace termina. Se ad esempio si vuole assegnare l'accesso a user1@contoso.com e a user2@contoso.com, immettere "user1@contoso.com,user2@contoso.com".
4. Selezionare **Crea nuovo** e specificare il nome del nuovo gruppo di risorse.
5. Per **Località** selezionare la stessa località della macchina virtuale che ha connettività con il server Active Directory.
6. Selezionare **OK**.

>[!IMPORTANT]
>Se si usa una soluzione Azure Active Directory Domain Services e Azure Active Directory pura, assicurarsi di distribuire il pool host nella stessa area di Azure Active Directory Domain Services per evitare errori di aggiunta al dominio e credenziali.

### <a name="configure-virtual-machines"></a>Configurare le macchine virtuali

Per il pannello **Configura le VM**:

1. Accettare i valori predefiniti oppure personalizzare il numero e le dimensioni delle VM.
2. Immettere un prefisso per i nomi delle macchine virtuali. Ad esempio, se si immette il nome "prefisso", le macchine virtuali saranno denominate "prefisso-0", "prefisso-1" e così via.
3. Selezionare **OK**.

### <a name="virtual-machine-settings"></a>Impostazioni della macchina virtuale

Per il pannello **Virtual machine settings** (Impostazioni macchina virtuale):

>[!NOTE]
> Se si intende aggiungere le macchine virtuali a un ambiente Azure AD Domain Services (Azure AD DS), assicurarsi che l'utente di aggiunta a un dominio sia anche membro del [gruppo AAD DC Administrators](../active-directory-domain-services/tutorial-create-instance.md#configure-an-administrative-group).

1. Per **Origine immagine** selezionare l'origine e immettere le informazioni appropriate per trovarla e archiviarla. Se si è scelto di non usare dischi gestiti, selezionare l'account di archiviazione contenente il file con estensione vhd.
2. Immettere il nome dell'entità utente e la password per l'account di dominio che aggiungerà le VM al dominio di Active Directory. Gli stessi valori di nome utente e password verranno creati come account locali nelle macchine virtuali. È possibile reimpostare questi account locali in seguito.
3. Selezionare la rete virtuale che ha connettività con il server Active Directory e quindi scegliere una subnet in cui ospitare le macchine virtuali.
4. Selezionare **OK**.

### <a name="windows-virtual-desktop-preview-tenant-information"></a>Informazioni sul tenant dell'anteprima di Desktop virtuale Windows

Per il pannello **Windows Virtual Desktop tenant information** (Informazioni tenant Desktop virtuale Windows):

1. Per **Windows Virtual Desktop tenant group name** (Nome gruppo di tenant Desktop virtuale Windows), immettere il nome del gruppo di tenant che contiene il tenant. Lasciare il valore predefinito se non è stato fornito un nome specifico per il gruppo di tenant.
2. Per **Windows Virtual Desktop tenant name** (Nome tenant Desktop virtuale Windows) immettere il nome del tenant in cui verrà creato il pool di host.
3. Specificare il tipo di credenziali da usare per l'autenticazione come proprietario di Servizi Desktop remoto del tenant di Desktop virtuale Windows. Se è stata completata l'esercitazione [Creare entità servizio e assegnazioni di ruolo con PowerShell](./create-service-principal-role-powershell.md), selezionare **Entità servizio**. Quando viene visualizzato il campo **ID tenant di Azure AD**, immettere l'ID dell'istanza di Azure Active Directory che contiene l'entità servizio.
4. Immettere le credenziali per l'account amministratore del tenant. Sono supportate solo entità servizio con una credenziale password.
5. Selezionare **OK**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Completare la configurazione e creare la macchina virtuale

Per gli ultimi due pannelli:

1. Nel pannello **Riepilogo** rivedere le informazioni di configurazione. Se è necessario cambiare qualcosa, tornare nel pannello appropriato e apportare le modifiche prima di continuare. Se le informazioni sono corrette, selezionare **OK**.
2. Nel pannello **Acquista** rivedere le informazioni aggiuntive sull'acquisto in Azure Marketplace.
3. Selezionare **Crea** per distribuire il pool di host.

A seconda del numero di VM create, questo processo può richiedere almeno 30 minuti.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Facoltativo) Assegnare altri utenti al gruppo di applicazioni desktop

Quando l'offerta di Azure Marketplace termina, è possibile assegnare altri utenti al gruppo di applicazioni desktop prima di iniziare a testare i desktop di sessione completi nelle macchine virtuali. Se sono già stati aggiunti gli utenti predefiniti nell'offerta di Azure Marketplace e non se ne vogliono aggiungere altri, è possibile ignorare questa sezione.

Prima di assegnare utenti al gruppo di applicazioni desktop, è necessario aprire una finestra di PowerShell. Quindi, è necessario immettere i due cmdlet seguenti.

Eseguire il cmdlet seguente per accedere all'ambiente di Desktop virtuale Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Aggiungere utenti al gruppo di applicazioni desktop tramite questo cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Il nome dell'entità utente deve corrispondere all'identità dell'utente in Azure Active Directory, ad esempio user1@contoso.com. Se si vogliono aggiungere più utenti, è necessario eseguire questo cmdlet per ognuno.

Dopo aver completato questi passaggi, gli utenti aggiunti al gruppo di applicazioni desktop possono accedere a Desktop virtuale Windows con i client di Desktop remoto supportati e vedere una risorsa per un desktop di sessione.

Ecco i client attualmente supportati:

- [Client di Desktop remoto per Windows 7 e Windows 10](connect-windows-7-and-10.md)
- [Client Web di Desktop virtuale Windows](connect-web.md)

>[!IMPORTANT]
>Per proteggere l'ambiente di Desktop virtuale Windows in Azure, è consigliabile non aprire la porta 3389 in ingresso nelle macchine virtuali. Desktop virtuale Windows non richiede una porta in ingresso 3389 per permettere agli utenti di accedere alle macchine virtuali del pool di host. Se è necessario aprire la porta 3389 per la risoluzione dei problemi, è consigliabile usare l'[accesso Just-In-Time alla VM](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un pool di host e assegnato gli utenti che possono accedere al desktop di questo, è possibile popolare il pool di host con programmi RemoteApp. Per altre informazioni su come gestire le app in Desktop virtuale Windows, vedere questa esercitazione:

> [!div class="nextstepaction"]
> [Esercitazione: Gestire i gruppi di app](./manage-app-groups.md)
