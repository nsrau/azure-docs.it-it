---
title: Risolvere i problemi di connessione SSH a una macchina virtuale di Azure | Documentazione Microsoft
description: Come risolvere i problemi, ad esempio una connessione SSH non riuscita o rifiutata per una macchina virtuale di Azure che esegue Linux.
keywords: connessione SSH rifiutata, errore SSH, SSH Azure, connessione SSH non riuscita
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: 190aab1f321aa9014eea95a63d525b394288b03b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709268"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Risoluzione dei problemi di connessione SSH a una macchina virtuale Linux di Azure che ha esito negativo, genera errori o è stata rifiutata.
Questo articolo illustra come trovare e correggere i problemi dovuti a errori Secure Shell (SSH), a errori di connessione SSH o al rifiuto della connessione SSH durante il tentativo di connessione a una macchina virtuale Linux. È possibile usare il portale di Azure, l'interfaccia della riga di comando Azure o l'estensione dell'accesso alle VM per Linux per risolvere i problemi di connessione.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Per ricevere assistenza in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Passaggi rapidi per la risoluzione dei problemi
Dopo ogni passaggio della procedura di risoluzione dei problemi, tentare la riconnessione alla VM.

1. [Reimpostare la configurazione SSH](#reset-config).
2. [Reimpostare le credenziali](#reset-credentials) per l'utente.
3. Verificare che le regole del [gruppo di sicurezza di rete](../../virtual-network/security-overview.md) consentano il traffico SSH.
   * Verificare l'esistenza di una [regola del gruppo di sicurezza di rete](#security-rules) che consenta il traffico SSH (per impostazione predefinita, la porta TCP 22).
   * Non è possibile usare il reindirizzamento o mapping delle porte senza usare Azure Load Balancer.
4. Controllare l'[integrità delle risorse della VM](../../resource-health/resource-health-overview.md).
   * Assicurarsi che la macchina virtuale venga segnalata come integra.
   * Se la [diagnostica di avvio è abilitata](boot-diagnostics.md), verificare che la macchina virtuale non segnali errori di avvio nei log.
5. [Riavviare la macchina virtuale](#restart-vm).
6. [Distribuire di nuovo la macchina virtuale](#redeploy-vm).

Continuare la lettura per la procedura di risoluzione dei problemi e spiegazioni più dettagliate.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Metodi disponibili per risolvere i problemi di connessione SSH
È possibile reimpostare le credenziali o la configurazione SSH usando uno dei metodi seguenti:

* [Portale di Azure](#use-the-azure-portal): indicato per ripristinare rapidamente la configurazione SSH o la chiave SSH nel caso in cui non siano stati installati gli strumenti di Azure.
* [Console seriale di macchina virtuale Azure](https://aka.ms/serialconsolelinux) -console seriale della macchina virtuale funzionerà indipendentemente dalla configurazione SSH e fornirà all'utente una console interattiva per la macchina virtuale. In effetti, "Impossibile SSH" situazioni sono specificamente qual è la console seriale progettato per aiutare a risolvere. Altri dettagli sotto.
* [Interfaccia della riga di comando di Azure](#use-the-azure-cli): se ci si trova già nella riga di comando, reimpostare rapidamente le credenziali o la configurazione SSH. Se si lavora con una macchina virtuale classica, è possibile usare l'[interfaccia della riga di comando classica di Azure](#use-the-azure-classic-cli).
* [Estensione VMAccessForLinux di Azure](#use-the-vmaccess-extension): creare e usare di nuovo i file di definizione JSON per reimpostare le credenziali utente o la configurazione di SSH.

Dopo ogni passaggio della procedura di risoluzione dei problemi, ritentare di connettersi alla VM. Se ancora non è possibile connettersi, procedere al passaggio successivo.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure
Il portale di Azure offre un modo rapido per reimpostare le credenziali utente o la configurazione SSH senza installare nessuno degli strumenti presenti nel computer locale.

Per iniziare, selezionare la macchina virtuale nel portale di Azure. Scorrere verso il basso la sezione **Supporto e risoluzione dei problemi** e selezionare **Reimposta password** come nell'esempio seguente:

![Reimpostare le credenziali o la configurazione SSH nel portale di Azure](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="a-idreset-config-reset-the-ssh-configuration"></a><a id="reset-config" />Reimpostare la configurazione SSH
Per reimpostare la configurazione SSH, selezionare `Reset configuration only` nella sezione **Modalità** come illustrato nello screenshot precedente e quindi selezionare **Aggiorna**. Dopo aver completato questa operazione, provare ad accedere nuovamente alla macchina virtuale.

### <a name="a-idreset-credentials-reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />Reimpostare le credenziali SSH di un utente
Per reimpostare le credenziali di un utente esistente, selezionare `Reset SSH public key` o `Reset password` nella sezione **Modalità** come illustrato nello screenshot precedente. Specificare il nome utente e la chiave SSH o la nuova password e quindi selezionare **Aggiorna**.

Da questo menu è possibile anche creare un utente con privilegi sudo nella macchina virtuale. Immettere il nuovo nome utente e la password o la chiave SSH associata e quindi selezionare **Aggiorna**.

### <a name="a-idsecurity-rules-check-security-rules"></a><a id="security-rules" />Controllare le regole di sicurezza

Usare la funzionalità [Verifica flusso IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) per verificare che una regola in un gruppo di sicurezza di rete blocchi il traffico da e verso una macchina virtuale. È anche possibile esaminare le regole del gruppo di sicurezza effettive per verificare che la regola del gruppo di sicurezza di rete che consente il traffico in ingresso sia presente e abbia la priorità per la porta SSH, ovvero la porta 22 predefinita. Per altre informazioni, vedere [Uso di regole di sicurezza effettive per risolvere i problemi di flusso del traffico delle macchine virtuali](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Controllare il routing

Usare la funzionalità [Hop successivo](../../network-watcher/network-watcher-check-next-hop-portal.md) di Network Watcher per verificare che una route non impedisca il routing del traffico da o verso una macchina virtuale. È anche possibile esaminare le route per determinare tutte quelle valide per un'interfaccia di rete. Per altre informazioni, vedere [Uso di route valide per risolvere i problemi relativi al flusso di traffico delle macchine virtuali](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Utilizzare la Console seriale di una VM di Azure
Il [Console seriale della macchina virtuale di Azure](./serial-console-linux.md) fornisce l'accesso a una console basata su testo per le macchine virtuali Linux. È possibile utilizzare la console per risolvere i problemi di connessione SSH in una shell interattiva. Verificare che siano soddisfatti i [prerequisiti](./serial-console-linux.md#prerequisites) per l'utilizzo di Console seriale e provare i comandi seguenti per continuare, risolvere i problemi la connettività SSH.

### <a name="check-that-ssh-is-running"></a>Verificare che SSH sia in esecuzione
È possibile usare il comando seguente per verificare se SSH è in esecuzione nella macchina virtuale:
```
$ ps -aux | grep ssh
```
Se è presente alcun output, SSH sia in esecuzione.

### <a name="check-which-port-ssh-is-running-on"></a>Verificare la porta SSH è in esecuzione in
È possibile usare il comando seguente per verificare la porta SSH è in esecuzione in:
```
$ sudo grep Port /etc/ssh/sshd_config
```
L'output apparirà simile:
```
Port 22
```

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure
Se non è già stato fatto, installare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-az-cli2) e accedere a un account di Azure tramite il comando [az login](/cli/azure/reference-index).

Se è stata creata e caricata un'immagine del disco Linux personalizzata, verificare che sia installato l'[agente Linux di Microsoft Azure](../extensions/agent-windows.md) in versione 2.0.5 o successiva. Per le macchine virtuali create tramite le immagini della raccolta, questa estensione dell'accesso è già installata e configurata automaticamente.

### <a name="reset-ssh-configuration"></a>Reimpostare la configurazione SSH
È possibile provare inizialmente a reimpostare la configurazione SSH ai valori predefiniti e riavviare quindi il server SSH nella macchina virtuale. Il nome dell'account utente, la password o le chiavi SSH non verranno modificate.
L'esempio seguente usa [az vm user reset-ssh](/cli/azure/vm/user) per reimpostare la configurazione SSH nella macchina virtuale denominata `myVM` in `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reimpostare le credenziali SSH di un utente
L'esempio seguente usa il comando [az vm user update](/cli/azure/vm/user) per reimpostare le credenziali per `myUsername` sul valore specificato in `myPassword`, nella macchina virtuale denominata `myVM` in `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Se si usa l'autenticazione con chiave SSH, è possibile reimpostare la chiave SSH per un determinato utente. L'esempio seguente usa il comando **az vm access set-linux-user** per aggiornare la chiave SSH memorizzata in `~/.ssh/id_rsa.pub` per l'utente denominato `myUsername`, nella VM denominata `myVM` in `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Usare l'estensione VMAccess
L'estensione di accesso alla macchina virtuale per Linux legge un file json che definisce le azioni da eseguire. Queste azioni includono la reimpostazione SSHD, la reimpostazione di una chiave SSH o l'aggiunta di un utente. È comunque possibile usare l'interfaccia della riga di comando di Azure per chiamare l'estensione VMAccess, ma è possibile anche usare di nuovo i file json tra più macchine virtuali, se desiderato. Questo approccio consente di creare un archivio di file json da chiamare per determinati scenari.

### <a name="reset-sshd"></a>Reimpostare un disco SSHD
Creare un file denominato `settings.json` con il contenuto seguente:

```json
{
    "reset_ssh":"True"
}
```

Tramite l'interfaccia della riga di comando di Azure chiamare l'estensione `VMAccessForLinux` per reimpostare la connessione SSHD specificando il file json. L'esempio seguente usa [az vm extension set](/cli/azure/vm/extension) per reimpostare SSHD nella macchina virtuale denominata `myVM` in `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reimpostare le credenziali SSH di un utente
Se il disco SSHD funziona correttamente, è possibile reimpostare le credenziali di un determinato utente. Per reimpostare la password per un utente, creare un file denominato `settings.json`. L'esempio seguente reimposta le credenziali per `myUsername` sul valore specificato in `myPassword`. Immettere le seguenti righe nel file `settings.json` con valori personalizzati:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

In alternativa, per reimpostare la chiave SSH per un utente, creare innanzitutto un file denominato `settings.json`. L'esempio seguente reimposta le credenziali per `myUsername` sul valore specificato in `myPassword` nella macchina virtuale denominata `myVM` in `myResourceGroup`. Immettere le seguenti righe nel file `settings.json` con valori personalizzati:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Dopo aver creato il file json, usare l'interfaccia della riga di comando di Azure per chiamare l'estensione `VMAccessForLinux` per reimpostare le credenziali dell'utente SSH specificando il file json. L'esempio seguente reimposta le credenziali sulla macchina virtuale denominata `myVM` in `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Usare l'interfaccia della riga di comando classica di Azure
Se necessario, [installare l'interfaccia della riga di comando classica di Azure e connettersi alla sottoscrizione di Azure](../../cli-install-nodejs.md). Verificare di usare la modalità di Resource Manager come indicato di seguito:

```azurecli
azure config mode arm
```

Se è stata creata e caricata un'immagine del disco Linux personalizzata, verificare che sia installato l'[agente Linux di Microsoft Azure](../extensions/agent-windows.md) in versione 2.0.5 o successiva. Per le macchine virtuali create tramite le immagini della raccolta, questa estensione dell'accesso è già installata e configurata automaticamente.

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

## <a name="a-idrestart-vm-restart-a-vm"></a><a id="restart-vm" />Riavviare una macchina virtuale
Se sono state reimpostate la configurazione SSH e le credenziali utente o si è verificato un errore durante queste operazioni, è possibile provare a riavviare la macchina virtuale per risolvere i problemi di calcolo alla base.

### <a name="azure-portal"></a>Portale di Azure
Per riavviare una macchina virtuale dal portale di Azure, selezionare la macchina virtuale e quindi selezionare **Riavvia** come nell'esempio seguente:

![Riavviare una macchina virtuale nel portale di Azure](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
L'esempio seguente usa il comando [az vm restart](/cli/azure/vm) per riavviare la macchina virtuale denominata `myVM` nel gruppo di risorse denominato `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Interfaccia della riga di comando classica di Azure
L'esempio seguente riavvia la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="a-idredeploy-vm-redeploy-a-vm"></a><a id="redeploy-vm" />Ridistribuire una macchina virtuale
È possibile ridistribuire una VM in un altro nodo all'interno di Azure, correggendo eventuali problemi di rete sottostanti. Per informazioni su come eseguire questa operazione, vedere [Ridistribuzione della macchina virtuale su un nuovo nodo di Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Al termine di questa operazione i dati temporanei del disco vanno persi e gli indirizzi IP dinamici associati alla macchina virtuale vengono aggiornati.
>
>

### <a name="azure-portal"></a>Portale di Azure
Per ridistribuire una macchina virtuale dal portale di Azure, selezionare la macchina virtuale e scorrere verso il basso fino alla sezione **Supporto e risoluzione dei problemi**. Fare clic su **Ridistribuisci**, come nell'esempio seguente:

![Ridistribuire una macchina virtuale nel portale di Azure](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
L'esempio seguente usa il comando [az vm redeploy](/cli/azure/vm) per ridistribuire la macchina virtuale denominata `myVM` nel gruppo di risorse denominato `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Interfaccia della riga di comando classica di Azure
L'esempio seguente ridistribuisce la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`. Usare i valori personalizzati come di seguito:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>VM create con il modello di distribuzione classica
Per risolvere gli errori di connessione SSH più comuni nelle VM create con il modello di distribuzione classica, provare a eseguire questi passaggi. Dopo ogni passaggio, tentare la riconnessione alla VM.

* Reimpostare l'accesso remoto dal [portale di Azure](https://portal.azure.com). Nel portale di Azure, selezionare la macchina virtuale e quindi selezionare **Reimposta accesso remoto...** .
* Riavviare la VM. Nel [portale di Azure](https://portal.azure.com) selezionare la macchina virtuale e selezionare **Riavvia**.

* Ridistribuire la VM su un nuovo nodo di Azure. Per informazioni su come eseguire questa operazione, vedere [Ridistribuzione della macchina virtuale su un nuovo nodo di Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

    Al termine di questa operazione i dati temporanei del disco andranno persi e gli indirizzi IP dinamici associati alla macchina virtuale saranno aggiornati.
* Seguire le istruzioni in [Come reimpostare una password o SSH per le macchine virtuali basate su Linux](../linux/classic/reset-access-classic.md) per:

  * Reimpostare la password o la chiave SSH.
  * Creare un account utente *sudo*.
  * Reimpostare la configurazione SSH.
* Controllare l'integrità delle risorse della VM per eventuali problemi di piattaforma.<br>
     Selezionare la macchina virtuale e scorrere verso il basso fino a **Impostazioni** > **Controlla integrità**.

## <a name="additional-resources"></a>Risorse aggiuntive
* Se non si riesce ancora a eseguire la configurazione SSH sulla macchina virtuale dopo aver eseguito la relativa procedura, usare i [passaggi dettagliati per la risoluzione dei problemi](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per esaminare altre procedure per la risoluzione del problema.
* Per altre informazioni sulla risoluzione dei problemi di accesso dell'applicazione, vedere [Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Per altre informazioni sulla risoluzione dei problemi di macchine virtuali create con il modello di distribuzione classica, vedere l'articolo su come [reimpostare una password o SSH per macchine virtuali basate su Linux](../linux/classic/reset-access-classic.md).
