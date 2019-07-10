---
title: Configurare il dispositivo IoT Edge - Machine Learning in Azure IoT Edge | Microsoft Docs
description: Configurare una macchina virtuale di Azure basata su Linux come dispositivo Azure IoT Edge che funge da gateway trasparente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a2096004a7b389f627c528a8dfb4768ac001f390
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155617"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Esercitazione: Configurare un dispositivo IoT Edge

> [!NOTE]
> Questo articolo fa parte di una serie di documenti relativi a un'esercitazione sull'uso di Azure Machine Learning in IoT Edge. Se l'articolo è stato aperto direttamente, è consigliabile iniziare con il [primo articolo](tutorial-machine-learning-edge-01-intro.md) della serie per risultati ottimali.

In questo articolo viene configurata una macchina virtuale di Azure basata su Linux come dispositivo Azure IoT Edge che funge da gateway trasparente. La configurazione del gateway trasparente consente ai dispositivi di connettersi all'hub IoT di Azure tramite il gateway senza riconoscerne l'esistenza. Allo stesso tempo, un utente può interagire con i dispositivi nell'hub IoT senza essere a conoscenza del dispositivo gateway intermedio. In definitiva, il gateway trasparente viene usato per aggiungere analisi dei dispositivi perimetrali con l'aggiunta di moduli IoT Edge al gateway.

I passaggi di questo articolo vengono in genere eseguiti da sviluppatori cloud.

## <a name="generate-certificates"></a>Generare i certificati

Perché possa operare come gateway, un dispositivo deve essere in grado di connettersi in modo sicuro a dispositivi downstream. Azure IoT Edge permette di usare un'infrastruttura a chiave pubblica (PKI) per configurare connessioni sicure tra dispositivi. Nel caso illustrato si consente la connessione di un dispositivo downstream a un dispositivo IoT Edge che funge da gateway trasparente. Per mantenere un livello di sicurezza ragionevole, il dispositivo downstream deve confermare l'identità del dispositivo IoT Edge. Per altre informazioni su come vengono usati i certificati dai dispositivi IoT Edge, vedere [Dettagli di utilizzo dei certificati di Azure IoT Edge](iot-edge-certs.md).

In questa sezione vengono creati certificati autofirmati usando un'immagine Docker che viene quindi creata ed eseguita. È stato scelto di usare un'immagine Docker per completare questo passaggio perché riduce sensibilmente il numero di passaggi necessari per creare i certificati nel computer di sviluppo Windows. Per informazioni su come produrre i certificati senza usare un contenitore, vedere [Generare certificati con Windows](how-to-create-transparent-gateway.md#generate-certificates-with-windows). L'articolo [Generare certificati con Linux](how-to-create-transparent-gateway.md#generate-certificates-with-linux) include una serie di istruzioni che vengono automatizzate con l'immagine Docker.

1. Accedere alla macchina virtuale di sviluppo.

2. Aprire un prompt della riga di comando ed eseguire il comando seguente per creare una directory nella VM.

    ```cmd
    mkdir c:\edgeCertificates
    ```

3. Avviare **Docker per Windows** dal menu Start di Windows.

4. Aprire Visual Studio Code.

5. Selezionare **File** > **Apri cartella** e scegliere **C:\\source\\IoTEdgeAndMlSample\\CreateCertificates**.

6. Fare clic con il pulsante destro del mouse sul Dockerfile e scegliere **Build Image** (Compila immagine).

7. Nella finestra di dialogo accettare il valore predefinito per il nome e il tag dell'immagine: **createcertificates:latest**.

8. Attendere il completamento del processo di compilazione.

    > [!NOTE]
    > È possibile che venga visualizzato un avviso relativo a una chiave pubblica mancante. Questo avviso può essere tranquillamente ignorato. Analogamente, verrà visualizzato un avviso di sicurezza che consiglia di controllare/reimpostare le autorizzazioni per l'immagine, che è possibile ignorare per questa immagine.

9. Nella finestra del terminale di Visual Studio Code e seguire il contenitore createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker chiederà l'accesso all'unità **c:\\** . Selezionare **Share it** (Condividila).

11. Specificare le credenziali quando richiesto.

12. Al termine dell'esecuzione del contenitore, cercare i file seguenti in **c:\\edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Caricare i certificati in Azure Key Vault

Per archiviare i certificati in sicurezza e renderli accessibili da più dispositivi, caricarli in Azure Key Vault. Come si può vedere nell'elenco precedente, sono disponibili due tipi di file di certificati: PFX e PEM. I file PFX verranno trattati come certificati di Key Vault da caricare in Key Vault. I file PEM sono in testo normale e verranno trattati come segreti di Key Vault. Verrà usata l'istanza di Key Vault associata all'area di lavoro del servizio Azure Machine Learning creata eseguendo [Azure Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).

1. Nel [portale di Azure](https://portal.azure.com) passare all'area di lavoro del servizio Azure Machine Learning.

2. Nella pagina di panoramica dell'area di lavoro del servizio Azure Machine Learning trovare il nome di **Key Vault**.

    ![Copiare il nome di Key Vault](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Nel computer di sviluppo caricare i certificati in Key Vault. Sostituire **\<subscriptionId\>** e **\<keyvaultname\>** con le informazioni delle risorse.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Se richiesto, accedere ad Azure.

5. Lo script viene eseguito per alcuni minuti e genera come output un elenco delle nuove voci di Key Vault.

    ![Output dello script di Key Vault](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Creare un dispositivo IoT Edge

Per connettere un dispositivo Azure IoT Edge a un hub IoT, creare prima di tutto un'identità per il dispositivo nell'hub. La stringa di connessione dell'identità del dispositivo nel cloud verrà usata per configurare il runtime nel dispositivo IoT Edge. Dopo aver configurato il dispositivo e averlo connesso all'hub, sarà possibile distribuire moduli e inviare messaggi. È anche possibile cambiare la configurazione del dispositivo IoT Edge fisico modificando la configurazione dell'identità del dispositivo corrispondente nell'hub IoT.

Per questa esercitazione, la nuova identità del dispositivo viene creata con Visual Studio Code. È anche possibile completare questi passaggi tramite il [portale di Azure](how-to-register-device-portal.md) o l'[interfaccia della riga di comando di Azure](how-to-register-device-cli.md).

1. Nel computer di sviluppo aprire Visual Studio Code.

2. Aprire il riquadro **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure) della finestra di esplorazione di Visual Studio Code.

3. Fare clic sui puntini di sospensione e selezionare **Create IoT Edge Device** (Crea dispositivo IoT Edge).

4. Assegnare un nome al dispositivo. Per praticità, viene usato **aaTurbofanEdgeDevice** in modo da visualizzarlo prima di tutti i dispositivi client creati in precedenza tramite DeviceHarness per l'invio di dati di test.

5. Il nuovo dispositivo viene visualizzato nell'elenco.

    ![Visualizzare il nuovo dispositivo aaTurbofanEdgeDevice nella finestra di esplorazione di VS Code](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Distribuire la macchina virtuale di Azure

Per creare il dispositivo IoT Edge per questa esercitazione viene usata l'immagine [Azure IoT Edge on Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) di Azure Marketplace. L'immagine Azure IoT Edge on Ubuntu installa la versione più recente del runtime Azure IoT Edge e le relative dipendenze all'avvio. La VM viene distribuita tramite uno script di PowerShell, `Create-EdgeVM.ps1`, un modello di Resource Manager, `IoTEdgeVMTemplate.json`, e uno script della shell, `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Abilitare la distribuzione a livello di codice

Per usare l'immagine del Marketplace in una distribuzione tramite script, è necessario abilitarle la distribuzione a livello di codice per l'immagine.

1. Accedere al portale di Azure.

1. Selezionare **Tutti i servizi**.

1. Nella barra di ricerca immettere e selezionare **Marketplace**.

1. Nella barra di ricerca immettere e selezionare **Azure IoT Edge on Ubuntu**.

1. Selezionare il collegamento ipertestuale **Per eseguire la distribuzione a livello di codice, fare clic qui**.

1. Selezionare il pulsante **Abilita** e quindi **Salva**.

    ![Abilitare la distribuzione a livello di codice per la VM](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Verrà visualizzata la notifica di operazione riuscita.

### <a name="create-virtual-machine"></a>Crea macchina virtuale

Eseguire quindi lo script per creare la macchina virtuale per il dispositivo IoT Edge.

1. Aprire una finestra di PowerShell e passare alla directory **EdgeVM**.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Eseguire lo script per creare la macchina virtuale.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Quando richiesto, specificare i valori per ogni parametro. Per sottoscrizione, gruppo di risorse e località, è consigliabile usare gli stessi valori specificati per tutte le risorse di questa esercitazione.

    * **ID sottoscrizione di Azure**: disponibile nel portale di Azure
    * **Nome gruppo di risorse**: un nome facile da ricordare per raggruppare le risorse per questa esercitazione
    * **Località**: un'area di Azure in cui verrà creata la macchina virtuale, ad esempio westus2 o northeurope. Per altre informazioni, vedere tutte le [aree di Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: il nome dell'account amministratore che verrà usato per accedere alla macchina virtuale
    * **AdminPassword**: la password da impostare per AdminUsername nella macchina virtuale

4. Per configurare la VM con lo script, è necessario accedere ad Azure con le credenziali associate alla sottoscrizione di Azure in uso.

5. Lo script verifica le informazioni per la creazione della VM. Selezionare **s** o **Accedi** per continuare.

6. Lo script viene eseguito per diversi minuti mentre vengono completati i passaggi seguenti:

    * Creare il gruppo di risorse se non esiste già
    * Creare la macchina virtuale
    * Aggiungere le eccezioni NSG per la VM per le porte 22 (SSH), 5671 (AMQP), 5672 (AMPQ) e 443 (SSL)
    * Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

7. L'output dello script è la stringa di connessione SSH per connettersi alla VM. Copiare la stringa di connessione per il passaggio successivo.

    ![Copiare la stringa di connessione SSH per la VM](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Stabilire la connessione al dispositivo IoT Edge

Nelle diverse sezioni successive verrà configurata la macchina virtuale di Azure creata. Il primo passaggio consiste nel connettersi alla macchina virtuale.

1. Aprire un prompt dei comandi e incollare la stringa di connessione SSH copiata dall'output dello script. Immettere le informazioni per nome utente, suffisso e area in base ai valori specificati nello script di PowerShell nella sezione precedente.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Quando viene richiesto di convalidare l'autenticità dell'host, digitare **sì** e selezionare **Accedi**.

3. Quando richiesto, specificare la password.

4. Ubuntu visualizza un messaggio di benvenuto e quindi dovrebbe essere visualizzato un prompt simile a `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Scaricare i certificati di Key Vault

In precedenza in questo articolo sono stati caricati i certificati in Key Vault per renderli disponibili per il dispositivo IoT Edge e il dispositivo foglia, ossia un dispositivo downstream che usa il dispositivo IoT Edge come gateway per comunicare con l'hub IoT. Il dispositivo foglia verrà trattato più avanti nell'esercitazione. In questa sezione scaricare i certificati nel dispositivo IoT Edge.

1. Nella sessione SSH della macchina virtuale Linux accedere ad Azure con l'interfaccia della riga di comando di Azure.

    ```bash
    az login
    ```

1. Verrà chiesto di aprire un browser all'indirizzo <https://microsoft.com/devicelogin> e specificare un codice univoco. Questi passaggi possono essere eseguiti nel computer locale. Dopo aver completato l'autenticazione, chiudere la finestra del browser.

1. Quando l'autenticazione viene completata correttamente, la VM Linux accede e visualizza l'elenco delle sottoscrizioni di Azure.

1. Impostare la sottoscrizione di Azure da usare per i comandi dell'interfaccia della riga di comando di Azure.

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. Nella VM creare una directory per i certificati.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Scaricare i certificati archiviati in Key Vault: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem e azure-iot-test-only.root.ca.cert.pem

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Aggiornare la configurazione del dispositivo IoT Edge

Il runtime IoT Edge usa il file /etc/iotedge/config.yaml per rendere persistente la configurazione. È necessario aggiornare tre informazioni in questo file:

* **Stringa di connessione del dispositivo**: la stringa di connessione ottenuta dall'identità del dispositivo nell'hub IoT
* **Certificati:** i certificati da usare per le connessioni stabilite con i dispositivi downstream
* **Nome host:** il nome di dominio completo del dispositivo VM IoT Edge.

L'immagine *Azure IoT Edge on Ubuntu* usata per creare la VM IoT Edge deriva da uno script della shell che aggiorna il file config.yaml con la stringa di connessione.

1. In Visual Studio Code fare clic con il pulsante destro del mouse sul dispositivo IoT Edge, quindi scegliere **Copy Device Connection String** (Copia stringa di connessione dispositivo).

    ![Copiare la stringa di connessione da Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Nella sessione SSH eseguire il comando per aggiornare il file config.yaml con la stringa di connessione del dispositivo.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Quindi i certificati e il nome host verranno aggiornati modificando direttamente il file config.yaml.

1. Aprire il file config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Aggiornare la sezione dei certificati del file config.yaml rimuovendo il carattere `#` iniziale e impostando il percorso in modo che il file sia come indicato nell'esempio seguente:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Assicurarsi che prima di "certificates:" non siano presenti spazi vuoti e che ogni certificato sia preceduto da due spazi.

    Fare clic con il pulsante destro del mouse su nano per incollare il contenuto degli Appunti nella posizione corrente del cursore. Usare i tasti di direzione per passare alla stringa da sostituire, eliminarla e quindi fare clic con il pulsane destro del mouse per incollare il contenuto del buffer.

3. Nel portale di Azure passare alla macchina virtuale. Copiare il nome DNS (FQDN del computer) dalla sezione **Panoramica**.

4. Incollare il nome FQDN nella sezione hostname del file config.yml. Assicurarsi che il nome sia tutto in minuscolo.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Salvare e chiudere il file (`Ctrl + X`, `Y`, `Enter`).

6. Riavviare il daemon iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Controllare lo stato del daemon IoT Edge. Dopo il comando, digitare ":q" per uscire.

    ```bash
    systemctl status iotedge
    ```

8. Se vengono visualizzati errori (testo a colori con il prefisso "\[ERROR\]") nello stato, esaminare i log del daemon per informazioni dettagliate.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Passaggi successivi

È stata appena completata la configurazione di una VM di Azure come gateway trasparente di Azure IoT Edge. Sono stati generati prima di tutto i certificati di test, che sono stati caricati in Azure Key Vault. Poi con uno script e un modello di Resource Manager la VM è stata distribuita con l'immagine "Ubuntu Server 16.04 LTS + Azure IoT Edge runtime" ottenuta da Azure Marketplace. Con un altro passaggio dello script è stata installata l'interfaccia della riga di comando di Azure ([Install Azure CLI with apt](https://docs.microsoft.com/cli/azure/install-azure-cli-apt)). Con la VM in esecuzione è stata stabilita una connessione tramite SSH, è stato eseguito l'accesso ad Azure, sono stati scaricati i certificati da Key Vault e sono stati apportati diversi aggiornamenti alla configurazione del runtime IoT Edge modificando il file config.yaml. Per altre informazioni sull'uso di IoT Edge come gateway, vedere [Come usare un dispositivo Azure IoT Edge come gateway](iot-edge-as-gateway.md). Per altre informazioni su come configurare un dispositivo IoT Edge come gateway trasparente, vedere [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md).

Continuare con l'articolo successivo per creare i moduli IoT Edge.

> [!div class="nextstepaction"]
> [Creare e distribuire moduli IoT Edge personalizzati](tutorial-machine-learning-edge-06-custom-modules.md)
