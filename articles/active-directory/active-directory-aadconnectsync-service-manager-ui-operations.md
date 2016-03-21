<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: interfaccia utente di Synchronization Service Manager | Microsoft Azure"
	description="Comprendere la scheda Connettori in Synchronization Service Manager di Azure AD Connect."
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
	ms.date="03/03/2016"
	ms.author="andkjell"/>


# Servizio di sincronizzazione Azure AD Connect: Synchronization Service Manager

| [Operazioni](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Connettori](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Finestra di progettazione metaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Ricerca metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) |
| --- | --- | --- | --- |

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

La scheda Operazioni mostra i risultati delle ultime operazioni. Questa scheda è fondamentale per comprendere e risolvere i problemi.

## Comprendere le informazioni della scheda Operazioni
Nella metà superiore sono mostrate tutte le esecuzioni in ordine cronologico. Per impostazione predefinita il log delle operazioni mantiene le informazioni relative agli ultimi 7 giorni, ma è possibile modificare l’impostazione mediante l’[utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md). Si desidera cercare le esecuzioni che non hanno lo stato di operazione riuscita. È possibile modificare l'ordinamento facendo clic sulle intestazioni.

La colonna **Stato** mostra le informazioni più importanti e riporta il problema più grave di un’esecuzione. Ecco un breve riepilogo degli stati disponibili, ordinati in base alla priorità con cui vanno analizzati (dove * indica diverse stringhe di errore possibili).

| Stato | Commento |
| --- | --- |
| stopped-* | Impossibile completare l'esecuzione. Ad esempio se il sistema remoto è inattivo e non può essere contattato. |
| stopped-error-limit | Sono presenti più di 5.000 errori. L'esecuzione è stata arrestata automaticamente a causa dell'elevato numero di errori. |
| completed-*-errors | L’esecuzione è stata completata, ma sono presenti errori (meno di 5.000) che devono essere analizzati. | | completed-*-warnings | L'esecuzione è stata completata, ma alcuni dati non si trovano nello stato previsto. Se sono presenti errori, questo in genere è solo un sintomo. Evitare di analizzare gli avvisi fino a quando gli errori non sono stati risolti. |
| esito positivo | Non sono presenti problemi. |

Quando si seleziona una riga, la parte inferiore viene aggiornata per visualizzare i dettagli di quella esecuzione. All'estrema sinistra della parte inferiore è possibile avere un elenco con voci del tipo **Passaggio #**. L’elenco è mostrato solo quando più domini sono presenti nella foresta e ogni dominio è rappresentato da un passaggio. Il nome di dominio è visibile sotto l'intestazione **Partizione**. Sotto **Statistiche di sincronizzazione** sono disponibili ulteriori informazioni sul numero di modifiche che sono state elaborate. È possibile fare clic sui collegamenti per ottenere un elenco degli oggetti modificati. Se ci sono oggetti con un errore, questi saranno mostrati sotto **Errori di sincronizzazione**.

## Risolvere gli errori nella scheda Operazioni
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png) Nel caso di errori, sia l'oggetto con l’errore sia l'errore stesso sono collegamenti che mostreranno ulteriori informazioni.

Per iniziare, fare clic su una stringa di errore (nella figura precedente **sync-rule-error-function-triggered**). Verrà innanzitutto presentata una panoramica dell'oggetto. Per visualizzare l'errore effettivo, fare clic sul pulsante **Analisi dello stack**. Verranno mostrate informazioni di debug relative all'errore.

**SUGGERIMENTO:** è possibile fare clic con il pulsante destro del mouse nella casella **Informazioni sullo stack delle chiamate**, scegliere **Seleziona tutto** e **Copia**. Si può quindi copiare lo stack ed esaminare l'errore nell'editor preferito, ad esempio il Blocco note.

- Se l’errore proviene da **SyncRulesEngine**, le informazioni sullo stack delle chiamate mostreranno prima di tutto un elenco di tutti gli attributi dell’oggetto. Scorrere verso il basso fino all'intestazione **InnerException =>**. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png) Sulla riga successiva è mostrato l'errore. Nell'immagine precedente l'errore proviene da una regola di sincronizzazione personalizzata creata da Fabrikam.

Se l'errore non fornisce informazioni sufficienti, è necessario esaminare i dati. È possibile fare clic sul collegamento con l'identificatore di oggetto e [seguire un oggetto e i relativi dati attraverso il sistema](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0309_2016-->