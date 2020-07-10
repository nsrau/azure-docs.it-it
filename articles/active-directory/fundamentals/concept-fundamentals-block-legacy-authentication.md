---
title: Bloccare i protocolli di autenticazione legacy in Azure AD
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
ms.custom: has-adal-ref
ms.openlocfilehash: 1e17421b27082a079d078f53c38d0c942db7ae71
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200543"
---
# <a name="blocking-legacy-authentication"></a>Bloccare l'autenticazione legacy
 
Per consentire agli utenti di accedere facilmente alle app cloud, Azure Active Directory (Azure AD) supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. Con il termine autenticazione legacy si fa riferimento a una richiesta di autenticazione effettuata da:

- Client Office precedenti che non usano l'autenticazione moderna (ad esempio, il client Office 2010)
- Qualsiasi client che usi protocolli di posta elettronica legacy come IMAP/SMTP/POP3

Oggi la maggior parte dei tentativi di accesso che compromettono la sicurezza derivano dall'autenticazione legacy. L'autenticazione legacy non supporta l'autenticazione a più fattori (MFA). Anche se nella directory è abilitato il criterio MFA, un attore malintenzionato può eseguire l'autenticazione usando un protocollo legacy e ignorando l'autenticazione a più fattori. Il miglior modo per proteggere l'account dalle richieste di autenticazione dannose effettuate dai protocolli legacy è bloccare completamente tali tentativi.

## <a name="identify-legacy-authentication-use"></a>Identificare l'uso dell'autenticazione legacy

Prima di poter bloccare l'autenticazione legacy nella directory, è necessario anzitutto capire se gli utenti usano app che sfruttano l'autenticazione legacy e il modo in cui questa influisce sulla directory complessiva. Per capire se viene usata l'autenticazione legacy è possibile usare i log di accesso di Azure AD.

1. Passare al **portale di Azure** > **Azure Active Directory** > **Accessi**.
1. Se non viene visualizzata, aggiungere la colonna **App client** facendo clic su  **Colonne** > **App client**.
1. Filtrare in base all' **app Client** > controllare tutte le opzioni dei **client di autenticazione legacy** presentate.
1. Filtrare per **Stato** > **Riuscito**. 
1. Se necessario, espandere l'intervallo di date con il filtro **Data**.

Il filtro mostrerà solo i tentativi di accesso riusciti eseguiti dai protocolli di autenticazione legacy selezionati. Facendo clic su ogni singolo tentativo di accesso vengono visualizzati altri dettagli. Dopo aver selezionato una singola riga di dati, la colonna App client o il campo App client nella scheda Informazioni di base indicano quale protocollo di autenticazione legacy è stato usato. Questi log indicano quali utenti ancora dipendono dall'autenticazione legacy e quali applicazioni usano protocolli legacy per eseguire le richieste di autenticazione. Per gli utenti che non figurano nei log e per i quali si ha la conferma che non usano l'autenticazione legacy, implementare un criterio di accesso condizionale o abilitare il criterio di base: bloccare l'autenticazione legacy solo per questi utenti.

## <a name="moving-away-from-legacy-authentication"></a>Abbandonare l'autenticazione legacy 

Una volta chiarito chi usa l'autenticazione legacy nella directory e quali applicazioni dipendono dalla stessa, il passaggio successivo consiste nell'aggiornare gli utenti per l'uso dell'autenticazione moderna. L'autenticazione moderna è un metodo di gestione dell'identità che offre un servizio di autenticazione e autorizzazione utente più sicuro. Se nella directory è stato configurato un criterio di autenticazione a più fattori, l'autenticazione moderna garantisce che all'utente venga richiesta l'autenticazione a più fattori quando necessario. Si tratta di un'alternativa più sicura rispetto ai protocolli di autenticazione legacy.

Questa sezione fornisce una panoramica dettagliata su come aggiornare l'ambiente per l'uso dell'autenticazione moderna. Prima di abilitare un criterio di blocco dell'autenticazione legacy nell'organizzazione, leggere i passaggi seguenti.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Passaggio 1: abilitare l'autenticazione moderna nella directory

Il primo passaggio per abilitare l'autenticazione moderna è verificare che questa sia supportata dalla directory. L'autenticazione moderna è abilitata per impostazione predefinita per le directory create successivamente al 1 agosto 2017 compreso. Se la directory è stata creata in una data precedente a quella indicata, sarà necessario abilitare l'autenticazione moderna manualmente seguendo i passaggi seguenti:

1. Verificare che la directory supporti già l'autenticazione moderna eseguendo `Get-CsOAuthConfiguration` dal modulo [PowerShell di Skype for Business Online](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Se il comando restituisce una proprietà `OAuthServers`  vuota, l'autenticazione moderna è disabilitata. Aggiornare l'impostazione per abilitare l'autenticazione moderna usando `Set-CsOAuthConfiguration`. Se la proprietà `OAuthServers`  contiene una voce, l'operazione è terminata.

Assicurarsi di completare questo passaggio prima di procedere. È fondamentale che le configurazioni della directory vengano modificate per prime in quanto stabiliscono il protocollo che verrà usato da tutti i client Office. Anche se si usano client Office che supportano l'autenticazione moderna, se nella directory questa è disabilitata verranno usati i protocolli legacy per impostazione predefinita.

### <a name="step-2-office-applications"></a>Passaggio 2: applicazioni Office

Dopo aver abilitato l'autenticazione moderna nella directory, è possibile avviare l'aggiornamento delle applicazioni abilitando l'autenticazione moderna per i client Office. I client Office 2016 o successivi supportano l'autenticazione moderna per impostazione predefinita. Non sono necessari altri passaggi.

Se si usano client Windows Office 2013 o precedenti, è consigliabile eseguire l'aggiornamento a Office 2016 o versione successiva. Anche dopo aver completato il passaggio precedente dell'abilitazione dell'autenticazione moderna nella directory, le applicazioni Office precedenti continueranno a usare i protocolli di autenticazione legacy. Se si usano i client Office 2013 e non è possibile eseguire immediatamente l'aggiornamento a Office 2016 o versione successiva, seguire i passaggi illustrati nell'articolo seguente per [abilitare l'autenticazione moderna per Office 2013 su dispositivi Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Come altra misura di protezione dell'account mentre si usa l'autenticazione legacy, è consigliabile usare password complesse in tutta la directory. Consultare [protezione delle password di Azure AD](../authentication/concept-password-ban-bad.md) per impedire l'uso di password vulnerabili nella directory.

Office 2010 non supporta l'autenticazione moderna. È necessario aggiornare eventuali utenti con Office 2010 a una versione più recente. È consigliabile eseguire l'aggiornamento a Office 2016 o versioni successive poiché bloccano l'autenticazione legacy per impostazione predefinita.

Se si usa macOS, è consigliabile eseguire l'aggiornamento a Office per Mac 2016 o versione successiva. Se si usa il client di posta elettronica nativo, sarà necessario disporre di macOS versione 10.14 o successiva su tutti i dispositivi.

### <a name="step-3-exchange-and-sharepoint"></a>Passaggio 3: Exchange e SharePoint

Per consentire ai client Outlook basati su Windows di usare l'autenticazione moderna, anche Exchange Online deve essere abilitato per l'autenticazione moderna. Se l'autenticazione moderna è disabilitata per Exchange Online, i client Outlook basati su Windows che supportano l'autenticazione moderna (Outlook 2013 o versione successiva) useranno l'autenticazione di base per connettersi alle cassette postali di Exchange Online.

In SharePoint Online l'autenticazione moderna è abilitata per impostazione predefinita. In Exchange Online l'autenticazione moderna è abilitata per impostazione predefinita per le directory create successivamente al 1 agosto 2017 compreso. Tuttavia, se in precedenza l'autenticazione moderna è stata disabilitata o si sta usando una directory creata prima della data indicata, seguire i passaggi descritti nell'articolo seguente per [abilitare l'autenticazione moderna in Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Passaggio 4: Skype for Business Online

Per evitare richieste di autenticazione legacy effettuate da Skype for Business, è necessario abilitare l'autenticazione moderna per Skype for Business Online. In Skype for Business l'autenticazione moderna è abilitata per impostazione predefinita per le directory create successivamente al 1 agosto 2017 compreso.

Si consiglia di eseguire la transizione a Microsoft Teams, che supporta l'autenticazione moderna per impostazione predefinita. Tuttavia, se non è possibile eseguire la migrazione in questo momento, sarà necessario abilitare l'autenticazione moderna per Skype for Business Online in modo che i relativi client inizino a usare l'autenticazione moderna. Per abilitare l'autenticazione moderna per Skype for Business, seguire la procedura descritta in questo articolo: [Topologie di Skype for Business supportate con l'autenticazione moderna](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported).

Oltre ad abilitare l'autenticazione moderna per Skype for Business Online, è consigliabile eseguire la stessa operazione anche per Exchange Online quando la si abilita per Skype for Business. Questo processo consente di sincronizzare lo stato dell'autenticazione moderna in Exchange Online e Skype for Business Online e di impedire più richieste di accesso per i client Skype for Business.

### <a name="step-5-using-mobile-devices"></a>Passaggio 5: usare i dispositivi mobili

Anche le applicazioni nei dispositivi mobili devono bloccare l'autenticazione legacy. Si consiglia di usare Outlook per dispositivi mobili. Outlook per dispositivi mobili supporta l'autenticazione moderna per impostazione predefinita e soddisfa altri criteri di protezione di base dell'autenticazione a più fattori.

Per poter usare il client di posta iOS nativo è necessario eseguire iOS versione 11.0 o successiva per assicurarsi che il client di posta sia stato aggiornato per bloccare l'autenticazione legacy.

### <a name="step-6-on-premises-clients"></a>Passaggio 6: client locali

Se si è un cliente ibrido che usa Exchange Server e Skype for Business in locale, sarà necessario aggiornare entrambi i servizi per abilitare l'autenticazione moderna. Quando si usa l'autenticazione moderna in un ambiente ibrido, l'autenticazione degli utenti ancora avviene a livello locale. La storia dell'autorizzazione dell'accesso alle relative risorse (file o messaggi di posta elettronica) viene modificata.

Prima di poter abilitare l'autenticazione moderna in locale, verificare di soddisfare i prerequisiti. A questo punto è possibile abilitare l'autenticazione moderna in locale.

I passaggi per l'abilitazione dell'autenticazione moderna sono disponibili negli articoli seguenti:

* [Come configurare Exchange Server locale per l'uso dell'autenticazione moderna ibrida](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Come usare l'autenticazione moderna (ADAL) con Skype for Business](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare Exchange Server locale per l'uso dell'autenticazione moderna ibrida](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [Come usare l'autenticazione moderna (ADAL) con Skype for Business](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [Bloccare l'autenticazione legacy](../conditional-access/block-legacy-authentication.md)
