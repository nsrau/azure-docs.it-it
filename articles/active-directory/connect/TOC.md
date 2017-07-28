# Panoramica
## [Che cos'è Azure AD Connect?](active-directory-aadconnect.md)
## [Che cos'è il servizio di sincronizzazione Azure AD Connect?](active-directory-aadconnectsync-whatis.md)
### [Utenti e contatti](active-directory-aadconnectsync-understanding-users-and-contacts.md)
### [Architettura](active-directory-aadconnectsync-understanding-architecture.md)
### [Provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md)
#### [Espressioni di provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
### [Configurazione predefinita](active-directory-aadconnectsync-understanding-default-configuration.md)
## [Che cosa sono Azure AD Connect e la federazione?](active-directory-aadconnectfed-whatis.md)


# Introduzione
## [Prerequisiti](active-directory-aadconnect-prerequisites.md)
## [Installare Azure AD Connect](active-directory-aadconnect-select-installation.md)
### [Impostazioni rapide](active-directory-aadconnect-get-started-express.md)
### [Impostazioni personalizzate](active-directory-aadconnect-get-started-custom.md)
### [Aggiornamento da DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
### [Eseguire l'aggiornamento da una versione precedente](active-directory-aadconnect-upgrade-previous-version.md)


# Procedure
## Pianificare e progettare
### [Concetti relativi alla progettazione](active-directory-aadconnect-design-concepts.md)
### [Topologie per Azure AD Connect](active-directory-aadconnect-topologies.md)
### [Active Directory Federation Services in Azure](active-directory-aadconnect-azure-adfs.md)
### [Considerazioni speciali sulle istanze](active-directory-aadconnect-instances.md)
### [Quando si ha già Azure AD](active-directory-aadconnect-existing-tenant.md)
## [Gestire Azure AD Connect](active-directory-aadconnect-whats-next.md)
### [Rinnovare i certificati per Office 365 e Azure AD](active-directory-aadconnect-o365-certs.md)
### [Abilitare il writeback dei dispositivi](active-directory-aadconnect-feature-device-writeback.md)
### [Opzioni di accesso utente](active-directory-aadconnect-user-signin.md)
#### [Seamless Single Sign-On](active-directory-aadconnect-sso.md)
##### [Avvio rapido](active-directory-aadconnect-sso-quick-start.md)
##### [Come funziona?](active-directory-aadconnect-sso-how-it-works.md)
##### [Domande frequenti](active-directory-aadconnect-sso-faq.md)
##### [Risoluzione dei problemi](active-directory-aadconnect-troubleshoot-sso.md)
#### [Autenticazione pass-through](active-directory-aadconnect-pass-through-authentication.md)
##### [Avvio rapido](active-directory-aadconnect-pass-through-authentication-quick-start.md)
##### [Limitazioni correnti](active-directory-aadconnect-pass-through-authentication-current-limitations.md)
##### [Come funziona?](active-directory-aadconnect-pass-through-authentication-how-it-works.md)
##### [Blocco intelligente](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)
##### [Domande frequenti](active-directory-aadconnect-pass-through-authentication-faq.md)
##### [Risoluzione dei problemi](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)
### [Supporto di più domini per la federazione](active-directory-aadconnect-multiple-domains.md)
### [Aggiornamento automatico](active-directory-aadconnect-feature-automatic-upgrade.md)
### [Usare un provider di identità SAML 2.0 per l'accesso Single Sign-On](active-directory-aadconnect-federation-saml-idp.md)



## Gestire il Servizio di sincronizzazione Azure AD Connect
### [Impedire eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
### [Sincronizzazione delle password](active-directory-aadconnectsync-implement-password-synchronization.md)
### [Account del servizio Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md)
### [Installazione guidata](active-directory-aadconnectsync-installation-wizard.md)
### [Modificare la configurazione predefinita](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)
### [Configurare il filtro](active-directory-aadconnectsync-configure-filtering.md)
### [Utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md)
### [Estensioni della directory](active-directory-aadconnectsync-feature-directory-extensions.md)

### [Modifica della password dell'account del servizio Azure AD Sync](active-directory-aadconnectsync-change-serviceacct-pass.md)
### [Modifica della password dell'account Active Directory Domain Services](active-directory-aadconnectsync-change-addsacct-pass.md)
### [Abilitare il Cestino di AD](active-directory-aadconnectsync-recycle-bin.md)

### [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)
#### [Operazioni](active-directory-aadconnectsync-service-manager-ui-operations.md)
#### [Connettori](active-directory-aadconnectsync-service-manager-ui-connectors.md)
#### [Finestra di progettazione metaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)
#### [Ricerca metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)


## Gestire Federation Services
### [Gestire e personalizzare](active-directory-aadconnect-federation-management.md)
### [Attuare la federazione di più istanze di Azure AD con un'unica istanza di AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)


## Risoluzione dei problemi
### [Connettività](active-directory-aadconnect-troubleshoot-connectivity.md)
### [Errori durante la sincronizzazione](active-directory-aadconnect-troubleshoot-sync-errors.md)
### [Oggetto non sincronizzato](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)
### [Sincronizzazione delle password](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)
### [Errore LargeObject provocato da userCertificate](active-directory-aadconnectsync-largeobjecterror-usercertificate.md)
### [Come eseguire il ripristino dal limite di 10 GB per LocalDB](active-directory-aadconnect-recover-from-localdb-10gb-limit.md)

# Riferimento
## [Sincronizzazione delle identità e resilienza degli attributi duplicati](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
## [Porte e protocolli necessari per la soluzione ibrida di gestione delle identità](active-directory-aadconnect-ports.md)
## [Funzionalità in anteprima](active-directory-aadconnect-feature-preview.md)
## [Cronologia delle versioni](active-directory-aadconnect-version-history.md)
## [Account e autorizzazioni](active-directory-aadconnect-accounts-permissions.md)

## Servizio di sincronizzazione Azure AD Connect
### [Attributi sincronizzati con Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md)
### [Cronologia di rilascio delle versioni dei connettori](active-directory-aadconnectsync-connector-version-history.md)
### [Riferimento alle funzioni](active-directory-aadconnectsync-functions-reference.md)
### [Attività operative e considerazioni](active-directory-aadconnectsync-operations.md)
### [Elenco di compatibilità di federazione di Azure AD](active-directory-aadconnect-federation-compatibility.md)
### [Concetti tecnici](active-directory-aadconnectsync-technical-concepts.md)
### [Funzionalità del servizio](active-directory-aadconnectsyncservice-features.md)




# Risorse correlate
## [Monitorare l'infrastruttura di gestione delle identità locale e i servizi di sincronizzazione nel cloud](../connect-health/active-directory-aadconnect-health.md)
## [Guide alla progettazione della soluzione ibrida di gestione delle identità](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)


# Risorse
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
##[Domande frequenti su Azure AD Connect](active-directory-aadconnect-faq.md)
##[Deprecazione di DirSync](active-directory-aadconnect-dirsync-deprecated.md)
