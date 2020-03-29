---
title: Configurare le impostazioni dello strumento di revisione - Moderatore del contenuto
titleSuffix: Azure Cognitive Services
description: Utilizzare lo strumento di revisione per configurare o recuperare il team, i tag, i connettori, i flussi di lavoro e le credenziali per Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220127"
---
# <a name="configure-the-review-tool"></a>Configurare lo strumento di revisione

Lo [strumento Revisione](https://contentmoderator.cognitive.microsoft.com) dispone di diverse funzionalità importanti a cui è possibile accedere tramite il menu **Impostazioni** nel dashboard.

![Menu delle impostazioni di Content Moderator Review](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Gestire team e sottoteam

La scheda **Team** ti consente di&mdash;gestire gruppi di utenti del tuo team e dei tuoi sottoteam che possono ricevere una notifica quando vengono avviate determinate [recensioni umane.](../review-api.md#reviews) È possibile avere un solo team (che viene creato quando ci si iscrive allo strumento Revisione), ma è possibile creare più sottoteam. L'amministratore del team può invitare membri, impostarne le autorizzazioni e assegnarli a diversi sottoteam.

![Esaminare le impostazioni del team degli strumenti](images/settings-2-team.png)

I sottoteam sono utili per la creazione di team di escalation o di team dedicati alla revisione di specifiche categorie di contenuto. Ad esempio, puoi inviare contenuti per adulti a un team separato per un'ulteriore revisione.

Questa sezione spiega come creare sottoteam e assegnare rapidamente recensioni al volo. È tuttavia possibile usare i [flussi di lavoro](workflows.md) per assegnare le revisioni in base a criteri specifici.

### <a name="create-a-subteam"></a>Creare un sottoteam

Passare alla sezione **Sottoteam** e fare clic su **Aggiungi sottoteam**. Immettere il nome del sottoteam nella finestra di dialogo e fare clic su **Salva**.

![Nome del sottoteam](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Invitare i compagni di squadra

Non è possibile assegnare un utente a un sottoteam se non è già membro del team predefinito, pertanto è necessario prima aggiungere revisori al team predefinito. Fare clic su **Invita** nella scheda **Team.**

![Invitare gli utenti](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Assegnare compagni di squadra al sottosquadra

Fare clic sul pulsante **Aggiungi membro** per assegnare i membri del team predefinito a uno o più sottoteam. È possibile aggiungere a un sottoteam solo utenti esistenti. Per aggiungere nuovi utenti non presenti nello strumento di revisione, invitarli usando il pulsante "Invite" (Invita) nella pagina Team Settings (Impostazioni team).

![Assegnare i membri al sottoteam](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Assegnare recensioni ai sottoteam

Dopo aver creato i sottoteam e i membri assegnati, è possibile iniziare ad assegnare [revisioni](../review-api.md#reviews) del contenuto a tali team secondari. Questa operazione viene eseguita dalla scheda **Revisione** del sito.
Per assegnare contenuto a un sottoteam, fare clic sui puntini di sospensione nell'angolo superiore destro, selezionare **Sposta in**e selezionare un sottoteam.

![Assegnare la revisione di un'immagine a un sottoteam](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Passare da un sottoteam a un altro

Se si è membri di più team secondari, è possibile passare da un sottoteam all'altro per modificare le revisioni dei contenuti visualizzate automaticamente. Nella scheda **Revisione,** selezionare il menu a discesa **Predefinito** e selezionare **Scegli sottoteam**. È possibile visualizzare le revisioni dei contenuti per diversi sottoteam, ma solo quelli di cui sono membri.

![Passare da un sottoteam a un altro](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tag

La scheda **Tag** consente di definire tag di moderazione&mdash;personalizzati oltre ai due tag di moderazione predefiniti**èadulto** (**a**) e **isracy** (**r**). Quando crei un tag personalizzato, questo diventa disponibile nelle recensioni insieme ai tag predefiniti. Puoi modificare i tag che vengono visualizzati nelle recensioni cambiando le loro impostazioni di visibilità.

![Visualizzazione Tag, incluse le caselle di controllo "È visibile"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Creare tag personalizzati

Per creare un nuovo tag, è necessario immettere un codice breve, un nome e una descrizione nei rispettivi campi.

- **Codice breve**: Inserisci un codice di due lettere per il tag. Esempio: **cb**
- **Nome**: Immettere un nome di tag breve e descrittivo in minuscolo senza spazi. Esempio: **isbullying**.
- **Descrizione**: (facoltativo) Immettere una descrizione del tipo di contenuto a cui è destinato il tag. Esempio: **rappresentazioni o istanze di bullismo informatico**.

Fare clic su **Aggiungi** per aggiungere un tag e fare clic su **Salva** al termine della creazione dei tag.

![Finestra di dialogo Crea nuovo tag dello strumento di revisione](images/settings-3-tags.png)

### <a name="delete-tags"></a>Eliminare i tag

È possibile eliminare i tag personalizzati selezionando l'icona del cestino accanto alle relative voci nell'elenco Tag, ma non è possibile eliminare i tag predefiniti.

## <a name="connectors"></a>Connettori

La scheda **Connettori** consente di gestire i connettori, ovvero plug-in specifici del servizio in grado di elaborare il contenuto in modi diversi come parte dei [flussi](../review-api.md#workflows)di lavoro del contenuto.

Il connettore predefinito quando si crea un flusso di lavoro è il connettore Moderatore contenuto, che può contrassegnare il contenuto come **adulto** o **vivace,** trovare volgarità e così via. Tuttavia, è possibile utilizzare altri connettori, elencati di seguito, purché si disponga delle credenziali per i rispettivi servizi (per utilizzare il connettore Faccia, ad esempio, è necessario ottenere una chiave di sottoscrizione [Face).](https://docs.microsoft.com/azure/cognitive-services/face/overview)

Lo [strumento revisione](./human-in-the-loop.md) include i seguenti connettori:

- Emozioni
- Viso
- Servizio cloud PhotoDNA
- Text Analytics

### <a name="add-a-connector"></a>Aggiungere un connettore

Per aggiungere un connettore e renderlo disponibile per l'utilizzo nei [flussi](../review-api.md#workflows)di lavoro del contenuto, selezionare il pulsante **Connetti** appropriato. Nella finestra di dialogo successiva immettere la chiave di sottoscrizione per il servizio. Al termine, il nuovo connettore dovrebbe essere visualizzato nella parte superiore della pagina.

![Impostazioni per i connettori di Content Moderator](images/settings-4-connectors.png)

## <a name="workflows"></a>Flussi di lavoro

La scheda **Flussi di lavoro** consente di gestire i [flussi di lavoro.](../review-api.md#workflows) I flussi di lavoro sono filtri basati su cloud per il contenuto e utilizzano i connettori per ordinare il contenuto in modi diversi e intraprendere le azioni appropriate. Qui è possibile definire, modificare e testare i flussi di lavoro. Per istruzioni su come eseguire questa operazione, vedere [Definire e usare i flussi di lavoro.](Workflows.md)

![Impostazioni dei flussi di lavoro di Content Moderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Credenziali

La scheda **Credenziali** consente di accedere rapidamente alla chiave di sottoscrizione di Content Moderator, che sarà necessario accedere a qualsiasi servizio di moderazione da una chiamata REST o da un SDK client.

![Credenziali di Content Moderator](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Usare credenziali esterne per i flussi di lavoroUse external credentials for workflows

Lo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) genera una chiave di valutazione gratuita per i servizi di Azure Content Moderator al momento dell'iscrizione, ma è anche possibile configurarla per l'uso di una chiave esistente dall'account azure.The Review tool generates a free trial key for Azure Content Moderator services when you sign up, but you can also configure it to use an existing key from your Azure account. Questa operazione è consigliata per scenari su larga scala, poiché le chiavi di prova gratuita hanno limiti di utilizzo rigorosi ([Prezzi e limiti](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Se è stata creata una [risorsa Moderatore contenuto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) in Azure, passare a essa nel portale di Azure e selezionare il pannello **Chiavi.If** you have created a Content Moderator resource in Azure, navigate to it in the Azure portal and select the Keys blade. Copia una delle tue chiavi.

![Chiavi di Content Moderator nel portale di Azure](images/credentials-azure-portal-keys.PNG)

Nella scheda **Credenziali** dello [strumento di revisione](https://contentmoderator.cognitive.microsoft.com)passare al riquadro Impostazioni flusso di **lavoro,** selezionare **Modifica**e incollare la chiave nel campo **Ocp-Apim-Subscription-Key.** A questo punto, i flussi di lavoro che chiamano le API di moderazione utilizzeranno le credenziali di Azure.Now, workflows that call the moderation APIs will use your Azure credential.

> [!NOTE]
> Gli altri due campi nel riquadro **Impostazioni flusso di lavoro** sono relativi a elenchi di termini e immagini personalizzati. Per informazioni su questi argomento, consulta le guide [Termini personalizzati](../try-terms-list-api.md) o [Immagini personalizzate.](../try-image-list-api.md)

### <a name="use-your-azure-account-with-the-review-apis"></a>Usare l'account Azure con le API di revisioneUse your Azure account with the review APIs

Per usare la chiave di Azure con le API di revisione, è necessario recuperare l'ID risorsa. Passare alla risorsa Content Moderator nel portale di Azure e selezionare il pannello **Proprietà.Go** to your Content Moderator resource in the Azure portal and select the Properties blade. Copiare il valore ID risorsa e incollarlo nel campo **ID risorsa nella** lista bianca della scheda **Credenziali** dello strumento revisione.

![ID risorsa di Content Moderator nel portale di Azure](images/credentials-azure-portal-resourceid.PNG)

Se hai inserito la chiave di sottoscrizione in entrambe le posizioni, la chiave di prova fornita con il tuo account dello strumento di revisione non verrà utilizzata, ma rimarrà disponibile.

## <a name="next-steps"></a>Passaggi successivi

Seguire la [Guida introduttiva dello strumento di revisione](../quick-start.md) per iniziare a usare lo strumento Di verifica in scenari di moderazione del contenuto.