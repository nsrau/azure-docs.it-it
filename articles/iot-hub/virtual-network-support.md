---
title: Supporto dell'hub IoT di Azure per le reti virtualiAzure IoT Hub support for virtual networks
description: Come usare il modello di connettività delle reti virtuali con l'hub IoTHow to use virtual networks connectivity pattern with IoT Hub
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
# <a name="iot-hub-support-for-virtual-networks"></a>Supporto dell'hub IoT per le reti virtuali

Questo articolo introduce il modello di connettività VNET e spiega come configurare un'esperienza di connettività privata in un hub IoT tramite una rete virtuale di Azure di proprietà del cliente.

> [!NOTE]
> Le funzionalità dell'hub IoT descritte in questo articolo sono attualmente disponibili per gli hub IoT [creati con identità](#create-an-iot-hub-with-managed-service-identity) del servizio gestito nelle aree seguenti: Stati Uniti orientali, Stati Uniti centro-meridionali e Stati Uniti occidentali 2.


## <a name="introduction"></a>Introduzione

Per impostazione predefinita, i nomi host dell'hub IoT eseguono il mapping a un endpoint pubblico con un indirizzo IP instradabile pubblicamente su Internet.By default, IoT Hub hostnames map to a public endpoint with a publicly routable IP address over the Internet. Come illustrato nella figura seguente, questo endpoint pubblico dell'hub IoT è condiviso tra hub di proprietà di clienti diversi ed è accessibile dai dispositivi IoT tramite reti WAN e reti locali.

Diverse funzionalità dell'hub IoT, tra cui [il routing dei messaggi,](./iot-hub-devguide-messages-d2c.md)il [caricamento](./iot-hub-devguide-file-upload.md)di file e [l'importazione/esportazione di](./iot-hub-bulk-identity-mgmt.md) dispositivi in blocco, richiedono analogamente la connettività dall'hub IoT a una risorsa di Azure di proprietà del cliente tramite il relativo endpoint pubblico. Come illustrato di seguito, questi percorsi di connettività costituiscono collettivamente il traffico in uscita dall'hub IoT alle risorse dei clienti.
![Endpoint pubblico dell'hub IoT](./media/virtual-network-support/public-endpoint.png)


Per diversi motivi, i clienti potrebbero voler limitare la connettività alle risorse di Azure (incluso l'hub IoT) tramite una rete virtuale di cui sono proprietari e che gestiscono. Di seguito ne sono elencati alcuni:

* Introduzione di livelli di sicurezza aggiuntivi tramite l'isolamento a livello di rete per l'hub IoT impedendo l'esposizione della connettività all'hub tramite Internet pubblico.

* Abilitazione di un'esperienza di connettività privata dalle risorse di rete locali assicurando che i dati e il traffico vengano trasmessi direttamente alla rete backbone di Azure.Enabling a private connectivity experience from your on-premises network assets ensuring that your data and traffic is transmitted directly to Azure backbone network.

* Prevenzione degli attacchi di esfiltrazione da reti locali sensibili. 

* Seguire i modelli di connettività a livello di Azure stabiliti usando [gli endpoint privati.](../private-link/private-endpoint-overview.md)


Questo articolo descrive come raggiungere questi obiettivi usando gli endpoint privati per la connettività in ingresso all'hub IoT, poiché usando l'eccezione dei servizi di prima parte attendibili di Azure per la connettività in uscita dall'hub IoT ad altre risorse di Azure.This article describes how to achieve these goalss using [private endpoints](../private-link/private-endpoint-overview.md) for ingress connectivity to IoT Hub, as using Azure trusted first party services exception for egress connectivity from IoT Hub to other Azure resources.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Connettività in ingresso all'hub IoT tramite endpoint privatiIngress connectivity to IoT Hub using private endpoints

Un endpoint privato è un indirizzo IP privato allocato all'interno di una rete virtuale di proprietà del cliente tramite il quale è raggiungibile una risorsa di Azure.A private endpoint is a private IP address allocated inside a customer-owned VNET via which an Azure resource is reachable. Avendo un endpoint privato per l'hub IoT, sarai in grado di consentire ai servizi che operano all'interno della tua rete virtuale di raggiungere l'hub IoT senza richiedere l'invio di traffico all'endpoint pubblico dell'hub IoT. Analogamente, i dispositivi che operano in locale possono usare [la rete privata virtuale (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) o il peering privato [ExpressRoute](https://azure.microsoft.com/services/expressroute/) per ottenere la connettività alla rete virtuale in Azure e successivamente all'hub IoT (tramite il relativo endpoint privato). Di conseguenza, i clienti che desiderano limitare la connettività agli endpoint pubblici dell'hub IoT (o eventualmente bloccarla completamente) possono raggiungere questo obiettivo usando [le regole del firewall dell'hub IoT](./iot-hub-ip-filtering.md) mantenendo la connettività all'hub usando l'endpoint privato.

> [!NOTE]
> L'obiettivo principale di questa configurazione è per i dispositivi all'interno di una rete locale. Questa configurazione non è consigliata per i dispositivi distribuiti in una rete WAN.

![Endpoint pubblico dell'hub IoT](./media/virtual-network-support/virtual-network-ingress.png)

Prima di procedere, assicurarsi che siano soddisfatti i seguenti prerequisiti:

* È necessario eseguire il provisioning dell'hub IoT con [l'identità](#create-an-iot-hub-with-managed-service-identity)del servizio gestito.

* È necessario eseguire il provisioning dell'hub IoT in una delle [aree supportate.](#regional-availability-private-endpoints)

* È stato eseguito il provisioning di una rete virtuale di Azure con una subnet in cui verrà creato l'endpoint privato. Per altre informazioni, vedere [Creare una rete virtuale usando l'interfaccia della riga di comando di Azure.See create](../virtual-network/quick-create-cli.md) a virtual network using Azure CLI for more details.

* Per i dispositivi che operano all'interno di reti locali, configurare la rete privata virtuale (VPN) o il peering privato ExpressRoute nella rete virtuale di Azure.For devices that operate inside of on-premises networks, set up [Virtual Private Network (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) or [ExpressRoute](https://azure.microsoft.com/services/expressroute/) private peering into your Azure VNET.


### <a name="regional-availability-private-endpoints"></a>Disponibilità regionale (endpoint privati)

Endpoint privati supportati nell'hub IoT creati nelle aree seguenti:Private endpoints supported in IoT Hub's created in the following regions:

* Stati Uniti orientali

* Stati Uniti centro-meridionali

* Stati Uniti occidentali 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Configurare un endpoint privato per l'ingresso dell'hub IoTSet up a private endpoint for IoT Hub ingress

Per configurare un endpoint privato, attenersi alla seguente procedura:

1. Eseguire il comando dell'interfaccia della riga di comando di Azure seguente per registrare nuovamente il provider dell'hub IoT di Azure con la sottoscrizione:Run the following Azure CLI command to re-register Azure IoT Hub provider with your subscription:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Passare alla scheda **Connessioni endpoint privati** nel portale dell'hub IoT (questa scheda è disponibile solo negli hub IoT nelle [aree supportate)](#regional-availability-private-endpoints)e fare clic sul **+** segno per aggiungere un nuovo endpoint privato.

3. Specificare la sottoscrizione, il gruppo di risorse, il nome e l'area in cui creare il nuovo endpoint privato (idealmente, l'endpoint privato deve essere creato nella stessa area dell'hub; vedere la sezione relativa alla [disponibilità regionale](#regional-availability-private-endpoints) per ulteriori dettagli).

4. Fare clic su **Avanti: Risorsa**e fornire la sottoscrizione per la risorsa Hub IoT e selezionare **"Microsoft.Devices/IotHubs"** come tipo di risorsa, il nome dell'hub IoT come **risorsa**e **iotHub** come sottorisorsa di destinazione.

5. Fare clic su **Avanti: Configurazione** e specificare la rete virtuale e la subnet in cui creare l'endpoint privato. Se lo si desidera, selezionare l'opzione per l'integrazione con la zona DNS privata di Azure.

6. Fare clic su **Avanti: Tag**e, facoltativamente, specificare i tag per la risorsa.

7. Fare clic su **Revisione e creazione** per creare la risorsa endpoint privata.


### <a name="pricing-private-endpoints"></a>Prezzi (endpoint privati)

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link).


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Connettività in uscita dall'hub IoT ad altre risorse di Azure

L'hub IoT deve accedere all'archiviazione BLOB di Azure, agli hub eventi, alle risorse del bus di servizio per [il routing dei messaggi,](./iot-hub-devguide-messages-d2c.md)al [caricamento](./iot-hub-devguide-file-upload.md)di file e [all'importazione/esportazione di dispositivi in blocco,](./iot-hub-bulk-identity-mgmt.md)che in genere viene eseguita sull'endpoint pubblico delle risorse. Nel caso in cui si associa l'account di archiviazione, gli hub eventi o la risorsa bus di servizio a una rete virtuale, la configurazione consigliata bloccherà la connettività alla risorsa per impostazione predefinita. Di conseguenza, ciò ostacolerà le funzionalità dell'hub IoT che richiede l'accesso a tali risorse.

Per risolvere questa situazione, è necessario abilitare la connettività dalla risorsa dell'hub IoT all'account di archiviazione, agli hub eventi o alle risorse del bus di servizio tramite l'opzione Servizi attendibili di prima parte di Azure.To alleviato this situation, you need to enable connectivity from your IoT Hub resource to your storage account, event hubs or service bus resources via the **Azure first party trusted services** option.

I prerequisiti sono i seguenti:

* È necessario eseguire il provisioning dell'hub IoT in una delle [aree supportate.](#regional-availability-trusted-microsoft-first-party-services)

* All'hub IoT deve essere assegnata un'identità del servizio gestito al momento del provisioning dell'hub. Seguire le istruzioni su come [creare un hub con identità del servizio gestito.](#create-an-iot-hub-with-managed-service-identity)


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Disponibilità regionale (servizi Microsoft di prima parte attendibili)

I servizi di prima parte attendibili di Azure generano un'eccezione per ignorare le restrizioni del firewall per l'archiviazione di Azure, gli hub eventi e le risorse del bus di servizio sono supportati solo per gli hub IoT nelle aree seguenti:Azure trusted first party services exception to bypass firewall restrictions to Azure storage, event hubs and service bus resources is only supported for IoT Hubs in the following regions:

* Stati Uniti orientali

* Stati Uniti centro-meridionali

* Stati Uniti occidentali 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>Prezzi (servizi di prima parte Microsoft attendibili)

La funzionalità di eccezione dei servizi Di prima parte Microsoft attendibili è gratuita negli hub IoT nelle [aree supportate.](#regional-availability-trusted-microsoft-first-party-services) I costi per gli account di archiviazione, gli hub eventi o le risorse del bus di servizio di cui è stato eseguito il provisioning vengono applicati separatamente.


### <a name="create-an-iot-hub-with-managed-service-identity"></a>Creare un hub IoT con identità del servizio gestitoCreate an IoT hub with managed service identity

Un'identità del servizio gestito può essere assegnata all'hub al momento del provisioning delle risorse (questa funzionalità non è attualmente supportata per gli hub esistenti), che richiede all'hub IoT di usare TLS 1.2 come versione minima. A tale scopo, è necessario usare il modello di risorsa ARM di seguito:For this purpose, you need to use the ARM resource template below:

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

Dopo aver sostituendo i valori per `name` `location`la `SKU.name` `SKU.tier`risorsa , e , è possibile usare l'interfaccia della riga di comando di Azure per distribuire la risorsa in un gruppo di risorse esistente usando:

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Dopo aver creato la risorsa, è possibile recuperare l'identità del servizio gestito assegnata all'hub usando l'interfaccia della riga di comando di Azure:After the resource is created, you can retrieve the managed service identity assigned to your hub using Azure CLI:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

Dopo aver eseguito il provisioning dell'hub IoT con un'identità del servizio gestito, seguire la sezione corrispondente per configurare gli endpoint di routing per [gli account di archiviazione,](#egress-connectivity-to-storage-account-endpoints-for-routing) [gli hub eventi](#egress-connectivity-to-event-hubs-endpoints-for-routing)e le risorse del bus di [servizio](#egress-connectivity-to-service-bus-endpoints-for-routing) oppure per configurare [il caricamento](#egress-connectivity-to-storage-accounts-for-file-upload) e l'esportazione di dispositivi in [blocco.](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Connettività in uscita agli endpoint degli account di archiviazione per il routing

L'hub IoT può essere configurato per instradare i messaggi a un account di archiviazione di proprietà del cliente. Per consentire alla funzionalità di routing di accedere a un account di archiviazione mentre sono presenti restrizioni del firewall, l'hub IoT deve avere un'identità del servizio gestito (vedere come [creare un hub con identità](#create-an-iot-hub-with-managed-service-identity)del servizio gestito ). Dopo aver eseguito il provisioning di un'identità del servizio gestito, eseguire la procedura seguente per concedere l'autorizzazione RBAC all'identità della risorsa dell'hub per accedere all'account di archiviazione.

1. Nel portale di Azure passare alla scheda Controllo di accesso **(IAM)** dell'account di archiviazione e fare clic su **Aggiungi** nella sezione **Aggiungi un'assegnazione** di ruolo.

2. Selezionare **Collaboratore dati BLOB di archiviazione** come **ruolo** **, Utente, gruppo o entità servizio** di Azure AD come **Assegnazione dell'accesso e** selezionare il nome della risorsa dell'hub IoT nell'elenco a discesa. Fare clic su **Salva**.

3. Passare alla scheda **Firewall e reti virtuali** nell'account di archiviazione e abilitare l'opzione Consenti accesso da reti **selezionate.** Nell'elenco **Eccezioni** selezionare la casella **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione.** Fare clic su **Salva**.

4. Nella pagina delle risorse dell'hub IoT passare alla scheda **Routing messaggi.**

5. Passare alla sezione **Endpoint personalizzati** e fare clic su **Aggiungi**. Selezionare **Archiviazione** come tipo di endpoint.

6. Nella pagina visualizzata specificare un nome per l'endpoint, selezionare il contenitore che si intende usare nell'archiviazione BLOB, specificare la codifica e il formato del nome file. Selezionare **Sistema assegnato** come **tipo di autenticazione** per l'endpoint di archiviazione. Fare clic sul pulsante **Crea**.

Ora l'endpoint di archiviazione personalizzato è configurato per usare l'identità assegnata dal sistema dell'hub e dispone dell'autorizzazione per accedere alla risorsa di archiviazione nonostante le restrizioni del firewall. È ora possibile utilizzare questo endpoint per impostare una regola di routing.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Connettività in uscita agli endpoint degli hub eventi per il routing

L'hub IoT può essere configurato per instradare i messaggi a uno spazio dei nomi degli hub eventi di proprietà del cliente. Per consentire alla funzionalità di routing di accedere a una risorsa hub eventi mentre sono presenti restrizioni del firewall, l'hub IoT deve disporre di un'identità del servizio gestito (vedere come [creare un hub con identità](#create-an-iot-hub-with-managed-service-identity)del servizio gestito ). Dopo aver eseguito il provisioning di un'identità del servizio gestito, seguire i passaggi seguenti per concedere l'autorizzazione RBAC all'identità della risorsa dell'hub per accedere agli hub eventi.

1. Nel portale di Azure passare alla scheda **Controllo di accesso (IAM)** degli hub eventi e fare clic su **Aggiungi** nella sezione **Aggiungi assegnazione ruolo.**

2. Selezionare **Mittente dati hub eventi** come **ruolo** **, Utente, gruppo o** entità servizio di Azure AD come **Assegnazione dell'accesso e** selezionare il nome della risorsa dell'hub IoT nell'elenco a discesa. Fare clic su **Salva**.

3. Passare alla scheda **Firewall e reti virtuali** negli hub eventi e abilitare l'opzione Consenti accesso da reti **selezionate.** Nell'elenco **Eccezioni** selezionare la casella **Consenti ai servizi Microsoft attendibili**di accedere agli hub eventi . Fare clic su **Salva**.

4. Nella pagina delle risorse dell'hub IoT passare alla scheda **Routing messaggi.**

5. Passare alla sezione **Endpoint personalizzati** e fare clic su **Aggiungi**. Selezionare **Hub eventi** come tipo di endpoint.

6. Nella pagina visualizzata specificare un nome per l'endpoint, selezionare lo spazio dei nomi e l'istanza degli hub eventi e fare clic sul pulsante **Crea.On** the page that shows up, provide a name for your endpoint, select your event hubs namespace and instance and click the Create button.

Ora l'endpoint hub eventi personalizzato è configurato per usare l'identità assegnata dal sistema dell'hub e dispone dell'autorizzazione per accedere alla risorsa hub eventi nonostante le restrizioni del firewall. È ora possibile utilizzare questo endpoint per impostare una regola di routing.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Connettività in uscita agli endpoint del bus di servizio per il routing

L'hub IoT può essere configurato per instradare i messaggi a uno spazio dei nomi del bus di servizio di proprietà del cliente. Per consentire alla funzionalità di routing di accedere a una risorsa del bus di servizio mentre sono presenti restrizioni del firewall, l'hub IoT deve avere un'identità del servizio gestito (vedere come [creare un hub con identità](#create-an-iot-hub-with-managed-service-identity)del servizio gestito ). Dopo aver eseguito il provisioning di un'identità del servizio gestito, seguire i passaggi seguenti per concedere l'autorizzazione RBAC all'identità della risorsa dell'hub per accedere al bus di servizio.

1. Nel portale di Azure passare alla scheda Controllo di accesso **(IAM)** del bus di servizio e fare clic su **Aggiungi** nella sezione **Aggiungi un'assegnazione** di ruolo.

2. Selezionare **Mittente dati bus** di servizio come **ruolo** **, Utente, gruppo o** entità servizio di Azure AD come **Assegnazione dell'accesso e** selezionare il nome della risorsa dell'hub IoT nell'elenco a discesa. Fare clic su **Salva**.

3. Passare alla scheda **Firewall e reti virtuali** nel bus di servizio e abilitare l'opzione Consenti accesso da reti **selezionate.** Nell'elenco **Eccezioni** selezionare la casella **Consenti ai servizi Microsoft attendibili di accedere a questo bus**di servizio . Fare clic su **Salva**.

4. Nella pagina delle risorse dell'hub IoT passare alla scheda **Routing messaggi.**

5. Passare alla sezione **Endpoint personalizzati** e fare clic su **Aggiungi**. Selezionare **Coda bus** di servizio o **Argomento bus di servizio** (se applicabile) come tipo di endpoint.

6. Nella pagina visualizzata specificare un nome per l'endpoint, selezionare lo spazio dei nomi e la coda o l'argomento del bus di servizio (se applicabile). Fare clic sul pulsante **Crea**.

Ora l'endpoint del bus di servizio personalizzato è configurato per usare l'identità assegnata dal sistema dell'hub e dispone dell'autorizzazione per accedere alla risorsa del bus di servizio nonostante le restrizioni del firewall. È ora possibile utilizzare questo endpoint per impostare una regola di routing.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Connettività in uscita agli account di archiviazione per il caricamento dei file

La funzionalità di caricamento dei file dell'hub IoT consente ai dispositivi di caricare file in un account di archiviazione di proprietà del cliente. Per consentire il caricamento del file per il funzionamento, sia i dispositivi che l'hub IoT devono disporre della connettività all'account di archiviazione. Se sull'account di archiviazione sono presenti restrizioni del firewall, i dispositivi devono usare il meccanismo dell'account di archiviazione supportato (inclusi [gli endpoint privati,](../private-link/create-private-endpoint-storage-portal.md) [gli endpoint](../virtual-network/virtual-network-service-endpoints-overview.md) del servizio o la configurazione diretta del [firewall)](../storage/common/storage-network-security.md)per ottenere la connettività. Analogamente, se nell'account di archiviazione sono presenti restrizioni del firewall, l'hub IoT deve essere configurato per accedere alla risorsa di archiviazione tramite l'eccezione dei servizi Microsoft attendibili. A tale scopo, l'hub IoT deve avere un'identità del servizio gestito (vedere come [creare un hub con identità](#create-an-iot-hub-with-managed-service-identity)del servizio gestito ). Dopo aver eseguito il provisioning di un'identità del servizio gestito, eseguire la procedura seguente per concedere l'autorizzazione RBAC all'identità della risorsa dell'hub per accedere all'account di archiviazione.

1. Nel portale di Azure passare alla scheda Controllo di accesso **(IAM)** dell'account di archiviazione e fare clic su **Aggiungi** nella sezione **Aggiungi un'assegnazione** di ruolo.

2. Selezionare **Collaboratore dati BLOB di archiviazione** come **ruolo** **, Utente, gruppo o entità servizio** di Azure AD come **Assegnazione dell'accesso e** selezionare il nome della risorsa dell'hub IoT nell'elenco a discesa. Fare clic su **Salva**.

3. Passare alla scheda **Firewall e reti virtuali** nell'account di archiviazione e abilitare l'opzione Consenti accesso da reti **selezionate.** Nell'elenco **Eccezioni** selezionare la casella **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione.** Fare clic su **Salva**.

4. Nella pagina delle risorse dell'hub IoT passare alla scheda **Caricamento file.**

5. Nella pagina visualizzata selezionare il contenitore che si desidera utilizzare nell'archiviazione BLOB, configurare le impostazioni di **notifica file**, **TTL SAS**, **TTL predefinito** e numero massimo di **recapiti** come desiderato. Selezionare **Sistema assegnato** come **tipo di autenticazione** per l'endpoint di archiviazione. Fare clic sul pulsante **Crea**.

Ora l'endpoint di archiviazione per il caricamento dei file è configurato per usare l'identità assegnata dal sistema dell'hub e dispone dell'autorizzazione per accedere alla risorsa di archiviazione nonostante le restrizioni del firewall.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Connettività in uscita agli account di archiviazione per l'importazione/esportazione di dispositivi in blocco

L'hub IoT supporta la funzionalità per [importare/esportare](./iot-hub-bulk-identity-mgmt.md) le informazioni dei dispositivi in blocco da/verso un BLOB di archiviazione fornito dal cliente. Per consentire il funzionamento della funzionalità di importazione/esportazione in blocco, sia i dispositivi che l'hub IoT devono disporre della connettività all'account di archiviazione.

Questa funzionalità richiede la connettività dall'hub IoT all'account di archiviazione. Per accedere a una risorsa del bus di servizio mentre sono presenti restrizioni del firewall, l'hub IoT deve disporre di un'identità del servizio gestito (vedere come [creare un hub con identità](#create-an-iot-hub-with-managed-service-identity)del servizio gestito ). Dopo aver eseguito il provisioning di un'identità del servizio gestito, seguire i passaggi seguenti per concedere l'autorizzazione RBAC all'identità della risorsa dell'hub per accedere al bus di servizio.

1. Nel portale di Azure passare alla scheda Controllo di accesso **(IAM)** dell'account di archiviazione e fare clic su **Aggiungi** nella sezione **Aggiungi un'assegnazione** di ruolo.

2. Selezionare **Collaboratore dati BLOB di archiviazione** come **ruolo** **, Utente, gruppo o entità servizio** di Azure AD come **Assegnazione dell'accesso e** selezionare il nome della risorsa dell'hub IoT nell'elenco a discesa. Fare clic su **Salva**.

3. Passare alla scheda **Firewall e reti virtuali** nell'account di archiviazione e abilitare l'opzione Consenti accesso da reti **selezionate.** Nell'elenco **Eccezioni** selezionare la casella **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione.** Fare clic su **Salva**.

È ora possibile usare le API REST di Azure IoT per [creare processi](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) di esportazione di importazione per informazioni su come usare la funzionalità di importazione/esportazione in blocco. Si noti che è `storageAuthenticationType="identityBased"` necessario fornire il `inputBlobContainerUri="https://..."` `outputBlobContainerUri="https://..."` nel corpo della richiesta e utilizzare e come URL di input e output dell'account di archiviazione, rispettivamente.


Anche l'SDK dell'hub IoT di Azure supporta questa funzionalità nel gestore del Registro di sistema del client del servizio. Il frammento di codice seguente viene illustrato come avviare un processo di importazione o di esportazione nell'utilizzo dell'SDK di C.

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


Per usare questa versione limitata dell'area degli SDK di Azure IoT con il supporto della rete virtuale per C, Java e Node.js:

1. Creare una variabile `EnableStorageIdentity` di ambiente `1`denominata e impostarne il valore su .

2. Scaricare l'SDK: [Java](https://aka.ms/vnetjavasdk) | [C'è](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
Per Python, scarica la nostra versione limitata da GitHub.

1. Passare alla pagina di rilascio di [GitHub](https://aka.ms/vnetpythonsdk).

2. Scaricare il file seguente, che si trova nella parte inferiore della pagina di rilascio sotto l'intestazione denominata **assets**.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Aprire un terminale e passare alla cartella contenente il file scaricato.

4. Eseguire il comando seguente per installare Python Service SDK con il supporto per le reti virtuali:
    > pip installare ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Passaggi successivi

Usare i collegamenti seguenti per ulteriori informazioni sulle funzionalità dell'hub IoT:

* [Routing dei messaggi](./iot-hub-devguide-messages-d2c.md)
* [Caricamento di file](./iot-hub-devguide-file-upload.md)
* [Importazione/esportazione di dispositivi in blocco](./iot-hub-bulk-identity-mgmt.md) 
