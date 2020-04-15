---
title: Blocco dei protocolli di autenticazione legacy in Azure ADBlocking legacy authentication protocols in Azure AD
description: Informazioni su come e perché le organizzazioni devono bloccare i protocolli di autenticazione legacy
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55ce0233fdefb8360376e94c0baafabe4c62ced7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309201"
---
# <a name="blocking-legacy-authentication"></a>Blocco dell'autenticazione legacy
 
Per consentire agli utenti di accedere facilmente alle app cloud, Azure Active Directory (Azure AD) supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. L'autenticazione legacy è un termine che fa riferimento a una richiesta di autenticazione effettuata da:Legacy authentication is a term that refers to an authentication request made by:

- Client di Office meno recenti che non utilizzano l'autenticazione moderna (ad esempio, client di Office 2010)
- Qualsiasi client che utilizza protocolli di posta legacy, ad esempio IMAP/SMTP/POP3

Oggi, la maggior parte di tutti i tentativi di accesso compromettenti proviene dall'autenticazione legacy. L'autenticazione legacy non supporta l'autenticazione a più fattori (MFA). Anche se si dispone di un criterio di autenticazione a più fattori abilitato nella directory, un attore non valido può eseguire l'autenticazione utilizzando un protocollo legacy e ignorare l'autenticazione a più fattori. Il modo migliore per proteggere il tuo account da richieste di autenticazione dannose effettuate da protocolli legacy è bloccare completamente questi tentativi.

## <a name="identify-legacy-authentication-use"></a>Identificare l'utilizzo dell'autenticazione legacy

Prima di poter bloccare l'autenticazione legacy nella directory, devi prima capire se gli utenti dispongono di app che usano l'autenticazione legacy e come influisce sulla directory complessiva. I log di accesso di Azure AD possono essere usati per capire se si usa l'autenticazione legacy.

1. Passare al **portale** >di Azure Ad Azure **Access-ins** **Azure Active Directory** >di Azure .
1. Aggiungere la colonna **App client** se non viene visualizzata facendo clic su **Colonne** > **Client App**.
1. Filtra **per app**   client> controllare tutte le opzioni dei client di **autenticazione legacy** presentate.
1. Filtra per **Stato** > **riuscito**. 
1. Se necessario, espandi l'intervallo di date utilizzando il filtro **Data.**

Il filtro mostrerà solo i tentativi di accesso riusciti effettuati dai protocolli di autenticazione legacy selezionati. Cliccando su ogni singolo tentativo di accesso ti mostrerà ulteriori dettagli. La colonna App client o il campo App client nella scheda Informazioni di base dopo aver selezionato una singola riga di dati indicherà quale protocollo di autenticazione legacy è stato utilizzato. Questi registri indicheranno quali utenti dipendono ancora dall'autenticazione legacy e quali applicazioni utilizzano protocolli legacy per effettuare richieste di autenticazione. Per gli utenti che non vengono visualizzati in questi log e vengono confermati di non utilizzare l'autenticazione legacy, implementare un criterio di accesso condizionale o abilitare il criterio di base: bloccare l'autenticazione legacy solo per questi utenti.

## <a name="moving-away-from-legacy-authentication"></a>Allontanarsi dall'autenticazione legacy 

Dopo aver idea toltuato meglio chi utilizza l'autenticazione legacy nella directory e quali applicazioni dipendono da essa, il passaggio successivo consiste nell'aggiornare gli utenti all'utilizzo dell'autenticazione moderna. L'autenticazione moderna è un metodo di gestione delle identità che offre l'autenticazione e l'autorizzazione degli utenti più sicure. Se nella directory è presente un criterio di autenticazione a più fattori, l'autenticazione moderna garantisce che all'utente venga richiesta l'autenticazione a più fattori quando necessario. È l'alternativa più sicura ai protocolli di autenticazione legacy.

In questa sezione viene fornita una panoramica dettagliata su come aggiornare l'ambiente all'autenticazione moderna. Leggere i passaggi seguenti prima di abilitare un criterio di blocco dell'autenticazione legacy nell'organizzazione.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Passaggio 1: Abilitare l'autenticazione moderna nella directoryStep 1: Enable modern authentication in your directory

Il primo passaggio per abilitare l'autenticazione moderna consiste nell'assicurarsi che la directory supporti l'autenticazione moderna. L'autenticazione moderna è abilitata per impostazione predefinita per le directory create il 1 agosto 2017 o dopo tale data. Se la directory è stata creata prima di questa data, sarà necessario abilitare manualmente l'autenticazione moderna per la directory attenendosi alla seguente procedura:

1. Verificare se la directory supporta già `Get-CsOAuthConfiguration` l'autenticazione moderna eseguendo dal [modulo PowerShell di Skype for Business Online.](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)
1. Se il comando `OAuthServers` restituisce una proprietà vuota, l'autenticazione moderna è disabilitata. Aggiornare l'impostazione `Set-CsOAuthConfiguration`per abilitare l'autenticazione moderna utilizzando . Se `OAuthServers` la vostra proprietà contiene una voce, siete a posto.

Assicurarsi di completare questo passaggio prima di procedere. È fondamentale che le configurazioni di directory vengano modificate prima perché determinano il protocollo che verrà utilizzato da tutti i client di Office. Anche se si utilizzano client di Office che supportano l'autenticazione moderna, per impostazione predefinita utilizzeranno protocolli legacy se l'autenticazione moderna è disabilitata nella directory.

### <a name="step-2-office-applications"></a>Passaggio 2: Applicazioni di Office

Dopo aver abilitato l'autenticazione moderna nella directory, è possibile iniziare ad aggiornare le applicazioni abilitando l'autenticazione moderna per i client di Office. I client di Office 2016 o versioni successive supportano l'autenticazione moderna per impostazione predefinita. Non sono necessari passaggi aggiuntivi.

Se si usano client Windows di Office 2013 o versioni precedenti, è consigliabile eseguire l'aggiornamento a Office 2016 o versione successiva. Anche dopo aver completato il passaggio precedente per abilitare l'autenticazione moderna nella directory, le applicazioni di Office precedenti continueranno a utilizzare protocolli di autenticazione legacy. Se si utilizzano client di Office 2013 e non è possibile eseguire immediatamente l'aggiornamento a Office 2016 o versione successiva, seguire la procedura descritta nell'articolo seguente in [Abilitare l'autenticazione moderna per Office 2013 nei dispositivi Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Per proteggere il tuo account mentre usi l'autenticazione legacy, ti consigliamo di utilizzare password complesse nella directory. Controllare [la protezione](../authentication/concept-password-ban-bad.md) con password di Azure AD per escludere le password deboli nella directory.

Office 2010 non supporta l'autenticazione moderna. Sarà necessario aggiornare tutti gli utenti con Office 2010 a una versione più recente di Office. È consigliabile eseguire l'aggiornamento a Office 2016 o versione successiva, in quanto blocca l'autenticazione legacy per impostazione predefinita.

Se si usa macOS, è consigliabile eseguire l'aggiornamento a Office per Mac 2016 o versione successiva. Se si utilizza il client di posta nativo, è necessario disporre di macOS versione 10.14 o successiva su tutti i dispositivi.

### <a name="step-3-exchange-and-sharepoint"></a>Passaggio 3: Exchange e SharePoint

Affinché i client Outlook basati su Windows utilizzino l'autenticazione moderna, exchange Online deve essere abilitata anche per l'autenticazione moderna. Se l'autenticazione moderna è disabilitata per Exchange Online, i client Outlook basati su Windows che supportano l'autenticazione moderna (Outlook 2013 o versione successiva) utilizzeranno l'autenticazione di base per connettersi alle cassette postali di Exchange Online.

SharePoint Online è abilitato per l'autenticazione predefinita moderna. Per le directory create dopo il 1 agosto 2017, l'autenticazione moderna è abilitata per impostazione predefinita in Exchange Online. Tuttavia, se in precedenza è stata disabilitata l'autenticazione moderna o si utilizza una directory creata prima di questa data, attenersi alla procedura descritta nel seguente articolo [per abilitare l'autenticazione moderna in Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Passaggio 4: Skype for Business

Per impedire le richieste di autenticazione legacy effettuate da Skype for Business, è necessario abilitare l'autenticazione moderna per Skype for Business online. Per le directory create dopo il 1 agosto 2017, l'autenticazione moderna per Skype for Business è abilitata per impostazione predefinita.

Ti consigliamo di passare a Microsoft Teams, che supporta l'autenticazione moderna per impostazione predefinita. Tuttavia, se non è possibile eseguire la migrazione in questo momento, è necessario abilitare l'autenticazione moderna per Skype per Business Online in modo che Skype per i client aziendali iniziare a utilizzare l'autenticazione moderna. Seguire i passaggi descritti in questo articolo [Topologie di Skype for Business supportate con l'autenticazione moderna](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)per abilitare l'autenticazione moderna per Skype for Business.

Oltre ad abilitare l'autenticazione moderna per Skype for Business online, è consigliabile abilitare l'autenticazione moderna per Exchange Online quando si abilita l'autenticazione moderna per Skype for Business. Questo processo consente di sincronizzare lo stato dell'autenticazione moderna in Exchange Online e Skype for Business online e impedirà più richieste di accesso per i client Skype for Business.

### <a name="step-5-using-mobile-devices"></a>Passaggio 5: Utilizzo dei dispositivi mobiliStep 5: Using mobile devices

Anche le applicazioni sul tuo dispositivo mobile devono bloccare l'autenticazione legacy. Si consiglia di utilizzare Outlook per dispositivi mobili. Outlook per dispositivi mobili supporta l'autenticazione moderna per impostazione predefinita e soddisfa altri criteri di protezione di base MFA.

Per utilizzare il client di posta iOS nativo, è necessario eseguire iOS versione 11.0 o successiva per assicurarsi che il client di posta sia stato aggiornato per bloccare l'autenticazione legacy.

### <a name="step-6-on-premises-clients"></a>Passaggio 6: Client localiStep 6: On-premises clients

Se si è un cliente ibrido che utilizza Exchange Server locale e Skype for Business locale, entrambi i servizi dovranno essere aggiornati per abilitare l'autenticazione moderna. Quando si usa l'autenticazione moderna in un ambiente ibrido, gli utenti vengono comunque autenticati in locale. La storia di autorizzare il loro accesso alle risorse (file o e-mail) cambia.

Prima di iniziare ad abilitare l'autenticazione moderna in locale, assicurarsi di aver soddisfatto i prerequisiti. È ora possibile abilitare l'autenticazione moderna in locale.

I passaggi per abilitare l'autenticazione moderna sono disponibili negli articoli seguenti:Steps for enabling modern authentication can be found in the following articles:

* [Come configurare Exchange Server locale per l'utilizzo dell'autenticazione moderna ibrida](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Come utilizzare l'autenticazione moderna (ADAL) con Skype per Business](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare Exchange Server locale per l'utilizzo dell'autenticazione moderna ibrida](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [Come utilizzare l'autenticazione moderna (ADAL) con Skype per Business](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [Bloccare l'autenticazione legacy](../conditional-access/block-legacy-authentication.md)
