---
title: Usare le chiavi SSH per connettersi alle macchine virtuali Linux
description: Informazioni su come generare e usare chiavi SSH da un computer Windows per connettersi a una macchina virtuale Linux in Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.date: 07/09/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: dcb5277773be1fc45a3d2b0901cf9fda177b7054
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512653"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Come usare le chiavi SSH con Windows in Azure

Questo articolo è per gli utenti di Windows che vogliono [creare](#create-an-ssh-key-pair) e usare chiavi SSH ( *Secure Shell* ) per [connettersi](#connect-to-your-vm) a macchine virtuali (VM) Linux in Azure. È anche possibile [generare e archiviare le chiavi SSH nel portale di Azure](../ssh-keys-portal.md) da usare durante la creazione di macchine virtuali nel portale.


Per usare le chiavi SSH da un client Linux o macOS, vedere la [Guida rapida](mac-create-ssh-keys.md). Per una panoramica più dettagliata di SSH, vedere [procedura dettagliata: creare e gestire le chiavi SSH per l'autenticazione in una VM Linux in Azure](create-ssh-keys-detailed.md).

## <a name="overview-of-ssh-and-keys"></a>Panoramica di SSH e delle chiavi

[SSH](https://www.ssh.com/ssh/) è un protocollo di connessione crittografato che consente l'accesso sicuro su connessioni non protette. SSH è il protocollo di connessione predefinito per le macchine virtuali Linux ospitate in Azure. Sebbene SSH fornisca una connessione crittografata, l'uso di password con SSH lascia ancora la VM vulnerabile agli attacchi di forza bruta. Si consiglia di connettersi a una macchina virtuale tramite SSH usando una coppia di chiavi pubblica/privata, nota anche come *chiavi SSH*. 

La coppia di chiavi pubblica-privata è analoga al blocco sulla porta anteriore. Il blocco viene esposto al **pubblico**, chiunque disponga della chiave destra può aprire lo sportello. La chiave è **privata**e viene assegnata solo a persone attendibili perché può essere usata per sbloccare lo sportello. 

- La *chiave pubblica* viene posizionata nella VM Linux quando si crea la macchina virtuale. 

- La *chiave privata* rimane nel sistema locale. Sulla chiave privata è necessario mantenere la massima riservatezza, evitando di condividerla.

Quando ci si connette alla VM Linux, la macchina virtuale testa il client SSH per assicurarsi che disponga della chiave privata corretta. In caso positivo, al client viene concesso il diritto di accesso alla macchina virtuale. 

A seconda dei criteri di sicurezza dell'organizzazione, è possibile riutilizzare una singola coppia di chiavi per accedere a più macchine virtuali e servizi di Azure. Non è necessaria una coppia di chiavi separata per ogni macchina virtuale. 

La chiave pubblica può essere condivisa con altri utenti, ma solo l'utente o l'infrastruttura di sicurezza locale deve avere accesso alla chiave privata.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>Client SSH

Le versioni recenti di Windows 10 includono [comandi client OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) per creare e usare chiavi SSH e stabilire connessioni SSH da PowerShell o da un prompt dei comandi. Questo è il modo più semplice per creare una connessione SSH alla VM Linux da un computer Windows. 

È anche possibile usare bash nella [Azure cloud Shell](../../cloud-shell/overview.md) per connettersi alla macchina virtuale. È possibile usare Cloud Shell in un [Web browser](https://shell.azure.com/bash), dal [portale di Azure](https://portal.azure.com)o come terminale in Visual Studio Code usando l' [estensione dell'account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

È anche possibile installare il [sottosistema Windows per Linux](https://docs.microsoft.com/windows/wsl/about) per connettersi alla macchina virtuale tramite SSH e usare altri strumenti Linux nativi in una shell bash.

## <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH

Creare una coppia di chiavi SSH usando il `ssh-keygen` comando. Immettere un nome file o usare il valore predefinito visualizzato tra parentesi (ad esempio `C:\Users\username/.ssh/id_rsa` ).  Immettere una passphrase per il file o lasciare vuota la passphrase se non si vuole usare una passphrase. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Creare una VM usando la chiave

Per creare una VM Linux che usa chiavi SSH per l'autenticazione, fornire la chiave SSH pubblica quando si crea la macchina virtuale.

Usando l'interfaccia della riga di comando di Azure, specificare il percorso e il nome file per la chiave pubblica usando `az vm create` e il `--ssh-key-value` parametro.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

Con PowerShell usare `New-AzVM` e aggiungere la chiave SSH alla configurazione della macchina virtuale utilizzando '. Per un esempio, vedere [Guida introduttiva: creare una macchina virtuale Linux in Azure con PowerShell](quick-create-powershell.md).

Se si eseguono numerose distribuzioni tramite il portale, potrebbe essere necessario caricare la chiave pubblica in Azure, dove può essere facilmente selezionata quando si crea una macchina virtuale dal portale. Per altre informazioni, vedere [caricare una chiave SSH](../ssh-keys-portal.md#upload-an-ssh-key).


## <a name="connect-to-your-vm"></a>Connettersi alla macchina virtuale

Con la chiave pubblica distribuita nella VM di Azure e la chiave privata nel sistema locale, stabilire una connessione SSH alla VM usando l'indirizzo IP o il nome DNS della VM. Sostituire *azureuser* e *10.111.12.123* nel comando seguente con il nome utente dell'amministratore, l'indirizzo IP (o il nome di dominio completo) e il percorso della chiave privata:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Se è stata configurata una passphrase durante la creazione della coppia di chiavi, immettere la passphrase quando viene richiesta.

Se la macchina virtuale usa i criteri di accesso JIT, è necessario richiedere l'accesso per connettersi alla macchina virtuale. Per altre informazioni sui criteri JIT, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)](../../security-center/security-center-just-in-time.md).


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle chiavi SSH nella portale di Azure, vedere [generare e archiviare chiavi SSH nell'portale di Azure](../ssh-keys-portal.md) da usare durante la creazione di macchine virtuali nel portale.

- Per i passaggi dettagliati, le opzioni e gli esempi avanzati di utilizzo delle chiavi SSH, vedere la [procedura dettagliata per creare coppie di chiavi SSH](create-ssh-keys-detailed.md).

- Si può anche usare PowerShell in Azure Cloud Shell per generare le chiavi SSH e stabilire connessioni SSH alle VM Linux. Vedere le [informazioni di avvio rapido di PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

- Se si verificano problemi nell'uso di SSH per connettersi alle macchine virtuali Linux, vedere [Risoluzione dei problemi di connessione SSH a una macchina virtuale Linux di Azure](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).
