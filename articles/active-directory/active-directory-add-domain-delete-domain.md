<properties
	pageTitle="Eliminare un dominio personalizzato in Azure Active Directory | Microsoft Azure"
	description="Come eliminare un dominio personalizzato in Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>


# Eliminazione di un nome di dominio personalizzato

È possibile eliminare un nome di dominio personalizzato che non è più necessario usare con la directory AD Azure. Ciò può verificarsi ad esempio se il nome dell'azienda è cambiato o si usa una directory Azure AD diversa. È inoltre possibile eliminare un dominio non verificato, ad esempio se si scopre dopo averlo aggiunto di non aver digitato correttamente il nome o di non aver impostato il valore corretto per la federazione del dominio.

## Prima di iniziare

Prima di rimuovere un nome di dominio, è consigliabile leggere le informazioni seguenti:

- Il nome di dominio contoso.onmicrosoft.com originale fornito per la directory al momento dell'iscrizione non può essere rimosso.
- Qualsiasi dominio di primo livello a cui sono associati sottodomini non può essere rimosso fino a quando non vengono rimossi i sottodomini. Ad esempio, non è possibile rimuovere adatum.com se è presente corp.adatum.com o un altro sottodominio che usa il nome di dominio di primo livello. Per altre informazioni, vedere l'articolo del supporto tecnico ["Il dominio dispone di sottodomini associati" o "Non è possibile rimuovere un dominio con sottodomini" quando si tenta di rimuovere un dominio da Office 365](https://support.microsoft.com/kb/2787792/).
- Se è stata attivata la sincronizzazione della directory, all'account è stato aggiunto automaticamente un dominio simile al seguente: contoso.mail.onmicrosoft.com. Questo nome di dominio non può essere rimosso.
- Per poter rimuovere un nome di dominio, è innanzitutto necessario rimuovere il nome di dominio da tutti gli account utente o di posta elettronica associati al dominio. È possibile rimuovere tutti gli account oppure modificare in blocco gli account utente per cambiare le informazioni sul nome di dominio e gli indirizzi di posta elettronica. Per altre informazioni, vedere [Creare o modificare utenti in Azure AD](active-directory-create-users.md). Ricordarsi di rimuovere:

	-   Tutti gli utenti con il dominio nel nome utente o nell'indirizzo di posta elettronica

	-   Tutti i gruppi abilitati alla posta con il dominio nell'indirizzo di posta elettronica

	-   Tutte le applicazioni con il dominio nell'URL di risposta

- Se si ospita un sito di SharePoint Online con un nome di dominio in uso per una raccolta siti di SharePoint Online, è prima necessario eliminare la raccolta siti per poter rimuovere il nome di dominio.

## Per eliminare un dominio

1.  Accedere al portale di Azure classico usando un account con privilegi di amministratore globale per tale directory.

2.  Aprire la directory e selezionare **Domini**.

3.  Selezionare il dominio e fare clic su Elimina.

## Passaggi successivi

- [Uso di nomi di dominio personalizzati per semplificare l'esperienza di accesso degli utenti](active-directory-add-domain.md)
- [Aggiungere informazioni personalizzate distintive dell'azienda alle pagine Accedi e Pannello di accesso](active-directory-add-company-branding.md)
- [Aggiungere e verificare un nome di dominio personalizzato in Azure Active Directory](active-directory-add-domain-add-verify-general.md)
- [Assegnare utenti a un dominio personalizzato](active-directory-add-domain-add-users.md)
- [Cosa fare se si modifica il registrar DNS per il nome di dominio personalizzato](active-directory-add-domain-change-registrar.md)

<!---HONumber=AcomDC_0211_2016-->