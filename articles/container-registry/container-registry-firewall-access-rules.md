---
title: Regole di accesso al firewall
description: Configurare le regole per accedere a un registro contenitori di Azure da dietro un firewall, consentendo l'accesso all'API REST di ("whitelist") e ai nomi di dominio dell'endpoint dati o agli intervalli di indirizzi IP specifici del servizio.
ms.topic: article
ms.date: 05/07/2020
ms.openlocfilehash: b3560fe769a97c8d3a4e5a3580d42d7c0b3a3f08
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82978349"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Configurare le regole per accedere a un registro contenitori di Azure dietro un firewall

Questo articolo illustra come configurare le regole nel firewall per consentire l'accesso a un registro contenitori di Azure. Ad esempio, un dispositivo Azure IoT Edge dietro un firewall o un server proxy potrebbe dover accedere a un registro contenitori per eseguire il pull di un'immagine del contenitore. In alternativa, un server bloccato in una rete locale potrebbe avere l'esigenza di accedere per eseguire il push di un'immagine.

Se invece si vuole configurare l'accesso alla rete in ingresso a un registro contenitori solo all'interno di una rete virtuale di Azure, vedere [configurare un collegamento privato di Azure per un registro contenitori di Azure](container-registry-private-link.md).

## <a name="about-registry-endpoints"></a>Informazioni sugli endpoint del registro di sistema

Per eseguire il pull o il push di immagini o altri artefatti in un registro contenitori di Azure, un client come un daemon Docker deve interagire con HTTPS con due endpoint distinti. Per i client che accedono a un registro da dietro un firewall, è necessario configurare le regole di accesso per entrambi gli endpoint.

* **Endpoint API REST del registro** di sistema: le operazioni di autenticazione e gestione del registro di sistema vengono gestite tramite l'endpoint API REST pubblico del registro di sistema. Questo endpoint è il nome del server di accesso del registro di sistema. Esempio: `myregistry.azurecr.io`

* **Endpoint di archiviazione (dati)** : Azure [alloca l'archiviazione BLOB](container-registry-storage.md) negli account di archiviazione di Azure per conto di ogni registro di sistema per gestire i dati per le immagini del contenitore e altri artefatti. Quando un client accede a livelli immagine in un registro contenitori di Azure, effettua richieste usando un endpoint dell'account di archiviazione fornito dal registro di sistema.

Se il registro di sistema è con [replica geografica](container-registry-geo-replication.md), un client potrebbe dover interagire con l'endpoint dati in un'area specifica o in più aree replicate.

## <a name="allow-access-to-rest-and-data-endpoints"></a>Consentire l'accesso agli endpoint REST e dati

* **Endpoint REST** : consente di accedere al nome completo del server di accesso al `<registry-name>.azurecr.io`registro di sistema, o a un intervallo di indirizzi IP associato
* **Endpoint di archiviazione (dati)** : consentire l'accesso a tutti gli account di archiviazione BLOB `*.blob.core.windows.net`di Azure usando il carattere jolly o un intervallo di indirizzi IP associato.
> [!NOTE]
> Azure Container Registry introduce [endpoint di dati dedicati](#enable-dedicated-data-endpoints-preview) (anteprima), consentendo di definire rigorosamente l'ambito delle regole del firewall del client per l'archiviazione del registro. Facoltativamente, abilitare gli endpoint dati in tutte le aree in cui il registro di sistema viene individuato o `<registry-name>.<region>.data.azurecr.io`replicato usando il modulo.

## <a name="allow-access-by-ip-address-range"></a>Consenti l'accesso in base all'intervallo di indirizzi IP

Se l'organizzazione dispone di criteri per consentire l'accesso solo a indirizzi IP o intervalli di indirizzi specifici, scaricare gli [intervalli IP di Azure e i tag di servizio-cloud pubblico](https://www.microsoft.com/download/details.aspx?id=56519).

Per trovare gli intervalli IP dell'endpoint REST di ACR per i quali è necessario consentire l'accesso, cercare **AzureContainerRegistry** nel file JSON.

> [!IMPORTANT]
> Gli intervalli di indirizzi IP per i servizi di Azure possono cambiare e gli aggiornamenti vengono pubblicati settimanalmente. Scaricare regolarmente il file JSON e apportare gli aggiornamenti necessari nelle regole di accesso. Se lo scenario prevede la configurazione delle regole del gruppo di sicurezza di rete in una rete virtuale di Azure o si usa il firewall di Azure, usare invece il [tag del servizio](#allow-access-by-service-tag) **AzureContainerRegistry** .
>

### <a name="rest-ip-addresses-for-all-regions"></a>Indirizzi IP REST per tutte le aree

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>Indirizzi IP REST per un'area specifica

Cercare l'area specifica, ad esempio **AzureContainerRegistry. AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Indirizzi IP di archiviazione per tutte le aree

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Indirizzi IP di archiviazione per aree specifiche

Cercare l'area specifica, ad esempio **storage. AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Consenti l'accesso in base al tag del servizio

In una rete virtuale di Azure, usare le regole di sicurezza di rete per filtrare il traffico da una risorsa, ad esempio una macchina virtuale, a un registro contenitori. Per semplificare la creazione delle regole di rete di Azure, usare il [tag del servizio](../virtual-network/security-overview.md#service-tags) **AzureContainerRegistry** . Un tag di servizio rappresenta un gruppo di prefissi di indirizzi IP per accedere a un servizio di Azure a livello globale o per area di Azure. Il tag viene aggiornato automaticamente in caso di modifica degli indirizzi. 

Ad esempio, creare una regola del gruppo di sicurezza di rete in uscita con **AzureContainerRegistry** di destinazione per consentire il traffico verso un registro contenitori di Azure. Per consentire l'accesso al tag del servizio solo in un'area specifica, specificare l'area nel formato seguente: **AzureContainerRegistry**. [*nome area*].

## <a name="enable-dedicated-data-endpoints-preview"></a>Abilita endpoint di dati dedicati (anteprima)

> [!WARNING]
> Se in precedenza è stato configurato l'accesso al firewall `*.blob.core.windows.net` client agli endpoint esistenti, il cambio di endpoint di dati dedicati influirà sulla connettività client, causando errori di pull. Per assicurarsi che i client dispongano di accesso coerente, aggiungere le nuove regole dell'endpoint dei dati alle regole del firewall del client. Al termine, abilitare gli endpoint dati dedicati per i registri usando l'interfaccia della riga di comando di Azure o altri strumenti.

Gli endpoint di dati dedicati sono una funzionalità facoltativa del livello di servizio del registro contenitori **Premium** . Per informazioni sui limiti e i livelli di servizio del registro di sistema, vedere [livelli di container Registry di Azure](container-registry-skus.md). Per abilitare gli endpoint dati usando l'interfaccia della riga di comando di Azure, usare l'interfaccia della riga di comando di Azure versione 2.4.0 Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Il comando [AZ ACR Update][az-acr-update] seguente abilita gli endpoint dati dedicati in *un registro di*sistema del registro di sistema. A scopo dimostrativo, si supponga che il registro di sistema venga replicato in due aree:

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Gli endpoint dati usano un modello a livello di `<registry-name>.<region>.data.azurecr.io`area,. Per visualizzare gli endpoint dati, usare il comando [AZ ACR Show-Endpoints][az-acr-show-endpoints] :

```azurecli
az acr show-endpoints --name myregistry
```

Output:

```
{
    "loginServer": "myregistry.azurecr.io",
    "dataEndpoints": [
        {
            "region": "eastus",
            "endpoint": "myregistry.eastus.data.azurecr.io",
        },
        {
            "region": "westus",
            "endpoint": "myregistry.westus.data.azurecr.io",
        }
    ]
}
```

Dopo aver configurato gli endpoint dati dedicati per il registro, è possibile abilitare le regole di accesso del firewall client per gli endpoint dati. Abilitare le regole di accesso dell'endpoint dati per tutte le aree del registro di sistema necessarie.

## <a name="configure-client-firewall-rules-for-mcr"></a>Configurare le regole del firewall per il client

Se è necessario accedere a Microsoft Container Registry () da dietro un firewall, vedere le indicazioni per configurare [le regole del firewall](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)per i client. Il registro di sistema principale per tutte le immagini Docker pubblicate da Microsoft, ad esempio le immagini di Windows Server.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [procedure consigliate di Azure per la sicurezza di rete](../security/fundamentals/network-best-practices.md)

* Altre informazioni sui [gruppi di sicurezza](/azure/virtual-network/security-overview) in una rete virtuale di Azure

* Scopri di più sugli [endpoint dati dedicati](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) per Azure container Registry



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints

