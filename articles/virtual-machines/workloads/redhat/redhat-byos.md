---
title: Red Hat Enterprise Linux le immagini Bring your own susbcription | Microsoft Docs
description: Informazioni sulle immagini Bring your own Subscription per Red Hat Enterprise Linux in Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: alsin
ms.openlocfilehash: afd59c44bea238b7dd6f116e491054f8b4a52d48
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486508"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-images-in-azure"></a>Red Hat Enterprise Linux immagini Bring your own Subscription in Azure
Le immagini Red Hat Enterprise Linux (RHEL) sono disponibili in Azure tramite un modello con pagamento in base al consumo (PAYG) o bring your own Subscription (BYOS). Questo documento fornisce una panoramica delle immagini BYOS in Azure.

## <a name="important-points-to-consider"></a>Punti importanti da considerare

- Le immagini RHEL BYOS fornite in questo programma sono immagini RHEL pronte per l'ambiente di produzione simili alle immagini RHEL PAYG nella raccolta/Marketplace di Azure. Il processo di registrazione per ottenere le immagini è in anteprima.

- Le immagini seguono i criteri correnti descritti in [Red Hat Enterprise Linux immagini in Azure](./redhat-images.md)

- I criteri di supporto standard si applicano alle macchine virtuali create da queste immagini

- Le macchine virtuali di cui è stato effettuato il provisioning dalle immagini RHEL BYOS non contengono tariffe RHEL associate alle immagini RHEL PAYG

- Le immagini non sono autorizzate, quindi è necessario usare Subscription-Manager per registrare e sottoscrivere le VM per ottenere gli aggiornamenti direttamente da Red Hat

- Attualmente non è possibile passare dinamicamente tra i modelli di fatturazione BYOS e PAYG per le immagini Linux. Per cambiare il modello di fatturazione, è necessaria la ridistribuzione della macchina virtuale dalla rispettiva immagine

- Crittografia dischi di Azure (ADE) è supportata in queste immagini RHEL BYOS. Il supporto ADE è attualmente in fase di anteprima. Prima di configurare ADE, è necessario registrarsi con Red Hat usando Subscription-Manager. Dopo la registrazione, per configurare ADE, vedere: [abilitare crittografia dischi di Azure per macchine virtuali IaaS Linux](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-overview)

- Sebbene le immagini non cambino (oltre gli aggiornamenti e le patch standard), il processo di registrazione è in anteprima e il flusso verrà ulteriormente migliorato per semplificare il processo

- Si ha il controllo completo sulle VM già sottoposte a provisioning da queste immagini o dai relativi snapshot indipendentemente dall'implementazione finale

## <a name="requirements-and-conditions-to-access-the-rhel-byos-images"></a>Requisiti e condizioni per accedere alle immagini RHEL BYOS

1. Acquisire familiarità con le condizioni del [programma Red Hat cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) e registrarsi nella [pagina di registrazione dell'accesso cloud di Red Hat](https://access.redhat.com/cloude/manager/image_imports/new).

1. Completare il [modulo di richiesta di accesso BYOS di RHEL](https://aka.ms/rhel-byos). È necessario avere a disposizione il numero di account Red Hat e le sottoscrizioni di Azure con cui si accede alle immagini RHEL BYOS con.

1. Dopo la revisione e l'approvazione da Red Hat e Microsoft, le sottoscrizioni di Azure verranno abilitate per l'accesso alle immagini.

### <a name="expected-time-for-image-access"></a>Tempo previsto per l'accesso alle immagini

Dopo aver completato il modulo RHEL BYOS e aver accettato i termini, Red Hat convaliderà l'idoneità per le immagini BYOS entro tre giorni lavorativi e, se valido, riceverà l'accesso alle immagini BYOS entro un giorno lavorativo.

## <a name="use-the-rhel-byos-images-from-the-azure-portal"></a>Usare le immagini RHEL BYOS nel portale di Azure

1. Quando la sottoscrizione è abilitata per le immagini RHEL BYOS, è possibile individuarla nella [portale di Azure](https://portal.azure.com) passando a **Crea una risorsa** e quindi **Visualizza tutto**.

1. Nella parte superiore della pagina si noterà che si dispone di offerte private.

    ![Offerte private del Marketplace](./media/rhel-byos-privateoffers.png)

1. È possibile fare clic sul collegamento viola o scorrere fino alla fine della pagina per visualizzare le offerte private.

1. Il resto del provisioning nell'interfaccia utente non sarà diverso da quello di altre immagini Red Hat esistenti. Scegliere la versione RHEL e seguire le istruzioni per eseguire il provisioning della macchina virtuale. Questo processo consentirà anche di accettare le condizioni dell'immagine nel passaggio finale.

>[!NOTE]
>Questa procedura fino a questo punto non consentirà l'immagine di RHEL BYOS per la distribuzione a livello di codice. verrà richiesto un passaggio aggiuntivo, come descritto nella sezione "informazioni aggiuntive" riportata di seguito.

Il resto di questo documento è incentrato sul metodo dell'interfaccia della riga di comando per il provisioning e l'accettazione di termini nell'immagine. L'interfaccia utente e l'interfaccia della riga di comando sono completamente intercambiabili per quanto riguarda il risultato finale, ovvero una macchina virtuale BYOS RHEL di cui è stato effettuato il provisioning.

## <a name="use-the-rhel-byos-images-from-the-azure-cli"></a>Usare le immagini RHEL BYOS dall'interfaccia della riga di comando di Azure
Il set di istruzioni seguente descrive il processo di distribuzione iniziale per una VM RHEL usando l'interfaccia della riga di comando di Azure. Queste istruzioni presuppongono che sia [installata l'interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli)della riga di comando di Azure.

>[!IMPORTANT]
>Assicurarsi di usare tutte le lettere minuscole nei riferimenti al server di pubblicazione, all'offerta, al piano e all'immagine per tutti i comandi seguenti

1. Verificare di essere nella sottoscrizione desiderata:
    ```azurecli
    az account show -o=json
    ```

1. Creare un gruppo di risorse per la macchina virtuale BYOS RHEL:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Accetta le condizioni per l'immagine:
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >Questi termini devono essere accettati *una sola volta per ogni sottoscrizione di Azure, per ogni SKU di immagine*.

1. Opzionale Convalidare la distribuzione della macchina virtuale con il comando seguente:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Eseguire il provisioning della macchina virtuale eseguendo lo stesso comando precedente senza l'argomento `--validate`:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. Connettersi tramite SSH alla macchina virtuale e verificare di disporre di un'immagine non autorizzata. A tale scopo, eseguire `sudo yum repolist`. L'output richiederà di usare Subscription-Manager per registrare la macchina virtuale con Red Hat.

## <a name="additional-information"></a>Informazioni aggiuntive
- Se si tenta di effettuare il provisioning di una macchina virtuale in una sottoscrizione non abilitata per questa offerta, si riceverà l'errore seguente ed è necessario contattare Microsoft o Red Hat per abilitare la sottoscrizione.
    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

- Se si crea uno snapshot dall'immagine RHEL BYOS e si pubblica l'immagine nella [raccolta immagini condivise](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries), sarà necessario specificare le informazioni sul piano corrispondenti all'origine dello snapshot. Ad esempio, il comando potrebbe essere simile a (si notino i parametri del piano nella riga finale):
    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

- Se si usa l'automazione per eseguire il provisioning di macchine virtuali dalle immagini RHEL BYOS, sarà necessario specificare parametri di piano simili a quelli illustrati in precedenza. Se, ad esempio, si utilizza la bonifica, è necessario fornire le informazioni sul piano in un [blocco di piano](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sull' [infrastruttura di Azure Red Hat Update](./redhat-rhui.md).
* Per altre informazioni sulle immagini di Red Hat in Azure, vedere la [pagina della documentazione](./redhat-images.md).
* Informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL sono reperibili alla pagina [Red Hat Enterprise Linux Life Cycle (Ciclo di vita di Red Hat Enterprise Linux)](https://access.redhat.com/support/policy/updates/errata).