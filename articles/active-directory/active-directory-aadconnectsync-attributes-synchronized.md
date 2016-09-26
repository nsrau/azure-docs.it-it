<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: Attributi sincronizzati con Azure Active Directory | Microsoft Azure"
	description="Fornisce l'elenco degli attributi sincronizzati con Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="markvi;andkjell"/>


# Servizio di sincronizzazione Azure AD Connect: Attributi sincronizzati con Azure Active Directory
Questo argomento elenca gli attributi sincronizzati tramite il servizio di sincronizzazione Azure AD Connect. Gli attributi sono raggruppati in base alle app Azure AD correlate.

## Attributi da sincronizzare
Spesso viene chiesto *Qual è l'elenco degli attributi minimi per sincronizzare*. L'approccio predefinito e consigliato consiste nel mantenere gli attributi predefiniti per creare un elenco indirizzi globale completo nel cloud e per ottenere tutte le funzionalità nei carichi di lavoro Office 365. Talvolta l'organizzazione non vuole sincronizzare alcuni attributi con il cloud perché contengono dati sensibili o informazioni personali, come illustrato nell'esempio seguente: ![attributi non validi](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

In questo caso, partire dal seguente elenco di attributi in questo argomento e individuare gli attributi che potrebbero contenere dati sensibili o informazioni personali e che non possono quindi essere sincronizzati. Deselezionare questi attributi durante l'installazione tramite [Filtro attributi e app di Azure AD](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering).

>[AZURE.WARNING] Quando si deselezionano gli attributi, prestare grande attenzione e deselezionare soltanto quelli che non devono assolutamente essere sincronizzati. Deselezionando altri attributi si potrebbe influire negativamente sulle funzionalità.

## Office 365 ProPlus

| Nome attributo| Utente| Commento |
| --- | :-: | --- |
| accountEnabled| X| Definisce se un account è abilitato.|
| cn| X| |
| displayName| X| |
| objectSID| X| Proprietà meccanica. Identificatore utente di Active Directory usato per mantenere la sincronizzazione tra Azure AD e Active Directory.|
| pwdLastSet| X| Proprietà meccanica. Consente di determinare quando invalidare token già rilasciati. Usata sia dal servizio di sincronizzazione delle password che dalla federazione.|
| sourceAnchor| X| Proprietà meccanica. Identificatore immutabile per mantenere la relazione tra Servizi di dominio Active Directory e Azure AD.|
| usageLocation| X| Proprietà meccanica. Paese dell'utente. Usato per l'assegnazione delle licenze.|
| userPrincipalName| X| Il nome dell'entità utente (UPN) costituisce l'ID di accesso per l'utente. In genere corrisponde al valore di [mail].|

## Exchange Online

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Definisce se un account è abilitato.|
| assistant| X| X| | |
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| homePhone| X| X| | |
| info| X| X| X| Questo attributo non viene attualmente utilizzato per i gruppi.|
| Initials| X| X| | |
| l| X| X| | |
| legacyExchangeDN| X| X| X| |
| mailNickname| X| X| X| |
| mangedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msDS-HABSeniorityIndex| X| X| X| |
| msDS-PhoneticDisplayName| X| X| X| |
| msExchArchiveGUID| X| | | |
| msExchArchiveName| X| | | |
| msExchAssistantName| X| X| | |
| msExchAuditAdmin| X| | | |
| msExchAuditDelegate| X| | | |
| msExchAuditDelegateAdmin| X| | | |
| msExchAuditOwner| X| | | |
| msExchBlockedSendersHash| X| X| | |
| msExchBypassAudit| X| | | |
| msExchCoManagedByLink| | | X| |
| msExchDelegateListLink| X| | | |
| msExchELCExpirySuspensionEnd| X| | | |
| msExchELCExpirySuspensionStart| X| | | |
| msExchELCMailboxFlags| X| | | |
| msExchEnableModeration| X| | X| |
| msExchExtensionCustomAttribute1| X| X| X| Questo attributo non viene attualmente utilizzato da Exchange Online. |
| msExchExtensionCustomAttribute2| X| X| X| Questo attributo non viene attualmente utilizzato da Exchange Online. |
| msExchExtensionCustomAttribute3| X| X| X| Questo attributo non viene attualmente utilizzato da Exchange Online. |
| msExchExtensionCustomAttribute4| X| X| X| Questo attributo non viene attualmente utilizzato da Exchange Online. |
| msExchExtensionCustomAttribute5| X| X| X| Questo attributo non viene attualmente utilizzato da Exchange Online. |
| msExchHideFromAddressLists| X| X| X| |
| msExchImmutableID| X| | | |
| msExchLitigationHoldDate| X| X| X| |
| msExchLitigationHoldOwner| X| X| X| |
| msExchMailboxAuditEnable| X| | | |
| msExchMailboxAuditLogAgeLimit| X| | | |
| msExchMailboxGuid| X| | | |
| msExchModeratedByLink| X| X| X| |
| msExchModerationFlags| X| X| X| |
| msExchRecipientDisplayType| X| X| X| |
| msExchRecipientTypeDetails| X| X| X| |
| msExchRemoteRecipientType| X| | | |
| msExchRequireAuthToSendTo| X| X| X| |
| msExchResourceCapacity| X| | | |
| msExchResourceDisplay| X| | | |
| msExchResourceMetaData| X| | | |
| msExchResourceSearchProperties| X| | | |
| msExchRetentionComment| X| X| X| |
| msExchRetentionURL| X| X| X| |
| msExchSafeRecipientsHash| X| X| | |
| msExchSafeSendersHash| X| X| | |
| msExchSenderHintTranslations| X| X| X| |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| msExchUserHoldPolicies| X| | | |
| msOrg-IsOrganizational| | | X| |
| objectSID| X| | X| Proprietà meccanica. Identificatore utente di Active Directory usato per mantenere la sincronizzazione tra Azure AD e Active Directory.|
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | Proprietà meccanica. Consente di determinare quando invalidare token già rilasciati. Usata sia dal servizio di sincronizzazione delle password che dalla federazione.|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Derivato da groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Proprietà meccanica. Identificatore immutabile per mantenere la relazione tra Servizi di dominio Active Directory e Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | Proprietà meccanica. Paese dell'utente. Usato per l'assegnazione delle licenze.|
| userCertificate| X| X| | |
| userPrincipalName| X| | | Il nome dell'entità utente (UPN) costituisce l'ID di accesso per l'utente. In genere corrisponde al valore di [mail].|
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |

## SharePoint Online

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Definisce se un account è abilitato.|
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| hideDLMembership| | | X| |
| homephone| X| X| | |
| info| X| X| X| |
| initials| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| middleName| X| X| | |
| mobile| X| X| | |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointLinkedBy| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| objectSID| X| | X| Proprietà meccanica. Identificatore utente di Active Directory usato per mantenere la sincronizzazione tra Azure AD e Active Directory.|
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherIpPhone| X| X| | |
| otherMobile| X| X| | |
| otherPager| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| postOfficeBox| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Proprietà meccanica. Consente di determinare quando invalidare token già rilasciati. Usata sia dal servizio di sincronizzazione delle password che dalla federazione.|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Derivato da groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Proprietà meccanica. Identificatore immutabile per mantenere la relazione tra Servizi di dominio Active Directory e Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| URL| X| X| | |
| usageLocation| X| | | Proprietà meccanica. Paese dell'utente. Usato per l'assegnazione delle licenze.|
| userPrincipalName| X| | | Il nome dell'entità utente (UPN) costituisce l'ID di accesso per l'utente. In genere corrisponde al valore di [mail].|
| wWWHomePage| X| X| | |

## Lync Online

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Definisce se un account è abilitato.|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| facsimiletelephonenumber| X| X| X| |
| givenName| X| X| | |
| homephone| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailNickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msExchHideFromAddressLists| X| X| X| |
| msRTCSIP-ApplicationOptions| X| | | |
| msRTCSIP-DeploymentLocator| X| X| | |
| msRTCSIP-Line| X| X| | |
| msRTCSIP-OptionFlags| X| X| | |
| msRTCSIP-OwnerUrn| X| | | |
| msRTCSIP-PrimaryUserAddress| X| X| | |
| msRTCSIP-UserEnabled| X| X| | |
| objectSID| X| | X| Proprietà meccanica. Identificatore utente di Active Directory usato per mantenere la sincronizzazione tra Azure AD e Active Directory.|
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Proprietà meccanica. Consente di determinare quando invalidare token già rilasciati. Usata sia dal servizio di sincronizzazione delle password che dalla federazione.|
| securityEnabled| | | X| Derivato da groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Proprietà meccanica. Identificatore immutabile per mantenere la relazione tra Servizi di dominio Active Directory e Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| usageLocation| X| | | Proprietà meccanica. Paese dell'utente. Usato per l'assegnazione delle licenze.|
| userPrincipalName| X| | | Il nome dell'entità utente (UPN) costituisce l'ID di accesso per l'utente. In genere corrisponde al valore di [mail].|
| wWWHomePage| X| X| | |

## Azure RMS

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Definisce se un account è abilitato.|
| cn| X| | X| Nome comune o alias. In genere il prefisso del valore [mail].|
| displayName| X| X| X| Stringa che rappresenta il nome spesso visualizzato come nome descrittivo (nome cognome).|
| mail| X| X| X| Indirizzo di posta elettronica completo.|
| member| | | X| |
| objectSID| X| | X| Proprietà meccanica. Identificatore utente di Active Directory usato per mantenere la sincronizzazione tra Azure AD e Active Directory.|
| proxyAddresses| X| X| X| Proprietà meccanica. Usata da Azure AD. Contiene tutti gli indirizzi di posta elettronica secondari per l'utente.|
| pwdLastSet| X| | | Proprietà meccanica. Consente di determinare quando invalidare token già rilasciati.|
| securityEnabled| | | X| Derivato da groupType.|
| sourceAnchor| X| X| X| Proprietà meccanica. Identificatore immutabile per mantenere la relazione tra Servizi di dominio Active Directory e Azure AD.|
| usageLocation| X| | | Proprietà meccanica. Paese dell'utente. Usato per l'assegnazione delle licenze.|
| userPrincipalName| X| | | Questo nome dell'entità utente (UPN) costituisce l'ID di accesso per l'utente. In genere corrisponde al valore di [mail].|

## Intune

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Definisce se un account è abilitato.|
| c| X| X| | |
| cn| X| | X| |
| description| X| X| X| |
| displayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| Proprietà meccanica. Identificatore utente di Active Directory usato per mantenere la sincronizzazione tra Azure AD e Active Directory.|
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Proprietà meccanica. Consente di determinare quando invalidare token già rilasciati. Usata sia dal servizio di sincronizzazione delle password che dalla federazione.|
| securityEnabled| | | X| Derivato da groupType|
| sourceAnchor| X| X| X| Proprietà meccanica. Identificatore immutabile per mantenere la relazione tra Servizi di dominio Active Directory e Azure AD.|
| usageLocation| X| | | Proprietà meccanica. Paese dell'utente. Usato per l'assegnazione delle licenze.|
| userPrincipalName| X| | | Il nome dell'entità utente (UPN) costituisce l'ID di accesso per l'utente. In genere corrisponde al valore di [mail].|

## Dynamics CRM

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Definisce se un account è abilitato.|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| l| X| X| | |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| objectSID| X| | X| Proprietà meccanica. Identificatore utente di Active Directory usato per mantenere la sincronizzazione tra Azure AD e Active Directory.|
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | Proprietà meccanica. Consente di determinare quando invalidare token già rilasciati. Usata sia dal servizio di sincronizzazione delle password che dalla federazione.|
| securityEnabled| | | X| Derivato da groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Proprietà meccanica. Identificatore immutabile per mantenere la relazione tra Servizi di dominio Active Directory e Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| title| X| X| | |
| usageLocation| X| | | Proprietà meccanica. Paese dell'utente. Usato per l'assegnazione delle licenze.|
| userPrincipalName| X| | | Il nome dell'entità utente (UPN) costituisce l'ID di accesso per l'utente. In genere corrisponde al valore di [mail].|

## Applicazioni di terze parti
Questo gruppo è un set di attributi usati come gli attributi minimi necessari per un'applicazione o un carico di lavoro generico. Può essere usato per un carico di lavoro non elencato in un'altra sezione o per un'app non Microsoft. Viene utilizzato in modo esplicito per:

- Yammer (viene usato solo Utente)
- [Scenari di collaborazione tra organizzazioni Business-to-Business (B2B) ibridi offerti da risorse come SharePoint](http://go.microsoft.com/fwlink/?LinkId=747036)

Questo gruppo è un set di attributi che può essere usato se non si utilizza la directory di Azure AD per supportare Office 365, Dynamics o Intune. Contiene un piccolo set di attributi principali.

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Definisce se un account è abilitato.|
| cn| X| | X| |
| displayName| X| X| X| |
| givenName| X| X| | |
| mail| X| | X| |
| managedBy| | | X| |
| mailNickName| X| X| X| |
| member| | | X| |
| objectSID| X| | | Proprietà meccanica. Identificatore utente di Active Directory usato per mantenere la sincronizzazione tra Azure AD e Active Directory.|
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Proprietà meccanica. Consente di determinare quando invalidare token già rilasciati. Usata sia dal servizio di sincronizzazione delle password che dalla federazione.|
| sn| X| X| | |
| sourceAnchor| X| X| X| Proprietà meccanica. Identificatore immutabile per mantenere la relazione tra Servizi di dominio Active Directory e Azure AD.|
| usageLocation| X| | | Proprietà meccanica. Paese dell'utente. Usato per l'assegnazione delle licenze.|
| userPrincipalName| X| | | Il nome dell'entità utente (UPN) costituisce l'ID di accesso per l'utente. In genere corrisponde al valore di [mail].|

## Windows 10
I computer o dispositivi appartenenti a un dominio Windows 10 sincronizzano alcuni attributi in Azure AD. Per altre informazioni sugli scenari, vedere [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md). Questi attributi verranno sempre sincronizzati e Windows 10 non appare come app che è possibile deselezionare. Un computer appartenente a un dominio Windows 10 viene identificato se l’attributo userCertificate è popolato.

| Nome attributo| Dispositivo| Commento |
| --- | :-: | --- |
| accountEnabled| X| |
| deviceTrustType| X| Valore hardcoded per i computer di dominio. |
| displayName | X| |
| ms-DS-CreatorSID | X| Chiamato anche registeredOwnerReference.|
| objectGUID | X| Chiamato anche deviceID.|
| objectSID | X| Chiamato anche onPremisesSecurityIdentifier.|
| operatingSystem | X| Chiamato anche deviceOSType.|
| operatingSystemVersion | X| Anche chiamato deviceOSVersion.|
| userCertificate | X| |

Questi attributi per l'**utente** si aggiungono alle altre app selezionate.

| Nome attributo| Utente| Commento |
| --- | :-: | --- |
| domainFQDN| X| Anche chiamato dnsDomainName. Ad esempio, contoso.com.|
| domainNetBios| X| Anche chiamato netBiosName. Ad esempio, CONTOSO.|

## Writeback della distribuzione ibrida Exchange
Se si sceglie di abilitare la distribuzione **ibrida di Exchange**, per questi attributi viene eseguito il writeback da Azure AD ad Active Directory locale. A seconda della versione di Exchange in uso, potrebbe essere sincronizzato un numero minore di attributi.

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| msDS-ExternalDirectoryObjectID| X| | | Derivato da cloudAnchor in Azure AD. Si tratta di un nuovo attributo di Exchange 2016.|
| msExchArchiveStatus| X| | | Archivio online: consente ai clienti di archiviare la posta elettronica.|
| msExchBlockedSendersHash| X| | | Filtro: esegue il writeback del filtro locale e dei dati dei mittenti attendibili e bloccati dai client.|
| msExchSafeRecipientsHash| X| | | Filtro: esegue il writeback del filtro locale e dei dati dei mittenti attendibili e bloccati dai client.|
| msExchSafeSendersHash| X| | | Filtro: esegue il writeback del filtro locale e dei dati dei mittenti attendibili e bloccati dai client.|
| msExchUCVoiceMailSettings| X| | | Abilitare la messaggistica unificata - Segreteria telefonica online: usata dall'integrazione di Microsoft Lync Server per indicare a Lync Server locale che tra i servizi online dell'utente è presente la segreteria telefonica.|
| msExchUserHoldPolicies| X| | | Blocco per controversia legale: consente ai servizi cloud di determinare gli utenti per i quali è attivato un blocco per controversia legale.|
| proxyAddresses| X| X| X| Viene inserito solo l'indirizzo x500 da Exchange Online.|

## Writeback dispositivi
Gli oggetti dispositivo vengono creati in Active Directory. Questi oggetti possono essere dispositivi aggiunti ad Azure AD o computer Windows 10 aggiunti al dominio.

| Nome attributo| Dispositivo| Commento |
| --- | :-: | --- |
| altSecurityIdentities | X| |
| displayName | X| |
| dn | X| |
| msDS-CloudAnchor | X| |
| msDS-DeviceID | X| |
| msDS-DeviceObjectVersion | X| |
| msDS-DeviceOSType | X| |
| msDS-DeviceOSVersion | X| |
| msDS-DevicePhysicalIDs | X| |
| msDS-KeyCredentialLink | X| Solo con lo schema di AD Windows Server 2016 |
| msDS-IsCompliant | X| |
| msDS-IsEnabled | X| |
| msDS-IsManaged | X| |
| msDS-RegisteredOwner | X| |


## Note

- Quando si usa un ID alternativo, l'attributo locale userPrincipalName viene sincronizzato con l'attributo onPremisesUserPrincipalName di Azure AD. L'attributo dell'ID alternativo, ad esempio mail, viene sincronizzato con l'attributo di Azure AD userPrincipalName.
- Negli elenchi sopra, il tipo di oggetto **Utente** si applica anche al tipo di oggetto **iNetOrgPerson**.

## Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0914_2016-->