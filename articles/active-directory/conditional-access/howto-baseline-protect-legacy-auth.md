---
title: Linea di base dei criteri blocco autenticazione legacy (anteprima) - Azure Active Directory
description: Criteri di accesso condizionale per i protocolli di autenticazione legacy di blocco
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8e9ea8956d87e2ec47cc65495e81d8a0f0ad8cb
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560930"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>Criterio di base: Autenticazione legacy di blocco (anteprima)

Per consentire agli utenti di accedere facilmente alle app cloud, Azure Active Directory (Azure AD) supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. L'autenticazione legacy è un termine che fa riferimento a una richiesta di autenticazione eseguita da:

* Client Office meno recenti che non usano l'autenticazione moderna (ad esempio, i client di Office 2010)
* Qualsiasi client che Usa protocolli di posta elettronica legacy, ad esempio IMAP o SMTP o POP3

Oggi, maggior parte di tutti i compromessi in termini tentativi di accesso provengono dall'autenticazione legacy. L'autenticazione legacy non supporta l'autenticazione a più fattori (MFA). Anche se si dispone di un criterio di autenticazione a più fattori abilitato nella directory, un attore malintenzionato potrebbe può eseguire l'autenticazione usando un protocollo legacy e ignorare l'autenticazione a più fattori.

Il modo migliore per proteggere l'account da richieste di autenticazione dannoso eseguite dai protocolli legacy è bloccare questi tentativi tutti insieme. Per renderne più semplice per impedire tutte le richieste di accesso inviate dai protocolli legacy a, viene creato un criterio di base che esegue questa operazione.

**L'autenticazione legacy di blocco** viene [criterio di base](concept-baseline-protection.md) che blocca tutte le richieste di autenticazione eseguite dai protocolli legacy. L'autenticazione moderna deve essere usato per accedere correttamente per tutti gli utenti. Usato in combinazione con altri criteri di base, tutte le richieste provenienti da protocolli legacy verranno bloccate e tutti gli utenti verrà richiesto di autenticazione a più fattori ogni volta che necessarie. Questo criterio non blocca Exchange ActiveSync.

## <a name="identify-legacy-authentication-use"></a>Identificare l'utilizzo di autenticazione legacy

Prima è possibile bloccare l'autenticazione legacy nella directory, è necessario innanzi tutto capire se gli utenti hanno le app che usano l'autenticazione legacy e relativo impatto sulla directory globale. Azure AD i log di accesso utilizzabile per capire se si usa l'autenticazione legacy.

1. Passare il **portale di Azure** > **Azure Active Directory** > **accessi**.
1. Aggiungere la colonna di App Client se non viene visualizzato facendo clic su **colonne** > **App Client**.
1. Filtrare in base al **App Client** > **altri client** e fare clic su **applica**.

Applicazione di filtri di solo i tentativi di show avere effettuato l'accesso sono state apportate dai protocolli di autenticazione legacy. Facendo clic su ogni tentativo di accesso singoli visualizzerà i dettagli aggiuntivi. Il **App Client** campo sotto il **le informazioni di base** scheda indicherà quale protocollo di autenticazione legacy è stato utilizzato.

Questi log indica che gli utenti dipendono ancora da autenticazione legacy e le applicazioni che usano protocolli legacy per effettuare richieste di autenticazione. Per gli utenti che non vengono visualizzati in questi log e vengano confermati che non venga usata l'autenticazione legacy, implementare criteri di accesso condizionale o attivare i **criterio di base: autenticazione legacy di blocco** per solo tali utenti.

## <a name="moving-away-from-legacy-authentication"></a>Abbandono di autenticazione legacy

Dopo aver creato un'idea di chi usa l'autenticazione legacy nella directory e le applicazioni che dipendono da esso, il passaggio successivo è aggiornare gli utenti per l'uso dell'autenticazione moderna. L'autenticazione moderna è un metodo di gestione delle identità che offre più sicuro l'autenticazione e autorizzazione. Se si dispone di un criterio di autenticazione a più fattori posto nella directory, l'autenticazione moderna garantisce che l'utente viene richiesto per l'autenticazione a più fattori quando necessario. È l'alternativa più sicura di protocolli di autenticazione legacy.

Questa sezione viene fornita una panoramica dettagliata su come aggiornare l'ambiente per l'autenticazione moderna. Leggere i passaggi seguenti prima di abilitare un'autenticazione legacy criteri dell'organizzazione di blocco.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Passaggio 1: Abilitare l'autenticazione moderna nella directory

Il primo passaggio nell'abilitazione dell'autenticazione moderna è verificando che la directory supporta l'autenticazione moderna. L'autenticazione moderna è abilitata per impostazione predefinita per le directory creati in corrispondenza o dopo il 1 ° agosto 2017. Se è stata creata la directory prima di questa data, è necessario abilitare manualmente l'autenticazione moderna per la directory usando la procedura seguente:

1. Verificare se la directory supporta già l'autenticazione moderna eseguendo `Get-CsOAuthConfiguration` dal [Skype per Business Online PowerShell module](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Se il comando restituisce un oggetto vuoto `OAuthServers` proprietà, quindi l'autenticazione moderna è disabilitato. Aggiornare l'impostazione per abilitare l'autenticazione moderna con `Set-CsOAuthConfiguration`. Se il `OAuthServers` proprietà contiene una voce, ora possibile iniziare.

Assicurarsi di completare questo passaggio prima di procedere. È fondamentale che le configurazioni di directory devono essere modificate prima poiché essi indicano quale protocollo da utilizzare per tutti i client di Office. Anche se si usano i client di Office che supportano l'autenticazione moderna, per impostazione predefinita verranno usando protocolli legacy se l'autenticazione moderna è disabilitato sulla directory.

### <a name="step-2-office-applications"></a>Passaggio 2: Applicazioni di Office

Dopo aver abilitato l'autenticazione moderna nella directory, è possibile avviare l'aggiornamento delle applicazioni, consentendo l'autenticazione moderna per i client di Office. Office 2016 o versioni successive client supportano l'autenticazione moderna per impostazione predefinita. Non sono operazioni aggiuntive necessarie.

Se si usano client Windows di Office 2013 o versioni precedenti, è consigliabile eseguire l'aggiornamento a Office 2016 o versioni successive. Anche dopo aver completato il passaggio precedente dell'abilitazione dell'autenticazione moderna nella directory, le applicazioni Office meno recenti continueranno a usare i protocolli di autenticazione legacy. Se si usano client di Office 2013 e non è possibile eseguire immediatamente l'aggiornamento a Office 2016 o versione successiva, seguire i passaggi nell'articolo seguente per [abilitare l'autenticazione moderna di Office 2013 nei dispositivi Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Per proteggere l'account quando si usa l'autenticazione legacy, è consigliabile utilizzare password complesse tra le directory. Consulta [protezione tramite password di Azure AD](../authentication/concept-password-ban-bad.md) per vietare password vulnerabili tra la directory.

Office 2010 non supporta l'autenticazione moderna. È necessario aggiornare tutti gli utenti con Office 2010 a una versione più recente di Office. Si consiglia l'aggiornamento a Office 2016 o versioni successive, quando si blocca l'autenticazione legacy per impostazione predefinita.

Se si usa MacOS, è consigliabile eseguire l'aggiornamento a Office per Mac 2016 o versione successiva. Se si usa il client di posta elettronica nativo, è necessario disporre di MacOS versione 10.14 o in un secondo momento tutti i dispositivi.

### <a name="step-3-exchange-and-sharepoint"></a>Passaggio 3: Exchange e SharePoint

Per i client Outlook basata su Windows usare l'autenticazione moderna, Exchange Online deve essere abilitata anche l'autenticazione moderna. Se l'autenticazione moderna è disabilitata per Exchange Online, i client Outlook basata su Windows che supportano l'autenticazione moderna (Outlook 2013 o versione successiva) userà l'autenticazione di base per la connessione alle cassette postali di Exchange Online.

SharePoint Online è abilitato per impostazione predefinita l'autenticazione moderna. Per le directory create dopo il 1 ° agosto 2017, l'autenticazione moderna è abilitata per impostazione predefinita in Exchange Online. Tuttavia, se in precedenza si aveva disabilitata l'autenticazione moderna o si usa una directory creata prima di questa data, seguire i passaggi nell'articolo seguente per [abilitare l'autenticazione moderna in Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Passaggio 4: Skype for Business Online

Per evitare richieste di autenticazione legacy eseguite da Skype for Business, è necessario abilitare l'autenticazione moderna per Skype for Business Online. Per le directory create dopo il 1 ° agosto 2017, l'autenticazione moderna per Skype for Business è abilitato per impostazione predefinita.

Per abilitare l'autenticazione moderna in Skype for Business, è consigliabile eseguire la transizione a Microsoft Teams, che supporta l'autenticazione moderna per impostazione predefinita. Tuttavia, se non si riesce a tr in questo momento, sarà necessario abilitare l'autenticazione moderna per Skype for Business Online in modo che Skype per i client di Business inizia a usare l'autenticazione moderna. Seguire questi passaggi nell'articolo [Skype per topologie di Business supportate con l'autenticazione moderna](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported), per i passaggi abilitare l'autenticazione moderna per Skype for Business.

Oltre ad abilitare l'autenticazione moderna per Skype for Business Online, è consigliabile modern authentication essere abilitato per Exchange Online quando si abilita l'autenticazione moderna per Skype for Business. Questo processo consente di sincronizzare lo stato dell'autenticazione moderna in Exchange Online e Skype for Business online e impedirà più richieste di accesso per Skype per i client di Business.

### <a name="step-5-using-mobile-devices"></a>Passaggio 5: Uso di dispositivi mobili

Le applicazioni sul dispositivo mobile devono bloccare anche l'autenticazione legacy. È consigliabile usare Outlook per dispositivi mobili. Outlook per dispositivi mobili supporta l'autenticazione moderna per impostazione predefinita e soddisferanno altri criteri di protezione della linea di base di autenticazione a più fattori.

Per usare il client di posta elettronica native per iOS, è necessario essere in esecuzione iOS versione 11.0 e versioni successive per assicurarsi che il client di posta elettronica è stato aggiornato per bloccare l'autenticazione legacy.

### <a name="step-6-on-premises-clients"></a>Passaggio 6: Client locali

Se sei un cliente ibrida con Exchange Server locale e Skype per Business locale, entrambi i servizi dovrà essere aggiornata per abilitare l'autenticazione moderna. Quando si usa l'autenticazione moderna in un ambiente ibrido, si ha ancora l'autenticazione degli utenti in locale. La storia di autorizzare l'accesso alle modifiche delle risorse (file o messaggi di posta elettronica).

Prima di abilitare l'autenticazione moderna in locale, è possibile che siano soddisfatti theIf siano soddisfatti i requisiti, ora possibile abilitare l'autenticazione moderna in locale.

Passaggi per abilitare l'autenticazione moderna disponibili negli articoli seguenti:

* [Come configurare Exchange Server locale da usare l'autenticazione moderna ibrida](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Come usare l'autenticazione moderna (ADAL) con Skype for Business](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>Abilitare il criterio di base

I criteri **criterio di base: L'autenticazione legacy di blocco (anteprima)** è preconfigurata e verrà visualizzata nella parte superiore quando si passa al pannello di accesso condizionale nel portale di Azure.

Per abilitare questo criterio e proteggere l'organizzazione:

1. Accedi per il **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare a **Azure Active Directory** > **accesso condizionale**.
1. Nell'elenco dei criteri, selezionare **criterio di base: L'autenticazione legacy di blocco (anteprima)** .
1. Impostare **abilitare i criteri** al **Usa i criteri immediatamente**.
1. Fare clic su **salvare**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

* [Criteri di protezione della linea di base di accesso condizionale](concept-baseline-protection.md)
* [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/azure-ad-secure-steps.md)
* [Che cos'è l'accesso condizionale in Azure Active Directory?](overview.md)
