---
title: 'Servizio di sincronizzazione Azure AD Connect: Interfaccia utente di Synchronization Service Manager | Documentazione Microsoft'
description: Comprendere la scheda Connettori in Synchronization Service Manager di Azure AD Connect.
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a9509b159941c9784fb91f624312f11fe0af2818


---
# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Servizio di sincronizzazione Azure AD Connect: Synchronization Service Manager
| [Operazioni](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Connectors (Connettori) (Connettori)](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Finestra di progettazione metaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Ricerca metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) |
| --- | --- | --- | --- |
|  | | | |

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

La scheda Operazioni mostra i risultati delle ultime operazioni. Questa scheda è fondamentale per comprendere e risolvere i problemi.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Comprendere le informazioni della scheda Operazioni
Nella metà superiore sono mostrate tutte le esecuzioni in ordine cronologico. Per impostazione predefinita, il log delle operazioni mantiene le informazioni relative agli ultimi sette giorni, ma è possibile modificare questa impostazione tramite l' [utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md). Si vogliono cercare le esecuzioni che non hanno lo stato di operazione riuscita. È possibile modificare l'ordinamento facendo clic sulle intestazioni.

La colonna **Status** (Stato) visualizza le informazioni più importanti e segnala il problema più grave di un'esecuzione. Ecco un breve riepilogo degli stati disponibili, ordinati in base alla priorità con cui vanno analizzati (dove * indica diverse stringhe di errore possibili).

| Status | Commento |
| --- | --- |
| stopped-* |Impossibile completare l'esecuzione. Ad esempio se il sistema remoto è inattivo e non può essere contattato. |
| stopped-error-limit |Sono presenti più di 5.000 errori. L'esecuzione è stata arrestata automaticamente a causa dell'elevato numero di errori. |
| completato-\*-errori |L'esecuzione è stata completata, ma sono presenti errori (meno di 5.000) che devono essere analizzati. |
| completato-\*-avvisi |L'esecuzione è stata completata, ma alcuni dati non si trovano nello stato previsto. Se sono presenti errori, questo messaggio è in genere solo un sintomo. Evitare di analizzare gli avvisi fino a quando gli errori non sono stati risolti. |
| esito positivo |Non sono presenti problemi. |

Quando si seleziona una riga, la parte inferiore viene aggiornata per visualizzare i dettagli di quella esecuzione. All'estrema sinistra della parte inferiore è possibile avere un elenco con voci del tipo **Step #**(Passaggio [n.]). L'elenco è mostrato solo quando nella foresta sono presenti più domini e ogni dominio è rappresentato da un passaggio. Il nome di dominio è visibile sotto l'intestazione **Partition**(Partizione). In **Synchronization Statistics**(Statistiche di sincronizzazione) sono presenti altre informazioni sul numero delle modifiche elaborate. È possibile fare clic sui collegamenti per ottenere un elenco degli oggetti modificati. Se alcuni oggetti presentano errori, questi verranno visualizzati in **Synchronization Errors**(Errori di sincronizzazione).

## <a name="troubleshoot-errors-in-operations-tab"></a>Risolvere gli errori nella scheda Operazioni
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png)  
 In caso di errori, sia l'oggetto con l'errore che l'errore stesso appaiono sotto forma di collegamenti per la visualizzazione di altre informazioni.

Per iniziare, fare clic su una stringa di errore. Nella figura si tratta di **sync-rule-error-function-triggered**. Verrà prima presentata una panoramica dell'oggetto. Per visualizzare l'errore effettivo, fare clic sul pulsante **Stack Trace** (Analisi dello stack). Questa analisi indica informazioni di debug relative all'errore.

**SUGGERIMENTO:** è possibile fare clic con il pulsante destro del mouse nella casella delle **informazioni sullo stack delle chiamate**, scegliere **select all** (seleziona tutto) e quindi **copy** (copia). Si può quindi copiare lo stack ed esaminare l'errore nell'editor preferito, ad esempio il Blocco note.

* Se l'errore proviene da **SyncRulesEngine**, le informazioni sullo stack delle chiamate visualizzano prima di tutto un elenco di tutti gli attributi dell'oggetto. Scorrere verso il basso fino all'intestazione **InnerException =>**.  
  ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png)  
   L'errore è visualizzato nella riga successiva. Nell'immagine precedente l'errore proviene da una regola di sincronizzazione personalizzata creata da Fabrikam.

Se l'errore non fornisce informazioni sufficienti, è necessario esaminare i dati. È possibile fare clic sul collegamento con l'identificatore di oggetto e [seguire un oggetto e i relativi dati attraverso il sistema](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).




<!--HONumber=Nov16_HO3-->


