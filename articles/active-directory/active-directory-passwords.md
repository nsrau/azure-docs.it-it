---
title: Reimpostazione della password di Azure AD | Microsoft Docs
description: Descrizione delle funzionalità di gestione delle password in Azure AD, incluse la reimpostazione delle password, la modifica, il report di gestione delle password e il writeback in Active Directory locale.
services: active-directory
documentationcenter: ''
author: asteen
manager: femila
editor: curtand

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: asteen

---
# Reimpostazione della password di Azure AD per utenti e amministratori
> [!IMPORTANT]
> Si desidera reimpostare la password di Azure o di O365? Se sì, [passare a questa sezione](#users-how-to-manage-your-own-password).
> 
> 

Self-Service è stato a lungo un obiettivo chiave per i reparti IT di tutto il mondo come misura di riduzione dei costi e facile utilizzo. In effetti, nel mercato si sono diffusi prodotti che consentono di gestire gruppi locali, password o profili utente dal cloud o in locale. Azure AD si distingue da queste offerte fornendo alcune delle funzionalità self-service più efficaci e semplici da usare disponibili attualmente.

**Gestione delle password di Azure AD** è un insieme di funzionalità che consentono agli utenti di gestire le password da qualsiasi dispositivo, in qualsiasi momento, da qualsiasi posizione, pur rimanendo conforme ai criteri di sicurezza definiti.

## UTENTI: Come gestire la password personale
Se si è un utente (non un amministratore) di un'azienda che accede alle risorse di lavoro usando Office 365 o account Microsoft, fare clic sui collegamenti seguenti per informazioni su come risolvere problemi comuni con la password.

| Argomento |  |
| --- | --- |
| Eseguire la registrazione per la reimpostazione della password |[Come registrarsi per la reimpostazione della password](active-directory-passwords-update-your-own-password.md#how-to-register-for-password-reset) |
| Modificare la password da O365 |[Come modificare la password da Office365](active-directory-passwords-update-your-own-password.md#how-to-change-your-password-from-o365) |
| Modificare la password da myapps.microsoft.com |[Come modificare la password dal pannello di accesso](active-directory-passwords-update-your-own-password.md#how-to-change-your-password-from-the-access-panel) |
| Reimpostare una password dimenticata |[Come reimpostare la password](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password) |
| Sbloccare un account a cui non si riesce ad accedere |[Come sbloccare il proprio account locale](active-directory-passwords-update-your-own-password.md#how-to-unlock-your-account) |
| Risolvere un problema relativo a una reimpostazione di password non riuscita |[Problemi comuni e relative soluzioni](active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions) |

## AMMINISTRATORI: Informazioni sulle operazioni preliminari per la reimpostazione della password di AD Azure
Se si è un amministratore che desidera abilitare la funzione di reimpostazione della password di Azure AD o semplicemente desidera maggiori informazioni, iniziare con i collegamenti seguenti per accedere agli argomenti di pertinenza.

| Argomento |  |
| --- | --- |
| Scenari supportati |[Quali operazioni sono possibili con la funzione di reimpostazione della password di Azure AD?](#what-is-possible-with-azure-ad-password-reset) |
| Perché usare questa funzione? |[Perché usare la funzione di reimpostazione della password di Azure AD?](#why-use-azure-ad-password-reset) |
| Prezzi e disponibilità |[Prezzi e disponibilità](#pricing-and-availability) |
| Abilitare la reimpostazione della password |[Abilitare la reimpostazione della password per gli utenti](#enable-password-reset-for-your-users) |
| Personalizzare il funzionamento |[Personalizzare il comportamento della funzione di reimpostazione della password](#customize-password-reset-behavior) |
| Distribuire la funzione agli utenti |[Configurare gli utenti per l'uso della funzione di reimpostazione della password](#configure-your-users-to-use-password-reset) |
| Visualizzazione dei report |[Visualizzare le attività di reimpostazione della password con report integrati](#view-password-reset-activity-with-integrated-reports) |
| Reimpostare la password di un utente |[Gestire le password utente personali](#manage-your-users-passwords) |
| Impostare i criteri di gestione delle password aziendali |[Impostazione dei criteri di gestione delle password](#set-password-policies) |
| Risolvere un problema |[Risolvere un problema](#troubleshoot-a-problem) |
| Domande frequenti |[Leggere una domanda frequente](#read-a-faq) |
| Dettagli tecnici |[Comprendere i dettagli tecnici](#understand-the-technical-details) |
| Nuove funzioni |[Aggiornamenti recenti del servizio](#recent-service-updates) |
| Collegamenti ad altra documentazione |[Collegamenti alla documentazione relativa alla reimpostazione della password](#links-to-password-reset-documentation) |

### Quali operazioni sono possibili con la funzione di reimpostazione della password di Azure AD?
Di seguito sono elencate alcune operazioni che è possibile eseguire con le funzionalità di gestione delle password di Azure AD.

* La **modifica delle password self-service** consente agli utenti finali o agli amministratori di cambiare le password scadute o non scadute senza dover chiamare un amministratore o rivolgersi al Supporto tecnico.
* La **reimpostazione delle password self-service** consente agli utenti finali o agli amministratori di reimpostare automaticamente le password senza dover chiamare un amministratore o rivolgersi al Supporto tecnico. La reimpostazione delle password self-service è disponibile solo in Azure AD Premium o Basic. Per altre informazioni, vedere Edizioni di Azure Active Directory.
* La **reimpostazione delle password avviata dall'amministratore** consente a un amministratore di reimpostare la password di un utente finale o di un altro amministratore dal [portale di gestione di Azure](https://manage.windowsazure.com).
* I **report sulle attività di gestione delle password** forniscono agli amministratori informazioni dettagliate sulle attività di reimpostazione delle password e di registrazione che si verificano all'interno dell'organizzazione.
* Il **writeback delle password** permette di gestire le password locali dal cloud, in modo che tutti gli scenari descritti in precedenza possano essere eseguiti da o per conto di utenti federati o con sincronizzazione tramite password. Per il writeback delle password è necessaria una licenza di Azure AD Premium. Per altre informazioni, vedere Introduzione a Azure AD Premium.

### Perché usare la funzione di reimpostazione della password di Azure AD?
Di seguito sono elencati alcuni motivi per cui è consigliabile usare le funzionalità di gestione delle password di Azure AD.

* **Ridurre i costi**: la reimpostazione delle password assistita dal supporto rappresenta in genere il 20% della spesa IT dell'organizzazione
* **Migliorare le esperienze dell’utente**: gli utenti non vogliono chiamare il supporto tecnico e trascorrere un'ora al telefono ogni volta che dimenticano le proprie password
* **Ridurre i volumi di supporto tecnico**: la gestione delle password è il singolo driver di supporto tecnico più grande per la maggior parte delle organizzazioni
* **Consentire la mobilità**: gli utenti possono reimpostare le password da ovunque si trovino

### Prezzi e disponibilità
La funzione di reimpostazione della password di AD Azure è disponibile in 3 livelli, in base al tipo di sottoscrizione eseguita:

* **Azure AD Free** - Gli amministratori di reti solo cloud possono reimpostare la password personale
* **Azure AD Basic o una sottoscrizione di O365 a pagamento** - Gli amministratori e gli utenti di reti solo cloud possono reimpostare la password personale
* **Azure AD Premium** - Qualsiasi utente o amministratore, inclusi gli utenti di reti solo cloud, federati o sincronizzati tramite password, può reimpostare la password personale (è necessario che sia [abilitata la funzione di writeback delle password](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords))

Per altre informazioni sui prezzi di Azure AD Premium o Basic, visitare la pagina [Prezzi di Active Directory di Azure](https://azure.microsoft.com/pricing/details/active-directory/).

## Abilitare la reimpostazione della password per gli utenti
| Argomento |  |
| --- | --- |
| Come è possibile abilitare la reimpostazione della password per gli utenti cloud? |[Consentire agli utenti di reimpostare le password di Azure Active Directory nel cloud](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) |
| Come è possibile abilitare la reimpostazione e la modifica delle password per gli utenti locali? |[Consentire agli utenti di reimpostare o modificare le password di Active Directory locale](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) |
| Come è possibile limitare la reimpostazione della password a un gruppo specifico di utenti? |[Limitare la reimpostazione della password a utenti specifici](active-directory-passwords-customize.md#restrict-access-to-password-reset) |
| Come è possibile testare la reimpostazione della password nel cloud? |[Reimpostare la password di Azure AD come utente](active-directory-passwords-getting-started.md#step-3-reset-your-azure-ad-password-as-a-user) |
| Come è possibile testare la reimpostazione della password in locale? |[Reimpostare la password di Azure AD locale come utente](active-directory-passwords-getting-started.md#step-5-reset-your-ad-password-as-a-user) |
| Come è possibile disabilitare la reimpostazione della password in un secondo momento? |[Impostazione: Utenti abilitati per la reimpostazione della password](active-directory-passwords-customize.md#users-enabled-for-password-reset) |

## Personalizzare il comportamento della funzione di reimpostazione della password
| Argomento |  |
| --- | --- |
| Come è possibile modificare i metodi di autenticazione supportati? |[Impostazione: Metodi di autenticazione disponibili per gli utenti](active-directory-passwords-customize.md#authentication-methods-available-to-users) |
| Come è possibile modificare il numero di metodi di autenticazione necessari? |[Impostazione: Numero di metodi di autenticazione necessari](active-directory-passwords-customize.md#number-of-authentication-methods-required) |
| Come è possibile configurare domande di sicurezza personalizzate? |[Impostazione: Domande di sicurezza personalizzate](active-directory-passwords-customize.md#custom-security-questions) |
| Come è possibile configurare domande di sicurezza localizzate predefinite? |[Impostazione: Domande di sicurezza basate su informazioni note](active-directory-passwords-customize.md#knowledge-based-security-questions) |
| Come è possibile modificare il numero di domande di sicurezza necessarie? |[Impostazione: Numero di domande necessarie per la registrazione (o la reimpostazione)](active-directory-passwords-customize.md#number-of-questions-required-to-register) |
| Come è possibile personalizzare le modalità con cui un utente può entrare in contatto con un amministratore? |[Impostazione: Personalizzare il collegamento "Contattare l'amministratore?"](active-directory-passwords-customize.md#customize-the-contact-your-administrator-link) |
| Come è possibile consentire agli utenti di sbloccare account di Active Directory senza reimpostare una password? |[Impostazione: Consenti agli utenti di sbloccare gli account senza reimpostare la password](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password) |
| Come è possibile abilitare le notifiche di reimpostazione della password per gli utenti? |[Impostazione: Notifica a utenti e amministratori quando è stata reimpostata la propria password](active-directory-passwords-customize.md#notify-users-and-admins-when-their-own-password-has-been-reset) |
| Come è possibile abilitare le notifiche di reimpostazione della password per gli amministratori? |[Impostazione: Notifica agli amministratori quando altri amministratori reimpostano le proprie password](active-directory-passwords-customize.md#notify-admins-when-other-admins-reset-their-own-passwords) |
| Come è possibile personalizzare la veste grafica della funzione di reimpostazione della password? |[Impostazione: Nome, marchio e logo dell'azienda](active-directory-passwords-customize.md#password-management-look-and-feel) |

## Configurare gli utenti per l'uso della funzione di reimpostazione della password
| Argomento |  |
| --- | --- |
| Come è possibile sapere se un account è configurato per la reimpostazione della password? |[Cosa rende un account configurato per la reimpostazione della password?](active-directory-passwords-best-practices.md#what-makes-an-account-configured) |
| Come è possibile rendere gli utenti configurati per la reimpostazione della password? |[Modi per popolare i dati di autenticazione degli utenti per la reimpostazione della password](active-directory-passwords-best-practices.md#ways-to-populate-authentication-data) |
| Come è possibile caricare manualmente i dati per gli utenti? |[Caricamento manuale dei dati di reimpostazione della password](active-directory-passwords-best-practices.md#uploading-data-yourself) |
| Come è possibile usare PowerShell per leggere o impostare dati per gli utenti? |[Come accedere ai dati di reimpostazione della password per gli utenti](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) |
| Come è possibile sincronizzare i dati di reimpostazione della password da una postazione locale? |[Dati usati per la reimpostazione della password](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |
| Come è possibile usare una campagna di posta elettronica per ottenere gli utenti da registrare e usufruire della funzione di reimpostazione della password? |[Implementazione della funzione di reimpostazione della password basata su posta elettronica](active-directory-passwords-best-practices.md#email-based-rollout) |
| Come è possibile forzare gli utenti a registrarsi durante la procedura di accesso? |[Implementazione della funzione di reimpostazione della password basata sulla registrazione forzata](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) |
| Come è possibile forzare gli utenti a riconfermare periodicamente la propria registrazione? |[Impostazione: Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione](active-directory-passwords-customize.md#number-of-days-before-users-must-confirm-their-contact-data) |
| Quali sono le procedure consigliate per comunicare la reimpostazione della password agli utenti finali? |[Creazione di un portale di reimpostazione della password per gli utenti](active-directory-passwords-best-practices.md#creating-your-own-password-portal) |

## Visualizzare le attività di reimpostazione della password con report integrati
| Argomento |  |
| --- | --- |
| Dove è possibile visualizzare i report di reimpostazione della password? |[Informazioni generali sui report di gestione delle password](active-directory-passwords-get-insights.md#overview-of-password-management-reports) |
| Dove è possibile visualizzare in che modo gli utenti aziendali usano la funzione di reimpostazione della password? |[Visualizzare le attività di reimpostazione delle password nell'organizzazione](active-directory-passwords-get-insights.md#view-password-reset-activity) |
| Dove è possibile visualizzare quanti utenti stanno effettuando la registrazione e a cosa si stanno registrando? |[Visualizzare le attività di registrazione per la reimpostazione delle password](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) |
| Come è possibile ottenere i report di reimpostazione della password da un'API? |[Creazione di un'applicazione in Azure AD per accedere all'API di generazione di report](active-directory-reporting-api-getting-started.md#creating-an-azure-ad-application-to-access-the-api) |
| Che tipo di informazioni sulla generazione dei report di reimpostazione della password sono disponibili tramite un'API? |[Eventi di registrazione e reimpostazione della password disponibili nell'API per la generazione di report](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview#SsprActivityEvent) |

## Gestire le password utente personali
| Argomento |  |
| --- | --- |
| Come è possibile reimpostare una password utente dal portale di gestione di O365? |[Reimpostare una password utente in Office 365](https://support.office.com/article/Reset-a-user-s-password-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C) |
| Come è possibile reimpostare una password utente tramite PowerShell? |[Reimpostare una password utente con Set-MsolUserPassword](https://msdn.microsoft.com/library/azure/dn194140.aspx) |

## Impostazione dei criteri di gestione delle password
| Argomento |  |
| --- | --- |
| Come è possibile impostare i criteri di scadenza delle password aziendali da Office 365? |[Impostare i criteri di scadenza delle password](https://support.office.com/article/Set-a-user-s-password-expiration-policy-0f54736f-eb22-414c-8273-498a0918678f) |
| Come è possibile usare PowerShell per impostare le password di un utente specifico in modo che non scadano mai? |[Impostare la password di un utente in modo che non scada mai tramite PowerShell](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466) |
| Come è possibile scoprire se la password di un utente è stata impostata con PowerShell in modo da non scadere mai? |[Controllare lo stato di scadenza delle password di un utente usando PowerShell](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466#__toc378845827) |

## Risolvere un problema
| Argomento |  |
| --- | --- |
| Quali informazioni è necessario fornire al personale di supporto per ottenere assistenza? |[Informazioni da includere quando è necessaria assistenza](active-directory-passwords-troubleshoot.md#information-to-include-when-you-need-help) |
| Come è possibile risolvere un problema relativo alla reimpostazione della password? |[Risolvere i problemi relativi al portale di reimpostazione della password](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-portal) |
| Come è possibile risolvere un problema relativo al writeback delle password? |[Risolvere un problema relativo al writeback delle password](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Come è possibile risolvere un problema relativo alla connettività di writeback delle password? |[Risolvere problemi relativi alla connettività di writeback delle password](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) |
| Come è possibile risolvere un problema relativo alla configurazione della funzione di reimpostazione della password? |[Risolvere problemi relativi alla configurazione della funzione di reimpostazione della password nel portale di gestione di Azure](active-directory-passwords-troubleshoot.md#troubleshoot-password-reset-configuration-in-the-azure-management-portal) |
| Come è possibile risolvere un problema relativo ai report di reimpostazione della password? |[Risolvere problemi relativi ai report di gestione delle password nel portale di gestione di Azure](active-directory-passwords-troubleshoot.md#troubleshoot-password-management-reports-in-the-azure-management-portal) |
| Come è possibile risolvere un problema relativo alla registrazione per la reimpostazione della password? |[Risolvere i problemi relativi al portale di registrazione per la reimpostazione della password](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-registration-portal) |
| Codici errore del registro eventi di writeback della password |[Codici errore del registro eventi di writeback della password](active-directory-passwords-troubleshoot.md#password-writeback-event-log-error-codes) |

## Leggere una domanda frequente
| Argomento |  |
| --- | --- |
| Leggere una domanda frequente sulla registrazione per la reimpostazione della password |[Domanda frequente sulla registrazione per la reimpostazione della password](active-directory-passwords-faq.md#password-reset-registration) |
| Leggere una domanda frequente sulla reimpostazione della password |[Domanda frequente sulla reimpostazione della password](active-directory-passwords-faq.md#password-reset) |
| Leggere una domanda frequente sui report di reimpostazione della password |[Domanda frequente sui report di gestione delle password](active-directory-passwords-faq.md#password-management-reports) |
| Leggere una domanda frequente sul writeback delle password |[Domanda frequente sul writeback delle password](active-directory-passwords-faq.md#password-writeback) |

## Comprendere i dettagli tecnici
| Argomento |  |
| --- | --- |
| Ottenere informazioni sul writeback delle password |[Panoramica del writeback delle password](active-directory-passwords-learn-more.md#password-writeback-overview) |
| Ottenere informazioni sulle modalità di funzionamento del writeback delle password |[Come funziona il writeback delle password?](active-directory-passwords-learn-more.md#how-password-writeback-works) |
| Ottenere informazioni sugli scenari supportati dal writeback delle password |[Scenari supportati per il writeback delle password](active-directory-passwords-learn-more.md#scenarios-supported-for-password-writeback) |
| Ottenere informazioni sulle modalità di protezione del writeback delle password |[Modello di sicurezza del writeback della password](active-directory-passwords-learn-more.md#password-writeback-security-model) |
| Ottenere informazioni sulla modalità di funzionamento del portale di reimpostazione delle password |[Funzionamento del portale di reimpostazione delle password](active-directory-passwords-learn-more.md#how-does-the-password-reset-portal-work) |
| Ottenere informazioni sui dati usati dalla reimpostazione della password |[Dati usati per la reimpostazione della password](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |

## Aggiornamenti recenti del servizio
#### Implementare la registrazione per la reimpostazione della password all'accesso ad app di Office 365: novembre 2015
* Dopo aver abilitato la funzione di [registrazione applicata](active-directory-passwords-customize.md#require-users-to-register-when-signing-in), agli utenti verrà ora richiesto di registrarsi da qualsiasi punto di accesso usando un account aziendale o dell'istituto di istruzione. In questo modo, le aziende velocizzano considerevolmente il processo di adozione della funzione di reimpostazione della password. Alcune aziende di grandi dimensioni, ad esempio, hanno adottato questa nuova funzionalità in due sole settimane.

#### Supporto per lo sblocco di account di Active Directory senza la reimpostazione della password: novembre 2015
* Il semplice sblocco (senza reset) costituisce oggi un driver di supporto tecnico di grandi dimensioni. In molte aziende, infatti, fino al 70% del budget destinato alla reimpostazione della password viene impiegato per sbloccare gli account. Per risolvere questo problema, la funzione di reimpostazione della password di Azure AD consente ora di abilitare una funzionalità che offre agli utenti la possibilità di sbloccare gli account di Active Directory separatamente dalla reimpostazione della password. Per sapere come attivarla: [Impostazione: Consenti agli utenti di sbloccare gli account senza reimpostare la password](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password)

#### Aggiornamenti a livello di usabilità della pagina di registrazione: ottobre 2015
* Dopo che i dati di un utente sono stati registrati, sarà ora sufficiente fare clic su "esito positivo" per aggiornare i dati senza dover inviare nuovamente il messaggio di posta elettronica o effettuare una nuova telefonata.

#### Maggiore affidabilità del writeback delle password: settembre 2015
* A partire dalla versione di settembre di Azure AD Connect, l'agente di writeback delle password effettuerà ripetuti tentativi di connessione e disporrà di funzionalità di failover aggiuntive e più affidabili.

#### API per il recupero dei dati di report sulla reimpostazione della password: agosto 2015
* I dati relativi ai report sulla reimpostazione delle password possono ora essere recuperati direttamente dall'[API di Report ed eventi di Azure AD](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

#### Supporto per la reimpostazione delle password di Azure AD durante l'aggiunta a un dominio cloud: agosto 2015
* Qualsiasi utente cloud può ora reimpostare la password direttamente dalla schermata di accesso di Windows 10 durante l'esperienza di aggiunta a un dominio cloud. Si noti che questa funzionalità non è ancora esposta nella schermata di accesso di Windows 10.

#### Implementare la registrazione per la reimpostazione delle password all'accesso alle app di Azure e federate: luglio 2015
* Oltre all'implementazione della registrazione all'accesso a myapps.microsoft.com, è ora supportata l'implementazione della registrazione durante l'accesso al portale di gestione di Azure e alle applicazioni federate Single Sign-On

#### Supporto per la localizzazione della domanda di sicurezza: maggio 2015
* È ora possibile selezionare domande di sicurezza predefinite, localizzate nel set di lingue completo di O365 durante la configurazione delle domande di sicurezza per la reimpostazione delle password.

#### Supporto per lo sblocco dell'account durante la reimpostazione delle password: giugno 2015
* Se si usa il writeback delle password e si reimposta la password quando l'account è bloccato, l'account di Active Directory verrà automaticamente sbloccato.

#### Registrazione SSPR personalizzata: aprile 2015
* La pagina di registrazione per la reimpostazione delle password è ora personalizzata con il logo della società.

#### Domande di sicurezza: marzo 2015
* Le domande di sicurezza sono state rilasciate e sono disponibili a livello generale.

#### Sblocco dell’account: marzo 2015
* I nuovi utenti possono sbloccare i propri account quando si verifica la reimpostazione delle password

## Presto disponibile
Di seguito sono riportate alcune interessanti funzionalità in fase di elaborazione.

**Supporto per i promemoria relativi all'aggiornamento dei dati registrati durante l'accesso**: in fase di elaborazione

* Sono ora supportati i promemoria per gli utenti relativi all'aggiornamento dei dati registrati all'accesso a myapps.microsoft.com, ma la possibilità di estenderli a tutti gli accessi è ancora in fase di elaborazione.

## Collegamenti alla documentazione relativa alla reimpostazione della password
Di seguito vengono forniti collegamenti a tutte le pagine della documentazione relative alla reimpostazione della password in Azure AD:

* **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).
* [**Funzionamento**](active-directory-passwords-how-it-works.md): informazioni sui sei diversi componenti del servizio e sulle relative funzioni
* [**Introduzione**](active-directory-passwords-getting-started.md): informazioni su come consentire agli utenti di reimpostare e modificare le password cloud o locali
* [**Personalizzazione**](active-directory-passwords-customize.md): informazioni su come personalizzare l'aspetto e il comportamento del servizio in base alle esigenze dell'organizzazione
* [**Procedure consigliate**](active-directory-passwords-best-practices.md): informazioni su come distribuire rapidamente e gestire in modo efficace le password nell'organizzazione
* [**Informazioni dettagliate**](active-directory-passwords-get-insights.md): informazioni sulle funzionalità di creazione report integrate
* [**Domande frequenti**](active-directory-passwords-faq.md): risposte alle domande frequenti
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere rapidamente eventuali problemi con il servizio
* [**Ulteriori informazioni**](active-directory-passwords-learn-more.md): approfondimenti sui dettagli tecnici del funzionamento del servizio

<!---HONumber=AcomDC_0727_2016-->