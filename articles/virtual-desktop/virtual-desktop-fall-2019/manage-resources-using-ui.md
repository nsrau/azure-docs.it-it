---
title: Distribuire lo strumento di gestione con un modello di Azure Resource Manager - Azure
description: Come installare uno strumento di interfaccia utente con un modello di Azure Resource Manager per gestire le risorse di desktop virtuali Windows (classiche).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f96365bcdf64d19dc0b894f2f1230233b3137bc7
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842652"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-an-azure-resource-manager-template"></a>Distribuire uno strumento di gestione di desktop virtuali Windows (classico) con un modello di Azure Resource Manager

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows (versione classica), che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows.

Le istruzioni di questo articolo illustrano come distribuire l'interfaccia utente usando un modello di Azure Resource Manager.

## <a name="important-considerations"></a>Considerazioni importanti

Poiché l'app richiede il consenso per interagire con Desktop virtuale Windows, lo strumento non supporta scenari B2B (Business-to-Business). Ogni sottoscrizione del tenant di Azure Active Directory (AAD) dovrà avere una distribuzione separata dello strumento di gestione.

Questo strumento di gestione è un esempio. Microsoft fornirà importanti aggiornamenti di qualità e sicurezza. Il [codice sorgente è disponibile in GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Clienti e partner sono invitati a personalizzare lo strumento in base ai loro requisiti aziendali.

I browser seguenti sono compatibili con lo strumento di gestione:
- Google Chrome 68 o versione successiva
- Microsoft Edge 40.15063 o versione successiva
- Mozilla Firefox 52.0 o versione successiva
- Safari 10 o versione successiva (solo macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Requisiti per la distribuzione dello strumento di gestione

Prima di distribuire lo strumento di gestione, è necessario che un utente di Azure Active Directory (Azure AD) crei una registrazione dell'app e distribuisca l'interfaccia utente di gestione. Questo utente dovrà avere:

- Azure AD Multi-Factor Authentication (multi-factor authentication) disabilitato
- L'autorizzazione per creare risorse nella sottoscrizione di Azure
- L'autorizzazione per creare un'applicazione di Azure AD. Seguire questa procedura per verificare se l'utente ha le autorizzazioni necessarie in base alle istruzioni indicate in [Autorizzazioni necessarie](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Dopo aver distribuito e configurato lo strumento di gestione, è consigliabile chiedere a un utente di avviare l'interfaccia utente di gestione per verificarne il corretto funzionamento. L'utente che avvia l'interfaccia utente di gestione deve avere un'assegnazione di ruolo che gli consenta di visualizzare o modificare il tenant di Desktop virtuale Windows.

## <a name="deploy-the-management-tool"></a>Distribuire lo strumento di gestione

Prima di iniziare, assicurarsi che le app server e client abbiano il consenso visitando la [pagina di consenso di Desktop virtuale Windows](https://rdweb.wvd.microsoft.com) per il tenant di Azure Active Directory (AAD) rappresentato.

Per distribuire il modello di Azure Resource Manager, seguire queste istruzioni:

1. Passare alla pagina [Azure RDS-Templates di GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Distribuire il modello in Azure.
    - Se la distribuzione viene eseguita in una sottoscrizione Enterprise, scorrere in basso e selezionare **Distribuisci in Azure**.
    - Se la distribuzione in Azure viene eseguita in una sottoscrizione di Cloud Solution Provider, seguire queste istruzioni:
        1. Scorrere in basso e fare clic con il pulsante destro del mouse su **Distribuisci in Azure**, quindi scegliere **Copia collegamento**.
        2. Aprire un editor di testo, come il Blocco note, e incollarvi il collegamento.
        3. Subito dopo <https://portal.azure.com/> e prima dell'hashtag (#) immettere una chiocciola (@) seguita dal nome di dominio del tenant. Ecco un esempio del formato: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Accedere al portale di Azure come utente con autorizzazioni di Amministratore/Collaboratore per la sottoscrizione di Cloud Solution Provider.
        5. Incollare nella barra degli indirizzi il collegamento copiato nell'editor di testo.
3. Per l'immissione dei parametri, procedere come segue:
    - Per il parametro **isServicePrincipal** selezionare **false**.
    - Per le credenziali, immettere le credenziali di Azure AD con l'autenticazione a più fattori disabilitata. Queste credenziali verranno usate per creare l'applicazione di Azure AD e le risorse di Azure. Per altre informazioni, vedere [Requisiti per la distribuzione dello strumento di gestione](#what-you-need-to-deploy-the-management-tool).
    - Per **applicationName** usare un nome univoco per l'applicazione che verrà registrata in Azure Active Directory. Questo nome verrà usato anche per l'URL dell'app Web. Ad esempio, è possibile usare un nome come "Apr3UX".
4. Dopo aver specificato i parametri, accettare le condizioni e selezionare **Acquista**.

## <a name="provide-consent-for-the-management-tool"></a>Fornire il consenso per lo strumento di gestione

Quando il modello di Azure Resource Manager di GitHub viene completato, nel portale di Azure sarà disponibile un gruppo di risorse contenente due servizi app con un piano di servizio app.

Prima di accedere e usare lo strumento di gestione, è necessario fornire il consenso per la nuova applicazione di Azure AD associata. Fornendo il consenso, si autorizza lo strumento di gestione a effettuare chiamate di gestione a Desktop virtuale Windows per conto dell'utente connesso.

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra le autorizzazioni concesse quando si fornisce il consenso allo strumento di gestione dell'interfaccia utente.](../media/management-ui-delegated-permissions.png)

Per determinare quale utente è possibile usare per accedere allo strumento, passare alla [pagina di impostazioni utente di Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) e notare il valore di **Gli utenti possono fornire il consenso alle app che accedono ai dati aziendali per loro conto**.

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra se gli utenti possono concedere il consenso alle applicazioni solo per se stessi.](../media/management-ui-user-consent-allowed.png)

- Se il valore è impostato su **Sì**, è possibile accedere con qualsiasi account utente di Azure Active Directory e fornire il consenso solo per tale utente. Se tuttavia in seguito si accede allo strumento di gestione con un account utente diverso, è necessario eseguire di nuovo la stessa procedura di consenso.
- Se il valore è impostato su **No**, è necessario accedere come amministratore globale in Azure Active Directory e fornire il consenso amministratore per tutti gli utenti della directory. Nessun altro utente visualizzerà la richiesta di consenso.


Dopo aver deciso quale utente usare, seguire queste istruzioni per fornire il consenso allo strumento:

1. Passare alle risorse di Azure, selezionare la risorsa app Azure Services con il nome specificato nel modello (ad esempio, Apr3UX) e passare all'URL associato. ad esempio,  `https://rdmimgmtweb-210520190304.azurewebsites.net` .
2. Accedere usando l'account utente appropriato di Azure Active Directory.
3. Se l'autenticazione è stata eseguita con un amministratore globale, è ora possibile selezionare la casella di controllo accanto a **Acconsenti per conto dell'organizzazione**. Selezionare **Accetto** per fornire il consenso.

   > [!div class="mx-imgBorder"]
   > ![Screenshot che mostra la pagina di consenso completa che verrà visualizzata per l'utente o l'amministratore.](../media/management-ui-consent-page.png)

A questo punto si potrà accedere allo strumento di gestione.

## <a name="use-the-management-tool"></a>Usare lo strumento di gestione

Dopo aver fornito il consenso per l'organizzazione o per uno specifico utente, è possibile accedere allo strumento di gestione in qualsiasi momento.

Per avviare lo strumento, seguire queste istruzioni:

1. Selezionare la risorsa Servizi app di Azure con il nome specificato nel modello, ad esempio Apr3UX, e passare all'URL associato, ad esempio `https://rdmimgmtweb-210520190304.azurewebsites.net`.
2. Accedere con le credenziali di Desktop virtuale Windows.
3. Quando viene chiesto di scegliere un gruppo di tenant, selezionare **Default Tenant Group** (Gruppo di tenant predefinito) nell'elenco a discesa.
4. Quando si seleziona **Default Tenant Group** (Gruppo di tenant predefinito), sul lato destro della finestra viene visualizzato un menu. In questo menu trovare il nome del gruppo di tenant e selezionarlo.

  > [!NOTE]
  > Se è disponibile un gruppo di tenant personalizzato, immettere il nome manualmente invece di scegliere una voce dell'elenco a discesa.

## <a name="report-issues"></a>Segnalare i problemi

Se si verificano problemi con lo strumento di gestione o altri strumenti di Desktop virtuale Windows, seguire le istruzioni nell'articolo relativo ai [modelli di Azure Resource Manager per Servizi Desktop remoto](https://github.com/Azure/RDS-Templates/blob/master/README.md) per segnalarli in GitHub.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come distribuire lo strumento di gestione e come connettersi, è possibile scoprire come usare il servizio di Azure per monitorare i problemi dei servizi e gli avvisi di integrità. Per altre informazioni, vedere l'esercitazione [Configurare gli avvisi dei servizi](set-up-service-alerts-2019.md).