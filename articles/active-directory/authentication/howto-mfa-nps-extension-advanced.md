---
title: Configurare l'estensione NPS MFA di Azure - Azure Active Directory
description: Dopo aver installato l'estensione NPS, usare questi passaggi per la configurazione avanzata, ad esempio per inserire gli indirizzi IP nell'elenco elementi consentiti e sostituire i nomi dell'entità utente.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ac0497b13dad6795e8dc7ffaf761fe887a9953
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988634"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Opzioni di configurazione avanzate per l'estensione NPS per Multi-Factor Authentication

L'estensione Server dei criteri di rete o NPS (Network Policy Server) estende le funzionalità di Azure Multi-Factor Authentication basate sul cloud all'infrastruttura locale. Questo articolo presuppone che l'estensione sia già installata e che sia necessario sapere in che modo personalizzare l'estensione in base alle proprie esigenze. 

## <a name="alternate-login-id"></a>ID di accesso alternativo

Poiché l'estensione NPS si connette sia alle directory locali sia a quelle nel cloud, è possibile riscontrare un problema se i nomi dell'entità utente (UPN) locali non corrispondono ai nomi nel cloud. Per risolvere questo problema, usare ID di accesso alternativi. 

All'interno dell'estensione NPS è possibile definire un attributo di Active Directory da usare al posto del nome UPN per Azure Multi-Factor Authentication. Ciò consente di proteggere le risorse locali con la verifica in due passaggi senza modificare i nomi UPN locali. 

Per configurare gli ID di accesso alternativi, passare a `HKLM\SOFTWARE\Microsoft\AzureMfa` e modificare i seguenti valori del Registro di sistema:

| Name | Type | Valore predefinito | Descrizione |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | string | Empty | Specificare il nome dell'attributo di Active Directory che si vuole usare al posto dell'UPN. Questo attributo viene usato come attributo AlternateLoginId. Se questo valore del Registro di sistema è impostato su un [attributo di Active Directory valido](https://msdn.microsoft.com/library/ms675090.aspx) (ad esempio, mail o displayName), il valore dell'attributo viene utilizzato al posto dell'UPN dell'utente per l'autenticazione. Se questo valore del Registro di sistema è vuoto o non configurato, AlternateLoginId è disabilitato e l'UPN dell'utente viene usato per l'autenticazione. |
| LDAP_FORCE_GLOBAL_CATALOG | boolean | False | Usare questo flag per forzare l'uso del catalogo globale per le ricerche LDAP quando si effettua la ricerca di AlternateLoginId. Configurare un controller di dominio come catalogo globale, aggiungere l'attributo AlternateLoginId al catalogo globale e quindi abilitare questo flag. <br><br> Se LDAP_LOOKUP_FORESTSè configurato (non vuoto), **questo flag viene applicato come true**, indipendentemente dal valore del Registro di sistema. In questo caso, l'estensione NPS richiede che il catalogo globale sia configurato con l'attributo AlternateLoginId per ogni foresta. |
| LDAP_LOOKUP_FORESTS | string | Empty | Specificare un elenco separato da punti e virgola delle foreste in cui eseguire la ricerca. Ad esempio, *contoso.com;foobar.com*. Se questo valore del Registro di sistema è configurato, l'estensione NPS esegue una ricerca in modo iterativo in tutte le foreste nell'ordine in cui sono elencate e restituisce il primo valore AlternateLoginId corretto. Se questo valore del Registro di sistema non è configurato, la ricerca di AlternateLoginId è limitata al dominio corrente.|

Per risolvere i problemi con gli ID di accesso alternativi, usare le procedure consigliate per gli [errori degli ID di accesso alternativi](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Eccezioni IP

Se è necessario monitorare la disponibilità del server, ad esempio se i servizi di bilanciamento del carico verificano quali server sono in esecuzione prima di inviare i carichi di lavoro, è opportuno che questi controlli non vengano bloccati da richieste di verifica. Creare quindi un elenco di indirizzi IP sicuramente usati dagli account del servizio e disattivare i requisiti di Multi-Factor Authentication per tale elenco.

Per configurare un indirizzo IP elenco consentito, passare a `HKLM\SOFTWARE\Microsoft\AzureMfa` e configurare il valore del Registro di sistema seguente:

| NOME | Type | Valore predefinito | Descrizione |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | string | Empty | Specificare un elenco separato da punti e virgola degli indirizzi IP. Includere gli indirizzi IP dei computer in cui hanno origine le richieste di servizio, ad esempio il server NAS/VPN. Intervalli di indirizzi IP e subnet non sono supportate. <br><br> Ad esempio: *10.0.0.1;10.0.0.2;10.0.0.3*.

Quando arriva una richiesta da un indirizzo IP presente nel `IP_WHITELIST`, verifica in due passaggi viene ignorata. L'elenco di IP viene confrontato con l'indirizzo IP fornito nel *ratNASIPAddress* attributo della richiesta RADIUS. Se arriva una richiesta RADIUS senza l'attributo ratNASIPAddress, viene registrato un avviso P_WHITE_LIST_WARNING che indica che l'indirizzo IP viene ignorato poiché l'IP di origine non è presente nella richiesta RADIUS nell'attributo NasIpAddress.

## <a name="next-steps"></a>Passaggi successivi

[Risolvere i messaggi di errore dall'estensione del Server dei criteri di rete per Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
