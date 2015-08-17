<properties

	pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
	description="Covers how to sign up for Azure and first steps you can try with Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


#Gestione dei gruppi di sicurezza in Azure Active Directory


##Come creare e gestire un gruppo di sicurezza

**Per creare un gruppo dal portale di gestione di Azure**

1. Nel portale di gestione fare clic su Active Directory e quindi sul nome della directory dell'organizzazione.
2. Fare clic sulla scheda **Gruppi**.
3. Nella pagina Gruppi fare clic su **Aggiungi gruppo**.
4. Nella finestra **Aggiungi gruppo** specificare il nome e la descrizione del gruppo.
5. Questa attività può essere completata usando il portale dell'account di Office 365, il portale dell'account Windows Intune o il portale di gestione di Azure, a seconda dei servizi sottoscritti dall'organizzazione. Per altre informazioni sull'uso dei portali per gestire Azure Active Directory, vedere Amministrazione della directory di Azure AD.

## Come assegnare o rimuovere utenti in un gruppo di sicurezza

**Per aggiungere un membro a un gruppo dal portale di gestione di Azure**

1. Nel portale di gestione fare clic su Active Directory e quindi sul nome della directory dell'organizzazione.
2. Fare clic sulla scheda **Gruppi**.
3. Nella pagina **Gruppi** fare clic sul nome del gruppo a cui si desidera aggiungere membri. Per impostazione predefinita, verrà visualizzata la scheda **Membri** del gruppo selezionato.
4. Nella pagina del gruppo fare clic su**Aggiungi membri**.
5. Nella pagina **Aggiungi membri** fare clic sul nome dell'utente o gruppo che si desidera aggiungere come membro del gruppo corrente e quindi verificare che questo nome venga aggiunto al riquadro Selezionato.


**Per rimuovere un membro da un gruppo dal portale di gestione di Azure**

1. Nel portale di gestione fare clic su Active Directory e quindi sul nome della directory dell'organizzazione.
2. Fare clic sulla scheda **Gruppi**.
3. Nella pagina Gruppi fare clic sul nome del gruppo da cui si desidera rimuovere i membri.
4. Nella pagina del gruppo fare clic sulla scheda **Membri**.
5. Nella pagina del gruppo fare clic sul nome del membro che si desidera rimuovere dal gruppo e quindi fare clic su **Rimuovi**.
6. Confermare che si desidera rimuovere il membro dal gruppo facendo clic su **Sì** come risposta alla domanda di verifica dell'azione.


##Uso di una regola per gestire in modo dinamico i membri di un gruppo di sicurezza
**Per abilitare l'appartenenza dinamica per un determinato gruppo, effettuare le seguenti operazioni:**

1. Nel portale di gestione di Azure, nella scheda **Gruppi** selezionare il gruppo che si desidera modificare e quindi nella scheda **Configura** di questo gruppo impostare l'opzione **Abilita appartenenze dinamiche** su **Sì**.
2. A questo punto, è possibile impostare una regola semplice per il gruppo che consenta di controllare il funzionamento dell'appartenenza dinamica per questo gruppo. Assicurarsi che il pulsante di opzione **Aggiungi utenti in** sia selezionato e quindi selezionare una proprietà dal menu a discesa, ad esempio department, jobTitle e così via. 
3. Selezionare una condizione (Non uguale a, Uguale a, Non inizia con, Inizia con, Non contiene, Contiene, Non corrispondente, Corrispondente) e quindi specificare un valore per la proprietà utente selezionata.
4. Ad esempio, se un gruppo viene assegnato a un'applicazione SaaS (per altre informazioni, vedere Assegnare a un gruppo l'accesso a un'applicazione SaaS in Azure AD) e si abilitano le appartenenze dinamiche per questo gruppo impostando una regola in base alla quale l'opzione Aggiungi utenti in viene impostata sul titolo mansione (jobTitle) uguale a (-eq) Rappresentante, tutti gli utenti all'interno della directory di Azure AD la cui mansione è impostata su Rappresentante avranno accesso all'applicazione SaaS.

Di seguito sono elencati alcuni argomenti contenenti informazioni aggiuntive su Azure Active Directory

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)

* [Informazioni su Azure Active Directory](active-directory-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=August15_HO6-->