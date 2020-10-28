---
title: Accedere a una macchina virtuale Windows in Azure usando Azure Active Directory (anteprima)
description: Azure AD accedere a una macchina virtuale di Azure che esegue Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85bbdff2f7e67434a3e21aaf51af96c1e851eb0d
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740186"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Accedere a una macchina virtuale Windows in Azure usando l'autenticazione Azure Active Directory (anteprima)

Le organizzazioni possono ora usare l'autenticazione Azure Active Directory (AD) per le macchine virtuali (VM) di Azure che eseguono **Windows Server 2019 Datacenter Edition** o **Windows 10 1809** e versioni successive. L'uso di Azure AD per l'autenticazione nelle macchine virtuali consente di controllare e applicare in modo centralizzato i criteri. Strumenti come il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e Azure AD l'accesso condizionale consentono di controllare chi può accedere a una macchina virtuale. Questo articolo illustra come creare e configurare una macchina virtuale Windows Server 2019 per usare l'autenticazione Azure AD.

> [!NOTE]
> Azure AD l'accesso per le macchine virtuali Windows di Azure è una funzionalità di anteprima pubblica di Azure Active Directory. Per ulteriori informazioni sulle anteprime, vedere la pagina relativa alle  [condizioni per l'utilizzo aggiuntive per Microsoft Azure anteprime](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L'uso dell'autenticazione Azure AD per accedere alle macchine virtuali Windows in Azure offre molti vantaggi, tra cui:

- Utilizzare le stesse credenziali di Azure AD federate o gestite utilizzate normalmente.
- Non è più necessario gestire gli account di amministratore locale.
- Il controllo degli accessi in base al ruolo di Azure consente di concedere l'accesso appropriato alle macchine virtuali in base alla necessità e di rimuoverlo quando non è più necessario.
- Prima di consentire l'accesso a una macchina virtuale, Azure AD l'accesso condizionale può applicare requisiti aggiuntivi, ad esempio: 
   - Autenticazione a più fattori
   - Controllo del rischio di accesso
- Automatizzare e ridimensionare Azure AD join di macchine virtuali Windows di Azure che fanno parte delle distribuzioni VDI.

> [!NOTE]
> Una volta abilitata questa funzionalità, le macchine virtuali Windows in Azure verranno Azure AD Unite. Non è possibile aggiungerlo ad altri domini come AD locale o Azure AD DS. Se necessario, è necessario disconnettere la macchina virtuale dal tenant di Azure AD disinstallando l'estensione.

## <a name="requirements"></a>Requisiti

### <a name="supported-azure-regions-and-windows-distributions"></a>Aree di Azure supportate e distribuzioni di Windows

Durante l'anteprima di questa funzionalità sono attualmente supportate le distribuzioni di Windows seguenti:

- Windows Server 2019 Datacenter
- Windows 10 1809 e versioni successive

> [!IMPORTANT]
> La connessione remota alle macchine virtuali Unite a Azure AD è consentita solo da PC Windows 10 Azure AD registrati (a partire da Windows 10 20H1) Azure AD aggiunti o ibridi Azure AD aggiunti alla **stessa** directory della macchina virtuale. 

Durante l'anteprima di questa funzionalità sono attualmente supportate le aree di Azure seguenti:

- Tutte le aree globali di Azure

> [!IMPORTANT]
> Per usare questa funzionalità di anteprima, distribuire solo una distribuzione di Windows supportata e in un'area di Azure supportata. La funzionalità non è attualmente supportata in Azure per enti pubblici o cloud sovrani.

### <a name="network-requirements"></a>Requisiti di rete

Per abilitare l'autenticazione Azure AD per le macchine virtuali Windows in Azure, è necessario assicurarsi che la configurazione di rete delle macchine virtuali consenta l'accesso in uscita agli endpoint seguenti sulla porta TCP 443:

- https: \/ /enterpriseregistration.Windows.NET
- https:\//login.microsoftonline.com
- https: \/ /Device.login.microsoftonline.com
- https: \/ /pas.Windows.NET

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Abilitazione dell'accesso Azure AD in per la macchina virtuale Windows in Azure

Per usare Azure AD account di accesso in per la macchina virtuale Windows in Azure, è prima necessario abilitare l'opzione di accesso Azure AD per la macchina virtuale Windows e quindi è necessario configurare le assegnazioni di ruolo di Azure per gli utenti autorizzati ad accedere alla macchina virtuale.
Esistono diversi modi per abilitare l'accesso Azure AD per la VM Windows:

- Uso dell'esperienza portale di Azure durante la creazione di una macchina virtuale Windows
- Uso dell'esperienza Azure Cloud Shell durante la creazione di una macchina virtuale Windows **o di una macchina virtuale Windows esistente**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Uso di portale di Azure creare una macchina virtuale per abilitare l'accesso Azure AD

È possibile abilitare l'accesso Azure AD per le immagini di macchina virtuale Windows Server 2019 Datacenter o Windows 10 1809 e versioni successive. 

Per creare una macchina virtuale Windows Server 2019 datacenter in Azure con Azure AD logon: 

1. Accedere al [portale di Azure](https://portal.azure.com)con un account che abbia accesso per creare macchine virtuali e selezionare **+ Crea una risorsa** .
1. Digitare **Windows Server** in Cerca nella barra di ricerca del Marketplace.
   1. Fare clic su **Windows Server** e scegliere **Windows Server 2019 datacenter** dall'elenco a discesa selezionare un piano software.
   1. Fare clic su **Crea** .
1. Nella scheda "gestione" abilitare l'opzione per l' **accesso con le credenziali di AAD (anteprima)** nella sezione Azure Active Directory da off a **on** .
1. Verificare che l' **identità gestita assegnata dal sistema** nella sezione Identity sia impostata **su on** . Questa azione deve essere eseguita automaticamente dopo aver abilitato l'accesso con Azure AD credenziali.
1. Esaminare il resto dell'esperienza di creazione di una macchina virtuale. Durante questa fase di anteprima sarà necessario creare un nome utente e una password di amministratore per la macchina virtuale.

![Accedi con credenziali di Azure AD creare una macchina virtuale](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Per accedere alla macchina virtuale usando le credenziali Azure AD, è necessario prima configurare le assegnazioni di ruolo per la macchina virtuale, come descritto in una delle sezioni seguenti.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Uso dell'esperienza Azure Cloud Shell per abilitare l'accesso Azure AD

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Gli strumenti comuni di Azure sono preinstallati e configurati in Cloud Shell per l'uso con l'account. È sufficiente selezionare il pulsante Copia per copiare il codice, incollarlo in Cloud Shell e quindi premere Invio per eseguirlo. Esistono alcuni modi per aprire Cloud Shell:

Selezionare Prova nell'angolo superiore destro di un blocco di codice.
Aprire Cloud Shell nel browser.
Selezionare il pulsante Cloud Shell nel menu nell'angolo in alto a destra del [portale di Azure](https://portal.azure.com).

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.31 o successiva. Eseguire az --version per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere l'articolo [installare l'interfaccia](/cli/azure/install-azure-cli)della riga di comando di Azure.

1. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#az-group-create). 
1. Creare una macchina virtuale con [AZ VM create](/cli/azure/vm#az-vm-create) usando una distribuzione supportata in un'area supportata. 
1. Installare l'estensione della macchina virtuale Azure AD login. 

L'esempio seguente distribuisce una macchina virtuale denominata myVM che usa Win2019Datacenter in un gruppo di risorse denominato myResourceGroup nell'area southcentralus. Negli esempi seguenti è possibile specificare i nomi del proprio gruppo di risorse e della macchina virtuale in base alle esigenze.

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
> È necessario abilitare l'identità gestita assegnata dal sistema nella macchina virtuale prima di installare l'estensione della VM di accesso Azure AD.

La creazione della VM e delle risorse di supporto richiede alcuni minuti.

Infine, installare l'estensione della macchina virtuale Azure AD login per abilitare Azure AD account di accesso per la macchina virtuale Windows. Le estensioni della macchina virtuale sono piccole applicazioni che eseguono attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure. Usare [AZ VM Extension](/cli/azure/vm/extension#az-vm-extension-set) set per installare l'estensione AADLoginForWindows nella macchina virtuale denominata myVM nel gruppo di risorse myResourceGroup:

> [!NOTE]
> È possibile installare l'estensione AADLoginForWindows in una macchina virtuale Windows Server 2019 o Windows 10 1809 e versioni successive esistente per abilitarla per l'autenticazione Azure AD. Di seguito è riportato un esempio di AZ CLI.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Il `provisioningState` di `Succeeded` viene visualizzato quando l'estensione viene installata nella macchina virtuale.

## <a name="configure-role-assignments-for-the-vm"></a>Configurare le assegnazioni di ruolo per la macchina virtuale

Ora che è stata creata la macchina virtuale, è necessario configurare i criteri di controllo degli accessi in base al ruolo di Azure per determinare chi può accedere alla macchina virtuale. Per autorizzare l'accesso alla macchina virtuale vengono usati due ruoli di Azure:

- **Account di accesso amministratore macchina virtuale** : gli utenti con questo ruolo assegnati possono accedere a una macchina virtuale di Azure con privilegi di amministratore.
- **Accesso utente alle macchine virtuali** : gli utenti a cui è stato assegnato questo ruolo possono accedere a una macchina virtuale di Azure con i privilegi di utente normale.

> [!NOTE]
> Per consentire a un utente di accedere alla VM tramite RDP, è necessario assegnare l'account di accesso dell'amministratore della macchina virtuale o del ruolo di accesso utente della macchina virtuale. Un utente di Azure con i ruoli proprietario o collaboratore assegnati a una macchina virtuale non dispone automaticamente dei privilegi necessari per accedere alla macchina virtuale tramite RDP. Questo consente di fornire la separazione controllata tra il set di persone che controllano le macchine virtuali e il set di persone che possono accedere alle macchine virtuali.

È possibile configurare le assegnazioni di ruolo per la macchina virtuale in diversi modi:

- Uso dell'esperienza del portale di Azure AD
- Uso dell'esperienza Azure Cloud Shell

### <a name="using-azure-ad-portal-experience"></a>Uso dell'esperienza del portale di Azure AD

Per configurare le assegnazioni di ruolo per le macchine virtuali Windows Server 2019 datacenter abilitate per la Azure AD:

1. Passare alla pagina di panoramica della macchina virtuale specifica
1. Selezionare **controllo di accesso (IAM)** dalle opzioni di menu
1. Selezionare **Aggiungi** , **Aggiungi assegnazione ruolo** per aprire il riquadro Aggiungi assegnazione ruolo.
1. Nell'elenco a discesa **ruolo** selezionare un ruolo, ad esempio account di **accesso amministratore macchina virtuale** o **accesso utente macchina virtuale** .
1. Nel campo **Seleziona** selezionare un utente, un gruppo, un'entità servizio o un'identità gestita. Se l'entità di sicurezza non è visualizzata nell'elenco, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati, gli indirizzi e-mail e gli identificatori di oggetto.
1. Selezionare **Save (Salva** ) per assegnare il ruolo.

Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo per l'ambito selezionato.

![Assegnare i ruoli agli utenti che accedono alla macchina virtuale](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Uso dell'esperienza Azure Cloud Shell

L'esempio seguente usa [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) per assegnare il ruolo Accesso amministratore alle macchine virtuali alla macchina virtuale per l'utente di Azure corrente. Il nome utente dell'account di Azure attivo si ottiene con [az account show](/cli/azure/account#az-account-show) e viene impostato l'ambito per la macchina virtuale creata in un passaggio precedente con [az vm show](/cli/azure/vm#az-vm-show). L'ambito può essere assegnato anche a livello di gruppo di risorse o di sottoscrizione e si applicano le normali autorizzazioni di ereditarietà RBAC di Azure. Per altre informazioni, vedere [accedere a una macchina virtuale Linux in Azure usando l'autenticazione Azure Active Directory](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Se il dominio di AAD e il dominio del nome utente di accesso non corrispondono, è necessario specificare l'ID oggetto dell'account utente con `--assignee-object-id` , non solo il nome utente per `--assignee` . È possibile ottenere l'ID oggetto per l'account utente con [az ad user list](/cli/azure/ad/user#az-ad-user-list).

Per altre informazioni su come usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso alle risorse della sottoscrizione di Azure, vedere gli articoli seguenti:

- [Aggiungere o rimuovere assegnazioni di ruolo di Azure tramite l'interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md)
- [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md)
- [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Uso dell'accesso condizionale

È possibile applicare criteri di accesso condizionale, ad esempio l'autenticazione a più fattori o il controllo dei rischi di accesso utente prima di autorizzare l'accesso alle macchine virtuali Windows in Azure abilitate con Azure AD accedi. Per applicare i criteri di accesso condizionale, è necessario selezionare l'opzione "accesso alla macchina virtuale Windows di Azure" dall'opzione di assegnazione app Cloud o azioni e quindi usare il rischio di accesso come condizione e/o richiedere l'autenticazione a più fattori come controllo di concessione dell'accesso. 

> [!NOTE]
> Se si usa "Richiedi autenticazione a più fattori" come controllo di concessione dell'accesso per richiedere l'accesso all'app "accesso alle macchine virtuali Windows di Azure", è necessario fornire l'attestazione di autenticazione a più fattori come parte del client che avvia la sessione RDP alla macchina virtuale Windows di destinazione in Azure. L'unico modo per ottenere questo risultato in un client Windows 10 consiste nell'usare il PIN di Windows Hello for business o l'autenticazione biometrica con il client RDP. Il supporto per l'autenticazione biometrica è stato aggiunto al client RDP in Windows 10 versione 1809. Il desktop remoto che usa l'autenticazione di Windows Hello for business è disponibile solo per le distribuzioni che usano il modello di attendibilità del certificato e attualmente non sono disponibili per il modello di attendibilità

> [!WARNING]
> La Multi-Factor Authentication di Azure abilitata/applicata per utente non è supportata per l'accesso alla macchina virtuale.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Accedere con le credenziali Azure AD a una macchina virtuale Windows

> [!IMPORTANT]
> La connessione remota alle macchine virtuali Unite a Azure AD è consentita solo da PC Windows 10 che sono Azure AD registrati (la compilazione minima richiesta è 20H1) o Azure AD aggiunti o ibridi Azure AD aggiunti alla **stessa** directory della macchina virtuale. Inoltre, per RDP con Azure AD credenziali, l'utente deve appartenere a uno dei due ruoli di Azure, l'account di accesso dell'amministratore della macchina virtuale o l'account di accesso utente della macchina virtuale. Se si usa un Azure AD PC Windows 10 registrato, è necessario immettere le credenziali nel formato AzureAD\UPN, ad AzureAD\john@contoso.com esempio. Attualmente, non è possibile usare Azure Bastion per accedere usando l'autenticazione di Azure Active Directory con l'estensione AADLoginForWindows. è supportato solo il protocollo RDP diretto.

Per accedere alla macchina virtuale Windows Server 2019 usando Azure AD: 

1. Passare alla pagina Panoramica della macchina virtuale abilitata con Azure AD accesso.
1. Selezionare **Connetti** per aprire il pannello Connetti a macchina virtuale.
1. Selezionare **Scarica file RDP** .
1. Selezionare **Apri** per avviare il client connessione Desktop remoto.
1. Selezionare **Connetti** per aprire la finestra di dialogo di accesso di Windows.
1. Accedere usando le credenziali Azure AD.

A questo punto è stato effettuato l'accesso alla macchina virtuale di Azure di Windows Server 2019 con le autorizzazioni del ruolo assegnate, ad esempio utente VM o amministratore VM. 

> [!NOTE]
> È possibile salvare il. File RDP in locale nel computer per avviare connessioni Desktop remoto future alla macchina virtuale anziché dover passare alla pagina Panoramica macchina virtuale nella portale di Azure e usare l'opzione Connetti.

## <a name="troubleshoot"></a>Risolvere problemi

### <a name="troubleshoot-deployment-issues"></a>Risolvere i problemi relativi alla distribuzione

Per completare la Azure AD processo join, è necessario che l'estensione AADLoginForWindows sia installata correttamente. Se l'installazione dell'estensione della macchina virtuale non riesce correttamente, seguire questa procedura.

1. Eseguire la connessione RDP alla VM usando l'account Administrator locale ed esaminare CommandExecuti'n. log in  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Se l'estensione viene riavviata dopo l'errore iniziale, il log con l'errore di distribuzione verrà salvato come CommandExecution_YYYYMMDDHHMMSSSSS. log. "
1. Aprire un prompt dei comandi di PowerShell nella macchina virtuale e verificare che le query eseguite sull'endpoint del servizio metadati dell'istanza (IMDS) in esecuzione nell'host di Azure restituiscono:

   | Comando da eseguire | Output previsto |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Correggere le informazioni sulla macchina virtuale di Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | ID tenant valido associato alla sottoscrizione di Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Token di accesso valido emesso da Azure Active Directory per l'identità gestita assegnata a questa macchina virtuale |

   > [!NOTE]
   > Il token di accesso può essere decodificato usando uno strumento come [http://calebb.net/](http://calebb.net/) . Verificare che "AppID" nel token di accesso corrisponda all'identità gestita assegnata alla macchina virtuale.

1. Verificare che gli endpoint necessari siano accessibili dalla macchina virtuale tramite la riga di comando:
   
   - curl https: \/ /login.microsoftonline.com/-D-
   - curl https: \/ /login.microsoftonline.com/ `<TenantID>` /-D-

   > [!NOTE]
   > Sostituire `<TenantID>` con il Azure ad ID tenant associato alla sottoscrizione di Azure.

   - curl https: \/ /enterpriseregistration.Windows.NET/-D-
   - curl https: \/ /Device.login.microsoftonline.com/-D-
   - curl https: \/ /pas.Windows.NET/-D-

1. Lo stato del dispositivo può essere visualizzato eseguendo `dsregcmd /status` . L'obiettivo è quello di visualizzare lo stato del dispositivo come `AzureAdJoined : YES` .

   > [!NOTE]
   > Azure AD attività di join viene acquisita nel Visualizzatore eventi nel registro Registration\Admin del dispositivo utente.

Se l'estensione AADLoginForWindows ha esito negativo con un codice di errore specifico, è possibile eseguire i passaggi seguenti:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problema 1: l'installazione dell'estensione AADLoginForWindows non riesce con codice di errore terminale ' 1007' e codice di uscita:-2145648574.

Questo codice di uscita viene convertito in DSREG_E_MSI_TENANTID_UNAVAILABLE perché l'estensione non è in grado di eseguire query sulle informazioni del tenant di Azure AD.

1. Verificare che la macchina virtuale di Azure possa recuperare il TenantID dal servizio metadati dell'istanza.

   - Connettersi tramite RDP alla macchina virtuale come amministratore locale e verificare che l'endpoint restituisca un ID tenant valido eseguendo questo comando da una riga di comando con privilegi elevati nella macchina virtuale:
      
      - Metadati curl-H: true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. L'amministratore della macchina virtuale tenta di installare l'estensione AADLoginForWindows, ma un'identità gestita assegnata dal sistema non ha abilitato prima la macchina virtuale. Passare al pannello identità della macchina virtuale. Dalla scheda assegnazione sistema verificare che lo stato sia impostato su attivato.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problema 2: l'installazione dell'estensione AADLoginForWindows non riesce con codice di uscita:-2145648607

Questo codice di uscita viene convertito in DSREG_AUTOJOIN_DISC_FAILED perché l'estensione non è in grado di raggiungere l' `https://enterpriseregistration.windows.net` endpoint.

1. Verificare che gli endpoint necessari siano accessibili dalla macchina virtuale tramite la riga di comando:

   - curl https: \/ /login.microsoftonline.com/-D-
   - curl https: \/ /login.microsoftonline.com/ `<TenantID>` /-D-
   
   > [!NOTE]
   > Sostituire `<TenantID>` con il Azure ad ID tenant associato alla sottoscrizione di Azure. Se è necessario trovare l'ID tenant, è possibile passare il puntatore del mouse sul nome dell'account per ottenere l'ID directory/tenant oppure selezionare Azure Active Directory > proprietà > ID directory nel portale di Azure.

   - curl https: \/ /enterpriseregistration.Windows.NET/-D-
   - curl https: \/ /Device.login.microsoftonline.com/-D-
   - curl https: \/ /pas.Windows.NET/-D-

1. Se uno dei comandi ha esito negativo con "Impossibile risolvere `<URL>` l'host", provare a eseguire questo comando per determinare il server DNS usato dalla macchina virtuale.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Sostituire `<URL>` con i nomi di dominio completi utilizzati dagli endpoint, ad esempio "login.microsoftonline.com".

1. Verificare quindi se la specifica di un server DNS pubblico consente il comando per la riuscita:

   `nslookup <URL> 208.67.222.222`

1. Se necessario, modificare il server DNS assegnato al gruppo di sicurezza di rete a cui appartiene la macchina virtuale di Azure.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problema 3: l'installazione dell'estensione AADLoginForWindows non riesce con codice di uscita: 51

Il codice di uscita 51 viene convertito in "questa estensione non è supportata nel sistema operativo della macchina virtuale".

Nell'anteprima pubblica, l'estensione AADLoginForWindows è destinata solo a essere installata in Windows Server 2019 o Windows 10 (Build 1809 o versione successiva). Verificare che la versione di Windows sia supportata. Se la compilazione di Windows non è supportata, disinstallare l'estensione della macchina virtuale.

### <a name="troubleshoot-sign-in-issues"></a>Risolvere i problemi di accesso

Alcuni errori comuni quando si tenta di eseguire la connessione RDP con Azure AD credenziali includono nessun ruolo di Azure assegnato, client non autorizzato o metodo di accesso 2FA richiesto. Per risolvere questi problemi, utilizzare le seguenti informazioni.

Il dispositivo e lo stato SSO possono essere visualizzati eseguendo `dsregcmd /status` . L'obiettivo è quello di visualizzare lo stato del dispositivo come `AzureAdJoined : YES` e `SSO State` `AzureAdPrt : YES` .

Inoltre, l'accesso RDP con account Azure AD viene acquisito nel Visualizzatore eventi nei registri eventi di AAD\Operational.

#### <a name="azure-role-not-assigned"></a>Ruolo di Azure non assegnato

Se viene visualizzato il messaggio di errore seguente quando si avvia una connessione Desktop remoto alla macchina virtuale: 

- L'account è configurato in modo da impedire l'uso di questo dispositivo. Per ulteriori informazioni, contattare l'amministratore di sistema

![L'account è configurato in modo da impedire l'uso di questo dispositivo.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Verificare di aver [configurato i criteri](../../virtual-machines/linux/login-using-aad.md) di controllo degli accessi in base al ruolo di Azure per la macchina virtuale che concede all'utente il ruolo di accesso dell'amministratore della macchina virtuale o dell'utente della macchina virtuale
 
#### <a name="unauthorized-client"></a>Client non autorizzato

Se viene visualizzato il messaggio di errore seguente quando si avvia una connessione Desktop remoto alla macchina virtuale: 

- Le credenziali non sono state utilizzate

![Le credenziali non sono state utilizzate](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Verificare che il PC Windows 10 che si sta usando per avviare la connessione Desktop remoto sia un Azure AD Unito o ibrido Azure AD aggiunto alla stessa directory di Azure AD a cui è stata aggiunta la macchina virtuale. Per altre informazioni sull'identità del dispositivo, vedere l'articolo [che cos'è un'identità del dispositivo](./overview.md).

> [!NOTE]
> Windows 10 Build 20H1 è stato aggiunto il supporto per un computer Azure AD registrato per avviare la connessione RDP alla macchina virtuale. Quando si usa Azure AD un computer registrato (non Azure AD aggiunto o ibrido Azure AD aggiunto) come client RDP per avviare le connessioni alla macchina virtuale, è necessario immettere le credenziali nel formato AzureAD\UPn (ad esempio AzureAD\john@contoso.com ).

Verificare inoltre che l'estensione AADLoginForWindows non sia stata disinstallata dopo il completamento di Azure AD join.
 
#### <a name="mfa-sign-in-method-required"></a>È necessario il metodo di accesso a multi-factor authentication

Se viene visualizzato il messaggio di errore seguente quando si avvia una connessione Desktop remoto alla macchina virtuale: 

- Il metodo di accesso che si sta tentando di usare non è consentito. Provare a usare un altro metodo di accesso o contattare l'amministratore di sistema.

![Il metodo di accesso che si sta tentando di usare non è consentito.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Se sono stati configurati criteri di accesso condizionale che richiedono l'autenticazione a più fattori prima di poter accedere alla risorsa, è necessario assicurarsi che il PC Windows 10 che avvia la connessione Desktop remoto alla VM esegua l'accesso usando un metodo di autenticazione avanzata, ad esempio Windows Hello. Se non si usa un metodo di autenticazione avanzata per la connessione Desktop remoto, verrà visualizzato l'errore precedente.

Se Windows Hello for business non è stato distribuito e se questa opzione non è disponibile per il momento, è possibile escludere il requisito di autenticazione a più fattori configurando i criteri di accesso condizionale che escludono l'app di accesso alle macchine virtuali Windows di Azure dall'elenco di app cloud che richiedono l'autenticazione a più fattori. Per altre informazioni su Windows Hello for business, vedere [Panoramica di Windows Hello for business](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> L'autenticazione PIN di Windows Hello for business con RDP è stata supportata da Windows 10 per diverse versioni, tuttavia è stato aggiunto il supporto per l'autenticazione biometrica con RDP in Windows 10 versione 1809. L'uso dell'autenticazione di Windows Hello for business durante RDP è disponibile solo per le distribuzioni che usano il modello di attendibilità del certificato e attualmente non disponibili per il modello di attendibilità della chiave
 
## <a name="preview-feedback"></a>Feedback sull'anteprima

Condividi i tuoi commenti e suggerimenti su questa funzionalità di anteprima o segnala i problemi usandola nel [Forum dei commenti Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Active Directory, vedere [Informazioni su Azure Active Directory](../fundamentals/active-directory-whatis.md)
