---
title: Azure Serial Console errors | Microsoft Docs
description: Common errors within the Azure Serial Console
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: 7bd9fe4044dace4061285c016cb08562b556b98e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483613"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Common errors within the Azure Serial Console
There are a set of known errors within the Azure Serial Console. This is a list of those errors and mitigation steps for them.

## <a name="common-errors"></a>Errori comuni

Errore                             |   Mitigazione
:---------------------------------|:--------------------------------------------|
"Azure Serial Console requires boot diagnostics to be enabled. Click here to configure boot diagnostics for your virtual machine." ![Boot diagnostics error](./media/virtual-machines-serial-console/virtual-machines-serial-console-boot-diagnostics-error.png) | Ensure that the VM or virtual machine scale set has [boot diagnostics](boot-diagnostics.md) enabled. If you are using serial console on a virtual machine scale set instance, ensure that your instance has the latest model.
"Azure Serial Console requires a virtual machine to be running. Use the Start button above to start your virtual machine." ![Deallocated error](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | The VM or virtual machine scale set instance must be in a started state to access the serial console (your VM must not be stopped or deallocated). Ensure your VM or virtual machine scale set instance is running and try again.
"Azure Serial Console is not enabled for this subscription, contact your subscription administrator to enable." ![Subscription disabled error](./media/virtual-machines-serial-console/virtual-machines-serial-console-subscription-disabled-error.png) | The Azure Serial Console can be disabled at a subscription level. If you are a subscription administrator, you may [enable and disable the Azure Serial Console](./serial-console-enable-disable.md). If you are not a subscription administrator, you should reach out to your subscription administrator for next steps.
È stata rilevata una risposta "Accesso negato" durante l'accesso all'account di archiviazione di diagnostica di avvio della macchina virtuale. ![Storage account firewall error](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| Assicurarsi che la diagnostica di avvio non disponga di un firewall dell'account. Un account di archiviazione di diagnostica di avvio accessibile è necessario per il funzionamento della console seriale. Per impostazione predefinita, la console seriale non funziona con i firewall dell'account di archiviazione abilitati nell'account di archiviazione della diagnostica di avvio.
Non si hanno le autorizzazioni necessarie per usare questa macchina virtuale con la console seriale. Assicurarsi di avere almeno le autorizzazioni del ruolo Collaboratore Macchina virtuale.| The serial console access requires you to have contributor level access or above on your VM or virtual machine scale set. For more information, see the [overview page](serial-console-overview.md).
The storage account '' used for boot diagnostics on this VM could not be found. Verify that boot diagnostics is enabled for this VM, this storage account has not been deleted, and you have access to this storage account. | Double check that you have not deleted the boot diagnostics storage account for your VM or virtual machine scale set
Provisioning for this VM has not yet succeeded. Please ensure the VM is fully deployed and retry the serial console connection. | Your VM or virtual machine scale set may still be provisioning. Wait some time and try again.
You do not have the required permissions to write to the boot diagnostics storage account for this VM. Please ensure you have at least VM Contributor permissions on ''. | Serial console access requires contributor level access on the boot diagnostics storage account. For more information, see the [overview page](serial-console-overview.md).
Impossibile determinare il gruppo di risorse per l'account di archiviazione della diagnostica di avvio *&lt;STORAGEACCOUNTNAME&gt;* . Verificare che la diagnostica di avvio sia abilitata per questa macchina virtuale e di avere accesso a questo account di archiviazione. | Serial console access requires contributor level access on the boot diagnostics storage account. For more information, see the [overview page](serial-console-overview.md).
Il Web socket è chiuso o non può essere aperto. | You may need to add firewall access to `*.console.azure.com`. A more detailed but longer approach is to allow firewall access to the [Microsoft Azure Datacenter IP ranges](https://www.microsoft.com/download/details.aspx?id=41653), which change fairly regularly.
Serial console does not work with a storage account using Azure Data Lake Storage Gen2 with hierarchical namespaces. | This is a known issue with hierarchical namespaces. To mitigate, ensure that your VM's boot diagnostics storage account is not created using Azure Data Lake Storage Gen2. This option can only be set upon storage account creation. You may have to create a separate boot diagnostics storage account without Azure Data Lake Storage Gen2 enabled to mitigate this issue.


## <a name="next-steps"></a>Passaggi successivi
* Learn more about the [Azure Serial Console for Linux VMs](./serial-console-linux.md)
* Learn more about the [Azure Serial Console for Windows VMs](./serial-console-windows.md)