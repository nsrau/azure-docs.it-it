<properties 
	pageTitle="Cronologia delle versioni di Azure AD Connect Health" 
	description="Questo documento descrive le versioni di Azure AD Connect Health e ciò che includono." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/08/2016" 
	ms.author="billmath"/>

# Azure AD Connect Health: cronologia delle versioni

Il team di Azure Active Directory aggiorna regolarmente Azure AD Connect Health con nuove funzionalità.

Questo articolo permette di tenere traccia delle versioni che sono state rilasciate.


## Luglio 2016

**Nuove funzionalità di anteprima:**

- [Azure AD Connect Health per Servizi di dominio Active Directory](active-directory-aadconnect-health-adds.md).


## Gennaio 2016


**Aggiornamento dell'agente:**

- Agente di Azure AD Connect Health per AD FS (versione 2.6.91.1512)


**Nuove funzionalità:**

- [Strumento di test della connettività per agenti di Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)


## Novembre 2015


**Nuove funzionalità:**

- Supporto per il [Controllo degli accessi in base al ruolo](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)


**Nuove funzionalità di anteprima:**

- [Azure AD Connect Health per la sincronizzazione](active-directory-aadconnect-health-sync.md).

**Problemi risolti:**

- Correzioni di bug per errori visualizzati durante le registrazioni di agenti.

## Settembre 2015

**Nuove funzionalità:**

- Report delle password del nome utente per AD FS errato
- Supporto per la configurazione del proxy HTTP non autenticato
- Supporto per la configurazione dell'agente nel Server Core
- Miglioramenti agli avvisi per AD FS
- Miglioramenti all'agente di Azure AD Connect Health per AD FS per la connettività e il caricamento di dati.


**Problemi risolti:**

- Correzioni di bug per i tipi di errore nei dati sull'utilizzo di AD FS.


## Giugno 2015

**Versione iniziale di Azure AD Connect Health per AD FS e proxy AD FS.**

**Nuove funzionalità:**

- Avvisi per il monitoraggio di AD FS e dei server proxy AD FS con notifiche di posta elettronica.
- Facilità di accesso alla topologia di AD FS e ai modelli nei contatori delle prestazioni di AD FS.
- Tendenze delle richieste di token con esito positivo nei server AD FS raggruppate per applicazioni, metodi di autenticazione, percorso di rete delle richieste e così via.
- Tendenze delle richieste con esito negativo nei server AD FS raggruppate per applicazioni, tipi di errore e così via.
- Distribuzione più semplice dell'agente con le credenziali di amministratore globale di Azure AD.




## Passaggi successivi
Informazioni su come [monitorare l'infrastruttura di gestione delle identità locale e i servizi di sincronizzazione nel cloud](active-directory-aadconnect-health.md).

<!---HONumber=AcomDC_0810_2016-->