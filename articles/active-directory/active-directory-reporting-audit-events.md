---
title: Eventi del report di controllo di Azure Active Directory | Microsoft Docs
description: Eventi controllati disponibili per la visualizzazione e il download dalla propria istanza di Azure Active Directory
services: active-directory
documentationcenter: 
author: dhanyahk
manager: mbaldwin
editor: 
ms.assetid: 307eedf7-05bc-448d-a84d-bead5a4c5770
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2016
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 6950072b8970dfc1b80674efe0be6cb7496fd5ce
ms.openlocfilehash: 2dda60160bbcaa729bc8e5cec4f47efc10d120cf


---
# <a name="azure-active-directory-audit-report-events"></a>Eventi del report di controllo di Azure Active Directory
*Questo documento fa parte della [guida alla creazione di report in Azure Active Directory](active-directory-reporting-guide.md).*

Il report di controllo di Azure Active Directory consente ai clienti di identificare le azioni con privilegi che si sono verificate nella propria istanza di Azure Active Directory. Le azioni con privilegi includono modifiche di elevazione dei privilegi, ad esempio la creazione dei ruoli o le reimpostazioni delle password, la modifica delle configurazioni dei criteri, ad esempio i criteri delle password o le modifiche alla configurazione della directory, ad esempio le modifiche alle impostazioni di federazione del dominio. Nei report è incluso il record di controllo per il nome dell'evento, l'attore che ha eseguito l'azione, la risorsa di destinazione interessata dalla modifica e la data e l'ora (UTC). I clienti possono recuperare l'elenco degli eventi di controllo per la propria istanza di Azure Active Directory tramite il [portale di Azure](https://portal.azure.com/), come descritto in [Visualizzare i log di controllo](active-directory-reporting-azure-portal.md).

## <a name="list-of-audit-report-events"></a>Elenco degli eventi del report di controllo
<!--- audit event descriptions should be in the past tense --->

| Eventi | Descrizione evento |
| --- | --- |
| **Eventi degli utenti** | |
| Aggiunta di un utente |È stato aggiunto un utente alla directory. |
| Eliminazione di un utente. |È stato eliminato un utente dalla directory. |
| Impostazione delle proprietà della licenza |Sono state impostate le proprietà della licenza per un utente nella directory. |
| Reimpostazione password utente |È stata reimpostata la password per un utente nella directory. |
| Modifica password utente |È stata modificata la password per un utente nella directory. |
| Modifica licenza utente |È stata modificata la licenza assegnata a un utente nella directory. Per visualizzare le licenze aggiornate, vedere le proprietà [Aggiornamento utente](#update-user-attributes) seguenti. |
| Aggiornamento utente |È stato aggiornato un utente nella directory. [Vedere le informazioni seguenti](#update-user-attributes) per conoscere gli attributi che possono essere aggiornati. |
| Impostazione forzatura per la modifica delle password utente |È stata impostata la proprietà che obbliga un utente a modificare la password all'accesso. |
| Aggiornamento delle credenziali dell'utente |L'utente ha modificato la password. |
| **Eventi del gruppo** | |
| Aggiungi gruppo |È stato creato un gruppo nella directory. |
| Aggiornare un gruppo |È stato aggiornato un gruppo nella directory Per visualizzare le proprietà dei gruppi aggiornate, vedere le [proprietà dei gruppi controllate](#update-group-attributes) nella sezione seguente |
| Eliminare gruppo |È stato eliminato un gruppo dalla directory. |
| CreateGroupSettings |Sono state create impostazioni del gruppo. |
| UpdateGroupSettings |Sono state aggiornate impostazioni del gruppo. Per visualizzare le impostazioni dei gruppi aggiornate, vedere le [proprietà dei gruppi controllate](#update-group-attributes) nella sezione seguente. |
| DeleteGroupSettings |Sono state eliminate impostazioni del gruppo. |
| SetGroupLicense |È stata configurata la licenza del gruppo. |
| SetGroupManagedBy |È stato configurato il gruppo che deve essere gestito dall'utente. |
| AddGroupMember |È stato aggiunto un membro al gruppo. |
| RemoveGroupMember |Rimuovere un membro dal gruppo |
| AddGroupOwner |È stato aggiunto un proprietario al gruppo. |
| RemoveGroupOwner |È stato rimosso il proprietario dal gruppo. |
| **Eventi dell'applicazione** | |
| Aggiunta di un'entità servizio |È stata aggiunta un'entità servizio alla directory. |
| Rimozione di un'entità servizio |È stata rimossa un'entità servizio dalla directory. |
| Aggiunta delle credenziali dell'entità servizio |Sono state aggiunte le credenziali a un'entità servizio. |
| Rimozione delle credenziali dell'entità servizio |Sono state rimosse le credenziali da un'entità servizio. |
| Aggiunta di una voce di delega |Creazione di un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) nella directory. |
| Impostazione voce di delega |Aggiornamento di un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) nella directory. |
| Rimozione voce di delega |Eliminazione di un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) nella directory. |
| **Eventi del ruolo** | |
| Aggiunta di un membro del ruolo al ruolo |È stato aggiunto un utente a un ruolo della directory. |
| Rimozione di un membro ruolo dal ruolo |È stato rimosso un utente da un ruolo della directory. |
| AddRoleDefinition |È stata aggiunta la definizione del ruolo. |
| UpdateRoleDefinition |È stata aggiornata la definizione del ruolo. Per visualizzare le impostazioni dei ruoli aggiornate, vedere le [proprietà delle definizioni dei ruoli controllate](#update-role-definition-attributes) nella sezione seguente |
| DeleteRoleDefinition |È stata eliminata la definizione del ruolo. |
| AddRoleAssignmentToRoleDefinition |È stata aggiunta l'assegnazione del ruolo alla definizione del ruolo. |
| RemoveRoleAssignmentFromRoleDefinition |È stata rimossa l'assegnazione del ruolo dalla definizione del ruolo. |
| AddRoleFromTemplate |È stato aggiunto un ruolo dal modello. |
| UpdateRole |È stato aggiornato un ruolo. |
| AddRoleScopeMemberToRole |È stato aggiunto un membro con ambito al ruolo. |
| RemoveRoleScopedMemberFromRole |È stato rimosso un membro con ambito dal ruolo. |
| **Eventi relativi al dispositivo (eventi nuovi)** | |
| AddDevice |È stato aggiunto un dispositivo. |
| UpdateDevice |È stato aggiornato un dispositivo. Per visualizzare le proprietà dei dispositivi aggiornate, vedere le [proprietà dei dispositivi controllate](#update-device-attributes) nella sezione seguente. |
| DeleteDevice |È stato eliminato un dispositivo. |
| AddDeviceConfiguration |È stata aggiunta la configurazione del dispositivo. |
| UpdateDeviceConfiguration |È stata aggiornata la configurazione del dispositivo. Per visualizzare le proprietà di configurazione dei dispositivi aggiornate, vedere le [proprietà di configurazione dei dispositivi controllate](#update-device-configuration-attributes) nella sezione seguente. |
| DeleteDeviceConfiguration |È stata eliminata la configurazione del dispositivo. |
| AddRegisteredOwner |È stato aggiunto un proprietario registrato al dispositivo. |
| AddRegisteredUsers |Sono stati aggiunti utenti registrati al dispositivo. |
| RemoveRegisteredOwner |Viene rimosso il proprietario registrato dal dispositivo. |
| RemoveRegisteredUsers |Vengono rimossi gli utenti registrati dal dispositivo. |
| RemoveDeviceCredentials |Vengono rimosse le credenziali del dispositivo. |
| **Eventi B2B** | |
| Inviti batch caricati. |Un amministratore ha caricato un file contenente gli inviti da inviare agli utenti partner. |
| Inviti batch elaborati. |È stato elaborato un file contenente gli inviti per gli utenti partner. |
| Invitare gli utenti esterni. |Un utente esterno è stato invitato alla directory. |
| Riscattare l'invito utente esterno. |Un utente esterno è stato riscattato alla directory. |
| Aggiungere utenti esterni al gruppo. |Un utente esterno è stato assegnato all'appartenenza a un gruppo nella directory. |
| Assegnare utenti esterni all'applicazione. |A un utente esterno è stato assegnato l'accesso diretto a un'applicazione. |
| Creazione del tenant virale. |Un nuovo tenant è stato creato in Azure AD per il riscatto dell’invito. |
| Creazione dell'utente virale. |Un utente è stato creato in un tenant esistente in Azure AD dal riscatto dell’invito. |
| **Unità amministrative (eventi nuovi)** | |
| AddAdministrativeUnit |Viene aggiunta un'unità amministrativa. |
| UpdateAdministrativeUnit |Viene aggiornata un'unità amministrativa. Per visualizzare le proprietà delle unità amministrative aggiornate, vedere le [proprietà delle unità amministrative controllate](#update-administrative-unit-attributes) nella sezione seguente. |
| DeleteAdministrativeUnit |Viene eliminata un'unità amministrativa. |
| AddMemberToAdministrativeUnit |Viene aggiunto un membro all'unità amministrativa. |
| RemoveMemberFromAdministrativeUnit |Viene rimosso un membro dall'unità amministrativa. |
| **Eventi della directory** | |
| Aggiunta di un partner alla società |È stato aggiunto un partner alla directory. |
| Rimozione di un partner dalla società |È stato rimosso un partner dalla directory. |
| DemotePartner |Viene abbassato di livello un partner. |
| Aggiunta di un dominio alla società |È stato aggiunto un dominio alla directory. |
| Rimozione di un dominio dalla società |È stato rimosso un dominio dalla directory. |
| Aggiornamento dominio |È stato aggiornato un dominio nella directory. Per visualizzare le proprietà dei domini aggiornate, vedere le [proprietà dei domini controllate](#update-domain-attributes) nella sezione seguente. |
| Impostazione dell'autenticazione del dominio |È stata modificata l'impostazione del dominio predefinita per la società. |
| Impostazione delle informazioni di contatto della società |Sono state impostate le preferenze di contatto a livello aziendale. Sono inclusi gli indirizzi di posta elettronica per le attività di marketing, oltre alle notifiche tecniche relative ai Microsoft Online Services. |
| Configurazione delle impostazioni di federazione nel dominio |Sono state aggiornate le impostazioni di federazione per un dominio. |
| Verifica dominio |È stato verificato un dominio nella directory. |
| Verifica del dominio tramite la verifica di posta elettronica |È stato verificato un dominio nella directory tramite la verifica di posta elettronica. |
| Impostazione del flag DirSyncEnabled per la società |È stata impostata la proprietà che abilita una directory per Azure AD Sync. |
| Impostazione criteri password |Sono stati impostati vincoli di lunghezza e caratteri per le password utente. |
| Impostazione informazioni società |Sono state aggiornate le impostazioni a livello aziendale. Per altre informazioni, vedere il cmdlet di PowerShell [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) . |
| SetCompanyAllowedDataLocation |È stata configurata la posizione dei dati consentita per l'azienda. |
| SetCompanyDirSyncEnabled |È stato impostato il flag DirSyncEnabled. |
| SetCompanyDirSyncFeature |È stata configurata la funzionalità Set DirSync. |
| SetCompanyInformation |Sono state configurate le informazioni sull'azienda. |
| SetCompanyMultiNationalEnabled |È stata abilitata la funzionalità multinazionale per l'azienda. |
| SetDirectoryFeatureOnTenant |È stata configurata una funzionalità directory nel tenant. |
| SetTenantLicenseProperties |Sono state configurate le proprietà della licenza del tenant. |
| CreateCompanySettings |Vengono create le impostazioni aziendali. |
| UpdateCompanySettings |Vengono aggiornate le impostazioni aziendali. Per visualizzare le proprietà aziendali aggiornate, vedere le [proprietà aziendali controllate](#update-company-attributes) nella sezione seguente. |
| DeleteCompanySettings |Vengono eliminate le impostazioni aziendali. |
| SetAccidentalDeletionThreshold |È stata configurata la soglia di eliminazioni accidentali. |
| SetRightsManagementProperties |Sono state configurate le proprietà di Rights Management. |
| PurgeRightsManagementProperties |Vengono ripulite configurate le proprietà di Rights Management. |
| UpdateExternalSecrets |Vengono aggiornati i segreti esterni. |
| **Eventi relativi ai criteri (eventi nuovi)** | |
| AddPolicy |Vengono aggiunti criteri. |
| UpdatePolicy |Vengono aggiornati i criteri. |
| DeletePolicy |Vengono eliminati i criteri. |
| AddDefaultPolicyApplication |Vengono aggiunti criteri all'applicazione. |
| AddDefaultPolicyServicePrincipal |Vengono aggiunti criteri all'entità servizio. |
| RemoveDefaultPolicyApplication |Vengono rimossi criteri dall'applicazione. |
| RemoveDefaultPolicyServicePrincipal |Vengono rimossi criteri dall'entità servizio. |
| RemovePolicyCredentials |Vengono rimosse le credenziali dei criteri. |

## <a name="audit-report-retention"></a>Conservazione dei report di controllo
Gli eventi nel report di controllo di Azure Active Directory vengono conservati per 180 giorni. Per altre informazioni sulla conservazione dei report, vedere la pagina relativa ai [criteri di conservazione dei report di Azure Active Directory](active-directory-reporting-retention.md).

Per i clienti interessati alla conservazione gli eventi di controllo per periodi più lunghi, l'API di creazione report consente di eseguire regolarmente il pull degli eventi di controllo in un archivio dati separato. Per i dettagli, vedere la pagina relativa all' [introduzione all'API di creazione report](active-directory-reporting-api-getting-started.md) .

## <a name="properties-included-with-each-audit-event"></a>Proprietà incluse in ogni evento di controllo
| Proprietà | Descrizione |
| --- | --- |
| Data e ora |Data e ora in cui si è verificato l'evento |
| Attore |Utente o entità servizio che ha eseguito l'azione |
| Azione |Azione eseguita |
| Destinazione |Utente o entità servizio su cui è stata eseguita l'azione |

## <a name="update-user-attributes"></a>Attributi "Aggiornamento utente"
L'evento di controllo di aggiornamento dell'utente include informazioni aggiuntive sugli attributi utente che sono stati aggiornati. Per ogni attributo, sono inclusi sia il valore precedente che quello nuovo.

| Attributo | Descrizione |
| --- | --- |
| AccountEnabled |L'utente può effettuare l'accesso. |
| AssignedLicense |Tutte le licenze assegnate all'utente. |
| AssignedPlan |Piani di servizio risultanti dalle licenze assegnate all'utente. |
| LicenseAssignmentDetail |Dettagli sulle licenze assegnate all'utente. Ad esempio, in caso di concessione di licenze basata su gruppo, sarebbe incluso il gruppo che ha concesso la licenza. |
| Mobile |Telefono cellulare dell'utente. |
| OtherMail |Indirizzo di posta elettronica alternativo dell'utente. |
| OtherMobile |Telefono cellulare alternativo dell'utente. |
| StrongAuthenticationMethod |Elenco di metodi di verifica configurato dall'utente per l'autenticazione a più fattori, ad esempio chiamata vocale, SMS o codice di verifica da un'app per dispositivi mobili. |
| StrongAuthenticationRequirement |Se l'autenticazione a più fattori è imposta, abilitata o disabilitata per questo utente. |
| StrongAuthenticationUserDetails |Numero di telefono dell'utente, numero di telefono alternativo e indirizzo di posta elettronica usati per la verifica dell'autenticazione a più fattori e della reimpostazione della password. |
| StrongAuthenticationPhoneAppDetail |Dati per le applicazioni del telefono registrato per eseguire l'accesso 2FA. |
| TelephoneNumber |Numero di telefono dell'utente. |
| AlternativeSecurityId |ID di sicurezza alternativo per l'oggetto. |
| CreationType |Metodo di creazione dell'utente, su invito o virale. |
| InviteTicket |Elenco di ticket di invito per l'utente. |
| InviteReplyUrl |Elenco di URL di risposta per l'accettazione dell'invito. |
| InviteResources |Elenco di risorse a cui l'utente è stato invitato. |
| LastDirSyncTime |Ultima volta in cui l'oggetto è stato aggiornato a causa della sincronizzazione dalla directory autorevole (cliente, locale). |
| MSExchRemoteRecipientType |Esegue il mapping ai tipi di destinatari MSO. Vedere [tipi di destinatari MSO] https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx per i tipi di destinatari |
| PreferredDataLocation |Posizione preferita per i dati dell'utente, del gruppo, del contatto, della cartella pubblica o del dispositivo. |
| ProxyAddresses |Indirizzo da cui un oggetto destinatario di Exchange Server viene riconosciuto in un sistema di posta elettronica esterno. |
| StsRefreshTokensValidFrom |Fornisce informazioni sulla revoca dei token di aggiornamento. Eventuali token di aggiornamento del servizio token di sicurezza emessi prima di questa data vengono considerati scaduti. |
| UserPrincipalName |Valore UPN che indica il nome di accesso basato su Internet per un utente. |
| UserState |Stato dell'utente PendingApproval/PendingAcceptance/Accepted/PendingVerification. |
| UserStateChangedOn |Timestamp dell'ultima modifica a UserState. Viene usato per attivare i flussi di lavoro del ciclo di vita. |
| UserType |Tipo di utente. Membro (0), guest (1), virale (2). |

## <a name="update-group-attributes"></a>Attributi di "Aggiornamento gruppo"
| Attributo | Descrizione |
| --- | --- |
| Classificazione |Classificazione per un gruppo unificato (HBI, MBI e così via). |
| Descrizione |Frasi descrittive sull'oggetto leggibili da utenti. |
| DisplayName |Nome visualizzato per un oggetto. |
| DirSyncEnabled |Indica se la sincronizzazione viene eseguita da una directory autorevole (cliente, locale). |
| GroupLicenseAssignment |Assegnazione di licenza di un gruppo. |
| GroupType |Tipo di gruppo, unificato (0). |
| IsMembershipRuleLocked |Indica che la proprietà MembershipRule viene configurata dal servizio di gestione dei gruppi self-service e non può essere modificata dagli utenti. È applicabile solo a gruppi in cui GroupType include GroupType.DynamicMembership. |
| IsPublic |Flag che indica se il gruppo è pubblico/privato. |
| LastDirSyncTime |Ultima volta in cui l'oggetto è stato aggiornato come risultato della sincronizzazione dalla directory autorevole (cliente, locale). |
| Mail |Indirizzo di posta elettronica primario. |
| MailEnabled |Indica se il gruppo ha funzionalità di posta elettronica. |
| MailNickname |Moniker per un oggetto Rubrica, in genere corrispondente alla parte del nome dell'indirizzo di posta elettronica che precede il simbolo "@". |
| MembershipRule |Stringa che esprime i criteri usati dal servizio di gestione di gruppi self-service per determinare i membri che devono appartenere al gruppo. Vedere anche IsMembershipRuleLocked. È applicabile solo a gruppi in cui GroupType include GroupType.DynamicMembership. |
| MembershipRuleProcessingState |Valore di enumerazione definito dal servizio di gestione dei gruppi self-service che indica lo stato di elaborazione dell'appartenenza per questo gruppo. È applicabile solo a gruppi in cui GroupType include GroupType.DynamicMembership. |
| ProxyAddresses |Indirizzo da cui un oggetto destinatario di Exchange Server viene riconosciuto in un sistema di posta elettronica esterno. |
| RenewedDateTime |Record del timestamp che indica la data più recente in cui il gruppo è stato rinnovato. |
| SecurityEnabled |Indica se l'appartenenza al gruppo può influire sulle decisioni di autorizzazione. |
| WellKnownObject |Applica un'etichetta a un oggetto directory, designandolo come parte di un set predefinito. |

## <a name="update-device-attributes"></a>Attributi di "Aggiornamento dispositivo"
| Attributo | Descrizione |
| --- | --- |
| AccountEnabled |Indica se un'entità di sicurezza può eseguire l'autenticazione. |
| CloudAccountEnabled |Indica se un'entità di sicurezza può eseguire l'autenticazione. Scritto da InTune quando il dispositivo viene gestito in locale. |
| CloudDeviceOSType |Tipo di dispositivo in base al sistema operativo, ad esempio Windows RT, iOS. Se impostato da un servizio cloud, ad esempio Intune, questo attributo diventa autorevole nella directory per DeviceOSType. |
| CloudDeviceOSVersion |Versione del sistema operativo. Se impostato da un servizio cloud, ad esempio Intune, questo attributo diventa autorevole nella directory per DeviceOSVersion. |
| CloudDisplayName |Valore dell'attributo LDAP displayName. Se impostato da un servizio cloud, ad esempio Intune, questo attributo diventa autorevole nella directory per displayName. |
| CloudCreated |Indica se l'oggetto è stato creato da servizi cloud. |
| CompliantUntil |Scadenza della conformità del dispositivo. |
| DeviceMetadata |Metadati personalizzati per il dispositivo. |
| DeviceObjectVersion |Questo attributo viene usato per identificare la versione dello schema del dispositivo. |
| DeviceOSType |Tipo di dispositivo in base al sistema operativo, ad esempio Windows RT, iOS. Scritto dal servizio di registrazione e destinato all'aggiornamento dal servizio di gestione MDM o dal servizio di gestione semplificata del servizio token di sicurezza. |
| DeviceOSVersion |Versione del sistema operativo. Scritto dal servizio di registrazione e destinato all'aggiornamento dal servizio di gestione MDM o dal servizio di gestione semplificata del servizio token di sicurezza. |
| DevicePhysicalIds |Attributo multivalore destinato all'archiviazione degli identificatori del dispositivo fisico. Può includere l'ID BIOS, l'identificazione personale di TPM, ID specifici dell'hardware e così via. |
| DirSyncEnabled |Indica se la sincronizzazione viene eseguita da una directory autorevole (cliente, locale). |
| DisplayName |Nome visualizzato per un oggetto. |
| IsCompliant |Questo attributo viene usato per gestire lo stato di gestione di dispositivi mobili del dispositivo. |
| IsManaged |Questo attributo viene usato per indicare che il dispositivo viene gestito da un MDM cloud. |
| LastDirSyncTime |Ultima volta in cui l'oggetto è stato aggiornato a causa della sincronizzazione dalla directory autorevole (cliente, locale). |

## <a name="update-device-configuration-attributes"></a>Attributi di "Aggiornamento configurazione dispositivi"
| Attributo | Descrizione |
| --- | --- |
| MaximumRegistrationInactivityPeriod |Numero massimo di giorni per cui un dispositivo può essere inattivo prima che venga considerato per la rimozione. |
| RegistrationQuota |Criterio usato per limitare il numero di registrazioni di dispositivi consentito per un singolo utente. |

## <a name="update-service-principal-configuration-attributes"></a>Attributi di "Aggiornamento configurazione delle entità servizio"
| Attributo | Descrizione |
| --- | --- |
| AccountEnabled |Indica se un'entità di sicurezza può eseguire l'autenticazione. |
| AppPrincipalId |Identità esterna definita dall'applicazione per un'entità di sicurezza. |
| DisplayName |Nome visualizzato per un oggetto. |
| ServicePrincipalName |Nome di un'entità servizio contenente "name/authority" in cui name specifica un valore della classe applicazione e authority contiene almeno hostname[:port] o "name" che specifica un identificatore per l'entità servizio. |

## <a name="update-app-attributes"></a>Attributi di "Aggiornamento app"
| Attributo | Descrizione |
| --- | --- |
| AppAddress |Set di indirizzi (URL di reindirizzamento) assegnati a un'entità servizio. |
| AppId |ID applicazione dell'app. |
| AppIdentifierUri |URI di applicazione che identifica l'applicazione.  Corrisponde in genere all'URL di accesso all'applicazione. |
| AppLogoUrl |URL per l'immagine del logo dell'applicazione archiviato in una rete CDN. |
| AvailableToOtherTenants |Se True, indica che l'applicazione è multi-tenant, ovvero può essere usata da altri tenant. |
| DisplayName |Nome visualizzato per il nome dell'applicazione. |
| Entitlement |Elenco di diritti dell'applicazione. |
| ExternalUserAccountDelegationsAllowed |Flag che indica se l'applicazione della risorsa è attendibile e può creare voci di delega per account utente esterni. |
| GroupMembershipClaims |Criteri delle attestazioni dell'appartenenza al gruppo. |
| PublicClient |True se il client non riesce a mantenere il segreto, ad esempio un client non riservato in OAuth2.0. |
| RecordConsentConditions |Tipi di condizioni di consenso, in base a quanto definito dai termini del contratto, ovvero None (0), SilentConsentForPartnerManagedApp(1). Questo valore verrà esposto nello schema dell'API Graph e può essere impostato/modificato solo da amministratori del tenant. |
| RequiredResourceAccess |Contenuto XML di un valore della proprietà RequiredResourceAccess. |
| WebApp |Se True, indica che questa applicazione è un'app Web. |
| WwwHomepage |Pagina Web primaria. |

## <a name="update-role-attributes"></a>Attributi di "Aggiornamento ruolo"
| Attributo | Descrizione |
| --- | --- |
| AppAddress |Set di indirizzi (URL di reindirizzamento) assegnati a un'entità servizio. |
| BelongsToFirstLoginObjectSet |Se True, indica che questo oggetto appartiene al set di oggetti necessari per abilitare l'accesso del primo amministratore di un nuovo tenant. |
| Builtin |Indica se la durata di un oggetto è di proprietà del sistema. |
| Descrizione |Frasi descrittive sull'oggetto leggibili da utenti. |
| DisplayName |Nome visualizzato per un oggetto. |
| MailNickname |Moniker per un oggetto Rubrica, in genere corrispondente alla parte del nome dell'indirizzo di posta elettronica che precede il simbolo "@". |
| RoleDisabled |Indica se il ruolo deve essere ignorato per finalità di controllo di accesso. |
| RoleTemplateId |Identità del modello del ruolo. |
| ServiceInfo |Informazioni sul provisioning specifiche del servizio che possono essere utilizzate da MOAC e/o da altre istanze del servizio di questo tipo o di altri tipi. |
| TaskSetScopeReference |Identifica un valore TaskSet e un set di ambiti associati a un valore Role o RoleTemplate. |
| ValidationError |Informazioni pubblicate da un servizio federato che descrivono un errore non temporaneo, specifico del servizio relativo alle proprietà o al collegamento da un'azione di amministratore oggetti da risolvere. |
| WellKnownObject |Applica un'etichetta a un oggetto directory, designandolo come parte di un set predefinito. |

## <a name="update-role-definition-attributes"></a>Attributi di "Aggiornamento definizione ruolo"
| Attributo | Descrizione |
| --- | --- |
| AssignableScopes |Raccolta di ambiti di autorizzazione a cui è possibile fare riferimento durante l'assegnazione di questo valore RoleDefinition a un'entità di sicurezza. |
| DisplayName |Nome visualizzato per un oggetto. |
| GrantedPermissions |Autorizzazioni concesse da questo valore RoleDefinition. |

## <a name="update-administrative-unit-attributes"></a>Attributi di "Aggiornamento unità amministrativa"
| Attributo | Descrizione |
| --- | --- |
| Descrizione |Questa proprietà viene aggiornata quando si modifica la descrizione di un'unità amministrativa. |
| DisplayName |Questa proprietà viene aggiornata quando si modifica il nome di un'unità amministrativa. |

## <a name="update-company-attributes"></a>Attributi di "Aggiornamento azienda"
| Attributo | Descrizione |
| --- | --- |
| AllowedDataLocation |Località in cui è autorizzato il provisioning degli utenti dell'azienda. |
| AuthorizedServiceInstance |Nomi delle istanze del servizio in cui è possibile distribuire un piano. |
| DirSyncEnabled |Indica se la sincronizzazione viene eseguita da una directory autorevole (cliente, locale). |
| DirSyncStatus |Indica se la sincronizzazione degli oggetti Rubrica in questo contesto tenant viene eseguita da una directory autorevole (cliente, locale). Espansione della proprietà DirSyncEnabled negli oggetti aziendali. |
| DirSyncFeatures |Flag di bit per tenere traccia di un set di funzionalità dirsync abilitate e disabilitate per il tenant. |
| DirectoryFeatures |Funzionalità della directory abilitate/disabilitate. |
| DirSyncConfiguration |Contiene tutta la configurazione di DirSync specifica per il tenant corrente. |
| DisplayName |Nome visualizzato per un oggetto. |
| IsMnc |Flag booleano impostato su "True" se l'azienda è abilitata per la funzionalità aziendale multinazionale. |
| ObjectSettings |Raccolta di impostazioni applicabili all'ambito dell'oggetto. |
| PartnerCommerceUrl |URL per il sito di e-commerce del partner. |
| PartnerHelpUrl |URL per il sito del supporto tecnico del partner. |
| PartnerSupportEmail |URL dell'indirizzo di posta elettronica del supporto tecnico del partner. |
| PartnerSupportTelephone |URL del telefono del supporto tecnico del partner. |
| PartnerSupportUrl |URL del sito del supporto tecnico del partner. |
| StrongAuthenticationDetails |Dettagli relativi a StrongAuthentication. |
| StrongAuthenticationPolicy |Criteri di autenticazione avanzata per l'azienda. |
| TechnicalNotificationMail |Indirizzo di posta elettronica per la notifica di problemi tecnici relativi a un'azienda. |
| TelephoneNumber |Numeri telefonici compatibili con la norma ITU E.123. |
| TenantType |Tipo di tenant. Se questo valore non è specificato, il tenant è un'azienda. In caso contrario, i valori possibili sono MicrosoftSupport (0), SyndicatePartner (1), BreadthPartner (2) BreadthPartnerDelegatedAdmin (3) ResellerPartnerDelegatedAdmin (4) ValueAddedResellerPartnerDelegatedAdmin (5). |
| VerifiedDomain |Set di nomi di dominio DNS associati a un'azienda. |

## <a name="update-domain-attributes"></a>Attributi di "Aggiornamento dominio"
| Attributo | Descrizione |
| --- | --- |
| Capabilities |Flag di bit che descrivono le funzionalità del dominio, se presenti. |
| Default |Indica se il dominio è il valore predefinito. Ad esempio, il suffisso UserPrincipalName predefinito quando un amministratore crea un nuovo utente in MOAC. |
| Initial |Indica se il dominio è il dominio iniziale per l'azienda, in base a quanto allocato da OCP. Il dominio iniziale è un sottodominio univoco di un dominio Microsoft Online, ad esempio contoso3.microsoftonline.com. |
| LiveType |Tipo dello spazio dei nomi Windows Live corrispondenti, se presenti. |
| Nome |Identificatore per l'endpoint. |
| PasswordNotificationWindowDays |Numero di giorni prima della scadenza della password dopo i quali viene inviata una notifica all'utente. |
| PasswordValidityPeriodDays |Numero di giorni di validità di una password prima che sia necessario modificarla. |

I record di controllo rappresentano un controllo obbligatorio per molte normative per la conformità. Per i clienti che usano i report di controllo di Azure Active Directory per garantire la conformità alle normative, è consigliabile inviare una copia di questo argomento della guida insieme al report di controllo esportato del cliente per fornire una descrizione delle informazioni contenute nel report. Per altre informazioni sulle normative di conformità attualmente soddisfatte da Azure, suggerire al revisore di visitare la pagina relativa alla [conformità](https://azure.microsoft.com/support/trust-center/compliance/) del Centro protezione Microsoft Azure.




<!--HONumber=Feb17_HO2-->


