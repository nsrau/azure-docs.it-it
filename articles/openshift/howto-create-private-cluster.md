---
title: Creare un cluster privato con Azure Red Hat OpenShift 3.11 Documenti Microsoft
description: Creare un cluster privato con Azure Red Hat OpenShift 3.11Create a private cluster with Azure Red Hat OpenShift 3.11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, cluster privato, cappello rosso
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399419"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Creare un cluster privato con Azure Red Hat OpenShift 3.11Create a private cluster with Azure Red Hat OpenShift 3.11

> [!IMPORTANT]
> I cluster privati Azure Red Hat OpenShift (ARO) sono attualmente disponibili solo in anteprima privata negli Stati Uniti orientali 2. L'accettazione dell'anteprima privata è solo su invito. Assicurati di registrare l'abbonamento prima di tentare di abilitare questa funzionalità.

I cluster privati offrono i vantaggi seguenti:Private clusters provide the following benefits:

* I cluster privati non espongono i componenti del piano di controllo del cluster (ad esempio i server API) su un indirizzo IP pubblico.
* La rete virtuale di un cluster privato è configurabile dai clienti, consentendo di configurare la rete per consentire il peering con altre reti virtuali, inclusi gli ambienti ExpressRoute.The virtual network of a private cluster is configurable by customers, allowing you to set up networking to allow peering with other virtual networks, including ExpressRoute environments. È inoltre possibile configurare DNS personalizzato nella rete virtuale per l'integrazione con i servizi interni.

## <a name="before-you-begin"></a>Prima di iniziare

> [!NOTE]
> Questa funzionalità richiede la versione 2019-10-27-anteprima dell'API HTTP ARO. Non è ancora supportato nell'interfaccia della riga di comando di Azure.It is not yet supported in the Azure CLI.

I campi nel frammento di configurazione seguente sono nuovi e devono essere inclusi nella configurazione del cluster. `managementSubnetCidr`deve trovarsi all'interno della rete virtuale del cluster e viene usato da Azure per gestire il cluster.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Un cluster privato può essere distribuito utilizzando gli script di esempio forniti di seguito. Una volta distribuito il `cluster get` cluster, `properties.FQDN` eseguire il comando e visualizzare la proprietà per determinare l'indirizzo IP privato del server API OpenShift.

La rete virtuale del cluster verrà creata con autorizzazioni in modo che sia possibile modificarla. È quindi possibile configurare la rete per accedere alla rete virtuale (ExpressRoute, VPN, peering della rete virtuale) in base alle proprie esigenze.

Se si modificano i server dei nomi DNS nella rete virtuale del cluster, sarà necessario eseguire un aggiornamento nel cluster con la `properties.RefreshCluster` proprietà impostata su `true` in modo che le macchine virtuali possano essere ricreate. Questo aggiornamento consentirà loro di prelevare i nuovi server dei nomi.

## <a name="sample-configuration-scripts"></a>Script di configurazione di esempio

Usare gli script di esempio in questa sezione per configurare e distribuire il cluster privato.

### <a name="environment"></a>Environment

Compilare le variabili di ambiente riportate di seguito come utilizzando i propri valori.

> [!NOTE]
> Il percorso deve `eastus2` essere impostato su poiché attualmente è l'unica posizione supportata per i cluster privati.

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

Utilizzando le variabili di ambiente definite in precedenza, ecco una configurazione cluster di esempio con cluster privato abilitato.

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

## <a name="deploy-a-private-cluster"></a>Distribuire un cluster privatoDeploy a private cluster

Dopo aver configurato il cluster privato con gli script di esempio precedenti, eseguire il comando seguente per distribuire il cluster privato.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come accedere alla console OpenShift, vedere Procedura dettagliata della [console Web](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).
