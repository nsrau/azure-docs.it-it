---
title: Risolvere gli errori quando si eliminano account o contenitori di archiviazione oppure dischi rigidi virtuali della versione classica di Azure | Microsoft Docs
description: Risoluzione dei problemi in caso di eliminazione delle risorse di archiviazione contenenti dischi rigidi virtuali collegati.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: c74f2ef9eed25719e722970671406c850b6a59b2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002596"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Risolvere gli errori di eliminazione delle risorse di archiviazione della versione classica
Questo articolo fornisce indicazioni sulla risoluzione dei problemi quando si verifica uno degli errori seguenti durante il tentativo di eliminare un account o un contenitore di archiviazione oppure un file BLOB di pagine *.vhd della versione classica di Azure. 


Questo articolo illustra solo i problemi che si verificano con risorse di archiviazione della versione classica. Se un utente elimina una macchina virtuale della versione classica usando il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure, i dischi non verranno eliminati automaticamente. L'utente visualizza l'opzione per l'eliminazione della risorsa "disco". Se l'opzione non è selezionata, la risorsa "disco" impedirà l'eliminazione dell'account di archiviazione, del contenitore di archiviazione e del file BLOB di pagine *.vhd effettivo.

Altre informazioni sui dischi di Azure sono disponibili [qui](../../virtual-machines/managed-disks-overview.md): Azure impedisce l'eliminazione di un disco collegato a una macchina virtuale per evitare il danneggiamento. Impedisce anche l'eliminazione di contenitori e account di archiviazione che dispongono di un BLOB di pagine collegato a una macchina virtuale. 

## <a name="what-is-a-disk"></a>Che cos'è un "disco"?
Una risorsa "disco" viene usata per montare un file BLOB di pagine *.vhd in una macchina virtuale, come un disco del sistema operativo o un disco dati. Una risorsa disco del sistema operativo o disco dati continua a mantenere un lease sul file *.vhd finché non viene eliminata. Le risorse di archiviazione nel percorso mostrato nell'immagine seguente non possono essere eliminate se una risorsa "disco" punta a esse.

![Screenshot del portale, con il riquadro "Proprietà" del disco (versione classica) aperto](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Procedura di eliminazione di una macchina virtuale classica 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. Eliminare la macchina virtuale classica.
2. Se è selezionata la casella di controllo "Dischi", il **lease del disco** (illustrato nell'immagine precedente) associato al BLOB di pagine *.vhd viene interrotto. Il file *.vhd BLOB di pagine effettivo rimarrà comunque nell'account di archiviazione.
![Screenshot mostra una finestra di dialogo per confermare l'eliminazione di una macchina virtuale.](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Dopo aver interrotto il lease dei dischi, è possibile eliminare i BLOB di pagine. Un account o un contenitore di archiviazione può essere eliminato dopo che sono state eliminate tutte le risorse "disco" in esso contenute.

>[!NOTE] 
>Se l'utente elimina la macchina virtuale ma non il disco rigido virtuale, i costi di archiviazione continueranno ad accumularsi nel file *.vhd BLOB di pagine. Gli addebiti saranno in linea con il tipo di account di archiviazione. Per altre informazioni, controllare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/). Se l'utente non ha più intenzione di usare i dischi rigidi virtuali, eliminarli per evitare addebiti in futuro. 

## <a name="unable-to-delete-storage-account"></a>Impossibilità di eliminare l'account di archiviazione 

Quando l'utente tenta di eliminare un account di archiviazione della versione classica che non è più necessario, è possibile che si verifichi il comportamento seguente.

#### <a name="azure-portal"></a>Portale di Azure 
L'utente passa all'account di archiviazione della versione classica nel [portale di Azure](https://portal.azure.com), fa clic su **Elimina** e visualizza il messaggio seguente: 

Con i dischi "collegati" a una macchina virtuale

![Screenshot mostra un messaggio che spiega il motivo per cui non è possibile eliminare un account di archiviazione.](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Con i dischi "scollegati" da una macchina virtuale

![Screenshot del portale, con il riquadro di non errore di "eliminazione" (versione classica) della macchina virtuale aperto](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
L'utente tenta di eliminare un account di archiviazione che non viene più usato tramite i cmdlet di PowerShell classici. Verrà visualizzato il messaggio seguente:

> <span style="color:cyan">**Remove-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount: richiesta non valida: l'account di archiviazione myclassicaccount ha alcune immagini e/o dischi attivi, ad esempio  
> myclassicaccount. Assicurarsi di rimuovere tali immagini e/o dischi prima di eliminare l'account di archiviazione.</span>

## <a name="unable-to-delete-storage-container"></a>Impossibilità di eliminare il contenitore di archiviazione

Quando l'utente tenta di eliminare un contenitore BLOB di archiviazione della versione classica che non è più necessario, è possibile che si verifichi il comportamento seguente.

#### <a name="azure-portal"></a>Portale di Azure 
Il portale di Azure non consente all'utente di eliminare un contenitore se esiste un lease di "dischi" che punta a un file BLOB di pagine *.vhd nel contenitore. Questa è un'impostazione predefinita per impedire l'eliminazione accidentale di un file di dischi rigidi virtuali con lease di "dischi" su di essi. 

![Screenshot del portale, con il riquadro "elenco" dei contenitori di archiviazione aperto](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Se l'utente sceglie di effettuare l'eliminazione tramite PowerShell, si verificherà l'errore seguente. 

> <span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Name vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer: il server remoto ha restituito un errore: (412) attualmente esiste un lease nel contenitore e non è stato specificato alcun ID lease nella richiesta. Codice di stato HTTP: 412-messaggio di errore HTTP: attualmente esiste un lease nel contenitore e non è stato specificato alcun ID lease nella richiesta.</span>

## <a name="unable-to-delete-a-vhd"></a>Impossibilità di eliminare un disco rigido virtuale 

Dopo aver eliminato la macchina virtuale di Azure, l'utente tenta di eliminare il file di disco rigido virtuale (BLOB di pagine) e visualizza il messaggio seguente:

#### <a name="azure-portal"></a>Portale di Azure 
Nel portale possono verificarsi due situazioni in base all'elenco di BLOB selezionati per l'eliminazione.

1. Se sono selezionati solo i BLOB "Con lease", il pulsante Elimina non viene visualizzato.
![Screenshot del portale, con il riquadro dell'elenco di BLOB del contenitore aperto e solo i BLOB con lease selezionati.](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Se è selezionata una combinazione di BLOB "Con lease" e "Disponibili", il pulsante "Elimina" viene visualizzato. L'operazione di "eliminazione" tuttavia ignorerà i BLOB di pagine con un lease di disco. 
![Screenshot del portale, con il riquadro dell'elenco di BLOB del contenitore aperto ed è stato selezionato il lease e i BLOB disponibili. ](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
 ![ Schermata del portale, con il riquadro "Elimina" del BLOB selezionato aperto](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Se l'utente sceglie di effettuare l'eliminazione tramite PowerShell, si verificherà l'errore seguente. 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob: il server remoto ha restituito un errore: (412) attualmente esiste un lease nel BLOB e nella richiesta non è stato specificato alcun ID lease. Codice di stato HTTP: 412-messaggio di errore HTTP: attualmente esiste un lease nel BLOB e non è stato specificato alcun ID lease nella richiesta.</span>


## <a name="resolution-steps"></a>Procedura per la risoluzione

### <a name="to-remove-classic-disks"></a>Per rimuovere dischi della versione classica
Seguire questa procedura nel portale di Azure:
1.  Passare al [portale di Azure](https://portal.azure.com).
2.  Passare ai dischi (versione classica). 
3.  Fare clic sulla scheda dischi. ![ Screenshot mostra i portale di Azure con dischi (classico) selezionati e un nome di disco e un account di archiviazione classici.](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Selezionare il disco dati, quindi fare clic su Elimina disco.
 ![Screenshot mostra la portale di Azure con dischi (versione classica) selezionata, con un disco dati selezionato e l'opzione da eliminare.](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Ripetere l'operazione di eliminazione precedentemente non riuscita.
6.  Non è possibile eliminare un account o contenitori di archiviazione se dispongono di un singolo disco.

### <a name="to-remove-classic-images"></a>Per rimuovere immagini della versione classica   
Seguire questa procedura nel portale di Azure:
1.  Passare al [portale di Azure](https://portal.azure.com).
2.  Passare alle immagini del sistema operativo (versione classica).
3.  Eliminare l'immagine.
4.  Ripetere l'operazione di eliminazione precedentemente non riuscita.
5.  Non è possibile eliminare un account o un contenitore di archiviazione se dispone di una sola immagine.
