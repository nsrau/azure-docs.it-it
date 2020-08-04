---
title: Accedere a una VM Linux con Azure Active Directory credenziali
description: Informazioni su come creare e configurare una macchina virtuale Linux per accedere usando l'autenticazione Azure Active Directory.
author: SanDeo-MSFT
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/29/2019
ms.author: sandeo
ms.openlocfilehash: 96fb914b5dafe5eb818f2b491bbe2d856763bd02
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534737"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Anteprima: accedere a una macchina virtuale Linux in Azure usando l'autenticazione Azure Active Directory

Per migliorare la sicurezza delle macchine virtuali Linux in Azure, è possibile eseguire l'integrazione con l'autenticazione di Azure Active Directory. Quando si usa l'autenticazione di Azure AD per le macchine virtuali Linux, i criteri che consentono o negano l'accesso alle macchine virtuali sono controllati e applicati dall'utente a livello centrale. Questo articolo illustra come creare e configurare una macchina virtuale Linux per usare l'autenticazione di Azure AD.


> [!IMPORTANT]
> L'autenticazione Azure Active Directory è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Usare questa funzionalità in una macchina virtuale di test che si prevede di rimuovere al termine del test.
>


L'uso dell'autenticazione di Azure AD per accedere alle macchine virtuali Linux in Azure comporta molti vantaggi, tra cui:

- **Sicurezza migliorata:**
  - È possibile usare le credenziali aziendali di Active Directory per accedere alle macchine virtuali Linux di Azure. Non è necessario creare account amministratore locali e gestirne le credenziali per tutta la loro durata.
  - Riducendo la dipendenza dagli account amministratore locali, non è necessario preoccuparsi della perdita o del furto delle credenziali, degli utenti che configurano credenziali vulnerabili e così via.
  - I criteri di durata e la complessità della password configurati per la directory di Azure AD contribuiscono a proteggere le macchine virtuali Linux.
  - Per aumentare la sicurezza dell'accesso alle macchine virtuali Azure, è possibile configurare l'autenticazione a più fattori.
  - Possono accedere alle macchine virtuali Linux con Azure Active Directory anche gli utenti che usano [Federation Services](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Collaborazione semplificata:** con il controllo degli accessi in base al ruolo è possibile specificare chi può accedere a una determinata macchina virtuale come utente normale o con privilegi di amministratore. Quando gli utenti si uniscono o abbandonano il team, è possibile aggiornare i criteri del controllo degli accessi in base al ruolo per la macchina virtuale per concedere l'accesso in base alle esigenze. Questa esperienza è molto più semplice rispetto alla necessità di eseguire lo scrubbing delle macchine virtuali per rimuovere le chiavi pubbliche SSH non necessarie. Quando i dipendenti lasciano l'organizzazione e l'account utente viene disabilitato o rimosso da Azure AD, non hanno più accesso alle risorse.

## <a name="supported-azure-regions-and-linux-distributions"></a>Aree di Azure e distribuzioni di Linux supportate

Durante l'anteprima di questa funzionalità sono attualmente supportate le distribuzioni di Linux seguenti:

| Distribuzione | Versione |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SuSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 e Ubuntu Server 18.04 |


Durante l'anteprima di questa funzionalità sono attualmente supportate le aree di Azure seguenti:

- Tutte le aree di Azure globali

>[!IMPORTANT]
> Per usare questa funzionalità in anteprima, basta distribuire una distribuzione Linux supportata in un'area di Azure supportata. La funzionalità non è supportata in Azure per enti pubblici o nei cloud sovrani.
>
> Non è supportata l'uso di questa estensione nei cluster Azure Kubernetes Service (AKS). Per altre informazioni, vedere [criteri di supporto per AKS](../../aks/support-policies.md).


Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.31 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Requisiti di rete

Per abilitare l'autenticazione Azure AD per le VM Linux in Azure, è necessario assicurarsi che la configurazione di rete delle macchine virtuali consenta l'accesso in uscita agli endpoint seguenti sulla porta TCP 443:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https: \/ /Device.login.microsoftonline.com
* https: \/ /pas.Windows.NET
* https:\//management.azure.com
* https: \/ /packages.Microsoft.com

> [!NOTE]
> Attualmente non è possibile configurare i gruppi di sicurezza di rete di Azure per le macchine virtuali abilitate con l'autenticazione Azure AD.

## <a name="create-a-linux-virtual-machine"></a>Creare una macchina virtuale Linux

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create), quindi creare una macchina virtuale con [az vm create](/cli/azure/vm#az-vm-create) usando una distribuzione Linux supportata in un'area supportata. L'esempio seguente consente di distribuire una macchina virtuale denominata *myVM* che usa *Ubuntu 16.04 LTS* in un gruppo di risorse denominato *myResourceGroup* nell'area *southcentralus *. Negli esempi seguenti è possibile specificare i nomi del proprio gruppo di risorse e della macchina virtuale in base alle esigenze.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

La creazione della VM e delle risorse di supporto richiede alcuni minuti.

## <a name="install-the-azure-ad-login-vm-extension"></a>Installare l'estensione della macchina virtuale per l'accesso ad Azure AD

> [!NOTE]
> Se si distribuisce questa estensione a una macchina virtuale creata in precedenza, verificare che il computer disponga di almeno 1 GB di memoria allocata altrimenti l'estensione non verrà installata

Per accedere a una macchina virtuale Linux con Azure AD credenziali, installare l'estensione della macchina virtuale Azure Active Directory Login. Le estensioni della macchina virtuale sono piccole applicazioni che eseguono attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure. Usare [az vm extension set](/cli/azure/vm/extension#az-vm-extension-set) per installare l'estensione *AADLoginForLinux* nella macchina virtuale denominata *myVM* nel gruppo di risorse *myResourceGroup*:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Il *provisioningState* di *succeeded* viene visualizzato dopo che l'estensione è stata installata correttamente nella macchina virtuale. Per installare l'estensione, la macchina virtuale deve disporre di un agente di macchine virtuali in esecuzione. Per altre informazioni, vedere [Panoramica dell'agente di macchine virtuali](../extensions/agent-windows.md).

## <a name="configure-role-assignments-for-the-vm"></a>Configurare le assegnazioni di ruolo per la macchina virtuale

I criteri di controllo degli accessi in base al ruolo di Azure (RBAC di Azure) determinano chi può accedere alla macchina virtuale. Per autorizzare l'accesso alla macchina virtuale vengono usati due ruoli di Azure:

- **Accesso amministratore alle macchine virtuali**: gli utenti a cui è stato assegnato questo ruolo possono accedere a una macchina virtuale di Azure con i privilegi di utente ROOT di Windows o Linux.
- **Accesso utente alle macchine virtuali**: gli utenti a cui è stato assegnato questo ruolo possono accedere a una macchina virtuale di Azure con i privilegi di utente normale.

> [!NOTE]
> Per consentire all'utente di accedere alla macchina virtuale su SSH, è necessario assegnare il ruolo *Accesso amministratore alle macchine virtuali* oppure *Accesso utente alle macchine virtuali*. Un utente di Azure a cui è stato assegnato il ruolo *Proprietario* oppure *Collaboratore* per una macchina virtuale non dispone automaticamente dei privilegi per accedere alla macchina virtuale su SSH.

L'esempio seguente usa [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) per assegnare il ruolo *Accesso amministratore alle macchine virtuali* alla macchina virtuale per l'utente di Azure corrente. Il nome utente dell'account di Azure attivo si ottiene con [az account show](/cli/azure/account#az-account-show) e viene impostato l'*ambito* per la macchina virtuale creata in un passaggio precedente con [az vm show](/cli/azure/vm#az-vm-show). È anche possibile assegnare l'ambito a livello di gruppo di risorse o di sottoscrizione e applicare le normali autorizzazioni di ereditarietà del controllo degli accessi in base al ruolo. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Se il dominio di Azure Active Directory e il dominio del nome utente di accesso non corrispondono, è necessario specificare l'ID oggetto dell'account utente con *--id-oggetto-assegnatario* e non solo il nome utente per *--assegnatario*. È possibile ottenere l'ID oggetto per l'account utente con [az ad user list](/cli/azure/ad/user#az-ad-user-list).

Per altre informazioni su come usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure, vedere come usare l'[interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md), il [portale di Azure](../../role-based-access-control/role-assignments-portal.md) o [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

È anche possibile configurare Azure AD per richiedere l'autenticazione a più fattori per un utente specifico per eseguire l'accesso alla macchina virtuale Linux. Per altre informazioni, vedere [Introduzione ad Azure Multi-Factor Authentication nel cloud](../../active-directory/authentication/howto-mfa-getstarted.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Accedere alla macchina virtuale Linux

Visualizzare innanzitutto l'indirizzo IP pubblico della macchina virtuale con [az vm show](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Accedere alla macchina virtuale Linux di Azure usando le credenziali di Azure AD. Il parametro `-l` consente di specificare il proprio indirizzo dell'account di Azure AD. Sostituire l'account di esempio con il proprio. Gli indirizzi dell'account devono essere immessi in tutte lettere minuscole. Sostituire l'indirizzo IP di esempio con l'indirizzo IP pubblico della macchina virtuale con il comando precedente.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

Viene richiesto di accedere a Azure AD con un codice di utilizzo monouso all'indirizzo [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) . Copiare e incollare il codice use monouso nella pagina di accesso del dispositivo.

Quando richiesto, immettere le credenziali di accesso di Azure AD nella pagina di accesso. 

Il messaggio seguente viene visualizzato nel Web browser dopo che è stata eseguita l'autenticazione:`You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Chiudere la finestra del browser, tornare al prompt di SSH e premere il tasto **Invio**. 

È stato effettuato l'accesso alla macchina virtuale Linux di Azure con le autorizzazioni del ruolo assegnato, ad esempio come *utente della macchina virtuale* oppure *amministratore della macchina virtuale*. Se all'account utente viene assegnato il ruolo di *accesso amministratore macchina virtuale* , è possibile utilizzare `sudo` per eseguire comandi che richiedono privilegi radice.

## <a name="sudo-and-aad-login"></a>Accesso AAD e sudo

La prima volta che si esegue sudo, si dovrà eseguire l'autenticazione una seconda volta. Se non si desidera doversi autenticare nuovamente per eseguire sudo, è possibile modificare il file sudoers `/etc/sudoers.d/aad_admins` e sostituire questa riga:

```bash
%aad_admins ALL=(ALL) ALL
```

Con la riga seguente:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Risolvere i problemi di accesso

Alcuni errori comuni quando si tenta di eseguire SSH con Azure AD credenziali non includono i ruoli di Azure assegnati e vengono ripetute richieste di accesso. Usare le sezioni seguenti per risolvere questi problemi.

### <a name="access-denied-azure-role-not-assigned"></a>Accesso negato: ruolo di Azure non assegnato

Se viene visualizzato l'errore seguente nel prompt SSH, verificare di aver configurato i criteri di controllo degli accessi in base al ruolo per la macchina virtuale che concede all'utente il ruolo *Accesso amministratore alle macchine virtuali* o *Accesso utente alle macchine virtuali*:

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Prompt di accesso SSH continui

Dopo aver completato correttamente il passaggio di autenticazione in un Web browser, è possibile che venga subito richiesto di eseguire di nuovo l'accesso con un codice aggiornato. Questo errore è in genere causato da una mancata corrispondenza tra il nome di accesso specificato nel prompt SSH e l'account con cui è stato effettuato l'accesso ad Azure AD. Per correggere questo problema:

- Verificare che il nome di accesso specificato nel prompt SSH sia corretto. Un errore di battitura nel nome di accesso potrebbe causare una mancata corrispondenza tra il nome di accesso specificato nel prompt di SSH e l'account con cui è stato effettuato l'accesso ad Azure AD. Ad esempio, è stato digitato *azuresuer \@ contoso.onmicrosoft.com* anziché *azureuser \@ contoso.onmicrosoft.com*.
- Se si dispone di più account utente, assicurarsi che non si indichi un account utente diverso nella finestra del browser quando si accede ad Azure AD.
- Linux è un sistema operativo che distingue tra maiuscole e minuscole. Quindi "Azureuser@contoso.onmicrosoft.com" e "azureuser@contoso.onmicrosoft.com" sono diversi e questo può causare una mancata corrispondenza. Assicurarsi di specificare l'UPN usando correttamente le maiuscole e le minuscole nel prompt SSH.

### <a name="other-limitations"></a>Altre limitazioni

Gli utenti che ereditano i diritti di accesso tramite gruppi annidati o assegnazioni di ruolo non sono attualmente supportati. All'utente o al gruppo devono essere assegnate direttamente le [assegnazioni di ruolo obbligatorie](#configure-role-assignments-for-the-vm). Ad esempio, l'utilizzo di gruppi di gestione o assegnazioni di ruolo a gruppi annidati non concederà le autorizzazioni corrette per consentire all'utente di eseguire l'accesso.

## <a name="preview-feedback"></a>Feedback sull'anteprima

Condividere il proprio feedback su questa funzionalità di anteprima o segnalare eventuali problemi riscontrati durante l'utilizzo nel [forum di commenti e suggerimenti su Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Active Directory, vedere [Informazioni su Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
