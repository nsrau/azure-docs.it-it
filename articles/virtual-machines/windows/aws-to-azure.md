---
title: Spostare macchine virtuali AWS Windows in Azure | Microsoft Docs
description: Spostare un'istanza Windows di Amazon Web Services (AWS) EC2 in Macchine virtuali di Azure usando Azure PowerShell.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: cynthn
ms.openlocfilehash: 7d2b498d3f84c4fd6cccf97c6d7781f293f5b395
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-azure-using-powershell"></a>Spostare una VM Windows da Amazon Web Services (AWS) ad Azure usando PowerShell

Se si stanno valutando le macchine virtuali di Azure per l'hosting dei carichi di lavoro, è possibile esportare un'istanza di VM Windows di Amazon Web Services (AWS) EC2 esistente e quindi caricare il disco rigido virtuale in Azure. Dopo il caricamento del disco rigido virtuale è possibile creare una nuova VM in Azure dal disco rigido virtuale. 

Questo argomento illustra lo spostamento di una singola VM da AWS ad Azure. Per spostare VM da AWS ad Azure su larga scala, vedere [Eseguire la migrazione delle macchine virtuali in Amazon Web Services (AWS) ad Azure con Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Preparare la macchina virtuale 
 
È possibile caricare dischi rigidi virtuali generalizzati e specializzati in Azure. Entrambi i tipi richiedono prima di tutto la preparazione della macchina virtuale prima dell'esportazione da AWS. 

- **Disco rigido virtuale generalizzato**: tutte le informazioni sull'account personale sono state rimosse dal disco rigido virtuale generalizzato usando Sysprep. Se si vuole usare il disco rigido virtuale come immagine dalla quale creare nuove macchine virtuali, è necessario: 
 
    * [Preparare una VM Windows](prepare-for-upload-vhd-image.md).  
    * Generalizzare una macchina virtuale Windows con Sysprep.  

 
- **Disco rigido virtuale specializzato**: un disco rigido virtuale specializzato gestisce gli account utente, le applicazioni e altri dati di stato dalla macchina virtuale originale. Se si intende usare il disco rigido virtuale così come è per creare una nuova macchina virtuale, assicurare il completamento delle operazioni seguenti.  
    * [Preparare un disco rigido virtuale (VHD) di Windows per il caricamento in Azure](prepare-for-upload-vhd-image.md). **Non** generalizzare la macchina Virtuale con Sysprep. 
    * Rimuovere tutti gli strumenti di virtualizzazione guest e gli agenti installati nella macchina virtuale, ad esempio gli strumenti VMware. 
    * Assicurarsi che la macchina virtuale sia configurata per eseguire il pull dell'indirizzo IP e delle impostazioni DNS tramite DHCP. In questo modo il server ottiene un indirizzo IP all'interno della rete virtuale all'avvio.  


## <a name="export-and-download-the-vhd"></a>Esportare e scaricare il disco rigido virtuale 

Esportare l'istanza EC2 in un disco rigido virtuale in un bucket Amazon S3. Seguire la procedura illustrata nell'argomento [Exporting an Instance as a VM Using VM Import/Export](http://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) (Esportazione di un'istanza come VM tramite l'importazione/esportazione della VM) della documentazione di Amazon ed eseguire il comando [create-instance-export-task](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) per esportare l'istanza EC2 in un file di disco rigido virtuale. 

Il file di disco rigido virtuale esportato viene salvato nel bucket Amazon S3 indicato. La sintassi di base per l'esportazione del disco rigido virtuale viene riportata di seguito. È sufficiente sostituire il testo segnaposto in <brackets> con le informazioni specifiche.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Dopo l'esportazione del disco rigido virtuale, seguire le istruzioni disponibili in [How Do I Download an Object from an S3 Bucket?](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) (Come scaricare un oggetto da un bucket S3) per scaricare il file disco rigido virtuale dal bucket S3. 

> [!IMPORTANT]
> AWS applica addebiti per il trasferimento di dati per il download del disco rigido virtuale. Per altre informazioni, vedere [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/) (Prezzi di Amazon S3).


## <a name="next-steps"></a>Passaggi successivi

È ora possibile caricare il disco rigido virtuale in Azure e creare una nuova VM. 

- Se è stato eseguito Sysprep nell'origine per la **generalizzazione** prima dell'esportazione, vedere [Caricare un disco rigido virtuale generalizzato e usarlo per creare una nuova VM in Azure](upload-generalized-managed.md)
- Se non è stato eseguito Sysprep prima dell'esportazione, il disco rigido virtuale viene considerato **specializzato**. Vedere [Caricare un disco rigido virtuale specializzato in Azure e creare una nuova VM](create-vm-specialized.md)

 
