<properties
	pageTitle="Creazione di una regola semplice per configurare le appartenenze dinamiche per un gruppo | Microsoft Azure"
	description="Viene illustrato come creare una regola semplice per configurare le appartenenze dinamiche per un gruppo."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="curtand"/>


# Creazione di una regola semplice per configurare le appartenenze dinamiche a un gruppo

Per abilitare l'appartenenza dinamica per un determinato gruppo, effettuare le seguenti operazioni:

1. Nel portale di Azure, nella scheda **Gruppi** selezionare il gruppo che si desidera modificare e quindi nella scheda **Configura** di questo gruppo impostare l'opzione **Abilita appartenenze dinamiche** su **Sì**.

2. A questo punto, è possibile impostare una regola semplice per il gruppo che consenta di controllare il funzionamento dell'appartenenza dinamica per questo gruppo. Assicurarsi che l’opzione **Aggiungi utenti in** sia selezionata e quindi selezionare una proprietà utente dall’elenco, ad esempio department, jobTitle e così via.

3. Selezionare una condizione (Non uguale a, Uguale a, Non inizia con, Inizia con, Non contiene, Contiene, Non corrispondente, Corrispondente) e quindi specificare un valore per la proprietà utente selezionata. Ad esempio, se un gruppo viene assegnato a un'applicazione SaaS e vengono abilitate le appartenenze dinamiche per questo gruppo impostando una regola in base alla quale l'opzione **Aggiungi utenti in** viene impostata sul titolo mansione (jobTitle) uguale a (-eq) Rappresentante, tutti gli utenti all'interno della directory di Azure AD la cui mansione è impostata su Rappresentante avranno accesso all'applicazione SaaS.

4. Si noti che è possibile configurare una regola per l'appartenenza dinamica nei gruppi di sicurezza o nei gruppi di Office. Le appartenenze dinamiche per i gruppi richiedono una licenza Azure AD Premium da assegnare all'amministratore che gestisce la regola in un gruppo e a tutti gli utenti selezionati dalla regola come membri del gruppo.

Per altre informazioni sulle regole complesse dell'appartenenza dinamica ai gruppi, vedere:

* [Uso di attributi per la creazione di regole avanzate](active-directory-accessmanagement-groups-with-advanced-rules.md)

Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Informazioni su Azure Active Directory](active-directory-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0224_2016-->