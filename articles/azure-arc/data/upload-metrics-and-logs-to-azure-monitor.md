---
title: Caricare i dati di utilizzo, le metriche e i log in monitoraggio di Azure
description: Caricare l'inventario delle risorse, i dati di utilizzo, le metriche e i log in monitoraggio di Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d508e2e24ac35171d87cdba9cb79981dfb8764e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757470"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Caricare i dati di utilizzo, le metriche e i log in monitoraggio di Azure

Periodicamente è possibile esportare le informazioni di utilizzo per finalità di fatturazione, monitoraggio delle metriche e log e quindi caricarle in Azure.  L'esportazione e il caricamento di uno di questi tre tipi di dati creeranno e aggiorneranno anche le risorse del gruppo di server per il controller di dati, l'istanza gestita di SQL e l'iperscalabilità PostgreSQL in Azure.

> [!NOTE] 
Durante il periodo di anteprima, non è previsto alcun costo per l'uso di Azure Arc Enabled Data Services.

## <a name="prerequisites"></a>Prerequisiti

Sono necessarie l'interfaccia della riga di comando di Azure (AZ) e l'interfaccia della riga di comando di Azure Data (azdata).  [Installare gli strumenti](./install-client-tools.md)di.

Prima di caricare i dati in Azure, è necessario assicurarsi che la sottoscrizione di Azure disponga del provider di risorse Microsoft. AzureData registrato.

Per verificarlo, è possibile eseguire il comando seguente:

```console
az provider show -n Microsoft.AzureData -o table
```

Se il provider di risorse non è attualmente registrato nella sottoscrizione, è possibile registrarlo eseguendo il comando seguente.  Il completamento di questo comando potrebbe richiedere un minuto o due.

```console
az provider register -n Microsoft.AzureData --wait
```

## <a name="upload-usage-data"></a>Caricare i dati di utilizzo

Le informazioni sull'utilizzo, ad esempio l'inventario e l'utilizzo delle risorse, possono essere caricate in Azure nei due passaggi seguenti:

1. Esportare i dati di utilizzo usando il ```azdata export``` comando, come indicato di seguito:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the export command
   azdata arc dc export --type usage --path usage.json
   ```
   Questo comando crea un `usage.json` file con tutte le risorse di dati abilitate per Azure Arc, ad esempio le istanze gestite di SQL e le istanze di iperscala PostgreSQL e così via, create nel controller dei dati.

2. Caricare i dati di utilizzo usando il ```azdata upload``` comando

   > [!NOTE]
   > Attendere almeno 24 ore dopo aver creato il controller di dati di Azure Arc prima di eseguire il caricamento

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the upload command
   azdata arc dc upload --path usage.json
   ```

## <a name="upload-metrics-and-logs"></a>Caricare metriche e log

Con Azure Arc Data Services è possibile caricare le metriche e i log in monitoraggio di Azure in modo da poter aggregare e analizzare metriche, log, generare avvisi, inviare notifiche o attivare azioni automatiche. 

L'invio dei dati a monitoraggio di Azure consente anche di archiviare i dati di monitoraggio e di log fuori sede e su vasta scala, abilitando l'archiviazione a lungo termine dei dati per l'analisi avanzata.

Se sono presenti più siti con Azure Arc Data Services, è possibile usare monitoraggio di Azure come posizione centralizzata per raccogliere tutti i log e le metriche nei siti.

### <a name="before-you-begin"></a>Prima di iniziare

Sono necessari alcuni passaggi di installazione monouso per abilitare gli scenari di caricamento dei log e delle metriche:

1. Creare un'applicazione di entità servizio/Azure Active Directory, tra cui la creazione di un segreto di accesso client e l'assegnazione dell'entità servizio al ruolo "server di pubblicazione di metriche di monitoraggio" nelle sottoscrizioni in cui si trovano le risorse dell'istanza del database.
2. Creare un'area di lavoro di log Analytics e ottenere le chiavi e impostare le informazioni nelle variabili di ambiente.

Il primo elemento è necessario per caricare le metriche e il secondo è necessario per caricare i log.

Seguire questi comandi per creare le metriche caricare un'entità servizio e assegnarla ai ruoli "monitoraggio delle metriche di pubblicazione" e "collaboratore" in modo che l'entità servizio possa caricare le metriche ed eseguire operazioni di creazione e caricamento.

## <a name="create-service-principal-and-assign-roles"></a>Creare un'entità servizio e assegnare ruoli

Seguire questi comandi per creare le metriche caricare un'entità servizio e assegnarla al ruolo "server di pubblicazione delle metriche di monitoraggio":

Per creare un'entità servizio, eseguire questo comando:

> [!NOTE]
> Per la creazione di un'entità servizio sono necessarie [determinate autorizzazioni in Azure](/azure/active-directory/develop/howto-create-service-principal-portal#permissions-required-for-registering-an-app).

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Output di esempio:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Salvare i valori appId e tenant in una variabile di ambiente per usarli in seguito. 

Per salvare i valori appId e tenant con PowerShell, seguire questo esempio:

```powershell
$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'
```

In alternativa, in Linux o macOS è possibile salvare i valori appId e tenant con questo esempio:

   ```console
   export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

   #Example (using Linux):
   export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
   export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
   export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
   ```

Eseguire questo comando per assegnare l'entità servizio al ruolo "server di pubblicazione metriche di monitoraggio" nella sottoscrizione in cui si trovano le risorse dell'istanza del database:


> [!NOTE]
> Quando si esegue da un ambiente Windows, è necessario utilizzare le virgolette doppie per i nomi dei ruoli.


```console
az role assignment create --assignee <appId value from output above> --role "Monitoring Metrics Publisher" --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123

#On Windows environment
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Contributor" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Output di esempio:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Creare un'area di lavoro di log Analytics

Eseguire quindi questi comandi per creare un'area di lavoro Log Analytics e impostare le informazioni di accesso nelle variabili di ambiente.

> [!NOTE]
> Ignorare questo passaggio se si dispone già di un'area di lavoro.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Output di esempio:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
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
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
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

Per caricare le metriche per le istanze gestite di SQL Azure Arc abilitate e i gruppi di server con iperscalabilità di Azure Arc abilitati, eseguire i comandi dell'interfaccia della riga di comando seguenti:

1. Esporta tutte le metriche nel file specificato:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the metrics
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Caricare le metriche in monitoraggio di Azure:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #upload the metrics
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Attendere almeno 30 minuti dopo la creazione di istanze di dati abilitate per Azure Arc per il primo caricamento
   >
   >Assicurarsi che `upload` la metrica immediatamente dopo `export` come monitoraggio di Azure accetti solo le metriche per gli ultimi 30 minuti. [Scopri di più](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


Se viene visualizzato un errore che indica l'impossibilità di ottenere le metriche durante l'esportazione, verificare che la raccolta dati sia impostata su ```true``` eseguendo il comando seguente:

```console
azdata arc dc config show
```

vedere la sezione "sicurezza"

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Verificare se le `allowNodeMetricsCollection` `allowPodMetricsCollection` proprietà e sono impostate su `true` .

## <a name="view-the-metrics-in-the-portal"></a>Visualizzare le metriche nel portale

Una volta caricate le metriche, è possibile visualizzarle dalla portale di Azure.
> [!NOTE]
> Si noti che potrebbero essere necessari alcuni minuti prima che i dati caricati vengano elaborati prima di poter visualizzare le metriche nel portale.


Per visualizzare le metriche nel portale, usare questo collegamento per aprire il portale: <https://portal.azure.com> quindi cercare l'istanza del database in base al nome nella barra di ricerca:

È possibile visualizzare l'utilizzo della CPU nella pagina panoramica oppure, se si desiderano metriche più dettagliate, è possibile fare clic su metriche nel riquadro di spostamento a sinistra

Scegliere SQL Server come spazio dei nomi della metrica:

Selezionare la metrica che si vuole visualizzare (è anche possibile selezionare più elementi):

Modificare la frequenza con gli ultimi 30 minuti:

> [!NOTE]
> È possibile caricare solo le metriche solo per gli ultimi 30 minuti. Monitoraggio di Azure rifiuta le metriche precedenti a 30 minuti.

## <a name="upload-logs-to-azure-monitor"></a>Caricare log in Monitoraggio di Azure

 Per caricare i log per le istanze di SQL gestito abilitate per Azure Arc e i gruppi di server con iperscalabilità di PostgreSQL abilitati per AzureArc, eseguire i comandi CLI seguenti

1. Esporta tutti i log nel file specificato:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the logs
   azdata arc dc export --type logs --path logs.json
   ```

2. Caricare i log in un'area di lavoro di log Analytics di monitoraggio di Azure:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #Upload the logs
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Visualizzare i log in portale di Azure

Dopo aver caricato i log, dovrebbe essere possibile eseguire una query su di essi usando la finestra di esplorazione delle query sui log nel modo seguente:

1. Aprire il portale di Azure, quindi cercare l'area di lavoro in base al nome nella barra di ricerca nella parte superiore e quindi selezionarla.
2. Fare clic su Log nel riquadro sinistro
3. Fare clic su Introduzione (oppure fare clic sui collegamenti nella pagina Introduzione per ulteriori informazioni sui Log Analytics se non si ha familiarità con esso).
4. Seguire l'esercitazione per altre informazioni su Log Analytics se è la prima volta che si usa Log Analytics
5. Espandere Log personalizzati nella parte inferiore dell'elenco delle tabelle; viene visualizzata una tabella denominata "sql_instance_logs_CL".
6. Fare clic sull'icona a forma di occhio accanto al nome della tabella
7. Fare clic sul pulsante "View in query editor" (Visualizza in editor di query)
8. Nell'editor di query sarà presente una query che mostra i 10 eventi più recenti nel log.
9. Da qui è possibile provare a eseguire query sui log usando l'editor di query, impostare gli avvisi e così via.

## <a name="automating-uploads-optional"></a>Automazione di caricamenti (facoltativo)

Se si vuole caricare le metriche e i log in base a una pianificazione, è possibile creare uno script ed eseguirlo su un timer ogni pochi minuti. Di seguito è riportato un esempio di automazione dei caricamenti usando uno script della shell Linux.

Nell'editor di testo/codice preferito aggiungere lo script seguente al file e salvarlo come file eseguibile di script, ad esempio. sh (Linux/Mac) o. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Rendere eseguibile il file di script

```console
chmod +x myuploadscript.sh
```

Eseguire lo script ogni 20 minuti:

```console
watch -n 1200 ./myuploadscript.sh
```

È anche possibile usare un'utilità di pianificazione dei processi come cron o Windows Utilità di pianificazione o un agente di orchestrazione come Ansible, Puppet o chef.

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Indicazioni generali sull'esportazione e sul caricamento dell'utilizzo, sulle metriche

Le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) in Azure Arc Enabled Data Services vengono registrate per finalità di fatturazione e monitoraggio. Sono disponibili servizi in background che eseguono il monitoraggio di queste operazioni CRUD e calcolano il consumo in modo appropriato. Il calcolo effettivo dell'utilizzo o del consumo avviene in base a una pianificazione e viene eseguito in background. 

Durante la fase di anteprima, questo processo si verifica ogni notte. Il materiale sussidiario generale è caricare l'utilizzo solo una volta al giorno. Quando le informazioni sull'utilizzo vengono esportate e caricate più volte nello stesso periodo di 24 ore, solo l'inventario delle risorse viene aggiornato in portale di Azure ma non nell'utilizzo delle risorse.

Per il caricamento delle metriche, monitoraggio di Azure accetta solo gli ultimi 30 minuti di dati ([altre informazioni](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). Le linee guida per il caricamento delle metriche sono il caricamento delle metriche immediatamente dopo la creazione del file di esportazione, in modo da poter visualizzare l'intero set di dati in portale di Azure. Ad esempio, se le metriche sono state esportate alle 2:00 PM ed è stato eseguito il comando Upload alle 2:50 PM. Poiché monitoraggio di Azure accetta solo i dati per gli ultimi 30 minuti, è possibile che non vengano visualizzati dati nel portale. 

## <a name="next-steps"></a>Passaggi successivi

[Caricare i dati di fatturazione in Azure e visualizzarli nel portale di Azure](view-billing-data-in-azure.md)

[Visualizzare la risorsa del controller di dati di Azure Arc in portale di Azure](view-data-controller-in-azure-portal.md)
