---
title: Domande frequenti sulle macchine virtuali Windows in Azure | Microsoft Docs
description: Offre le risposte ad alcune delle domande comuni sulle macchine virtuali Windows create con un modello di Gestione risorse.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: d8f457569ef1e9dfe400266982596ab53ec4f10d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Domande frequenti sulle Macchine virtuali Windows
Questo articolo analizza alcune delle domande più comuni sulle macchine virtuali Windows create in Azure mediante il modello di distribuzione di Gestione risorse. Per la versione di Linux di questo argomento, vedere [Domande frequenti sulle Macchine virtuali Linux](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Cosa è possibile eseguire in una VM di Azure?
Tutti i sottoscrittori possono eseguire software del server in una macchina virtuale Azure. Per informazioni sui criteri di supporto per l'esecuzione del software server Microsoft in Azure, vedere [Supporto di software server Microsoft per le macchine virtuali Microsoft Azure](https://support.microsoft.com/kb/2721672)

Alcune versioni di Windows 7, Windows 8.1 e Windows 10 sono disponibili agli abbonati MSDN Azure con benefici e agli abbonati MSDN sviluppo e test con pagamento in base al consumo, per attività di sviluppo e test. Per ulteriori informazioni, incluse le istruzioni e limitazioni, vedere [Immagini Client Windows per gli abbonati MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanta memoria è possibile utilizzare con una macchina virtuale?
Ogni disco dati può arrivare fino a 4 TB (4.095 GB). Il numero di dischi dati che è possibile utilizzare dipende dalla dimensione della macchina virtuale. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Azure Managed Disks è l'offerta di archiviazione su disco consigliata per l'uso con Macchine virtuali di Azure per l'archiviazione permanente dei dati. Puoi usare più dischi gestiti con ogni macchina virtuale. Il servizio Managed Disks offre due tipi di opzioni di archiviazione durevole: Managed Disks Premium e Standard. Per informazioni sui prezzi, vedere [Prezzi di Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

Gli account di archiviazione di Azure offrono inoltre spazio di archiviazione per il disco del sistema operativo e per qualsiasi disco di dati. Ogni disco è un file con estensione vhd archiviato come BLOB di pagine. Per informazioni sui prezzi, vedere [Dettagli prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Come si accede alla macchina virtuale?
Stabilire una connessione remota mediante la Connessione desktop remoto (RDP) per una VM Windows. Per istruzioni, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Sono supportate al massimo due connessioni simultanee, a meno che il server non sia configurato come host sessione Servizi Desktop remoto.  

In caso di problemi con il Desktop remoto, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Se si ha familiarità con Hyper-V, è possibile che si stia cercando uno strumento simile a VMConnect. Azure non offre uno strumento simile, poiché l'accesso da console a una macchina virtuale non è supportato.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>È possibile utilizzare il disco temporaneo (l'unità D: per impostazione predefinita) per archiviare i dati?
Non usare il disco temporaneo per archiviare i dati. Si tratta solo di memorie temporanee, pertanto si rischierebbe di perdere dati che non possono essere recuperati. La perdita di dati può verificarsi quando si sposta la macchina virtuale in un host diverso. Il ridimensionamento di una macchina virtuale, l'aggiornamento dell'host o un errore hardware nell'host sono alcuni dei motivi che potrebbero portare a spostare una macchina virtuale.

Se si dispone di un'applicazione che deve utilizzare la lettera di unità D:, è possibile riassegnare le lettere di unità in modo che il disco temporaneo ne usi una diversa da D:. Per le istruzioni, vedere [Modificare la lettera di unità di un disco temporaneo di Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Come modificare la lettera di unità del disco temporaneo?
È possibile modificare la lettera di unità spostando il file di paging e riassegnando le lettere di unità, ma è necessario assicurarsi di che eseguire i passaggi in un ordine specifico. Per le istruzioni, vedere [Modificare la lettera di unità di un disco temporaneo di Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>È possibile aggiungere una VM esistente a un set di disponibilità?
di serie Se si desidera che la VM faccia parte di un set di disponibilità, è necessario creare la VM all'interno del set. Attualmente non è possibile aggiungere una VM a un set di disponibilità dopo la sua creazione.

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
Sì. Il nome del computer non può contenere più di 15 caratteri. Vedere [Naming conventions rules and restrictions](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Regole e restrizioni per le convenzioni di denominazione) per altre informazioni sulla denominazione delle risorse.

## <a name="are-there-any-resource-group-name-requirements"></a>Vi sono requisiti relativi al nome del gruppo di risorse?
Sì. Il nome del gruppo di risorse non può contenere più di 90 caratteri. Vedere [Naming conventions rules and restrictions](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Regole e restrizioni per le convenzioni di denominazione) per altre informazioni sui gruppi di risorse.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quali requisiti devono avere i nomi utente durante la creazione di una VM?

I nomi utente possono contenere un massimo di 20 caratteri e non possono terminare con un punto ("."). 


I nomi utente seguenti non sono consentiti:
<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> admin </td><td style="text-align:center"> user </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>    <tr>
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
Le password devono contenere da 12 a 123 caratteri e soddisfare 3 dei seguenti 4 requisiti di complessità:

* Devono contenere caratteri minuscoli
* Devono contenere caratteri maiuscoli
* Devono contenere almeno un numero
* Devono contenere un carattere speciale (REGEX.CONFRONTA [\W_])

Le password seguenti non sono consentite:

<table>
    <tr>
        <td>abc@123 </td>
        <td>P@$$w0rd </td>
        <td>P@ssw0rd </td>
        <td>P@ssword123 </td>
        <td>Pa$$word </td>
    </tr>
    <tr>
        <td>pass@word1 </td>
        <td>Password! </td>
        <td>Password1 </td>
        <td>Password22 </td>
        <td>iloveyou! </td>
    </tr>
</table>
