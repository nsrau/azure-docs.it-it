---
title: Domande frequenti sulle macchine virtuali Linux in Azure | Microsoft Docs
description: Fornisce le risposte ad alcune delle domande comuni sulle macchine virtuali Linux create con un modello di Resource Manager.
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cynthn
ms.openlocfilehash: 0e06d21bd0b6ef807f38e41dcd50c9cd715607a3
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Domande frequenti sulle macchine virtuali Linux
Questo articolo analizza alcune delle domande più comuni sulle macchine virtuali Linux create in Azure mediante il modello di distribuzione Resource Manager. Per la versione di Windows di questo argomento, vedere [Domande frequenti sulle macchine virtuali Windows](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Cosa è possibile eseguire in una VM di Azure?
Tutti i sottoscrittori possono eseguire software del server in una macchina virtuale Azure. Per altre informazioni, vedere [Distribuzioni di Linux supportate da Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanta memoria è possibile utilizzare con una macchina virtuale?
Ogni disco dati può essere fino a 1 TB. Il numero di dischi dati che è possibile utilizzare dipende dalla dimensione della macchina virtuale. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Un account di archiviazione Azure fornisce memoria per il disco del sistema operativo e per qualsiasi disco dati. Ogni disco è un file con estensione vhd archiviato come BLOB di pagine. Per informazioni sui prezzi, vedere [Dettagli prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Come si accede alla macchina virtuale?
Stabilire una connessione remota per accedere alla macchina virtuale tramite Secure Shell (SSH). Vedere le istruzioni su come connettersi [da Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [da Linux e Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Per impostazione predefinita, la SSH consente un massimo di 10 connessioni simultanee. È possibile aumentare questo numero modificando il file di configurazione.

Se si verificano problemi, vedere l'argomento [Risolvere i problemi relativi alle connessioni Secure Shell (SSH)](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>È possibile usare il disco temporaneo (/dev/sdb1) per archiviare i dati?
Non usare il disco temporaneo (/dev/sdb1) per archiviare i dati. Serve solo per l'archiviazione temporanea. I dati non ripristinabili rischiano di andare persi.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>È possibile copiare o clonare una VM di Azure esistente?
Sì. Per le istruzioni, vedere [Creare una copia di una macchina virtuale Linux nel modello di distribuzione di Resource Manager](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Perché non si vedono le aree del Canada centrale e del Canada orientale tramite Azure Resource Manager?
Le due nuove aree del Canada centrale e del Canada orientale non vengono registrate automaticamente per la creazione della macchina virtuale per le sottoscrizioni di Azure esistenti. La registrazione viene eseguita automaticamente quando si distribuisce una macchina virtuale tramite il portale di Azure in qualsiasi altra area di Azure Resource Manager. Dopo aver distribuito una macchina virtuale in qualsiasi altra area di Azure le nuove aree dovrebbero essere disponibili per le macchine virtuali successive.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>È possibile aggiungere un NIC alla VM dopo la sua creazione?
Sì, ora è possibile. La macchina virtuale deve prima essere arrestata e deallocata. È possibile a questo punto aggiungere o rimuovere una scheda di interfaccia di rete (a meno che non sia l'ultima nella macchina virtuale). 

## <a name="are-there-any-computer-name-requirements"></a>Esistono requisiti relativi al nome del computer?
Sì. Il nome del computer non può contenere più di 64 caratteri. Vedere [Naming conventions rules and restrictions](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Regole e restrizioni per le convenzioni di denominazione) per altre informazioni sulla denominazione delle risorse.

## <a name="are-there-any-resource-group-name-requirements"></a>Vi sono requisiti relativi al nome del gruppo di risorse?
Sì. Il nome del gruppo di risorse non può contenere più di 90 caratteri. Vedere [Naming conventions rules and restrictions](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Regole e restrizioni per le convenzioni di denominazione) per altre informazioni sui gruppi di risorse.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quali requisiti devono avere i nomi utente durante la creazione di una VM?
I nomi utente devono contenere da 1 a 64 caratteri.

I nomi utente seguenti non sono consentiti:

<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> admin </td><td style="text-align:center"> user </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">backup </td><td style="text-align:center"> console </td><td style="text-align:center"> david </td><td style="text-align:center"> guest</td>
    </tr>
    <tr>
        <td style="text-align:center">john </td><td style="text-align:center"> proprietario </td><td style="text-align:center"> root </td><td style="text-align:center"> server</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> support </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quali requisiti devono avere le password durante la creazione di una VM?
Le password devono contenere da 6 a 72 caratteri e soddisfare 3 dei seguenti 4 requisiti di complessità:

* Devono contenere caratteri minuscoli
* Devono contenere caratteri maiuscoli
* Devono contenere almeno un numero
* Devono contenere un carattere speciale (REGEX.CONFRONTA [\W_])

Le password seguenti non sono consentite:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Password!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>
