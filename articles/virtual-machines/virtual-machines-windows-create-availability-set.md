---
title: "Creare un set di disponibilità per una VM in Azure | Documentazione Microsoft"
description: "Informazioni su come creare un set di disponibilità gestito o non gestito per le macchine virtuali tramite Azure PowerShell o il portale nel modello di distribuzione di Resource Manager."
keywords: "set di disponibilità"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a3db8659-ace8-4e78-8b8c-1e75c04c042c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 652c4c51d67b8914885406e631e7233694a8a1d8
ms.openlocfilehash: a221f3bc9e058a46e46edf8f7177d21151ae3595
ms.lasthandoff: 03/01/2017

---
# <a name="increase-vm-availability-by-creating-an-azure-availability-set"></a>Aumentare la disponibilità per una macchina virtuale tramite la creazione di un set di disponibilità di Azure 
I set di disponibilità forniscono ridondanza all'applicazione. È consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia sempre disponibile e soddisfi per almeno il 99,95% i requisiti del contratto di servizio di Azure. Per altre informazioni, vedere [Contratto di Servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> La macchina virtuale deve essere creata nello stesso gruppo di risorse del set di disponibilità.
> 

Se si desidera includere la VM in un set di disponibilità, è necessario creare quest'ultimo prima o durante la creazione della prima macchina virtuale nel set. Se la macchina virtuale usa Managed Disks, è necessario creare il set di disponibilità come set di disponibilità gestito.

Per altre informazioni sulla creazione e sull'uso dei set di disponibilità, vedere l'articolo relativo alla [gestione della disponibilità delle macchine virtuali](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>Utilizzare il portale per creare un set di disponibilità prima di creare le VM
1. Nel menu dell'hub, fare clic su **Sfoglia** e selezionare **Set di disponibilità**.
2. Nel pannello **Set di disponibilità** fare clic su **Aggiungi**.
   
    ![Schermata che mostra il pulsante Aggiungi per creare un nuovo set di disponibilità.](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)
3. Nel pannello **Crea set di disponibilità** compilare le informazioni sul set.
   
    ![Schermata che mostra le informazioni da immettere per creare il set di disponibilità.](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)
   
   * **Nome** : il nome deve contenere da 1 a 80 caratteri, tra numeri, lettere, punti, caratteri di sottolineatura e trattini. Il primo carattere deve essere una lettera o un numero. L'ultimo carattere deve essere una lettera o un numero o un carattere di sottolineatura.
   * **Domini di errore** : i domini di errore definiscono il gruppo di macchine virtuali che condividono una fonte di alimentazione e uno switch di rete comuni. Per impostazione predefinita, le VM sono distribuite in un massimo di tre domini di errore; per la distribuzione possono essere usati da 1 a 3 domini.
   * **Domini di aggiornamento**: per impostazione predefinita vengono assegnati cinque domini di aggiornamento, ma è possibile impostarne da 1 a 20. I domini di aggiornamento indicano gruppi di macchine virtuali e l'hardware fisico sottostante che possono essere riavviati nello stesso momento. Ad esempio, se si specificano cinque domini di aggiornamento, quando in un set di disponibilità vengono configurate più di cinque macchine virtuali, la sesta macchina viene inserita nello stesso dominio di aggiornamento della prima, la settima nel dominio di aggiornamento della seconda e così via. L'ordine delle operazioni di riavvio potrebbe non essere sequenziale, ma verrà riavviato un solo dominio di aggiornamento alla volta.
   * **Sottoscrizione** : se sono disponibili più sottoscrizioni, selezionare quella da usare.
   * **Gruppo di risorse**: per selezionare un gruppo di risorse esistente, fare clic sulla freccia verso il basso e scegliere un gruppo di risorse dall'elenco. È inoltre possibile creare un nuovo gruppo di risorse digitando un nome. Il nome può contenere lettere, numeri, punti, trattini, caratteri di sottolineatura e parentesi aperte o chiuse, mentre non può terminare con un punto. Tutte le VM nel gruppo di disponibilità devono essere create nello stesso gruppo di risorse del set di disponibilità.
   * **Percorso** : selezionare un percorso nell'elenco a discesa.
   * **Gestito**: selezionare *Sì* per creare un set di disponibilità gestito da usare con le macchine virtuali che usano Managed Disks per l'archiviazione. Selezionare **No** se le macchine virtuali presenti nel set usano dischi non gestiti in un account di archiviazione.
   
4. Dopo aver immesso le informazioni, fare clic su **Crea**. 

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>Usare il portale per creare una macchina virtuale e un set di disponibilità contemporaneamente
Quando si crea una nuova VM tramite il portale, è anche possibile creare un nuovo set di disponibilità durante la creazione della prima VM del set. Se si sceglie di usare Managed Disks per la macchina virtuale, verrà creato un set di disponibilità gestito.

![Schermata che illustra la procedura di creazione di un nuovo set di disponibilità in fase di creazione della VM.](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)

## <a name="add-a-new-vm-to-an-existing-availability-set-in-the-portal"></a>Aggiungere una nuova VM a un set di disponibilità esistente nel portale
Per ogni nuova VM da aggiungere al set, assicurarsi di creare la VM nello stesso **gruppo di risorse** e di selezionare il set di disponibilità esistente al Passaggio 3. 

![Schermata che illustra come selezionare un set di disponibilità da utilizzare per la VM.](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)

## <a name="use-powershell-to-create-an-availability-set"></a>Usare PowerShell per creare un set di disponibilità
In questo esempio viene creato un set di disponibilità denominato **myAvailabilitySet** nel gruppo di risorse **myResourceGroup** nella posizione **Stati Uniti occidentali**. Questa operazione deve essere eseguita prima di creare la prima VM da inserire nel set.

Prima di iniziare, verificare di avere la versione più recente del modulo di PowerShell AzureRM.Compute. Eseguire il comando seguente per installarlo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Per altre informazioni, vedere [Controllo delle versioni di Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning).


Se si usano dischi gestiti per le macchine virtuali, digitare:

```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "myResourceGroup" '
    -Name "myAvailabilitySet" -Location "West US" -managed
```

Se si usa il proprio account di archiviazione per le macchine virtuali, digitare:

```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "myResourceGroup" '
    -Name "myAvailabilitySet" -Location "West US" 
```

Per ulteriori informazioni, vedere [New AzureRmAvailabilitySet](/powershell/new-azurermavailabilityset).

## <a name="troubleshooting"></a>Risoluzione dei problemi
* Quando si crea una VM, se il set di disponibilità desiderato non si trova nell'elenco a discesa nel portale, potrebbe essere stato creato in un altro gruppo di risorse. Se non si conosce il gruppo di risorse del set di disponibilità, accedere al menu dell'hub e fare clic su Sfoglia > Gruppi di disponibilità per visualizzare un elenco dei set di disponibilità e dei gruppi di risorse appartenenti a ciascuno.

## <a name="next-steps"></a>Passaggi successivi
Aumentare lo spazio di archiviazione della VM aggiungendo un ulteriore [disco dati](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


