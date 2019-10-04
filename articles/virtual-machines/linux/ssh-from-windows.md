---
title: Usare le chiavi SSH con Windows per le VM Linux | Microsoft Docs
description: Informazioni su come generare e usare chiavi SSH in un computer Windows per connettersi a una macchina virtuale Linux in Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: e8e63f2c916153b5d43267869d7bc5be8fa646c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70081987"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Come usare le chiavi SSH con Windows in Azure

Questo articolo descrive i modi per generare e usare chiavi *Secure Shell* (SSH) in un computer Windows per creare e connettersi a una macchina virtuale (VM) Linux in Azure. Per usare le chiavi SSH da un client Linux o macOS, vedere il materiale sussidiario [rapido](mac-create-ssh-keys.md) o [dettagliato](create-ssh-keys-detailed.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Pacchetti Windows e client SSH
Per la connessione a VM Linux e la loro gestione in Azure si usa un client *SSH*. I computer Linux o macOS hanno di solito una suite di comandi SSH per generare e gestire le chiavi SSH e stabilire connessioni SSH. 

Nei computer Windows non sempre sono installati comandi SSH simili. Le versioni recenti di Windows 10 forniscono [comandi client OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) per creare e gestire le chiavi SSH e stabilire connessioni SSH da un prompt dei comandi. Le versioni recenti di Windows 10 includono anche il [sottosistema Windows per Linux](https://docs.microsoft.com/windows/wsl/about) per eseguire e accedere a utilità come un client SSH in modo nativo dall'interno della shell Bash. 

Altri comuni client SSH Windows che è possibile installare in locale sono inclusi nei pacchetti seguenti:

* [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git per Windows](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

È anche possibile usare le utilità SSH disponibili in Bash in [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Accedere a Cloud Shell nel Web browser all'indirizzo [https://shell.azure.com](https://shell.azure.com) o nel [portale di Azure](https://portal.azure.com). 
* Accedere a Cloud Shell come un terminale da Visual Studio Code installando l'[estensione Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH
Le sezioni seguenti descrivono due opzioni per creare una coppia di chiavi SSH in Windows. È possibile usare un comando della shell (`ssh-keygen`) o uno strumento con interfaccia utente grafica (PuTTYgen). Si noti anche che quando si usa PowerShell per creare una chiave, caricare la chiave pubblica come formato SSH. com (SECSH). Quando si usa l'interfaccia della riga di comando, convertire la chiave nel formato OpenSSH prima del caricamento. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>Creare chiavi SSH con ssh-keygen

Se si esegue una shell dei comandi in Windows che supporta gli strumenti client SSH (o si usa Azure Cloud Shell), creare una coppia di chiavi SSH usando il comando `ssh-keygen`. Digitare il comando seguente e rispondere ai prompt. Se nel percorso selezionato è presente una coppia di chiavi SSH, questi file vengono sovrascritti. 

```bash
ssh-keygen -t rsa -b 2048
```

Per altre informazioni, vedere la procedura [rapida](mac-create-ssh-keys.md) o [dettagliata](create-ssh-keys-detailed.md) per creare le chiavi SSH con `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Creare chiavi SSH con PuTTYgen

Se si preferisce usare uno strumento basato su interfaccia utente grafica per creare le chiavi SSH, è possibile usare il generatore di chiavi PuTTYgen, incluso nel [pacchetto di download di PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Per creare una coppia di chiavi SSH RSA con PuTTYgen:

1. Avviare PuTTYgen.

2. Fare clic su **Genera**. Per impostazione predefinita PuTTYgen genera una chiave SSH-2 RSA a 2048 bit.

4. Spostare il puntatore del mouse nell'area vuota per generare casualità per la chiave.

5. Dopo la generazione della chiave pubblica, è possibile facoltativamente immettere e confermare una passphrase. La passphrase sarà chiesta in fase di autenticazione per la macchina virtuale con la chiave SSH privata. Senza una passphrase, se un utente ottiene la chiave privata, potrà accedere a qualsiasi macchina virtuale o servizio che usa tale chiave. Creare una passphrase è altamente consigliato. Tuttavia, se si dimentica la passphrase, non è possibile recuperarla.

6. La chiave pubblica è visualizzata nella parte superiore della finestra. È possibile copiare l'intera chiave pubblica e quindi incollarla nel portale di Azure o in un modello di Azure Resource Manager quando si crea una macchina virtuale Linux. È inoltre possibile selezionare **Salva chiave pubblica** per salvare una copia nel computer:

    ![Salvare un file di chiave pubblica PuTTY](./media/ssh-from-windows/save-public-key.png)

7. Facoltativamente, per salvare la chiave privata nel formato chiave privata di PuTTy (file con estensione ppk), selezionare **Salva la chiave privata**. Il file con estensione ppk sarà necessario in un secondo momento per usare PuTTY per stabilire una connessione SSH alla macchina virtuale.

    ![Salvare il file della chiave privata PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Se si vuole salvare la chiave privata nel formato OpenSSH, il formato di chiave privata usato da molti client SSH, selezionare **Conversioni** > **Esporta chiave OpenSSH**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Fornire una chiave SSH pubblica quando si distribuisce una macchina virtuale

Per creare una VM Linux che usa chiavi SSH per l'autenticazione, fornire la chiave SSH pubblica quando si crea la VM tramite il portale di Azure o altri metodi.

L'esempio seguente illustra come copiare e incollare questa chiave pubblica nel portale di Azure quando si crea una VM Linux. La chiave pubblica in genere viene archiviata nella directory ~/.ssh/authorized_key nella nuova macchina virtuale.

   ![Usare la chiave pubblica quando si crea una VM nel portale di Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Connettersi alla macchina virtuale

Un modo per stabilire una connessione SSH alla VM Linux da Windows è usare un client SSH. Questo è il metodo preferito se un client SSH è installato nel sistema Windows o se si usano gli strumenti SSH in Bash in Azure Cloud Shell. Se si preferisce uno strumento basato su interfaccia utente grafica, è possibile stabilire la connessione con PuTTY.  

### <a name="use-an-ssh-client"></a>Usare un client SSH
Con la chiave pubblica distribuita nella VM di Azure e la chiave privata nel sistema locale, stabilire una connessione SSH alla VM usando l'indirizzo IP o il nome DNS della VM. Sostituire *azureuser* e *myvm.westus.cloudapp.azure.com* nel comando seguente con il nome utente dell'amministratore e il nome di dominio completo (o indirizzo IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se durante la creazione della coppia di chiavi è stata configurata una passphrase, immetterla quando viene richiesta durante la procedura di accesso.

Se la macchina virtuale usa i criteri di accesso JIT, è necessario richiedere l'accesso per connettersi alla macchina virtuale. Per altre informazioni sui criteri JIT, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>Connettersi con PuTTY

Se è stato installato il [pacchetto di download di PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) e in precedenza è stata generata una chiave privata PuTTY (file con estensione ppk), è possibile connettersi a una macchina virtuale Linux con PuTTY.

1. Avviare PuTTY.

2. Specificare il nome host o l'indirizzo IP della VM dal portale di Azure:

    ![Aprire una nuova connessione PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Selezionare la categoria **Connessione** > **SSH** > **Autorizzazione**. Individuare e selezionare la chiave privata PuTTY (file con estensione ppk):

    ![Selezionare la chiave privata PuTTY per l'autenticazione](./media/ssh-from-windows/putty-auth-dialog.png)

4. Fare clic su **Apri** per connettersi alla VM.

## <a name="next-steps"></a>Passaggi successivi

* Per la procedura dettagliata, le opzioni ed esempi avanzati sull'uso delle chiavi SSH, vedere la [procedura dettagliata per creare coppie di chiavi SSH](create-ssh-keys-detailed.md).

* Si può anche usare PowerShell in Azure Cloud Shell per generare le chiavi SSH e stabilire connessioni SSH alle VM Linux. Vedere le [informazioni di avvio rapido di PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Se si verificano problemi nell'uso di SSH per connettersi alle macchine virtuali Linux, vedere [Risoluzione dei problemi di connessione SSH a una macchina virtuale Linux di Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
