---
title: Connettersi ad Azure Arc Istanza gestita SQL abilitato
description: Connettersi ad Azure Arc Istanza gestita SQL abilitato
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3277dc4d9c4485b117bfcfd1d6e130e7370cd8c2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939195"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>Connettersi ad Azure Arc Istanza gestita SQL abilitato

Questo articolo illustra come connettersi al Istanza gestita SQL abilitato per Azure Arc. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-azure-arc-enabled-sql-managed-instances"></a>Visualizza istanze gestite di SQL Azure Arc abilitate

Per visualizzare la Istanza gestita SQL abilitata per Azure Arc e gli endpoint esterni, usare il comando seguente:

```console
azdata arc sql mi list
```

L'output dovrebbe essere simile al seguente:

```console
Name    Replicas    ExternalEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Se si usa AKS o kubeadm o OpenShift e così via, è possibile copiare il numero di porta e l'indirizzo IP esterno da qui e connettersi usando lo strumento preferito per la connessione a un'istanza di SQL Server/Azure SQL, ad esempio Azure Data Studio o SQL Server Management Studio.  Tuttavia, se si usa la VM di avvio rapido, vedere di seguito per informazioni speciali su come connettersi a tale macchina virtuale dall'esterno di Azure. 

> [!NOTE]
> È possibile che i criteri aziendali blocchino l'accesso all'IP e alla porta, soprattutto se questa viene creata nel cloud pubblico.

## <a name="connect"></a>Connessione 

Connettersi con Azure Data Studio, SQL Server Management Studio o SQLCMD

Aprire Azure Data Studio e connettersi all'istanza di con l'indirizzo IP e il numero di porta dell'endpoint esterno. Se si usa una macchina virtuale di Azure, sarà necessario l'indirizzo IP _pubblico_ , che è identificabile usando la [Nota speciale sulle distribuzioni delle macchine virtuali di Azure](#special-note-about-azure-virtual-machine-deployments).

Ad esempio:

- Server: 52.229.9.30, 30913
- Nome utente: SA
- Password: password SQL specificata al momento del provisioning

> [!NOTE]
> È possibile usare Azure Data Studio [visualizzare i dashboard dell'istanza gestita di SQL](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards).

Per connettersi usando SQLCMD o Linux o Windows, è possibile usare un comando simile al seguente. Immettere la password SQL quando richiesto:

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Nota speciale sulle distribuzioni delle macchine virtuali di Azure

Se si usa una macchina virtuale di Azure, l'indirizzo IP dell'endpoint non visualizzerà l'indirizzo IP pubblico. Per individuare l'indirizzo IP esterno, utilizzare il comando seguente:

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Sarà quindi possibile combinare l'indirizzo IP pubblico con la porta per effettuare la connessione.

Potrebbe inoltre essere necessario esporre la porta dell'istanza di SQL tramite il gateway di sicurezza di rete (NSG). Per consentire il traffico tramite (NSG), è necessario aggiungere una regola che è possibile eseguire usando il comando seguente.

Per impostare una regola, è necessario conoscere il nome del NSG, che è possibile trovare usando il comando seguente:

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Una volta ottenuto il nome del NSG, è possibile aggiungere una regola del firewall usando il comando seguente. I valori di esempio qui creano una regola NSG per la porta 30913 e consentono la connessione da **qualsiasi** indirizzo IP di origine.  Non si tratta di una procedura consigliata per la sicurezza.  È possibile bloccare meglio gli elementi specificando un valore-Source-prefix-prefissi specifico per l'indirizzo IP del client o un intervallo di indirizzi IP che includa gli indirizzi IP del team o dell'organizzazione.

Sostituire il valore del `--destination-port-ranges` parametro seguente con il numero di porta ottenuto nel `azdata sql instance list` comando F riportato sopra.

```console
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare i dashboard dell'istanza gestita di SQL](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [Visualizza Istanza gestita SQL nell'portale di Azure](view-arc-data-services-inventory-in-azure-portal.md)
