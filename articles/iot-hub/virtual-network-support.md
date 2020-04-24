---
title: Supporto dell'hub Internet degli Azure per le reti virtuali
description: Come usare il modello di connettività delle reti virtuali con l'hub Internet
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 34f66c13b0e7eb7092332a48744f9abfd8f0db80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501427"
---
# <a name="iot-hub-support-for-virtual-networks"></a>Supporto dell'hub Internet per reti virtuali

Questo articolo presenta il modello di connettività di VNET e spiega come configurare un'esperienza di connettività privata a un hub Internet tramite un VNET di Azure di proprietà del cliente.

> [!NOTE]
> Le funzionalità dell'hub Internet delle cose descritte in questo articolo sono attualmente disponibili per gli hub di Internet delle cose [creati con l'identità del servizio gestito](#create-an-iot-hub-with-managed-service-identity) nelle aree seguenti: Stati Uniti orientali, Stati Uniti centro-meridionali e Stati Uniti occidentali 2.


## <a name="introduction"></a>Introduzione

Per impostazione predefinita, il nome host dell'hub Internet viene mappato a un endpoint pubblico con un indirizzo IP instradabile pubblicamente tramite Internet. Come illustrato nell'immagine seguente, questo endpoint pubblico dell'hub Internet viene condiviso tra gli hub di proprietà di clienti diversi ed è possibile accedervi tramite i dispositivi Internet, oltre alle reti locali.

Diverse funzionalità dell'hub Internet, tra cui [routing dei messaggi](./iot-hub-devguide-messages-d2c.md), [caricamento di file](./iot-hub-devguide-file-upload.md)e [importazione/esportazione di dispositivi in blocco](./iot-hub-bulk-identity-mgmt.md) , richiedono la connettività dall'hub Internet alle risorse di Azure di proprietà del cliente tramite il relativo endpoint pubblico. Come illustrato di seguito, questi percorsi di connettività costituiscono collettivamente il traffico in uscita dall'hub delle cose alle risorse dei clienti.
![Endpoint pubblico dell'hub Internet](./media/virtual-network-support/public-endpoint.png)


Per diversi motivi, i clienti potrebbero voler limitare la connettività alle risorse di Azure (incluso l'hub Internet) tramite un VNET di cui si è proprietari e che operano. Di seguito ne sono elencati alcuni:

* Introducendo livelli aggiuntivi di sicurezza tramite l'isolamento a livello di rete per l'hub Internet delle cose, impedendo l'esposizione della connettività all'hub sulla rete Internet pubblica.

* L'abilitazione di un'esperienza di connettività privata dalle risorse della rete locale, assicurando che i dati e il traffico vengano trasmessi direttamente alla rete backbone di Azure.

* Prevenzione degli attacchi exfiltration da reti locali riservate. 

* Seguenti modelli di connettività a livello di Azure creati usando [endpoint privati](../private-link/private-endpoint-overview.md).


Questo articolo descrive come raggiungere questi obiettivi usando [endpoint privati](../private-link/private-endpoint-overview.md) per la connettività in ingresso all'hub Internet, come l'uso di un'eccezione dei servizi di terze parti attendibile di Azure per la connettività in uscita dall'hub Internet alle altre risorse di Azure.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Connettività in ingresso all'hub Internet tramite endpoint privati

Un endpoint privato è un indirizzo IP privato allocato all'interno di un VNET di proprietà del cliente tramite il quale è raggiungibile una risorsa di Azure. Se si dispone di un endpoint privato per l'hub Internet delle cose, sarà possibile consentire ai servizi che operano all'interno della VNET di raggiungere l'hub Internet senza richiedere l'invio del traffico all'endpoint pubblico dell'hub. Analogamente, i dispositivi che operano in locale possono usare la [rete privata virtuale (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) o il peering privato di [ExpressRoute](https://azure.microsoft.com/services/expressroute/) per ottenere la connettività ai VNET in Azure e successivamente all'hub Internet (tramite il relativo endpoint privato). Di conseguenza, i clienti che desiderano limitare la connettività agli endpoint pubblici dell'hub Internet (o eventualmente bloccarli completamente) possono raggiungere questo obiettivo usando le regole del [firewall dell'hub](./iot-hub-ip-filtering.md) Internet e mantenere la connettività all'hub usando l'endpoint privato.

> [!NOTE]
> L'obiettivo principale di questa configurazione è per i dispositivi all'interno di una rete locale. Questa configurazione non è consigliata per i dispositivi distribuiti in una rete WAN.

![Endpoint pubblico dell'hub Internet](./media/virtual-network-support/virtual-network-ingress.png)

Prima di procedere, assicurarsi che siano soddisfatti i seguenti prerequisiti:

* È necessario eseguire il provisioning dell'hub Internet delle cose con l' [identità del servizio gestito](#create-an-iot-hub-with-managed-service-identity).

* È necessario eseguire il provisioning dell'hub Internet delle cose in una delle [aree supportate](#regional-availability-private-endpoints).

* È stato effettuato il provisioning di una VNET di Azure con una subnet in cui verrà creato l'endpoint privato. Per altri dettagli, vedere [creare una rete virtuale usando l'interfaccia](../virtual-network/quick-create-cli.md) della riga di comando di Azure.

* Per i dispositivi che operano all'interno di reti locali, configurare la [rete privata virtuale (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) o il peering privato di [EXPRESSROUTE](https://azure.microsoft.com/services/expressroute/) in Azure vnet.


### <a name="regional-availability-private-endpoints"></a>Disponibilità a livello di area (endpoint privati)

Gli endpoint privati supportati nell'hub di Internet delle cose sono creati nelle aree seguenti:

* Stati Uniti orientali

* Stati Uniti centro-meridionali

* Stati Uniti occidentali 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Configurare un endpoint privato per il traffico in ingresso dell'hub

Per configurare un endpoint privato, attenersi alla procedura seguente:

1. Eseguire il comando dell'interfaccia della riga di comando di Azure seguente per registrare nuovamente il provider dell'hub Azure Internet con la sottoscrizione:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Passare alla scheda **connessioni endpoint privato** nel portale dell'hub di Internet delle cose. questa scheda è disponibile solo per gli hub Internet delle [aree geografiche supportate](#regional-availability-private-endpoints), quindi **+** fare clic sul segno per aggiungere un nuovo endpoint privato.

3. Fornire la sottoscrizione, il gruppo di risorse, il nome e l'area in cui creare il nuovo endpoint privato (idealmente, l'endpoint privato deve essere creato nella stessa area dell'hub. per ulteriori informazioni, vedere la [sezione relativa alla disponibilità a livello](#regional-availability-private-endpoints) di area).

4. Fare clic su **Avanti: risorsa**e fornire la sottoscrizione per la risorsa hub Internet e selezionare **"Microsoft. Devices/IotHubs"** come tipo di risorsa, il nome dell'hub Internet come **risorsa**e **iotHub** come risorsa secondaria di destinazione.

5. Fare clic su **Avanti: configurazione** e fornire la rete virtuale e la subnet per creare l'endpoint privato in. Se necessario, selezionare l'opzione per l'integrazione con la zona DNS privata di Azure.

6. Fare clic su **Avanti: Tag**e, facoltativamente, specificare eventuali tag per la risorsa.

7. Fare clic su **Verifica + crea** per creare la risorsa dell'endpoint privato.


### <a name="pricing-private-endpoints"></a>Prezzi (endpoint privati)

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link).


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Connettività in uscita dall'hub Internet ad altre risorse di Azure

L'hub Internet delle cose necessita dell'accesso all'archiviazione BLOB di Azure, agli hub eventi, alle risorse del bus di servizio per il [routing dei messaggi](./iot-hub-devguide-messages-d2c.md), al [caricamento di file](./iot-hub-devguide-file-upload.md)e all' [importazione/esportazione di dispositivi in blocco](./iot-hub-bulk-identity-mgmt.md), che in genere si verifica sull'endpoint pubblico delle risorse. Nel caso in cui l'account di archiviazione, Hub eventi o risorsa del bus di servizio venga associato a una VNET, la configurazione consigliata bloccherà la connettività alla risorsa per impostazione predefinita. Di conseguenza, ciò impedirà la funzionalità dell'hub delle cose che richiede l'accesso a tali risorse.

Per risolvere questo problema, è necessario abilitare la connettività dalla risorsa dell'hub Internet all'account di archiviazione, agli hub eventi o alle risorse del bus di servizio tramite l'opzione **Servizi attendibili per la prima entità di Azure** .

I prerequisiti sono i seguenti:

* È necessario eseguire il provisioning dell'hub Internet delle cose in una delle [aree supportate](#regional-availability-trusted-microsoft-first-party-services).

* All'hub Internet delle cose deve essere assegnata un'identità del servizio gestito al momento del provisioning dell'hub. Seguire le istruzioni per [creare un hub con identità del servizio gestito](#create-an-iot-hub-with-managed-service-identity).


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Disponibilità a livello di area (servizi Microsoft prima entità attendibili)

Un'eccezione dei servizi di terze parti attendibile di Azure per ignorare le restrizioni del firewall per archiviazione di Azure, Hub eventi e risorse del bus di servizio è supportata solo per gli hub Internet nelle aree seguenti:

* Stati Uniti orientali

* Stati Uniti centro-meridionali

* Stati Uniti occidentali 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>Prezzi (servizi Microsoft prima entità attendibili)

La funzionalità di eccezione Microsoft per i servizi di terze parti attendibile è gratuita negli hub di Internet delle cose nelle [aree supportate](#regional-availability-trusted-microsoft-first-party-services). Gli addebiti per gli account di archiviazione con provisioning, gli hub eventi o le risorse del bus di servizio vengono applicati separatamente.


### <a name="create-an-iot-hub-with-managed-service-identity"></a>Creare un hub Internet delle cose con l'identità del servizio gestito

Un'identità del servizio gestito può essere assegnata all'hub al momento del provisioning delle risorse (questa funzionalità non è attualmente supportata per gli Hub esistenti), che richiede l'uso di TLS 1,2 come versione minima. A questo scopo, è necessario usare il modello di risorse ARM seguente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

Dopo aver sostituito i valori per la risorsa `name`, `location` `SKU.name` e `SKU.tier`, è possibile usare l'interfaccia della riga di comando di Azure per distribuire la risorsa in un gruppo di risorse esistente usando:

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Dopo aver creato la risorsa, è possibile recuperare l'identità del servizio gestito assegnata all'hub usando l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

Quando viene eseguito il provisioning dell'hub Internet con un'identità del servizio gestito, seguire la sezione corrispondente per configurare gli endpoint di routing per gli [account di archiviazione](#egress-connectivity-to-storage-account-endpoints-for-routing), gli [Hub eventi](#egress-connectivity-to-event-hubs-endpoints-for-routing)e le risorse del [bus di servizio](#egress-connectivity-to-service-bus-endpoints-for-routing) oppure per configurare il caricamento dei [file](#egress-connectivity-to-storage-accounts-for-file-upload) e l' [importazione/esportazione di dispositivi in blocco](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport).


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Connettività in uscita agli endpoint dell'account di archiviazione per il routing

È possibile configurare l'hub Internet per indirizzare i messaggi a un account di archiviazione di proprietà del cliente. Per consentire alla funzionalità di routing di accedere a un account di archiviazione mentre sono presenti restrizioni del firewall, l'hub Internet delle cose deve avere un'identità del servizio gestito (vedere come [creare un hub con identità del servizio gestito](#create-an-iot-hub-with-managed-service-identity)). Una volta eseguito il provisioning di un'identità del servizio gestito, attenersi alla procedura seguente per assegnare l'autorizzazione RBAC all'identità della risorsa dell'hub per accedere all'account di archiviazione.

1. Nella portale di Azure passare alla scheda controllo di accesso dell'account di archiviazione **(IAM)** e fare clic su **Aggiungi** nella sezione **Aggiungi un'assegnazione di ruolo** .

2. Selezionare **collaboratore dati BLOB di archiviazione** come **ruolo**, **Azure ad utente, gruppo o entità servizio** per l' **assegnazione dell'accesso a** e selezionare il nome della risorsa dell'hub cose nell'elenco a discesa. Fare clic sul pulsante **Salva** .

3. Passare alla scheda **firewall e reti virtuali** nell'account di archiviazione e abilitare l'opzione **Consenti accesso dalle reti selezionate** . Nell'elenco **eccezioni** selezionare la casella **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione**. Fare clic sul pulsante **Salva** .

4. Nella pagina delle risorse dell'hub Internet, passare alla scheda **routing messaggi** .

5. Passare alla sezione **endpoint personalizzati** e fare clic su **Aggiungi**. Selezionare **archiviazione** come tipo di endpoint.

6. Nella pagina visualizzata specificare un nome per l'endpoint, selezionare il contenitore che si intende usare nell'archivio BLOB, fornire la codifica e il formato del nome file. Selezionare **sistema assegnato** come **tipo di autenticazione** all'endpoint di archiviazione. Fare clic sul pulsante **Create** (Crea).

A questo punto, l'endpoint di archiviazione personalizzato è configurato per usare l'identità assegnata dal sistema dell'hub e dispone dell'autorizzazione per accedere alla risorsa di archiviazione nonostante le restrizioni del firewall. È ora possibile usare questo endpoint per configurare una regola di routing.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Connettività in uscita agli endpoint di hub eventi per il routing

È possibile configurare l'hub Internet per indirizzare i messaggi a uno spazio dei nomi di hub eventi di proprietà del cliente. Per consentire alla funzionalità di routing di accedere a una risorsa di hub eventi mentre sono presenti restrizioni del firewall, l'hub Internet delle cose deve avere un'identità del servizio gestito (vedere come [creare un hub con identità del servizio gestito](#create-an-iot-hub-with-managed-service-identity)). Una volta eseguito il provisioning di un'identità del servizio gestito, attenersi alla procedura seguente per assegnare l'autorizzazione RBAC all'identità della risorsa dell'hub per accedere a hub eventi.

1. Nella portale di Azure passare alla scheda **controllo di accesso** di hub eventi e fare clic su **Aggiungi** nella sezione **Aggiungi un'assegnazione di ruolo** .

2. Selezionare **mittente dati Hub eventi** come **ruolo**, **Azure ad utente, gruppo o entità servizio** per l' **assegnazione dell'accesso a** e selezionare il nome della risorsa dell'hub tutto nell'elenco a discesa. Fare clic sul pulsante **Salva** .

3. Passare alla scheda **firewall e reti virtuali** nell'hub eventi e abilitare l'opzione **Consenti accesso dalle reti selezionate** . Nell'elenco **eccezioni** selezionare la casella **Consenti ai servizi Microsoft attendibili di accedere a hub eventi**. Fare clic sul pulsante **Salva** .

4. Nella pagina delle risorse dell'hub Internet, passare alla scheda **routing messaggi** .

5. Passare alla sezione **endpoint personalizzati** e fare clic su **Aggiungi**. Selezionare **Hub eventi** come tipo di endpoint.

6. Nella pagina visualizzata specificare un nome per l'endpoint, selezionare lo spazio dei nomi dell'hub eventi e l'istanza e fare clic sul pulsante **Crea** .

A questo punto l'endpoint dell'hub eventi personalizzato è configurato per usare l'identità assegnata dal sistema dell'hub e dispone dell'autorizzazione per accedere alla risorsa di hub eventi nonostante le restrizioni del firewall. È ora possibile usare questo endpoint per configurare una regola di routing.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Connettività in uscita agli endpoint del bus di servizio per il routing

È possibile configurare l'hub Internet per indirizzare i messaggi a uno spazio dei nomi del bus di servizio di proprietà del cliente. Per consentire alla funzionalità di routing di accedere a una risorsa del bus di servizio mentre sono presenti restrizioni del firewall, l'hub Internet delle cose deve avere un'identità del servizio gestito (vedere come [creare un hub con identità del servizio gestito](#create-an-iot-hub-with-managed-service-identity)). Una volta eseguito il provisioning di un'identità del servizio gestito, attenersi alla procedura seguente per assegnare l'autorizzazione RBAC all'identità della risorsa dell'hub per accedere al bus di servizio.

1. Nella portale di Azure passare alla scheda **controllo di accesso (IAM)** del bus di servizio e fare clic su **Aggiungi** nella sezione **Aggiungi un'assegnazione di ruolo** .

2. Selezionare **mittente dati del bus di servizio** come **ruolo**, **Azure ad utente, gruppo o entità servizio** per l' **assegnazione dell'accesso a** e selezionare il nome della risorsa dell'hub cose nell'elenco a discesa. Fare clic sul pulsante **Salva** .

3. Passare alla scheda **firewall e reti virtuali** nel bus di servizio e abilitare l'opzione **Consenti accesso dalle reti selezionate** . Nell'elenco **eccezioni** selezionare la casella **Consenti ai servizi Microsoft attendibili di accedere a questo bus di servizio**. Fare clic sul pulsante **Salva** .

4. Nella pagina delle risorse dell'hub Internet, passare alla scheda **routing messaggi** .

5. Passare alla sezione **endpoint personalizzati** e fare clic su **Aggiungi**. Selezionare la coda o l' **argomento** del bus di servizio (come applicabile) del **bus di servizio** come tipo di endpoint.

6. Nella pagina visualizzata specificare un nome per l'endpoint, selezionare lo spazio dei nomi e la coda o l'argomento del bus di servizio, come applicabile. Fare clic sul pulsante **Create** (Crea).

A questo punto l'endpoint del bus di servizio personalizzato è configurato per usare l'identità assegnata dal sistema dell'hub e dispone dell'autorizzazione per accedere alla risorsa del bus di servizio nonostante le restrizioni del firewall. È ora possibile usare questo endpoint per configurare una regola di routing.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Connettività in uscita agli account di archiviazione per il caricamento di file

La funzionalità di caricamento dei file dell'hub Internet consente ai dispositivi di caricare file in un account di archiviazione di proprietà del cliente. Per consentire il funzionamento del caricamento del file, sia i dispositivi che l'hub Internet devono avere la connettività all'account di archiviazione. Se sono presenti restrizioni del firewall nell'account di archiviazione, i dispositivi devono usare uno dei meccanismi dell'account di archiviazione supportati (inclusi [endpoint privati](../private-link/create-private-endpoint-storage-portal.md), endpoint di [servizio](../virtual-network/virtual-network-service-endpoints-overview.md) o [configurazione firewall diretta](../storage/common/storage-network-security.md)) per ottenere la connettività. Analogamente, se sono presenti restrizioni del firewall nell'account di archiviazione, è necessario configurare l'hub Internet per accedere alla risorsa di archiviazione tramite l'eccezione dei servizi Microsoft attendibili. A questo scopo, l'hub Internet delle cose deve avere un'identità del servizio gestito (vedere come [creare un hub con l'identità del servizio gestito](#create-an-iot-hub-with-managed-service-identity)). Una volta eseguito il provisioning di un'identità del servizio gestito, attenersi alla procedura seguente per assegnare l'autorizzazione RBAC all'identità della risorsa dell'hub per accedere all'account di archiviazione.

1. Nella portale di Azure passare alla scheda controllo di accesso dell'account di archiviazione **(IAM)** e fare clic su **Aggiungi** nella sezione **Aggiungi un'assegnazione di ruolo** .

2. Selezionare **collaboratore dati BLOB di archiviazione** come **ruolo**, **Azure ad utente, gruppo o entità servizio** per l' **assegnazione dell'accesso a** e selezionare il nome della risorsa dell'hub cose nell'elenco a discesa. Fare clic sul pulsante **Salva** .

3. Passare alla scheda **firewall e reti virtuali** nell'account di archiviazione e abilitare l'opzione **Consenti accesso dalle reti selezionate** . Nell'elenco **eccezioni** selezionare la casella **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione**. Fare clic sul pulsante **Salva** .

4. Nella pagina delle risorse dell'hub Internet, passare alla scheda **caricamento file** .

5. Nella pagina visualizzata selezionare il contenitore che si intende usare nell'archivio BLOB, configurare le **impostazioni di notifica dei file**, la durata ( **TTL**) della firma di accesso condiviso, il **valore TTL predefinito** e il **numero massimo** di recapiti desiderato. Selezionare **sistema assegnato** come **tipo di autenticazione** all'endpoint di archiviazione. Fare clic sul pulsante **Create** (Crea).

Ora l'endpoint di archiviazione per il caricamento di file è configurato per usare l'identità assegnata dal sistema dell'hub e dispone dell'autorizzazione per accedere alla risorsa di archiviazione nonostante le restrizioni del firewall.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Connettività in uscita agli account di archiviazione per l'importazione/esportazione bulk di dispositivi

L'hub Internet delle cose supporta la funzionalità per [importare/esportare](./iot-hub-bulk-identity-mgmt.md) le informazioni dei dispositivi in blocco da/in un BLOB di archiviazione fornito dal cliente. Per consentire il funzionamento della funzionalità di importazione/esportazione bulk, sia i dispositivi che l'hub Internet devono disporre della connettività all'account di archiviazione.

Questa funzionalità richiede la connettività dall'hub Internet all'account di archiviazione. Per accedere a una risorsa del bus di servizio mentre sono presenti restrizioni del firewall, l'hub Internet delle cose deve avere un'identità del servizio gestito (vedere come [creare un hub con identità del servizio gestito](#create-an-iot-hub-with-managed-service-identity)). Una volta eseguito il provisioning di un'identità del servizio gestito, attenersi alla procedura seguente per assegnare l'autorizzazione RBAC all'identità della risorsa dell'hub per accedere al bus di servizio.

1. Nella portale di Azure passare alla scheda controllo di accesso dell'account di archiviazione **(IAM)** e fare clic su **Aggiungi** nella sezione **Aggiungi un'assegnazione di ruolo** .

2. Selezionare **collaboratore dati BLOB di archiviazione** come **ruolo**, **Azure ad utente, gruppo o entità servizio** per l' **assegnazione dell'accesso a** e selezionare il nome della risorsa dell'hub cose nell'elenco a discesa. Fare clic sul pulsante **Salva** .

3. Passare alla scheda **firewall e reti virtuali** nell'account di archiviazione e abilitare l'opzione **Consenti accesso dalle reti selezionate** . Nell'elenco **eccezioni** selezionare la casella **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione**. Fare clic sul pulsante **Salva** .

È ora possibile usare le API REST di Azure per la [creazione di processi di importazione/esportazione](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) per informazioni su come usare la funzionalità di importazione/esportazione bulk. Si noti che sarà necessario specificare `storageAuthenticationType="identityBased"` nel corpo della richiesta e usare `inputBlobContainerUri="https://..."` e `outputBlobContainerUri="https://..."` come URL di input e output dell'account di archiviazione, rispettivamente.


L'SDK dell'hub di Azure è supportato anche da questa funzionalità nel gestore del registro di sistema del client del servizio. Nel frammento di codice seguente viene illustrato come avviare un processo di importazione o di esportazione in utilizzando C# SDK.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```


Per usare questa versione limitata dell'area degli SDK di Azure per la rete virtuale con supporto per la rete virtuale per C#, Java e node. js:

1. Creare una variabile di ambiente `EnableStorageIdentity` denominata e impostarne il `1`valore su.

2. Scaricare l'SDK: [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [node. js](https://aka.ms/vnetnodesdk)
 
Per Python, scaricare la versione limitata da GitHub.

1. Passare alla [pagina della versione GitHub](https://aka.ms/vnetpythonsdk).

2. Scaricare il seguente file, disponibile nella parte inferiore della pagina di rilascio sotto l'intestazione denominata **assets**.
    > *azure_iot_hub-2.2.0_limited-PY2. PY3-None-any. WHL*

3. Aprire un terminale e passare alla cartella con il file scaricato.

4. Eseguire il comando seguente per installare Python Service SDK con supporto per le reti virtuali:
    > pip install./azure_iot_hub-2.2.0_limited-PY2. PY3-None-any. WHL


## <a name="next-steps"></a>Passaggi successivi

Usare i collegamenti seguenti per altre informazioni sulle funzionalità dell'hub Internet:

* [Routing dei messaggi](./iot-hub-devguide-messages-d2c.md)
* [Caricamento file](./iot-hub-devguide-file-upload.md)
* [Importazione/esportazione di dispositivi in blocco](./iot-hub-bulk-identity-mgmt.md) 
