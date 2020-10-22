---
title: Visualizzare i log e le metriche con Kibana e Grafana
description: Visualizzare i log e le metriche con Kibana e Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3693c30a34601512770f5d9071f5d786410fb00e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360378"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Visualizzare i log e le metriche con Kibana e Grafana

I dashboard Web Kibana e Grafana offrono informazioni e chiarezza sugli spazi dei nomi Kubernetes usati dai servizi dati abilitati per Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="retrieve-the-ip-address-of-your-cluster"></a>Recuperare l'indirizzo IP del cluster

Per accedere ai dashboard, sarà necessario recuperare l'indirizzo IP del cluster. Il metodo per il recupero dell'indirizzo IP corretto varia a seconda del modo in cui si è scelto di distribuire Kubernetes. Scorrere le opzioni seguenti per trovare quello più adatto.

### <a name="azure-virtual-machine"></a>Macchina virtuale di Azure

per recuperare l'indirizzo IP pubblico, usare il comando seguente:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

### <a name="kubeadm-cluster"></a>Cluster Kubeadm

Per recuperare l'indirizzo IP del cluster, usare il comando seguente:

```console
kubectl cluster-info
```


### <a name="aks-or-other-load-balanced-cluster"></a>AKS o altro cluster con bilanciamento del carico

Per monitorare l'ambiente in AKS o in un altro cluster con bilanciamento del carico, è necessario ottenere l'indirizzo IP del servizio proxy di gestione. Usare questo comando per recuperare l'indirizzo **IP esterno** :

```console
kubectl get svc mgmtproxy-svc-external -n <namespace>

#Example:
#kubectl get svc mgmtproxy-svc-external -n arc
NAME                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)           AGE
mgmtproxy-svc-external   LoadBalancer   10.0.186.28   52.152.148.25   30777:30849/TCP   19h
```

## <a name="additional-firewall-configuration"></a>Configurazione aggiuntiva del firewall

Potrebbe essere necessario aprire le porte nel firewall per accedere agli endpoint Kibana e Grafana.

Di seguito è riportato un esempio di come eseguire questa operazione per una macchina virtuale di Azure. È necessario eseguire questa operazione se Kubernetes è stato distribuito con lo script.

I passaggi seguenti evidenziano come creare una regola NSG per gli endpoint Kibana e Grafana:

### <a name="find-the-name-of-the-nsg"></a>Trovare il nome del gruppo di sicurezza di rete

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>Aggiungere la regola NSG

Una volta ottenuto il nome del NSG, è possibile aggiungere una regola usando il comando seguente:

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```

## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Monitorare le istanze gestite di SQL Azure in Azure Arc

Ora che si dispone dell'indirizzo IP ed è stata esposta la porta, è necessario poter accedere a Grafana e Kibana.

> [!NOTE]
>  Quando viene richiesto di immettere un nome utente e una password, immettere il nome utente e la password specificati al momento della creazione del controller di dati di Azure Arc.

> [!NOTE]
>  Verrà visualizzato un avviso di certificato perché i certificati usati in anteprima sono certificati autofirmati.

Usare il modello di URL seguente per accedere ai dashboard di registrazione e monitoraggio per l'istanza gestita di SQL di Azure:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

I dashboard pertinenti sono:

* "Metriche dell'istanza gestita di SQL di Azure"
* "Metriche del nodo host"
* "Metriche di Pod host"

## <a name="monitor-azure-database-for-postgresql-hyperscale---azure-arc"></a>Monitorare l'iperscalabilità di database di Azure per PostgreSQL-Azure Arc

Usare il modello di URL seguente per accedere ai dashboard di registrazione e monitoraggio per l'iperscalabilità PostgreSQL:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

I dashboard pertinenti sono:

* "Metriche postgres"
* "Metriche tabella postgres"
* "Metriche del nodo host"
* "Metriche di Pod host"

## <a name="next-steps"></a>Passaggi successivi
- Provare [a caricare metriche e log in monitoraggio di Azure](upload-metrics-and-logs-to-azure-monitor.md)
- Leggere le informazioni su Grafana:
   - [Introduzione](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Nozioni fondamentali su Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Esercitazioni su Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Scopri di più su Kibana
   - [Introduzione](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Guida a Kibana](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Introduzione ai dashboard DrillDown con visualizzazioni dei dati in Kibana](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Come compilare dashboard Kibana](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

