---
title: Esercitazione sul trasferimento di dati all'account di archiviazione con Azure Stack Edge con GPU| Microsoft Docs
description: Informazioni su come aggiungere e connettersi agli account di archiviazione locali ed Edge nel dispositivo Azure Stack Edge con GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to storage accounts on Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 66c09b7fa0eb8fbf709441da5996fb2237b3e284
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256397"
---
# <a name="tutorial-transfer-data-via-storage-accounts-with-azure-stack-edge-gpu"></a>Esercitazione: Trasferire i dati tramite gli account di archiviazione con Azure Stack Edge con GPU 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Questa esercitazione descrive come aggiungere account di archiviazione e connettersi a tali account nel dispositivo Azure Stack Edge. Dopo aver aggiunto gli account di archiviazione, Azure Stack Edge può trasferire i dati in Azure.

Per completare questa procedura sono necessari circa 30 minuti.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Aggiungere un account di archiviazione
> * Effettuare la connessione all'account di archiviazione

 
## <a name="prerequisites"></a>Prerequisiti

Prima di aggiungere gli account di archiviazione ad Azure Stack Edge, verificare che:

- Il dispositivo fisico sia stato installato come descritto in [Installare Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).

- Il dispositivo fisico sia stato attivato come descritto in [Attivare il dispositivo Azure Stack Edge](azure-stack-edge-gpu-deploy-activate.md).


## <a name="add-an-edge-storage-account"></a>Aggiungere un account di archiviazione Edge

Per creare un account di archiviazione Edge, seguire questa procedura:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]


## <a name="connect-to-the-edge-storage-account"></a>Connettersi all'account di archiviazione Edge

È ora possibile connettersi alle API REST di archiviazione Edge tramite *HTTP* o *HTTPS*.

- *HTTPS* è il modo sicuro e consigliato.
- La connessione tramite *HTTP* viene essere usata solo su reti attendibili.

## <a name="connect-via-http"></a>Connettersi tramite HTTP

Per la connessione alle API REST di archiviazione Edge tramite HTTP, sono necessari i passaggi seguenti:

- Aggiungere l'endpoint del servizio BLOB e l'indirizzo VIP del servizio coerente di Azure all'host remoto
- Verificare la connessione 

Ogni passaggio viene descritto nelle sezioni seguenti.

### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-client"></a>Aggiungere l'indirizzo IP del dispositivo e l'endpoint del servizio BLOB al client remoto

[!INCLUDE [Add device IP and blob service endpoint to host file](../../includes/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint.md)]


### <a name="verify-connection"></a>Verificare la connessione

Per verificare la connessione, sono in genere necessarie le informazioni seguenti (variabili) raccolte nel passaggio precedente:

- Nome dell'account di archiviazione.
- Chiave di accesso dell'account di archiviazione.
- Endpoint del servizio BLOB.

Sono già disponibili il nome dell'account di archiviazione e l'endpoint del servizio BLOB. È possibile ottenere la chiave di accesso dell'account di archiviazione connettendosi al dispositivo tramite Azure Resource Manager usando un client di Azure PowerShell.

Eseguire la procedura descritta in [Connettersi al dispositivo tramite Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md). Dopo aver effettuato l'accesso alle API del dispositivo locale tramite Azure Resource Manager, ottenere l'elenco degli account di archiviazione nel dispositivo. Eseguire il cmdlet seguente:

`Get-AzureRMStorageAccount`

Dall'elenco degli account di archiviazione nel dispositivo, identificare l'account di archiviazione per cui è necessaria la chiave di accesso. Prendere nota del nome dell'account di archiviazione e del gruppo di risorse.

Di seguito è riportato un output di esempio:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccount

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
myasetiered1       myasetiered1      DBELocal StandardLRS Storage            11/27/2019 7:10:12 PM Succeeded         False
```

Per ottenere la chiave di accesso, eseguire il cmdlet seguente:

`Get-AzureRmStorageAccountAccessKey`

Di seguito è riportato un output di esempio:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasetiered1
Name: myasetiered1

KeyName Value    Permissions                                                                                
------- -----    -----------                                                                                
key1    Jb2brrNjRNmArFcDWvL4ufspJjlo+Nie1uh8Mp4YUOVQNbirA1uxEdHeV8Z0dXbsG7emejFWI9hxyR1T93ZncA==        Full
key2    6VANuHzHcJV04EFeyPiWRsFWnHPkgmX1+a3bt5qOQ2qIzohyskIF/2gfNMqp9rlNC/w+mBqQ2mI42QgoJSmavg==        Full
```

Copiare e salvare questa chiave. Questa chiave verrà usata per verificare la connessione usando Azure Storage Explorer.

Per verificare che la connessione sia stata stabilita correttamente, usare Storage Explorer per collegarsi a un account di archiviazione esterno. Se Storage Explorer non è disponibile, [scaricarlo](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409).

[!INCLUDE [Verify connection using Storage Explorer](../../includes/azure-stack-edge-gateway-verify-connection-storage-explorer.md)]


## <a name="connect-via-https"></a>Connettersi tramite HTTPS

Per la connessione alle API REST di archiviazione BLOB di Azure tramite HTTPS, sono necessari i passaggi seguenti:

- Ottenere il certificato dell'endpoint BLOB
- Importare il certificato nel client o nell'host remoto
- Aggiungere l'indirizzo IP del dispositivo e l'endpoint di servizio BLOB al client o all'host remoto
- Configurare e verificare la connessione

Ogni passaggio viene descritto nelle sezioni seguenti.

### <a name="get-certificate"></a>Ottenere il certificato

L'accesso all'archiviazione BLOB tramite HTTPS richiede un certificato SSL per il dispositivo. Il certificato verrà inoltre caricato nel dispositivo Azure Stack Edge come file con estensione *pfx* con una chiave privata allegata. Per altre informazioni su come creare (solo a scopo di test e sviluppo) e caricare questi certificati nel dispositivo Azure Stack Edge, vedere:

- [Creare il certificato dell'endpoint BLOB](azure-stack-edge-j-series-manage-certificates.md#create-certificates-optional).
- [Caricare il certificato dell'endpoint BLOB](azure-stack-edge-j-series-manage-certificates.md#upload-certificates).
- [Importare i certificati nel client che accede al dispositivo](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

### <a name="import-certificate"></a>Importare il certificato

Se si usa Azure Storage Explorer per connettersi agli account di archiviazione nel dispositivo, è anche necessario importare il certificato in Storage Explorer nel formato PEM. Nell'ambiente Windows, i file con estensione *cer* e codifica Base64 equivalgono al formato PEM.

Per importare i certificati in Azure Storage Explorer, seguire questa procedura:

1. Assicurarsi che per Azure Storage Explorer siano impostate le API di Azure Stack come destinazione. Passare a **Modifica > API di Azure Stack di destinazione**. Quando richiesto, riavviare Storage Explorer per applicare la modifica.

2. Per importare i certificati SSL, passare a **Modifica > Certificati SSL > Importa certificati**.

  
   ![Importare i certificati in Storage Explorer](./media/azure-stack-edge-j-series-deploy-add-storage-accounts/import-cert-storage-explorer-1.png) 

3. Individuare e specificare la catena di firma e i certificati BLOB. Sia la catena di firma che il certificato BLOB devono essere in formato PEM, che corrisponde al formato con codifica Base64 nel sistema Windows. Si riceverà una notifica della corretta importazione dei certificati.


### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Aggiungere l'indirizzo IP del dispositivo e l'endpoint di servizio BLOB

Seguire la stessa procedura per [aggiungere l'indirizzo IP del dispositivo e l'endpoint di servizio BLOB quando ci si connette tramite *HTTP*](#add-device-ip-address-and-blob-service-endpoint-to-the-remote-client).

### <a name="configure-and-verify-connection"></a>Configurare e verificare la connessione

Seguire la procedura per [Configurare e verificare la connessione usata per connettersi tramite *HTTP*](#verify-connection). L'unica differenza è che è consigliabile lasciare deselezionata l'opzione *Use http* (Usa HTTP).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati i seguenti argomenti relativi ad Azure Stack Edge:

> [!div class="checklist"]
> * Aggiungere un account di archiviazione
> * Eseguire la connessione a un account di archiviazione

Per informazioni su come trasformare i dati con Azure Stack Edge, passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Trasformare i dati con Azure Stack Edge](./azure-stack-edge-j-series-deploy-configure-compute.md)


