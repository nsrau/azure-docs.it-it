---
title: Spostare una configurazione di manutenzione in un'altra area di Azure
description: Informazioni su come spostare una configurazione di manutenzione della macchina virtuale in un'altra area di Azure
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 4cff7eb4a69005f2e74747b6e58447f100c69b60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86501603"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Spostare una configurazione del controllo di manutenzione in un'altra area

Seguire questo articolo per spostare una configurazione del controllo di manutenzione in un'area di Azure diversa. Potrebbe essere necessario spostare una configurazione per diversi motivi. Ad esempio, per sfruttare i vantaggi di una nuova area, per distribuire le funzionalità o i servizi disponibili in un'area specifica, per soddisfare i requisiti di governance e criteri interni oppure in risposta alla pianificazione della capacità.

Il controllo della manutenzione, con configurazioni di manutenzione personalizzate, consente di controllare la modalità di applicazione degli aggiornamenti della piattaforma alle macchine virtuali [Windows](./maintenance-control-cli.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) e [Linux](./maintenance-control-cli.md?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) e agli host dedicati di Azure. Esistono un paio di scenari per lo trasferimento del controllo di manutenzione tra le aree:

- Per spostare la configurazione del controllo di manutenzione, ma non le risorse associate alla configurazione, seguire le istruzioni riportate in questo articolo.
- Per spostare le risorse associate a una configurazione di manutenzione, ma non la configurazione stessa, seguire [queste istruzioni](move-region-maintenance-configuration-resources.md).
- Per spostare sia la configurazione di manutenzione che le risorse associate, seguire prima le istruzioni riportate in questo articolo. Seguire quindi [queste istruzioni](move-region-maintenance-configuration-resources.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a trasferire una configurazione del controllo di manutenzione:

- Le configurazioni di manutenzione sono associate a macchine virtuali di Azure o host dedicati di Azure. Prima di iniziare, verificare che le risorse VM/host siano presenti nella nuova area.
- Identify: 
    - Configurazioni esistenti del controllo di manutenzione.
    - Gruppi di risorse in cui si trovano attualmente le configurazioni esistenti. 
    - Gruppi di risorse a cui verranno aggiunte le configurazioni dopo il passaggio alla nuova area. 
    - Risorse associate alla configurazione di manutenzione che si desidera spostare.
    - Verificare che le risorse nella nuova regione siano uguali a quelle associate alle configurazioni di manutenzione correnti. Le configurazioni possono avere gli stessi nomi nella nuova area, come nel caso precedente, ma ciò non è obbligatorio.

## <a name="prepare-and-move"></a>Preparazione e spostamento 

1. Recuperare tutte le configurazioni di manutenzione in ogni sottoscrizione. Eseguire l'interfaccia della riga di comando [AZ Maintenance Configuration List](/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) per eseguire questa operazione, sostituendo $subId con l'ID sottoscrizione.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Esaminare l'elenco di tabelle restituite dei record di configurazione all'interno della sottoscrizione. Ecco un esempio. L'elenco conterrà i valori per l'ambiente specifico.

    **Nome** | **Posizione** | **Gruppo di risorse**
    --- | --- | ---
    Ignora manutenzione | eastus2 | configurazione: gruppo di risorse
    IgniteDemoConfig | eastus2 | configurazione: gruppo di risorse
    defaultMaintenanceConfiguration-eastus | eastus | test-configurazione
    

3. Salvare l'elenco per riferimento. Quando si spostano le configurazioni, è possibile verificare che tutti gli elementi siano stati spostati.
4. Come riferimento, eseguire il mapping di ogni configurazione/gruppo di risorse al nuovo gruppo di risorse nella nuova area.
5. Creare nuove configurazioni di manutenzione nella nuova area usando [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)o l' [interfaccia](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)della riga di comando.
6. Associare le configurazioni alle risorse nella nuova area, usando [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)o l' [interfaccia](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)della riga di comando.


## <a name="verify-the-move"></a>Verificare lo spostamento

Dopo aver spostato le configurazioni, confrontare le configurazioni e le risorse nella nuova area con l'elenco di tabelle preparato.


## <a name="clean-up-source-resources"></a>Pulire le risorse di origine

Dopo lo spostamento, provare a eliminare le configurazioni di manutenzione spostate nell'area di origine, in [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)o nell' [interfaccia](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)della riga di comando.


## <a name="next-steps"></a>Passaggi successivi

Seguire [queste istruzioni](move-region-maintenance-configuration-resources.md) se è necessario spostare le risorse associate alle configurazioni di manutenzione. 
