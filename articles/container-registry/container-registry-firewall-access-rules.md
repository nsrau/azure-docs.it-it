---
title: Regole di accesso al firewall
description: Configurare le regole per accedere a un registro contenitori di Azure da dietro un firewall, consentendo l'accesso (inserimento in un "elenco elementi consentiti") all'API REST e ai nomi di dominio dell'endpoint dati o agli intervalli di indirizzi IP specifici del servizio.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 4774191087b3e88e657e8dc9bc6a9f9c406153e2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849987"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Configurare le regole per accedere a un registro contenitori di Azure dietro un firewall

Questo articolo illustra come configurare le regole nel firewall per consentire l'accesso a un registro contenitori di Azure. Ad esempio, un dispositivo Azure IoT Edge dietro un firewall o un server proxy potrebbe dover accedere a un registro contenitori per eseguire il pull di un'immagine del contenitore. In alternativa, un server bloccato in una rete locale potrebbe dover accedere per eseguire il push di un'immagine.

Se invece si vuole configurare l'accesso alla rete in ingresso a un registro contenitori solo all'interno di una rete virtuale di Azure, vedere [Configurare un collegamento privato di Azure per un registro contenitori di Azure](container-registry-private-link.md).

## <a name="about-registry-endpoints"></a>Informazioni sugli endpoint del registro contenitori

Per eseguire il pull o il push di immagini o altri artefatti in un registro contenitori di Azure, un client come un daemon Docker deve interagire su HTTPS con due endpoint distinti. Per i client che accedono a un registro da dietro un firewall, è necessario configurare le regole di accesso per entrambi gli endpoint. Entrambi gli endpoint vengono raggiunti sulla porta 443.

* **Endpoint API REST del registro contenitori**: le operazioni di autenticazione e gestione del registro contenitori vengono gestite tramite l'endpoint API REST pubblico del registro. Questo endpoint è il nome del server di accesso del registro. Esempio: `myregistry.azurecr.io`

* **Endpoint di archiviazione (dati)** : Azure [alloca l'archiviazione BLOB](container-registry-storage.md) negli account di archiviazione di Azure per conto di ogni registro per gestire i dati per le immagini del contenitore e altri artefatti. Quando un client accede a livelli immagine in un registro contenitori di Azure, effettua richieste usando un endpoint dell'account di archiviazione fornito dal registro.

Se si tratta di un registro [con replica geografica](container-registry-geo-replication.md), un client potrebbe dover interagire con l'endpoint dati in un'area specifica o in più aree replicate.

## <a name="allow-access-to-rest-and-data-endpoints"></a>Consentire l'accesso agli endpoint REST e di dati

* **Endpoint REST**: consentire l'accesso al nome del server di accesso completo al registro `<registry-name>.azurecr.io` o a un intervallo di indirizzi IP associato
* **Endpoint di archiviazione (dati)** : consentire l'accesso a tutti gli account di archiviazione BLOB di Azure usando il carattere jolly `*.blob.core.windows.net` o un intervallo di indirizzi IP associato.
> [!NOTE]
> Registro Azure Container introduce [endpoint di dati dedicati](#enable-dedicated-data-endpoints), consentendo di definire rigorosamente l'ambito delle regole del firewall del client per l'archiviazione del registro. Facoltativamente, è possibile abilitare gli endpoint di dati in tutte le aree in cui il registro viene individuato o replicato usando il modulo `<registry-name>.<region>.data.azurecr.io`.

## <a name="allow-access-by-ip-address-range"></a>Consentire l'accesso in base all'indirizzo IP

Se l'organizzazione dispone di criteri per consentire l'accesso solo a indirizzi IP o intervalli di indirizzi specifici, scaricare il documento [Tag di servizio e intervalli IP di Azure: cloud pubblico](https://www.microsoft.com/download/details.aspx?id=56519).

Per trovare gli intervalli IP dell'endpoint REST del Registro Azure Container per i quali è necessario consentire l'accesso, cercare **AzureContainerRegistry** nel file JSON.

> [!IMPORTANT]
> Gli intervalli di indirizzi IP per i servizi di Azure possono cambiare e gli aggiornamenti vengono pubblicati settimanalmente. Scaricare regolarmente il file JSON e apportare gli aggiornamenti necessari nelle regole di accesso. Se lo scenario prevede la configurazione di regole del gruppo di sicurezza di rete in una rete virtuale di Azure o si usa il Firewall di Azure, usare invece il [tag di servizio](#allow-access-by-service-tag) **AzureContainerRegistry**.
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

### <a name="storage-ip-addresses-for-specific-regions"></a>Indirizzi IP di archiviazione per aree specifiche

Cercare l'area specifica, ad esempio **storage.AustraliaCentral**.

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

## <a name="allow-access-by-service-tag"></a>Consentire l'accesso in base ai tag del servizio

In una rete virtuale di Azure, usare le regole di sicurezza di rete per filtrare il traffico da una risorsa, ad esempio una macchina virtuale, a un registro contenitori. Per semplificare la creazione delle regole di rete di Azure, usare il [tag del servizio](../virtual-network/security-overview.md#service-tags) **AzureContainerRegistry**. Un tag di servizio rappresenta un gruppo di prefissi di indirizzi IP per accedere a un servizio di Azure a livello globale o per area di Azure. Il tag viene aggiornato automaticamente in caso di modifica degli indirizzi. 

Ad esempio, creare una regola del gruppo di sicurezza di rete in uscita con destinazione **AzureContainerRegistry** per consentire il traffico verso un registro contenitori di Azure. Per consentire l'accesso al tag del servizio solo in un'area specifica, specificare l'area nel formato seguente: **AzureContainerRegistry**.[*nome regione*].

## <a name="enable-dedicated-data-endpoints"></a>Abilitare endpoint dati dedicati 

> [!WARNING]
> Se in precedenza è stato configurato l'accesso del firewall client agli endpoint `*.blob.core.windows.net` esistenti, il passaggio a endpoint di dati dedicati influirà sulla connettività client, causando errori di pull. Per assicurarsi che i client dispongano di un accesso coerente, aggiungere le nuove regole dell'endpoint dei dati alle regole del firewall del client. Al termine, abilitare gli endpoint dati dedicati per i registri tramite l'interfaccia della riga di comando di Azure o altri strumenti.

Gli endpoint dati dedicati sono una funzionalità facoltativa del livello di servizio del registro contenitori **Premium**. Per informazioni sui livelli di servizio e sui limiti del registro contenitori, vedere [Livelli di servizio del Registro Azure Container](container-registry-skus.md). 

È possibile abilitare endpoint dati dedicati usando il portale di Azure o l'interfaccia della riga di comando di Azure. Gli endpoint dati seguono un modello a livello di area, `<registry-name>.<region>.data.azurecr.io`. In un registro con replica geografica, l'abilitazione degli endpoint dati abilita gli endpoint in tutte le aree di replica.

### <a name="portal"></a>Portale

Per abilitare gli endpoint dati tramite il portale:

1. Passare al registro contenitori.
1. Selezionare **Rete** > **Accesso pubblico**.
1. Selezionare la casella di controllo **Abilita endpoint dati dedicato**.
1. Selezionare **Salva**.

Gli endpoint dati vengono visualizzati nel portale.

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="Endpoint dati dedicati nel portale":::

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per abilitare gli endpoint dati tramite l'interfaccia della riga di comando di Azure, usare l'interfaccia della riga di comando di Azure versione 2.4.0 o superiore. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Il comando [az acr update][az-acr-update] seguente abilita gli endpoint dati dedicati in un registro *myregistry*. 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Per visualizzare gli endpoint dati, usare il comando [az acr show-endpoints][az-acr-show-endpoints]:

```azurecli
az acr show-endpoints --name myregistry
```

L'output a scopo dimostrativo mostra due endpoint regionali

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

Dopo avere configurato gli endpoint dati dedicati per il registro, è possibile abilitare le regole di accesso del firewall client per gli endpoint dati. Abilitare le regole di accesso dell'endpoint dati per tutte le aree del registro necessarie.

## <a name="configure-client-firewall-rules-for-mcr"></a>Configurare le regole del firewall per il client per MCR

Se è necessario accedere a Registro Azure Container Microsoft (MCR) da dietro un firewall, vedere le indicazioni per configurare le [regole del firewall del client MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). MCR è il registro di sistema principale per tutte le immagini Docker pubblicate da Microsoft, ad esempio le immagini di Windows Server.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [procedure consigliate di Azure per la sicurezza di rete](../security/fundamentals/network-best-practices.md)

* Altre informazioni sui [gruppi di sicurezza](/azure/virtual-network/security-overview) in una rete virtuale di Azure

* Altre informazioni sulla configurazione di un [collegamento privato](container-registry-private-link.md) per un registro contenitori

* Altre informazioni sugli [endpoint di dati dedicati](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) per Registro Azure Container



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints

