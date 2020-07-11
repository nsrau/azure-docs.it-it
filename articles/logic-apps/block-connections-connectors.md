---
title: Blocca le connessioni per connettori API specifici
description: Limitare la creazione e l'uso di connessioni API in app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 06/19/2020
ms.openlocfilehash: 6563f3e263867387332940db58abff62e085cded
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187694"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>Blocca le connessioni create dai connettori nelle app per la logica di Azure

Se l'organizzazione non consente la connessione a risorse limitate o non approvate usando i connettori in app per la logica di Azure, è possibile bloccare la funzionalità per creare e usare tali connessioni nei flussi di lavoro delle app per la logica. Con [criteri di Azure](../governance/policy/overview.md)è possibile definire e applicare [criteri](../governance/policy/overview.md#policy-definition) che impediscono la creazione o l'uso di connessioni per i connettori che si desidera bloccare. Per motivi di sicurezza, ad esempio, potrebbe essere necessario bloccare le connessioni a piattaforme di social media specifiche o ad altri servizi e sistemi.

Questo argomento illustra come configurare un criterio che blocca connessioni specifiche usando il portale di Azure, ma è possibile creare le definizioni dei criteri in altri modi, ad esempio tramite l'API REST di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure e i modelli di Azure Resource Manager. Per altre informazioni, vedere [esercitazione: creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, [creare un account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

* ID di riferimento per il connettore che si desidera bloccare. Per altre informazioni, vedere [trovare l'ID di riferimento del connettore](#connector-reference-ID).

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>Trovare l'ID di riferimento del connettore

Se è già presente un'app per la logica con la connessione che si vuole bloccare, seguire i [passaggi per la portale di Azure](#connector-ID-portal). In caso contrario, seguire questi passaggi:

1. Visitare l' [elenco di connettori per app](https://docs.microsoft.com/connectors/connector-reference/connector-reference-logicapps-connectors)per la logica.

1. Trovare la pagina di riferimento per il connettore che si desidera bloccare.

   Ad esempio, se si vuole bloccare il connettore Instagram, andare alla pagina seguente: 
   
   `https://docs.microsoft.com/connectors/instagram/`

1. Dall'URL della pagina, copiare e salvare l'ID di riferimento del connettore alla fine senza la barra ( `/` ), ad esempio `instagram` .

   Successivamente, quando si crea la definizione dei criteri, si utilizza questo ID nell'istruzione della condizione della definizione, ad esempio:

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>Portale di Azure

1. Nella [portale di Azure](https://portal.azure.com)individuare e aprire l'app per la logica.

1. Nel menu dell'app per la logica selezionare **visualizzazione del codice dell'app** per la logica in modo che sia possibile visualizzare la definizione JSON dell'app per la logica.

   ![Aprire "visualizzazione codice app per la logica" per trovare l'ID connettore](./media/block-connections-connectors/code-view-connector-id.png)

1. Trovare l' `parameters` oggetto che contiene l' `$connections` oggetto, che include un `{connection-name}` oggetto per ogni connessione nell'app per la logica e specifica le informazioni su tale connessione:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "{connection-name}": {
                  "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
                  "connectionName": "{connection-name}",
                  "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"
               }
            }
         }
      }
   }
   ```

   Per il connettore Instagram, ad esempio, trovare l' `instagram` oggetto, che identifica una connessione Instagram:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "instagram": {
                  "connectionId": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Web/connections/instagram",
                  "connectionName": "instagram",
                  "id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"
               }
            }
         }
      }
   }
   ```

1. Per la connessione che si desidera bloccare, trovare la `id` proprietà e il valore che seguono questo formato: 

   `"id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"`

   Ecco ad esempio la `id` proprietà e il valore per una connessione Instagram:

   `"id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"`

1. Dal `id` valore della proprietà, copiare e salvare l'ID di riferimento del connettore alla fine, ad esempio `instagram` .

   Successivamente, quando si crea la definizione dei criteri, si utilizza questo ID nell'istruzione della condizione della definizione, ad esempio:

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>Creare criteri per bloccare la creazione di connessioni

Per bloccare la creazione di una connessione in un'app per la logica, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com). Nella casella di ricerca del portale immettere `policy` e selezionare **criteri**.

   ![In portale di Azure individuare e selezionare "criteri"](./media/block-connections-connectors/find-select-azure-policy.png)

1. In **creazione e modifica**nel menu **criteri** selezionare **definizioni**  >  **+ definizione criteri**.

   ![Selezionare "definizioni" > "+ definizione dei criteri"](./media/block-connections-connectors/add-new-policy-definition.png)

1. In **definizione criteri**specificare le informazioni per la definizione dei criteri, in base alle proprietà descritte nell'esempio:

   ![Proprietà definizione criteri](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Posizione della definizione** | Sì | <*nome sottoscrizione di Azure*> | Sottoscrizione di Azure da usare per la definizione dei criteri <p><p>1. per trovare la sottoscrizione, selezionare il pulsante con i puntini di sospensione (**...**). <br>2. dall'elenco **sottoscrizione** individuare e selezionare la sottoscrizione. <br>3. al termine, selezionare **Seleziona**. |
   | **Nome** | Sì | <*Policy-Definition-nome*> | Nome da usare per la definizione dei criteri |
   | **Descrizione** | No | <*Policy-Definition-nome*> | Una descrizione per la definizione dei criteri |
   | **Categoria** | Sì | **App per la logica** | Nome di una categoria esistente o di una nuova categoria per la definizione dei criteri |
   | **Imposizione dei criteri** | Sì | **Abilitato** | Questa impostazione specifica se abilitare o disabilitare la definizione dei criteri quando si salva il lavoro. |
   ||||

1. In **regola dei criteri**, la casella di modifica JSON è già popolata con un modello di definizione dei criteri. Sostituire questo modello con la [definizione dei criteri](../governance/policy/concepts/definition-structure.md) in base alle proprietà descritte nella tabella seguente e seguendo questa sintassi:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | `mode` | `All` | Modalità che determina i tipi di risorse valutati dal criterio. <p><p>Questo scenario imposta `mode` su `All` , che applica i criteri a gruppi di risorse di Azure, sottoscrizioni e tutti i tipi di risorsa. <p><p>Per ulteriori informazioni, vedere la pagina relativa alla [modalità struttura della definizione dei criteri](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | Condizione che determina quando applicare la regola dei criteri <p><p>In questo scenario, `{condition-to-evaluate}` determina se il `api.id` valore in `Microsoft.Web/connections/api.id` corrisponde a `*managedApis/{connector-name}` , che specifica un valore jolly (*). <p><p>Per altre informazioni, vedere [struttura della definizione dei criteri-regola dei criteri](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `field` | `Microsoft.Web/connections/api.id` | `field`Valore da confrontare con la condizione <p><p>In questo scenario, `field` Usa l' [*alias*](../governance/policy/concepts/definition-structure.md#aliases), `Microsoft.Web/connections/api.id` , per accedere al valore nella proprietà Connector, `api.id` . |
   | `like` | `*managedApis/{connector-name}` | Operatore logico e valore da utilizzare per il confronto del `field` valore <p><p>In questo scenario, l' `like` operatore e il carattere jolly (*) assicurano che la regola funzioni indipendentemente dall'area, mentre la stringa, `*managedApis/{connector-name}` , è il valore in base al quale `{connector-name}` è l'ID del connettore che si desidera bloccare. <p><p>Si supponga, ad esempio, di voler bloccare la creazione di connessioni a piattaforme o database di social media: <p><p>Twitter`twitter` <br>Instagram`instagram` <br>Facebook`facebook` <br>Pinterest`pinterest` <br>-SQL Server o Azure SQL:`sql` <p><p>Per trovare questi ID connettore, vedere l'articolo [relativo all'ID di riferimento per Find Connector](#connector-reference-ID) più indietro in questo argomento. |
   | `then` | `{effect-to-apply}` | Effetto da applicare quando `if` viene soddisfatta la condizione <p><p>In questo scenario, l'oggetto `{effect-to-apply}` è in grado di bloccare e interrompere una richiesta o un'operazione che non è conforme ai criteri. <p><p>Per altre informazioni, vedere [struttura della definizione dei criteri-regola dei criteri](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `effect` | `deny` | `effect`Per bloccare la richiesta è necessario creare la connessione specificata <p><p>Per altre informazioni, vedere [comprendere gli effetti dei criteri di Azure-nega](../governance/policy/concepts/effects.md#deny). |
   ||||

   Si supponga, ad esempio, di voler bloccare la creazione di connessioni con il connettore Instagram. Di seguito è illustrata la definizione dei criteri che è possibile usare:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
   }
   ```

   Ecco il modo in cui viene visualizzata la casella della **regola dei criteri** :

   ![Regola per la definizione dei criteri](./media/block-connections-connectors/policy-definition-create-connections-2.png)

   Per più connettori, è possibile aggiungere altre condizioni, ad esempio:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "anyOf": [
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/instagram"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/twitter"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/facebook"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/pinterest"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

1. Al termine, selezionare **Salva**. Dopo aver salvato la definizione dei criteri, i criteri di Azure generano e aggiungono altri valori di proprietà alla definizione dei criteri.

1. Quindi, per assegnare la definizione dei criteri in cui si desidera applicare il criterio, [creare un'assegnazione di criteri](#create-policy-assignment).

Per ulteriori informazioni sulle definizioni di criteri di Azure, vedere gli argomenti seguenti:

* [Definizione della struttura dei criteri](../governance/policy/concepts/definition-structure.md)
* [Esercitazione: Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)
* [Definizioni di criteri predefiniti di Criteri di Azure per App per la logica di Azure](../logic-apps/policy-samples.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>Creazione di criteri per bloccare l'utilizzo di connessioni

Quando si crea una connessione all'interno di un'app per la logica, tale connessione esiste come risorsa di Azure separata. Se si elimina solo l'app per la logica, la connessione non viene eliminata automaticamente e continua a esistere fino a quando non viene eliminata. Si potrebbe avere uno scenario in cui la connessione esiste già o dove è necessario creare la connessione da usare all'esterno di un'app per la logica. È comunque possibile bloccare la funzionalità per usare una connessione esistente in un'app per la logica creando un criterio che impedisce il salvataggio di app per la logica con connessione limitata o non approvata.

1. Accedere al [portale di Azure](https://portal.azure.com). Nella casella di ricerca del portale immettere `policy` e selezionare **criteri**.

   ![In portale di Azure individuare e selezionare "criteri"](./media/block-connections-connectors/find-select-azure-policy.png)

1. In **creazione e modifica**nel menu **criteri** selezionare **definizioni**  >  **+ definizione criteri**.

   ![Selezionare "definizioni" > "+ definizione dei criteri"](./media/block-connections-connectors/add-new-policy-definition.png)

1. In **definizione criteri**specificare le informazioni per la definizione dei criteri, in base alle proprietà descritte nell'esempio e continuare usando Instagram come esempio:

   ![Proprietà definizione criteri](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | Proprietà | Obbligatoria | valore | Description |
   |----------|----------|-------|-------------|
   | **Posizione della definizione** | Sì | <*nome sottoscrizione di Azure*> | Sottoscrizione di Azure da usare per la definizione dei criteri <p><p>1. per trovare la sottoscrizione, selezionare il pulsante con i puntini di sospensione (**...**). <br>2. dall'elenco **sottoscrizione** individuare e selezionare la sottoscrizione. <br>3. al termine, selezionare **Seleziona**. |
   | **Nome** | Sì | <*Policy-Definition-nome*> | Nome da usare per la definizione dei criteri |
   | **Descrizione** | No | <*Policy-Definition-nome*> | Una descrizione per la definizione dei criteri |
   | **Categoria** | Sì | **App per la logica** | Nome di una categoria esistente o di una nuova categoria per la definizione dei criteri |
   | **Imposizione dei criteri** | Sì | **Abilitato** | Questa impostazione specifica se abilitare o disabilitare la definizione dei criteri quando si salva il lavoro. |
   ||||

1. In **regola dei criteri**, la casella di modifica JSON è già popolata con un modello di definizione dei criteri. Sostituire questo modello con la [definizione dei criteri](../governance/policy/concepts/definition-structure.md) in base alle proprietà descritte nella tabella seguente e seguendo questa sintassi:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Proprietà | Valore | Description |
   |----------|-------|-------------|
   | `mode` | `All` | Modalità che determina i tipi di risorse valutati dal criterio. <p><p>Questo scenario imposta `mode` su `All` , che applica i criteri a gruppi di risorse di Azure, sottoscrizioni e tutti i tipi di risorsa. <p><p>Per ulteriori informazioni, vedere la pagina relativa alla [modalità struttura della definizione dei criteri](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | Condizione che determina quando applicare la regola dei criteri <p><p>In questo scenario, `{condition-to-evaluate}` determina se l'output della stringa da `[string(field('Microsoft.Logic/workflows/parameters'))]` contiene la stringa, `{connector-name}` . <p><p>Per altre informazioni, vedere [struttura della definizione dei criteri-regola dei criteri](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | Valore da confrontare con la condizione <p><p>In questo scenario, `value` è l'output di stringa di `[string(field('Microsoft.Logic/workflows/parameters'))]` , che converte l' `$connectors` oggetto all'interno dell' `Microsoft.Logic/workflows/parameters` oggetto in una stringa. |
   | `contains` | `{connector-name}` | Operatore logico e valore da utilizzare per il confronto con la `value` Proprietà. <p><p>In questo scenario, l' `contains` operatore garantisce che la regola funzioni indipendentemente da dove `{connector-name}` viene visualizzato, dove la stringa, `{connector-name}` , è l'ID del connettore che si desidera limitare o bloccare. <p><p>Si supponga, ad esempio, di voler bloccare l'uso delle connessioni a piattaforme o database di social media: <p><p>Twitter`twitter` <br>Instagram`instagram` <br>Facebook`facebook` <br>Pinterest`pinterest` <br>-SQL Server o Azure SQL:`sql` <p><p>Per trovare questi ID connettore, vedere l'articolo [relativo all'ID di riferimento per Find Connector](#connector-reference-ID) più indietro in questo argomento. |
   | `then` | `{effect-to-apply}` | Effetto da applicare quando `if` viene soddisfatta la condizione <p><p>In questo scenario, l'oggetto `{effect-to-apply}` prevede il blocco e l'esito negativo di una richiesta o di un'operazione che non è conforme ai criteri. <p><p>Per altre informazioni, vedere [struttura della definizione dei criteri-regola dei criteri](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `effect` | `deny` | Il `effect` è `deny` o blocca la richiesta di salvataggio di un'app per la logica che usa la connessione specificata <p><p>Per altre informazioni, vedere [comprendere gli effetti dei criteri di Azure-nega](../governance/policy/concepts/effects.md#deny). |
   ||||

   Si supponga, ad esempio, di voler bloccare il salvataggio di app per la logica che usano connessioni Instagram. Di seguito è illustrata la definizione dei criteri che è possibile usare:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   Ecco il modo in cui viene visualizzata la casella della **regola dei criteri** :

   ![Regola per la definizione dei criteri](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. Al termine, selezionare **Salva**. Dopo aver salvato la definizione dei criteri, i criteri di Azure generano e aggiungono altri valori di proprietà alla definizione dei criteri.

1. Quindi, per assegnare la definizione dei criteri in cui si desidera applicare il criterio, [creare un'assegnazione di criteri](#create-policy-assignment).

Per ulteriori informazioni sulle definizioni di criteri di Azure, vedere gli argomenti seguenti:

* [Definizione della struttura dei criteri](../governance/policy/concepts/definition-structure.md)
* [Esercitazione: Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)
* [Definizioni di criteri predefiniti di Criteri di Azure per App per la logica di Azure](../logic-apps/policy-samples.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>Crea assegnazione criteri

Successivamente, è necessario assegnare la definizione dei criteri in cui si vuole applicare il criterio, ad esempio, a un singolo gruppo di risorse, a più gruppi di risorse, a Azure Active Directory (Azure AD) o a una sottoscrizione di Azure. Per questa attività, seguire questa procedura per creare un'assegnazione di criteri:

1. Se è stato effettuato l'accesso, accedere nuovamente al [portale di Azure](https://portal.azure.com). Nella casella di ricerca del portale immettere `policy` e selezionare **criteri**.

   ![In portale di Azure individuare e selezionare "criteri"](./media/block-connections-connectors/find-select-azure-policy.png)

1. In **creazione e modifica**nel menu **criteri** selezionare **assegnazioni**  >  **assegna criterio**.

   ![Selezionare "assegnazioni" > "assegna"](./media/block-connections-connectors/add-new-policy-assignment.png)

1. In **nozioni di base**fornire queste informazioni per l'assegnazione dei criteri:

   | Proprietà | Obbligatorio | Descrizione |
   |----------|----------|-------------|
   | **Scope** | Sì | Risorse in cui si desidera applicare l'assegnazione dei criteri. <p><p>1. accanto alla casella **ambito** , selezionare il pulsante con i puntini di sospensione (**...**). <br>2. dall'elenco **sottoscrizione** selezionare la sottoscrizione di Azure. <br>3. Facoltativamente, nell'elenco **gruppo di risorse** selezionare il gruppo di risorse. <br>4. al termine, selezionare **Seleziona**. |
   | **Esclusioni** | No | Tutte le risorse di Azure da escludere dall'assegnazione dei criteri. <p><p>1. accanto alla casella **esclusioni** , selezionare il pulsante con i puntini di sospensione (**...**). <br>2. dall'elenco di **risorse** selezionare la risorsa > **Aggiungi a ambito selezionato**. <br>3. al termine, selezionare **Salva**. |
   | **Definizione di criteri** | Sì | Nome della definizione dei criteri che si desidera assegnare e applicare. Questo esempio continua con il criterio Instagram di esempio "blocca le connessioni Instagram". <p><p>1. accanto alla casella **definizione criteri** selezionare il pulsante con i puntini di sospensione (**...**). <br>2. trovare e selezionare la definizione dei criteri usando il filtro dei **tipi** o la casella di **ricerca** . <br>3. al termine, selezionare **Seleziona**. |
   | **Nome assegnazione** | Sì | Nome da utilizzare per l'assegnazione dei criteri, se diverso dalla definizione dei criteri |
   | **ID assegnazione** | Sì | ID generato automaticamente per l'assegnazione di criteri |
   | **Descrizione** | No | Una descrizione per l'assegnazione dei criteri |
   | **Imposizione dei criteri** | Sì | Impostazione che Abilita o Disabilita l'assegnazione dei criteri |
   | **Assegnato da** | No | Nome della persona che ha creato e applicato l'assegnazione dei criteri |
   ||||

   Ad esempio, per assegnare i criteri a un gruppo di risorse di Azure usando l'esempio di Instagram:

   ![Proprietà di assegnazione dei criteri](./media/block-connections-connectors/policy-assignment-basics.png)

1. Al termine, selezionare **Rivedi e crea**.

   Dopo aver creato un criterio, potrebbe essere necessario attendere fino a 15 minuti prima che i criteri abbiano effetto. Anche le modifiche potrebbero avere effetti ritardati simili.

1. Una volta che i criteri diventano effettivi, è possibile [testare i criteri](#test-policy).

Per altre informazioni, vedere [Guida introduttiva: creare un'assegnazione di criteri per identificare le risorse non conformi](../governance/policy/assign-policy-portal.md).

<a name="test-policy"></a>

## <a name="test-the-policy"></a>Testare il criterio

Per provare il criterio, avviare la creazione di una connessione usando il connettore con restrizioni Now nella finestra di progettazione dell'app per la logica. Continuando con l'esempio di Instagram, quando si accede a Instagram si ottiene questo errore che l'app per la logica non è riuscita a creare la connessione:

![Errore di connessione dovuto a criteri applicati](./media/block-connections-connectors/connection-failure-message.png)

Il messaggio include le informazioni seguenti:

| Descrizione | Contenuto |
|---|---|
| Motivo dell'errore | `"Resource 'instagram' was disallowed by policy."` |
| Nome assegnazione | `"Block Instagram connections"` |
| ID assegnazione | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| ID definizione dei criteri | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [criteri di Azure](../governance/policy/overview.md)