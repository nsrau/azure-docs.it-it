---
title: Risolvere i problemi di connessione SSH a una macchina virtuale di Azure | Documentazione Microsoft
description: Come risolvere i problemi, ad esempio una connessione SSH non riuscita o rifiutata per una macchina virtuale di Azure che esegue Linux.
keywords: connessione SSH rifiutata, errore SSH, SSH Azure, connessione SSH non riuscita
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 8df7eff135b6cb316562a503bb938b051d87562f
ms.lasthandoff: 03/21/2017


---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Risoluzione dei problemi di connessione SSH a una macchina virtuale Linux di Azure che ha esito negativo, genera errori o è stata rifiutata.
Sono vari i motivi per cui possono verificarsi errori Secure Shell (SSH), la connessione SSH non riesce o viene rifiutata durante il tentativo di connessione a una macchina virtuale Linux. Questo articolo consente di individuare i problemi e correggerli. È possibile usare il portale di Azure, l'interfaccia della riga di comando Azure o l'estensione dell'accesso alle VM per Linux per risolvere i problemi di connessione.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Per ricevere assistenza in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](http://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al sito del [supporto di Azure](http://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](http://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Passaggi rapidi per la risoluzione dei problemi
Dopo ogni passaggio della procedura di risoluzione dei problemi, tentare la riconnessione alla VM.

1. Reimpostare la configurazione SSH.
2. Reimpostare le credenziali per l'utente.
3. Verificare che le regole del [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) consentano il traffico SSH.
   * Verificare l'esistenza di una regola del gruppo di sicurezza di rete che consente il traffico SSH (per impostazione predefinita, la porta TCP 22).
   * Non è possibile usare il reindirizzamento o mapping delle porte senza usare Azure Load Balancer.
4. Controllare l'[integrità delle risorse della VM](../resource-health/resource-health-overview.md). 
   * Assicurarsi che la macchina virtuale venga segnalata come integra.
   * Se si dispone della diagnostica di avvio abilitata, verificare che la macchina virtuale non segnali errori di avvio nei log.
5. Riavviare la VM.
6. Distribuire di nuovo la VM.

Continuare la lettura per la procedura di risoluzione dei problemi e spiegazioni più dettagliate.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Metodi disponibili per risolvere i problemi di connessione SSH
È possibile reimpostare le credenziali o la configurazione SSH usando uno dei metodi seguenti:

* [Portale di Azure](#use-the-azure-portal): indicato per ripristinare rapidamente la configurazione SSH o la chiave SSH nel caso in cui non siano stati installati gli strumenti di Azure.
* [Interfaccia della riga di comando di Azure 2.0](#use-the-azure-cli-20): se ci si trova già nella riga di comando, reimpostare rapidamente le credenziali o la configurazione SSH. È possibile anche usare l'[interfaccia della riga di comando di Azure 1.0](#use-the-azure-cli-10)
* [Estensione VMAccessForLinux di Azure](#use-the-vmaccess-extension): creare e usare di nuovo i file di definizione JSON per reimpostare le credenziali utente o la configurazione di SSH.

Dopo ogni passaggio della procedura di risoluzione dei problemi, ritentare di connettersi alla VM. Se ancora non è possibile connettersi, procedere al passaggio successivo.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure
Il portale di Azure offre un modo rapido per reimpostare le credenziali utente o la configurazione SSH senza installare nessuno degli strumenti presenti nel computer locale.

Selezionare la macchina virtuale nel portale di Azure. Scorrere verso il basso la sezione **Supporto e risoluzione dei problemi** e selezionare **Reimposta password** come nell'esempio seguente:

![Reimpostare le credenziali o la configurazione SSH nel portale di Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Reimpostare la configurazione SSH
Come primo passaggio, selezionare `Reset SSH configuration only` dal menu a discesa **Modalità** come nella schermata precedente, quindi fare clic sul pulsante **Reimposta**. Dopo aver completato questa operazione, provare ad accedere nuovamente alla macchina virtuale.

### <a name="reset-ssh-credentials-for-a-user"></a>Reimpostare le credenziali SSH di un utente
Per reimpostare le credenziali di un utente esistente, selezionare `Reset SSH public key` o `Reset password` dal menu a discesa **Modalità** come nella schermata precedente. Specificare il nome utente e la chiave SSH o la nuova password, quindi fare clic sul pulsante **Reimposta**.

Da questo menu è possibile anche creare un utente con privilegi sudo nella macchina virtuale. Inserire il nuovo nome utente e la password o la chiave SSH associata, quindi fare clic sul pulsante **Reimposta**.

## <a name="use-the-azure-cli-20"></a>Usare l'interfaccia della riga di comando di Azure 2.0
Se non è già stato fatto, installare la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e accedere a un account di Azure tramite il comando [az login](/cli/azure/#login).

Se è stata creata e caricata un'immagine del disco Linux personalizzata, verificare che sia installato l'[agente Linux di Microsoft Azure](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) in versione 2.0.5 o successiva. Per le macchine virtuali create tramite le immagini della raccolta, questa estensione dell'accesso è già installata e configurata automaticamente.

### <a name="reset-ssh-credentials-for-a-user"></a>Reimpostare le credenziali SSH di un utente
L'esempio seguente usa il comando [az vm access set-linux-user](/cli/azure/vm/access#set-linux-user) per reimpostare le credenziali per `myUsername` sul valore specificato in `myPassword`, nella VM denominata `myVM` in `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
az vm access set-linux-user --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Se si usa l'autenticazione con chiave SSH, è possibile reimpostare la chiave SSH per un determinato utente. L'esempio seguente usa il comando **az vm access set-linux-user** per aggiornare la chiave SSH memorizzata in `~/.ssh/id_rsa.pub` per l'utente denominato `myUsername`, nella VM denominata `myVM` in `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
az vm access set-linux-user --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Usare l'estensione VMAccess
L'estensione di accesso alla macchina virtuale per Linux legge un file json che definisce le azioni da eseguire. Queste azioni includono la reimpostazione SSHD, la reimpostazione di una chiave SSH o l'aggiunta di un utente. È comunque possibile usare l'interfaccia della riga di comando di Azure per chiamare l'estensione VMAccess, ma è possibile anche usare di nuovo i file json tra più macchine virtuali, se desiderato. Questo approccio consente di creare un archivio di file json da chiamare per determinati scenari.

### <a name="reset-sshd"></a>Reimpostare un disco SSHD
Creare un file denominato `PrivateConf.json` con il contenuto seguente:

```json
{  
    "reset_ssh":"True"
}
```

Tramite l'interfaccia della riga di comando di Azure chiamare l'estensione `VMAccessForLinux` per reimpostare la connessione SSHD specificando il file json. L'esempio seguente reimposta SSHD su una macchina virtuale denominata `myVM` in `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reimpostare le credenziali SSH di un utente
Se il disco SSHD funziona correttamente, è possibile reimpostare le credenziali di un determinato utente. Per reimpostare la password per un utente, creare un file denominato `PrivateConf.json`. L'esempio seguente reimposta le credenziali per `myUsername` sul valore specificato in `myPassword`. Immettere le seguenti righe nel file `PrivateConf.json` con valori personalizzati:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

In alternativa, per reimpostare la chiave SSH per un utente, creare innanzitutto un file denominato `PrivateConf.json`. L'esempio seguente reimposta le credenziali per `myUsername` sul valore specificato in `myPassword` nella macchina virtuale denominata `myVM` in `myResourceGroup`. Immettere le seguenti righe nel file `PrivateConf.json` con valori personalizzati:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Dopo aver creato il file json, usare l'interfaccia della riga di comando di Azure per chiamare l'estensione `VMAccessForLinux` per reimpostare le credenziali dell'utente SSH specificando il file json. L'esempio seguente reimposta le credenziali sulla macchina virtuale denominata `myVM` in `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```

## <a name="use-the-azure-cli-10"></a>Usare l'interfaccia della riga di comando di Azure 1.0
Se necessario, [installare l'interfaccia della riga di comando di Azure 1.0 e connettersi alla sottoscrizione di Azure](../cli-install-nodejs.md). Verificare di usare la modalità di Resource Manager come indicato di seguito:

```azurecli
azure config mode arm
```

Se è stata creata e caricata un'immagine del disco Linux personalizzata, verificare che sia installato l'[agente Linux di Microsoft Azure](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) in versione 2.0.5 o successiva. Per le macchine virtuali create tramite le immagini della raccolta, questa estensione dell'accesso è già installata e configurata automaticamente.

### <a name="reset-ssh-configuration"></a>Reimpostare la configurazione SSH
La configurazione del disco SSHD in sé può essere errata oppure il servizio ha rilevato un errore. È possibile reimpostare il disco SSHD per controllare la validità della configurazione SSH. La reimpostazione SSHD deve essere il primo passaggio da eseguire per la risoluzione dei problemi.

L'esempio seguente reimposta SSHD su una macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`. Usare i nomi della macchina virtuale e del gruppo di risorse personalizzati come segue:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reimpostare le credenziali SSH di un utente
Se il disco SSHD funziona correttamente, è possibile reimpostare la password di un determinato utente. L'esempio seguente reimposta le credenziali per `myUsername` sul valore specificato in `myPassword` nella macchina virtuale denominata `myVM` in `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Se si usa l'autenticazione con chiave SSH, è possibile reimpostare la chiave SSH per un determinato utente. L'esempio seguente aggiorna la chiave SSH archiviata in `~/.ssh/id_rsa.pub` per l'utente denominato `myUsername` nella macchina virtuale denominata `myVM` in `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```


## <a name="restart-a-vm"></a>Riavviare una macchina virtuale
Se sono state reimpostate la configurazione SSH e le credenziali utente o si è verificato un errore durante queste operazioni, è possibile provare a riavviare la macchina virtuale per risolvere i problemi di calcolo alla base.

### <a name="azure-portal"></a>Portale di Azure
Per riavviare una macchina virtuale dal portale di Azure, selezionare la macchina virtuale e scegliere il pulsante **Riavvia** come nell'esempio seguente:

![Riavviare una macchina virtuale nel portale di Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli-10"></a>Interfaccia della riga di comando di Azure 1.0
L'esempio seguente riavvia la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
L'esempio seguente usa il comando [az vm restart](/cli/azure/vm#restart) per riavviare la macchina virtuale denominata `myVM` nel gruppo di risorse denominato `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Ridistribuire una VM
È possibile ridistribuire una VM in un altro nodo all'interno di Azure, correggendo eventuali problemi di rete sottostanti. Per informazioni su come eseguire questa operazione, vedere [Ridistribuzione della macchina virtuale su un nuovo nodo di Azure](virtual-machines-windows-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Al termine di questa operazione i dati temporanei del disco andranno persi e gli indirizzi IP dinamici associati alla macchina virtuale saranno aggiornati.
> 
> 

### <a name="azure-portal"></a>Portale di Azure
Per ridistribuire una macchina virtuale dal portale di Azure, selezionare la macchina virtuale e scorrere verso il basso fino alla sezione **Supporto e risoluzione dei problemi**. Fare clic sul pulsante **Ridistribuisci** come nell'esempio seguente:

![Ridistribuire una macchina virtuale nel portale di Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli-10"></a>Interfaccia della riga di comando di Azure 1.0
L'esempio seguente ridistribuisce la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
L'esempio seguente usa il comando [az vm redeploy](/cli/azure/vm#redeploy) per ridistribuire la macchina virtuale denominata `myVM` nel gruppo di risorse denominato `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>VM create con il modello di distribuzione classica
Per risolvere gli errori di connessione SSH più comuni nelle VM create con il modello di distribuzione classica, provare a eseguire questi passaggi. Dopo ogni passaggio, tentare la riconnessione alla VM.

* Reimpostare l'accesso remoto dal [portale di Azure](https://portal.azure.com). Nel portale di Azure, selezionare la macchina virtuale e fare clic sul pulsante **Reimposta accesso remoto**.
* Riavviare la VM. Nel [portale di Azure](https://portal.azure.com) selezionare la macchina virtuale e fare clic sul pulsante **Riavvia**.
  
    -OPPURE-
  
    Nel [portale di Azure classico](https://manage.windowsazure.com) selezionare **Macchine virtuali** > **Istanze** > **Riavvia**.
* Ridistribuire la VM su un nuovo nodo di Azure. Per informazioni su come eseguire questa operazione, vedere [Ridistribuzione della macchina virtuale su un nuovo nodo di Azure](virtual-machines-windows-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
    Al termine di questa operazione i dati temporanei del disco andranno persi e gli indirizzi IP dinamici associati alla macchina virtuale saranno aggiornati.
* Seguire le istruzioni in [Come reimpostare una password o SSH per le macchine virtuali basate su Linux](virtual-machines-linux-classic-reset-access.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) per:
  
  * Reimpostare la password o la chiave SSH.
  * Creare un account utente *sudo*.
  * Reimpostare la configurazione SSH.
* Controllare l'integrità delle risorse della VM per eventuali problemi di piattaforma.<br>
     Selezionare la macchina virtuale e scorrere verso il basso fino a **Impostazioni** > **Controlla integrità**.

## <a name="additional-resources"></a>Risorse aggiuntive
* Se non si riesce ancora a eseguire la configurazione SSH sulla macchina virtuale dopo aver eseguito la relativa procedura, usare i [passaggi dettagliati per la risoluzione dei problemi](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per esaminare altre procedure per la risoluzione del problema.
* Per altre informazioni sulla risoluzione dei problemi di accesso dell'applicazione, vedere [Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Per altre informazioni sulla risoluzione dei problemi di macchine virtuali create con il modello di distribuzione classica, vedere l'articolo su come [reimpostare una password o SSH per macchine virtuali basate su Linux](virtual-machines-linux-classic-reset-access.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).


