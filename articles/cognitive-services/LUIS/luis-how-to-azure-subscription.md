---
title: Come usare le chiavi di creazione e di runtime - LUISHow to use authoring and runtime keys - LUIS
description: Quando si utilizza per la prima volta Language Understanding (LUIS), non è necessario creare una chiave di creazione. Quando intendi pubblicare l'app, quindi usare l'endpoint di runtime, devi creare e assegnare la chiave di runtime all'app.
services: cognitive-services
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: ea2799e7b8bb18ad4a729a70ae1477cde9f97e95
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754359"
---
# <a name="create-luis-resources"></a>Creare risorse LUISCreate LUIS resources

Le risorse di creazione e esecuzione forniscono l'autenticazione all'app LUIS e all'endpoint di stima.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Quando si accede al portale LUIS, è possibile scegliere di continuare con:

* una [chiave](#trial-key) di prova gratuita, che fornisce la creazione e alcune query dell'endpoint di stima.
* una risorsa di creazione e modifica di Azure [LUIS.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Accedere al portale LUIS e iniziare la creazione

1. Accedere al [portale LUIS](https://www.luis.ai) e accettare le condizioni per l'utilizzo.
1. Iniziare l'app LUIS scegliendo il tipo di chiave di creazione LUIS da usare: chiave di valutazione gratuita o nuova chiave di creazione di Azure LUIS.

    ![Scegliere un tipo di risorsa di creazione Language Understanding](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Al termine del processo di selezione delle risorse, [creare una nuova app.](luis-how-to-start-new-app.md#create-new-app-in-luis)

## <a name="trial-key"></a>Chiave di prova

La chiave di prova (antipasto) è fornita per voi. Viene utilizzato come chiave di autenticazione per eseguire query sul runtime dell'endpoint di stima, fino a 1000 query al mese.

È visibile sia nella pagina **Impostazioni utente** che nelle pagine **Gestisci -> risorse** di Azure nel portale LUIS.

Quando si è pronti a pubblicare l'endpoint di stima, [creare](#create-luis-resources) e [assegnare](#assign-a-resource-to-an-app) chiavi di runtime di creazione e stima per sostituire la funzionalità della chiave di avvio.

<a name="create-resources-in-the-azure-portal"></a>


[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-resources-in-azure-cli"></a>Creare risorse nell'interfaccia della riga di comando di AzureCreate resources in Azure CLI

Usare [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) di Azure per creare ogni risorsa singolarmente.

Risorsa `kind`:

* Creazione:`LUIS.Authoring`
* Previsione:`LUIS`

1. Accedere all'interfaccia della riga di comando di Azure:Sign in to the Azure CLI:

    ```azurecli
    az login
    ```

    Verrà aperto un browser che consente di selezionare l'account corretto e fornire l'autenticazione.

1. Creare una risorsa di creazione `LUIS.Authoring` **LUIS**, di `my-resource-group` tipo `westus` , denominata `my-luis-authoring-resource` nel gruppo di risorse _esistente_ denominato per l'area.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Creare una **risorsa endpoint di stima LUIS**, di `my-resource-group` tipo `westus` `LUIS`, denominata `my-luis-prediction-resource` nel gruppo di risorse _esistente_ denominato per l'area. Se si desidera una velocità effettiva superiore `F0` `S0`rispetto al livello gratuito, passare a . Ulteriori informazioni sui [piani tariffari e](luis-boundaries.md#key-limits)sulla velocità effettiva .

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Queste chiavi non vengono utilizzate dal portale LUIS finché **non** vengono assegnate nel portale LUIS nelle risorse di **Azure Gestisci->**.

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Assegnare una risorsa di creazione nel portale LUIS per tutte le appAssign an authoring resource in the LUIS portal for all apps

È possibile assegnare una risorsa di creazione per una singola app o per tutte le app in LUIS. La procedura seguente assegna tutte le app a una singola risorsa di creazione.

1. Accedere al [portale LUIS](https://www.luis.ai).
1. Nella barra di spostamento superiore, all'estrema destra, seleziona il tuo account utente, quindi seleziona **Impostazioni**.
1. Nella pagina **Impostazioni utente** selezionare Aggiungi risorsa di creazione e **modifica,** quindi selezionare una risorsa di creazione esistente. Selezionare **Salva**.

## <a name="assign-a-resource-to-an-app"></a>Assegnare una risorsa a un'appAssign a resource to an app

È possibile assegnare una singola risorsa, fase di esecuzione dell'endpoint di creazione o stima, a un'app con la procedura seguente.

1. Accedere al [portale LUIS](https://www.luis.ai), quindi selezionare un'app dall'elenco **App** personali.
1. Passare alla pagina **Gestisci risorse di Azure >.Navigate** to the Manage -> Azure resources page.

    ![Selezionare Gestisci -> risorse di Azure nel portale LUIS per assegnare una risorsa all'app.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Selezionare la scheda Risorsa di stima o creazione, quindi selezionare il pulsante **Aggiungi risorsa stima** o Aggiungi risorsa di **creazione.**
1. Selezionare i campi nel modulo per trovare la risorsa corretta, quindi selezionare **Salva**.

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Assegnare risorse di runtime senza usare il portale LUISAssign runtime resource without using LUIS portal

Per scopi di automazione, ad esempio una pipeline CI/CD, è possibile automatizzare l'assegnazione di una risorsa di runtime LUIS a un'app LUIS. Per farlo, è necessario seguire questa procedura:

1. Ottenere un token di Azure Resource Manager per questo [sito Web](https://resources.azure.com/api/token?plaintext=true). Il token scade, perciò usarlo immediatamente. La richiesta restituisce un token di Azure Resource Manager.

    ![Richiedere il token di Azure Resource Manager e ricevere il token di Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Usare il token per richiedere le risorse di runtime LUIS tra sottoscrizioni, [dall'API Get LUIS azure accounts](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), a cui l'account utente ha accesso.

    Questa API POST richiede le impostazioni seguenti:

    |Intestazione|valore|
    |--|--|
    |`Authorization`|Il valore di `Authorization` è `Bearer {token}`. Si noti che il valore del token deve essere preceduto dalla parola `Bearer` e uno spazio.|
    |`Ocp-Apim-Subscription-Key`|La chiave di creazione.|

    Questa API restituisce una matrice di oggetti JSON delle sottoscrizioni LUIS inclusi ID sottoscrizione, gruppo di risorse e nome della risorsa restituito come nome dell'account. Trovare l'elemento della matrice che rappresenta la risorsa LUIS da assegnare all'app LUIS.

1. Assegnare il token alla risorsa servizio LUIS con l'API [Assign a LUIS azure accounts to an application](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) (Assegnare un account LUIS di Azure a un'applicazione).

    Questa API POST richiede le impostazioni seguenti:

    |Type|Impostazione|valore|
    |--|--|--|
    |Intestazione|`Authorization`|Il valore di `Authorization` è `Bearer {token}`. Si noti che il valore del token deve essere preceduto dalla parola `Bearer` e uno spazio.|
    |Intestazione|`Ocp-Apim-Subscription-Key`|La chiave di creazione.|
    |Intestazione|`Content-type`|`application/json`|
    |QueryString|`appid`|L'ID dell'app LUIS.
    |Corpo||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Quando l'API ha esito positivo, restituisce 201 - stato creato.

## <a name="unassign-resource"></a>Annullare l'assegnazione di una risorsa

1. Accedere al [portale LUIS](https://www.luis.ai), quindi selezionare un'app dall'elenco **App** personali.
1. Passare alla pagina **Gestisci risorse di Azure >.Navigate** to the Manage -> Azure resources page.
1. Selezionare la scheda Prediction o Authoring resource , quindi selezionare il pulsante **Un (Un(Unassign resource)** per la risorsa.

Quando si annulla l'assegnazione di una risorsa, questa non viene eliminata da Azure.When you unslassign a resource, it is not deleted from Azure. Viene soltanto scollegata da LUIS.

## <a name="reset-authoring-key"></a>Reimpostare la chiave di creazione

**Per le app migrate delle risorse di [creazione:](luis-migration-authoring.md) **se la chiave di creazione è compromessa, reimpostare la chiave nel portale di Azure nella pagina **Chiavi** per tale risorsa di creazione.

**Per le app di cui non è ancora stata eseguita la migrazione:** la chiave viene reimpostata su tutte le app nel portale LUIS. Se si creano le app tramite le API di creazione, è necessario modificare il valore di Ocp-Apim-Subscription-Key nella nuova chiave.

## <a name="regenerate-azure-key"></a>Rigenerare la chiave di AzureRegenerate Azure key

Rigenerare le chiavi di Azure dal portale di Azure nella pagina Chiavi.Regenerate the Azure keys from the Azure portal, on the **Keys** page.

## <a name="delete-account"></a>Eliminare l'account

Per informazioni sui dati che vengono eliminati quando viene eliminato l'account, vedere [Data storage and removal](luis-concept-data-storage.md#accounts) (Archiviazione e rimozione dei dati).

## <a name="change-pricing-tier"></a>Modificare il piano tariffario

1.  In [Azure](https://portal.azure.com), trova la sottoscrizione LUIS. Selezionare la sottoscrizione LUIS.
    ![Trova la sottoscrizione LUIS](./media/luis-usage-tiers/find.png)
1.  Selezionare **Piano tariffario** per visualizzare i piani tariffari disponibili.
    ![Visualizza piano tariffario](./media/luis-usage-tiers/subscription.png)
1.  Selezionare il piano tariffario e scegliere **Seleziona** per salvare la modifica.
    ![Modifica il piano tariffario LUIS](./media/luis-usage-tiers/plans.png)
1.  Una volta completata la modifica dei prezzi, una finestra popup verifica il nuovo piano tariffario.
    ![Verifica il piano tariffario LUIS](./media/luis-usage-tiers/updated.png)
1. Ricorda di [assegnare questa chiave di endpoint](#assign-a-resource-to-an-app) nella pagina **Pubblica** e di usarla in tutte le query di endpoint.

## <a name="viewing-azure-resource-metrics"></a>Visualizzazione delle metriche delle risorse di AzureViewing Azure resource metrics

### <a name="viewing-azure-resource-summary-usage"></a>Visualizzazione dell'utilizzo del riepilogo delle risorse di AzureViewing Azure resource summary usage
È possibile visualizzare le informazioni sull'utilizzo di LUIS in Azure. La pagina **Panoramica** contiene informazioni di riepilogo recenti, incluse chiamate ed errori. Se effettui una richiesta LUIS di endpoint, quindi osservi la **pagina Panoramica**, attendi fino a cinque minuti perché compaia l'utilizzo.

![Visualizzazione del riepilogo dell'utilizzo](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Personalizzazione dei grafici di utilizzo delle risorse di AzureCustomizing Azure resource usage charts
Metrica offre una visualizzazione più dettagliata dei dati.

![Metriche predefinite](./media/luis-usage-tiers/metrics-default.png)

È possibile configurare i grafici delle metriche per periodo di tempo e il tipo di metrica.

![Metriche personalizzate](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Avviso di superamento della soglia delle transazioni totali
Se desideri sapere quando è stata raggiunta una determinata soglia delle transazioni, ad esempio 10.000 transazioni, puoi creare un avviso.

![Avvisi predefiniti](./media/luis-usage-tiers/alert-default.png)

Aggiungi un avviso di metrica per la metrica **Totale chiamate** riferito a un determinato periodo di tempo. Aggiungi gli indirizzi e-mail di tutti gli utenti che devono ricevere l'avviso. Aggiungi webhook per tutti i sistemi che devono ricevere l'avviso. È anche possibile eseguire un'app per la logica quando viene generato l'avviso.

## <a name="next-steps"></a>Passaggi successivi

* [Scopri come usare le versioni](luis-how-to-manage-versions.md) per controllare il ciclo di vita dell'app.
* Comprendere i concetti, inclusi la [risorsa di creazione](luis-concept-keys.md#authoring-key) e i [collaboratori](luis-concept-keys.md#contributions-from-other-authors) per tale risorsa.
* Informazioni su [come creare](luis-how-to-azure-subscription.md) risorse di creazione e esecuzione
* Eseguire la migrazione alla nuova [risorsa di creazione](luis-migration-authoring.md)
