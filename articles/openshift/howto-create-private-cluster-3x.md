---
title: Creare un cluster privato con Azure Red Hat OpenShift 3.11 | Microsoft Docs
description: Creare un cluster privato con Azure Red Hat OpenShift 3.11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, private cluster, red hat
ms.openlocfilehash: f4ce6c79fa9fe6d05fdea4b877a8aa7faf404a9b
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727449"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Creare un cluster privato con Azure Red Hat OpenShift 3.11

I cluster privati offrono i seguenti vantaggi:

* I cluster privati non espongono i componenti del piano di controllo cluster, come ad esempio i server API, in un indirizzo IP pubblico.
* La rete virtuale di un cluster privato può essere configurata dai clienti, consentendo l'eventuale peering con altre reti virtuali, inclusi gli ambienti ExpressRoute. È anche possibile configurare un DNS personalizzato nella rete virtuale per l'integrazione con servizi interni.

## <a name="before-you-begin"></a>Prima di iniziare

I campi contenuti nel seguente frammento di configurazione sono nuovi e devono essere inclusi nella configurazione del cluster. `managementSubnetCidr` deve trovarsi all'interno della rete virtuale del cluster e viene usato da Azure per gestire il cluster.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Un cluster privato può essere distribuito tramite gli script di esempio indicati di seguito. Una volta distribuito il cluster, eseguire il comando `cluster get` e visualizzare la proprietà `properties.FQDN` per determinare l'indirizzo IP privato del server dell'API OpenShift.

La rete virtuale del cluster verrà creata con determinate autorizzazioni, in modo che sia possibile modificarla. Sarà quindi possibile configurare la rete per accedere alla rete virtuale (ExpressRoute, VPN, peering di rete virtuale) in base alle esigenze.

Se si modificano i server dei nomi DNS nella rete virtuale del cluster, sarà necessario eseguire un aggiornamento nel cluster con la proprietà `properties.RefreshCluster` impostata su `true`, in modo che sia possibile ricreare l'immagine delle macchine virtuali. Questo aggiornamento consentirà di scegliere i nuovi server dei nomi.

## <a name="sample-configuration-scripts"></a>Script di configurazione di esempio

Per configurare e distribuire il cluster privato, usare gli script di esempio riportati in questa sezione.

### <a name="environment"></a>Environment

Specificare le variabili di ambiente seguenti in modo simile a quando si usano i propri valori.

> [!NOTE]
> Il percorso deve essere impostato su `eastus2` perché si tratta attualmente dell'unica posizione supportata per i cluster privati.

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>private-cluster.json

Usando le variabili di ambiente definite in precedenza, di seguito si riporta una configurazione del cluster di esempio con cluster privato abilitato.

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>Distribuire un cluster privato

Dopo aver configurato il cluster privato con gli script di esempio precedenti, eseguire il comando seguente per distribuirlo.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come accedere alla console OpenShift, vedere [Procedura dettagliata della console Web](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).
