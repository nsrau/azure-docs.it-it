---
title: Controllo di manutenzione per le macchine virtuali di Azure con il portale di Azure
description: Informazioni su come controllare quando viene applicata la manutenzione alle VM di Azure usando il controllo di manutenzione e il portale di Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 4232f4cd39c3f6dd6efa0bf78959798ea842e2dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84676047"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>Controllare gli aggiornamenti con il controllo di manutenzione e i portale di Azure

Il controllo della manutenzione consente di decidere quando applicare gli aggiornamenti alle VM isolate e agli host dedicati di Azure. In questo argomento vengono illustrate le opzioni di portale di Azure per il controllo di manutenzione. Per ulteriori informazioni sui vantaggi derivanti dall'utilizzo del controllo manutenzione, delle relative limitazioni e di altre opzioni di gestione, vedere [gestione degli aggiornamenti della piattaforma con il controllo di manutenzione](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Creare una configurazione di manutenzione

1. Accedere al portale di Azure.

1. Cercare le **configurazioni di manutenzione**.

   ![Screenshot che illustra come aprire le configurazioni di manutenzione](media/virtual-machines-maintenance-control-portal/maintenance-configurations-search.png)

1. Scegliere **Aggiungi**.

   ![Screenshot che illustra come aggiungere una configurazione di manutenzione](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add.png)

1. Scegliere una sottoscrizione e un gruppo di risorse, specificare un nome per la configurazione e scegliere un'area. Fare clic su **Avanti**.

   ![Screenshot con le nozioni di base sulla configurazione di manutenzione](media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics.png)

1. Aggiungere i tag e i valori. Fare clic su **Avanti**.

   ![Screenshot che illustra come aggiungere tag a una configurazione di manutenzione](media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags.png)

1. Esaminare il riepilogo. Fare clic su **Crea**.

   ![Screenshot che illustra come creare una configurazione di manutenzione](media/virtual-machines-maintenance-control-portal/maintenance-configurations-create.png)

1. Al termine della distribuzione, fare clic su **Vai alla risorsa**.

   ![Screenshot che mostra la distribuzione della configurazione di manutenzione completata](media/virtual-machines-maintenance-control-portal/maintenance-configurations-deployment-complete.png)

## <a name="assign-the-configuration"></a>Assegnare la configurazione

Nella pagina dettagli della configurazione di manutenzione fare clic su assegnazioni e quindi fare clic su **Assegna risorsa**. 

![Screenshot che illustra come assegnare una risorsa](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

Selezionare le risorse a cui si desidera assegnare la configurazione di manutenzione e fare clic su **OK**. La colonna **tipo** indica se la risorsa è una macchina virtuale isolata o un host dedicato di Azure. Per assegnare la configurazione, è necessario che la macchina virtuale sia in esecuzione. Si verifica un errore se si tenta di assegnare una configurazione a una macchina virtuale arrestata. 

<!---Shantanu to add details about the error case--->

![Screenshot che illustra come selezionare una risorsa](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>Verificare la configurazione

È possibile verificare che la configurazione sia stata applicata correttamente o controllare la configurazione di manutenzione attualmente assegnata usando le **configurazioni di manutenzione**. La colonna **tipo** indica se la configurazione è assegnata a una macchina virtuale isolata o a un host dedicato di Azure. 

![Screenshot che illustra come verificare una configurazione di manutenzione](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

È anche possibile controllare la configurazione di una macchina virtuale specifica nella relativa pagina delle proprietà. Fare clic su **manutenzione** per visualizzare la configurazione assegnata a tale macchina virtuale.

![Screenshot che illustra come controllare la manutenzione per un host](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>Verificare la presenza di aggiornamenti in sospeso

Esistono anche due modi per verificare se gli aggiornamenti sono in sospeso per una configurazione di manutenzione. In **configurazioni di manutenzione**, in dettagli per la configurazione, fare clic su **assegnazioni** e controllare **lo stato di manutenzione**.

![Screenshot che illustra come controllare gli aggiornamenti in sospeso](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

È anche possibile controllare un host specifico usando le **macchine virtuali** o le proprietà dell'host dedicato. 

![Screenshot che illustra come controllare la manutenzione per un host](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>Applicare gli aggiornamenti

È possibile applicare aggiornamenti in sospeso su richiesta tramite **macchine virtuali**. Nei dettagli della macchina virtuale fare clic su **manutenzione** e quindi su **applica manutenzione**.

![Screenshot che illustra come applicare gli aggiornamenti in sospeso](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>Verificare lo stato dell'applicazione degli aggiornamenti 

È possibile controllare lo stato di avanzamento degli aggiornamenti per una configurazione in **configurazioni di manutenzione** o usando **macchine virtuali**. Nei dettagli della macchina virtuale fare clic su **manutenzione**. Nell'esempio seguente lo stato di **manutenzione** indica che un aggiornamento è **in sospeso**.

![Screenshot che illustra come controllare lo stato degli aggiornamenti in sospeso](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>Eliminare una configurazione di manutenzione

Per eliminare una configurazione, aprire i dettagli di configurazione e fare clic su **Elimina**.

![Screenshot che illustra come controllare la manutenzione per un host](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [manutenzione e aggiornamenti](maintenance-and-updates.md).
