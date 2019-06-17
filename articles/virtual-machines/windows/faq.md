---
title: Domande frequenti sulle macchine virtuali Windows in Azure | Microsoft Docs
description: Offre le risposte ad alcune delle domande comuni sulle macchine virtuali Windows create con un modello di Gestione risorse.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 61f24b3c13a53b23538327cd1458a54756b7caa5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65466359"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Domande frequenti sulle Macchine virtuali Windows
Questo articolo analizza alcune delle domande più comuni sulle macchine virtuali Windows create in Azure mediante il modello di distribuzione di Gestione risorse. Per la versione Linux di questo argomento, vedere [domande frequenti sulle macchine virtuali Linux](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="what-can-i-run-on-an-azure-vm"></a>Cosa è possibile eseguire in una VM di Azure?
Tutti i sottoscrittori possono eseguire software del server in una macchina virtuale Azure. Per informazioni sui criteri di supporto per il software server Microsoft in esecuzione in Azure, vedere [supporto di software server Microsoft per le macchine virtuali di Azure](https://support.microsoft.com/kb/2721672).

Alcune versioni di Windows 7, Windows 8.1 e Windows 10 sono disponibili agli abbonati MSDN Azure con benefici e agli abbonati MSDN sviluppo e test con pagamento in base al consumo, per attività di sviluppo e test. Per ulteriori informazioni, incluse le istruzioni e limitazioni, vedere [Immagini Client Windows per gli abbonati MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanta memoria è possibile utilizzare con una macchina virtuale?
Ogni disco dati può arrivare fino a 4 TB (4.095 GB). Il numero di dischi dati che è possibile utilizzare dipende dalla dimensione della macchina virtuale. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Azure Managed Disks è l'offerta di archiviazione su disco consigliata per l'uso con Macchine virtuali di Azure per l'archiviazione permanente dei dati. Puoi usare più dischi gestiti con ogni macchina virtuale. Il servizio Managed Disks offre due tipi di opzioni di archiviazione durevole: Managed Disks Premium e Standard. Per informazioni sui prezzi, vedere [Prezzi di Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

Gli account di archiviazione di Azure offrono inoltre spazio di archiviazione per il disco del sistema operativo e per qualsiasi disco di dati. Ogni disco è un file con estensione vhd archiviato come BLOB di pagine. Per informazioni sui prezzi, vedere [Dettagli prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Come si accede alla macchina virtuale?
Stabilire una connessione remota mediante la Connessione desktop remoto (RDP) per una VM Windows. Per istruzioni, vedere [come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Sono supportate al massimo due connessioni simultanee, a meno che il server non sia configurato come host sessione Servizi Desktop remoto.  

In caso di problemi con il Desktop remoto, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Se si ha familiarità con Hyper-V, è possibile che si stia cercando uno strumento simile a VMConnect. Azure non offre uno strumento simile, poiché l'accesso da console a una macchina virtuale non è supportato.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>È possibile utilizzare il disco temporaneo (l'unità D: per impostazione predefinita) per archiviare i dati?
Non usare il disco temporaneo per archiviare i dati. Si tratta solo di memorie temporanee, pertanto si rischierebbe di perdere dati che non possono essere recuperati. La perdita di dati può verificarsi quando si sposta la macchina virtuale in un host diverso. Il ridimensionamento di una macchina virtuale, l'aggiornamento dell'host o un errore hardware nell'host sono alcuni dei motivi che potrebbero portare a spostare una macchina virtuale.

Se si dispone di un'applicazione che deve utilizzare la lettera di unità D:, è possibile riassegnare le lettere di unità in modo che il disco temporaneo ne usi una diversa da D:. Per le istruzioni, vedere [Modificare la lettera di unità di un disco temporaneo di Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Come modificare la lettera di unità del disco temporaneo?
È possibile modificare la lettera di unità spostando il file di paging e riassegnando le lettere di unità, ma è necessario assicurarsi di che eseguire i passaggi in un ordine specifico. Per le istruzioni, vedere [Modificare la lettera di unità di un disco temporaneo di Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>È possibile aggiungere una VM esistente a un set di disponibilità?
No. Se si desidera che la VM faccia parte di un set di disponibilità, è necessario creare la VM all'interno del set. Attualmente non è possibile aggiungere una VM a un set di disponibilità dopo la sua creazione.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>È possibile caricare una macchina virtuale in Azure?
Sì. Per istruzioni, vedere [Migrazione di macchine virtuali locali in Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>È possibile ridimensionare il disco del sistema operativo?
Sì. Per istruzioni, vedere [Come espandere l'unità del sistema operativo di una macchina virtuale in un gruppo di risorse di Azure](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>È possibile copiare o clonare una VM di Azure esistente?
Sì. Usando immagini gestite, è possibile creare un'immagine di una macchina virtuale e quindi usare l'immagine per creare diverse nuove macchine virtuali. Per istruzioni, vedere [Creare un'immagine personalizzata di una macchina virtuale](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Perché non si vedono le aree del Canada centrale e del Canada orientale tramite Azure Resource Manager?

Le due nuove aree del Canada centrale e del Canada orientale non vengono registrate automaticamente per la creazione della macchina virtuale per le sottoscrizioni di Azure esistenti. La registrazione viene eseguita automaticamente quando si distribuisce una macchina virtuale tramite il portale di Azure in qualsiasi altra area di Azure Resource Manager. Dopo aver distribuito una macchina virtuale in qualsiasi altra area di Azure le nuove aree dovrebbero essere disponibili per le macchine virtuali successive.

## <a name="does-azure-support-linux-vms"></a>Le VM di Linux sono supportate da Azure?
Sì. Per creare rapidamente una VM Linux di prova, vedere [Creare una VM Linux in Azure usando il portale](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>È possibile aggiungere un NIC alla VM dopo la sua creazione?
Sì, ora è possibile. La macchina virtuale deve prima essere arrestata e deallocata. È possibile a questo punto aggiungere o rimuovere una scheda di interfaccia di rete (a meno che non sia l'ultima nella macchina virtuale). 

## <a name="are-there-any-computer-name-requirements"></a>Esistono requisiti relativi al nome del computer?
Sì. Il nome del computer non può contenere più di 15 caratteri. Vedere [Naming conventions rules and restrictions](/azure/architecture/best-practices/naming-conventions#compute) (Regole e restrizioni per le convenzioni di denominazione) per altre informazioni sulla denominazione delle risorse.

## <a name="are-there-any-resource-group-name-requirements"></a>Vi sono requisiti relativi al nome del gruppo di risorse?
Sì. Il nome del gruppo di risorse non può contenere più di 90 caratteri. Vedere [Naming conventions rules and restrictions](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) (Regole e restrizioni per le convenzioni di denominazione) per altre informazioni sui gruppi di risorse.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quali requisiti devono avere i nomi utente durante la creazione di una VM?

I nomi utente possono contenere un massimo di 20 caratteri e non possono terminare con un punto ("."). 

I nomi utente seguenti non sono consentiti:

| | | | |
|-----------------|-----------|--------------------|----------|
| `administrator` | `admin`   | `user`             | `user1`  |
| `test`          | `user2`   | `test1`            | `user3`  |
| `admin1`        | `1`       | `123`              | `a`      |
| `actuser`       | `adm`     | `admin2`           | `aspnet` |
| `backup`        | `console` | `david`            | `guest`  |
| `john`          | `owner`   | `root`             | `server` |
| `sql`           | `support` | `support_388945a0` | `sys`    |
| `test2`         | `test3`   | `user4`            | `user5`  |


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quali requisiti devono avere le password durante la creazione di una VM?

Esistono diverse password i requisiti di lunghezza, a seconda dello strumento in uso:
 - Portale - compresa tra 12 e 72 caratteri
 - PowerShell - compresa tra 8 e 123 caratteri
 - CLI - tra 12 e 123

* Devono contenere caratteri minuscoli
* Devono contenere caratteri maiuscoli
* Devono contenere almeno un numero
* Devono contenere un carattere speciale (REGEX.CONFRONTA [\W_])

Le password seguenti non sono consentite:

<table>
    <tr>
        <td>abc@123</td>
        <td>iloveyou!</td>
        <td>P@$$w0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$$word</td>
        <td>pass@word1</td>
        <td>Password!</td>
        <td>Password1</td>
        <td>Password22</td>
    </tr>
</table>
