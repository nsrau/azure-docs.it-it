---
title: Configurare le impostazioni dello strumento di revisione-Content Moderator
titleSuffix: Azure Cognitive Services
description: Usare lo strumento di revisione per configurare o recuperare il team, i tag, i connettori, i flussi di lavoro e le credenziali per Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2ba314c814bdc92f62a607e28aefa30372bf297f
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757422"
---
# <a name="configure-the-review-tool"></a>Configurare lo strumento di revisione

Lo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) include diverse funzionalità importanti a cui è possibile accedere tramite il menu **Impostazioni** del dashboard.

![Menu delle impostazioni di Content Moderator Revisione](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Gestisci team e sottoteam

La scheda **Team** consente di gestire il team e i sottoteam&mdash;gruppi di utenti che possono ricevere notifiche quando vengono avviate determinate verifiche [umane](../review-api.md#reviews) . È possibile avere un solo team (creato quando si esegue l'iscrizione con lo strumento di revisione), ma è possibile creare più sottoteam. L'amministratore del team può invitare i membri, impostare le relative autorizzazioni e assegnarli a diversi sottoteam.

![Esaminare le impostazioni del team degli strumenti](images/settings-2-team.png)

I sottoteam sono utili per la creazione di team di escalation o di team dedicati alla revisione di specifiche categorie di contenuto. Ad esempio, è possibile inviare contenuto per adulti a un team separato per un'ulteriore verifica.

In questa sezione viene illustrato come creare i sottoteam e assegnare rapidamente le verifiche in tempo reale. È tuttavia possibile usare i [flussi di lavoro](workflows.md) per assegnare le revisioni in base a criteri specifici.

### <a name="create-a-subteam"></a>Creare un sottoteam

Passare alla sezione **sottoteam** e fare clic su **Aggiungi sottogruppo**. Immettere il nome del sottoteam nella finestra di dialogo e fare clic su **Salva**.

![Nome del sottoteam](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Invitare i colleghi

Non è possibile assegnare un utente a un sottoteam se non è già membro del team predefinito, quindi è necessario prima aggiungere i revisori al team predefinito. Fare clic su **invita** nella scheda **Team** .

![Invitare gli utenti](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Assegnare i colleghi al sottoteam

Fare clic sul pulsante **Aggiungi membro** per assegnare i membri del team predefinito a uno o più sottoteam. È possibile aggiungere a un sottoteam solo utenti esistenti. Per aggiungere nuovi utenti non presenti nello strumento di revisione, invitarli usando il pulsante "Invite" (Invita) nella pagina Team Settings (Impostazioni team).

![Assegnare i membri al sottoteam](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Assegna revisioni a sottoteam

Una volta creati i sottoteam e i membri assegnati, è possibile iniziare ad assegnare le [revisioni](../review-api.md#reviews) del contenuto a tali sottoteam. Questa operazione viene eseguita dalla scheda **Verifica** del sito.
Per assegnare contenuto a un sottoteam, fare clic sui puntini di sospensione nell'angolo in alto a destra, selezionare **Sposta in**e selezionare un sottoteam.

![Assegnare la revisione di un'immagine a un sottoteam](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Passare da un sottoteam a un altro

Se si è membri di più sottoteam, è possibile passare da un sottoteam all'altro per modificare le revisioni del contenuto visualizzate. Nella scheda **Revisione** selezionare il menu a discesa con etichetta **predefinito** e **scegliere Scegli sottogruppo**. È possibile visualizzare le revisioni del contenuto per diversi sottoteam, ma solo quelle di cui si è membri.

![Passare da un sottoteam a un altro](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tag

La **scheda Tag** consente di definire tag di moderazione personalizzati, oltre ai due tag di moderazione predefiniti&mdash;**Adult** (**a**) e **l'** utente (**r**). Quando si crea un tag personalizzato, diventa disponibile nei revisioni insieme ai tag predefiniti. È possibile modificare i tag che vengono visualizzati nelle verifiche cambiando le impostazioni di visibilità.

![Visualizzazione dei tag, incluse le caselle di controllo "visibile"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Crea tag personalizzati

Per creare un nuovo tag, è necessario immettere un codice, un nome e una descrizione brevi nei rispettivi campi.

- **Short code**: immettere un codice di due lettere per il tag. Esempio: **CB**
- **Nome**: immettere un nome di tag breve e descrittivo in lettere minuscole senza spazi. Esempio: **bullismo**.
- **Descrizione**: (facoltativo) immettere una descrizione del tipo di contenuto a cui è destinato il tag. Esempio: **rappresentazioni o istanze di cyber bullismo**.

Fare clic su **Aggiungi** per aggiungere un tag, quindi fare clic su **Salva** al termine della creazione di tag.

![Finestra di dialogo Crea nuovo tag dello strumento di Revisione](images/settings-3-tags.png)

### <a name="delete-tags"></a>Elimina tag

È possibile eliminare tag personalizzati selezionando l'icona del cestino accanto alle relative voci nell'elenco dei tag, ma non è possibile eliminare i tag predefiniti.

## <a name="connectors"></a>Connettori

La scheda **connettori** consente di gestire i connettori, che sono plug-in specifici del servizio in grado di elaborare il contenuto in modi diversi come parte dei [flussi di lavoro](../review-api.md#workflows)del contenuto.

Il connettore predefinito quando si crea un flusso di lavoro è il connettore Content Moderator, che può contrassegnare il contenuto come **adulto** o **audace**, trovare volgarità e così via. Tuttavia, è possibile usare altri connettori, elencati qui, purché siano disponibili le credenziali per i rispettivi servizi (per usare il connettore API Viso, ad esempio, è necessario ottenere una chiave di sottoscrizione [API viso](https://docs.microsoft.com/azure/cognitive-services/face/overview) ).

Lo [strumento di revisione](./human-in-the-loop.md) include i connettori seguenti:

- API Emozioni
- Face API
- Servizio cloud PhotoDNA
- API Analisi del testo

### <a name="add-a-connector"></a>Aggiungere un connettore

Per aggiungere un connettore (e renderlo disponibile per l'uso nei [flussi di lavoro](../review-api.md#workflows)del contenuto), selezionare il pulsante **Connetti** appropriato. Nella finestra di dialogo successiva immettere la chiave di sottoscrizione per il servizio. Al termine, il nuovo connettore verrà visualizzato nella parte superiore della pagina.

![Impostazioni per i connettori di Content Moderator](images/settings-4-connectors.png)

## <a name="workflows"></a>Flussi di lavoro

La scheda **flussi di lavoro** consente di gestire i flussi di [lavoro](../review-api.md#workflows). I flussi di lavoro sono filtri basati sul cloud per il contenuto e funzionano con i connettori per ordinare il contenuto in modi diversi e intraprendere le azioni appropriate. Qui è possibile definire, modificare e testare i flussi di lavoro. Per istruzioni su come eseguire questa operazione, vedere [definire e usare i flussi di lavoro](Workflows.md) .

![Impostazioni dei flussi di lavoro di Content Moderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Credenziali

La scheda **credenziali** consente di accedere rapidamente alla chiave di sottoscrizione content moderator, che sarà necessario accedere ai servizi di moderazione da una chiamata REST o da un SDK client.

![Credenziali di Content Moderator](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Usare credenziali esterne per i flussi di lavoro

Lo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) genera una chiave di valutazione gratuita per i servizi content moderator di Azure al momento dell'iscrizione, ma è anche possibile configurarla per l'uso di una chiave esistente dall'account Azure. Questa operazione è consigliata per scenari su larga scala, in quanto le chiavi di valutazione gratuite hanno limiti di utilizzo rigidi ([prezzi e limiti](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Se è stata creata una [risorsa content moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) in Azure, accedervi nel portale di Azure e selezionare il pannello **chiavi** . Copiare una delle chiavi.

![Chiavi di Content Moderator nel portale di Azure](images/credentials-azure-portal-keys.PNG)

Nella scheda **credenziali** [dello strumento di verifica](https://contentmoderator.cognitive.microsoft.com)andare al riquadro **Impostazioni flusso di lavoro** , selezionare **modifica**e incollare la chiave nel campo **OCP-gestione API-Subscription-Key** . A questo punto, i flussi di lavoro che chiamano le API di moderazione useranno le credenziali di Azure.

> [!NOTE]
> Gli altri due campi nel riquadro **Impostazioni flusso di lavoro** sono per elenchi di termini e immagini personalizzati. Per informazioni su questi concetti, vedere le guide [personalizzate](../try-terms-list-api.md) o le guide alle [Immagini personalizzate](../try-image-list-api.md) .

### <a name="use-your-azure-account-with-the-review-apis"></a>Usare l'account Azure con le API di Revisione

Per usare la chiave di Azure con le API di revisione, è necessario recuperare l'ID della risorsa. Passare alla risorsa Content Moderator nella portale di Azure e selezionare il pannello **Proprietà** . Copiare il valore di ID risorsa e incollarlo nel campo **ID risorsa** nell'elenco elementi consentiti della scheda **credenziali** dello strumento di verifica.

![ID risorsa di Content Moderator nel portale di Azure](images/credentials-azure-portal-resourceid.PNG)

Se la chiave di sottoscrizione è stata immessa in entrambe le posizioni, la chiave di valutazione fornita con l'account dello strumento di verifica non verrà usata, ma rimarrà disponibile.

## <a name="next-steps"></a>Passaggi successivi

Seguire la [Guida introduttiva dello strumento di revisione](../quick-start.md) per iniziare a usare lo strumento di revisione negli scenari di moderazione dei contenuti.