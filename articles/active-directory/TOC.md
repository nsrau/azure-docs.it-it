# [Documentazione di Azure Active Directory](index.md)

# Panoramica
## [Informazioni su Azure Active Directory](fundamentals/active-directory-whatis.md)
## [Informazioni sulla Gestione delle identità di Azure](fundamentals/identity-fundamentals.md)
## [Informazioni sulle soluzioni di gestione delle identità di Azure](fundamentals/understand-azure-identity-solutions.md)
## [Associate Azure subscriptions](fundamentals/active-directory-how-subscriptions-associated-directory.md) (Associare le sottoscrizioni di Azure)
## [Considerazioni sulla residenza e sui dati](fundamentals/active-directory-data-storage-eu.md)
## [Domande frequenti](fundamentals/active-directory-faq.md)
## [Novità](fundamentals/whats-new.md)


# Attività iniziali
## [Iscriversi ad Azure AD Premium](fundamentals/active-directory-get-started-premium.md)
## [Aggiungere un nome di dominio personalizzato](fundamentals/add-custom-domain.md)
## [Configurare la personalizzazione aziendale](fundamentals/customize-branding.md)
## [Aggiungere utenti ad Azure AD](fundamentals/add-users-azure-active-directory.md)
## [Assegna licenze agli utenti](fundamentals/license-users-groups.md)
## [Configurare la reimpostazione self-service delle password](authentication/quickstart-sspr.md)
## [Aggiungere le informazioni sulla privacy dell'organizzazione in Azure AD](active-directory-properties-area.md)
## [Accedere ad Azure Active Directory per creare un nuovo tenant](fundamentals/active-directory-access-create-new-tenant.md)


# Procedure
## Pianificare e progettare
### [Informazioni sull'architettura di Azure AD](fundamentals/active-directory-architecture.md)
### [Mapping di attestazioni in Azure Active Directory](develop/active-directory-claims-mapping.md)
### [Distribuire una soluzione ibrida di gestione delle identità](hybrid/plan-hybrid-identity-design-considerations-overview.md)
#### Determinare i requisiti
##### [Identità](hybrid/plan-hybrid-identity-design-considerations-business-needs.md)
##### [Sincronizzazione della directory](hybrid/plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Multi-Factor Authentication](hybrid/plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Strategia del ciclo di vita delle identità](hybrid/plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Pianificare la sicurezza dei dati](hybrid/plan-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Protezione dei dati](hybrid/plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Gestione dei contenuti](hybrid/plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Controllo di accesso](hybrid/plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Risposta agli eventi imprevisti](hybrid/plan-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Pianificare il ciclo di vita delle identità
##### [Attività](hybrid/plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Strategia di adozione](hybrid/plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Passaggi successivi](hybrid/plan-hybrid-identity-design-considerations-nextsteps.md)
#### [Confronto fra strumenti](hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md)

## Gestire gli utenti
### [Aggiungere nuovi utenti ad Azure AD](fundamentals/add-users-azure-active-directory.md)
### [Gestire i profili utente](fundamentals/active-directory-users-profile-azure-portal.md)
### [Reimpostare le password utente](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Assegnare utenti a ruoli amministrativi](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [Aggiungere utenti guest da un'altra directory (B2B)](b2b/what-is-b2b.md)
#### [Aggiunta di utenti B2B da parte di amministratori](b2b/add-users-administrator.md)
#### [Aggiunta di utenti B2B da parte di information worker](b2b/add-users-information-worker.md)
#### [API e personalizzazione](b2b/customize-invitation-api.md)
#### [Federazione Google](b2b/google-federation.md)
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
#### [Domande frequenti](b2b/faq.md)
#### [Risoluzione dei problemi B2B](b2b/troubleshoot.md)
#### [Informazioni sull'utente B2B](b2b/user-properties.md)
#### [Token utente B2B](b2b/user-token.md)
#### [B2B per app integrate con Azure AD](b2b/configure-saas-apps.md)
#### [Mapping delle attestazioni utente B2B](b2b/claims-mapping.md)
#### [Confrontare Collaborazione B2B con B2C](b2b/compare-with-b2c.md)
#### [Ottenere assistenza per B2B](b2b/get-support.md)

## [Gestire i gruppi e i membri](fundamentals/active-directory-manage-groups.md)
### [Gestire i gruppi](fundamentals/active-directory-groups-create-azure-portal.md)
### [Eliminare un gruppo e i relativi membri](fundamentals/active-directory-groups-delete-group.md)
### [Gestire le impostazioni dei gruppi](fundamentals/active-directory-groups-settings-azure-portal.md)
## [Gestire i report](reports-monitoring/overview-reports.md)
### [Attività di accesso](reports-monitoring/concept-sign-ins.md)
### [Attività di controllo](reports-monitoring/concept-audit-logs.md)
### [Utenti a rischio](reports-monitoring/concept-user-at-risk.md)
### [Accessi a rischio](reports-monitoring/concept-risky-sign-ins.md)
### [Eventi di rischio](reports-monitoring/concept-risk-events.md)
### [Monitoraggio dei log con Monitoraggio di Azure](reports-monitoring/concept-activity-logs-in-azure-monitor.md)
### [Domande frequenti](reports-monitoring/reports-faq.md)

### Attività
#### [Scaricare un report relativo agli accessi](reports-monitoring/quickstart-download-sign-in-report.md)
#### [Scaricare un report di controllo](reports-monitoring/quickstart-download-audit-report.md)
#### [Configurare località denominate](reports-monitoring/quickstart-configure-named-locations.md)
#### [Trovare report sull'attività](reports-monitoring/howto-find-activity-reports.md)
#### [Usare il pacchetto di contenuto Power BI per Azure AD](reports-monitoring/howto-power-bi-content-pack.md)
#### [Correggere gli utenti contrassegnati per il rischio](reports-monitoring/howto-remediate-users-flagged-for-risk.md)
#### [Instradare i log attività a un hub eventi di Azure](reports-monitoring/quickstart-azure-monitor-stream-logs-to-event-hub.md)
#### [Archiviare i log attività in un account di archiviazione di Azure](reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
#### [Integrare i log attività con Splunk usando Monitoraggio di Azure](reports-monitoring/tutorial-integrate-activity-logs-with-splunk.md)
#### [Integrare i log attività con SumoLogic usando Monitoraggio di Azure](reports-monitoring/howto-integrate-activity-logs-with-sumologic.md)
#### [Integrare i log attività con Log Analytics usando Monitoraggio di Azure](reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

### riferimento
#### [Conservazione](reports-monitoring/reference-reports-data-retention.md)
#### [Latenze](reports-monitoring/reference-reports-latencies.md)
#### [Informazioni di riferimento sulle attività di controllo](reports-monitoring/reference-audit-activities.md)
#### [Codici errore relativi alle attività di accesso](reports-monitoring/reference-sign-ins-error-codes.md)
#### [Interpretare lo schema del log di controllo in Monitoraggio di Azure](reports-monitoring/reference-azure-monitor-audit-log-schema.md)
#### [Interpretare lo schema del log di accesso in Monitoraggio di Azure](reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md)

### Risolvere problemi
#### [Dati mancanti nei log attività di Azure AD](reports-monitoring/troubleshoot-missing-audit-data.md)
#### [Dati mancanti nei download](reports-monitoring/troubleshoot-missing-data-download.md)
#### [Errori del pacchetto di contenuto dei log attività di Azure AD](reports-monitoring/troubleshoot-content-pack.md)
#### [Errori nell'API di creazione report di Azure AD](reports-monitoring/troubleshoot-graph-api.md)

### [Accesso a livello di codice](reports-monitoring/concept-reporting-api.md)
#### [Prerequisiti](reports-monitoring/howto-configure-prerequisites-for-reporting-api.md)
#### [Uso dei certificati](reports-monitoring/tutorial-access-api-with-certificates.md)

## [Gestire le password](authentication/concept-sspr-howitworks.md)

## Gestire le app
### [Panoramica](manage-apps/what-is-application-management.md)
### [Introduzione](manage-apps/plan-an-application-integration.md)
### [Esercitazioni sull'integrazione di app SaaS](saas-apps/tutorial-list.md)

### [Provisioning e deprovisioning di utenti nelle app SaaS](manage-apps/user-provisioning.md) 
#### [Esercitazioni sull'integrazione di app](saas-apps/tutorial-list.md) 
#### [Automatizzare il provisioning nelle app abilitate per SCIM](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Personalizzare i mapping degli attributi](manage-apps/customize-application-attributes.md) 
#### [Scrivere espressioni per i mapping degli attributi](manage-apps/functions-for-customizing-application-data.md) 
#### [Usare i filtri per la definizione dell'ambito](manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) 
#### [Creare report sul provisioning utenti automatico](manage-apps/check-status-user-account-provisioning.md) 
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
##### [Caratteri jolly](manage-apps/application-proxy-wildcard.md)
##### [Rimuovere i dati personali](manage-apps/application-proxy-remove-personal-data.md)


#### Procedure dettagliate di pubblicazione
##### [Desktop remoto](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](manage-apps/application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Risolvere i problemi](manage-apps/application-proxy-troubleshoot.md)

### Gestire le app aziendali
#### [Aggiungere un'applicazione](manage-apps/add-application-portal.md)
#### [Visualizzare le app di tenant](manage-apps/view-applications-portal.md)
#### [Configurare l'accesso Single Sign-On](manage-apps/configure-single-sign-on-portal.md)
#### [Assegnare utenti](manage-apps/assign-user-or-group-access-portal.md)
#### [Modificare la personalizzare](manage-apps/change-name-or-logo-portal.md)
#### [Disabilitare l'accesso degli utenti](manage-apps/disable-user-sign-in-portal.md)
#### [Rimuovere utenti](manage-apps/remove-user-or-group-access-portal.md)

#### [Gestire il provisioning degli account utente](manage-apps/configure-automatic-user-provisioning-portal.md)

#### [Firma di certificati avanzata per app SAML](manage-apps/certificate-signing-options.md)
#### [Nascondere un'applicazione dall'esperienza di un utente](manage-apps/hide-application-from-user-portal.md)
### [Configurare l'accelerazione automatica dell'accesso tramite i criteri HRD](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Eseguire la migrazione di app di AD FS in Azure AD](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Manage access to apps](manage-apps/what-is-access-management.md) (Gestire l'accesso alle app)
#### [Accesso SSO](manage-apps/what-is-single-sign-on.md)
#### [Certificati per SSO](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Restrizioni relative ai tenant](manage-apps/tenant-restrictions.md)
#### [Use SCIM provision users](manage-apps/use-scim-to-provision-users-and-groups.md) (Usare SCIM per il provisioning degli utenti)

### [Informazioni sulle esperienze di consenso per applicazioni Azure AD](develop/application-consent-experience.md)

### Risolvere problemi



#### Pannello di accesso
##### [App non visualizzate](manage-apps/access-panel-troubleshoot-application-not-appearing.md)
##### [Visualizzazione di app impreviste](manage-apps/access-panel-troubleshoot-unexpected-application.md)
##### [L'accesso non è riuscito](manage-apps/access-panel-troubleshoot-web-sign-in-problem.md)
##### [Errore durante l'installazione dell'estensione del browser](manage-apps/access-panel-extension-problem-installing.md)
##### [Come usare l'accesso alle app self-service](manage-apps/access-panel-manage-self-service-access.md)
##### [Errore durante l'uso dell'accesso alle app self-service](manage-apps/access-panel-troubleshoot-self-service-access.md)

#### Aggiunta di un'app
##### [Scegliere un tipo di app](manage-apps/choose-application-type.md)
##### [Problemi comuni - App della raccolta](manage-apps/adding-gallery-app-common-problems.md)
##### [Problemi comuni - App non della raccolta](manage-apps/adding-non-gallery-app-common-problems.md)

#### Proxy dell'applicazione
##### [Problema durante la visualizzazione di una pagina dell'app](manage-apps/application-proxy-page-appearance-broken-problem.md)
##### [Il caricamento dell'applicazione richiede troppo tempo](manage-apps/application-proxy-page-load-speed-problem.md)
##### [I collegamenti nella pagina dell'applicazione non funzionano](manage-apps/application-proxy-page-links-broken-problem.md)
##### [Porte da aprire per l'app](manage-apps/application-proxy-connectivity-ports-how-to.md)
##### [Nessun connettore funzionante in un gruppo di connettori per le app](manage-apps/application-proxy-connectivity-no-working-connector.md)
##### [Configurare nel portale di amministrazione](manage-apps/application-proxy-config-how-to.md)
##### [Configurare l'accesso Single Sign-On all'app](manage-apps/application-proxy-config-sso-how-to.md)
##### [Problema durante la creazione di un'app nel portale di amministrazione](manage-apps/application-proxy-config-problem.md)
##### [Configurare la delega vincolata di Kerberos](manage-apps/application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Configurare con PingAccess](manage-apps/application-proxy-back-end-ping-access-how-to.md)
##### [Errore di accesso non riuscito all'applicazione aziendale](manage-apps/application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Problemi di installazione del connettore dell'agente proxy dell'applicazione](manage-apps/application-proxy-connector-installation-problem.md)


#### Registrazione dell'applicazione
##### [Immettere i campi per l'oggetto applicazione](develop/registration-config-specific-application-property-how-to.md)
##### [Modificare le impostazioni predefinite per la durata del token](develop/registration-config-change-token-lifetime-how-to.md)

#### Authentication
##### [Configurare gli endpoint](develop/registration-config-how-to.md)

#### Accesso condizionale
##### [Il cliente non rispetta i prerequisiti per la registrazione del dispositivo](active-directory-conditional-access.md)
##### [Come e quando vengono applicate le regole al di fuori della rete aziendale?](https://aka.ms/calocation)
##### [Come si aumenta il numero di dispositivi che un utente può registrare in Azure AD?](active-directory-azureadjoin-setup.md)
##### [Come si configura l'accesso condizionale per Exchange Online?](https://aka.ms/csforexchange)
##### [Come si configura l'accesso condizionale per i dispositivi Windows 7?](active-directory-conditional-access.md#device-based-conditional-access)
##### [Quali applicazioni sono supportate con l'accesso condizionale?](active-directory-conditional-access-supported-apps.md)

#### Trovare un'API
##### [Trovare un'API](develop/api-find-an-api-how-to.md)

#### Gestione dell'accesso
##### [Assegnare utenti e gruppi a un'app](manage-apps/methods-for-assigning-users-and-groups.md)
##### [Rimuovere l'accesso di un utente a un'app](manage-apps/methods-for-removing-user-access.md)
##### [Configurare l'assegnazione di app self-service](manage-apps/manage-self-service-access.md)
##### [Utente imprevisto assegnato](manage-apps/ways-users-get-assigned-to-applications.md)
##### [App imprevista nell'elenco di applicazioni](manage-apps/application-types.md)

#### App multi-tenant
##### [Configurare una nuova app](develop/setup-multi-tenant-app.md)
##### [Aggiungere alla raccolta di app](develop/registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Autorizzazioni
##### [Scegliere le autorizzazioni per un'API](develop/perms-for-given-api.md)
##### [Confronto tra delegati e autorizzazioni dell'applicazione](develop/delegated-and-app-perms.md)
##### [Consenso per applicazioni](develop/consent-framework.md)

#### Provisioning
##### [Tempo necessario](manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Tempo necessario eccessivo - App della raccolta](manage-apps/application-provisioning-when-will-provisioning-finish.md)
##### [Configurare il provisioning utenti - App della raccolta](manage-apps/application-provisioning-config-how-to.md)
##### [Problema durante la configurazione del provisioning utenti - App della raccolta](manage-apps/application-provisioning-config-problem.md)
##### [Problema di salvataggio delle credenziali dell'amministratore durante la configurazione del provisioning utenti nell'app della raccolta](manage-apps/application-provisioning-config-problem-storage-limit.md)
##### [Utenti senza provisioning - App della raccolta](manage-apps/application-provisioning-config-problem-no-users-provisioned.md)
##### [Provisioning di utenti errati - App della raccolta](manage-apps/application-provisioning-config-problem-wrong-users-provisioned.md)

#### Single sign-on
##### [Scegliere un metodo](manage-apps/single-sign-on-modes.md)
##### [Configurare](develop/registration-config-sso-how-to.md)
##### [Configurare l'accesso federato - App della raccolta](manage-apps/configure-federated-single-sign-on-gallery-applications.md)
##### [Problemi comuni della configurazione dell'accesso federato - App della raccolta](manage-apps/configure-federated-single-sign-on-gallery-applications-problems.md)
##### [Configurare l'accesso federato - App non della raccolta](manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
##### [Problemi comuni della configurazione dell'accesso federato - App non della raccolta](manage-apps/configure-federated-single-sign-on-non-gallery-applications-problems.md)
##### [Configurare la password - App della raccolta](manage-apps/configure-password-single-sign-on-gallery-applications.md)
##### [Problemi comuni della configurazione delle password - App della raccolta](manage-apps/configure-password-single-sign-on-gallery-applications-problems.md)
##### [Configurare la password - App non della raccolta](manage-apps/configure-password-single-sign-on-non-gallery-applications.md)
##### [Problemi comuni della configurazione delle password - App non della raccolta](manage-apps/configure-password-single-sign-on-non-gallery-applications-problems.md)

#### Problemi di accesso utente
##### [Richiesta imprevista del consenso](manage-apps/application-sign-in-unexpected-user-consent-prompt.md)
##### [Errore di consenso utente](manage-apps/application-sign-in-unexpected-user-consent-error.md)
##### [Problemi durante l'accesso dal portale personalizzato](manage-apps/application-sign-in-other-problem-deeplink.md)
##### [Problemi durante l'accesso dal riquadro di accesso](manage-apps/application-sign-in-other-problem-access-panel.md)
##### [Errore della pagina di accesso dell'applicazione](manage-apps/application-sign-in-problem-application-error.md)
##### [Problema dell'accesso Single Sign-On della password - App non della raccolta](manage-apps/application-sign-in-problem-password-sso-non-gallery.md)
##### [Problema dell'accesso Single Sign-On della password - App della raccolta](manage-apps/application-sign-in-problem-password-sso-gallery.md)
##### [Problema durante l'accesso a un'app Microsoft](manage-apps/application-sign-in-problem-first-party-microsoft.md)
##### [Problema dell'accesso Single Sign-On federato - App non della raccolta](manage-apps/application-sign-in-problem-federated-sso-non-gallery.md)
##### [Problema dell'accesso Single Sign-On federato - App della raccolta](manage-apps/application-sign-in-problem-federated-sso-gallery.md)
##### [Problema dell'app sviluppata in modo personalizzato](manage-apps/application-sign-in-problem-custom-dev.md)
##### [Problema dell'app locale - Proxy dell'applicazione](manage-apps/application-sign-in-problem-on-premises-application-proxy.md)

### [Sviluppare app](active-directory-applications-guiding-developers-for-lob-applications.md)


## Gestire la directory
### [Azure AD Connect](hybrid/whatis-hybrid-identity.md)
### Nomi di dominio personalizzati
#### [Guida introduttiva](fundamentals/add-custom-domain.md)
### [Amministrare la directory](fundamentals/active-directory-administer.md)
### [Integrare le identità locali usando Azure AD Connect](hybrid/whatis-hybrid-identity.md)

### [Configure token lifetimes](develop/active-directory-configurable-token-lifetimes.md) (Configurare la durata dei token)

## Proteggere le identità

### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)



## [Risolvere i problemi](fundamentals/active-directory-troubleshooting-support-howto.md)

## Distribuire il modello di verifica (PoC) di Azure AD
### [Studio del modello di verifica: introduzione](active-directory-playbook-intro.md)
### [Studio del modello di verifica: ingredienti](active-directory-playbook-ingredients.md)
### [Studio del modello di verifica: implementazione](active-directory-playbook-implementation.md)
### [Studio del modello di verifica: blocchi predefiniti](active-directory-playbook-building-blocks.md)

# riferimento
## [Esempi di codice](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [cmdlet di Azure PowerShell](/powershell/azure/overview)
## [Informazioni di riferimento sulle API Java](/java/api)
## [API .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)

# Risorse correlate
## [Autenticazione a più fattori](/azure/multi-factor-authentication/)
## [Azure AD Connect](hybrid/whatis-hybrid-identity.md)
## [Azure AD Connect Health](hybrid/whatis-hybrid-identity-health.md)
## [Azure AD per sviluppatori](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/pim-configure.md)

# Risorse
## [Piani di distribuzione di Azure AD](./fundamentals/active-directory-deployment-plans.md)
## [Forum di commenti e suggerimenti su Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Prezzi](https://azure.microsoft.com/pricing/details/active-directory/)
## [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
