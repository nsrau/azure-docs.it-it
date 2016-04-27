<properties
	pageTitle="Aggiornamento da DirSync e Azure AD Sync | Microsoft Azure"
	description="Informazioni su come eseguire l'aggiornamento da DirSync e Azure AD Sync ad Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/13/2016"
	ms.author="andkjell"/>


# Aggiornamento del servizio di sincronizzazione di Azure Active Directory ("DirSync") e Azure Active Directory Sync ("Azure AD Sync")
Azure AD Connect è il modo migliore per collegare la directory locale con Azure AD e Office 365. Questo è il momento giusto per eseguire l'aggiornamento ad Azure AD Connect dallo strumento di sincronizzazione di Windows Azure Active Directory (DirSync) o Azure AD Sync in quanto questi strumenti vengono deprecati e il supporto terminerà il 13 aprile 2017.

I due strumenti di sincronizzazione delle identità deprecati erano disponibili per i clienti a foresta singola (DirSync) e per i clienti avanzati e con più foreste (Azure AD Sync). Gli strumenti obsoleti sono stati sostituiti con un'unica soluzione disponibile per tutti gli scenari: Azure AD Connect. Questa soluzione offre nuove funzionalità, miglioramenti e supporto per nuovi scenari. Per poter continuare a sincronizzare i dati delle identità locali con Azure AD e Office 365, è consigliabile eseguire l'aggiornamento ad Azure AD Connect.

L'ultima versione di DirSync è stata rilasciata a luglio 2014, mentre l'ultima versione di Azure AD Sync è stato rilasciata nel mese di maggio 2015.

## Cos'è Azure AD Connect
Azure AD Connect è il successore di DirSync e Azure AD Sync. La soluzione combina tutti gli scenari dei due strumenti supportati. Per altre informazioni sull'argomento, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

## Pianificazione della deprecazione

|Data | Commento |
| --- | --- |
| 13 aprile 2016 | Annuncio della deprecazione dei servizi di sincronizzazione di Azure Active Directory ("DirSync") e Microsoft Azure Active Directory Sync ("Azure AD Sync"). |
| 13 aprile 2017 | Termine del supporto. I clienti non potranno più aprire un caso di supporto senza prima eseguire l'aggiornamento ad Azure AD Connect. |

## Come eseguire la transizione ad Azure AD Connect
Se si utilizza DirSync, è possibile eseguire l'aggiornamento in due modi: aggiornamento sul posto e distribuzione in parallelo. L'aggiornamento sul posto è consigliato per la maggior parte dei clienti e se si dispone di un sistema operativo recente con meno di 50.000 oggetti. In altri casi è consigliabile eseguire una distribuzione in parallelo in cui la configurazione di DirSync viene trasferita su un nuovo server che esegue Azure AD Connect.

Se si utilizza Azure AD Sync è consigliabile un aggiornamento sul posto. Se si desidera, è possibile installare un nuovo server Azure AD Connect in parallelo ed eseguire una migrazione swing dal server Azure AD Sync ad Azure AD Connect.

| Soluzione | Scenario |
| ----- | ----- |
| [Aggiornamento da DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>Se è presente un server DirSync esistente già in esecuzione.</li>
| [Aggiornamento da Azure AD Sync](active-directory-aadconnect-upgrade-previous-version.md)| <li>Se si esegue l'aggiornamento da Azure AD Sync.</li>

Per istruzioni su come eseguire un aggiornamento sul posto da DirSync ad Azure AD Connect, vedere questo video del Channel 9:

<!-- The next section is actually supposed to look like this, but video ids are not working at present.
> [AZURE.VIDEO azure-active-directory-connect-in-place-upgrade-from-legacy-tools]
-->

[Azure Active Directory Connect: in-place upgrade from legacy tools (Azure Active Directory Connect: aggiornamento sul posto dagli strumenti legacy)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools) ![aggiornamento sul posto](./media/active-directory-aadconnect-dirsync-deprecated/inplaceupgrade.png)

## Domande frequenti
**D: DirSync/Azure AD Sync smetteranno di funzionare il 13 aprile 2017?** No. La data di dismissione della comunicazione di questi strumenti Azure AD verrà annunciata in un secondo momento. Quando disponibile, questa informazione sarà visualizzata in questo argomento.

**D: E per quanto riguarda il connettore Azure AD per FIM/MIM?** Al momento, per il connettore di Azure AD per FIM/MIM **non** è prevista la deprecazione. Si tratta di un **blocco della funzionalità**: non viene aggiunta alcuna nuova funzionalità e non si riceveranno correzioni dei bug. Microsoft consiglia ai clienti che ne fanno uso di pianificare la transizione ad Azure AD Connect. È consigliabile non avviare nuove distribuzioni tramite questo strumento. Nel futuro il connettore verrà deprecato.

## Risorse aggiuntive

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0413_2016-->