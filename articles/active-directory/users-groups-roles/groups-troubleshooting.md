---
title: Risolvere i problemi relativi alle appartenenze dinamiche ai gruppi-Azure AD | Microsoft Docs
description: Suggerimenti per la risoluzione dei problemi relativi all'appartenenza dinamica ai gruppi in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f685ac63e3b4a8cf466be4eb4561472fb084d49
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026551"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Risolvere i problemi relativi ai gruppi

## <a name="troubleshooting-group-creation-issues"></a>Risoluzione dei problemi di creazione del gruppo

**La creazione del gruppo di sicurezza è stata disabilitata nel portale di Azure ma i gruppi possono comunque essere creati tramite PowerShell** L' **utente può creare gruppi di sicurezza nell'impostazione dei portali di Azure** nell'portale di Azure controlla se gli utenti non amministratori possono creare o meno gruppi di sicurezza nel pannello di accesso o nel portale di Azure. Non controlla la creazione di gruppi di sicurezza tramite PowerShell.

Per disabilitare la creazione di gruppi per gli utenti non amministratori in PowerShell:
1. Verificare che gli utenti non amministratori siano autorizzati a creare gruppi:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Se restituisce `UsersPermissionToCreateGroupsEnabled : True`, gli utenti non amministratori possono creare gruppi. Per disabilitare questa funzionalità:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**È stato ricevuto un errore massimo di gruppi consentiti durante il tentativo di creare un gruppo dinamico in PowerShell**<br/>
Se viene visualizzato un messaggio in PowerShell che indica _criteri di gruppo dinamici numero massimo di gruppi consentiti raggiunto_, significa che è stato raggiunto il limite massimo per i gruppi dinamici nel tenant. Il numero massimo di gruppi dinamici per tenant è 5.000.

Per creare nuovi gruppi dinamici, è necessario innanzitutto eliminare alcuni gruppi dinamici esistenti. Non è possibile aumentare il limite.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Risoluzione dei problemi di appartenenza dinamica per i gruppi

**È stata configurata una regola su un gruppo, ma nessuna appartenenza viene aggiornata nel gruppo**<br/>
1. Verificare i valori per gli attributi utente o dispositivo nella regola. Verificare che siano presenti utenti che soddisfano la regola. Per i dispositivi, controllare le proprietà del dispositivo per assicurarsi che tutti gli attributi sincronizzati includano i valori previsti.<br/>
2. Verificare lo stato di elaborazione dell'appartenenza per verificare che sia stato completato. È possibile controllare lo [stato di elaborazione dell'appartenenza](groups-create-rule.md#check-processing-status-for-a-rule) e la data dell'ultimo aggiornamento nella pagina **Panoramica** del gruppo.

Se non vengono rilevati problemi, attendere il popolamento del gruppo. A seconda delle dimensioni del tenant, potrebbero essere necessarie fino a 24 ore per eseguire il popolamento per la prima volta o dopo una modifica di una regola.

**È stata configurata una regola, ma i membri esistenti della regola sono stati rimossi**<br/>Si tratta di un comportamento previsto. I membri esistenti del gruppo vengono rimosse quando una regola viene abilitata o modificata. Gli utenti restituiti dalla valutazione della regola vengono aggiunti come membri al gruppo.

**Quando si aggiunge o modifica una regola non vengono visualizzate immediatamente le modifiche a livello di appartenenza. Perché?**<br/>La valutazione dell'appartenenza dedicata viene eseguita periodicamente in un processo asincrono in background. La durata del processo è determinata dal numero di utenti nella directory e dalle dimensioni del gruppo creato in base alla regola. In genere, per le directory con un numero limitato di utenti le modifiche a livello di appartenenza al gruppo verranno visualizzate nell'arco di pochi minuti. L'inserimento dei dati per le directory con un numero elevato di utenti può richiedere intervalli maggiori o uguali a 30 minuti.

**Come è possibile forzare l'elaborazione del gruppo adesso?**<br/>
Attualmente, non è possibile attivare automaticamente il gruppo da elaborare su richiesta. Tuttavia, è possibile attivare manualmente la rielaborazione aggiornando la regola di appartenenza per aggiungere uno spazio vuoto alla fine.  

**Si è verificato un errore di elaborazione delle regole**<br/>La tabella seguente elenca gli errori comuni della regola di appartenenza dinamica con la relativa risoluzione.

| Errore del parser della regola | Uso errato | Uso corretto |
| --- | --- | --- |
| Errore: l'attributo non è supportato |(user.invalidProperty -eq "Valore") |(user.department -eq "valore")<br/><br/>Verificare che l'attributo sia incluso nell'[elenco di proprietà supportate](groups-dynamic-membership.md#supported-properties). |
| Errore: l'operatore non è supportato sull'attributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>L'operatore usato non è supportato per il tipo di proprietà, in questo esempio contains non può essere usato nel tipo booleano. Usare gli operatori corretti per il tipo di proprietà. |
| Errore: si è verificato un errore di compilazione della query. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2.  (user.userPrincipalName -match "*@domain.ext") | 1. operatore mancante. Usare i due predicati di join -and oppure -or<br>(user.department -eq "Vendite") -or (user.department -eq "Marketing")<br>2. errore nell'espressione regolare usata con-match<br>(user.userPrincipalName -match ".*@domain.ext")<br>in alternativa: (user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>Passaggi successivi

Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Gestione di applicazioni con Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Informazioni su Azure Active Directory](../fundamentals/active-directory-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md)