---
title: Appartenenza dinamica ai gruppi basata su attributi in Azure Active Directory | Microsoft Docs
description: Procedura per creare regole avanzate per l'appartenenza dinamica ai gruppi, inclusi i parametri e gli operatori supportati per le regole delle espressioni.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: 8a52d80f32f822691be862d566c17c84efc73c26
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Creare regole basate su attributi per l'appartenenza dinamica ai gruppi in Azure Active Directory
In Azure Active Directory (Azure AD) è possibile creare regole avanzate per abilitare complesse appartenenze dinamiche ai gruppi basate su attributi. Questo articolo descrive in dettaglio la sintassi e gli attributi per creare regole di appartenenza dinamica per utenti o dispositivi.

Quando gli attributi di un utente o un dispositivo cambiano, il sistema valuta tutte le regole dinamiche del gruppo in una directory per verificare se la modifica attiverà aggiunte o rimozioni nel gruppo. Se un utente o un dispositivo soddisfa una regola in un gruppo, viene aggiunto come membro a tale gruppo. Se non soddisfano la regola, vengono rimossi.

> [!NOTE]
> È possibile configurare una regola per l'appartenenza dinamica nei gruppi di sicurezza o nei gruppi di Office 365.
>
> Questa funzionalità richiede una licenza Azure AD Premium P1 per ogni utente membro per almeno un gruppo dinamico. Perché gli utenti siano membri dei gruppi dinamici, non è obbligatorio che vengano effettivamente assegnate a loro le licenze, ma è necessario avere il numero minimo di licenze nel tenant per coprire tutti gli utenti. Se ad esempio si ha un totale di 1.000 utenti univoci in tutti i gruppi dinamici del tenant, è necessario avere almeno 1.000 licenze di Azure AD Premium P1 o versione successiva per soddisfare il requisito delle licenze.
>
> Sebbene sia possibile creare un gruppo dinamico per i dispositivi o gli utenti, non è possibile creare una regola che contenga sia oggetti utente che dispositivo.
> 
> Al momento non è possibile creare un gruppo di dispositivi in base agli attributi dell'utente proprietario. Le regole di appartenenza dispositivo possono fare riferimento solo ad attributi immediati degli oggetti dispositivo nella directory.
> 
> Microsoft Teams non supporta ancora l'appartenenza a gruppi dinamica. È possibile convalidare l'errore nei log associato a "Non è possibile eseguire la migrazione dell'appartenenza a gruppi dinamica"

## <a name="to-create-an-advanced-rule"></a>Per creare una regola avanzata
1. Accedere al [centro amministrativo Azure AD](https://aad.portal.azure.com) con un account di amministratore globale o amministratore di account utente.
2. Selezionare **Utenti e gruppi**.
3. Selezionare **Tutti i gruppi** e selezionare **Nuovo gruppo**.

   ![Aggiungere un nuovo gruppo](./media/active-directory-groups-dynamic-membership-azure-portal/new-group-creation.png)

4. Nel pannello **Gruppo** immettere un nome e una descrizione per il nuovo gruppo. Selezionare un **Tipo di appartenenza** di **Utente dinamico** o **Dispositivo dinamico**, a seconda che si intenda creare una regola per gli utenti o per i dispositivi e quindi selezionare **Aggiungi query dinamica**. È possibile usare il generatore di regole per creare una regola semplice o scrivere una regola avanzata manualmente. Questo articolo contiene ulteriori informazioni sugli attributi disponibili per utenti e dispositivi, oltre a esempi di regole avanzate.

   ![Aggiungere una regola di appartenenza dinamica](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

5. Dopo avere creato la regola, selezionare **Aggiungi query** nella parte inferiore del pannello.
6. Selezionare **Crea** on the **Gruppo** per creare il gruppo.

> [!TIP]
> La creazione del gruppo potrebbe non riuscire se la regola avanzata immessa non è corretta. Verrà visualizzata una notifica nell'angolo superiore destro del portale, che contiene una spiegazione dei motivi per cui la regola non è stata accettata dal sistema. Leggere attentamente per capire come occorre modificare la regola per renderla valida.

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
> Le operazioni di stringa ed espressione regolare non fanno distinzione tra maiuscole e minuscole. È anche possibile eseguire controlli Null usando *null* come costante, ad esempio user.department -eq *null*.
> Le stringhe contenenti virgolette (") devono essere precedute dal carattere di escape ', ad esempio user.department -eq \`"Sales".

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
| In ingresso | -in |
| Non incluso | -notIn |

## <a name="operator-precedence"></a>Precedenza degli operatori

Tutti gli operatori sono elencati di seguito per la precedenza da una posizione inferiore a quelli superiori. Gli operatori nella stessa riga hanno uguale precedenza:
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
Tutti gli operatori possono essere usati con o senza trattino come prefisso. Le parentesi sono necessarie solo quando la priorità non soddisfa i requisiti.
Ad esempio: 
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
La tabella seguente elenca gli errori comuni con la relativa risoluzione

| Errore di analisi della query | Uso errato | Uso corretto |
| --- | --- | --- |
| Errore: l'attributo non è supportato |(user.invalidProperty -eq "Valore") |(user.department -eq "valore")<br/><br/>Verificare che l'attributo sia incluso nell'[elenco di proprietà supportate](#supported-properties). |
| Errore: l'operatore non è supportato sull'attributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>L'operatore usato non è supportato per il tipo di proprietà, in questo esempio contains non può essere usato nel tipo booleano. Usare gli operatori corretti per il tipo di proprietà. |
| Errore: si è verificato un errore di compilazione della query. |1. (user.department -eq "Sales") (user.department -eq "Marketing")<br/><br/>2. (user.userPrincipalName -match "*@domain.ext") |1. Operatore mancante. Usare i due predicati di join -and oppure -or<br/><br/>(user.department -eq "Vendite") -or (user.department -eq "Marketing")<br/><br/>2. Errore nell'espressione regolare usata con -match<br/><br/>(user.userPrincipalName -match ".*@domain.ext"), in alternativa: (user.userPrincipalName -match "@domain.ext$")|

## <a name="supported-properties"></a>Proprietà supportate
Di seguito sono elencate tutte le proprietà utente che è possibile usare nelle regole avanzate:

### <a name="properties-of-type-boolean"></a>Proprietà di tipo boolean
Operatori consentiti

* -eq
* -ne

| Properties | Valori consentiti | Uso |
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

| Properties | Valori consentiti | Uso |
| --- | --- | --- |
| city |Qualsiasi valore di stringa o *null* |(user.city -eq "valore") |
| country |Qualsiasi valore di stringa o *null* |(user.country -eq "valore") |
| companyName | Qualsiasi valore di stringa o *null* | (user.companyName -eq "value") |
| department |Qualsiasi valore di stringa o *null* |(user.department -eq "valore") |
| displayName |Qualsiasi valore stringa. |(user.displayName -eq "valore") |
| employeeId |Qualsiasi valore stringa. |(user.employeeId -eq "valore")<br>(user.employeeId -ne *null*) |
| facsimileTelephoneNumber |Qualsiasi valore di stringa o *null* |(user.facsimileTelephoneNumber -eq "valore") |
| givenName |Qualsiasi valore di stringa o *null* |(user.givenName -eq "valore") |
| jobTitle |Qualsiasi valore di stringa o *null* |(user.jobTitle -eq "valore") |
| mail |Qualsiasi valore di stringa o *null* (indirizzo SMTP dell'utente) |(user.mail -eq "valore") |
| mailNickName |Qualsiasi valore stringa (alias di posta dell'utente) |(user.mailNickName -eq "valore") |
| mobile |Qualsiasi valore di stringa o *null* |(user.mobile -eq "valore") |
| objectId |GUID dell'oggetto utente |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Identificatore di sicurezza (SID) locale per gli utenti sincronizzati da un ambiente locale al cloud. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Nessuno DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Qualsiasi valore di stringa o *null* |(user.physicalDeliveryOfficeName -eq "valore") |
| postalCode |Qualsiasi valore di stringa o *null* |(user.postalCode -eq "valore") |
| preferredLanguage |Codice ISO 639-1 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Qualsiasi valore di stringa o *null* |(user.sipProxyAddress -eq "valore") |
| state |Qualsiasi valore di stringa o *null* |(user.state -eq "valore") |
| streetAddress |Qualsiasi valore di stringa o *null* |(user.streetAddress -eq "valore") |
| surname |Qualsiasi valore di stringa o *null* |(user.surname -eq "valore") |
| telephoneNumber |Qualsiasi valore di stringa o *null* |(user.telephoneNumber -eq "valore") |
| usageLocation |Codice di paese di due lettere |(user.usageLocation -eq "US") |
| userPrincipalName |Qualsiasi valore stringa. |(user.userPrincipalName -eq "alias@domain") |
| userType |membro guest *null* |(user.userType -eq "Membro") |

### <a name="properties-of-type-string-collection"></a>Proprietà di tipo insieme String
Operatori consentiti

* -contains
* -notContains

| Properties | Valori consentiti | Uso |
| --- | --- | --- |
| otherMails |Qualsiasi valore stringa. |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Proprietà multivalore
Operatori consentiti

* -any (soddisfatto quando almeno un elemento della raccolta corrisponde alla condizione)
* -all (soddisfatto quando tutti gli elementi della raccolta corrispondono alla condizione)

| Properties | Valori | Uso |
| --- | --- | --- |
| assignedPlans |Ogni oggetto della raccolta espone le proprietà di stringa seguenti: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

Le proprietà multivalore sono raccolte di oggetti dello stesso tipo. È possibile usare gli operatori -any e -all per applicare una condizione rispettivamente a uno o a tutti gli elementi della raccolta. Ad esempio: 

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

Per specificare un valore Null in una regola, è possibile usare il valore *null*. Prestare attenzione a non usare le virgolette per la parola *null*, in caso contrario, verrà interpretato come valore di stringa letterale. Il modo corretto per fare riferimento al valore Null è il seguente:
```
   user.mail –ne null
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
È anche possibile creare una regola che consenta di selezionare gli oggetti dispositivo per l'appartenenza a un gruppo. È possibile usare gli attributi del dispositivo seguenti.

 Attributo del dispositivo  | Valori | Esempio
 ----- | ----- | ----------------
 accountEnabled | true false | (device.accountEnabled -eq true)
 displayName | Qualsiasi valore stringa. |(device.displayName - eq "Iphone di Rob")
 deviceOSType | Qualsiasi valore stringa. | (device.deviceOSType -eq "iPad") o (device.deviceOSType -eq "iPhone")
 deviceOSVersion | Qualsiasi valore stringa. | (device.OSVersion -eq "9.1")
 deviceCategory | nome di una categoria di dispositivo valido | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | Qualsiasi valore stringa. | (device.deviceManufacturer -eq "Samsung")
 deviceModel | Qualsiasi valore stringa. | (device.deviceModel -eq "iPad Air")
 deviceOwnership | Personale, Azienda, Sconosciuto | (device.deviceOwnership -eq "Company")
 domainName | Qualsiasi valore stringa. | (device.domainName -eq "contoso.com")
 enrollmentProfileName | Nome del profilo di registrazione dispositivi Apple | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | true false | (device.isRooted -eq true)
 managementType | MDM (per i dispositivi mobili)<br>PC (per i computer gestiti dall'agente di PC Intune) | (device.managementType -eq "MDM")
 organizationalUnit | qualsiasi valore di stringa corrispondente al nome dell'unità organizzativa impostato da un server di Active Directory locale | (device.organizationalUnit -eq "US PCs")
 deviceId | ID dispositivo di Azure AD valido | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | ID oggetto di Azure AD valido |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Modifica dell'appartenenza dinamica in statica e viceversa
È possibile modificare il modo in cui viene gestita l'appartenenza in un gruppo. Ciò è utile per mantenere lo stesso ID e nome di gruppo nel sistema in modo che i riferimenti al gruppo esistenti restino validi. Creando un nuovo gruppo sarebbe infatti necessario aggiornare tali riferimenti.

È in corso l'aggiornamento del portale di Azure per supportare questa funzionalità. Nel frattempo è possibile usare i cmdlet di PowerShell come illustrato di seguito.

> [!WARNING]
> Quando si modifica un gruppo statico esistente in gruppo dinamico, tutti i membri esistenti verranno rimossi dal gruppo e verrà quindi elaborata la regola di appartenenza per aggiungere nuovi membri. Se il gruppo viene usato per controllare l'accesso alle app o alle risorse, i membri originali potrebbero perdere l'accesso finché non viene completata l'elaborazione della regola di appartenenza.
>
> È consigliabile testare prima la nuova regola per verificare che la nuova appartenenza nel gruppo sia quella prevista.

**Uso di PowerShell per modificare la gestione delle appartenenze in un gruppo**

> [!NOTE]
> Per modificare le proprietà dei gruppi dinamici, sarà necessario usare i cmdlet della **versione di anteprima di** [Azure AD PowerShell versione 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). È possibile installare l'anteprima da [qui](https://www.powershellgallery.com/packages/AzureADPreview).

Di seguito è riportato un esempio delle funzioni che cambiano la gestione delle appartenenze in un gruppo esistente. È necessario prestare attenzione in modo da modificare correttamente la proprietà GroupTypes e mantenere tutti i valori eventualmente presenti non correlati all'appartenenza dinamica.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Per rendere statico un gruppo:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
Per rendere dinamico un gruppo:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>Passaggi successivi
Questi articoli forniscono informazioni aggiuntive sui gruppi in Azure Active Directory.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)
