---
title: Accedere alla macchina virtuale Windows in Azure usando Azure Active Directory (anteprima)Sign in to Windows virtual machine in Azure using Azure Active Directory (Preview)
description: Azure AD sign in to an Azure VM running Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88ae3c45126403161e35ec46e5ccc2666c3edb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050070"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Accedere alla macchina virtuale Windows in Azure usando l'autenticazione di Azure Active Directory (anteprima)Sign in to Windows virtual machine in Azure using Azure Active Directory authentication (Preview)

Le organizzazioni possono ora usare l'autenticazione di Azure Active Directory (AD) per le macchine virtuali di Azure (VM) che eseguono **Windows Server 2019 Datacenter edition** o Windows **10 1809** e versioni successive. L'uso di Azure AD per l'autenticazione nelle macchine virtuali consente di controllare e applicare i criteri in modo centralizzato. Strumenti come il controllo degli accessi in base al ruolo di Azure e l'accesso condizionale di Azure AD consentono di controllare chi può accedere a una macchina virtuale. Questo articolo illustra come creare e configurare una macchina virtuale di Windows Server 2019 per usare l'autenticazione di Azure AD.

|     |
| --- |
| Azure AD sign in for Azure Windows VMs is a public preview feature of Azure Active Directory. Per altre informazioni sulle anteprime, vedere Condizioni per [l'utilizzo supplementari per le anteprime di Microsoft AzureFor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) more information about previews, see Supplemental Terms of Use for Microsoft Azure Previews|
|     |

L'uso dell'autenticazione di Azure AD per accedere alle macchine virtuali Windows in Azure offre molti vantaggi, tra cui:There are many benefits of using Azure AD authentication to log in to Windows VMs in Azure, including:

- Usare le stesse credenziali di Azure AD federate o gestite normalmente usate.
- Non è più necessario gestire gli account amministratore locale.
- Il controllo degli accessi in base al ruolo di Azure consente di concedere l'accesso appropriato alle macchine virtuali in base alle esigenze e di rimuoverlo quando non è più necessario.
- Prima di consentire l'accesso a una macchina virtuale, l'accesso condizionale di Azure AD può applicare requisiti aggiuntivi, ad esempio:Before allowing access to a VM, Azure AD Conditional Access can enforce additional requirements such as: 
   - Autenticazione a più fattori
   - Controllo dei rischi di accesso
- Automatizzare e ridimensionare l'aggiunta di Azure AD alle macchine virtuali Windows di Azure che fanno parte delle distribuzioni VDI.

> [!NOTE]
> Dopo aver abilitato questa funzionalità, le macchine virtuali Windows in Azure verranno aggiunte ad Azure AD. Non è possibile aggiungerlo ad un altro dominio, ad esempio AD locale o Azure AD DS. Se è necessario eseguire questa operazione, è necessario disconnettere la macchina virtuale dal tenant di Azure AD disinstallando l'estensione.

## <a name="requirements"></a>Requisiti

### <a name="supported-azure-regions-and-windows-distributions"></a>Aree di Azure supportate e distribuzioni di WindowsSupported Azure regions and Windows distributions

Le distribuzioni di Windows seguenti sono attualmente supportate durante l'anteprima di questa funzionalità:

- Windows Server 2019 Datacenter
- Windows 10 1809 e versioni successive

> [!IMPORTANT]
> La connessione remota alle macchine virtuali aggiunte ad Azure AD è consentita solo da PC Windows 10 aggiunti ad Azure AD aggiunti ad Azure AD aggiunti alla **stessa** directory della macchina virtuale. 

Durante l'anteprima di questa funzionalità sono attualmente supportate le aree di Azure seguenti:

- Tutte le aree globali di AzureAll Azure global regions

> [!IMPORTANT]
> Per usare questa funzionalità di anteprima, distribuire solo una distribuzione di Windows supportata e in un'area di Azure supportata. La funzionalità non è attualmente supportata in Azure per enti pubblici o cloud sovrani.

### <a name="network-requirements"></a>Requisiti di rete

Per abilitare l'autenticazione di Azure AD per le macchine virtuali Windows in Azure, è necessario verificare che la configurazione di rete delle macchine virtuali consenta l'accesso in uscita agli endpoint seguenti tramite la porta TCP 443:To enable Azure AD authentication for your Windows VMs in Azure, you need to ensure your VMs network configuration permits outbound access to the following endpoints over TCP port 443:

- https:\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https:\//device.login.microsoftonline.com
- https:\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Abilitazione dell'accesso di Azure AD per la macchina virtuale Windows in AzureEnabling Azure AD login in for Windows VM in Azure

Per usare l'accesso ad Azure AD per la macchina virtuale Windows in Azure, è innanzitutto necessario abilitare l'opzione di accesso di Azure AD per la macchina virtuale Windows e quindi configurare le assegnazioni di ruolo RBAC per gli utenti autorizzati ad accedere alla macchina virtuale.
Esistono diversi modi per abilitare l'accesso di Azure AD per la macchina virtuale Windows:There are multiple ways you can enable Azure AD login for your Windows VM:

- Uso dell'esperienza del portale di Azure durante la creazione di una macchina virtuale WindowsUsing the Azure portal experience when creating a Windows VM
- Uso dell'esperienza di Azure Cloud Shell durante la creazione di una macchina virtuale Windows o per una **macchina virtuale Windows esistenteUsing** the Azure Cloud Shell experience when creating a Windows VM or for an existing Windows VM

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Uso del portale di Azure creare esperienza VM per abilitare l'accesso ad Azure ADUsing Azure portal create VM experience to enable Azure AD login

È possibile abilitare l'account di accesso di Azure AD per Windows Server 2019 Datacenter o Windows 10 1809 e immagini vm successive. 

Per creare una macchina virtuale di Windows Server 2019 Datacenter in Azure con accesso ad Azure AD:To create a Windows Server 2019 Datacenter VM in Azure with Azure AD logon: 

1. Accedere al [portale](https://portal.azure.com)di Azure con un account che dispone dell'accesso per creare macchine virtuali e selezionare **Crea una risorsa**.
1. Digitare **Windows Server** nella barra di ricerca Cerca nel Marketplace.
   1. Fare clic su **Windows Server** e scegliere Windows **Server 2019 Datacenter** dall'elenco a discesa Selezionare un piano software.
   1. Fare clic su **Crea**.
1. Nella scheda "Gestione", abilitare l'opzione **Accedi con credenziali AAD (Anteprima)** nella sezione Azure Active Directory da Disattivato a **Attivato**.
1. Assicurarsi che **l'identità gestita assegnata dal sistema** nella sezione Identità sia impostata su **On**. Questa azione deve essere eseguita automaticamente quando si abilita l'accesso con le credenziali di Azure AD.
1. Passare attraverso il resto dell'esperienza di creazione di una macchina virtuale. Durante questa anteprima, sarà necessario creare un nome utente e una password di amministratore per la macchina virtuale.

![Accesso con credenziali di Azure AD creare una macchina virtualeLogin with Azure AD credentials create a VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Per accedere alla macchina virtuale usando le credenziali di Azure AD, è innanzitutto necessario configurare le assegnazioni di ruolo per la macchina virtuale come descritto in una delle sezioni seguenti.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Uso dell'esperienza di Azure Cloud Shell per abilitare l'accesso ad Azure ADUsing the Azure Cloud Shell experience to enable Azure AD login

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Gli strumenti comuni di Azure sono preinstallati e configurati in Cloud Shell per l'uso con l'account. È sufficiente selezionare il pulsante Copia per copiare il codice, incollarlo in Cloud Shell e quindi premere Invio per eseguirlo. Esistono alcuni modi per aprire Cloud Shell:

Selezionare Prova nell'angolo superiore destro di un blocco di codice.
Aprire Cloud Shell nel browser.
Selezionare il pulsante Cloud Shell nel menu nell'angolo superiore destro del portale di [Azure.](https://portal.azure.com)

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.31 o successiva. Eseguire az --version per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere l'articolo [Installare l'interfaccia della riga di comando](/cli/azure/install-azure-cli)di Azure.If you need to install or upgrade, see the article Install Azure CLI .

1. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#az-group-create). 
1. Creare una macchina virtuale con [az vm create](/cli/azure/vm#az-vm-create) usando una distribuzione supportata in un'area supportata. 
1. Installare l'estensione della macchina virtuale di accesso di Azure AD. 

L'esempio seguente distribuisce una macchina virtuale denominata myVM che usa Win2019Datacenter in un gruppo di risorse denominato myResourceGroup, nell'area southcentralus.The following example deploys a VM named myVM that uses Win2019Datacenter, into a resource group named myResourceGroup, in the southcentralus region. Negli esempi seguenti è possibile specificare i nomi del proprio gruppo di risorse e della macchina virtuale in base alle esigenze.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> È necessario abilitare l'identità gestita assegnata dal sistema nella macchina virtuale prima di installare l'estensione della macchina virtuale di accesso di Azure AD.

La creazione della macchina virtuale e delle risorse di supporto richiede alcuni minuti.

Infine, installare l'estensione della macchina virtuale di accesso di Azure AD per abilitare l'account di accesso di Azure AD per la macchina virtuale Windows.Finally, install the Azure AD login VM extension to enable Azure AD login for Windows VM. Le estensioni della macchina virtuale sono piccole applicazioni che eseguono attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure. Usare az vm extension set per installare l'estensione AADLoginForWindows nella macchina virtuale denominata myVM nel gruppo di risorse myResourceGroup:Use [az vm extension](/cli/azure/vm/extension#az-vm-extension-set) set to install the AADLoginForWindows extension on the VM named myVM in the myResourceGroup resource group:

> [!NOTE]
> È possibile installare AADLoginForWindows estensione in un Windows Server 2019 esistente o Windows 10 1809 e versioni successive VM per abilitarlo per l'autenticazione di Azure AD. Di seguito è riportato un esempio di CLI di A.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

L'oggetto `provisioningState` di `Succeeded` , una volta installata l'estensione nella macchina virtuale.

## <a name="configure-role-assignments-for-the-vm"></a>Configurare le assegnazioni di ruolo per la macchina virtuale

Dopo aver creato la macchina virtuale, è necessario configurare i criteri di controllo degli accessi in base al ruolo di Azure per determinare chi può accedere alla macchina virtuale. Per autorizzare l'account alla VM vengono usati due ruoli Controllo degli accessi in base al ruolo:

- **Accesso amministratore macchina virtuale:** gli utenti con questo ruolo assegnato possono accedere a una macchina virtuale di Azure con privilegi di amministratore.
- **Accesso utente alle macchine virtuali**: gli utenti a cui è stato assegnato questo ruolo possono accedere a una macchina virtuale di Azure con i privilegi di utente normale.

> [!NOTE]
> Per consentire a un utente di accedere alla macchina virtuale tramite RDP, è necessario assegnare il ruolo Di accesso amministratore macchina virtuale o Accesso utente macchina virtuale. Un utente di Azure con i ruoli Proprietario o Collaboratore assegnato a una macchina virtuale non dispone automaticamente dei privilegi per accedere alla macchina virtuale tramite RDP. In questo modo si garantisce la separazione controllata tra il set di persone che controllano le macchine virtuali e il set di utenti che possono accedere alle macchine virtuali.

È possibile configurare le assegnazioni di ruolo per la macchina virtuale in diversi modi:There are multiple ways you can configure role assignments for VM:

- Uso dell'esperienza del portale di Azure ADUsing the Azure AD Portal experience
- Uso dell'esperienza di Azure Cloud Shell

### <a name="using-azure-ad-portal-experience"></a>Uso dell'esperienza del portale di Azure ADUsing Azure AD Portal experience

Per configurare le assegnazioni di ruolo per le macchine virtuali di Windows Server 2019 Datacenter abilitate per Azure AD:To configure role assignments for your Azure AD enabled Windows Server 2019 Datacenter VMs:

1. Passare alla pagina di panoramica della macchina virtuale specifica
1. Selezionare **Controllo di accesso (IAM)** dalle opzioni del menu
1. Selezionare **Aggiungi**, **Aggiungi assegnazione ruolo** per aprire il riquadro Aggiungi assegnazione ruolo.
1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Accesso amministratore macchina virtuale** o Accesso utente macchina **virtuale**.
1. Nel campo **Seleziona** selezionare un utente, un gruppo, un'entità servizio o un'identità gestita. Se l'entità di sicurezza non è visualizzata nell'elenco, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati, gli indirizzi e-mail e gli identificatori di oggetto.
1. Selezionare **Salva**per assegnare il ruolo.

Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo per l'ambito selezionato.

![Assegnare ruoli agli utenti che accederanno alla macchina virtualeAssign roles to users who will access the VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Uso dell'esperienza di Azure Cloud Shell

L'esempio seguente usa [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) per assegnare il ruolo Accesso amministratore alle macchine virtuali alla macchina virtuale per l'utente di Azure corrente. Il nome utente dell'account di Azure attivo si ottiene con [az account show](/cli/azure/account#az-account-show) e viene impostato l'ambito per la macchina virtuale creata in un passaggio precedente con [az vm show](/cli/azure/vm#az-vm-show). È anche possibile assegnare l'ambito a livello di gruppo di risorse o di sottoscrizione e applicare le normali autorizzazioni di ereditarietà del controllo degli accessi in base al ruolo. Per ulteriori informazioni, vedere [Controlli di accesso in base al](../../virtual-machines/linux/login-using-aad.md)ruolo .

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Se il dominio AAD e il dominio del nome utente di accesso `--assignee-object-id`non corrispondono, `--assignee`è necessario specificare l'ID oggetto dell'account utente con il , non solo il nome utente per . È possibile ottenere l'ID oggetto per l'account utente con [az ad user list](/cli/azure/ad/user#az-ad-user-list).

Per altre informazioni su come usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure, vedere gli articoli seguenti:For more information on how to use RBAC to manage access to your Azure subscription resources, see the following articles:

- [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e l'interfaccia della riga di comando di Azure](/azure/role-based-access-control/role-assignments-cli)
- [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e il portale di Azure](/azure/role-based-access-control/role-assignments-portal)
- [Gestire l'accesso alle risorse di Azure usando RBAC e Azure PowerShell.Manage access to Azure resources using RBAC and Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="using-conditional-access"></a>Utilizzo dell'accesso condizionaleUsing Conditional Access

È possibile applicare criteri di accesso condizionale, ad esempio l'autenticazione a più fattori o il controllo dei rischi di accesso utente prima di autorizzare l'accesso alle macchine virtuali Windows in Azure abilitate con l'accesso ad Azure AD. Per applicare i criteri di accesso condizionale, è necessario selezionare l'app "Accesso autonomo a Windows VM di Azure dall'opzione di assegnazione delle app o delle azioni cloud e quindi usare il rischio di accesso come condizione e/o richiedere l'autenticazione a più fattori come controllo di accesso della concessione. 

> [!NOTE]
> Se si usa "Richiedi autenticazione a più fattori" come controllo di accesso grant per richiedere l'accesso all'app "Accesso a Windows VM di Azure", è necessario fornire l'attestazione di autenticazione a più fattori come parte del client che avvia la sessione RDP per Windows di destinazione VM in Azure. L'unico modo per ottenere questo risultato in un client Windows 10 consiste nell'utilizzare il PIN di Windows Hello for Business o l'autenticazione biometrica con il client RDP. Il supporto per l'autenticazione biometrica è stato aggiunto al client RDP in Windows 10 versione 1809. Desktop remoto con l'autenticazione di Windows Hello for Business è disponibile solo per le distribuzioni che utilizzano il modello di attendibilità del certificato e che attualmente non sono disponibili per il modello di attendibilità chiave.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Accedere usando le credenziali di Azure AD a una macchina virtuale WindowsLog in using Azure AD credentials to a Windows VM

> [!IMPORTANT]
> La connessione remota alle macchine virtuali aggiunte ad Azure AD è consentita solo da PC Windows 10 aggiunti ad Azure AD aggiunti ad Azure AD aggiunti alla **stessa** directory della macchina virtuale. Inoltre, a RDP usando le credenziali di Azure AD, l'utente deve appartenere a uno dei due ruoli RBAC, Virtual Machine Administrator Login o Virtual Machine User Login. Al momento, Azure Bastion non può essere usato per accedere usando l'autenticazione di Azure Active Directory con l'estensione AADLoginForWindows.At this time, Azure Bastion cannot be used to login using Azure Active Directory authentication with the AADLoginForWindows extension. È supportato solo RDP diretto.

To login in to your Windows Server 2019 virtual machine using Azure AD: 

1. Passare alla pagina di panoramica della macchina virtuale abilitata con l'accesso ad Azure AD.
1. Selezionare **Connetti** per aprire il pannello Connetti a macchina virtuale.
1. Selezionare **Scarica file RDP**.
1. Selezionare **Apri** per avviare il client Connessione desktop remoto.
1. Selezionare Connetti per aprire la finestra di dialogo di accesso di Windows.Select **Connect** to launch the Windows logon dialog.
1. Accedere usando le credenziali di Azure AD.

È stato eseguito l'accesso alla macchina virtuale di Azure di Windows Server 2019 con le autorizzazioni del ruolo assegnate, ad esempio Utente macchina virtuale o Amministratore macchina virtuale. 

> [!NOTE]
> È possibile salvare il file . File RDP localmente nel computer per avviare future connessioni desktop remoto alla macchina virtuale anziché dover passare alla pagina di panoramica della macchina virtuale nel portale di Azure e tramite l'opzione di connessione.

## <a name="troubleshoot"></a>Risolvere i problemi

### <a name="troubleshoot-deployment-issues"></a>Risolvere i problemi relativi alla distribuzione

L'estensione AADLoginForWindows deve essere installata correttamente affinché la macchina virtuale completi il processo di aggiunta ad Azure AD. Eseguire la procedura seguente se l'estensione della macchina virtuale non viene installata correttamente.

1. RDP alla macchina virtuale utilizzando l'account amministratore locale ed esaminare il CommandExecuti'n.log in  
   
   C:. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 

   > [!NOTE]
   > Se l'estensione viene riavviata dopo l'errore iniziale, il log con l'errore di distribuzione verrà salvato come CommandExecution_YYYYMMDDHHMMSSSSS.log. "
1. Aprire un prompt dei comandi nella macchina virtuale e verificare queste query sull'endpoint del servizio metadati dell'istanza (IMDS) in esecuzione nell'host di Azure restituisce:Open a command prompt on the VM and verify these queries against the Instance Metadata Service (IMDS) Endpoint running on the Azure host returns:

   | Comando da eseguire | Output previsto |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Informazioni corrette sulla macchina virtuale di Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | ID tenant valido associato alla sottoscrizione di AzureValid Tenant ID associated with the Azure Subscription |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Token di accesso valido rilasciato da Azure Active Directory per l'identità gestita assegnata a questa macchina virtuale |

   > [!NOTE]
   > Il token di accesso può essere [http://calebb.net/](http://calebb.net/)decodificato utilizzando uno strumento come . Verificare che "appid" nel token di accesso corrisponda all'identità gestita assegnata alla macchina virtuale.

1. Verificare che gli endpoint necessari siano accessibili dalla macchina virtuale tramite la riga di comando:Ensure the required endpoints are accessible from the VM using the command line:
   
   - curl https:\//login.microsoftonline.com/ -D –
   - curl https:\/`<TenantID>`/login.microsoftonline.com/ / -D –

   > [!NOTE]
   > Sostituire `<TenantID>` con l'ID tenant di Azure AD associato alla sottoscrizione di Azure.Replace with the Azure AD Tenant ID that is associated with the Azure subscription.

   - curl https:\//enterpriseregistration.windows.net/ -D -
   - curl https:\//device.login.microsoftonline.com/ -D -
   - curl https:\//pas.windows.net/ -D -

1. Lo stato del dispositivo `dsregcmd /status`può essere visualizzato eseguendo . L'obiettivo è che `AzureAdJoined : YES`lo stato del dispositivo venga visualizzato come .

   > [!NOTE]
   > L'attività di aggiunta ad Azure AD viene acquisita nel Visualizzatore eventi nel log Registrazione dispositivo utente/Amministrazione.

Se l'estensione AADLoginForWindows non riesce con un determinato codice di errore, è possibile eseguire la procedura seguente:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problema 1: AADLoginForWindows estensione non riesce a installare con il codice di errore del terminale '1007' e il codice di uscita: -2145648574.

Questo codice di uscita viene convertito in DSREG_E_MSI_TENANTID_UNAVAILABLE perché l'estensione non è in grado di eseguire query sulle informazioni del tenant di Azure AD.

1. Verificare che la macchina virtuale di Azure possa recuperare il TenantID dal servizio metadati dell'istanza.

   - RDP to the VM as a local administrator and verify the endpoint returns valid Tenant ID by running this command from an elevated command line on the VM:
      
      - curl -H Metadati:truehttp://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. L'amministratore della macchina virtuale tenta di installare l'estensione AADLoginForWindows, ma un'identità gestita a cui è assegnata dal sistema non ha prima abilitata la macchina virtuale. Passare al pannello Identità della macchina virtuale. Dalla scheda Sistema assegnato, verificare che lo stato sia attivato.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problema 2: AADLoginForWindows estensione non riesce a installare con il codice di uscita: -2145648607

Questo codice di uscita si traduce in DSREG_AUTOJOIN_DISC_FAILED `https://enterpriseregistration.windows.net` perché l'estensione non è in grado di raggiungere l'endpoint.

1. Verificare che gli endpoint necessari siano accessibili dalla macchina virtuale tramite la riga di comando:Verify the required endpoints are accessible from the VM using the command line:

   - curl https:\//login.microsoftonline.com/ -D –
   - curl https:\/`<TenantID>`/login.microsoftonline.com/ / -D –
   
   > [!NOTE]
   > Sostituire `<TenantID>` con l'ID tenant di Azure AD associato alla sottoscrizione di Azure.Replace with the Azure AD Tenant ID that is associated with the Azure subscription. Se è necessario trovare l'ID tenant, è possibile passare il mouse sul nome dell'account per ottenere l'ID directory/tenant oppure selezionare Azure Active Directory > Proprietà > ID directory nel portale di Azure.If you need to find the tenant ID, you can hover over your account name to get the directory/tenant ID, or select Azure Active Directory > Properties > Directory ID in the Azure portal.

   - curl https:\//enterpriseregistration.windows.net/ -D -
   - curl https:\//device.login.microsoftonline.com/ -D -
   - curl https:\//pas.windows.net/ -D -

1. Se uno dei comandi non riesce `<URL>`con "Impossibile risolvere l'host", provare a eseguire questo comando per determinare il server DNS utilizzato dalla macchina virtuale.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Sostituire `<URL>` con i nomi di dominio completi utilizzati dagli endpoint, ad esempio "login.microsoftonline.com".

1. Successivamente, verificare se specificare un server DNS pubblico consente al comando di avere esito positivo:Next, see if specifying a public DNS server allows the command to succeed:

   `nslookup <URL> 208.67.222.222`

1. Se necessario, modificare il server DNS assegnato al gruppo di sicurezza di rete a cui appartiene la macchina virtuale di Azure.If necessary, change the DNS server that is assigned to the network security group that the Azure VM belongs to.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problema 3: AADLoginForWindows estensione non riesce a installare con il codice di uscita: 51

Il codice di uscita 51 viene convertito in "Questa estensione non è supportata nel sistema operativo della macchina virtuale".

In Anteprima pubblica, l'estensione AADLoginForWindows deve essere installata solo in Windows Server 2019 o Windows 10 (Build 1809 o versione successiva). Verificare che la versione di Windows sia supportata. Se la build di Windows non è supportata, disinstallare l'estensione della macchina virtuale.

### <a name="troubleshoot-sign-in-issues"></a>Risolvere i problemi di accesso

Alcuni errori comuni quando si tenta di RDP con le credenziali di Azure AD includono nessun ruolo RBAC assegnato, client non autorizzato o metodo di accesso 2FA richiesto. Utilizzare le informazioni seguenti per risolvere questi problemi.

Lo stato Dispositivo e SSO `dsregcmd /status`possono essere visualizzati eseguendo . L'obiettivo è che `AzureAdJoined : YES` Lo `SSO State` stato `AzureAdPrt : YES`del dispositivo mostri come e mostri .

Inoltre, l'accesso RDP con gli account di Azure AD viene acquisito nel Visualizzatore eventi nei registri eventi di AAD.Operational.

#### <a name="rbac-role-not-assigned"></a>ruolo Controllo degli accessi in base al ruolo non assegnato

Se viene visualizzato il seguente messaggio di errore quando si avvia una connessione desktop remoto alla macchina virtuale: 

- Il tuo account è configurato per impedirti di utilizzare questo dispositivo. Per ulteriori informazioni, contattare l'amministratore di sistema

![Il tuo account è configurato per impedirti di utilizzare questo dispositivo.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Verificare di aver [configurato i criteri RBAC](../../virtual-machines/linux/login-using-aad.md) per la macchina virtuale che concede all'utente il ruolo Virtual Machine Administrator Login o Virtual Machine User Login:
 
#### <a name="unauthorized-client"></a>Client non autorizzato

Se viene visualizzato il seguente messaggio di errore quando si avvia una connessione desktop remoto alla macchina virtuale: 

- Le tue credenziali non hanno funzionato

![Le tue credenziali non hanno funzionato](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Verificare che il PC Windows 10 usato per avviare la connessione desktop remoto sia uno dei componenti aggiunti ad Azure AD o dell'aggiunta ad Azure AD ibrido alla stessa directory di Azure AD a cui viene aggiunta la macchina virtuale. Per ulteriori informazioni sull'identità del dispositivo, vedere l'articolo [Che cos'è un'identità del dispositivo](/azure/active-directory/devices/overview).

> [!NOTE]
> Windows 10 20H1 aggiungerà il supporto per il PC registrato di Azure AD per avviare la connessione desktop remoto alla macchina virtuale. Partecipa al Programma Windows Insider per provare questo ed esplorare le nuove funzionalità di Windows 10.

Verificare inoltre che l'estensione AADLoginForWindows non sia stata disinstallata dopo il completamento dell'aggiunta ad Azure AD.
 
#### <a name="mfa-sign-in-method-required"></a>È richiesto il metodo di accesso MFA

Se viene visualizzato il seguente messaggio di errore quando si avvia una connessione desktop remoto alla macchina virtuale: 

- Il metodo di accesso che si sta tentando di utilizzare non è consentito. Provare con un metodo di accesso diverso o contattare l'amministratore di sistema.

![Il metodo di accesso che si sta tentando di utilizzare non è consentito.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Se è stato configurato un criterio di accesso condizionale che richiede l'autenticazione a più fattori (MFA) prima di poter accedere alla risorsa, è necessario assicurarsi che il PC Windows 10 che accede la connessione desktop remoto alla macchina virtuale metodo di autenticazione, ad esempio Windows Hello. Se non si utilizza un metodo di autenticazione avanzata per la connessione desktop remoto, verrà visualizzato l'errore precedente.

Se non è stato distribuito Windows Hello for Business e per il momento non è disponibile un'opzione, è possibile escludere il requisito dell'autenticazione a più fattori configurando i criteri di accesso condizionale che escludono l'app "Accesso tramite macchine virtuali di Windows Azure" dall'elenco delle app cloud che richiedono l'autenticazione a più fattori. Per altre informazioni su Windows Hello for Business, vedere [Panoramica](/windows/security/identity-protection/hello-for-business/hello-identity-verification)di Windows Hello for Business .

> [!NOTE]
> L'autenticazione PIN di Windows Hello for Business con RDP è stata supportata da Windows 10 per diverse versioni, tuttavia il supporto per l'autenticazione biometrica con RDP è stato aggiunto in Windows 10 versione 1809. L'uso dell'autenticazione di Windows Hello for Business durante RDP è disponibile solo per le distribuzioni che usano il modello di attendibilità del certificato e che attualmente non sono disponibili per il modello di attendibilità chiave.
 
## <a name="preview-feedback"></a>Feedback sull'anteprima

Condividere i commenti e suggerimenti su questa funzionalità di anteprima o segnalare problemi usandolo nel forum dei commenti e suggerimenti di [Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Active Directory, vedere [Informazioni su Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
