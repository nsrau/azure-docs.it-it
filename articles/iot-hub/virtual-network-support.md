---
title: Supporto dell'hub IoT di Azure per le reti virtuali
description: Come usare il modello di connettività delle reti virtuali con l'hub IoT
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 09/24/2020
ms.author: jlian
ms.openlocfilehash: eb25fc0d7831bc06b708431ce3d47c73b36fe5c6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91281251"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>Supporto dell'hub IoT per le reti virtuali con collegamento privato e identità gestita

Per impostazione predefinita, i nomi host dell'hub IoT eseguono il mapping a un endpoint pubblico con un indirizzo IP instradabile pubblicamente su Internet. Diversi clienti possono condividere questo endpoint pubblico dell'hub IoT e consentire l'accesso a tutti i dispositivi IoT su reti WAN (Wide Area Network) e su reti locali.

![Endpoint pubblico dell'hub IoT](./media/virtual-network-support/public-endpoint.png)

Le funzionalità dell'hub IoT, tra cui [routing dei messaggi](./iot-hub-devguide-messages-d2c.md), [caricamento dei file](./iot-hub-devguide-file-upload.md) e [importazione/esportazione in blocco dei dispositivi](./iot-hub-bulk-identity-mgmt.md), richiedono anche la connettività dall'hub IoT a una risorsa di Azure di proprietà del cliente tramite il rispettivo endpoint pubblico. Questi percorsi di connettività generano collettivamente il traffico in uscita dall'hub IoT alle risorse dei clienti.

Potrebbe essere opportuno limitare la connettività alle risorse di Azure (incluso l'hub IoT) tramite una rete virtuale gestita di proprietà. Di seguito sono elencati alcuni motivi per cui questa scelta è consigliabile:

* Isolamento della rete per l'hub IoT per impedire l'esposizione della connettività alla rete Internet pubblica.

* Abilitazione di un'esperienza di connettività privata dalle risorse di rete locali per garantire che i dati e il traffico vengano trasmessi direttamente alla rete di backbone di Azure.

* Prevenzione degli attacchi di esfiltrazione da reti locali sensibili. 

* Applicazione di modelli di connettività consolidati a livello di Azure tramite [endpoint privati](../private-link/private-endpoint-overview.md).

Questo articolo descrive come raggiungere questi obiettivi usando un [collegamento privato di Azure](../private-link/private-link-overview.md) per la connettività in ingresso all'hub IoT e l'uso di eccezioni di servizi Microsoft attendibili per la connettività in uscita dall'hub IoT alle altre risorse di Azure.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Connettività in ingresso all'hub IoT tramite il collegamento privato di Azure

Un endpoint privato è un indirizzo IP privato allocato all'interno di una rete virtuale di proprietà del cliente tramite la quale è raggiungibile una risorsa di Azure. Tramite il collegamento privato di Azure è possibile configurare un endpoint privato per l'hub IoT per consentire ai servizi all'interno della rete virtuale di raggiungere l'hub IoT senza richiedere che il traffico venga inviato all'endpoint pubblico dell'hub IoT. In modo analogo, i dispositivi locali possono utilizzare la [rete VPN (Virtual Private Network)](../vpn-gateway/vpn-gateway-about-vpngateways.md) o il peering [ExpressRoute](https://azure.microsoft.com/services/expressroute/) per ottenere la connettività alla rete virtuale e all'hub IoT (tramite l'endpoint privato). Di conseguenza, è possibile limitare o bloccare completamente la connettività agli endpoint pubblici dell'hub IoT utilizzando il [filtro IP dell'hub IoT](./iot-hub-ip-filtering.md) e [configurando il routing in modo che non vengano inviati dati all'endpoint predefinito](#built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint). Questo approccio consente di mantenere la connettività all'hub utilizzando l'endpoint privato per i dispositivi. Questa configurazione è destinata principalmente ai dispositivi all'interno di una rete locale. Questa configurazione non è consigliata per i dispositivi distribuiti su una rete WAN.

![Endpoint pubblico dell'hub IoT](./media/virtual-network-support/virtual-network-ingress.png)

Prima di iniziare, verificare che siano soddisfatti i seguenti prerequisiti:

* Creare una [rete virtuale di Azure](../virtual-network/quick-create-portal.md) con una subnet in cui verrà creato l'endpoint privato.

* Per i dispositivi che operano in reti locali, impostare il peering privato [Virtual Private Network (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [ExpressRoute](https://azure.microsoft.com/services/expressroute/) nella rete virtuale di Azure.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Configurare un endpoint privato per il traffico in ingresso dell'hub IoT

L'endpoint privato funziona per le API del dispositivo dell'hub Internet (ad esempio, i messaggi da dispositivo a cloud) e le API del servizio, ad esempio la creazione e l'aggiornamento di dispositivi.

1. Nel portale di Azure selezionare **Rete**, **Connessioni a endpoint privato**, quindi fare clic su **+ endpoint privato**.

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Screenshot che mostra come aggiungere un endpoint privato per l'hub IoT":::

1. Specificare la sottoscrizione, il gruppo di risorse e l'area per la creazione del nuovo endpoint privato. Idealmente, l'endpoint privato deve essere creato nella stessa area dell'hub.

1. Fare clic su **Avanti: Risorsa** e specificare la sottoscrizione per la risorsa dell'hub IoT, selezionare "**Microsoft.Devices/IotHubs**" come tipo di risorsa, il nome dell'hub IoT come **risorsa** e **iotHub** come risorsa secondaria di destinazione.

1. Fare clic su **Avanti: Configurazione** e specificare la rete virtuale e la subnet in cui creare l'endpoint privato. Selezionare l'opzione per l'integrazione con la zona DNS privata di Azure, se lo si desidera.

1. Fare clic su **Avanti: Tag** e, facoltativamente, fornire eventuali tag per la risorsa.

1. Fare clic su **Rivedi e crea** per creare la risorsa di collegamento privato.

### <a name="built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint"></a>L'endpoint predefinito compatibile con l'hub eventi non supporta l'accesso tramite endpoint privato

L'[endpoint predefinito compatibile con l'hub eventi](iot-hub-devguide-messages-read-builtin.md) non supporta l'accesso tramite endpoint privato. Se configurato, un endpoint privato dell'hub IoT è destinato solo alla connettività di ingresso. L'utilizzo di dati dell'endpoint predefinito compatibile con l'hub eventi può essere eseguito solo attraverso la rete Internet pubblica. 

Inoltre, il [filtro IP](iot-hub-ip-filtering.md) dell'hub IoT non consente di controllare l'accesso pubblico all'endpoint predefinito. Per bloccare completamente l'accesso alla rete pubblica all'hub IoT, è necessario: 

1. Configurare l'accesso di un endpoint privato per l'hub IoT
1. [Disabilitare l'accesso alla rete pubblica](iot-hub-public-network-access.md) o usare il filtro IP per bloccare tutti gli indirizzi IP
1. Arrestare l'uso dell'endpoint dell'hub eventi predefinito [impostando il routing in modo che non invii dati](iot-hub-devguide-messages-d2c.md)
1. Disattivare la [route di fallback](iot-hub-devguide-messages-d2c.md#fallback-route)
1. Configurare l'uscita verso altre risorse di Azure usando il [servizio Microsoft attendibile](#egress-connectivity-from-iot-hub-to-other-azure-resources)

### <a name="pricing-for-private-link"></a>Prezzi del collegamento privato

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Connettività in uscita dall'hub IoT ad altre risorse di Azure

L'hub IoT può connettersi all'archivio BLOB di Azure, all'hub eventi, alle risorse del bus di servizio per il [routing dei messaggi](./iot-hub-devguide-messages-d2c.md), il [caricamento dei file](./iot-hub-devguide-file-upload.md) e l'[importazione/esportazione in blocco dei dispositivi](./iot-hub-bulk-identity-mgmt.md) tramite l'endpoint pubblico delle risorse. Per impostazione predefinita, il binding della risorsa a una rete virtuale blocca la connettività alla risorsa. Di conseguenza, questa configurazione impedisce all'hub IoT di inviare dati alle risorse. Per risolvere questo problema, abilitare la connettività dalla risorsa dell'hub IoT all'account di archiviazione, all'hub eventi o alle risorse del bus di servizio tramite l'opzione del **servizio Microsoft attendibile**.

### <a name="turn-on-managed-identity-for-iot-hub"></a>Attivare l'identità gestita per l'hub IoT

Per consentire ad altri servizi di trovare l'hub IoT come servizio Microsoft attendibile, è necessario disporre di un'identità gestita assegnata dal sistema.

1. Nel portale dell'hub IoT passare a **Identità**.

1. In **Stato** selezionare **Attiva**, quindi fare clic su **Salva**.

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="Screenshot che mostra come attivare l'identità gestita per l'hub IoT":::

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>Assegnare l'identità gestita all'hub Internet in fase di creazione usando il modello ARM

Per assegnare l'identità gestita all'hub Internet per il tempo di provisioning delle risorse, usare il modello ARM seguente:

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

Dopo aver sostituito i valori per la risorsa `name` , `location` `SKU.name` e, `SKU.tier` è possibile usare l'interfaccia della riga di comando di Azure per distribuire la risorsa in un gruppo di risorse esistente usando:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Dopo aver creato la risorsa, è possibile recuperare l'identità del servizio gestito assegnata all'hub usando l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

### <a name="pricing-for-managed-identity"></a>Prezzi per l'identità gestita

La funzionalità di eccezione dei servizi attendibili di prima parte di Microsoft è gratuita. Gli addebiti per gli account di archiviazione con provisioning, gli hub eventi o le risorse del bus di servizio vengono applicati separatamente.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Connettività in uscita verso gli endpoint dell'account di archiviazione per il routing

L'hub IoT può eseguire il routing dei messaggi a un account di archiviazione di proprietà del cliente. Per consentire alla funzionalità di routing di accedere a un account di archiviazione mentre sono in attive restrizioni del firewall, l'hub IoT deve disporre di un'[identità gestita](#turn-on-managed-identity-for-iot-hub). Una volta eseguito il provisioning di un'identità gestita,attenersi alla procedura seguente per assegnare l'autorizzazione RBAC all'identità della risorsa dell'hub per l'accesso all'account di archiviazione.

1. Nel portale di Azure, passare alla scheda **Controllo di accesso (IAM)** dell'account di archiviazione e fare clic su **Aggiungi** nella sezione **Aggiungi un'assegnazione di ruolo**.

2. Selezionare **Collaboratore ai dati dei BLOB di archiviazione** ([*non* Collaboratore o Collaboratore Account di archiviazione](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) come **ruolo**, **Utente, gruppo o entità servizio di Azure AD** per il campo **Assegnazione dell'accesso a** e quindi il nome della risorsa dell'hub IoT dall'elenco a discesa. Fare clic sul pulsante **Salva** .

3. Passare alla scheda **Firewall e reti virtuali** dell'account di archiviazione e abilitare l'opzione per **consentire l'accesso da reti selezionate**. Nell'elenco **Eccezioni** selezionare la casella di controllo **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione**. Fare clic sul pulsante **Salva** .

4. Nella pagina delle risorse dell'hub IoT, passare alla scheda **Routing messaggi**.

5. Passare alla sezione **Endpoint personalizzati** e fare clic su **Aggiungi**. Come tipo di endpoint selezionare **Archiviazione**.

6. Nella pagina visualizzata, fornire un nome per l'endpoint, selezionare il contenitore che si intende utilizzare nell'archivio BLOB, specificare la codifica e il formato per il nome file. Selezionare **Identity-based** come **tipo di autenticazione** per l'endpoint di archiviazione. Fare clic sul pulsante **Create** (Crea).

A questo punto, l'endpoint di archiviazione personalizzato è impostato per l'uso dell'identità assegnata dal sistema dell'hub e dispone dell'autorizzazione per accedere alla risorsa di archiviazione nonostante le restrizioni del firewall. Ora è possibile usare l'endpoint per impostare una regola di gestione.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Connettività in uscita verso gli endpoint dell'hub eventi per il routing

È possibile configurare l'hub IoT per il routing dei messaggi verso uno spazio dei nomi dell'hub eventi di proprietà del cliente. Per consentire alla funzionalità di routing di accedere a una risorsa dell'hub eventi mentre sono in attive restrizioni del firewall, l'hub IoT deve disporre di un'identità gestita. Una volta creata un'identità gestita, attenersi alla procedura seguente per assegnare l'autorizzazione RBAC all'identità della risorsa dell'hub per l'accesso all'hub eventi.

1. Nel portale di Azure, passare alla scheda **Controllo di accesso (IAM)** dell'hub eventi e fare clic su **Aggiungi** nella sezione **Aggiungi un'assegnazione di ruolo**.

2. Selezionare **Mittente dei dati di Hub eventi di Azure** come **ruolo**, **Utente, gruppo o entità servizio di Azure AD** per il campo **Assegna accesso a** e quindi il nome della risorsa dell'hub IoT dall'elenco a discesa. Fare clic sul pulsante **Salva** .

3. Passare alla scheda **Firewall e reti virtuali** nell'hub eventi e abilitare l'opzione per **consentire l'accesso da reti selezionate**. Nell'elenco **Eccezioni** selezionare la casella di controllo per **consentire ai servizi Microsoft attendibili di accedere a questo account di archiviazione**. Fare clic sul pulsante **Salva** .

4. Nella pagina delle risorse dell'hub IoT, passare alla scheda **Routing messaggi**.

5. Passare alla sezione **Endpoint personalizzati** e fare clic su **Aggiungi**. Come tipo di endpoint selezionare **Hub eventi**.

6. Nella pagina visualizzata specificare un nome per l'endpoint, selezionare lo spazio dei nomi e l'istanza dell'hub eventi, quindi fare clic sul pulsante **Crea**.

A questo punto, l'hub eventi personalizzato è impostato per l'uso dell'identità assegnata dal sistema dell'hub e dispone dell'autorizzazione per accedere alla risorsa dell'hub eventi nonostante le restrizioni del firewall. Ora è possibile usare l'endpoint per impostare una regola di gestione.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Connettività in uscita verso gli endpoint del bus di servizio per il routing

È possibile configurare l'hub IoT per il routing dei messaggi verso uno spazio dei nomi del bus di servizio di proprietà del cliente. Per consentire alla funzionalità di routing di accedere a una risorsa del bus di servizio mentre sono in attive restrizioni del firewall, l'hub IoT deve disporre di un'identità gestita. Una volta eseguito il provisioning di un'identità gestita, attenersi alla procedura seguente per assegnare l'autorizzazione RBAC all'identità della risorsa dell'hub per l'accesso al bus di servizio.

1. Nel portale di Azure, passare alla scheda **Controllo di accesso (IAM)** del bus di servizio e fare clic su **Aggiungi** nella sezione **Aggiungi un'assegnazione di ruolo**.

2. Selezionare **Mittente dei dati del bus di servizio di Azure** come **ruolo**, **Utente, gruppo o entità servizio di Azure AD** per il campo **Assegna accesso a** e quindi il nome della risorsa dell'hub IoT dall'elenco a discesa. Fare clic sul pulsante **Salva** .

3. Passare alla scheda **Firewall e reti virtuali** nel bus di servizio e abilitare l'opzione per **consentire l'accesso da reti selezionate**. Nell'elenco **Eccezioni** selezionare la casella di controllo per **consentire ai servizi Microsoft attendibili di accedere a questo bus di servizio**. Fare clic sul pulsante **Salva** .

4. Nella pagina delle risorse dell'hub IoT, passare alla scheda **Routing messaggi**.

5. Passare alla sezione **Endpoint personalizzati** e fare clic su **Aggiungi**. Come tipo di endpoint selezionare **Coda del bus di servizio** o **Argomento del bus di servizio** (come appropriato).

6. Nella pagina visualizzata specificare un nome per l'endpoint, selezionare lo spazio dei nomi e la coda o l'argomento del bus di servizio (come appropriato). Fare clic sul pulsante **Create** (Crea).

A questo punto, il bus di servizio personalizzato è impostato per l'uso dell'identità assegnata dal sistema dell'hub e dispone dell'autorizzazione per accedere alla risorsa del bus di servizio nonostante le restrizioni del firewall. Ora è possibile usare l'endpoint per impostare una regola di gestione.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Connettività in uscita verso gli account di archiviazione per il caricamento dei file

La funzionalità di caricamento dei file dell'hub IoT consente ai dispositivi di caricare i file in un account di archiviazione di proprietà del cliente. Per consentire il caricamento dei file, sia i dispositivi sia l'hub IoT devono disporre di connettività all'account di archiviazione. Se sono presenti restrizioni del firewall per l'account di archiviazione, i dispositivi devono usare uno dei meccanismi dell'account di archiviazione supportati (tra cui [endpoint privati](../private-link/create-private-endpoint-storage-portal.md), [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) o [configurazione diretta del firewall](../storage/common/storage-network-security.md)) per garantire la connettività. In modo analogo, se sono presenti restrizioni del firewall per l'account di archiviazione, l'hub IoT deve essere configurato per l'accesso alla risorsa di archiviazione tramite l'eccezione dei servizi Microsoft attendibili. A tale scopo, l'hub IoT deve disporre di un'identità gestita. Una volta eseguito il provisioning di un'identità gestita, attenersi alla procedura seguente per assegnare l'autorizzazione RBAC all'identità della risorsa dell'hub per l'accesso all'account di archiviazione.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

1. Nel portale di Azure, passare alla scheda **Controllo di accesso (IAM)** dell'account di archiviazione e fare clic su **Aggiungi** nella sezione **Aggiungi un'assegnazione di ruolo**.

2. Selezionare **Collaboratore ai dati dei BLOB di archiviazione** ([*non* Collaboratore o Collaboratore Account di archiviazione](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) come **ruolo**, **Utente, gruppo o entità servizio di Azure AD** per il campo **Assegnazione dell'accesso a** e quindi il nome della risorsa dell'hub IoT dall'elenco a discesa. Fare clic sul pulsante **Salva** .

3. Passare alla scheda **Firewall e reti virtuali** dell'account di archiviazione e abilitare l'opzione per **consentire l'accesso da reti selezionate**. Nell'elenco **Eccezioni** selezionare la casella di controllo **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione**. Fare clic sul pulsante **Salva** .

4. Nella pagina delle risorse dell'hub IoT, passare alla scheda **Caricamento file**.

5. Nella pagina visualizzata selezionare il contenitore che si desidera utilizzare nell'archivio BLOB, configurare i valori per i campi **Impostazioni di notifica file**, **Durata TTL della firma di accesso condiviso**, **Durata (TTL) predefinita** e **Numero massimo di distribuzioni** in base alle esigenze. Selezionare **Identity-based** come **tipo di autenticazione** per l'endpoint di archiviazione. Fare clic sul pulsante **Crea**. Se si riceve un errore in questo passaggio, impostare temporaneamente l'account di archiviazione per consentire l'accesso da **tutte le reti**, quindi riprovare. È possibile configurare il firewall nell'account di archiviazione al termine della configurazione del caricamento del file.

A questo punto, l'endpoint di archiviazione per il caricamento dei file è impostato per l'uso dell'identità assegnata dal sistema dell'hub e dispone dell'autorizzazione per accedere alla risorsa di archiviazione nonostante le restrizioni del firewall.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Connettività in uscita verso gli account di archiviazione per l'importazione/esportazione in blocco dei dispositivi

L'hub IoT supporta la funzionalità per l'[importazione/esportazione](./iot-hub-bulk-identity-mgmt.md) in blocco delle informazioni dei dispositivi da o verso un BLOB di archiviazione fornito dal cliente. Per l'uso dell'importazione/esportazione in blocco, sia i dispositivi sia l'hub IoT devono disporre di connettività all'account di archiviazione.

Questa funzionalità richiede la connettività dall'hub IoT all'account di archiviazione. Per consentire l'accesso a una risorsa del bus di servizio mentre sono in attive restrizioni del firewall, l'hub IoT deve disporre di un'identità gestita. Una volta eseguito il provisioning di un'identità gestita, attenersi alla procedura seguente per assegnare l'autorizzazione RBAC all'identità della risorsa dell'hub per l'accesso al bus di servizio.

1. Nel portale di Azure, passare alla scheda **Controllo di accesso (IAM)** dell'account di archiviazione e fare clic su **Aggiungi** nella sezione **Aggiungi un'assegnazione di ruolo**.

2. Selezionare **Collaboratore ai dati dei BLOB di archiviazione** ([*non* Collaboratore o Collaboratore Account di archiviazione](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) come **ruolo**, **Utente, gruppo o entità servizio di Azure AD** per il campo **Assegnazione dell'accesso a** e quindi il nome della risorsa dell'hub IoT dall'elenco a discesa. Fare clic sul pulsante **Salva** .

3. Passare alla scheda **Firewall e reti virtuali** dell'account di archiviazione e abilitare l'opzione per **consentire l'accesso da reti selezionate**. Nell'elenco **Eccezioni** selezionare la casella di controllo **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione**. Fare clic sul pulsante **Salva** .

Ora è possibile usare le API REST di IoT di Azure per [creare processi di esportazione e importazione](https://docs.microsoft.com/rest/api/iothub/service/jobs/getimportexportjobs) per informazioni su come usare la funzionalità di importazione/esportazione in blocco. È necessario fornire il `storageAuthenticationType="identityBased"` nel corpo della richiesta e usare rispettivamente `inputBlobContainerUri="https://..."` e `outputBlobContainerUri="https://..."` come URL di input e output per l'account di archiviazione.

Gli SDK dell'hub IoT di Azure supportano questa funzionalità anche nel gestore del Registro di sistema del client del servizio. Il frammento di codice seguente mostra come avviare un processo di importazione o esportazione usando l'SDK C#.

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

Per usare questa versione degli SDK IoT di Azure con supporto di rete virtuale per C#, Java e Node.js:

1. Creare una variabile di ambiente denominata `EnableStorageIdentity` e impostare il valore su `1`.

2. Scaricare l'SDK:  [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
Per Python, scaricare la versione limitata da GitHub.

1. Passare alla [pagina di rilascio di GitHub](https://aka.ms/vnetpythonsdk).

2. Scaricare il file seguente, disponibile nella parte inferiore della pagina di rilascio nella sezione **risorse**.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Aprire un terminale e passare alla cartella in cui è stato scaricato il file.

4. Per installare l'SDK Service per Python con supporto per reti virtuali, usare il comando seguente:
    > pip install ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Passaggi successivi

Consultare i collegamenti seguenti per altre informazioni sulle funzionalità dell'hub IoT:

* [Routing dei messaggi](./iot-hub-devguide-messages-d2c.md)
* [Caricamento file](./iot-hub-devguide-file-upload.md)
* [Importazione/esportazione in blocco dei dispositivi](./iot-hub-bulk-identity-mgmt.md) 
