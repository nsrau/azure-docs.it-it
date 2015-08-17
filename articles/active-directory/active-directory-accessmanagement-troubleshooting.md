
<properties 
	pageTitle="Risoluzione dei problemi di appartenenza dinamica per i gruppi | Microsoft Azure" 
	description="Argomento che elenca i suggerimenti relativi alla risoluzione dei problemi per l'appartenenza dinamica per i gruppi di Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="swadhwa" 
	editor="Curtis"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


# Risoluzione dei problemi di appartenenza dinamica ai gruppi

| Sintomo | Azione |
|--------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| È stata configurata una regola in un gruppo, ma nessuna appartenenza viene aggiornata nel gruppo | Verificare che l'opzione Abilita gestione gruppi delegata sia impostata su Sì nella scheda Configura della directory. Si noti che questa impostazione viene visualizzata solo se è stato effettuato l'accesso con un account che dispone di una licenza Azure Active Directory Premium. Verificare i valori degli attributi utente della regola: sono presenti utenti che soddisfano la regola? |
| È stata configurata una regola, ma i membri esistenti della regola sono stati rimossi | Si tratta di una situazione prevista: i membri esistenti del gruppo vengono rimosse quando una regola viene abilitata o modificata. Il set di utenti risultante dalla valutazione della regola viene aggiunto come membro al gruppo. |
| Quando si aggiunge o modifica una regola non vengono visualizzate immediatamente le modifiche a livello di appartenenza. Perché? | La valutazione dell'appartenenza dedicata viene eseguita periodicamente nell'ambito di un processo asincrono in background. Il numero di utenti nel tenant e la dimensione del gruppo creato in base alla regola sono fattori determinanti per la durata del processo. In genere, per i tenant con un numero limitato di utenti le modifiche a livello di appartenenza al gruppo verranno visualizzate nell'arco di pochi minuti. L'inserimento dei dati per i tenant con un numero elevato di utenti può richiedere intervalli maggiori o uguali a 30 minuti. |

Di seguito sono elencati alcuni argomenti contenenti informazioni aggiuntive su Azure Active Directory

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)

* [Informazioni su Azure Active Directory](active-directory-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=August15_HO6-->