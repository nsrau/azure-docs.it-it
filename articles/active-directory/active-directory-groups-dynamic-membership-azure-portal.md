---

title: Appartenenza dinamica ai gruppi basata su attributi in Azure Active Directory | Microsoft Docs
description: Procedura per creare regole avanzate per l&quot;appartenenza dinamica ai gruppi, inclusi i parametri e gli operatori supportati per le regole delle espressioni.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 2f1d68c7127324477cfc8b87df0fd82dee7cd1d6
ms.contentlocale: it-it
ms.lasthandoff: 06/17/2017


---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Creare regole basate su attributi per l'appartenenza dinamica ai gruppi in Azure Active Directory
In Azure Active Directory (Azure AD) è possibile creare regole avanzate per abilitare complesse appartenenze dinamiche ai gruppi basate su attributi. Questo articolo descrive in dettaglio la sintassi e gli attributi per creare regole di appartenenza dinamica.

## <a name="to-create-the-advanced-rule"></a>Per creare la regola avanzata
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi premere **INVIO**.

   ![Apertura di Gestione utenti](./media/active-directory-groups-dynamic-membership-azure-portal/search-user-management.png)
3. Nel pannello **Utenti e gruppi** selezionare **Tutti i gruppi**.

   ![Apertura del pannello Gruppi](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)
4. Nel pannello **Utenti e gruppi - Tutti i gruppi** selezionare il comando **Aggiungi**.

   ![Aggiungere un nuovo gruppo](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)
5. Nel pannello **Gruppo** immettere un nome e una descrizione per il nuovo gruppo. Selezionare un **Tipo di appartenenza** di **Utente dinamico** o **Dispositivo dinamico**, a seconda che si intenda creare una regola per gli utenti o per i dispositivi e quindi selezionare **Aggiungi query dinamica**. Per gli attributi usati per le regole di dispositivo, vedere [Uso degli attributi per creare regole per gli oggetti dispositivo](#using-attributes-to-create-rules-for-device-objects).

   ![Aggiungere una regola di appartenenza dinamica](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)
6. Nel pannello **Regole di appartenenza dinamica** immettere la regola nella casella **Aggiungi regola avanzata per l'appartenenza dinamica**, premere INVIO e quindi selezionare **Crea** nella parte inferiore del pannello.
7. Selezionare **Crea** on the **Gruppo** per creare il gruppo.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Creazione del corpo di una regola avanzata
La regola avanzata che è possibile creare per le appartenenze dinamiche ai gruppi è essenzialmente un'espressione binaria composta da tre parti che genera un risultato di tipo true o false. Di seguito sono elencate le tre parti:

* Parametro sinistro
* Operatore binario
* Costante destra

Una regola avanzata completa ha un aspetto simile al seguente: (leftParameter binaryOperator "RightConstant"), dove le parentesi di apertura e chiusura sono facoltative per l'intera espressione binaria, le virgolette doppie sono anch'esse facoltative e sono obbligatorie solo per la costante a destra quando si tratta di una stringa e la sintassi del parametro a sinistra è user.property. Una regola avanzata può includere più espressioni binarie separate dagli operatori logici -and, -or e -not.

Di seguito sono riportati alcuni esempi di regola avanzata con il formato corretto:
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
Per l'elenco completo dei parametri supportati e degli operatori delle regole di espressione, vedere le sezioni riportate di seguito. Per gli attributi usati per le regole di dispositivo, vedere [Uso degli attributi per creare regole per gli oggetti dispositivo](#using-attributes-to-create-rules-for-device-objects).

La lunghezza totale del corpo della regola avanzata non può superare i 2048 caratteri.

> [!NOTE]
> Le operazioni di stringa ed espressione regolare non fanno distinzione tra maiuscole e minuscole. È inoltre possibile eseguire controlli Null usando $null come costante, ad esempio user.department -eq $null.
> Le stringhe contenenti virgolette (") devono essere precedute dal carattere di escape ', ad esempio user.department -eq \`"Sales".
>
>

## <a name="supported-expression-rule-operators"></a>Operatori delle regole di espressione supportati
Nella tabella seguente sono elencati tutti gli operatori delle regole di espressione supportati e la relativa sintassi da usare nel corpo della regola avanzata:

| Operatore | Sintassi |
| --- | --- |
| Non uguale a |-ne |
| Uguale a |-eq |
| Non inizia con |-notStartsWith |
| Inizia con |-startsWith |
| Non contiene |-notContains |
| Contiene |-contains |
| Non corrispondente |-notMatch |
| Corrispondente |-match |
| In | -in |
| Non incluso | -notIn |

## <a name="operator-precedence"></a>Precedenza degli operatori

Tutti gli operatori sono elencati di seguito in base alla precedenza, dal minore al maggiore. Gli operatori sulla stessa riga hanno la stessa precedenza. -any -all -or -and -not -eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn

Tutti gli operatori possono essere usati con o senza trattino come prefisso.

Si noti che le parentesi non sono sempre necessarie. Le parentesi devono essere aggiunte solo quando la precedenza non rispetta i requisiti specifici, ad esempio:
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
Equivale a:
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>Uso degli operatori -In e -notIn

Per confrontare il valore di un attributo utente con una serie di valori diversi è possibile usare gli operatori -In o -notIn. Di seguito è illustrato un esempio con l'operatore -In:
```
    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]
```
Si noti l'uso di "[" e "]" all'inizio e alla fine dell'elenco di valori. Questa condizione restituisce True se il valore di user.department è uguale a uno dei valori nell'elenco.


## <a name="query-error-remediation"></a>Correzione degli errori di query
Nella tabella seguente sono elencati errori potenziali e indica come correggerli se si verificano

| Errore di analisi della query | Uso errato | Uso corretto |
| --- | --- | --- |
| Errore: l'attributo non è supportato |(user.invalidProperty -eq "Valore") |(user.department -eq "valore")<br/>La proprietà deve corrispondere a una delle proprietà nell' [elenco di proprietà supportate](#supported-properties). |
| Errore: l'operatore non è supportato sull'attributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/>La proprietà è di tipo booleano. Usare gli operatori supportati (-eq o - ne) per il tipo boolean nell'elenco precedente. |
| Errore: si è verificato un errore di compilazione della query. |(user.department -eq "Vendite") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext" |(user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>L'operatore logico deve corrispondere a una delle proprietà supportate nell'elenco sopra.(user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")Error nell'espressione regolare. |
| Errore: l'espressione binaria non ha il formato corretto |(user.department –eq "Vendite") (user.department -eq "Vendite")(user.department-eq"Vendite") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>La query include più errori. La parentesi non si trova nella posizione corretta. |
| Errore: si è verificato un errore sconosciuto durante la configurazione delle appartenenze dinamiche. |(user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain" |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>La query include più errori. La parentesi non si trova nella posizione corretta. |

## <a name="supported-properties"></a>Proprietà supportate
Di seguito sono elencate tutte le proprietà utente che è possibile usare nelle regole avanzate:

### <a name="properties-of-type-boolean"></a>Proprietà di tipo boolean
Operatori consentiti

* -eq
* -ne

| Proprietà | Valori consentiti | Utilizzo |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Proprietà di tipo stringa
Operatori consentiti

* -eq
* -ne
* -notStartsWith
* -startsWith
* -contains
* -notContains
* -match
* -notMatch
* -in
* -notIn

| Proprietà | Valori consentiti | Utilizzo |
| --- | --- | --- |
| city |Qualsiasi valore stringa o $null |(user.city -eq "valore") |
| country |Qualsiasi valore stringa o $null |(user.country -eq "valore") |
| CompanyName | Qualsiasi valore stringa o $null | (user.CompanyName -eq "valore") |
| department |Qualsiasi valore stringa o $null |(user.department -eq "valore") |
| displayName |Qualsiasi valore stringa. |(user.displayName -eq "valore") |
| facsimileTelephoneNumber |Qualsiasi valore stringa o $null |(user.facsimileTelephoneNumber -eq "valore") |
| givenName |Qualsiasi valore stringa o $null |(user.givenName -eq "valore") |
| jobTitle |Qualsiasi valore stringa o $null |(user.jobTitle -eq "valore") |
| mail |Qualsiasi valore stringa o $null (indirizzo SMTP dell'utente) |(user.mail -eq "valore") |
| mailNickName |Qualsiasi valore stringa (alias di posta dell'utente) |(user.mailNickName -eq "valore") |
| mobile |Qualsiasi valore stringa o $null |(user.mobile -eq "valore") |
| objectId |GUID dell'oggetto utente |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | ID di sicurezza (SID) locale per gli utenti sincronizzati da un ambiente locale al cloud. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Nessuno DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Qualsiasi valore stringa o $null |(user.physicalDeliveryOfficeName -eq "valore") |
| postalCode |Qualsiasi valore stringa o $null |(user.postalCode -eq "valore") |
| preferredLanguage |Codice ISO 639-1 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Qualsiasi valore stringa o $null |(user.sipProxyAddress -eq "valore") |
| state |Qualsiasi valore stringa o $null |(user.state -eq "valore") |
| streetAddress |Qualsiasi valore stringa o $null |(user.streetAddress -eq "valore") |
| surname |Qualsiasi valore stringa o $null |(user.surname -eq "valore") |
| telephoneNumber |Qualsiasi valore stringa o $null |(user.telephoneNumber -eq "valore") |
| usageLocation |Codice di paese di due lettere |(user.usageLocation -eq "US") |
| userPrincipalName |Qualsiasi valore stringa. |(user.userPrincipalName -eq "alias@domain") |
| userType |membro guest $null |(user.userType -eq "Membro") |

### <a name="properties-of-type-string-collection"></a>Proprietà di tipo insieme String
Operatori consentiti

* -contains
* -notContains

| Proprietà | Valori consentiti | Utilizzo |
| --- | --- | --- |
| otherMails |Qualsiasi valore stringa. |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Proprietà multivalore
Operatori consentiti

* -any (soddisfatto quando almeno un elemento della raccolta corrisponde alla condizione)
* -all (soddisfatto quando tutti gli elementi della raccolta corrispondono alla condizione)

| Proprietà | Valori | Utilizzo |
| --- | --- | --- |
| assigendPlans |Ogni oggetto della raccolta espone le proprietà di stringa seguenti: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

Le proprietà multivalore sono raccolte di oggetti dello stesso tipo. È possibile usare gli operatori -any e -all per applicare una condizione rispettivamente a uno o a tutti gli elementi della raccolta. ad esempio:

assignedPlans è una proprietà multivalore che elenca tutti i piani di servizio assegnati all'utente. L'espressione riportata di seguito selezionerà gli utenti che hanno il piano di servizio Exchange Online (Piano 2) con lo stato abilitato:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

L'identificatore GUID identifica il piano di servizio Exchange Online (Piano 2).

> [!NOTE]
> Questa espressione è utile per identificare tutti gli utenti per i quali è stata abilitata una funzionalità di Office 365 o di un altro servizio online Microsoft. Ad esempio, per identificarli come destinatari di un determinato set di criteri.

L'espressione seguente selezionerà tutti gli utenti che hanno un piano di servizio qualsiasi associato al servizio Intune (identificato dal nome di servizio "SCO"):
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Uso dei valori Null

Per specificare un valore Null in una regola, è possibile usare "null" o $null. Esempio:
```
   user.mail –ne null
```
equivale a
```
   user.mail –ne $null
   ```

## <a name="extension-attributes-and-custom-attributes"></a>Attributi di estensione ed attributi personalizzati
Gli attributi di estensione e gli attributi personalizzati sono supportati nelle regole di appartenenza dinamica.

Gli attributi di estensione vengono sincronizzati dall'istanza locale di Window Server AD e hanno il formato "ExtensionAttributeX", dove X equivale a 1 - 15.
Ecco un esempio di regola che usa un attributo di estensione:
```
(user.extensionAttribute15 -eq "Marketing")
```
Gli attributi personalizzati vengono sincronizzati dall'istanza locale di Windows Server AD o da un'applicazione SaaS collegata e hanno il formato "user.extension_[GUID]\__[Attributo]", dove [GUID] è l'identificatore univoco in AAD per l'applicazione che ha creato l'attributo in AAD e [Attributo] è il nome dell'attributo creato.
Ecco un esempio di regola che usa un attributo personalizzato:
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
È possibile trovare il nome dell'attributo personalizzato nella directory eseguendo una query sull'attributo nell'utente con Esplora grafico e cercando il nome dell'attributo.

## <a name="direct-reports-rule"></a>Regola per i dipendenti diretti
È possibile creare un gruppo contenente tutti i dipendenti diretti di un manager. Quando i dipendenti diretti del manager cambieranno, l'appartenenza al gruppo verrà automaticamente modificata.

> [!NOTE]
> 1. Per il corretto funzionamento della regola, verificare che la proprietà **Manager ID** sia impostata correttamente sugli utenti inclusi nel tenant. È possibile controllare il valore corrente per un utente nella **scheda Profilo**.
> 2. Questa regola supporta solo i dipendenti **diretti**. Attualmente non è possibile creare un gruppo per una gerarchia nidificata, ad esempio un gruppo che include dipendenti diretti e i relativi dipendenti.

**Per configurare il gruppo**

1. Eseguire i passaggi da 1 a 5 illustrati nella sezione [Per creare la regola avanzata](#to-create-the-advanced-rule) e selezionare **Utente dinamico** come **Tipo di appartenenza**.
2. Nel pannello **Dynamic membership rules** (Regole di appartenenza dinamica) immettere la regola con la sintassi seguente:

    *Dipendenti diretti per "{obectID_of_manager}"*

    Esempio di una regola valida:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. Dopo aver salvato la regola, tutti gli utenti con il valore Manager ID specificato verranno aggiunti al gruppo.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Uso degli attributi per creare regole per gli oggetti dispositivo
È anche possibile creare una regola che consenta di selezionare gli oggetti dispositivo per l'appartenenza a un gruppo. È possibile usare gli attributi del dispositivo seguenti:

| Proprietà              | Valori consentiti                  | Utilizzo                                                       |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| accountEnabled          | true false                      | (device.accountEnabled -eq true)                            |
| displayName             | Qualsiasi valore stringa.                | (device.displayName - eq "Iphone di Rob")                       |
| deviceOSType            | Qualsiasi valore stringa.                | (device.deviceOSType -eq "IOS")                             |
| deviceOSVersion         | Qualsiasi valore stringa.                | (device.OSVersion -eq "9.1")                                |
| deviceCategory          | Qualsiasi valore stringa.                | (device.deviceCategory -eq "")                              |
| deviceManufacturer      | Qualsiasi valore stringa.                | (device.deviceManufacturer -eq "Microsoft")                 |
| deviceModel             | Qualsiasi valore stringa.                | (device.deviceModel -eq "IPhone 7+")                        |
| deviceOwnership         | Qualsiasi valore stringa.                | (device.deviceOwnership -eq "")                             |
| domainName              | Qualsiasi valore stringa.                | (device.domainName -eq "contoso.com")                       |
| enrollmentProfileName   | Qualsiasi valore stringa.                | (device.enrollmentProfileName -eq "")                       |
| isRooted                | true false                      | (device.deviceOSType -eq true)                              |
| managementType          | Qualsiasi valore stringa.                | (device.managementType -eq "")                              |
| organizationalUnit      | Qualsiasi valore stringa.                | (device.organizationalUnit -eq "")                          |
| deviceId                | un deviceId valido                | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d") |
| objectId                | un objectId AAD valido            | (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d") |




## <a name="next-steps"></a>Passaggi successivi
Questi articoli forniscono informazioni aggiuntive sui gruppi in Azure Active Directory.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)

