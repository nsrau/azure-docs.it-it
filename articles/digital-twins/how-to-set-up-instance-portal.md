---
title: Configurare un'istanza e l'autenticazione (portale)
titleSuffix: Azure Digital Twins
description: Vedere come configurare un'istanza del servizio dispositivi digitali gemelli di Azure usando il portale di Azure
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0dfc93987fb2ca25b9e397e4aa91adcaaafd8a38
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478851"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Configurare un'istanza e l'autenticazione di Azure Digital Twins (portale)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Questo articolo illustra i passaggi per **configurare una nuova istanza di Azure Digital Twins** , inclusa la creazione dell'istanza e la configurazione dell'autenticazione. Al termine dell'articolo, si disporrà di un'istanza di gemelli digitali di Azure pronta per iniziare la programmazione.

Questa versione di questo articolo descrive manualmente questa procedura, una alla volta, usando il portale di Azure. Il portale di Azure è una console unificata basata sul Web che offre un'alternativa agli strumenti da riga di comando.
* Per eseguire questa procedura manualmente usando l'interfaccia della riga di comando, vedere la versione dell'interfaccia della riga di comando di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (CLI)*](how-to-set-up-instance-cli.md).
* Per eseguire un'installazione automatica usando uno script di distribuzione di esempio, vedere la versione con script di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (con script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Creare l'istanza di Azure Digital Twins

In questa sezione si creerà **una nuova istanza di Azure Digital Twins** usando il [portale di Azure](https://ms.portal.azure.com/). Passare al portale e accedere con le proprie credenziali.

Una volta nel portale, iniziare selezionando _Crea una risorsa_ nel menu di Home page dei servizi di Azure.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Selezione di ' crea una risorsa ' dal home page della portale di Azure":::

Cercare i dispositivi *gemelli digitali di Azure* nella casella di ricerca e scegliere il servizio **Azure Digital gemelli (anteprima)** dai risultati. Selezionare il pulsante _Crea_ per creare una nuova istanza del servizio.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Selezione di ' crea una risorsa ' dal home page della portale di Azure":::

Nella pagina *Crea risorsa* seguente inserire i valori indicati di seguito:
* **Sottoscrizione** : la sottoscrizione di Azure in uso
  - **Gruppo di risorse** : gruppo di risorse in cui distribuire l'istanza. Se non è già presente un gruppo di risorse esistente, è possibile crearne uno selezionando il collegamento *Crea nuovo* e immettendo un nome per un nuovo gruppo di risorse
* **Location** : area abilitata per i dispositivi gemelli di Azure per la distribuzione. Per altri dettagli sul supporto regionale, visita i [*prodotti Azure disponibili in base all'area (gemelli digitali di Azure)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Nome della risorsa** : un nome per l'istanza di Azure Digital gemelli. Il nome della nuova istanza deve essere univoco all'interno dell'area per la sottoscrizione. Ciò significa che se la sottoscrizione ha un'altra istanza di Azure Digital gemelli nell'area che usa già il nome scelto, verrà chiesto di selezionare un nome diverso.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Selezione di ' crea una risorsa ' dal home page della portale di Azure":::

Al termine, selezionare _Verifica + crea_ . Verrà visualizzata una pagina di riepilogo in cui è possibile esaminare i dettagli dell'istanza immessi e quindi fare clic su _Crea_ . 

### <a name="verify-success-and-collect-important-values"></a>Verificare l'esito positivo e raccogliere i valori importanti

Dopo aver premuto *Crea* , è possibile visualizzare lo stato della distribuzione dell'istanza nelle notifiche di Azure lungo la barra delle icone del portale. La notifica indicherà quando la distribuzione ha avuto esito positivo e sarà possibile selezionare il pulsante _Vai alla risorsa_ per visualizzare l'istanza creata.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Selezione di ' crea una risorsa ' dal home page della portale di Azure":::

In alternativa, se la distribuzione ha esito negativo, la notifica indicherà il motivo. Osservare il suggerimento del messaggio di errore e riprovare a creare l'istanza.

>[!TIP]
>Una volta creata l'istanza, è possibile tornare alla relativa pagina in qualsiasi momento cercando il nome dell'istanza nella barra di ricerca portale di Azure.

Dalla pagina *Panoramica* dell'istanza, annotare il *nome* , il *gruppo di risorse* e il *nome host* . Questi sono tutti i valori importanti che potrebbero essere necessari quando si continua a usare l'istanza di Azure Digital gemelli. Se altri utenti eseguiranno la programmazione in base all'istanza, è necessario condividerli con questi valori.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Selezione di ' crea una risorsa ' dal home page della portale di Azure":::

A questo punto si ha un'istanza di Azure Digital Twins pronta per l'uso. Successivamente, si forniranno le autorizzazioni utente di Azure appropriate per gestirlo.

## <a name="set-up-user-access-permissions"></a>Configurare le autorizzazioni di accesso utente

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Prima di tutto, aprire la pagina per l'istanza di Azure Digital Twins nell'portale di Azure. Selezionare *controllo di accesso (IAM)* dal menu dell'istanza. Selezionare il pulsante  *Aggiungi* in *Aggiungi un'assegnazione di ruolo* .

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Selezione di ' crea una risorsa ' dal home page della portale di Azure":::

Nella pagina *Aggiungi assegnazione ruolo* , inserire i valori (che devono essere completati da un utente con [autorizzazioni sufficienti](#prerequisites-permission-requirements) nella sottoscrizione di Azure):
* **Ruolo** : selezionare il *proprietario dei dati di Azure Digital gemelli* dal menu a discesa
* **Assegnare l'accesso a** : selezionare *Azure ad utente, gruppo o entità servizio* dal menu a discesa
* **Select** : consente di cercare il nome o l'indirizzo di posta elettronica dell'utente da assegnare. Quando si seleziona il risultato, l'utente viene visualizzato nella sezione *membri selezionati* .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Selezione di ' crea una risorsa ' dal home page della portale di Azure":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Al termine dell'immissione dei dettagli, fare clic sul pulsante *Salva* .

### <a name="verify-success"></a>Verificare l'esito positivo

È possibile visualizzare l'assegnazione di ruolo configurata in *controllo di accesso (IAM) > assegnazioni di ruolo* . L'utente dovrebbe essere visualizzato nell'elenco con un ruolo di *proprietario dei dati di Azure Digital gemelli* . 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Selezione di ' crea una risorsa ' dal home page della portale di Azure":::

A questo punto si ha un'istanza di Azure Digital Twins pronta per l'uso e sono state assegnate le autorizzazioni per gestirla.

## <a name="next-steps"></a>Passaggi successivi

Testare le singole chiamate API REST nell'istanza usando i comandi dell'interfaccia della riga di comando di Azure Digital gemelli: 
* [riferimento AZ DT](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Procedura: Usare l'interfaccia della riga di comando di Gemelli digitali di Azure*](how-to-use-cli.md)

In alternativa, vedere come connettere un'applicazione client all'istanza con il codice di autenticazione:
* [*Procedura: scrivere codice di autenticazione dell'app*](how-to-authenticate-client.md)