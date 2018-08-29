---
title: Usare le chiavi SSH con Windows per le VM Linux | Microsoft Docs
description: Informazioni su come generare e usare chiavi SSH in un computer Windows per connettersi a una macchina virtuale Linux in Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: danlep
ms.openlocfilehash: fc245d38af90e0c395389e24b14d061fcfe0c10c
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2018
ms.locfileid: "42143499"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Come usare le chiavi SSH con Windows in Azure

Questo articolo descrive i modi per generare e usare chiavi Secure Shell (SSH) in un computer Windows per creare e connettersi a una macchina virtuale (VM) Linux in Azure. Per usare le chiavi SSH da un client Linux o macOS, vedere il materiale sussidiario [rapido](mac-create-ssh-keys.md) o [dettagliato](create-ssh-keys-detailed.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Pacchetti Windows e client SSH
Per la connessione a VM Linux e la loro gestione in Azure si usa un client *SSH*. I computer Linux o macOS hanno di solito una suite di comandi SSH per generare e gestire le chiavi SSH e stabilire connessioni SSH. 

Nei computer Windows non sempre sono installati comandi SSH simili. Le versioni recenti di Windows 10 forniscono [comandi client OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) per creare e gestire le chiavi SSH e stabilire connessioni SSH da un prompt dei comandi. Le versioni recenti di Windows 10 includono anche il [sottosistema Windows per Linux](https://docs.microsoft.com/windows/wsl/about) per eseguire e accedere a utilità come un client SSH in modo nativo dall'interno della shell Bash. 

Se si vuole usare un altro strumento SSH per Windows, comuni client SSH Windows che è possibile installare localmente sono disponibili nei pacchetti seguenti:

* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git per Windows](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Un'altra possibilità è usare le utilità SSH disponibili in Bash in [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Accedere a Cloud Shell nel Web browser all'indirizzo [https://shell.azure.com](https://shell.azure.com) o nel [portale di Azure](https://portal.azure.com). 
* Accedere a Cloud Shell come un terminale da Visual Studio Code installando l'[estensione Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH
Questa sezione illustra due opzioni per creare una coppia di chiavi SSH in Windows.

### <a name="create-ssh-keys-with-ssh-keygen"></a>Creare chiavi SSH con ssh-keygen

Se si esegue una shell dei comandi in Windows che supporta gli strumenti client SSH (o si usa Azure Cloud Shell), creare una coppia di chiavi SSH usando il comando `ssh-keygen`. Digitare il comando seguente e rispondere ai prompt. Se nel percorso corrente è presente una coppia di chiavi SSH, questi file vengono sovrascritti. 

```bash
ssh-keygen -t rsa -b 2048
```

Per e altre informazioni, vedere le procedure [rapida](mac-create-ssh-keys.md) o [dettagliata](create-ssh-keys-detailed.md) per creare le chiavi con `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Creare chiavi SSH con PuTTYgen

Se si preferisce usare uno strumento basato su interfaccia utente grafica per creare le chiavi SSH, è possibile usare il generatore di chiavi PuTTYgen, incluso nel [pacchetto di download di PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Per creare una coppia di chiavi SSH RSA con PuTTYgen:

1. Avviare PuTTYgen.

2. Fare clic su **Genera**. Per impostazione predefinita PuTTYgen genera una chiave SSH-2 RSA a 2048 bit.

4. Posizionare il cursore del mouse sull'area vuota per generare casualità per la chiave.

5. Dopo la generazione della chiave pubblica, è possibile facoltativamente immettere e confermare una passphrase. La passphrase sarà chiesta in fase di autenticazione per la VM con la chiave SSH. Quando non si imposta una passphrase, se un utente ottiene la chiave privata potrà accedere a qualsiasi VM o servizio che usa tale chiave. Creare una passphrase è altamente consigliato. Tuttavia, se si dimentica la passphrase, non è possibile recuperarla.

6. La chiave pubblica è visualizzata nella parte superiore della finestra. È possibile copiare e incollare questa chiave pubblica di una sola riga nel portale di Azure o in un modello di Azure Resource Manager quando si crea una VM Linux. È inoltre possibile fare clic su **Salva chiave pubblica** per salvare una copia nel computer:

    ![Salvare un file di chiave pubblica PuTTY](./media/ssh-from-windows/save-public-key.png)

7. Facoltativamente, per salvare la chiave privata nel formato chiave privata di PuTTy (file con estensione ppk), fare clic su **Salva la chiave privata**. Il file con estensione ppk è necessario se si vuole usare PuTTY in un secondo momento per stabilire una connessione SSH alla VM.

    ![Salvare il file della chiave privata PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Se si vuole salvare la chiave privata nel formato OpenSSH, il formato di chiave privata utilizzato da molti client SSH, fare clic su **Conversioni** > **Esporta chiave OpenSSH**.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Fornire la chiave SSH pubblica quando si distribuisce una VM

Per creare una VM Linux che usa chiavi SSH per l'autenticazione, fornire la chiave SSH pubblica quando si crea la VM tramite il portale di Azure o altri metodi.

L'esempio seguente illustra come copiare e incollare questa chiave pubblica nel portale di Azure quando si crea una VM Linux. Quindi la chiave pubblica viene generalmente archiviata in `~/.ssh/authorized_keys` nella nuova VM.

   ![Usare la chiave pubblica quando si crea una VM nel portale di Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Connettersi alla macchina virtuale

Un modo per stabilire una connessione SSH alla VM Linux da Windows è usare un client SSH. Questo è il metodo preferito se un client SSH è installato nel sistema Windows o se si usano strumenti SSH in Bash in Azure Cloud Shell. Se si preferisce uno strumento basato su interfaccia utente grafica, è possibile stabilire la connessione con PuTTY.  

### <a name="use-an-ssh-client"></a>Usare un client SSH
Con la chiave pubblica distribuita nella VM di Azure e la chiave privata nel sistema locale, stabilire una connessione SSH alla VM usando l'indirizzo IP o il nome DNS della VM. Sostituire *azureuser* e *myvm.westus.cloudapp.azure.com* nel comando seguente con il nome utente dell'amministratore e il nome di dominio completo (o indirizzo IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se è stata configurata una passphrase durante la creazione della coppia di chiavi, immettere la passphrase quando viene richiesta durante la procedura di accesso.

### <a name="connect-with-putty"></a>Connettersi con PuTTY

Se è stato installato il [pacchetto di download di PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) e in precedenza è stata generata una chiave privata PuTTY (file con estensione ppk), è possibile connettersi alla VM Linux con PuTTY.

1. Avviare PuTTY.

2. Specificare il nome host o l'indirizzo IP della VM dal portale di Azure:

    ![Aprire una nuova connessione PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Prima di selezionare **Apri**, fare clic sulla scheda **Connessione** > **SSH** > **Aut**. Individuare e selezionare la chiave privata PuTTY (file con estensione ppk):

    ![Selezionare la chiave privata PuTTY per l'autenticazione](./media/ssh-from-windows/putty-auth-dialog.png)

4. Fare clic su **Apri** per connettersi alla VM.

## <a name="next-steps"></a>Passaggi successivi

* Per la procedura dettagliata, le opzioni ed esempi avanzati sull'uso delle chiavi SSH, vedere la [procedura dettagliata per creare coppie di chiavi SSH](create-ssh-keys-detailed.md).

* Si può anche usare PowerShell in Azure Cloud Shell per generare le chiavi SSH e stabilire connessioni SSH alle VM Linux. Vedere le [informazioni di avvio rapido di PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Se si verificano problemi nell'uso di SSH per connettersi alle VM Linux, vedere [Risoluzione dei problemi di connessione SSH a una VM Linux di Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
