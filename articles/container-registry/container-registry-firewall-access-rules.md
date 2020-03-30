---
title: Regole di accesso al firewall
description: Configurare le regole per accedere a un registro di contenitori di Azure dall'esterno di un firewall, consentendo l'accesso ai nomi di dominio dell'endpoint di archiviazione e dell'API di archiviazione ("whitelisting") o agli intervalli di indirizzi IP specifici del servizio.
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77168011"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Configurare le regole per accedere a un registro di contenitori di Azure dietro un firewallConfigure rules to access an Azure container registry behind a firewall

Questo articolo illustra come configurare le regole nel firewall per consentire l'accesso a un registro dei contenitori di Azure.This article explains how to configure rules on your firewall to allow access to an Azure container registry. Ad esempio, un dispositivo Azure IoT Edge dietro un firewall o un server proxy potrebbe dover accedere a un registro contenitori per eseguire il pull di un'immagine del contenitore. In alternativa, un server bloccato in una rete locale potrebbe richiedere l'accesso per eseguire il push di un'immagine.

Se invece si desidera configurare le regole di accesso alla rete in ingresso in un registro contenitori solo all'interno di una rete virtuale di Azure o da un intervallo di indirizzi IP pubblici, vedere [Limitare l'accesso a un registro](container-registry-vnet.md)di contenitori di Azure da una rete virtuale.

## <a name="about-registry-endpoints"></a>Informazioni sugli endpoint del Registro di sistema

Per eseguire il pull o il push di immagini o altri elementi in un registro del contenitore di Azure, un client, ad esempio un daemon Docker, deve interagire tramite HTTPS con due endpoint distinti.

* **Endpoint dell'API REST del Registro** di sistema: le operazioni di autenticazione e gestione del Registro di sistema vengono gestite tramite l'endpoint dell'API REST pubblica del Registro di sistema. Questo endpoint è il nome del server di accesso del Registro di sistema o un intervallo di indirizzi IP associato. 

* **Endpoint di archiviazione:** Azure [alloca l'archiviazione BLOB](container-registry-storage.md) negli account di Archiviazione di Azure per conto di ogni Registro di sistema per gestire i dati per le immagini del contenitore e altri elementi. Quando un client accede ai livelli immagine in un registro contenitore di Azure, effettua richieste usando un endpoint dell'account di archiviazione fornito dal Registro di sistema.

Se il Registro di sistema è [replicato geograficamente,](container-registry-geo-replication.md)un client potrebbe dover interagire con gli endpoint REST e di archiviazione in un'area specifica o in più aree replicate.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Consentire l'accesso ai nomi di dominio REST e di archiviazioneAllow access to REST and storage domain names

* **Endpoint REST:** consente l'accesso al nome completo del server di accesso al Registro di sistema, ad esempio`myregistry.azurecr.io`
* **Endpoint di archiviazione (dati):** consentire l'accesso a tutti gli account di archiviazione BLOB di Azure usando il carattere jollyStorage (data) endpoint - Allow access to all Azure blob storage accounts using the wildcard`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Consenti accesso in base all'intervallo di indirizzi IP

Se l'organizzazione dispone di criteri per consentire l'accesso solo a specifici indirizzi IP o intervalli di indirizzi, scaricare [Intervalli di indirizzi IP di Azure e tag di servizio - Cloud pubblico](https://www.microsoft.com/download/details.aspx?id=56519).

Per trovare gli intervalli IP dell'endpoint REST ACR per i quali è necessario consentire l'accesso, cercare **AzureContainerRegistry** nel file JSON.

> [!IMPORTANT]
> Gli intervalli di indirizzi IP per i servizi di Azure possono cambiare e gli aggiornamenti vengono pubblicati settimanalmente. Scaricare regolarmente il file JSON e apportare gli aggiornamenti necessari nelle regole di accesso. Se lo scenario prevede la configurazione delle regole del gruppo di sicurezza di rete in una rete virtuale di Azure per accedere al Registro di sistema del contenitore di Azure, usare invece il tag del servizio **AzureContainerRegistry.If** your scenario involves configuring network security group rules in an Azure virtual network to access Azure Container Registry, use the AzureContainerRegistry [service tag](#allow-access-by-service-tag) instead.
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

Cercare l'area specifica, ad esempio **AzureContainerRegistry.AustraliaEast**.

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

### <a name="storage-ip-addresses-for-specific-regions"></a>Indirizzi IP di archiviazione per aree specificheStorage IP addresses for specific regions

Cercare l'area specifica, ad esempio **Storage.AustraliaCentral**.

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

## <a name="allow-access-by-service-tag"></a>Consenti accesso tramite tag di servizio

In una rete virtuale di Azure usare le regole di sicurezza di rete per filtrare il traffico da una risorsa, ad esempio una macchina virtuale a un registro contenitori. Per semplificare la creazione delle regole di rete di Azure, usare il tag del [servizio](../virtual-network/security-overview.md#service-tags) **AzureContainerRegistry.** Un tag di servizio rappresenta un gruppo di prefissi di indirizzi IP per accedere a un servizio di Azure a livello globale o per area di Azure.A service tag represents a group of IP address prefixes to access an Azure service globally or per Azure region. Il tag viene aggiornato automaticamente quando gli indirizzi cambiano. 

Ad esempio, creare una regola del gruppo di sicurezza di rete in uscita con AzureContainerRegistry di destinazione per consentire il traffico verso un registro contenitore di Azure.For example, create an outbound network security group rule with destination **AzureContainerRegistry** to allow traffic to an Azure container registry. Per consentire l'accesso al tag del servizio solo in un'area specifica, specificare l'area nel formato seguente: **AzureContainerRegistry**. [*nome area*].

## <a name="configure-client-firewall-rules-for-mcr"></a>Configurare le regole del firewall client per MCR

Se è necessario accedere a Microsoft Container Registry (MCR) da un firewall, vedere le indicazioni per configurare [le regole del firewall client MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). MCR è il Registro di sistema principale per tutte le immagini docker pubblicate da Microsoft, ad esempio le immagini di Windows Server.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle procedure consigliate di Azure per la sicurezza di [reteLearn](../security/fundamentals/network-best-practices.md) about Azure best practices for network security

* Altre informazioni sui gruppi di sicurezza in una rete virtuale di [AzureLearn](/azure/virtual-network/security-overview) more about security groups in an Azure virtual network



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

