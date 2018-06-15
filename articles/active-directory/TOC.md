# [Documentazione di Azure Active Directory](index.md)

# Panoramica
## [Informazioni su Azure Active Directory](active-directory-whatis.md)
## [Informazioni sulla Gestione delle identità di Azure](fundamentals/identity-fundamentals.md)
## [Informazioni sulle soluzioni di gestione delle identità di Azure](fundamentals/understand-azure-identity-solutions.md)
## [Scegliere una soluzione ibrida di gestione delle identità](choose-hybrid-identity-solution.md)
## [Associate Azure subscriptions](active-directory-how-subscriptions-associated-directory.md) (Associare le sottoscrizioni di Azure)
## [Considerazioni sulla residenza e sui dati](fundamentals/active-directory-data-storage-eu.md)
## [Domande frequenti](fundamentals/active-directory-faq.md)
## [Novità](fundamentals/whats-new.md)


# Attività iniziali
## [Inizia a usare Azure AD](get-started-azure-ad.md)
## [Iscriversi ad Azure AD Premium](active-directory-get-started-premium.md)
## [Aggiungere un nome di dominio personalizzato](add-custom-domain.md)
## [Configurare la personalizzazione aziendale](customize-branding.md)
## [Aggiungere utenti ad Azure AD](add-users-azure-active-directory.md)
## [Assegna licenze agli utenti](fundamentals/license-users-groups.md)
## [Configurare la reimpostazione self-service delle password](authentication/quickstart-sspr.md)
## [Aggiungere le informazioni sulla privacy dell'organizzazione in Azure AD](active-directory-properties-area.md)


# Procedure
## Pianificare e progettare
### [Informazioni sull'architettura di Azure AD](fundamentals/active-directory-architecture.md)
### [Mapping di attestazioni in Azure Active Directory](active-directory-claims-mapping.md)
### [Distribuire una soluzione ibrida di gestione delle identità](active-directory-hybrid-identity-design-considerations-overview.md)
#### Determinare i requisiti
##### [Identità](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Directory sync](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md) (Sincronizzazione della directory)
##### [Multi-Factor Authentication](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Strategia del ciclo di vita delle identità](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Pianificare la sicurezza dei dati](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Protezione dei dati](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Gestione dei contenuti](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Controllo di accesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Risposta agli eventi imprevisti](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Pianificare il ciclo di vita delle identità
##### [Attività](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Strategia di adozione](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Passaggi successivi](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Confronto fra strumenti](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Gestire gli utenti
### [Aggiungere nuovi utenti ad Azure AD](add-users-azure-active-directory.md)
### [Gestire i profili utente](active-directory-users-profile-azure-portal.md)
### [Condividere gli account](active-directory-sharing-accounts.md)
### [Assegnare utenti a ruoli amministrativi](active-directory-users-assign-role-azure-portal.md)
### [Ripristinare un utente eliminato](active-directory-users-restore.md)
### [Aggiungere utenti guest da un'altra directory (B2B)](b2b/what-is-b2b.md)
#### [Aggiunta di utenti B2B da parte di amministratori](b2b/add-users-administrator.md)
#### [Aggiunta di utenti B2B da parte di information worker](b2b/add-users-information-worker.md)
#### [API e personalizzazione](b2b/customize-invitation-api.md)
#### [Esempi di Azure PowerShell e di codice](b2b/code-samples.md)
#### [Esempio di portale per l'iscrizione self-service](b2b/self-service-portal.md)
#### [Invito tramite posta elettronica](b2b/invitation-email-elements.md)
#### [Riscatto di invito](b2b/redemption-experience.md)
#### [Aggiungere utenti B2B senza un invito](b2b/add-user-without-invite.md)
#### [Consentire o bloccare gli inviti](b2b/allow-deny-list.md)
#### [Accesso condizionale per B2B](b2b/conditional-access.md)
#### [Criteri di condivisione per B2B](b2b/delegate-invitations.md)
#### [Aggiungere un utente B2B a un ruolo](b2b/add-guest-to-role.md)
#### [Gruppi dinamici e utenti B2B](b2b/use-dynamic-groups.md)
#### [Uscire da un'organizzazione](b2b/leave-the-organization.md)
#### [Controllo e report](b2b/auditing-and-reporting.md)
#### [B2B per organizzazioni ibride](b2b/hybrid-organizations.md)
##### [Concedere l'accesso alle app locali agli utenti di B2B](b2b/hybrid-cloud-to-on-premises.md)
##### [Concedere l'accesso alle app cloud agli utenti locali](b2b/hybrid-on-premises-to-cloud.md)
#### [Condivisione esterna di Office 365 e B2B](b2b/o365-external-user.md)
#### [Licenze B2B](b2b/licensing-guidance.md)
#### [Limitazioni correnti](b2b/current-limitations.md)
#### [DOMANDE FREQUENTI](b2b/faq.md)
#### [Risoluzione dei problemi B2B](b2b/troubleshoot.md)
#### [Informazioni sull'utente B2B](b2b/user-properties.md)
#### [Token utente B2B](b2b/user-token.md)
#### [B2B per app integrate con Azure AD](b2b/configure-saas-apps.md)
#### [Mapping delle attestazioni utente B2B](b2b/claims-mapping.md)
#### [Confrontare Collaborazione B2B con B2C](b2b/compare-with-b2c.md)
#### [Ottenere assistenza per B2B](b2b/get-support.md)

## [Gestire i gruppi e i membri](active-directory-manage-groups.md)
### Gestire i gruppi
#### [Portale di Azure](active-directory-groups-create-azure-portal.md)
#### [Azure AD PowerShell for Graph (v2)](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
#### [Azure AD PowerShell MSOnline](active-directory-accessmanagement-groups-settings-cmdlets.md)
### [Gestire i membri dei gruppi](active-directory-groups-members-azure-portal.md)
### [Gestire i proprietari dei gruppi](active-directory-accessmanagement-managing-group-owners.md)
### [Gestire l'appartenenza ai gruppi](fundamentals/active-directory-groups-membership-azure-portal.md)
### [Assegnazione di licenze mediante i gruppi](active-directory-licensing-whatis-azure-portal.md)
#### [Assegnare licenze a un gruppo](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identificare e risolvere i problemi relativi alle licenze in un gruppo](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Eseguire la migrazione di singoli utenti con licenza alle licenze basate sui gruppi](active-directory-licensing-group-migration-azure-portal.md)
#### [Eseguire la migrazione degli utenti tra le licenze dei prodotti](active-directory-licensing-group-product-migration.md)
#### [Scenari aggiuntivi per le licenze basate sui gruppi](active-directory-licensing-group-advanced.md)
#### [Esempi di Azure PowerShell per le licenze basate sui gruppi](active-directory-licensing-ps-examples.md)
#### [Informazioni di riferimento per i prodotti e i piani di servizio in Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Configurare la scadenza dei gruppi di Office 365](active-directory-groups-lifecycle-azure-portal.md)
### [Applicare un criterio di denominazione per i gruppi](groups-naming-policy.md)
### [View all groups](active-directory-groups-view-azure-portal.md) (Visualizzare tutti i gruppi)
### [Aggiungere l'accesso dei gruppi alle app SaaS](active-directory-accessmanagement-group-saasapps.md)
### [Ripristinare un gruppo eliminato di Office 365](fundamentals/active-directory-groups-restore-azure-portal.md)
### [Gestire le impostazioni dei gruppi](active-directory-groups-settings-azure-portal.md) 
### Creare regole avanzate
#### [Portale di Azure](active-directory-groups-dynamic-membership-azure-portal.md)
### [Configurare i gruppi self-service](active-directory-accessmanagement-self-service-group-management.md)
### [Risolvere i problemi](active-directory-accessmanagement-troubleshooting.md)

## [Gestire i report](active-directory-reporting-azure-portal.md)
### [Attività di accesso](active-directory-reporting-activity-sign-ins.md)
### [Attività di controllo](active-directory-reporting-activity-audit-logs.md)
### [Utenti a rischio](active-directory-reporting-security-user-at-risk.md)
### [Accessi a rischio](active-directory-reporting-security-risky-sign-ins.md)
### [Eventi di rischio](active-directory-reporting-risk-events.md)
### [DOMANDE FREQUENTI](active-directory-reporting-faq.md)
### Attività
#### [Configurare località denominate](active-directory-named-locations.md)
#### [Trovare report sull'attività](active-directory-reporting-migration.md)
#### [Usare il pacchetto di contenuto Power BI di Azure Active Directory](active-directory-reporting-power-bi-content-pack-how-to.md)
#### [Correggere gli utenti contrassegnati per il rischio](active-directory-report-security-user-at-risk-remediation.md)
### Riferimenti
#### [Conservazione](active-directory-reporting-retention.md)
#### [Latenze](active-directory-reporting-latencies-azure-portal.md)
#### [Notifications](active-directory-reporting-notifications.md)
#### [Informazioni di riferimento sulle attività di controllo](active-directory-reporting-activity-audit-reference.md)
#### [Codici errore relativi alle attività di accesso](active-directory-reporting-activity-sign-ins-errors.md)
#### [Autenticazione a più fattori](active-directory-reporting-activity-sign-ins-mfa.md)



### Risolvere problemi
#### [Dati di controllo mancanti](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Dati mancanti nei download](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Errori dei pacchetti di contenuto dei log attività di Azure Active Directory](active-directory-reporting-troubleshoot-content-pack.md)
### [Accesso a livello di codice](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Prerequisiti](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Esempi di controllo](active-directory-reporting-api-audit-samples.md)
#### [Esempi di accesso](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Uso dei certificati](active-directory-reporting-api-with-certificates.md)

## Gestire le password
### [Panoramica delle password](authentication/active-directory-passwords-overview.md)
### Documenti degli utenti
#### [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md)
#### [Procedure consigliate per le password](active-directory-secure-passwords.md)
#### [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md)
### [Funzionamento di SSPR](authentication/concept-sspr-howitworks.md)
### [Guida alla distribuzione di SSPR](authentication/howto-sspr-deployment.md)
### [SSPR e Windows 10](authentication/tutorial-sspr-windows.md)
### [Criteri di SSPR](authentication/concept-sspr-policy.md)
### [Personalizzazione di SSPR](authentication/concept-sspr-customization.md)
### [Requisiti relativi ai dati di SSPR](authentication/howto-sspr-authenticationdata.md)
### [Creazione di report di SSPR](authentication/howto-sspr-reporting.md)
### Amministratori IT: reimpostare le password
#### [Portale di Azure](active-directory-users-reset-password-azure-portal.md)
### [Concedere in licenza SSPR](authentication/concept-sspr-licensing.md)
### [Writeback delle password](authentication/howto-sspr-writeback.md)
### [Risolvere i problemi](authentication/active-directory-passwords-troubleshoot.md)
### [DOMANDE FREQUENTI](authentication/active-directory-passwords-faq.md)


## Gestire i dispositivi
### [Introduzione](device-management-introduction.md)
### [Uso del portale di Azure](device-management-azure-portal.md)
### [Pianificare l'aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [Domande frequenti](device-management-faq.md)
### Attività
#### [Configurare i dispositivi Windows 10 registrati in Azure AD](device-management-azuread-registered-devices-windows10-setup.md)
#### [Configurare i dispositivi aggiunti ad Azure AD](device-management-azuread-joined-devices-setup.md)
#### [Configurare i dispositivi aggiunti all'istanza ibrida di Azure AD](device-management-hybrid-azuread-joined-devices-setup.md)
#### [Deploy on-premises](active-directory-device-registration-on-premises-setup.md) (Distribuire in locale)
#### [Aggiunta ad Azure AD durante il completamento dell'installazione in Windows 10](device-management-azuread-joined-devices-frx.md)
### Risolvere problemi
#### [Dispositivi Windows 10 e Windows Server 2016 aggiunti ad Azure AD ibridi](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [Dispositivi Windows legacy aggiunti ad Azure AD ibridi](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## Gestire le app
### [Overview](manage-apps/what-is-application-management.md)
### [Introduzione](manage-apps/plan-an-application-integration.md)
### [Esercitazioni sull'integrazione di app SaaS](active-directory-saas-tutorial-list.md)
### [Cloud App Discovery](manage-apps/cloud-app-discovery.md)
#### [Creare report di snapshot](manage-apps/cloud-app-discovery-create-snapshot-reports.md)
#### [Configurare la creazione di report continua](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [Usare un parser di log personalizzato](https://docs.microsoft.com/cloud-app-security/custom-log-parser)

### [Provisioning e deprovisioning di utenti nelle app SaaS](active-directory-saas-app-provisioning.md) 
#### [Esercitazioni sull'integrazione di app](active-directory-saas-tutorial-list.md) 
#### [Automatizzare il provisioning nelle app abilitate per SCIM](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Personalizzare i mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md) 
#### [Scrivere espressioni per i mapping degli attributi](active-directory-saas-writing-expressions-for-attribute-mappings.md) 
#### [Usare i filtri per la definizione dell'ambito](active-directory-saas-scoping-filters.md) 
#### [Creare report sul provisioning utenti automatico](active-directory-saas-provisioning-reporting.md) 
#### [Risolvere i problemi del provisioning utenti](active-directory-application-provisioning-content-map.md) 

### [Accedere alle app in remoto con il proxy delle app](manage-apps/application-proxy.md)
#### Attività iniziali
##### [Abilitare il proxy delle app](manage-apps/application-proxy-enable.md)
##### [Pubblicare le app](manage-apps/application-proxy-publish-azure-portal.md)
##### [Domini personalizzati](manage-apps/application-proxy-configure-custom-domain.md)
#### [Single Sign-On](manage-apps/application-proxy-single-sign-on.md)
##### [SSO con la delega vincolata Kerberos](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [SSO con le intestazioni](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [SSO con l'insieme di credenziali delle password](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### Concetti
##### [Connettori](manage-apps/application-proxy-connectors.md)
##### [Sicurezza](manage-apps/application-proxy-security.md)
##### [Reti](manage-apps/application-proxy-network-topology.md)


##### [Aggiornamento da TMG o UAG](manage-apps/application-proxy-migration.md)

#### Configurazioni avanzate
##### [Pubblicare su reti distinte](manage-apps/application-proxy-connector-groups.md)
##### [Server proxy](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [App in grado di riconoscere attestazioni](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [App client native](manage-apps/application-proxy-configure-native-client-application.md)
##### [Installazione invisibile all'utente](manage-apps/application-proxy-register-connector-powershell.md)
##### [Home page personalizzata](manage-apps/application-proxy-configure-custom-home-page.md)
##### [Tradurre i collegamenti inline](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
##### [Caratteri jolly](active-directory-application-proxy-wildcard.md)
##### [Rimuovere i dati personali](manage-apps/application-proxy-remove-personal-data.md)


#### Procedure dettagliate di pubblicazione
##### [Desktop remoto](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](active-directory-application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Risolvere i problemi](manage-apps/application-proxy-troubleshoot.md)

### Gestire le app aziendali
#### [Assegnare utenti](manage-apps/assign-user-or-group-access-portal.md)
#### [Modificare la personalizzare](manage-apps/change-name-or-logo-portal.md)
#### [Disabilitare l'accesso degli utenti](manage-apps/disable-user-sign-in-portal.md)
#### [Rimuovere utenti](manage-apps/remove-user-or-group-access-portal.md)
#### [Visualizzare tutte le app personali](manage-apps/view-applications-portal.md)
#### [Gestire il provisioning degli account utente](manage-apps/configure-automatic-user-provisioning-portal.md)
#### [Gestire l'accesso Single Sign-On per le app aziendali](manage-apps/configure-single-sign-on-portal.md)
#### [Firma di certificati avanzata per app SAML](manage-apps/certificate-signing-options.md)
#### [Nascondere un'applicazione dall'esperienza di un utente](manage-apps/hide-application-from-user-portal.md)
### [Configurare l'accelerazione automatica dell'accesso tramite i criteri HRD](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Eseguire la migrazione di app di AD FS in Azure AD](migrate-adfs-apps-to-azure.md) 
### [Gestire l'accesso alle app](manage-apps/what-is-access-management.md)
#### [Accesso SSO](manage-apps/what-is-single-sign-on.md)
#### [Certificati per SSO](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Restrizioni relative ai tenant](manage-apps/tenant-restrictions.md)
#### [Usare SCIM per il provisioning degli utenti](manage-apps/use-scim-to-provision-users-and-groups.md)

### [Risolvere i problemi](active-directory-application-troubleshoot-content-map.md)
#### [Sviluppo di applicazioni](active-directory-application-dev-troubleshoot-content-map.md)
##### [Configurazione e registrazione](active-directory-application-dev-config-content-map.md)
##### [Sviluppo](active-directory-application-dev-development-content-map.md)
#### [Gestione di applicazioni](active-directory-application-management-troubleshoot-content-map.md)
##### [Configurazione](active-directory-application-config-content-map.md)
##### [Accesso](active-directory-application-sign-in-content-map.md)
##### [Provisioning](active-directory-application-provisioning-content-map.md)

###### [Verifica del provisioning di un utente](application-provisioning-when-will-provisioning-finish-specific-user.md) 
###### [Tempi di provisioning eccessivi](application-provisioning-when-will-provisioning-finish.md) 
###### [Come configurare il provisioning utenti](application-provisioning-config-how-to.md) 
###### [Problema con la configurazione del provisioning](application-provisioning-config-problem.md) 
###### [Problema con il salvataggio delle credenziali di amministratore](application-provisioning-config-problem-storage-limit.md) 
###### [Nessun utente è sottoposto a provisioning](application-provisioning-config-problem-no-users-provisioned.md) 
###### [Provisioning di utenti non corretti](application-provisioning-config-problem-wrong-users-provisioned.md) 

##### [Gestione dell'accesso](active-directory-application-access-content-map.md)
##### [Pannello di accesso](active-directory-application-access-panel-content-map.md)
##### [Proxy dell'applicazione](active-directory-application-proxy-content-map.md)
##### [Accesso condizionale](active-directory-application-conditional-access-content-map.md)
### [Sviluppare app](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Raccolta documenti](active-directory-apps-index.md)

## Gestire la directory
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Nomi di dominio personalizzati
#### [Guida introduttiva](add-custom-domain.md)
#### [Aggiungere nomi di dominio personalizzati](active-directory-domains-manage-azure-portal.md)
### [Amministrare la directory](fundamentals/active-directory-administer.md)
### [Eliminare una directory](directory-delete-howto.md)
### [Multiple directories](active-directory-licensing-directory-independence.md) (Diverse directory)
### [Iscrizione self-service](active-directory-self-service-signup.md)
### [Acquisire la proprietà di una directory non gestita](domains-admin-takeover.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Abilitazione](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Impostazioni di Criteri di gruppo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Impostazioni di Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Domande frequenti](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Risolvere i problemi](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Integrare le identità locali usando Azure AD Connect](./connect/active-directory-aadconnect.md)

## [Gestire l'accesso ad Azure](../role-based-access-control/toc.yml)

## Delegare l'accesso alle risorse
### [Ruoli dell'amministratore](active-directory-assign-admin-roles-azure-portal.md)
#### [Assegnare un ruolo di amministratore a un utente](active-directory-users-assign-role-azure-portal.md) 
#### [Confrontare le autorizzazioni dei membri e degli utenti guest](fundamentals/users-default-permissions.md) 
### [Protezione dell'accesso con privilegi](admin-roles-best-practices.md)  
### [Creare account amministrativi di accesso di emergenza](active-directory-admin-manage-emergency-access-accounts.md) 


#### [Autorizzazioni utente predefinite](fundamentals/users-default-permissions.md)
### [Unità amministrative](active-directory-administrative-units-management.md)
### [Configure token lifetimes](active-directory-configurable-token-lifetimes.md) (Configurare la durata dei token)
### [Protezione dei ruoli con privilegi](admin-roles-best-practices.md)

## Verifiche di accesso
### [Panoramica delle verifiche di accesso](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Completare una verifica di accesso](active-directory-azure-ad-controls-complete-access-review.md)
### [Creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md)
### [Come eseguire una verifica dell'accesso](active-directory-azure-ad-controls-perform-access-review.md)
### [Come verificare l'accesso](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Accesso guest con verifiche di accesso](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Gestione dell'accesso utente con verifiche](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Gestione di programmi e controlli](active-directory-azure-ad-controls-manage-programs-controls.md)
### [Recuperare i risultati della verifica di accesso](active-directory-azure-ad-controls-retrieve-access-review.md)

## Proteggere le identità
### [Accesso condizionale](active-directory-conditional-access-azure-portal.md)
#### [Introduzione](active-directory-conditional-access-azure-portal-get-started.md)
#### Guide introduttive
##### [Configurare l'autenticazione a più fattori per singole app cloud](active-directory-conditional-access-app-based-mfa.md)
#### Esercitazioni
##### [Eseguire la migrazione di criteri di MFA classici](active-directory-conditional-access-migration-mfa.md)
#### Concetti
##### [Condizioni](active-directory-conditional-access-conditions.md)
##### [Condizioni delle posizioni](active-directory-conditional-access-locations.md)
##### [Controlli](active-directory-conditional-access-controls.md)
##### [Strumento What if](active-directory-conditional-access-whatif.md)
##### [Informazioni sui criteri relativi ai dispositivi per i servizi di Office 365](active-directory-conditional-access-device-policies.md)
#### Guide alle procedure
##### [Procedure consigliate](active-directory-conditional-access-best-practices.md)
##### [Configurare criteri di accesso condizionale per i tentativi di accesso da reti non attendibili](active-directory-conditional-access-untrusted-networks.md)
##### [Configurare l'accesso condizionale basato su dispositivo](active-directory-conditional-access-policy-connected-applications.md)
##### [Configurare l'accesso condizionale basato su app](active-directory-conditional-access-mam.md)
##### [Specificare le condizioni per l'utilizzo per utenti e app](active-directory-tou.md)
##### [Eseguire la migrazione di criteri classici](active-directory-conditional-access-migration.md)
##### [Configurare la connettività VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/always-on-vpn-deploy)
##### [Configurare SharePoint ed Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Correzione](active-directory-conditional-access-device-remediation.md)
#### [Riferimento tecnico](active-directory-conditional-access-technical-reference.md)
#### [Domande frequenti](active-directory-conditional-faqs.md)

### Autenticazione basata su certificati
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Introduzione](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Abilitazione](active-directory-identityprotection-enable.md)
#### [Rilevare le vulnerabilità](active-directory-identityprotection-vulnerabilities.md)
#### [Eventi di rischio](active-directory-identity-protection-risk-events.md)
#### [Notifications](active-directory-identityprotection-notifications.md)
#### [Esperienza d'accesso](active-directory-identityprotection-flows.md)
#### [Simulare eventi di rischio](active-directory-identityprotection-playbook.md)
#### [Sbloccare utenti](active-directory-identityprotection-unblock-howto.md)
#### [Domande frequenti](active-directory-identity-protection-faqs.md)
#### [Glossario](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

## Integrare altri servizi con Azure AD 
### [Integrare LinkedIn con Azure AD](linkedin-integration.md)

## [Distribuire AD FS in Azure](active-directory-aadconnect-azure-adfs.md)
### [Disponibilità elevata](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Modificare l'algoritmo hash della firma](active-directory-federation-sha256-guidance.md)

## [Risolvere i problemi](active-directory-troubleshooting-support-howto.md)

## Distribuire il modello di verifica (PoC) di Azure AD
### [Studio del modello di verifica: introduzione](active-directory-playbook-intro.md)
### [Studio del modello di verifica: ingredienti](active-directory-playbook-ingredients.md)
### [Studio del modello di verifica: implementazione](active-directory-playbook-implementation.md)
### [Studio del modello di verifica: blocchi predefiniti](active-directory-playbook-building-blocks.md)


# Riferimenti
## [Esempi di codice](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Cmdlet di Azure PowerShell](/powershell/azure/overview)
## [Informazioni di riferimento sulle API Java](/java/api)
## [API .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Limiti e restrizioni del servizio](active-directory-service-limits-restrictions.md)

# Risorse correlate
## [Autenticazione a più fattori](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD per sviluppatori](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# Risorse
## [Forum di commenti e suggerimenti su Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Prezzi](https://azure.microsoft.com/pricing/details/active-directory/)
## [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
