---
title: Compilare i gruppi in modo dinamico in base agli attributi degli oggetti in Azure Active Directory | Microsoft Docs
description: Procedura per creare regole avanzate per l'appartenenza ai gruppi, inclusi i parametri e gli operatori supportati per le regole delle espressioni.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 04813a42-d40a-48d6-ae96-15b7e5025884
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal
ms.translationtype: HT
ms.sourcegitcommit: f2ac16c2f514aaa7e3f90fdf0d0b6d2912ef8485
ms.openlocfilehash: b136d3841243ad7aa88786f76b2d31e5dfae9079
ms.contentlocale: it-it
ms.lasthandoff: 09/08/2017

---

# <a name="populate-groups-dynamically-based-on-object-attributes"></a>Compilare i gruppi in modo dinamico in base agli attributi degli oggetti
Il portale di Azure classico offre la possibilità di consentire appartenenze dinamiche basate su attributi più complesse ai gruppi di Azure Active Directory, ovvero Azure AD.  

Quando gli attributi di un utente o un dispositivo cambiano, il sistema valuta tutte le regole dinamiche del gruppo in una directory per verificare se la modifica attiverà aggiunte o rimozioni nel gruppo. Se un utente o un dispositivo soddisfa una regola in un gruppo, viene aggiunto come membro a tale gruppo. Se non soddisfano la regola, vengono rimossi.

> [!NOTE]
> - È possibile configurare una regola per l'appartenenza dinamica nei gruppi di sicurezza o nei gruppi di Office 365.
>
> - Questa funzionalità richiede una licenza Azure AD Premium P1 per ogni utente membro per almeno un gruppo dinamico.
>
> - Sebbene sia possibile creare un gruppo dinamico per i dispositivi o gli utenti, non è possibile creare una regola che contenga sia oggetti utente che dispositivo.

> - Al momento non è possibile creare un gruppo di dispositivi in base agli attributi dell'utente proprietario. Le regole di appartenenza dispositivo possono fare riferimento solo ad attributi immediati degli oggetti dispositivo nella directory.

## <a name="to-create-an-advanced-rule"></a>Per creare una regola avanzata
1. Nel [portale di Azure classico](https://manage.windowsazure.com)selezionare **Active Directory**e aprire la directory dell'organizzazione.
2. Selezionare la scheda **Gruppi** e aprire il gruppo da modificare.
3. Selezionare la scheda **Configura**, selezionare l'opzione **Regola avanzata** e immettere la regola avanzata nella casella di testo.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Creazione del corpo di una regola avanzata
La regola avanzata che è possibile creare per le appartenenze dinamiche ai gruppi è essenzialmente un'espressione binaria composta da tre parti che genera un risultato di tipo true o false. Di seguito sono elencate le tre parti:

* Parametro sinistro
* Operatore binario
* Costante destra

Una regola avanzata completa simile alla seguente: (parametroSinistro operatoreBinario "CostanteDestra"), dove le parentesi di apertura e chiusura sono obbligatorie per racchiudere l'intera espressione binaria, le virgolette doppie sono obbligatorie per la costante a destra e la sintassi del parametro a sinistra è utente.proprietà. Una regola avanzata può includere più espressioni binarie separate dagli operatori logici -and, -or e -not.
Di seguito sono riportati alcuni esempi di regola avanzata con il formato corretto:

* (user.department -eq "Vendite") -or (user.department -eq "Marketing")
* (user.department -eq "Vendite") -and -not (user.jobTitle -contains "SDE")

Per l'elenco completo dei parametri supportati e degli operatori delle regole di espressione, vedere le sezioni riportate di seguito.


Si noti che la proprietà deve avere come prefisso il tipo di oggetto corretto, ovvero user o device.
La convalida della regola seguente avrà esito negativo: mail –ne null

Ecco la regola corretta:

user.mail –ne null

La lunghezza totale del corpo della regola avanzata non può superare i 2048 caratteri.

> [!NOTE]
> Le operazioni di stringa ed espressione regolare non fanno distinzione tra maiuscole e minuscole.
> Le stringhe contenenti virgolette (") devono essere precedute dal carattere di escape ', ad esempio user.department -eq \`"Sales".
> Usare le virgolette solo per i valori di tipo stringa e usare solo le virgolette singole.
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

   user.department –eq "Marketing" –and user.country –eq "US"

Equivale a:

   (user.department –eq "Marketing") –and (user.country –eq "US")

## <a name="using-the--in-and--notin-operators"></a>Uso degli operatori -In e -notIn

Per confrontare il valore di un attributo utente con una serie di valori diversi è possibile usare gli operatori -In o -notIn. Di seguito è illustrato un esempio con l'operatore -In:

    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]

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
| companyName | Qualsiasi valore stringa o $null | (user.companyName -eq "value") |
| department |Qualsiasi valore stringa o $null |(user.department -eq "valore") |
| displayName |Qualsiasi valore stringa. |(user.displayName -eq "valore") |
| facsimileTelephoneNumber |Qualsiasi valore stringa o $null |(user.facsimileTelephoneNumber -eq "valore") |
| givenName |Qualsiasi valore stringa o $null |(user.givenName -eq "valore") |
| jobTitle |Qualsiasi valore stringa o $null |(user.jobTitle -eq "valore") |
| mail |Qualsiasi valore stringa o $null (indirizzo SMTP dell'utente) |(user.mail -eq "valore") |
| mailNickName |Qualsiasi valore stringa (alias di posta dell'utente) |(user.mailNickName -eq "valore") |
| mobile |Qualsiasi valore stringa o $null |(user.mobile -eq "valore") |
| objectId |GUID dell'oggetto utente |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Identificatore di sicurezza (SID) locale per gli utenti sincronizzati da un ambiente locale al cloud. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
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

| Proprietà | Valori | Uso |
| --- | --- | --- |
| assignedPlans |Ogni oggetto della raccolta espone le proprietà di stringa seguenti: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

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

   user.mail –ne null equivale a user.mail –ne $null

## <a name="extension-attributes-and-custom-attributes"></a>Attributi di estensione ed attributi personalizzati
Gli attributi di estensione e gli attributi personalizzati sono supportati nelle regole di appartenenza dinamica.

Gli attributi di estensione vengono sincronizzati dall'istanza locale di Window Server AD e hanno il formato "ExtensionAttributeX", dove X equivale a 1 - 15.
Ecco un esempio di regola che usa un attributo di estensione:

(user.extensionAttribute15 -eq "Marketing")

Gli attributi personalizzati vengono sincronizzati dall'istanza locale di Windows Server AD o da un'applicazione SaaS collegata e hanno il formato "user.extension_[GUID]\__[Attributo]", dove [GUID] è l'identificatore univoco in AAD per l'applicazione che ha creato l'attributo in AAD e [Attributo] è il nome dell'attributo creato.
Ecco un esempio di regola che usa un attributo personalizzato:

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

È possibile trovare il nome dell'attributo personalizzato nella directory eseguendo una query sull'attributo nell'utente con Esplora grafico e cercando il nome dell'attributo. Non sono attualmente supportati attributi multivalore sincronizzati da Active Directory locale. 

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
| deviceCategory          | nome di una categoria di dispositivo valido    | (device.deviceCategory -eq "BYOD")                          |
| deviceManufacturer      | Qualsiasi valore stringa.                | (device.deviceManufacturer -eq "Microsoft")                 |
| deviceModel             | Qualsiasi valore stringa.                | (device.deviceModel -eq "IPhone 7+")                        |
| deviceOwnership         | Personale, Azienda               | (device.deviceOwnership -eq "Company")                      |
| domainName              | Qualsiasi valore stringa.                | (device.domainName -eq "contoso.com")                       |
| enrollmentProfileName   | Qualsiasi valore stringa.                | (device.enrollmentProfileName -eq "")                       |
| isRooted                | true false                      | (device.deviceOSType -eq true)                              |
| managementType          | Qualsiasi valore stringa.                | (device.managementType -eq "")                              |
| organizationalUnit      | Qualsiasi valore stringa.                | (device.organizationalUnit -eq "")                          |
| deviceId                | un deviceId valido                | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d") |
| objectId                | un objectId AAD valido            | (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d") |

> [!NOTE]
> Impossibile creare le regole di dispositivo usando l'elenco a discesa "regola semplice" nel portale di Azure classico.
>
>

## <a name="next-steps"></a>Passaggi successivi
Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Risoluzione dei problemi di appartenenza dinamica per i gruppi](active-directory-accessmanagement-troubleshooting.md)
* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)
* [Azure Active Directory cmdlets for configuring group settings](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

