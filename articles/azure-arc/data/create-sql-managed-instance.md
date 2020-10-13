---
title: Creare un'istanza gestita di SQL di Azure in Azure Arc
description: Creare un'istanza gestita di SQL di Azure in Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2a931b1a3c3f88af1abec4fd1810aae09c849c48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940854"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Creare un'istanza gestita di SQL di Azure in Azure Arc

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="login-to-the-azure-arc-data-controller"></a>Accedere al controller dati di Azure Arc

Prima di poter creare un'istanza, accedere al controller dati di Azure Arc se non è già stato effettuato l'accesso.

```console
azdata login
```

Verranno quindi richiesti il nome utente, la password e lo spazio dei nomi System.  

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-sql-managed-instance"></a>Creare un'istanza gestita di SQL di Azure

Per visualizzare le opzioni di creazione disponibili SQL Istanza gestita, utilizzare il comando seguente:
```console
azdata arc sql mi create --help
```

Per creare un Istanza gestita SQL, usare il comando seguente:

```console
azdata arc sql mi create -n <instanceName> --storage-class-data <storage class> --storage-class-logs <storage class>
```

Esempio:

```console
azdata arc sql mi create -n sqldemo --storage-class-data managed-premium --storage-class-logs managed-premium
```
> [!NOTE]
>  I nomi devono avere una lunghezza inferiore a 13 caratteri e devono essere conformi alle [convenzioni di denominazione DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)
>
>  Quando si specifica l'allocazione di memoria e l'allocazione vCore, usare questa formula per assicurarsi che la creazione abbia esito positivo. per ogni vCore è necessario almeno 4 GB di RAM di capacità disponibile nel nodo Kubernetes in cui verrà eseguito il Pod dell'istanza gestita di SQL.
>
>  Quando si crea un'istanza di SQL, non usare maiuscole nel nome se si esegue il provisioning in Azure
>
>  Per elencare le classi di archiviazione disponibili nell'esecuzione del cluster Kubernetes `kubectl get storageclass` 


> [!NOTE]
> Se si desidera automatizzare la creazione di istanze di SQL ed evitare la richiesta interattiva per la password amministratore, è possibile impostare le `AZDATA_USERNAME` `AZDATA_PASSWORD` variabili di ambiente e sul nome utente e la password desiderati prima di eseguire il `azdata arc sql mi create` comando.
> 
>  Se il controller dati è stato creato usando AZDATA_USERNAME e AZDATA_PASSWORD nella stessa sessione terminal, verranno usati i valori per AZDATA_USERNAME e AZDATA_PASSWORD per creare anche l'istanza gestita di SQL.

> [!NOTE]
> La creazione di Istanza gestita SQL di Azure non registrerà le risorse in Azure. I passaggi per registrare la risorsa sono disponibili negli articoli seguenti: 
> - [Visualizzare i log e le metriche con Kibana e Grafana](monitor-grafana-kibana.md)
> - [Caricare i dati di fatturazione in Azure e visualizzarli nel portale di Azure](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Visualizza istanza in Azure Arc

Per visualizzare l'istanza di, usare il comando seguente:

```console
azdata arc sql mi list
```

L'output dovrebbe essere simile al seguente:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Se si usa AKS o `kubeadm` OpenShift e così via, è possibile copiare il numero di porta e l'indirizzo IP esterno da qui e connettersi usando lo strumento preferito per la connessione a un'istanza di SQL Server/SQL di Azure, ad esempio Azure Data Studio o SQL Server Management Studio. Tuttavia, se si usa la VM di avvio rapido, vedere l'articolo [connettersi ad Azure Arc Enabled SQL istanza gestita](connect-managed-instance.md) per istruzioni speciali.


## <a name="next-steps"></a>Passaggi successivi
- [Connettersi ad Azure Arc Istanza gestita SQL abilitato](connect-managed-instance.md)
- [Registrare l'istanza con Azure e caricare le metriche e i log relativi all'istanza](upload-metrics-and-logs-to-azure-monitor.md)
- [Distribuire un'istanza gestita di SQL di Azure con Azure Data Studio](create-sql-managed-instance-azure-data-studio.md)
