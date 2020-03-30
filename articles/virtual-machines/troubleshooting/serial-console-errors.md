---
title: Errori della console seriale di Azure Documenti Microsoft
description: Errori comuni all'interno della console seriale di AzureCommon errors within the Azure Serial Console
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
ms.openlocfilehash: 61ae0ef92fe522a2a038a6076a5e0c0a10ee47b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060698"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Errori comuni all'interno della console seriale di AzureCommon errors within the Azure Serial Console
Esistono un set di errori noti all'interno della console seriale di Azure.There are a set of known errors within the Azure Serial Console. Questo è un elenco di tali errori e passaggi di attenuazione per essi.

## <a name="common-errors"></a>Errori comuni

Errore                             |   Strategia di riduzione del rischio
:---------------------------------|:--------------------------------------------|
"La console seriale di Azure richiede l'abilitazione della diagnostica di avvio. Fare clic qui per configurare la diagnostica di avvio per la macchina virtuale." | Verificare che la macchina virtuale o il set di scalabilità della macchina virtuale sia abilitata per la diagnostica di [avvio.](boot-diagnostics.md) Se si usa una console seriale in un'istanza del set di scalabilità di macchine virtuali, verificare che l'istanza disponga del modello più recente.
"La console seriale di Azure richiede l'esecuzione di una macchina virtuale. Usare il pulsante Start sopra riportato per avviare la macchina virtuale."  | L'istanza del set di scalabilità della macchina virtuale o della macchina virtuale deve essere in uno stato avviato per accedere alla console seriale (la macchina virtuale non deve essere arrestata o deallocata). Verificare che l'istanza del set di scalabilità della macchina virtuale o della macchina virtuale sia in esecuzione e riprovare.
"Console seriale di Azure non è abilitata per questa sottoscrizione, contattare l'amministratore della sottoscrizione per abilitare." | La console seriale di Azure può essere disabilitata a livello di sottoscrizione. Se si è un amministratore della sottoscrizione, è possibile [abilitare e disabilitare la console seriale](./serial-console-enable-disable.md)di Azure. Se non si è un amministratore della sottoscrizione, contattare l'amministratore della sottoscrizione per i passaggi successivi.
È stata rilevata una risposta "Accesso negato" durante l'accesso all'account di archiviazione di diagnostica di avvio della macchina virtuale. | Assicurarsi che la diagnostica di avvio non disponga di un firewall dell'account. Un account di archiviazione di diagnostica di avvio accessibile è necessario per il funzionamento della console seriale. Per impostazione predefinita, la console seriale non funziona con i firewall dell'account di archiviazione abilitati nell'account di archiviazione della diagnostica di avvio.
Non si hanno le autorizzazioni necessarie per usare questa macchina virtuale con la console seriale. Assicurarsi di avere almeno le autorizzazioni del ruolo Collaboratore Macchina virtuale.| L'accesso alla console seriale richiede l'accesso a livello di collaboratore o superiore nel set di scalabilità della macchina virtuale o della macchina virtuale. Per ulteriori informazioni, vedere la [pagina di panoramica](serial-console-overview.md).
Impossibile trovare l'account di archiviazione '' usato per la diagnostica di avvio in questa macchina virtuale. Verificare che la diagnostica di avvio sia abilitata per questa macchina virtuale, che l'account di archiviazione non sia stato eliminato e di avere accesso a questo account di archiviazione. | Verificare di non aver eliminato l'account di archiviazione di diagnostica di avvio per la macchina virtuale o il set di scalabilità della macchina virtuale
La connessione della console seriale alla macchina virtuale ha rilevato un errore: 'Richiesta non valida' (400)The serial console connection to the VM encountered an error: 'Bad Request' (400) | Ciò può verificarsi se l'URI di diagnostica di avvio non è corretto. Ad esempio, è stato utilizzato "http://" al posto di "https://". L'URI di diagnostica di avvio può essere risolto con questo comando:The boot diagnostics URI can be fixed with this command:`az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Non si dispone delle autorizzazioni necessarie per scrivere nell'account di archiviazione di diagnostica di avvio per questa macchina virtuale. Assicurarsi di disporre almeno delle autorizzazioni Collaboratore VM | L'accesso alla console seriale richiede l'accesso a livello di collaboratore nell'account di archiviazione di diagnostica di avvio. Per ulteriori informazioni, vedere la [pagina di panoramica](serial-console-overview.md).
Impossibile determinare il gruppo di risorse * &lt;&gt;* per l'account di archiviazione di diagnostica di avvio STORAGEACCOUNTNAME . Verificare che la diagnostica di avvio sia abilitata per questa VM e di avere accesso a questo account di archiviazione. | L'accesso alla console seriale richiede l'accesso a livello di collaboratore nell'account di archiviazione di diagnostica di avvio. Per ulteriori informazioni, vedere la [pagina di panoramica](serial-console-overview.md).
Il provisioning per questa macchina virtuale non è ancora riuscito. Assicurarsi che la macchina virtuale sia completamente distribuita e ritentare la connessione della console seriale. | Il provisioning della macchina virtuale o del set di scalabilità della macchina virtuale potrebbe essere ancora in fase di provisioning. Attendere un po' di tempo e riprovare.
Il Web socket è chiuso o non può essere aperto. | Potrebbe essere necessario aggiungere `*.console.azure.com`l'accesso del firewall a . Un approccio più dettagliato ma più lungo consiste nel consentire l'accesso del firewall agli intervalli IP del data [center di Microsoft Azure,](https://www.microsoft.com/download/details.aspx?id=41653)che cambiano abbastanza regolarmente.
La console seriale non funziona con un account di archiviazione che usa Azure Data Lake Storage Gen2 con spazi dei nomi gerarchici. | Si tratta di un problema noto con gli spazi dei nomi gerarchici. Per attenuare, verificare che l'account di archiviazione di diagnostica di avvio della macchina virtuale non venga creato usando Azure Data Lake Storage Gen2.To mitigate, ensure that your VM's boot diagnostics storage account is not created using Azure Data Lake Storage Gen2. Questa opzione può essere impostata solo al momento della creazione dell'account di archiviazione. Potrebbe essere necessario creare un account di archiviazione di diagnostica di avvio separato senza Azure Data Lake Storage Gen2 abilitato per ridurre questo problema.
La connessione della console seriale alla macchina virtuale ha rilevato un errore: 'Accesso \<negato'(SubscriptionNotEnabled) - Nome sottoscrizione non definito, ID sottoscrizione> è nello stato non abilitato non definito | Questo problema può verificarsi se la sottoscrizione che un utente ha creato il proprio account di archiviazione Cloud Shell in è stata disabilitata. Per attenuare, avviare Cloud Shell ed [eseguire i passaggi necessari](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#unmount-clouddrive-1) per eseguire nuovamente il provisioning di un account di archiviazione di backup per Cloud Shell nella sottoscrizione corrente.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla console seriale di Azure per le macchine virtuali LinuxLearn more about the [Azure Serial Console for Linux VMs](./serial-console-linux.md)
* Altre informazioni sulla console seriale di Azure per le macchine virtuali WindowsLearn more about [the Azure Serial Console for Windows VMs](./serial-console-windows.md)