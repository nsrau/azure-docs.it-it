---
title: Configurare le impostazioni degli strumenti di revisione - Content Moderator
titlesuffix: Azure Cognitive Services
description: Utilizzare lo strumento di revisione per configurare o recuperare il team, i tag, i connettori, flussi di lavoro e le credenziali di Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: f88ccbabc925b651abbc06f571a9d4220ed8aeb2
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756510"
---
# <a name="configure-the-review-tool"></a>Configurare lo strumento di revisione

Il [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) include diverse funzionalità importanti che è possibile accedere tramite il **impostazioni** menu del dashboard.

![Contenuto Moderator verifica troppo menu impostazioni](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Gestisci team e sottoteam

Il **Team** scheda consente di gestire i team e sottoteam&mdash;gruppi di utenti autorizzati possano ricevere notifiche quando determinati [revisione umana](../review-api.md#reviews) siano avviati. È possibile avere solo un team (che vengono creati quando si effettua l'iscrizione con lo strumento di revisione), ma è possibile creare più sottogruppi. L'amministratore del team può invitare membri, impostare le relative autorizzazioni e assegnarli ai diversi sottoteam.

![Rivedere le impostazioni del team degli strumenti](images/settings-2-team.png)

I sottoteam sono utili per la creazione di team di escalation o di team dedicati alla revisione di specifiche categorie di contenuto. Ad esempio, è possibile inviare i contenuti per adulti a un team separato per analizzarli più attentamente.

Questa sezione viene illustrato come creare sottogruppi e assegnare rapidamente le verifiche in tempo reale. È tuttavia possibile usare i [flussi di lavoro](workflows.md) per assegnare le revisioni in base a criteri specifici.

### <a name="create-a-subteam"></a>Creare un sottoteam

Andare alla **sottoteam** della sezione e fare clic su **aggiungere Sub-Team**. Immettere il nome del sub-team nella finestra di dialogo e fare clic su **salvare**.

![Nome del sottoteam](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Invitare i colleghi

È possibile assegnare un utente a un Sub-team se non sono già un membro del team predefinito, pertanto è necessario innanzitutto aggiungere revisori per il team predefinito. Fare clic su **invitare** nel **Team** scheda.

![Invitare gli utenti](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Assegnare i colleghi a subteam

Scegliere il **Add Member** pulsante per assegnare i membri del team predefinito per uno o più sottogruppi. È possibile aggiungere a un sottoteam solo utenti esistenti. Per aggiungere nuovi utenti non presenti nello strumento di revisione, invitarli usando il pulsante "Invite" (Invita) nella pagina Team Settings (Impostazioni team).

![Assegnare i membri al sottoteam](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Assegnare le verifiche di sottoteam

Dopo aver creato i sottogruppi e assegnato membri, è possibile avviare assegnazione del contenuto [esamina](../review-api.md#reviews) per tali sottogruppi. Tale scopo, aprire il **revisione** scheda del sito.
Per assegnare il contenuto a un Sub-team, fare clic sui puntini di sospensione nell'angolo superiore destro, seleziona **spostarne**e selezionare una Sub-team.

![Assegnare la revisione di un'immagine a un sottoteam](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Passare da un sottoteam a un altro

Se sei un membro di più di una Sub-team, è possibile passare tra tali sottoteam per modificare le revisioni del contenuto vengono visualizzate automaticamente. Nel **revisione** scheda, selezionare il menu a discesa etichettato **predefinito** e selezionare **Sub-team scegliere**. È possibile visualizzare le verifiche di contenuto per sottoteam diverso, ma solo quelle di cui il membro.

![Passare da un sottoteam a un altro](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tag

Il **tag** scheda consente di definire tag personalizzati moderazione oltre i tag di moderazione due predefiniti&mdash;**isadult** (**un**) e **isracy**  (**r**). Quando si crea un tag personalizzato, diventa disponibile per le revisioni insieme i tag predefiniti. È possibile modificare i tag che compaiono nelle revisioni passando le relative impostazioni di visibilità.

![Visualizzare i tag, incluse le caselle di controllo "Is visible"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Creare tag personalizzati

Per creare un nuovo tag, è necessario immettere un codice breve, un nome e una descrizione nei rispettivi campi.

- **Codice breve**: Immettere un codice di due lettere per il tag. Esempio: **cb**
- **Nome**: Immettere un nome di tag breve e descrittivo in lettere minuscole, senza spazi. Esempio: **isbullying**.
- **Descrizione**: (facoltativo) immettere una descrizione del tipo di contenuto che le destinazioni di tag. Esempio: **Le raffigurazioni o le istanze di informatica bullismo**.

Fare clic su **Add** per aggiungere un tag e fare clic su **salvare** al termine della creazione di tag.

![Strumento di revisione Crea finestra di dialogo Nuovo tag](images/settings-3-tags.png)

### <a name="delete-tags"></a>Eliminare i tag

È possibile eliminare i tag personalizzati selezionando l'icona del Cestino accanto alle relative voci dell'elenco di tag, ma non è possibile eliminare i tag predefiniti.

## <a name="connectors"></a>Connettori

Il **connettori** scheda consente di gestire i connettori, ovvero i plug-in specifici del servizio in grado di elaborare il contenuto in diversi modi come parte del contenuto [i flussi di lavoro](../review-api.md#workflows).

Il connettore predefinito quando si crea un flusso di lavoro è il connettore di Content Moderator, che è possibile contrassegnare il contenuto come **adult** oppure **pornografici**, trovare il contenuto volgare e così via. Tuttavia, è possibile usare gli altri connettori, elencati in questo caso, purché si disponga delle credenziali per i rispettivi servizi (per usare il connettore API viso, ad esempio, è necessario ottenere un [API viso](https://docs.microsoft.com/azure/cognitive-services/face/overview) chiave di sottoscrizione).

Il [strumento di revisione](./human-in-the-loop.md) include connettori seguenti:

- API Emozioni
- API Viso
- Servizio cloud PhotoDNA
- API Analisi del testo

### <a name="add-a-connector"></a>Aggiungere un connettore

Aggiungere un connettore (e renderlo disponibile per l'uso nel contenuto [flussi di lavoro](../review-api.md#workflows)), selezionare un valore appropriato **Connect** pulsante. Nella finestra di dialogo successiva, immettere la chiave di sottoscrizione per il servizio. Al termine, verrà visualizzato il nuovo connettore nella parte superiore della pagina.

![Impostazioni per i connettori di Content Moderator](images/settings-4-connectors.png)

## <a name="workflows"></a>Flussi di lavoro

Il **flussi di lavoro** scheda consente di gestire le [i flussi di lavoro](../review-api.md#workflows). I flussi di lavoro sono i filtri basati su cloud per il contenuto e funzionano con i connettori per ordinare il contenuto in modi diversi e intraprendere le azioni appropriate. In questo caso, è possibile definire, modificare e testare i flussi di lavoro. Visualizzare [definire e usare i flussi di lavoro](Workflows.md) per indicazioni su come eseguire questa operazione.

![Impostazioni dei flussi di lavoro di Content Moderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Credenziali

Il **credenziali** scheda fornisce accesso rapido per la chiave di sottoscrizione Content Moderator, dovrai accedere a qualsiasi servizio di moderazione da una chiamata REST o SDK client.

![Credenziali di Content Moderator](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Usare le credenziali esterne per i flussi di lavoro

Il [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) genera una chiave di valutazione gratuita per servizi di Azure Content Moderator quando si effettua l'iscrizione, ma è anche possibile configurarlo per usare un oggetto esistente della chiave dal proprio account Azure. È consigliato per gli scenari su larga scala, come le chiavi di valutazione gratuite con limiti di utilizzo strict ([prezzi e limiti](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Se è stato creato un [risorsa Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) in Azure, passare a esso nel portale di Azure e seleziona il **chiavi** pannello. Copiare una delle chiavi.

![Chiavi di Content Moderator nel portale di Azure](images/credentials-azure-portal-keys.PNG)

Nel [strumento di revisione](https://contentmoderator.cognitive.microsoft.com)del **credenziali** scheda, passa al **le impostazioni del flusso di lavoro** riquadro, selezionare **modifica**e incollarvi la chiave di **Ocp-Apim-Subscription-Key** campo. A questo punto, i flussi di lavoro che chiamano l'API di moderazione utilizzerà le credenziali di Azure.

> [!NOTE]
> Gli altri due campi nel **delle impostazioni del flusso di lavoro** riquadro sono per gli elenchi di termini e l'immagine personalizzati. Vedere le [condizioni personalizzate](../try-terms-list-api.md) oppure [immagini personalizzate](../try-image-list-api.md) Guide per ulteriori informazioni, vedere.

### <a name="use-your-azure-account-with-the-review-apis"></a>Usare l'account Azure con l'API per la revisione

Per usare la chiave di Azure con l'API per la revisione, è necessario recuperare l'ID di risorsa. Passare alla risorsa di Content Moderator nel portale di Azure e seleziona il **proprietà** pannello. Copiare il valore di ID di risorsa e incollarlo nella **ID di risorsa nell'elenco elementi consentiti** campo lo strumento di revisione **credenziali** scheda.

![ID risorsa di Content Moderator nel portale di Azure](images/credentials-azure-portal-resourceid.PNG)

Se ci si trova la chiave di sottoscrizione in entrambe le posizioni, la chiave di prova fornito con l'account dello strumento di verifica non verrà utilizzata ma rimarrà disponibile.

## <a name="next-steps"></a>Passaggi successivi

Seguire le [Guida introduttiva di strumento di revisione](../quick-start.md) per iniziare a usare lo strumento di revisione in scenari di moderazione dei contenuti.