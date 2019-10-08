---
title: Distribuire lo strumento di gestione - Azure
description: Come installare lo strumento dell'interfaccia utente per gestire le risorse di Desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: helohr
ms.openlocfilehash: f07403d8d0b2c6d0dd7a6b851a87b47b0c32501a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679494"
---
# <a name="tutorial-deploy-a-management-tool"></a>Esercitazione: Distribuire uno strumento di gestione

Lo strumento di gestione fornisce un'interfaccia utente per la gestione delle risorse di Desktop virtuale Windows. Questa esercitazione descrive come distribuire lo strumento di gestione e come connettersi.

>[!NOTE]
>Le istruzioni riguardano una configurazione specifica di Desktop virtuale Windows che può essere usata con processi esistenti dell'organizzazione.

## <a name="important-considerations"></a>Considerazioni importanti

Poiché l'app richiede il consenso per interagire con Desktop virtuale Windows, lo strumento non supporta scenari B2B (Business-to-Business). Ogni sottoscrizione del tenant di Azure Active Directory (AAD) dovrà avere una distribuzione separata dello strumento di gestione.

Questo strumento di gestione è un esempio. Microsoft fornirà importanti aggiornamenti di qualità e sicurezza. Il [codice sorgente è disponibile in GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Clienti e partner sono invitati a personalizzare lo strumento in base ai loro requisiti aziendali.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Requisiti per eseguire il modello di Azure Resource Manager

Prima di distribuire il modello di Azure Resource Manager, è necessario un utente di Azure Active Directory per distribuire l'interfaccia utente di gestione. Questo utente dovrà avere:

- La funzionalità Multi-Factor Authentication (MFA) disabilitata
- L'autorizzazione per creare risorse nella sottoscrizione di Azure
- L'autorizzazione per creare un'applicazione di Azure AD. Seguire questi passaggi per verificare se l'utente ha le [autorizzazioni necessarie](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

Dopo aver distribuito il modello di Azure Resource Manager, è consigliabile avviare l'interfaccia utente di gestione per la convalida. Questo utente dovrà avere:
- Un'assegnazione di ruolo per visualizzare o modificare il tenant di Desktop virtuale Windows

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Eseguire il modello di Azure Resource Manager per effettuare il provisioning dell'interfaccia utente di gestione

Prima di iniziare, assicurarsi che le app server e client abbiano il consenso visitando la [pagina di consenso di Desktop virtuale Windows](https://rdweb.wvd.microsoft.com) per il tenant di Azure Active Directory (AAD) rappresentato.

Per distribuire il modello di Azure Resource Manager, seguire queste istruzioni:

1. Passare alla pagina [Azure RDS-Templates di GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Distribuire il modello in Azure.
    - Se la distribuzione viene eseguita in una sottoscrizione Enterprise, scorrere in basso e selezionare **Distribuisci in Azure**. Vedere [Istruzioni per i parametri del modello](#guidance-for-template-parameters).
    - Se la distribuzione in Azure viene eseguita in una sottoscrizione di Cloud Solution Provider, seguire queste istruzioni:
        1. Scorrere in basso e fare clic con il pulsante destro del mouse su **Distribuisci in Azure**, quindi scegliere **Copia collegamento**.
        2. Aprire un editor di testo, come il Blocco note, e incollarvi il collegamento.
        3. Subito dopo <https://portal.azure.com/> e prima dell'hashtag (#) immettere una chiocciola (@) seguita dal nome di dominio del tenant. Ecco un esempio del formato: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Accedere al portale di Azure come utente con autorizzazioni di Amministratore/Collaboratore per la sottoscrizione di Cloud Solution Provider.
        5. Incollare nella barra degli indirizzi il collegamento copiato nell'editor di testo.

### <a name="guidance-for-template-parameters"></a>Istruzioni per i parametri del modello
Ecco come immettere i parametri per configurare lo strumento:

- URL del broker di Desktop remoto:\//rdbroker.wvd.microsoft.com/
- URL delle risorse: https:\//mrs-prod.ame.gbl/mrs-RDInfra-prod
- Usare le credenziali di AAD con la funzionalità MFA disabilitata per accedere ad Azure. Vedere [Requisiti per eseguire il modello di Azure Resource Manager](#what-you-need-to-run-the-azure-resource-manager-template).
- Usare un nome univoco per l'applicazione che verrà registrata in Azure Active Directory per lo strumento di gestione, ad esempio Apr3UX.

## <a name="provide-consent-for-the-management-tool"></a>Fornire il consenso per lo strumento di gestione

Quando il modello di Azure Resource Manager di GitHub viene completato, nel portale di Azure sarà disponibile un gruppo di risorse contenente due servizi app con un piano di servizio app.

Prima di accedere e usare lo strumento di gestione, sarà necessario fornire il consenso per la nuova applicazione di Azure Active Directory associata allo strumento. Fornendo il consenso, si autorizza lo strumento di gestione a effettuare chiamate di gestione a Desktop virtuale Windows per conto dell'utente connesso.

![Screenshot che mostra le autorizzazioni concesse quando si fornisce il consenso allo strumento di gestione dell'interfaccia utente.](media/management-ui-delegated-permissions.png)

Per determinare quale utente è possibile usare per accedere allo strumento, passare alla [pagina di impostazioni utente di Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) e notare il valore di **Gli utenti possono fornire il consenso alle app che accedono ai dati aziendali per loro conto**.

![Screenshot che mostra se gli utenti possono concedere il consenso alle applicazioni solo per se stessi.](media/management-ui-user-consent-allowed.png)

- Se il valore è impostato su **Sì**, è possibile accedere con qualsiasi account utente di Azure Active Directory e fornire il consenso solo per tale utente. Se tuttavia in seguito si accede allo strumento di gestione con un account utente diverso, è necessario eseguire di nuovo la stessa procedura di consenso.
- Se il valore è impostato su **No**, è necessario accedere come amministratore globale in Azure Active Directory e fornire il consenso amministratore per tutti gli utenti della directory. Nessun altro utente visualizzerà la richiesta di consenso.


Dopo aver deciso quale utente usare, seguire queste istruzioni per fornire il consenso allo strumento:

1. Passare alle risorse di Azure, selezionare la risorsa Servizi app di Azure con il nome specificato nel modello, ad esempio Apr3UX, e passare all'URL associato, ad esempio <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Accedere usando l'account utente appropriato di Azure Active Directory.
3. Se l'autenticazione è stata eseguita con un amministratore globale, è ora possibile selezionare la casella di controllo accanto a **Acconsenti per conto dell'organizzazione**. Selezionare **Accetto** per fornire il consenso.
   
   ![Screenshot che mostra la pagina di consenso completa che verrà visualizzata per l'utente o l'amministratore.](media/management-ui-consent-page.png)

A questo punto si potrà accedere allo strumento di gestione.

## <a name="use-the-management-tool"></a>Usare lo strumento di gestione

Dopo aver fornito il consenso per l'organizzazione o per uno specifico utente, è possibile accedere allo strumento di gestione in qualsiasi momento.

Per avviare lo strumento, seguire queste istruzioni:

1. Selezionare la risorsa Servizi app di Azure con il nome specificato nel modello, ad esempio Apr3UX, e passare all'URL associato, ad esempio <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Accedere con le credenziali di Desktop virtuale Windows.
3. Quando viene chiesto di scegliere un gruppo di tenant, selezionare **Default Tenant Group** (Gruppo di tenant predefinito) nell'elenco a discesa.

> [!NOTE]
> Se è disponibile un gruppo di tenant personalizzato, immettere il nome manualmente invece di scegliere una voce dell'elenco a discesa.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come distribuire lo strumento di gestione e come connettersi, è possibile scoprire come usare Integrità dei servizi di Azure per monitorare i problemi dei servizi e gli avvisi di integrità.

> [!div class="nextstepaction"]
> [Configurare gli avvisi dei servizi](./set-up-service-alerts.md)
