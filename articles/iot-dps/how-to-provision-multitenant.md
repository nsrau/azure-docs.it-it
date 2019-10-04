---
title: Come effettuare il provisioning di dispositivi per la multi-tenancy nel servizio Device Provisioning in hub IoT di Azure | Microsoft Docs
description: Come effettuare il provisioning di dispositivi per la multi-tenancy con l'istanza del servizio di provisioning di dispositivi
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 84e1f57175d772ad281c18b67fa1be484c0cac69
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66116119"
---
# <a name="how-to-provision-for-multitenancy"></a>Come effettuare il provisioning per la multi-tenancy 

I criteri di allocazione definiti dal servizio di provisioning supportano una vasta gamma di scenari di allocazione. Due scenari comuni sono:

* **Georilevazione/GeoLatency**: quando un dispositivo si sposta tra le posizioni, la latenza della rete viene migliorata effettuando il provisioning del dispositivo sull'hub IoT più vicino a ogni posizione. In questo scenario per le registrazioni viene selezionato un gruppo di hub IoT, che si estendono su più aree geografiche. Per queste registrazioni il criterio di allocazione selezionato è **Latenza più bassa**. Questo criterio fa sì che il servizio Device Provisioning valuti la latenza del dispositivo e determini l'hub IoT più vicino tra quelli all'interno del gruppo. 

* **Multi-tenancy**: è possibile che i dispositivi usati all'interno di una soluzione IoT debbano essere assegnati a un hub IoT o a un gruppo di hub IoT specifico. La soluzione potrebbe richiedere che tutti i dispositivi per un determinato tenant comunichino con un gruppo specifico di hub IoT. In alcuni casi un tenant potrebbe disporre di hub IoT e richiedere che i dispositivi siano assegnati ai relativi hub IoT.

È comune combinare questi due scenari. Ad esempio, una soluzione IoT multi-tenant assegnerà comunemente dispositivi tenant usando un gruppo di hub IoT dislocati tra diverse aree. Questi dispositivi tenant possono essere assegnati all'hub IoT in tale gruppo, che ha la latenza più bassa in base alla posizione geografica.

Questo articolo usa un esempio di dispositivo simulato proveniente da [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) per illustrare come effettuare il provisioning di dispositivi in uno scenario multi-tenant tra più aree geografiche. In questo articolo si eseguiranno i passaggi seguenti:

* Usare l'interfaccia della riga di comando di Azure per creare due hub IoT a livello di area (**Stati Uniti occidentali** e **Stati Uniti orientali**)
* Creare una registrazione multi-tenant
* Usare l'interfaccia della riga di comando di Azure per creare due macchine virtuali Linux a livello di area che fungano da dispositivi nelle stesse aree (**Stati Uniti occidentali** e **Stati Uniti orientali**)
* Configurare l'ambiente di sviluppo per Azure IoT C SDK in entrambe le macchine virtuali Linux
* Simulare i dispositivi per verificare che sia stato effettuato il provisioning per lo stesso tenant nell'area più vicina.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prerequisiti

* Completamento della guida introduttiva per [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>Creare due hub IoT a livello di area

In questa sezione si userà Azure Cloud Shell per creare due nuovi hub IoT a livello di area nelle aree **Stati Uniti occidentali** e **Stati Uniti orientali** per un tenant.


1. Usare Azure Cloud Shell per creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

    L'esempio seguente crea un gruppo di risorse denominato *contoso-us-resource-group* nell'area *eastus*. È consigliabile usare questo gruppo per tutte le risorse create in questo articolo. Ciò faciliterà la pulizia una volta completate le operazioni.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Usare Azure Cloud Shell per creare un hub IoT nell'area **eastus** con il comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). L'hub IoT verrà aggiunto a *contoso-us-resource-group*.

    L'esempio seguente crea un hub IoT denominato *contoso-east-hub* nella località *eastus*. È necessario usare il nome univoco dell'hub al posto di **contoso-east-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Il completamento del comando può richiedere alcuni minuti.

3. Usare Azure Cloud Shell per creare un hub IoT nell'area **westus** con il comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Questo hub IoT verrà aggiunto anche a *contoso-us-resource-group*.

    L'esempio seguente crea un hub IoT denominato *contoso-west-hub* nella località *westus*. È necessario usare il nome univoco dell'hub al posto di **contoso-west-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Il completamento del comando può richiedere alcuni minuti.



## <a name="create-the-multitenant-enrollment"></a>Creare la registrazione multi-tenant

In questa sezione si creerà un nuovo gruppo di registrazione per i dispositivi tenant.  

Per semplicità, in questo articolo viene usata l'[attestazione con chiave simmetrica](concepts-symmetric-key-attestation.md) per la registrazione. Per una soluzione più sicura è consigliabile usare l'[attestazione del certificato X.509](concepts-security.md#x509-certificates) con una catena di certificati.

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'istanza del servizio Device Provisioning.

2. Selezionare la scheda **Gestisci registrazioni**, quindi fare clic sul pulsante **Aggiungi gruppo di registrazioni** nella parte superiore della pagina. 

3. In **Aggiungi gruppo di registrazioni** immettere le informazioni seguenti e fare clic sul pulsante **Salva**.

    **Nome gruppo**: immettere **contoso-us-devices**.

    **Tipo di attestazione**: selezionare **Chiave simmetrica**.

    **Genera chiavi automaticamente**: questa casella di controllo dovrebbe essere già selezionata.

    **Selezionare la modalità di assegnazione dei dispositivi agli hub**: selezionare **Latenza più bassa**.

    ![Aggiungere il gruppo di registrazione multi-tenant per l'attestazione con chiave simmetrica](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. In **Aggiungi gruppo di registrazione** fare clic su **Collega un nuovo hub IoT** per collegare entrambi gli hub a livello di area.

    **Sottoscrizione** se si hanno più sottoscrizioni, scegliere quella in cui sono stati creati gli hub IoT a livello di area.

    **Hub IoT**: selezionare uno degli hub a livello di area creato.

    **Criteri di accesso**: scegliere **iothubowner**.

    ![Collegare gli hub IoT a livello di area al servizio di provisioning](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Una volta collegati entrambi gli hub IoT a livello di area, è necessario selezionarli per il gruppo di registrazione e fare clic su **Salva** per creare il gruppo dell'hub IoT a livello di area per la registrazione.

    ![Creare il gruppo dell'hub a livello di area per la registrazione](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Dopo aver salvato la registrazione, aprirla nuovamente e annotare la **Chiave primaria**. È necessario salvare la registrazione prima di poter generare le chiavi. Questa chiave verrà usata per generare chiavi univoche di dispositivo per entrambi i dispositivi simulati in un secondo momento.


## <a name="create-regional-linux-vms"></a>Creare macchine virtuali Linux a livello di area

In questa sezione verranno create due macchine virtuali Linux a livello di area. Queste macchine virtuali eseguiranno un esempio di simulazione dei dispositivi da ogni area per illustrare il provisioning dei dispositivi per i dispositivi tenant di entrambe le aree.

Per rendere più semplici le operazioni di pulizia, queste macchine virtuali verranno aggiunte allo stesso gruppo di risorse contenente gli hub IoT creati, ovvero *contoso-us-resource-group*. Tuttavia, le macchine virtuali verranno eseguite in aree separate (**Stati Uniti occidentali** e **Stati Uniti orientali**).

1. In Azure Cloud Shell eseguire il comando seguente per creare una macchina virtuale dell'area **Stati Uniti orientali** dopo aver apportato le modifiche ai parametri seguenti nel comando:

    **--name**: immettere un nome univoco per la macchina virtuale del dispositivo a livello di area **Stati Uniti orientali**. 

    **--admin-username**: usare il proprio nome utente amministratore.

    **--admin-password**: usare la propria password amministratore.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Il completamento del comando richiederà alcuni minuti. Dopo che il comando è stato completato, prendere nota del valore **publicIpAddress** per la macchina virtuale a livello di area Stati Uniti orientali.

1. In Azure Cloud Shell eseguire il comando per creare una macchina virtuale dell'area **Stati Uniti occidentali** dopo aver apportato le modifiche ai parametri seguenti nel comando:

    **--name**: immettere un nome univoco per la macchina virtuale del dispositivo a livello di area **Stati Uniti occidentali**. 

    **--admin-username**: usare il proprio nome utente amministratore.

    **--admin-password**: usare la propria password amministratore.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Il completamento del comando richiederà alcuni minuti. Dopo che il comando è stato completato, prendere nota del valore **publicIpAddress** per la macchina virtuale a livello di area Stati Uniti occidentali.

1. Aprire due shell della riga di comando. Connettersi a una delle macchine virtuali a livello di area in ogni shell tramite SSH. 

    Passare il nome utente amministratore e l'indirizzo IP pubblico annotato per la macchina virtuale come parametri per la connessione SSH. Quando richiesto, immettere la password amministratore.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Preparare l'ambiente di sviluppo per Azure IoT C SDK

In questa sezione si clonerà Azure IoT C SDK in ogni macchina virtuale. L'SDK contiene un esempio che consentirà di simulare il provisioning di dispositivi del tenant da ogni area.


1. Per ogni macchina virtuale, installare **Cmake**, **g++** , **gcc** e [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) usando i comandi seguenti:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```


1. Clonare [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) in entrambe le macchine virtuali.

    ```bash
    cd ~/
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Il completamento di questa operazione richiederà alcuni minuti.

1. Per entrambe le macchine virtuali, creare una nuova cartella **cmake** all'interno del repository e passare a tale cartella.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Per entrambe le macchine virtuali, eseguire il comando seguente, che compila una versione dell'SDK specifica per la piattaforma client di sviluppo. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Al termine della compilazione, le ultime righe di output saranno simili all'output seguente:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>Derivare le chiavi univoche di dispositivo

Quando si usa l'attestazione con chiave simmetrica con le registrazioni di gruppo, non si usano direttamente le chiavi dei gruppi di registrazione. Viene invece creata una chiave derivata univoca per ogni dispositivo, come indicato in [Group Enrollments with symmetric keys](concepts-symmetric-key-attestation.md#group-enrollments) (Registrazioni di gruppo con chiavi simmetriche).

Per generare la chiave di dispositivo, usare la chiave master del gruppo per calcolare un valore [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) dell'ID registrazione univoco per il dispositivo e convertire il risultato nel formato Base64.

Non includere la chiave master del gruppo nel codice del dispositivo.

Usare l'esempio di shell Bash per creare una chiave di dispositivo derivata per ogni dispositivo tramite **openssl**.

- Sostituire il valore per **KEY** con la **Chiave primaria** annotata in precedenza per la registrazione.

- Sostituire il valore per **REG_ID** con il proprio ID registrazione univoco per ogni dispositivo. Usare caratteri alfanumerici minuscoli e trattini ("-") per definire entrambi gli ID.

Generazione della chiave di dispositivo di esempio per *contoso-simdevice-east*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Generazione della chiave di dispositivo di esempio per *contoso-simdevice-west*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


I dispositivi tenant useranno entrambi la chiave di dispositivo derivata e l'ID registrazione univoco per eseguire l'attestazione con chiave simmetrica per il gruppo di registrazione durante il provisioning agli hub IoT del tenant.




## <a name="simulate-the-devices-from-each-region"></a>Simulare i dispositivi da ogni area


In questa sezione si aggiornerà un esempio di provisioning in Azure IoT C SDK per entrambe le macchine virtuali a livello di area. 

Il codice di esempio simula una sequenza di avvio di dispositivo che invia la richiesta di provisioning all'istanza del servizio Device Provisioning. La sequenza di avvio riconoscerà il dispositivo e lo assegnerà all'hub IoT più vicino in base alla latenza.

1. Nel portale di Azure selezionare la scheda **Panoramica** per il servizio Device Provisioning e prendere nota del valore di **_Ambito ID_** .

    ![Estrarre le informazioni dell'endpoint del servizio Device Provisioning dal pannello del portale](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Aprire **~/azure-iot-sdk-c/provisioning\_client/samples/prov\_dev\_client\_sample/prov\_dev\_client\_sample.c** per la modifica in entrambe le macchine virtuali.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Trovare la costante `id_scope` e sostituire il valore con il valore **Ambito ID** copiato in precedenza. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Trovare la definizione per la funzione `main()` nello stesso file. Assicurarsi che la variabile `hsm_type` sia impostata su `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` come illustrato di seguito, in modo che corrisponda al metodo di attestazione del gruppo di registrazione. 

    Salvare le modifiche ai file in entrambe le macchine virtuali.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Su entrambe le macchine virtuali individuare la chiamata a `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c** che è impostata come commento.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Rimuovere il commento dalla chiamata alla funzione e sostituire i valori di segnaposto (incluse le parentesi angolari) con l'ID registrazione univoco e le chiave di dispositivo derivate per ogni dispositivo. Le chiavi illustrate di seguito sono solo a titolo di esempio. Usare le chiavi generate in precedenza.

    Stati Uniti orientali:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    Stati Uniti occidentali:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Salvare i file.

1. In entrambe le macchine virtuali passare alla cartella di esempio illustrata di seguito e compilare l'esempio.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Al termine della compilazione, eseguire **prov\_dev\_client\_sample.exe** in entrambe le macchine virtuali per simulare un dispositivo tenant da ogni area. Si noti che ogni dispositivo viene allocato all'hub IoT del tenant più vicino alle aree del dispositivo simulato.

    Eseguire la simulazione:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Output di esempio per la macchina virtuale Stati Uniti orientali:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Output di esempio per la macchina virtuale Stati Uniti occidentali:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare a usare le risorse create in questo articolo, è possibile mantenerle. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create in questo articolo per evitare inutili spese.

Questi passaggi presuppongono che tutte le risorse in questo articolo siano state create come indicato nello stesso gruppo di risorse denominato **contoso-us-resource-group**.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se si è creato l'hub IoT all'interno di un gruppo di risorse esistente che contiene risorse che si vogliono conservare, eliminare solo la risorsa dell'hub IoT invece di eliminare il gruppo di risorse.
>

Per eliminare il gruppo di risorse per nome:

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.

2. Nella casella di testo **Filtra per nome...** digitare il nome del gruppo di risorse che contiene le risorse di interesse, **contoso-us-resource-group**. 

3. A destra del gruppo di risorse nell'elenco dei risultati fare clic su **...** quindi su **Elimina gruppo di risorse**.

4. Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere di nuovo il nome del gruppo di risorse per confermare e fare clic su **Elimina**. Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su altre Reprovisioning, vedere [dispositivo Hub IoT un nuovo provisioning concetti](concepts-device-reprovision.md) 
- Per informazioni su ulteriori il deprovisioning di utenti, vedere [come effettuare il deprovisioning di dispositivi che sono stati precedentemente sottoposti a provisioning](how-to-unprovision-devices.md) 











