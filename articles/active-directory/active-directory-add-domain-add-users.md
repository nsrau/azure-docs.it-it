<properties
	pageTitle="Assegnare gli utenti a un dominio personalizzato in Azure Active Directory | Microsoft Azure"
	description="Come popolare un dominio personalizzato in Azure Active Directory con gli account utente."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/21/2016"
	ms.author="curtand;jeffsta"/>

# Assegnare utenti a un dominio personalizzato

Dopo aver aggiunto il dominio personalizzato in Azure Active Directory, è necessario aggiungere gli account utente del dominio per poter iniziare l'autenticazione.

## Utenti sincronizzati da una directory nella rete aziendale

Se è già stata impostata una connessione tra Active Directory locale e Azure Active Directory, la sincronizzazione può popolare gli account. Per altre informazioni sulla sincronizzazione di Azure Active Directory con Active Directory locale, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

## Utenti aggiunti e gestiti nel cloud

Per modificare il dominio di un account utente esistente:

1.  Aprire il portale di Azure classico usando un account amministratore globale o amministratore utenti.

2.  Aprire la directory.

3.  Selezionare la scheda **Utenti**,

4.  Selezionare l'utente dall'elenco.

5.  Modificare il dominio dell'utente e quindi selezionare **Salva**.

Questa operazione può essere eseguita anche usando [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) o l'[API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## Selezionare un dominio personalizzato durante la creazione di un nuovo utente

1.  Aprire il portale di Azure classico usando un account amministratore globale o amministratore utenti.

2.  Aprire la directory.

3.  Selezionare la scheda **Utenti**,

4.  Sulla barra dei comandi selezionare **Aggiungi**.

5.  Quando si aggiunge il nome utente, scegliere il dominio personalizzato dall'elenco di domini.

6.  Selezionare **Salva**.

## Passaggi successivi

-   [Uso di nomi di dominio personalizzati per semplificare l'esperienza di accesso degli utenti](active-directory-add-domain.md)

-   [Gestire i nomi di dominio personalizzati](active-directory-add-manage-domain-names.md)

-   [Informazioni sui concetti relativi alla gestione dei domini in Azure AD](active-directory-add-domain-concepts.md)

<!---HONumber=AcomDC_0622_2016-->