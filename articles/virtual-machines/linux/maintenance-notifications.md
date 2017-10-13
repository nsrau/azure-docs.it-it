---
title: Gestire gli avvisi di manutenzione per macchine virtuali Linux in Azure | Microsoft Docs
description: Visualizzare gli avvisi relativi alla manutenzione per macchine virtuali Linux in esecuzione in Azure e avviare la manutenzione self-service.
services: virtual-machines-linux
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: zivr
ms.openlocfilehash: 2fc96898c9b7ebefc7648b5819975d9015e76c0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>Gestire gli avvisi relativi alla manutenzione pianificata per le macchine virtuali Linux

Azure esegue periodicamente aggiornamenti per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host per le macchine virtuali. Gli aggiornamenti sono considerati modifiche, ad esempio l'applicazione di patch all'ambiente di hosting o l'aggiornamento e la rimozione di componenti hardware. La maggior parte di questi aggiornamenti viene eseguita senza alcuna conseguenza per le macchine virtuali ospitate. Ci sono casi tuttavia in cui gli aggiornamenti hanno conseguenze:

- Se la manutenzione non richiede un riavvio, Azure usa la migrazione sul posto per sospendere la VM mentre l'host viene aggiornato.

- Se la manutenzione richiede un riavvio, si riceve un avviso che informa per quando è pianificata la manutenzione. In questi casi, viene anche indicata una finestra temporale in cui avviare la manutenzione manualmente, in un momento opportuno per l'utente.


La manutenzione pianificata che richiede un riavvio viene pianificata a ondate. Ogni ondata ha un ambito diverso (aree),

- e inizia con un avviso ai clienti. Per impostazione predefinita, l'avviso viene inviato ai comproprietari e ai proprietari della sottoscrizione. Agli avvisi è possibile aggiungere più destinatari e opzioni di messaggistica come messaggio di posta elettronica, SMS e Webhook. 
- Dopo l'avviso viene avviata la finestra self-service. In questa finestra è possibile individuare le macchine virtuali incluse nell'ondata e avviare la manutenzione usando la ridistribuzione proattiva. 
- Dopo la finestra self-service ha inizio la finestra di manutenzione pianificata. In questo intervallo Azure pianifica e applica la manutenzione necessaria alla macchina virtuale.  

La presenza di due finestre consente di avere tempo sufficiente per iniziare la manutenzione e riavviare la macchina virtuale, sapendo comunque quando Azure avvierà automaticamente la manutenzione.

Per eseguire una query sulla finestra di manutenzione della macchina virtuale e avviare la manutenzione self-service è possibile usare il portale di Azure, PowerShell, API REST e l'interfaccia della riga di comando di Azure.

 > [!NOTE]
 > Se si tenta di avviare la manutenzione senza riuscirci, Azure contrassegna la macchina virtuale come **ignorata** e non la riavvia durante la finestra di manutenzione pianificata. L'utente viene contattato in un secondo momento con una nuova pianificazione. 

## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>Individuare le macchine virtuali con manutenzione pianificata con l'interfaccia della riga di comando

È possibile visualizzare le informazioni per la manutenzione pianificata con [azure vm get-instance-view](/cli/azure/vm?view=azure-cli-latest#az_vm_get_instance_view).
 
Le informazioni vengono restituite solo se è presente una manutenzione pianificata. Se non è presente alcuna manutenzione pianificata per la macchina virtuale, il comando non restituisce informazioni. 

```azure-cli
az vm get-instance-view  - g rgName  -n vmName 
```

MaintenanceRedeployStatus restituisce i valori seguenti: 

| Valore | Descrizione   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se in questo momento è possibile avviare la manutenzione per la macchina virtuale ||
| PreMaintenanceWindowStartTime         | Inizio della finestra di manutenzione self-service, che segnala la possibilità di avviare la manutenzione della VM ||
| PreMaintenanceWindowEndTime           | Termine della finestra di manutenzione self-service, che segnala la possibilità di avviare la manutenzione della VM ||
| MaintenanceWindowStartTime            | Inizio della finestra di manutenzione pianificata, che segnala la possibilità di avviare la manutenzione della VM ||
| MaintenanceWindowEndTime              | Termine della finestra di manutenzione pianificata, che segnala la possibilità di avviare la manutenzione della VM ||
| LastOperationResultCode               | Risultato dell'ultimo tentativo di avviare la manutenzione della macchina virtuale ||


## <a name="start-maintenance-on-your-vm-using-cli"></a>Avviare la manutenzione della macchina virtuale usando l'interfaccia della riga di comando

La chiamata seguente avvia la manutenzione in una macchina virtuale se `IsCustomerInitiatedMaintenanceAllowed` è impostata su true.

```azure-cli
az vm perform-maintenance rgName vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come registrare eventi di manutenzione da una macchina virtuale usando [gli eventi pianificati](scheduled-events.md).