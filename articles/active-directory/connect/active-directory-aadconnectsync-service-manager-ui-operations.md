---
title: Operazioni di Synchronization Service Manager per Azure AD Connect | Microsoft Docs
description: Comprendere la scheda Connettori in Synchronization Service Manager di Azure AD Connect.
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 309a4ca2d5ef929bb50ed899f24709c062d7db29
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Uso della scheda Operazioni di Synchronization Service Manager

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

La scheda Operazioni mostra i risultati delle ultime operazioni. Questa scheda è fondamentale per comprendere e risolvere i problemi.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Comprendere le informazioni della scheda Operazioni
La metà superiore mostra tutte le esecuzioni in ordine cronologico. Per impostazione predefinita, il log delle operazioni mantiene le informazioni relative agli ultimi sette giorni, ma è possibile modificare questa impostazione tramite l' [utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md). Si vogliono cercare le esecuzioni che non hanno lo stato di operazione riuscita. È possibile modificare l'ordinamento facendo clic sulle intestazioni.

La colonna **Status** (Stato) visualizza le informazioni più importanti e segnala il problema più grave di un'esecuzione. Ecco un breve riepilogo degli stati disponibili, ordinati in base alla priorità con cui vanno analizzati (dove * indica diverse stringhe di errore possibili).

| Status | Comment |
| --- | --- |
| stopped-\* |Impossibile completare l'esecuzione. Ad esempio se il sistema remoto è inattivo e non può essere contattato. |
| stopped-error-limit |Sono presenti più di 5.000 errori. L'esecuzione è stata arrestata automaticamente a causa dell'elevato numero di errori. |
| completato-\*-errori |L'esecuzione è stata completata, ma sono presenti errori (meno di 5.000) che devono essere analizzati. |
| completato-\*-avvisi |L'esecuzione è stata completata, ma alcuni dati non si trovano nello stato previsto. Se sono presenti errori, questo messaggio è in genere solo un sintomo. Evitare di analizzare gli avvisi fino a quando gli errori non sono stati risolti. |
| esito positivo |Non sono presenti problemi. |

Quando si seleziona una riga, la parte inferiore viene aggiornata per visualizzare i dettagli di quella esecuzione. All'estrema sinistra della parte inferiore è possibile avere un elenco con voci del tipo **Step #**(Passaggio [n.]). L'elenco è mostrato solo quando nella foresta sono presenti più domini e ogni dominio è rappresentato da un passaggio. Il nome di dominio è visibile sotto l'intestazione **Partition**(Partizione). In **Synchronization Statistics**(Statistiche di sincronizzazione) sono presenti altre informazioni sul numero delle modifiche elaborate. È possibile fare clic sui collegamenti per ottenere un elenco degli oggetti modificati. Se alcuni oggetti presentano errori, questi verranno visualizzati in **Synchronization Errors**(Errori di sincronizzazione).

Per altre informazioni, vedere [Risoluzione dei problemi relativi a un oggetto che non esegue la sincronizzazione in Azure AD](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
