---
title: Criteri di base blocco autenticazione legacy (anteprima)-Azure Active Directory
description: Criteri di accesso condizionale per bloccare i protocolli di autenticazione legacy
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
ms.openlocfilehash: a313240685e539b613dee1c7ff8bd56bb24eb2ba
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227313"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>Criteri di base: Blocca autenticazione legacy (anteprima)

Per consentire agli utenti di accedere facilmente alle app cloud, Azure Active Directory (Azure AD) supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. L'autenticazione legacy è un termine che fa riferimento a una richiesta di autenticazione effettuata da:

* Client di Office meno recenti che non usano l'autenticazione moderna (ad esempio, Office 2010 client)
* Qualsiasi client che usa protocolli di posta legacy, ad esempio IMAP/SMTP/POP3

Attualmente, la maggior parte di tutti i tentativi di accesso compromessa provengono dall'autenticazione legacy. L'autenticazione legacy non supporta multi-factor authentication. Anche se nella directory sono abilitati criteri di autenticazione a più fattori, un attore malintenzionato può eseguire l'autenticazione usando un protocollo legacy e ignorare l'autenticazione a più fattori.

Il modo migliore per proteggere l'account dalle richieste di autenticazione dannose effettuate da protocolli legacy è quello di bloccare completamente questi tentativi. Per semplificare la protezione dell'ambiente, sono stati creati i criteri di base per bloccare l'autenticazione legacy.

**Blocca autenticazione legacy** è un [criterio di base](concept-baseline-protection.md) che blocca tutte le richieste di autenticazione effettuate da protocolli legacy. Per eseguire correttamente l'accesso a tutti gli utenti, è necessario usare l'autenticazione moderna. Usato in combinazione con gli altri criteri di base, tutte le richieste provenienti da protocolli legacy verranno bloccate e tutti gli utenti dovranno eseguire l'autenticazione a più fattori ogni volta che è necessario. Questo criterio non blocca Exchange ActiveSync.

## <a name="identify-legacy-authentication-use"></a>Identificare l'uso dell'autenticazione legacy

Prima di poter bloccare l'autenticazione legacy nella directory, è prima di tutto necessario comprendere se gli utenti hanno app che usano l'autenticazione legacy e come influiscono sulla directory complessiva. È possibile usare i log di accesso Azure AD per capire se si usa l'autenticazione legacy.

1. Passare al **portale di Azure** > **Azure Active Directory** > **accessi**.
1. Aggiungere la colonna app client se non viene visualizzata facendo clic su **colonne** > **client App**.
1. Filtrare in base all' **app** > client**altri client** e fare clic su **applica**.

Con il filtro vengono visualizzati solo i tentativi di accesso eseguiti dai protocolli di autenticazione legacy. Se si fa clic su ogni singolo tentativo di accesso, vengono visualizzati altri dettagli. Il campo **app client** nella scheda **informazioni di base** indicherà quale protocollo di autenticazione legacy è stato usato.

Questi log indicheranno quali utenti sono ancora a seconda dell'autenticazione legacy e quali applicazioni usano protocolli legacy per eseguire richieste di autenticazione. Per gli utenti che non sono presenti in questi registri e che non usano l'autenticazione legacy, implementare criteri di accesso condizionale o abilitare i **criteri di base: bloccare l'autenticazione legacy** solo per questi utenti.

## <a name="moving-away-from-legacy-authentication"></a>Allontanarsi dall'autenticazione legacy

Quando si ha un'idea migliore di chi usa l'autenticazione legacy nella directory e quali applicazioni dipendono da tale autenticazione, il passaggio successivo consiste nell'aggiornamento degli utenti per l'uso dell'autenticazione moderna. L'autenticazione moderna è un metodo di gestione delle identità che offre l'autenticazione e l'autorizzazione utente più sicure. Se si dispone di un criterio di autenticazione a più fattori nella directory, l'autenticazione moderna garantisce che all'utente venga richiesto di eseguire l'autenticazione a più fattori quando richiesto. Si tratta dell'alternativa più sicura ai protocolli di autenticazione legacy.

Questa sezione fornisce una panoramica dettagliata su come aggiornare l'ambiente all'autenticazione moderna. Leggere i passaggi seguenti prima di abilitare un criterio di blocco dell'autenticazione legacy nell'organizzazione.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Passaggio 1: Abilitare l'autenticazione moderna nella directory

Il primo passaggio nell'abilitazione dell'autenticazione moderna è garantire che la directory supporti l'autenticazione moderna. Per impostazione predefinita, l'autenticazione moderna è abilitata per le directory create il 1 ° agosto 2017. Se la directory è stata creata prima di questa data, è necessario abilitare manualmente l'autenticazione moderna per la directory attenendosi alla procedura seguente:

1. Verificare se la directory supporta già l'autenticazione moderna eseguendo `Get-CsOAuthConfiguration` dal modulo di [PowerShell per Skype for business online](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Se il comando restituisce una proprietà `OAuthServers` vuota, l'autenticazione moderna è disabilitata. Aggiornare l'impostazione per abilitare l'autenticazione moderna `Set-CsOAuthConfiguration`usando. Se la `OAuthServers` proprietà contiene una voce, è possibile iniziare.

Assicurarsi di completare questo passaggio prima di procedere. È fondamentale che le configurazioni di directory vengano modificate per prime perché stabiliscono quale protocollo verrà usato da tutti i client di Office. Anche se si usano i client di Office che supportano l'autenticazione moderna, per impostazione predefinita verranno usati i protocolli legacy se l'autenticazione moderna è disabilitata nella directory.

### <a name="step-2-office-applications"></a>Passaggio 2: Applicazioni di Office

Dopo aver abilitato l'autenticazione moderna nella directory, è possibile avviare l'aggiornamento delle applicazioni abilitando l'autenticazione moderna per i client di Office. Per impostazione predefinita, i client Office 2016 o versioni successive supportano l'autenticazione moderna. Non sono necessari passaggi aggiuntivi.

Se si usano client Windows Office 2013 o versioni precedenti, è consigliabile eseguire l'aggiornamento a Office 2016 o versione successiva. Anche dopo aver completato il passaggio precedente dell'abilitazione dell'autenticazione moderna nella directory, le applicazioni di Office precedenti continueranno a usare i protocolli di autenticazione legacy. Se si usano i client di Office 2013 e non è possibile eseguire immediatamente l'aggiornamento a Office 2016 o versione successiva, seguire la procedura illustrata nell'articolo seguente per [abilitare l'autenticazione moderna per Office 2013 nei dispositivi Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Per proteggere l'account mentre si usa l'autenticazione legacy, è consigliabile usare password complesse in tutta la directory. Controllare [Azure ad la protezione delle password](../authentication/concept-password-ban-bad.md) per vietare le password vulnerabili nella directory.

Office 2010 non supporta l'autenticazione moderna. Sarà necessario aggiornare gli utenti con Office 2010 a una versione più recente di Office. È consigliabile eseguire l'aggiornamento a Office 2016 o versione successiva, perché blocca l'autenticazione legacy per impostazione predefinita.

Se si usa MacOS, è consigliabile eseguire l'aggiornamento a Office per Mac 2016 o versione successiva. Se si usa il client di posta elettronica nativo, sarà necessario avere MacOS versione 10,14 o successiva su tutti i dispositivi.

### <a name="step-3-exchange-and-sharepoint"></a>Passaggio 3: Exchange e SharePoint

Per consentire ai client Outlook basati su Windows di usare l'autenticazione moderna, Exchange Online deve essere abilitato anche per l'autenticazione moderna. Se l'autenticazione moderna è disabilitata per Exchange Online, i client Outlook basati su Windows che supportano l'autenticazione moderna (Outlook 2013 o versione successiva) utilizzeranno l'autenticazione di base per connettersi alle cassette postali di Exchange Online.

SharePoint Online è abilitato per l'autenticazione moderna. Per le directory create dopo il 1 ° agosto 2017, l'autenticazione moderna è abilitata per impostazione predefinita in Exchange Online. Tuttavia, se in precedenza è stata disabilitata l'autenticazione moderna o si sta utilizzando una directory creata prima di questa data, attenersi alla procedura descritta nell'articolo seguente per [abilitare l'autenticazione moderna in Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Passaggio 4: Skype for Business Online

Per evitare richieste di autenticazione legacy effettuate da Skype for business, è necessario abilitare l'autenticazione moderna per Skype for business online. Per le directory create dopo il 1 ° agosto 2017, l'autenticazione moderna per Skype for business è abilitata per impostazione predefinita.

Si consiglia di eseguire la transizione a Microsoft teams, che supporta l'autenticazione moderna per impostazione predefinita. Tuttavia, se non è possibile eseguire la migrazione in questo momento, sarà necessario abilitare l'autenticazione moderna per Skype for business online in modo che i client Skype for business inizino a usare l'autenticazione moderna. Per abilitare l'autenticazione moderna per Skype for business, seguire la procedura illustrata in questo articolo [topologie Skype for business supportate con l'autenticazione moderna](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported).

Oltre ad abilitare l'autenticazione moderna per Skype for business online, è consigliabile abilitare l'autenticazione moderna per Exchange Online quando si Abilita l'autenticazione moderna per Skype for business. Questo processo consente di sincronizzare lo stato dell'autenticazione moderna in Exchange Online e Skype for business online e di impedire più richieste di accesso per i client Skype for business.

### <a name="step-5-using-mobile-devices"></a>Passaggio 5: Uso dei dispositivi mobili

Anche le applicazioni nel dispositivo mobile devono bloccare l'autenticazione legacy. Si consiglia di usare Outlook per dispositivi mobili. Outlook per dispositivi mobili supporta l'autenticazione moderna per impostazione predefinita e soddisferà altri criteri di protezione di base dell'autenticazione a più fattori.

Per poter usare il client di posta iOS nativo, è necessario eseguire iOS versione 11,0 o successiva per assicurarsi che il client di posta sia stato aggiornato per bloccare l'autenticazione legacy.

### <a name="step-6-on-premises-clients"></a>Passaggio 6: Client locali

Se si è un cliente ibrido che usa Exchange Server locale e Skype for business locale, sarà necessario aggiornare entrambi i servizi per consentire l'autenticazione moderna. Quando si usa l'autenticazione moderna in un ambiente ibrido, si stanno ancora eseguendo l'autenticazione degli utenti in locale. La storia dell'autorizzazione dell'accesso alle risorse (file o messaggi di posta elettronica) cambia.

Prima di iniziare ad abilitare l'autenticazione moderna in locale, verificare di aver soddisfatto i prerequisiti.
A questo punto si è pronti per abilitare l'autenticazione moderna in locale.

I passaggi per l'abilitazione dell'autenticazione moderna sono disponibili negli articoli seguenti:

* [Come configurare Exchange Server locale per l'uso dell'autenticazione ibrida moderna](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Come usare l'autenticazione moderna (ADAL) con Skype for business](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>Abilitare i criteri di base

Criteri di **base dei criteri: Blocca autenticazione legacy (anteprima)** è preconfigurata e viene visualizzata nella parte superiore quando si passa al pannello accesso condizionale in portale di Azure. Questa impostazione viene applicata solo dopo un accesso riuscito, in modo che gli utenti abbiano comunque la possibilità di provare a usare l'autenticazione legacy.

Per abilitare questo criterio e proteggere l'organizzazione:

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare a **Azure Active Directory** > **accesso condizionale**.
1. Nell'elenco dei criteri selezionare **criteri di base: Blocca l'autenticazione legacy (anteprima**).
1. Impostare **Abilita criterio** per **usare immediatamente i criteri**.
1. Fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

* [Criteri di protezione di base per l'accesso condizionale](concept-baseline-protection.md)
* [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/azure-ad-secure-steps.md)
* [Che cos'è l'accesso condizionale in Azure Active Directory?](overview.md)
