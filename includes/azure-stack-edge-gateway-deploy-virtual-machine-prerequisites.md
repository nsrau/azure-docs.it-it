---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 3a17e73c66c2296cc36b24e3b0a8abfcab00e46a
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89419402"
---
Prima di poter distribuire le macchine virtuali nel dispositivo Azure Stack Edge, è necessario configurare il client per la connessione al dispositivo tramite Azure Resource Manager Azure PowerShell. Per i passaggi dettagliati, vedere [connettersi a Azure Resource Manager nel dispositivo Azure stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Assicurarsi che i passaggi seguenti possano essere usati per accedere al dispositivo dal client (questa configurazione è stata eseguita durante la connessione a Azure Resource Manager, ma si sta verificando che la configurazione sia stata completata correttamente): 

1. Verificare il funzionamento della comunicazione Azure Resource Manager. Digitare:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Chiamare le API del dispositivo locale per l'autenticazione. Digitare: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Specificare il nome utente- *EdgeARMuser* e la password per la connessione tramite Azure Resource Manager.

1. Se il **calcolo** è stato configurato per Kubernetes, è possibile ignorare questo passaggio. Procedere per assicurarsi di avere abilitato un'interfaccia di rete per il calcolo. Nell'interfaccia utente locale passare a impostazioni di **calcolo** . Selezionare l'interfaccia di rete che si userà per creare uno switch virtuale. Le macchine virtuali create verranno collegate a un commutere virtuale collegato a questa porta e alla rete associata. Assicurarsi di scegliere una rete che corrisponda all'indirizzo IP che verrà usato per la macchina virtuale.  

    ![Abilita impostazioni di calcolo 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Abilitare il calcolo nell'interfaccia di rete. Azure Stack Edge creerà e gestirà un commutire virtuale corrispondente a tale interfaccia di rete. Non immettere in questo momento indirizzi IP specifici per Kubernetes. L'abilitazione del calcolo può richiedere diversi minuti.

    <!--If you decide to use another network interface for compute, make sure that you:
    
    - Delete all the VMs that you have deployed using Azure Resource Manager.
    
    - Delete all virtual network interfaces and the virtual network associated with this network interface. 
    
    - You can now enable another network interface for compute.-->

<!--1. You may also need to configure TLS 1.2 on your client machine if running older versions of AzCopy.--> 

