# Panoramica
## [Informazioni su Azure Active Directory](active-directory-whatis.md)
## [Scegliere un'edizione](active-directory-editions.md)
## [Informazioni sulla Gestione delle identità di Azure](fundamentals-identity.md)
## [Visualizzare in anteprima l'esperienza del portale di Azure AD](active-directory-preview-explainer.md)


# Introduzione
## [Come ottenere un tenant di Azure Active Directory](active-directory-howto-tenant.md)
## [Iscriversi ad Azure AD Premium](active-directory-get-started-premium.md)
## [Associate Azure subscriptions](active-directory-how-subscriptions-associated-directory.md) (Associare le sottoscrizioni di Azure)
## Gestire le licenze di Azure AD
### [Portale di Azure](active-directory-licensing-get-started-azure-portal.md)
### [Portale classico](active-directory-licensing-what-is.md)
## [Ottenere Azure per l'organizzazione](sign-up-organization.md)
## [Domande frequenti](active-directory-faq.md)
## [Esercitazioni su app SaaS](active-directory-saas-tutorial-list.md)

# Procedure
## Pianificare e progettare
### [Distribuire una soluzione ibrida di gestione delle identità](active-directory-hybrid-identity-design-considerations-overview.md)
#### Determinare i requisiti
##### [Identità](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Directory sync](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md) (Sincronizzazione della directory)
##### [Multi-Factor Authentication](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Identity lifecycle strategy](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) (Strategia del ciclo di vita delle identità)
#### [Pianificare la sicurezza dei dati](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Protezione dei dati](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Gestione dei contenuti](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Controllo di accesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Risposta agli eventi imprevisti](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Pianificare il ciclo di vita delle identità
##### [Attività](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Strategia di adozione](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Passaggi successivi](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Tools comparison](active-directory-hybrid-identity-design-considerations-tools-comparison.md) (Confronto fra strumenti)

## Gestire gli utenti
### Aggiungi utenti
#### [Portale di Azure](active-directory-users-create-azure-portal.md)
#### [Portale classico](active-directory-create-users.md)

### [Aggiungere utenti da altre directory (portale classico)](active-directory-create-users-external.md)
### [Eliminare utenti](active-directory-users-delete-user-azure-portal.md)
### [Gestire i profili utente](active-directory-users-profile-azure-portal.md)
### [Reset a password](active-directory-users-reset-password-azure-portal.md) (Reimpostare una password)
### [Manage user work information](active-directory-users-work-info-azure-portal.md) (Gestire le informazioni di lavoro di un utente)
### [Condividere gli account](active-directory-sharing-accounts.md)

## [Gestire i gruppi e i membri](active-directory-manage-groups.md)
### Gestire i gruppi
#### [portale di Azure](active-directory-groups-create-azure-portal.md)
#### [Portale classico](active-directory-accessmanagement-manage-groups.md)
#### [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [Manage group members](active-directory-groups-members-azure-portal.md) (Gestire i membri dei gruppi)
### [Gestire i proprietari dei gruppi](active-directory-accessmanagement-managing-group-owners.md)
### [Manage group membership](active-directory-groups-membership-azure-portal.md) (Gestire l'appartenenza ai gruppi)
### [View all groups](active-directory-groups-view-azure-portal.md) (Visualizzare tutti i gruppi)
### [Abilitare i gruppi dedicati](active-directory-accessmanagement-dedicated-groups.md)
### [Aggiungere l'accesso dei gruppi alle app SaaS](active-directory-accessmanagement-group-saasapps.md)
### Gestire le impostazioni dei gruppi
#### [portale di Azure](active-directory-groups-settings-azure-portal.md)
#### [Cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
### Creare regole avanzate
#### [portale di Azure](active-directory-groups-dynamic-membership-azure-portal.md)
#### [Portale classico](active-directory-accessmanagement-groups-with-advanced-rules.md)
### [Licenze basate sui gruppi](active-directory-licensing-whatis-azure-portal.md)
#### [Assegnare licenze a un gruppo](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identificare e risolvere i problemi relativi alle licenze per un gruppo](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Eseguire la migrazione di singoli utenti con licenza alle licenze basate sui gruppi](active-directory-licensing-group-migration-azure-portal.md)
#### [Scenari aggiuntivi per le licenze basate sui gruppi](active-directory-licensing-group-advanced.md)
### [Configurare i gruppi self-service](active-directory-accessmanagement-self-service-group-management.md)
### [Risolvere i problemi](active-directory-accessmanagement-troubleshooting.md)

## [Gestire i report](active-directory-reporting-azure-portal.md)
### [Attività di accesso](active-directory-reporting-activity-sign-ins.md)
### [Attività di controllo](active-directory-reporting-activity-audit-logs.md)
### [Utenti a rischio](active-directory-reporting-security-user-at-risk.md)
### [Accessi a rischio](active-directory-reporting-security-risky-sign-ins.md)
### [Eventi di rischio](active-directory-reporting-risk-events.md)
### [Reti denominate](active-directory-known-networks-azure-portal.md)
### [Migrazione dei report](active-directory-reporting-migration.md)
### [Conservazione](active-directory-reporting-retention.md)
### [Latenze](active-directory-reporting-latencies-azure-portal.md)
### [Domande frequenti](active-directory-reporting-faq.md)
### Risoluzione dei problemi
#### [Dati di controllo mancanti](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Dati mancanti nei download](active-directory-reporting-troubleshoot-missing-data-download.md)
###    Accesso a livello di codice
#### [Informazioni di riferimento sul controllo](active-directory-reporting-api-audit-reference.md)
#### [Esempi di controllo](active-directory-reporting-api-audit-samples.md)
#### [Prerequisiti](active-directory-reporting-api-prerequisites.md)
#### [Sign-in reference](active-directory-reporting-api-sign-in-activity-reference.md) (Informazioni di riferimento sull'accesso)
#### [Sign-in samples](active-directory-reporting-api-sign-in-activity-samples.md) (Esempi di accesso)
### [Portale classico](active-directory-view-access-usage-reports.md)
#### [Creazione di report di Azure AD](active-directory-reporting-getting-started.md)
#### [Guida alla creazione di report](active-directory-reporting-guide.md)
#### [Reti note](active-directory-known-networks.md)
#### [API](active-directory-reporting-api-getting-started.md)
#### [Eventi di controllo](active-directory-reporting-audit-events.md)
#### [Latenze](active-directory-reporting-latencies.md)
#### [Notifications](active-directory-reporting-notifications.md)
#### Informazioni sui report
##### [Accesso irregolare](active-directory-reporting-irregular-sign-in-activity.md)
##### [Multiple failures](active-directory-reporting-sign-ins-after-multiple-failures.md) (Diversi errori)
##### [Suspicious IP addresses](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md) (Indirizzi IP sospetti)
##### [Multiple geographies](active-directory-reporting-sign-ins-from-multiple-geographies.md) (Diverse aree geografiche)
##### [Possibly infected devices](active-directory-reporting-sign-ins-from-possibly-infected-devices.md) (Dispositivi potenzialmente infetti)
##### [Unknown sources](active-directory-reporting-sign-ins-from-unknown-sources.md) (Origini sconosciute)
##### [Anomalous sign-ins](active-directory-reporting-users-with-anomalous-sign-in-activity.md) (Accessi con anomalie)

## [Gestire le password](active-directory-manage-passwords.md)
### [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md)
### [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md)
### [Reimpostazione della password self-service abilitata da amministratori](active-directory-passwords.md)
### [Informazioni sulla gestione delle password](active-directory-passwords-how-it-works.md)
### [Informazioni sui criteri e sulle restrizioni](active-directory-passwords-policy.md)
### Reimpostare le password
#### [portale di Azure](active-directory-users-reset-password-azure-portal.md)
#### [Portale classico](active-directory-create-users-reset-password.md)
### [Configurare i criteri di scadenza](active-directory-passwords-set-expiration-policy.md)
### Abilitare la gestione delle password
#### [Attività iniziali](active-directory-passwords-getting-started.md)
#### [Distribuire](active-directory-passwords-best-practices.md)
#### [Personalizzare](active-directory-passwords-customize.md)
#### [Visualizzare i report](active-directory-passwords-get-insights.md)
#### [Altre informazioni](active-directory-passwords-learn-more.md)
#### [Domande frequenti](active-directory-passwords-faq.md)
#### [Risoluzione dei problemi](active-directory-passwords-troubleshoot.md)

## Gestire i dispositivi
### [Registrare i dispositivi](active-directory-device-registration-overview.md)
#### [Configurazione](active-directory-conditional-access-automatic-device-registration-setup.md)
#### [Deploy on-premises](active-directory-device-registration-on-premises-setup.md) (Distribuire in locale)
#### [Domande frequenti](active-directory-device-registration-faq.md)
#### Risoluzione dei problemi
##### [Risoluzione dei problemi per Windows 10 e Windows Server 2016](active-directory-device-registration-troubleshoot-windows.md)
##### [Risoluzione dei problemi per i client Windows di livello inferiore](active-directory-device-registration-troubleshoot-windows-legacy.md)
### [Aggiunta ad Azure AD](active-directory-azureadjoin-overview.md)
#### [Pianificare](active-directory-azureadjoin-deployment-aadjoindirect.md)
#### [Configurare la registrazione dei dispositivi](active-directory-azureadjoin-setup.md)
#### [Register new devices](active-directory-azureadjoin-user-frx.md) (Registrare nuovi dispositivi)
#### [Distribuire](active-directory-azureadjoin-devices-group-policy.md)
#### [Informazioni sull'integrazione con Windows 10](active-directory-azureadjoin-windows10-devices-overview.md)
#### [Usare dispositivi Windows 10](active-directory-azureadjoin-windows10-devices.md)
#### [Aggiungere il dispositivo](active-directory-azureadjoin-personal-device.md)
#### [Aggiungere un dispositivo Windows 10](active-directory-azureadjoin-user-upgrade.md)

## Gestire le app
### [Panoramica](active-directory-enable-sso-scenario.md)
### [Attività iniziali](active-directory-integrating-applications-getting-started.md)

### [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
#### [Aggiornare le impostazioni del Registro di sistema](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
#### [Informazioni sulla sicurezza e sulla privacy](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)

### [Concedere l'accesso remoto alle app](active-directory-application-proxy-get-started.md)
#### [Enable App Proxy](active-directory-application-proxy-enable.md) (Abilitare il proxy delle app)
#### [Informazioni sui connettori](application-proxy-understand-connectors.md)
#### [Publish apps](application-proxy-publish-azure-portal.md) (Pubblicare le app)
#### [Sicurezza](application-proxy-security-considerations.md)
#### [Reti](application-proxy-network-topology-considerations.md)
#### [Desktop remoto](application-proxy-publish-remote-desktop.md)
#### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)
#### [Pubblicare su reti distinte](active-directory-application-proxy-connectors-azure-portal.md)
#### [Server proxy](application-proxy-working-with-proxy-servers.md)
#### [Domini personalizzati](active-directory-application-proxy-custom-domains.md)
#### [Accedere alle app](active-directory-appssoaccess-whatis.md)
##### [Portale di Azure](application-proxy-sso-azure-portal.md)
#### [SSO con la delega vincolata Kerberos](active-directory-application-proxy-sso-using-kcd.md)
#### [SSO con le intestazioni](application-proxy-ping-access.md)
#### [Claims-aware apps](active-directory-application-proxy-claims-aware-apps.md) (App in grado di riconoscere attestazioni)
#### [Native client apps](active-directory-application-proxy-native-client.md) (App client native)
#### [Home page personalizzata](application-proxy-office365-app-launcher.md)
#### [Accesso condizionale](active-directory-application-proxy-conditional-access.md)
#### [Silent install](active-directory-application-proxy-silent-installation.md) (Installazione invisibile all'utente)
#### [Microsoft Forefront](application-proxy-transition-from-uag-tmg.md)
#### [Risolvere i problemi](active-directory-application-proxy-troubleshoot.md)
#### Usare il portale classico
##### [Scaricare i connettori](application-proxy-enable-classic-portal.md)
##### [Publish apps](active-directory-application-proxy-publish.md) (Pubblicare le app)
##### [Usare i connettori](active-directory-application-proxy-connectors.md)


### Gestire le app aziendali
#### [Assegnare utenti](active-directory-coreapps-assign-user-azure-portal.md)
#### [Customize branding](active-directory-coreapps-change-app-logo-user-azure-portal.md) (Modificare la personalizzare)
#### [Disable user sign-ins](active-directory-coreapps-disable-app-azure-portal.md) (Disabilitare l'accesso degli utenti)
#### [Rimuovere utenti](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [View all my apps](active-directory-coreapps-view-azure-portal.md) (Visualizzare tutte le app personali)
#### [Gestire il provisioning degli account utente](active-directory-enterprise-apps-manage-provisioning.md)

### Sviluppare
#### [Assegnare utenti](active-directory-applications-guiding-developers-assigning-users.md)
#### [Assign groups](active-directory-applications-guiding-developers-assigning-groups.md) (Assegnare gruppi)
#### [Require assignment](active-directory-applications-guiding-developers-requiring-user-assignment.md) (Richiedere l'assegnazione)
#### [Sviluppare app line-of-business](active-directory-applications-guiding-developers-for-lob-applications.md)

### [Manage access to apps](active-directory-managing-access-to-apps.md) (Gestire l'accesso alle app)
#### [Self-service access](active-directory-self-service-application-access.md) (Accesso self-service)
#### [Certificates for SSO](active-directory-sso-certs.md) (Certificati per SSO)
#### [Restrizioni relative ai tenant](active-directory-tenant-restrictions.md)
#### [Use SCIM provision users](active-directory-scim-provisioning.md) (Usare SCIM per il provisioning degli utenti)

### [Risolvere i problemi](active-directory-application-troubleshoot-content-map.md)
#### [Sviluppo di applicazioni](active-directory-application-dev-troubleshoot-content-map.md)
##### [Configurazione e registrazione](active-directory-application-dev-config-content-map.md)
##### [Sviluppo](active-directory-application-dev-development-content-map.md)
#### [Gestione di applicazioni](active-directory-application-management-troubleshoot-content-map.md)
##### [Configurazione](active-directory-application-config-content-map.md)
##### [Accesso](active-directory-application-sign-in-content-map.md)
##### [Provisioning](active-directory-application-provisioning-content-map.md)
##### [Gestione dell'accesso](active-directory-application-access-content-map.md)
##### [Pannello di accesso](active-directory-application-access-panel-content-map.md)
##### [Proxy dell'applicazione](active-directory-application-proxy-content-map.md)
##### [Accesso condizionale](active-directory-application-conditional-access-content-map.md)

### [Raccolta documenti](active-directory-apps-index.md)

## Gestire la directory
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Nomi di dominio personalizzati
#### [Panoramica](active-directory-add-domain-concepts.md)
#### Aggiungere il nome di dominio
##### [Portale di Azure](active-directory-domains-add-azure-portal.md)
##### [Portale classico](active-directory-add-domain.md)
##### [With AD FS](active-directory-add-domain-federated.md) (Con AD FS)
#### [Assegnare utenti](active-directory-add-domain-add-users.md)
#### Gestire i nomi di dominio
##### [portale di Azure](active-directory-domains-manage-azure-portal.md)
##### [Portale classico](active-directory-add-manage-domain-names.md)
### Personalizzare la pagina di accesso
#### [Portale di Azure](active-directory-branding-custom-signon-azure-portal.md)
#### [Language-specific](active-directory-branding-localize-azure-portal.md) (Specifico della lingua)
#### [Portale classico](active-directory-add-company-branding.md)
### [Amministrare la directory](active-directory-administer.md)
### [Multiple directories](active-directory-licensing-directory-independence.md) (Diverse directory)
### [O365 directories](active-directory-manage-o365-subscription.md) (Directory di O365)
### [Iscrizione self-service](active-directory-self-service-signup.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Abilitazione](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Impostazioni di Criteri di gruppo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Impostazioni di Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Domande frequenti](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Risolvere i problemi](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

### [Integrate partners with Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md) (Integrare i partner Azure AD B2B)
#### [Aggiunta di utenti B2B da parte di amministratori](active-directory-b2b-admin-add-users.md)
#### [Aggiunta di utenti B2B da parte di information worker](active-directory-b2b-iw-add-users.md)
#### [API e personalizzazione](active-directory-b2b-api.md)
#### [Esempi di codice e PowerShell](active-directory-b2b-code-samples.md)
#### [Esempio di portale per l'iscrizione self-service](active-directory-b2b-self-service-portal.md)
#### [Invito tramite posta elettronica](active-directory-b2b-invitation-email.md)
#### [Riscatto di invito](active-directory-b2b-redemption-experience.md)
#### [Accesso condizionale per B2B](active-directory-b2b-mfa-instructions.md)
#### [Criteri di condivisione per B2B](active-directory-b2b-delegate-invitations.md)
#### [Aggiungere un utente B2B a un ruolo](active-directory-b2b-add-guest-to-role.md)
#### [Gruppi dinamici e utenti B2B](active-directory-b2b-dynamic-groups.md)
#### [Controllo e report](active-directory-b2b-auditing-and-reporting.md)
#### [Condivisione esterna di Office 365 e B2B](active-directory-b2b-o365-external-user.md)
#### [Licenze](active-directory-b2b-licensing.md)
#### [Limitazioni correnti](active-directory-b2b-current-limitations.md)
#### [Domande frequenti](active-directory-b2b-faq.md)
#### [Risoluzione dei problemi B2B](active-directory-b2b-troubleshooting.md)
#### [Informazioni sull'utente B2B](active-directory-b2b-user-properties.md)
#### [Token utente B2B](active-directory-b2b-user-token.md)
#### [B2B per app integrate con Azure AD](active-directory-b2b-configure-saas-apps.md)
#### [Mapping delle attestazioni utente B2B](active-directory-b2b-claims-mapping.md)
#### [Confrontare Collaborazione B2B con B2C](active-directory-b2b-compare-b2c.md)
#### [Ottenere assistenza per B2B](active-directory-b2b-support.md)

### [Integrare le identità locali usando Azure AD Connect](./connect/active-directory-aadconnect.md)

## Delegare l'accesso alle risorse
### [Ruoli dell'amministratore](active-directory-assign-admin-roles.md)
#### [Assign admin roles](active-directory-users-assign-role-azure-portal.md) (Assegnare ruoli amministrativi)
### [Unità amministrative](active-directory-administrative-units-management.md)
### [Accesso alle risorse in Azure](active-directory-understanding-resource-access.md)
### [Controllo degli accessi in base al ruolo](role-based-access-control-what-is.md)
#### Gestire le assegnazioni di accesso
##### [Per utente](role-based-access-control-manage-assignments.md)
##### [Per risorsa](role-based-access-control-configure.md)
#### [Ruoli predefiniti](role-based-access-built-in-roles.md)
#### [Ruoli personalizzati](role-based-access-control-custom-roles.md)
#### [Creazione di report](role-based-access-control-access-change-history-report.md)
#### Altri modi per gestire i ruoli
##### [Interfaccia della riga di comando di Azure](role-based-access-control-manage-access-azure-cli.md)
##### [PowerShell](role-based-access-control-manage-access-powershell.md)
##### [REST](role-based-access-control-manage-access-rest.md)
#### [Risoluzione dei problemi](role-based-access-control-troubleshooting.md)
### [Configure token lifetimes](active-directory-configurable-token-lifetimes.md) (Configurare la durata dei token)

## Proteggere le identità
### [Accesso condizionale](active-directory-conditional-access.md)
#### [Introduzione](active-directory-conditional-access-azuread-connected-apps.md)
#### [App supportate](active-directory-conditional-access-supported-apps.md)
#### [Informazioni sui criteri dei dispositivi](active-directory-conditional-access-device-policies.md)
#### [Configurare l'accesso alle app connesse](active-directory-conditional-access-policy-connected-applications.md)
#### [Domande frequenti](active-directory-conditional-faqs.md)
#### [Risoluzione dei problemi](active-directory-conditional-access-device-remediation.md)
#### [Riferimento](active-directory-conditional-access-technical-reference.md)
### Windows Hello
#### [Authenticate without passwords](active-directory-azureadjoin-passport.md) (Autenticazione senza password)
#### [Abilitare Windows Hello for Business](active-directory-azureadjoin-passport-deployment.md)
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
#### [Unblock users](active-directory-identityprotection-unblock-howto.md) (Sbloccare utenti)
#### [Glossario](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## [Distribuire Active Directory Domain Services in macchine virtuali di Azure](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Windows Server Active Directory nelle VM di Azure](active-directory-deploying-ws-ad-guidelines.md)
### [Controller di dominio di replica in una rete virtuale di Azure](active-directory-install-replica-active-directory-domain-controller.md)
### [Nuova foresta in una rete virtuale Azure](active-directory-new-forest-virtual-machine.md)



## [Distribuire AD FS in Azure](active-directory-aadconnect-azure-adfs.md)
### [Disponibilità elevata](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Change signature hash algorithm](active-directory-federation-sha256-guidance.md) (Modificare l'algoritmo hash della firma)

## [Risolvere i problemi](active-directory-troubleshooting.md)

## Distribuire il modello di verifica (PoC) di Azure AD
### [Studio del modello di verifica: introduzione](active-directory-playbook-intro.md)
### [Studio del modello di verifica: ingredienti](active-directory-playbook-ingredients.md)
### [Studio del modello di verifica: implementazione](active-directory-playbook-implementation.md)
### [Studio del modello di verifica: blocchi predefiniti](active-directory-playbook-building-blocks.md)


# Riferimento
## [Cmdlet di PowerShell](/powershell/ )
## [Informazioni di riferimento sulle API Java](/java/api)
## [API .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Limiti e restrizioni del servizio](active-directory-service-limits-restrictions.md)

# Risorse correlate
## [Autenticazione a più fattori](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD for developers](./develop/active-directory-how-to-integrate.md) (Azure AD per sviluppatori)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# Risorse
## [Prezzi](https://azure.microsoft.com/pricing/details/active-directory/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=active-directory)
## [Forum di commenti e suggerimenti su Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
