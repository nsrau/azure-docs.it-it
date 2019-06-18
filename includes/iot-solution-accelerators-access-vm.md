---
title: File di inclusione
description: File di inclusione
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 8d1f0f6ae3ffc123f1ae8318a6fbce4a9a3024fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814788"
---
## <a name="access-the-virtual-machine"></a>Accesso alla macchina virtuale

La procedura seguente usa il comando di Azure in Azure Cloud Shell. Se si preferisce, è possibile [installare CLI Azure](/cli/azure/install-azure-cli) sullo sviluppo di computer ed eseguire i comandi in locale.

I passaggi seguenti mostrano come configurare la macchina virtuale di Azure per consentire l'accesso **SSH**. I passaggi illustrati presuppongono che il nome scelto per l'acceleratore di soluzioni sia **contoso-simulation**. Sostituire questo valore con il nome della distribuzione:

1. Elenca il contenuto del gruppo di risorse che contiene le risorse dell'acceleratore di soluzioni:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Prendere nota del nome della macchina virtuale, dell'indirizzo IP pubblico e del gruppo di sicurezza di rete. Questi valori saranno necessari in un momento successivo.

1. Aggiornare il gruppo di sicurezza di rete per consentire l'accesso SSH. Il comando seguente presuppone che il nome del gruppo di sicurezza di rete sia **contoso-simulation-nsg**. Sostituire questo valore con il nome del gruppo di sicurezza di rete in uso:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Abilitare l'accesso SSH solo durante il test e lo sviluppo. Se si abilita SSH, [disabilitarlo appena possibile](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Aggiornare la password dell'account **azureuser** nella macchina virtuale con una password conosciuta. Scegliere la propria password quando si esegue questo comando:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Trovare l'indirizzo IP pubblico della macchina virtuale. Il comando seguente presuppone che il nome della macchina virtuale sia **vm-vikxv**. Sostituire questo valore con il nome della macchina virtuale annotato in precedenza:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Prendere nota dell'indirizzo IP pubblico della macchina virtuale.
