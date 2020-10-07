---
title: 'Avvio rapido: Nuova assegnazione di criteri con JavaScript'
description: In questa guida di avvio rapido si usa JavaScript per creare un'assegnazione di Criteri di Azure per identificare le risorse non conformi.
ms.date: 09/24/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 7548053e4bd5be214bf7de3eef3dc4c6c95442d4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91348174"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-javascript"></a>Avvio rapido: Creare un'assegnazione di criteri per identificare le risorse non conformi con JavaScript

Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse. In questo avvio rapido si creerà un'assegnazione di criteri per identificare le macchine virtuali che non usano dischi gestiti. Al termine, si identificheranno le macchine virtuali _non conformi_.

La libreria di JavaScript viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida illustra come usare la libreria di JavaScript per creare un'assegnazione di criteri.

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- **Node.js**: è richiesto [Node.js](https://nodejs.org/) versione 12 o successiva.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-libraries"></a>Aggiungere le librerie di criteri

Per abilitare l'uso di JavaScript con Criteri di Azure, è necessario aggiungere le librerie. Queste librerie funzionano ovunque sia possibile usare JavaScript, incluso [Bash in Windows 10](/windows/wsl/install-win10).

1. Configurare un nuovo progetto Node.js usando il comando seguente.

   ```bash
   npm init -y
   ```

1. Aggiungere un riferimento alla libreria yargs.

   ```bash
   npm install yargs
   ```

1. Aggiungere un riferimento alle librerie di Criteri di Azure.

   ```bash
   # arm-policy is for working with Azure Policy objects such as definitions and assignments
   npm install @azure/arm-policy

   # arm-policyinsights is for working with Azure Policy compliance data such as events and states
   npm install @azure/arm-policyinsights
   ```

1. Aggiungere un riferimento alla libreria di autenticazione di Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Verificare in _package.json_ che la versione di `@azure/arm-policy` sia **3.1.0** o successiva, che la versione di `@azure/arm-policyinsights` sia **3.2.0** o successiva e che la versione di `@azure/ms-rest-nodeauth` sia **3.0.5** o successiva.

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questo avvio rapido i crea un'assegnazione di criteri e si assegna la definizione **Controlla macchine virtuali che non usano dischi gestiti** (`06a78e20-9358-41c9-923c-fb736d382a4d`). Questa definizione di criteri identifica le risorse che non rispettano le condizioni in essa impostate.

1. Creare un nuovo file denominato _policyAssignment.js_ e immettere il codice seguente.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyObjects = require("@azure/arm-policy");
   
   if (argv.subID && argv.name && argv.displayName && argv.policyDefID && argv.scope && argv.description) {
   
       const createAssignment = async () => {
           const credentials = await authenticator.interactiveLogin();
           const client = new policyObjects.PolicyClient(credentials, argv.subID);
           const assignments = new policyObjects.PolicyAssignments(client);
   
           const result = await assignments.create(
               argv.scope,
               argv.name,
               {
                   displayName: argv.displayName,
                   policyDefinitionId: argv.policyDefID,
                   description: argv.description
               }
           );
           console.log(result);
       };
   
       createAssignment();
   }
   ```

1. Immettere il comando seguente nel terminale:

   ```bash
   node policyAssignment.js `
      --subID "{subscriptionId}" `
      --name "audit-vm-manageddisks" `
      --displayName "Audit VMs without managed disks Assignment" `
      --policyDefID "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      --description "Shows all virtual machines not using managed disks" `
      --scope "{scope}"
   ```

I comandi precedenti usano le informazioni seguenti:

- **subID**: ID sottoscrizione per il contesto di autenticazione. Assicurarsi di sostituire `{subscriptionId}` con la sottoscrizione in uso.
- **name**: nome univoco per l'oggetto assegnazione di criteri. L'esempio precedente usa _audit-vm-manageddisks_.
- **displayName**: nome visualizzato per l'assegnazione di criteri. In questo caso viene usato _controllare le macchine virtuali senza assegnazione di dischi gestiti_.
- **policyDefID**: percorso di definizione di criteri in base al quale si crea l'assegnazione. In questo caso si tratta dell'ID della definizione di criteri _Controllare le macchine virtuali che non usano i dischi gestiti_.
- **description** - Spiegazione più dettagliata delle operazioni eseguite dal criterio o dei motivi per cui è assegnato a questo ambito.
- **scope** - Ambito che determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri. Può spaziare da un gruppo di gestione a una singola risorsa. Assicurarsi di sostituire `{scope}` con uno dei criteri seguenti:
  - Gruppo di gestione: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Sottoscrizione: `/subscriptions/{subscriptionId}`
  - Gruppo di risorse: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Risorsa: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

A questo punto si è pronti per identificare le risorse non conformi e comprendere così lo stato di conformità dell'ambiente.

## <a name="identify-non-compliant-resources"></a>Identificare risorse non conformi

Dopo aver creato l'assegnazione di criteri, è possibile identificare le risorse non conformi.

1. Creare un nuovo file denominato _policyState.js_ e immettere il codice seguente.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyInsights = require("@azure/arm-policyinsights");
   
   if (argv.subID && argv.name) {
   
       const getStates = async () => {
   
           const credentials = await authenticator.interactiveLogin();
           const client = new policyInsights.PolicyInsightsClient(credentials);
           const policyStates = new policyInsights.PolicyStates(client);
           const result = await policyStates.listQueryResultsForSubscription(
               "latest",
               argv.subID,
               {
                   queryOptions: {
                       filter: "IsCompliant eq false and PolicyAssignmentId eq '" + argv.name + "'",
                       apply: "groupby((ResourceId))"
                   }
               }
           );
           console.log(result);
       };
   
       getStates();
   }
   ```

1. Immettere il comando seguente nel terminale:

   ```bash
   node policyState.js --subID "{subscriptionId}" --name "audit-vm-manageddisks"
   ```

Sostituire `{subscriptionId}` con la sottoscrizione per cui si vogliono visualizzare i risultati di conformità per l'assegnazione dei criteri denominata 'audit-vm-manageddisks' creata nei passaggi precedenti. Per un elenco di altri ambiti e modi per riepilogare i dati, vedere i metodi [PolicyStates*](/javascript/api/@azure/arm-policyinsights/).

I risultati saranno simili all'esempio seguente:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

I risultati corrispondono a quanto visualizzato nella scheda **Conformità risorsa** di un'assegnazione di criteri nella vista del portale di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

- Eliminare l'assegnazione di criteri _Controlla le macchine virtuali senza assegnazione di dischi gestiti_ tramite il portale. La definizione di criteri è predefinita, quindi non esiste alcuna definizione da rimuovere.

- Per rimuovere le librerie installate dall'applicazione, eseguire il comando seguente.

  ```bash
  npm uninstall @azure/arm-policy @azure/arm-policyinsights @azure/ms-rest-nodeauth yargs
  ```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata assegnata una definizione dei criteri per identificare le risorse non conformi nell'ambiente Azure.

Per altre informazioni sull'assegnazione di definizioni di criteri per convalidare la conformità delle nuove risorse, continuare con l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./tutorials/create-and-manage.md)