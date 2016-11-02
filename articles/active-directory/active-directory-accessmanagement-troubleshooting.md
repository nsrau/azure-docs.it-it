
<properties
	pageTitle="Risoluzione dei problemi di appartenenza dinamica per i gruppi | Microsoft Azure"
	description="Suggerimenti per la risoluzione dei problemi di appartenenza dinamica per i gruppi di Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""
	/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="curtand"/>


# Risoluzione dei problemi di appartenenza dinamica per i gruppi

**Ho configurato una regola in un gruppo ma le appartenenze nel gruppo non vengono aggiornate**<br/>Verificare che l'impostazione **Abilita gestione gruppi delegata** sia impostata su **Sì** nella scheda **Configura**. Questa impostazione verrà visualizzato solo se si è connessi come utente a cui è assegnata una licenza di Azure Active Directory Premium. Verificare i valori degli attributi utente della regola: sono presenti utenti che soddisfano la regola?

**Ho configurato una regola, ma i membri esistenti della regola sono stati rimossi**<br/>Si tratta di un comportamento previsto. I membri esistenti del gruppo vengono rimosse quando una regola viene abilitata o modificata. Gli utenti restituiti dalla valutazione della regola vengono aggiunti come membri al gruppo.

**Quando aggiungo o modifico una regola non visualizzo immediatamente le modifiche all'appartenenza. Perché?**<br/>La valutazione dell'appartenenza dedicata viene eseguita periodicamente in un processo asincrono in background. La durata del processo è determinata dal numero di utenti nella directory e dalle dimensioni del gruppo creato in base alla regola. In genere, per le directory con un numero limitato di utenti le modifiche a livello di appartenenza al gruppo verranno visualizzate nell'arco di pochi minuti. L'inserimento dei dati per le directory con un numero elevato di utenti può richiedere intervalli maggiori o uguali a 30 minuti.

Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Informazioni su Azure Active Directory](active-directory-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0817_2016-->