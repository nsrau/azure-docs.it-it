---
title: Tipi di unità di test, Marketplace commerciale Microsoft
description: Tipi di unità di test nel Marketplace commerciale
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/19/2020
ms.author: keferna
author: keferna
ms.openlocfilehash: 8ccc4cb6a6f95cfc51fb7e265e455131bc6393c2
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735602"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager test drive

Usare questo tipo se si dispone di un'offerta in Azure Marketplace o in AppSource, ma si vuole creare una test drive solo con le risorse di Azure. Un modello di Azure Resource Manager (ARM) è un contenitore codificato di risorse di Azure che è possibile progettare per rappresentare meglio la soluzione. Test Drive accetta il modello ARM fornito e distribuisce tutte le risorse necessarie per un gruppo di risorse. Questa è l'unica opzione test drive per le offerte di macchine virtuali o app di Azure.

Se non si ha familiarità con il modello di gestione risorse di Azure, leggere le informazioni [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) e [comprendere la struttura e la sintassi dei modelli ARM](../azure-resource-manager/resource-group-authoring-templates.md) per comprendere meglio come compilare e testare i propri modelli.

Per informazioni su un' **app** per la logica o **ospitata** test drive, vedere [che cos'è un test drive?](what-is-test-drive.md)

## <a name="technical-configuration"></a>Configurazione tecnica

Un modello di distribuzione contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che rientrano in questo scenario usano solo risorse di Azure. Impostare le proprietà seguenti nel centro per i partner:

- **Aree** (obbligatorio): attualmente esistono 26 aree supportate da Azure in cui è possibile rendere disponibile il test drive. In genere, è consigliabile rendere disponibile il test drive nelle aree in cui si prevede il numero maggiore di clienti, in modo che questi possano selezionare l'area più vicina per ottenere prestazioni ottimali. È necessario assicurarsi che alla sottoscrizione sia consentito distribuire tutte le risorse necessarie in ognuna delle aree selezionate.

- **Istanze** : selezionare il tipo (frequente o poco utilizzata) e il numero di istanze disponibili, che verrà moltiplicato per il numero di aree in cui è disponibile l'offerta.

  - **Frequente** : questo tipo di istanza viene distribuito ed è in attesa dell'accesso per ogni area selezionata. I clienti possono accedere immediatamente alle istanze impostate su *Frequente* di un test drive, anziché dover attendere la distribuzione. Il compromesso è che queste istanze sono sempre in esecuzione nella sottoscrizione di Azure, comportando quindi un costo relativo al tempo di attività più elevato. È consigliabile prevedere almeno un'istanza *Frequente* , in quanto la maggior parte dei clienti non vuole attendere il completamento delle distribuzioni e di conseguenza si verifica un calo di utilizzo da parte dei clienti se non è presente alcuna istanza impostata su *Frequente* .

  - **Poco utilizzata** : questo tipo di istanza rappresenta il numero totale di istanze che è possibile distribuire per ogni area. Per le istanze di questo tipo è necessario distribuire l'intero modello di Resource Manager del test drive quando un cliente richiede il test drive e di conseguenza le istanze impostate su *Poco utilizzata* vengono caricate più lentamente rispetto a quelle impostate su *Frequente* . Il compromesso consiste nel fatto che è necessario pagare solo per la durata del test drive, che *non* è sempre in esecuzione nella sottoscrizione di Azure come un'istanza *Frequente* .

- **Modello di Azure Resource Manager del test drive** : caricare il file con estensione zip contenente il modello di Azure Resource Manager. Per altre informazioni sulla creazione di un modello di Azure Resource Manager, vedere l'articolo di avvio rapido [Creare e distribuire modelli di Resource Manager con il portale di Azure](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md).

    > [!note]
    > Per la pubblicazione corretta, è importante convalidare la formattazione del modello ARM. Due modi per eseguire questa operazione sono (1) usando uno [strumento API online](https://docs.microsoft.com/rest/api/resources/deployments/validate) o (2) con una [distribuzione di prova](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal).

- **Durata test drive** (obbligatorio): immettere il numero di ore durante il quale il test drive resterà attivo. Il test drive termina automaticamente alla fine di questo periodo di tempo. Usare solo numeri interi. Ad esempio, "2" ore è un valore valido, mentre "1,5" non lo è.

## <a name="write-the-test-drive-template"></a>Scrivere il modello di test drive

Dopo aver progettato il pacchetto di risorse desiderato, scrivere e compilare il modello di test drive ARM. Poiché test drive esegue le distribuzioni in modalità completamente automatica, i modelli test drive presentano alcune restrizioni:

### <a name="parameters"></a>Parametri

La maggior parte dei modelli include un set di parametri che definiscono i nomi delle risorse, le dimensioni delle risorse (ad esempio, i tipi di account di archiviazione o le dimensioni delle macchine virtuali), i nomi utente e le password, i nomi DNS e così via. Quando si distribuiscono soluzioni usando il portale di Azure, è possibile popolare manualmente tutti questi parametri, selezionare i nomi DNS o i nomi degli account di archiviazione disponibili e così via.

![Elenco dei parametri in Azure Resource Manager](media/test-drive/resource-manager-parameters.png)

Tuttavia, test drive funziona automaticamente, senza interazione umana, pertanto supporta solo un set limitato di categorie di parametri. Se un parametro nel modello di test drive ARM non rientra in una delle categorie supportate, è necessario sostituire questo parametro con una variabile o un valore costante.

È possibile usare qualsiasi nome valido per i parametri. test drive riconosce la categoria di parametri utilizzando un valore del tipo di metadati. Specificare il tipo di metadati per ogni parametro di modello. in caso contrario, il modello non passerà la convalida:

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

> [!NOTE]
> Tutti i parametri sono facoltativi, pertanto se non si vuole usare alcun, non è necessario.

### <a name="accepted-parameter-metadata-types"></a>Tipi di metadati di parametro accettati

| Tipo di metadati   | Tipo di parametro  | Descrizione     | Valore di esempio    |
|---|---|---|---|
| **baseUri**     | string          | URI di base del pacchetto di distribuzione| `https:\//\<\..\>.blob.core.windows.net/\<\..\>` |
| **username**    | string          | Nuovo nome utente casuale.| admin68876      |
| **password**    | stringa sicura    | Nuova password casuale | Lp!ACS\^2kh     |
| **ID sessione**   | string          | ID di sessione test drive univoco (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseUri

Test Drive Inizializza questo parametro con un **URI di base** del pacchetto di distribuzione in modo da poter usare questo parametro per costruire un URI di qualsiasi file incluso nel pacchetto.

```JSON
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

Usare questo parametro all'interno del modello per costruire un URI di qualsiasi file dal pacchetto di distribuzione test drive. Nell'esempio seguente viene illustrato come costruire un URI del modello collegato:

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>username

Test Drive Inizializza questo parametro con un nuovo nome utente casuale:

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Valore di esempio: `admin68876`

È possibile usare i nomi utente casuali o costanti per la soluzione.

#### <a name="password"></a>password

Test Drive Inizializza questo parametro con una nuova password casuale:

```JSON
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Valore di esempio:  `Lp!ACS^2kh`

È possibile usare password casuali o costanti per la soluzione.

#### <a name="session-id"></a>ID sessione

Test Drive Inizializza questo parametro con un GUID univoco che rappresenta l'ID sessione del test drive:

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

Valore di esempio: `b8c8693e-5673-449c-badd-257a405a6dee`

È possibile usare questo parametro per identificare in modo univoco la sessione di test drive, se necessario.

### <a name="unique-names"></a>Nomi univoci

Alcune risorse di Azure, ad esempio gli account di archiviazione o i nomi DNS, richiedono nomi univoci a livello internazionale. Ciò significa che ogni volta che test drive distribuisce il modello ARM, viene creato un nuovo gruppo di risorse con un nome univoco per tutte le risorse. Pertanto, è necessario utilizzare la funzione [uniquestring](../azure-resource-manager/templates/template-functions.md) concatenata con i nomi delle variabili negli ID del gruppo di risorse per generare valori univoci casuali:

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Assicurarsi di concatenare le stringhe di parametro/variabile ( `contosovm` ) con un output di stringa univoco ( `resourceGroup().id` ), perché ciò garantisce l'univocità e l'affidabilità di ogni variabile.

Ad esempio, la maggior parte dei nomi di risorse non può iniziare con una cifra, ma la funzione di stringa univoca può restituire una stringa che inizia con una cifra. Pertanto, se si usa l'output di stringa univoca non elaborata, le distribuzioni avranno esito negativo.

È possibile trovare altre informazioni sulle regole di denominazione delle risorse e le restrizioni in [questo articolo](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="deployment-location"></a>Percorso di distribuzione

È possibile rendere l'utente test drive disponibile in diverse aree di Azure. Lo scopo è quello di consentire all'utente di selezionare l'area più vicina, per offrire la migliore esperienza utente.

Quando test drive crea un'istanza del Lab, crea sempre un gruppo di risorse nell'area scelta da un utente e quindi esegue il modello di distribuzione in questo contesto di gruppo. Pertanto, il modello deve selezionare il percorso di distribuzione dal gruppo di risorse:

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

Usare quindi questo percorso per tutte le risorse per un'istanza del lab specifica:

```JSON
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

Assicurarsi che la sottoscrizione sia autorizzata a distribuire tutte le risorse desiderate in ognuna delle aree selezionate. Assicurarsi inoltre che le immagini di macchine virtuali siano disponibili in tutte le aree che verranno abilitate. in caso contrario, il modello di distribuzione non funzionerà per alcune aree.

### <a name="outputs"></a>Output

Normalmente con i modelli Gestione risorse è possibile distribuire senza produrre alcun output. Infatti, si conoscono tutti i valori usati per popolare i parametri del modello ed è sempre possibile esaminare manualmente le proprietà di una risorsa.

Per test drive modelli di Gestione risorse, tuttavia, è importante tornare a test drive tutte le informazioni necessarie per ottenere l'accesso al Lab (URI del sito Web, nomi host delle macchine virtuali, nomi utente e password). Assicurarsi che tutti i nomi di output siano leggibili, in quanto queste variabili vengono presentate al cliente.

Non esistono restrizioni relative agli output del modello. Test Drive converte tutti i valori di output in stringhe, quindi se si invia un oggetto all'output, un utente visualizzerà una stringa JSON.

Esempio:

```JSON
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Limiti delle sottoscrizioni

Non dimenticare i limiti di sottoscrizione e servizio. Se, ad esempio, si vuole distribuire fino a 10 4 macchine virtuali core, è necessario assicurarsi che la sottoscrizione usata per il Lab consenta di usare i core 40. Per altre informazioni sui limiti di sottoscrizione e servizio di Azure, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md). Poiché è possibile eseguire contemporaneamente più unità di test, verificare che la sottoscrizione sia in grado di gestire il numero di core moltiplicato per il numero totale di unità di test simultanee che possono essere eseguite.

### <a name="what-to-upload"></a>Elementi da caricare

Il modello di test drive ARM viene caricato come file zip, che può includere vari elementi di distribuzione, ma deve avere un file denominato `main-template.json` . Si tratta del modello di distribuzione ARM che test drive usa per creare un'istanza di un Lab. Se si dispone di risorse aggiuntive oltre a questo file, è possibile farvi riferimento come risorse esterne all'interno del modello o includerle nel file zip.

Durante la certificazione di pubblicazione, test drive decomprime il pacchetto di distribuzione e inserisce il contenuto in un contenitore BLOB test drive interno. La struttura del contenitore riflette la struttura del pacchetto di distribuzione:

| package.zip                       | Contenitore BLOB di test drive         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

Si definisce Uri di base un Uri del contenitore BLOB. Poiché ogni revisione del Lab ha un proprio contenitore BLOB, ogni revisione del Lab ha un proprio URI di base. Test Drive può passare un URI di base del pacchetto di distribuzione decompresso nel modello tramite i parametri del modello.

### <a name="transform-template-examples-for-test-drive"></a>Esempi di modelli di trasformazione per test drive

Il processo di trasformazione di un'architettura di risorse in un modello di test drive Gestione risorse può essere scoraggiante. Per ulteriori informazioni, vedere questi esempi di come trasformare meglio i modelli di distribuzione correnti in [una test drive](what-is-test-drive.md#transforming-examples).

## <a name="test-drive-deployment-subscription-details"></a>Dettagli sottoscrizione della distribuzione test drive

La sezione finale da completare è la possibilità di distribuire automaticamente le unità di test connettendosi alla sottoscrizione di Azure e Azure Active Directory (AD).

![Dettagli sottoscrizione della distribuzione test drive](media/test-drive/deployment-subscription-details.png)

1. Ottenere un **ID sottoscrizione di Azure** . Si concede l'accesso ai servizi di Azure e al portale di Azure. Nella sottoscrizione viene tenuto traccia dell'uso delle risorse e i servizi vengono fatturati. Se non si ha già una sottoscrizione di Azure separata per le unità di test, crearne una. È possibile trovare gli ID sottoscrizione di Azure, ad esempio, accedendo `1a83645ac-1234-5ab6-6789-1h234g764ghty1` a portale di Azure e selezionando **sottoscrizioni** nel menu di spostamento a sinistra.

   ![Sottoscrizioni di Azure](media/test-drive/azure-subscriptions.png)

2. Ottenere un **ID Tenant Azure ad** . Se è già disponibile un ID tenant, è possibile trovarlo nell' **Azure Active Directory**  >  **Properties**  >  **ID directory** delle proprietà:

   ![Proprietà di Azure Active Directory](media/test-drive/azure-active-directory-properties.png)

   Se non si ha un ID tenant, crearne uno nuovo in Azure Active Directory. Per informazioni sulla configurazione di un tenant, vedere [Guida introduttiva: configurare un tenant](../active-directory/develop/quickstart-create-new-tenant.md).

3. **ID app Azure ad** : creare e registrare una nuova applicazione. Questa applicazione verrà utilizzata per eseguire operazioni nell'istanza di test drive.

   1. Passare alla directory appena creata o a una directory già esistente e selezionare Azure Active Directory nel riquadro filtro.
   2. Cercare **registrazioni app** e selezionare **Aggiungi** .
   3. Immettere un nome applicazione.
   4. Selezionare il **tipo** di **app Web/API** .
   5. Specificare qualsiasi valore nell'URL di accesso. questo campo non viene utilizzato.
   6. Selezionare **Crea** .
   7. Dopo aver creato l'applicazione, selezionare **Proprietà**  >  **impostare l'applicazione come multi-tenant** e quindi **salvare** .

4. Selezionare **Salva** .

5. Copiare l'ID applicazione per l'app registrata e incollarlo nel campo test drive.

   ![Dettaglio di ID applicazione AD Azure](media/test-drive/azure-ad-application-id-detail.png)

6. Poiché l'applicazione viene usata per la distribuzione nella sottoscrizione, è necessario aggiungere l'applicazione come collaboratore nella sottoscrizione:

   1. Consente di selezionare il tipo di **sottoscrizione** in uso per la test drive.
   1. Selezionare **Controllo di accesso (IAM)** .
   1. Selezionare la scheda **assegnazioni di ruolo** , quindi **Aggiungi assegnazione di ruolo** .

      ![Aggiungere una nuova entità di sicurezza controllo di accesso](media/test-drive/access-control-principal.jpg)

   1. Impostare **Role** e **assegnare l'accesso a** come illustrato. Nel campo **Seleziona** immettere il nome dell'applicazione Azure ad. Selezionare l'applicazione a cui si desidera assegnare il ruolo **collaboratore** .

      ![Aggiungere le autorizzazioni](media/test-drive/access-control-permissions.jpg)

   1. Selezionare **Salva** .

7. Generare una chiave di autenticazione **app Azure ad** . In **chiavi** aggiungere una **Descrizione della chiave** , impostare la durata su **never expires** (una chiave scaduta interrompe il test drive in produzione), quindi selezionare **Save (Salva** ). Copiare e incollare questo valore nel campo test drive obbligatorio.

![Mostra le chiavi per l'applicazione Azure AD](media/test-drive/azure-ad-app-keys.png)

## <a name="republish"></a>Ripubblicare

Ora che tutti i campi test drive sono completi, **ripubblicare** l'offerta. Una volta che il test drive ha superato la certificazione, testare l'esperienza del cliente nella versione di anteprima dell'offerta:

1. Avviare un test drive nell'interfaccia utente.
1. Aprire la sottoscrizione di Azure all'interno del portale di Azure.
1. Verificare che la test drive venga distribuita correttamente.

   ![Portale di Azure](media/test-drive/azure-portal.png)

Non eliminare le istanze di test drive di cui è stato effettuato il provisioning per i clienti. il servizio test drive eliminerà automaticamente questi gruppi di risorse dopo che un cliente ha completato l'operazione.

Quando si ha familiarità con l'offerta di anteprima, è possibile **iniziare** . È disponibile un processo di revisione finale per verificare l'intera esperienza end-to-end. Se l'offerta viene rifiutata, verrà inviato un messaggio di posta elettronica al contatto tecnico per l'offerta, spiegando cosa è necessario correggere.

## <a name="next-steps"></a>Passaggi successivi

- Se si seguono le istruzioni per creare l'offerta nel centro per i partner, usare la freccia indietro per tornare a tale argomento.
- Per altre informazioni su altri tipi di test drive, vedere [che cos'è un test drive?](what-is-test-drive.md).
