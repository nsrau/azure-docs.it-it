---
title: Regole del firewall per accedere ad Azure Container Registry
description: Configurare le regole per accedere a un registro contenitori di Azure da dietro un firewall.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/17/2019
ms.author: danlep
ms.openlocfilehash: 285d5592ce667f7b4d84f26d67a00af0698c2967
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620961"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Configurare le regole per accedere a un registro contenitori di Azure dietro un firewall

Questo articolo illustra come configurare le regole nel firewall per consentire l'accesso a un registro contenitori di Azure. Ad esempio, un dispositivo Azure IoT Edge dietro un firewall o un server proxy potrebbe dover accedere a un registro contenitori per eseguire il pull di un'immagine del contenitore. In alternativa, un server bloccato in una rete locale potrebbe avere l'esigenza di accedere per eseguire il push di un'immagine.

Se invece si vuole configurare le regole di accesso alla rete in ingresso in un registro contenitori per consentire l'accesso solo all'interno di una rete virtuale di Azure o di un intervallo di indirizzi IP pubblici, vedere [limitare l'accesso a un registro contenitori di Azure da una rete virtuale](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>Informazioni sugli endpoint del registro di sistema

Per eseguire il pull o il push di immagini o altri artefatti in un registro contenitori di Azure, un client come un daemon Docker deve interagire con HTTPS con due endpoint distinti.

* **Endpoint API REST del registro** di sistema: le operazioni di autenticazione e gestione del registro di sistema vengono gestite tramite l'endpoint API REST pubblico del registro di sistema. Questo endpoint è l'URL del server di accesso del registro di sistema o un intervallo di indirizzi IP associato. 

* **Endpoint di archiviazione** : Azure [alloca l'archiviazione BLOB](container-registry-storage.md) negli account di archiviazione di Azure per conto di ogni registro di sistema per gestire le immagini del contenitore e altri artefatti. Quando un client accede a livelli immagine in un registro contenitori di Azure, effettua richieste usando un endpoint dell'account di archiviazione fornito dal registro di sistema.

Se il registro di sistema è con [replica geografica](container-registry-geo-replication.md), un client potrebbe dover interagire con gli endpoint REST e di archiviazione in un'area specifica o in più aree replicate.

## <a name="allow-access-to-rest-and-storage-urls"></a>Consenti l'accesso agli URL REST e di archiviazione

* **Endpoint REST** : consente l'accesso all'URL del server del registro di sistema, ad esempio`myregistry.azurecr.io`
* **Endpoint di archiviazione** : consentire l'accesso a tutti gli account di archiviazione BLOB di Azure usando il carattere jolly`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Consenti l'accesso in base all'intervallo di indirizzi IP

Se è necessario consentire l'accesso a indirizzi IP specifici, scaricare gli [intervalli IP di Azure e i tag del servizio-cloud pubblico](https://www.microsoft.com/download/details.aspx?id=56519).

Per trovare gli intervalli IP dell'endpoint REST di ACR, cercare **AzureContainerRegistry** nel file JSON.

> [!IMPORTANT]
> Gli intervalli di indirizzi IP per i servizi di Azure possono cambiare e gli aggiornamenti vengono pubblicati settimanalmente. Scaricare regolarmente il file JSON e apportare gli aggiornamenti necessari nelle regole di accesso. Se lo scenario prevede la configurazione delle regole del gruppo di sicurezza di rete in una rete virtuale di Azure per accedere ad Azure Container Registry, usare invece il [tag del servizio](#allow-access-by-service-tag) **AzureContainerRegistry** .
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

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [procedure consigliate di Azure per la sicurezza di rete](../security/azure-security-network-security-best-practices.md)

* Altre informazioni sui [gruppi di sicurezza](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview.md) in una rete virtuale di Azure



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

