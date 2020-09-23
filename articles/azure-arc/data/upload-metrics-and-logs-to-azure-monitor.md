---
title: Caricare inventario delle risorse, dati di utilizzo, metriche e log in monitoraggio di Azure
description: Caricare inventario delle risorse, dati di utilizzo, metriche e log in monitoraggio di Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac6ffd2b5bf48079db6a0cd261dbe2535e1821ac
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939105"
---
# <a name="upload-resource-inventory-usage-data-metrics-and-logs-to-azure-monitor"></a>Caricare inventario delle risorse, dati di utilizzo, metriche e log in monitoraggio di Azure

Con i servizi dati di Azure Arc è *possibile caricare le* metriche e i log in monitoraggio di Azure in modo da poter aggregare e analizzare metriche, log, generare avvisi, inviare notifiche o attivare azioni automatizzate. L'invio dei dati a monitoraggio di Azure consente anche di archiviare i dati di monitoraggio e di log fuori sito e su vasta scala, abilitando l'archiviazione a lungo termine dei dati per l'analisi avanzata.  Se sono presenti più siti con Azure Arc Data Services, è possibile usare monitoraggio di Azure come posizione centralizzata per raccogliere tutti i log e le metriche nei siti.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Sono necessari alcuni passaggi di installazione monouso per abilitare gli scenari di caricamento dei log e delle metriche:

1) Creare un'applicazione di entità servizio/Azure Active Directory, tra cui la creazione di un segreto di accesso client e l'assegnazione dell'entità servizio al ruolo "server di pubblicazione di metriche di monitoraggio" nelle sottoscrizioni in cui si trovano le risorse dell'istanza del database.
2) Creare un'area di lavoro di log Analytics e ottenere le chiavi e impostare le informazioni nelle variabili di ambiente.

Il primo elemento è necessario per caricare le metriche e il secondo è necessario per caricare i log.

Seguire questi comandi per creare le metriche caricare un'entità servizio e assegnarla ai ruoli "monitoraggio delle metriche di pubblicazione" e "collaboratore" in modo che l'entità servizio possa caricare le metriche ed eseguire operazioni di creazione e caricamento.

## <a name="create-service-principal-and-assign-roles"></a>Creare un'entità servizio e assegnare ruoli

Seguire questi comandi per creare le metriche caricare un'entità servizio e assegnarla al ruolo "server di pubblicazione delle metriche di monitoraggio":

Per creare un'entità servizio, eseguire questo comando:

> [!NOTE]
> Per la creazione di un'entità servizio sono necessarie [determinate autorizzazioni in Azure](/active-directory/develop/howto-create-service-principal-portal#required-permissions).

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Output di esempio:

```console
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Salvare i valori appId e tenant in una variabile di ambiente da usare in un secondo momento:

```console
#PowerShell

$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Linux/macOS

export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Example (using Linux):
export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
```

Eseguire questo comando per assegnare l'entità servizio al ruolo "server di pubblicazione metriche di monitoraggio" nella sottoscrizione in cui si trovano le risorse dell'istanza del database:

```console
az role assignment create --assignee <appId value from output above> --role 'Monitoring Metrics Publisher' --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Monitoring Metrics Publisher' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Output di esempio:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Creare un'area di lavoro di log Analytics

Eseguire quindi questi comandi per creare un'area di lavoro Log Analytics e impostare le informazioni di accesso nelle variabili di ambiente.

> [!NOTE]
> Ignorare questo passaggio se si dispone già di un'area di lavoro.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --name MyLogsWorkpace
```

Output di esempio:

```console
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>Assegnare l'ID e la chiave condivisa alle variabili di ambiente

Salvare il customerId (ID area di lavoro) come variabile di ambiente da usare in un secondo momento:

```console
#PowerShell
$Env:WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Linux/macOS
export WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Example (using Linux)
#export WORKSPACE_ID='d6abb435-2626-4df1-b887-445fe44a4123'
```

Questo comando consente di stampare le chiavi di accesso necessarie per connettersi all'area di lavoro di log Analytics:

```console
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --name MyLogsWorkpace
```

Output di esempio:

```console
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Salvare la chiave primaria in una variabile di ambiente da usare in un secondo momento:

```console
#PowerShell:
$Env:WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Linux/macOS:
export WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Example (using Linux):
export WORKSPACE_SHARED_KEY='JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123=='

```

## <a name="set-final-environment-variables-and-confirm"></a>Imposta le variabili di ambiente finali e conferma

Impostare l'URL dell'autorità SPN in una variabile di ambiente:

```console
#PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'

#Linux/macOS:
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

Verificare che tutte le variabili di ambiente necessarie siano impostate se si desidera:

```console
#PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY

#Linux/macOS
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

## <a name="upload-metrics-to-azure-monitor"></a>Caricare le metriche in monitoraggio di Azure

Per caricare le metriche per le istanze gestite di SQL di Azure e i gruppi di server di scalabilità di database di Azure per PostgreSQL, eseguire i comandi seguenti dell'interfaccia della riga di comando

Tutte le metriche verranno esportate nel file specificato:

```console
azdata arc dc export -t metrics --path metrics.json
```

In questo modo le metriche vengono caricate in Monitoraggio di Azure:

```console
azdata arc dc upload --path metrics.json
```

## <a name="view-the-metrics-in-the-portal"></a>Visualizzare le metriche nel portale

Una volta caricate le metriche, sarà possibile visualizzarle dal portale di Azure.

Per visualizzare le metriche nel portale, usare questo collegamento speciale per aprire il portale: <https://portal.azure.com> quindi cercare l'istanza del database in base al nome nella barra di ricerca:

È possibile visualizzare l'utilizzo della CPU nella pagina panoramica oppure, se si desiderano metriche più dettagliate, è possibile fare clic su metriche nel riquadro di spostamento a sinistra

Scegliere SQL Server come spazio dei nomi della metrica:

Selezionare la metrica che si vuole visualizzare (è anche possibile selezionare più elementi):

Modificare la frequenza con gli ultimi 30 minuti:

> [!NOTE]
> È possibile caricare solo le metriche solo per gli ultimi 30 minuti. Monitoraggio di Azure rifiuta le metriche precedenti a 30 minuti.

## <a name="upload-logs-to-azure-monitor"></a>Caricare log in Monitoraggio di Azure

 Per caricare i log per le istanze gestite di SQL di Azure e i gruppi di server con iperscalabilità di database di Azure per PostgreSQL, eseguire i comandi CLI seguenti:

Tutti i log verranno esportati nel file specificato:

```console
azdata arc dc export -t logs --path logs.json
```

I log vengono caricati in un'area di lavoro di log Analytics di monitoraggio di Azure:

```console
azdata arc dc upload --path logs.json
```

## <a name="view-your-logs-in-azure-portal"></a>Visualizzare i log in portale di Azure

Dopo aver caricato i log, dovrebbe essere possibile eseguire una query su di essi usando la finestra di esplorazione delle query sui log nel modo seguente:

1. Aprire il portale di Azure, quindi cercare l'area di lavoro in base al nome nella barra di ricerca nella parte superiore e quindi selezionarla.
2. Fare clic su Log nel riquadro sinistro
3. Fare clic su Introduzione (oppure fare clic sui collegamenti nella pagina Introduzione per ulteriori informazioni sui Log Analytics se non si ha familiarità con esso).
4. Seguire l'esercitazione per altre informazioni su Log Analytics se è la prima volta
5. Espandere Log personalizzati nella parte inferiore dell'elenco delle tabelle; viene visualizzata una tabella denominata "sql_instance_logs_CL".
6. Fare clic sull'icona a forma di occhio accanto al nome della tabella
7. Fare clic sul pulsante "View in query editor" (Visualizza in editor di query)
8. A questo punto viene visualizzata una query nell'editor di query che mostra i 10 eventi più recenti nel log
9. Da qui è possibile provare a eseguire query sui log usando l'editor di query, impostare gli avvisi e così via.

## <a name="automating-metrics-and-logs-uploads-optional"></a>Automatizzazione delle metriche e dei caricamenti dei log (facoltativo)

Se si vuole caricare continuamente metriche e log, è possibile creare uno script ed eseguirlo su un timer ogni pochi minuti.  Di seguito è riportato un esempio di automazione dei caricamenti usando uno script della shell Linux.

Nell'editor di testo/codice preferito aggiungere quanto segue al contenuto dello script al file e salvarlo come file eseguibile di script, ad esempio. sh (Linux/Mac) o. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Rendere eseguibile il file di script

```console
chmod +x myuploadscript.sh
```

Eseguire lo script ogni 2 minuti:

```console
watch -n 120 ./myuploadscript.sh
```

È anche possibile usare un'utilità di pianificazione dei processi come cron o Windows Utilità di pianificazione o un agente di orchestrazione come Ansible, Puppet o chef.
