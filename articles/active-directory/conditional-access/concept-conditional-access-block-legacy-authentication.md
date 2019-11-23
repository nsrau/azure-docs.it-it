---
title: Blocco di protocolli di autenticazione legacy in Azure AD
description: Informazioni su come e perché le organizzazioni devono bloccare i protocolli di autenticazione legacy
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19b29181f023b49cca7159fbbcad4a4675744a96
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819731"
---
# <a name="blocking-legacy-authentication"></a>Blocco dell'autenticazione legacy
 
Per consentire agli utenti di accedere facilmente alle app cloud, Azure Active Directory (Azure AD) supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. L'autenticazione legacy è un termine che fa riferimento a una richiesta di autenticazione effettuata da:

- Client di Office meno recenti che non usano l'autenticazione moderna (ad esempio, Office 2010 client)
- Qualsiasi client che usa protocolli di posta legacy, ad esempio IMAP/SMTP/POP3

Attualmente, la maggior parte di tutti i tentativi di accesso compromessa provengono dall'autenticazione legacy. L'autenticazione legacy non supporta multi-factor authentication. Anche se nella directory sono abilitati criteri di autenticazione a più fattori, un attore malintenzionato può eseguire l'autenticazione usando un protocollo legacy e ignorare l'autenticazione a più fattori. Il modo migliore per proteggere l'account dalle richieste di autenticazione dannose effettuate da protocolli legacy è quello di bloccare completamente questi tentativi.

## <a name="identify-legacy-authentication-use"></a>Identificare l'uso dell'autenticazione legacy

Prima di poter bloccare l'autenticazione legacy nella directory, è prima di tutto necessario comprendere se gli utenti hanno app che usano l'autenticazione legacy e come influiscono sulla directory complessiva. È possibile usare i log di accesso Azure AD per capire se si usa l'autenticazione legacy.

1. Passare alla portale di Azure > Azure Active Directory accessi >.
1. Aggiungere la colonna app client se non viene visualizzata facendo clic su colonne > app client.
1. Filtrare in base all'app client > controllare tutte le altre opzioni client presentate e fare clic su Applica.
1. Filtrare in base allo stato > operazione riuscita e fare clic su Applica. 
1. Espandere l'intervallo di date, se necessario, usando il filtro per la data.

Il filtro mostrerà solo i tentativi di accesso riusciti eseguiti dai protocolli di autenticazione legacy selezionati. Se si fa clic su ogni singolo tentativo di accesso, vengono visualizzati altri dettagli. La colonna app client o il campo app client nella scheda informazioni di base dopo aver selezionato una singola riga di dati indicherà quale protocollo di autenticazione legacy è stato usato. Questi log indicheranno quali utenti sono ancora a seconda dell'autenticazione legacy e quali applicazioni usano protocolli legacy per eseguire richieste di autenticazione. Per gli utenti che non sono presenti in questi registri e che non usano l'autenticazione legacy, implementare criteri di accesso condizionale o abilitare i criteri di base: bloccare l'autenticazione legacy solo per questi utenti.

## <a name="moving-away-from-legacy-authentication"></a>Allontanarsi dall'autenticazione legacy 

Quando si ha un'idea migliore di chi usa l'autenticazione legacy nella directory e quali applicazioni dipendono da tale autenticazione, il passaggio successivo consiste nell'aggiornamento degli utenti per l'uso dell'autenticazione moderna. L'autenticazione moderna è un metodo di gestione delle identità che offre l'autenticazione e l'autorizzazione utente più sicure. Se si dispone di un criterio di autenticazione a più fattori nella directory, l'autenticazione moderna garantisce che all'utente venga richiesto di eseguire l'autenticazione a più fattori quando richiesto. Si tratta dell'alternativa più sicura ai protocolli di autenticazione legacy.

Questa sezione fornisce una panoramica dettagliata su come aggiornare l'ambiente all'autenticazione moderna. Leggere i passaggi seguenti prima di abilitare un criterio di blocco dell'autenticazione legacy nell'organizzazione.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Passaggio 1: abilitare l'autenticazione moderna nella directory

Il primo passaggio nell'abilitazione dell'autenticazione moderna è garantire che la directory supporti l'autenticazione moderna. Per impostazione predefinita, l'autenticazione moderna è abilitata per le directory create il 1 ° agosto 2017. Se la directory è stata creata prima di questa data, è necessario abilitare manualmente l'autenticazione moderna per la directory attenendosi alla procedura seguente:

1. Verificare se la directory supporta già l'autenticazione moderna eseguendo `Get-CsOAuthConfiguration` dal [modulo di PowerShell per Skype for business online](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Se il comando restituisce una proprietà `OAuthServers` vuota, l'autenticazione moderna è disabilitata. Aggiornare l'impostazione per abilitare l'autenticazione moderna usando `Set-CsOAuthConfiguration`. Se la proprietà `OAuthServers` contiene una voce, è possibile iniziare.

Assicurarsi di completare questo passaggio prima di procedere. È fondamentale che le configurazioni di directory vengano modificate per prime perché stabiliscono quale protocollo verrà usato da tutti i client di Office. Anche se si usano i client di Office che supportano l'autenticazione moderna, per impostazione predefinita verranno usati i protocolli legacy se l'autenticazione moderna è disabilitata nella directory.

### <a name="step-2-office-applications"></a>Passaggio 2: applicazioni di Office

Dopo aver abilitato l'autenticazione moderna nella directory, è possibile avviare l'aggiornamento delle applicazioni abilitando l'autenticazione moderna per i client di Office. Per impostazione predefinita, i client Office 2016 o versioni successive supportano l'autenticazione moderna. Non sono necessari passaggi aggiuntivi.

Se si usano client Windows Office 2013 o versioni precedenti, è consigliabile eseguire l'aggiornamento a Office 2016 o versione successiva. Anche dopo aver completato il passaggio precedente dell'abilitazione dell'autenticazione moderna nella directory, le applicazioni di Office precedenti continueranno a usare i protocolli di autenticazione legacy. Se si usano i client di Office 2013 e non è possibile eseguire immediatamente l'aggiornamento a Office 2016 o versione successiva, seguire la procedura illustrata nell'articolo seguente per [abilitare l'autenticazione moderna per Office 2013 nei dispositivi Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Per proteggere l'account mentre si usa l'autenticazione legacy, è consigliabile usare password complesse in tutta la directory. Consultare Azure AD di [protezione delle password](../authentication/concept-password-ban-bad.md) per vietare le password vulnerabili nella directory.

Office 2010 non supporta l'autenticazione moderna. Sarà necessario aggiornare gli utenti con Office 2010 a una versione più recente di Office. È consigliabile eseguire l'aggiornamento a Office 2016 o versione successiva, perché blocca l'autenticazione legacy per impostazione predefinita.

Se si usa MacOS, è consigliabile eseguire l'aggiornamento a Office per Mac 2016 o versione successiva. Se si usa il client di posta elettronica nativo, sarà necessario avere MacOS versione 10,14 o successiva su tutti i dispositivi.

### <a name="step-3-exchange-and-sharepoint"></a>Passaggio 3: Exchange e SharePoint

Per consentire ai client Outlook basati su Windows di usare l'autenticazione moderna, Exchange Online deve essere abilitato anche per l'autenticazione moderna. Se l'autenticazione moderna è disabilitata per Exchange Online, i client Outlook basati su Windows che supportano l'autenticazione moderna (Outlook 2013 o versione successiva) utilizzeranno l'autenticazione di base per connettersi alle cassette postali di Exchange Online.

SharePoint Online è abilitato per l'autenticazione moderna. Per le directory create dopo il 1 ° agosto 2017, l'autenticazione moderna è abilitata per impostazione predefinita in Exchange Online. Tuttavia, se in precedenza è stata disabilitata l'autenticazione moderna o si sta utilizzando una directory creata prima di questa data, attenersi alla procedura descritta nell'articolo seguente per [abilitare l'autenticazione moderna in Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Passaggio 4: Skype for business

Per evitare richieste di autenticazione legacy effettuate da Skype for business, è necessario abilitare l'autenticazione moderna per Skype for business online. Per le directory create dopo il 1 ° agosto 2017, l'autenticazione moderna per Skype for business è abilitata per impostazione predefinita.

Si consiglia di eseguire la transizione a Microsoft teams, che supporta l'autenticazione moderna per impostazione predefinita. Tuttavia, se non è possibile eseguire la migrazione in questo momento, sarà necessario abilitare l'autenticazione moderna per Skype for business online in modo che i client Skype for business inizino a usare l'autenticazione moderna. Per abilitare l'autenticazione moderna per Skype for business, seguire la procedura illustrata in questo articolo [topologie Skype for business supportate con l'autenticazione moderna](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported).

Oltre ad abilitare l'autenticazione moderna per Skype for business online, è consigliabile abilitare l'autenticazione moderna per Exchange Online quando si Abilita l'autenticazione moderna per Skype for business. Questo processo consente di sincronizzare lo stato dell'autenticazione moderna in Exchange Online e Skype for business online e di impedire più richieste di accesso per i client Skype for business.

### <a name="step-5-using-mobile-devices"></a>Passaggio 5: uso dei dispositivi mobili

Anche le applicazioni nel dispositivo mobile devono bloccare l'autenticazione legacy. Si consiglia di usare Outlook per dispositivi mobili. Outlook per dispositivi mobili supporta l'autenticazione moderna per impostazione predefinita e soddisferà altri criteri di protezione di base dell'autenticazione a più fattori.

Per poter usare il client di posta iOS nativo, è necessario eseguire iOS versione 11,0 o successiva per assicurarsi che il client di posta sia stato aggiornato per bloccare l'autenticazione legacy.

### <a name="step-6-on-premises-clients"></a>Passaggio 6: client locali

Se si è un cliente ibrido che usa Exchange Server locale e Skype for business locale, sarà necessario aggiornare entrambi i servizi per consentire l'autenticazione moderna. Quando si usa l'autenticazione moderna in un ambiente ibrido, si stanno ancora eseguendo l'autenticazione degli utenti in locale. La storia dell'autorizzazione dell'accesso alle risorse (file o messaggi di posta elettronica) cambia.

Prima di iniziare ad abilitare l'autenticazione moderna in locale, verificare di aver soddisfatto i prerequisiti. A questo punto si è pronti per abilitare l'autenticazione moderna in locale.

I passaggi per l'abilitazione dell'autenticazione moderna sono disponibili negli articoli seguenti:

* [Come configurare Exchange Server locale per l'uso dell'autenticazione ibrida moderna](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Come usare l'autenticazione moderna (ADAL) con Skype for business](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare Exchange Server locale per l'uso dell'autenticazione ibrida moderna](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [Come usare l'autenticazione moderna (ADAL) con Skype for business](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [Blocca autenticazione legacy](block-legacy-authentication.md)
