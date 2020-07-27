---
title: 'Guida introduttiva: Creare un dispositivo Azure IoT Edge in Linux | Microsoft Docs'
description: Questa guida introduttiva descrive come creare un dispositivo IoT Edge e distribuire codice predefinito in modalità remota dal portale di Azure.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/30/2020
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: fada393dcab61cf00a0a3befcf4af3660bf625dc
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536522"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Avvio rapido: Distribuire il primo modulo IoT Edge in un dispositivo Linux virtuale

Per testare Azure IoT Edge in questa guida di avvio rapido, distribuire il codice in contenitori in un dispositivo IoT Edge Linux virtuale. IoT Edge consente di gestire in remoto il codice nei dispositivi, in modo da poter inviare più carichi di lavoro ai dispositivi perimetrali. Per questa guida di avvio rapido, come dispositivo IoT Edge è consigliabile usare una macchina virtuale di Azure che consenta di creare rapidamente un computer di test con il servizio IoT Edge installato e quindi eliminarlo al termine dell'operazione.

In questa guida introduttiva si apprende come:

1. Creare un hub IoT.
2. Registrare un dispositivo IoT Edge nell'hub IoT.
3. Installare e avviare il runtime IoT Edge nel dispositivo virtuale.
4. Distribuire in remoto un modulo in un dispositivo IoT Edge.

![Diagramma - Guida introduttiva sull'architettura per dispositivo e cloud](./media/quickstart-linux/install-edge-full.png)

Questa guida di avvio rapido illustra come creare una macchina virtuale Linux configurata per l'uso come dispositivo IoT Edge. Verrà quindi distribuito un modulo dal portale di Azure nel dispositivo. Il modulo distribuito in questa guida di avvio rapido è un sensore simulato che genera dati di temperatura, umidità e pressione. Le altre esercitazioni su Azure IoT Edge si basano sulle procedure eseguite qui tramite la distribuzione di moduli aggiuntivi che analizzano dati simulati per ottenere informazioni aziendali dettagliate.

Se non si ha una sottoscrizione di Azure attiva, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Usare l'interfaccia della riga di comando di Azure per completare molti dei passaggi di questa guida introduttiva. Azure IoT include un'estensione per abilitare funzionalità aggiuntive.

Aggiungere l'estensione Azure IoT all'istanza di Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Prerequisiti

Risorse cloud:

* Un gruppo di risorse per la gestione di tutte le risorse usate in questa guida introduttiva. In questa guida di avvio rapido e nelle esercitazioni successive verrà usato il nome del gruppo di risorse di esempio **IoTEdgeResources**.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Per iniziare, creare un hub IoT con l'interfaccia della riga di comando di Azure.

![Diagramma - Creare un hub IoT nel cloud](./media/quickstart-linux/create-iot-hub.png)

Per questa guida introduttiva è possibile usare il livello gratuito dell'hub IoT. Se l'hub IoT è già stato usato in passato ed è disponibile un hub, è possibile usarlo qui.

Il codice seguente crea un hub **F1** gratuito nel gruppo di risorse **IoTEdgeResources**. Sostituire `{hub_name}` con un nome univoco per l'hub IoT. La creazione di un hub IoT potrebbe richiedere alcuni minuti.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Se si verifica un errore perché è già presente un hub gratuito nella sottoscrizione, modificare lo SKU in **S1**. Ogni sottoscrizione può avere un solo hub IoT gratuito. Se si verifica un errore che indica che il nome dell'hub IoT non è disponibile, significa che qualcuno ha già un hub con lo stesso nome. Provare con un nuovo nome.

## <a name="register-an-iot-edge-device"></a>Registrare un dispositivo IoT Edge

Registrare un dispositivo IoT Edge con l'hub IoT appena creato.

![Diagramma - Registrare un dispositivo con un'identità dell'hub IoT](./media/quickstart-linux/register-device.png)

Creare un'identità del dispositivo IoT Edge affinché il dispositivo possa comunicare con l'hub IoT. L'identità del dispositivo si trova nel cloud e si usa una stringa di connessione del dispositivo univoca per associare un dispositivo fisico a un'identità del dispositivo.

Poiché i dispositivi IoT Edge si comportano e possono essere gestiti in modo diverso rispetto ai dispositivi IoT tipici, indicare con un flag `--edge-enabled` che l'identità si riferisce a un dispositivo IoT Edge.

1. In Azure Cloud Shell immettere il comando seguente per creare un dispositivo denominato **myEdgeDevice** nell'hub.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Se viene visualizzato un errore relativo alle chiavi dei criteri iothubowner, assicurarsi che Cloud Shell esegua la versione più recente dell'estensione azure-iot.

2. Visualizzare la stringa di connessione per il dispositivo, che collega il dispositivo fisico alla sua identità nell'hub IoT. Contiene il nome dell'hub IoT, il nome del dispositivo e quindi una chiave condivisa che autentica le connessioni tra i due. Si farà riferimento a questa stringa di connessione nella sezione successiva quando verrà configurato il dispositivo IoT Edge.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

   ![Visualizzare la stringa di connessione dall'output dell'interfaccia della riga di comando](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Configurare il dispositivo IoT Edge

Creare una macchina virtuale con il runtime Azure IoT Edge.

![Diagramma - Avviare il runtime nel dispositivo](./media/quickstart-linux/start-runtime.png)

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. È costituito da tre componenti. Il *daemon di sicurezza IoT Edge* si avvia a ogni avvio di un dispositivo IoT Edge ed esegue il bootstrap del dispositivo avviando l'agente IoT Edge. L'*agente IoT Edge* semplifica la distribuzione e il monitoraggio di moduli nel dispositivo IoT Edge, tra cui l'hub di IoT Edge. L'*hub IoT Edge* gestisce le comunicazioni tra moduli nel dispositivo IoT Edge e tra il dispositivo e l'hub IoT.

Durante la configurazione del runtime, si immette una stringa di connessione del dispositivo. Si tratta della stringa recuperata tramite l'interfaccia della riga di comando di Azure. La stringa associa il dispositivo fisico all'identità del dispositivo IoT Edge in Azure.

### <a name="deploy-the-iot-edge-device"></a>Distribuire il dispositivo IoT Edge

In questa sezione verrà usato un modello di Azure Resource Manager per creare una nuova macchina virtuale e installare il runtime IoT Edge al suo interno. Se invece si vuole usare il proprio dispositivo Linux, è possibile seguire la procedura di installazione in [Installare il runtime Azure IoT Edge in Linux](how-to-install-iot-edge-linux.md), quindi tornare a questa guida di avvio rapido.

Usare il comando dell'interfaccia della riga di comando seguente per creare il dispositivo IoT Edge in base al modello [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) predefinito.

* Per gli utenti di Bash o Cloud Shell, copiare il comando seguente in un editor di testo, sostituire il testo segnaposto con le informazioni correnti e quindi copiare il testo nella finestra di Bash o Cloud Shell:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* Per gli utenti di PowerShell, copiare il comando seguente nella finestra di PowerShell, quindi sostituire il testo segnaposto con le informazioni correnti:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://aka.ms/iotedge-vm-deploy" `
   --parameters dnsLabelPrefix='my-edge-vm1' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

Questo modello accetta i parametri seguenti:

| Parametro | Descrizione |
| --------- | ----------- |
| **resource-group** | Gruppo di risorse in cui verranno create le risorse. Usare il gruppo di risorse **IoTEdgeResources** predefinito usato in questo articolo o fornire il nome di un gruppo di risorse esistente nella sottoscrizione in uso. |
| **template-uri** | Puntatore al modello di Resource Manager in uso. |
| **dnsLabelPrefix** | Stringa che verrà usata per creare il nome host della macchina virtuale. Usare la stringa **my-edge-vm** di esempio o specificare una nuova stringa. |
| **adminUsername** | Nome utente per l'account amministratore della macchina virtuale. Usare il nome utente **azureUser** di esempio o specificare un nuovo nome utente. |
| **deviceConnectionString** | Stringa di connessione dell'identità del dispositivo nell'hub IoT, che verrà usata per configurare il runtime IoT Edge nella macchina virtuale. Il comando dell'interfaccia della riga di comando all'interno di questo parametro acquisisce automaticamente la stringa di connessione. Sostituire il testo segnaposto con il nome dell'hub IoT. |
| **authenticationType** | Metodo di autenticazione per l'account amministratore. In questa guida di avvio rapido viene usata l'autenticazione della **password**, ma è anche possibile impostare questo parametro su **sshPublicKey**. |
| **adminPasswordOrKey** | Password o valore della chiave pubblica SSH per l'account amministratore. Sostituire il testo segnaposto con una password sicura. La password deve avere una lunghezza minima di 12 caratteri e contenere tre dei quattro elementi seguenti: caratteri minuscoli, caratteri maiuscoli, numeri e caratteri speciali. |

Al termine della distribuzione, nell'interfaccia della riga di comando verrà restituito l'output in formato JSON che contiene le informazioni SSH per connettersi alla macchina virtuale. Copiare il valore della voce **public SSH** della sezione **outputs**:

   ![Recuperare il valore della voce public SSH dall'output](./media/quickstart-linux/outputs-public-ssh.png)

### <a name="view-the-iot-edge-runtime-status"></a>Visualizzare lo stato del runtime IoT Edge

I restanti comandi di questa guida introduttiva vengono eseguiti nel dispositivo IoT Edge stesso ed è pertanto possibile verificare le operazioni effettuate nel dispositivo. Se si usa una macchina virtuale, connettersi ad essa usando il nome utente amministratore configurato e il nome DNS restituito dal comando di distribuzione. È anche possibile trovare il nome DNS nella pagina Panoramica della macchina virtuale nel portale di Azure. Usare il comando seguente per connettersi alla macchina virtuale. Sostituire `{admin username}` e `{DNS name}` con valori personalizzati.

   ```console
   ssh {admin username}@{DNS name}
   ```

Una volta stabilita la connessione alla macchina virtuale, verificare che il runtime sia stato installato e configurato correttamente nel dispositivo IoT Edge.

1. Verificare che il daemon di sicurezza IoT Edge sia in esecuzione come servizio di sistema.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Osservare il daemon di sicurezza IoT Edge come servizio di sistema](./media/quickstart-linux/iotedged-running.png)

   >[!TIP]
   >Per eseguire comandi `iotedge` sono necessari privilegi elevati. Quando ci si disconnette dal computer e si accede di nuovo per la prima volta dopo l'installazione del runtime IoT Edge, le autorizzazioni vengono aggiornate automaticamente. Fino ad allora, usare `sudo` davanti ai comandi.

2. Se è necessario risolvere problemi del servizio, recuperare i log di servizio.

   ```bash
   journalctl -u iotedge
   ```

3. Visualizzare tutti i moduli in esecuzione nel dispositivo IoT Edge. Poiché il servizio è stato avviato per la prima volta, verrà visualizzato solo il modulo **edgeAgent** in esecuzione. Il modulo edgeAgent viene eseguito per impostazione predefinita e permette di installare e avviare tutti i moduli aggiuntivi distribuiti nel dispositivo.

   ```bash
   sudo iotedge list
   ```

   ![Visualizzare un modulo nel dispositivo](./media/quickstart-linux/iotedge-list-1.png)

Il dispositivo IoT Edge è ora configurato. È pronto per eseguire i moduli distribuiti nel cloud.

## <a name="deploy-a-module"></a>Distribuire un modulo

Gestire il dispositivo Azure IoT Edge dal cloud per distribuire un modulo che invierà dati di telemetria all'hub IoT.

![Diagramma - Distribuire un modulo dal cloud al dispositivo](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visualizzare i dati generati

In questa guida introduttiva è stato creato un nuovo dispositivo IoT Edge, nel quale è stato installato il runtime di IoT Edge. È stato quindi usato il portale di Azure per distribuire un modulo di IoT Edge da eseguire nel dispositivo senza dovere apportare modifiche al dispositivo stesso.

In questo caso il modulo di cui è stato eseguito il push crea i dati dell'ambiente di esempio che potranno essere usati successivamente per il test. Il sensore simulato monitora un macchinario e l'ambiente intorno al macchinario. Questo sensore può trovarsi ad esempio in una stanza di server, in un ambiente di produzione o in una turbina eolica. Il messaggio include la temperatura e l'umidità dell'ambiente, la temperatura e la pressione dell'apparecchiatura e un timestamp. Le esercitazioni di IoT Edge usano i dati creati da questo modulo come dati di test per le analisi.

Aprire di nuovo il prompt dei comandi nel dispositivo IoT Edge o usare la connessione SSH dall'interfaccia della riga di comando di Azure. Verificare che il modulo distribuito dal cloud sia in esecuzione nel dispositivo IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Visualizzare tre moduli nel dispositivo](./media/quickstart-linux/iotedge-list-2.png)

Visualizzare i messaggi inviati dal modulo del sensore temperatura:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Quando viene fatto riferimento a nomi di moduli, i comandi di IoT Edge fanno distinzione tra maiuscole e minuscole.

   ![Visualizzare i dati dal modulo](./media/quickstart-linux/iotedge-logs.png)

È anche possibile visualizzare i messaggi ricevuti dall'hub IoT usando l'[estensione Azure IoT Hub per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole continuare con le esercitazioni su IoT Edge, è possibile usare il dispositivo registrato e configurato in questa guida introduttiva. In caso contrario, è possibile eliminare le risorse di Azure create per evitare addebiti.

Se la macchina virtuale e l'hub IoT sono stati creati in un nuovo gruppo di risorse, è possibile eliminare il gruppo e tutte le risorse associate. Verificare il contenuto del gruppo di risorse per assicurarsi che non vi siano dati da conservare. Se non si vuole eliminare l'intero gruppo, è possibile eliminare le singole risorse.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile.

Rimuovere il gruppo **IoTEdgeResources**. L'eliminazione di un gruppo di risorse potrebbe richiedere alcuni minuti.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Per confermare la rimozione del gruppo di risorse, visualizzare l'elenco dei gruppi di risorse.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un dispositivo IoT Edge ed è stata usata l'interfaccia cloud di Azure IoT Edge per distribuire il codice nel dispositivo. A questo punto, è disponibile un dispositivo di test che genera dati non elaborati relativi al proprio ambiente.

Il passaggio successivo consiste nell'impostare l'ambiente di sviluppo locale in modo che sia possibile avviare la creazione di moduli IoT Edge che eseguano la propria logica di business.

> [!div class="nextstepaction"]
> [Sviluppare moduli IoT Edge per i dispositivi Linux](tutorial-develop-for-linux.md)
