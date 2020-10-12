---
title: Errori della console seriale di Azure | Microsoft Docs
description: Errori comuni nella console seriale di Azure
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
ms.openlocfilehash: cad12a55332a6c7898f9709776c58d7dba8dd81a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526436"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Errori comuni nella console seriale di Azure
Nella console seriale di Azure è presente un set di errori noti. Si tratta di un elenco di questi errori e dei relativi passaggi di mitigazione.

## <a name="common-errors"></a>Errori comuni

Errore                             |   Strategia di riduzione del rischio
:---------------------------------|:--------------------------------------------|
"La console seriale di Azure richiede l'abilitazione della diagnostica di avvio. Fare clic qui per configurare la diagnostica di avvio per la macchina virtuale. " | Verificare che la VM o il set di scalabilità di macchine virtuali disponga della [diagnostica di avvio](boot-diagnostics.md) abilitata. Se si usa la console seriale in un'istanza del set di scalabilità di macchine virtuali, assicurarsi che l'istanza disponga del modello più recente.
"La console seriale di Azure richiede l'esecuzione di una macchina virtuale. Usare il pulsante avvia sopra per avviare la macchina virtuale ".  | Per accedere alla console seriale, la macchina virtuale o l'istanza del set di scalabilità di macchine virtuali deve essere avviata (la macchina virtuale non deve essere arrestata o deallocata). Verificare che la VM o l'istanza del set di scalabilità di macchine virtuali sia in esecuzione e riprovare.
"La console seriale di Azure non è abilitata per questa sottoscrizione, contattare l'amministratore della sottoscrizione per abilitare". | La console seriale di Azure può essere disabilitata a livello di sottoscrizione. Se si è un amministratore della sottoscrizione, è possibile [abilitare e disabilitare la console seriale di Azure](./serial-console-enable-disable.md). Se non si è un amministratore della sottoscrizione, è necessario contattare l'amministratore della sottoscrizione per i passaggi successivi.
È stata rilevata una risposta "Accesso negato" durante l'accesso all'account di archiviazione di diagnostica di avvio della macchina virtuale. | Assicurarsi che la diagnostica di avvio non disponga di un firewall dell'account. Un account di archiviazione di diagnostica di avvio accessibile è necessario per il funzionamento della console seriale. Per impostazione predefinita, la console seriale non funziona con i firewall dell'account di archiviazione abilitati nell'account di archiviazione della diagnostica di avvio.
Non si hanno le autorizzazioni necessarie per usare questa macchina virtuale con la console seriale. Assicurarsi di avere almeno le autorizzazioni del ruolo Collaboratore Macchina virtuale.| L'accesso alla console seriale richiede l'accesso a livello di collaboratore o superiore nella VM o nel set di scalabilità di macchine virtuali. Per ulteriori informazioni, vedere la [pagina Panoramica](serial-console-overview.md).
L'account di archiviazione '' usato per la diagnostica di avvio in questa macchina virtuale non è stato trovato. Verificare che la diagnostica di avvio sia abilitata per questa macchina virtuale, che l'account di archiviazione non sia stato eliminato e di avere accesso a questo account di archiviazione. | Verificare che non sia stato eliminato l'account di archiviazione della diagnostica di avvio per la macchina virtuale o il set di scalabilità di macchine virtuali
La connessione della console seriale alla macchina virtuale ha rilevato un errore:' richiesta non valida ' (400) | Questo problema può verificarsi se l'URI della diagnostica di avvio non è corretto. Ad esempio, è stato usato "http://" anziché "https://". L'URI di diagnostica di avvio può essere corretto con questo comando: `az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Non si dispone delle autorizzazioni necessarie per scrivere nell'account di archiviazione della diagnostica di avvio per questa macchina virtuale. Assicurarsi di avere almeno le autorizzazioni di collaboratore per le macchine virtuali | Console seriale l'accesso richiede l'accesso a livello di collaboratore nell'account di archiviazione della diagnostica di avvio. Per ulteriori informazioni, vedere la [pagina Panoramica](serial-console-overview.md).
Non è possibile determinare il gruppo di risorse per l'account di archiviazione della diagnostica di avvio * &lt; STORAGEACCOUNTNAME &gt; *. Verificare che la diagnostica di avvio sia abilitata per questa VM e di avere accesso a questo account di archiviazione. | Console seriale l'accesso richiede l'accesso a livello di collaboratore nell'account di archiviazione della diagnostica di avvio. Per ulteriori informazioni, vedere la [pagina Panoramica](serial-console-overview.md).
Il provisioning per questa macchina virtuale non è stato ancora completato. Assicurarsi che la macchina virtuale sia completamente distribuita e ripetere la connessione alla console seriale. | È ancora possibile eseguire il provisioning della macchina virtuale o del set di scalabilità di macchine virtuali. Attendere del tempo e riprovare.
Il Web socket è chiuso o non può essere aperto. | Potrebbe essere necessario aggiungere l'accesso al firewall a `*.console.azure.com` . Un approccio più dettagliato ma più lungo consiste nel consentire l'accesso del firewall agli [intervalli IP del data center Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), che cambiano abbastanza regolarmente.
Console seriale non funziona con un account di archiviazione usando Azure Data Lake Storage Gen2 con spazi dei nomi gerarchici. | Si tratta di un problema noto con gli spazi dei nomi gerarchici. Per attenuare, verificare che l'account di archiviazione della diagnostica di avvio della macchina virtuale non venga creato con Azure Data Lake Storage Gen2. Questa opzione può essere impostata solo al momento della creazione dell'account di archiviazione. Potrebbe essere necessario creare un account di archiviazione di diagnostica di avvio separato senza Azure Data Lake Storage Gen2 abilitato per attenuare questo problema.
Si è verificato un errore durante la connessione della console seriale alla macchina virtuale:' Forbidden ' (SubscriptionNotEnabled)-nome sottoscrizione non definito. ID \<subscription id> in stato non abilitato non definito | Questo problema può verificarsi se la sottoscrizione in cui un utente ha creato il Cloud Shell account di archiviazione è stata disabilitata. Per attenuare, avviare Cloud Shell ed [eseguire i passaggi necessari](../../cloud-shell/persisting-shell-storage.md#unmount-clouddrive-1) per effettuare il provisioning di un account di archiviazione di supporto per cloud Shell nella sottoscrizione corrente.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [console seriale di Azure per macchine virtuali Linux](./serial-console-linux.md)
* Altre informazioni sulla [console seriale di Azure per le macchine virtuali Windows](./serial-console-windows.md)
