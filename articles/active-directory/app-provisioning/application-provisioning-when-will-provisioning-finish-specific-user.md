---
title: Scopri quando un utente specifico sarà in grado di accedere a un'app
description: Come identificare se un utente fondamentale è in grado di accedere a un'applicazione configurata per il provisioning utenti con Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48370e2806b70d550bce95ceff3857a79561f247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264141"
---
# <a name="check-the-status-of-user-provisioning"></a>Controllare lo stato del provisioning degli utenti

Il servizio di provisioning di Azure AD esegue un ciclo di provisioning iniziale sul sistema di origine e sul sistema di destinazione, seguito da cicli incrementali periodici. Quando configuri il provisioning per un'app, puoi controllare lo stato corrente del servizio di provisioning e vedere quando un utente sarà in grado di accedere a un'app.

## <a name="view-the-provisioning-progress-bar"></a>Visualizzare la barra di avanzamento del provisioning

 Nella pagina **Provisioning** per un'app è possibile visualizzare lo stato del servizio di provisioning di Azure AD. La sezione **Stato corrente** nella parte inferiore della pagina mostra se un ciclo di provisioning ha avviato il provisioning degli account utente. È possibile osservare l'avanzamento del ciclo, vedere quanti utenti e gruppi sono stati sottoposti a provisioning e vedere quanti ruoli sono stati creati.

Quando si configura per la prima volta il provisioning automatico, la sezione **Stato corrente** nella parte inferiore della pagina mostra lo stato del ciclo di provisioning iniziale. Questa sezione viene aggiornata ogni volta che viene eseguito un ciclo incrementale. Vengono visualizzati i seguenti dettagli:
- Tipo di ciclo di provisioning (iniziale o incrementale) attualmente in esecuzione o completato l'ultima volta.
- Barra di **avanzamento** che mostra la percentuale del ciclo di provisioning completata. La percentuale riflette il conteggio delle pagine di cui è stato eseguito il provisioning. Tieni presente che ogni pagina può contenere più utenti o gruppi, quindi la percentuale non è direttamente correlata al numero di utenti, gruppi o ruoli di cui è stato eseguito il provisioning.
- Un pulsante **Aggiorna** è possibile utilizzare per mantenere aggiornata la visualizzazione.
- Il numero di **utenti** e **gruppi** nell'archivio dati del connettore. Il conteggio aumenta ogni volta che un oggetto viene aggiunto all'ambito di provisioning. Il conteggio non andrà giù se un utente viene eliminato temporaneamente o eliminato definitivamente in quanto ciò non rimuove l'oggetto dall'archivio dati del connettore. Il conteggio verrà ricollegato alla prima sincronizzazione dopo la [reimpostazione](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) del CDS 
- Un collegamento **Visualizza log di controllo,** che apre i log di provisioning di Azure AD per informazioni dettagliate su tutte le operazioni eseguite dal servizio di provisioning degli utenti, incluso lo stato del provisioning per i singoli utenti (vedere la sezione Usare i log di [provisioning](#use-provisioning-logs-to-check-a-users-provisioning-status) di seguito).

Al termine di un ciclo di provisioning, la sezione **Statistiche fino ad oggi** mostra il numero cumulativo di utenti e gruppi di cui è stato eseguito il provisioning fino ad oggi, insieme alla data di completamento e alla durata dell'ultimo ciclo. **L'ID attività** identifica in modo univoco il ciclo di provisioning più recente. **L'ID processo** è un identificatore univoco per il processo di provisioning ed è specifico dell'app nel tenant.

Lo stato di avanzamento del provisioning può essere visualizzato nel portale di Azure nella scheda Provisioning del **nome &gt; dell'applicazione App di Azure Active Directory &gt; \[Enterprise.The\] &gt; ** provisioning progress can viewed in the Azure portal, in the Azure Active Directory Enterprise Apps application name vo Provisioning tab.

![Barra di avanzamento della pagina di provisioning](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Usare i log di provisioning per controllare lo stato di provisioning di un utenteUse provisioning logs to check a user's provisioning status

Per visualizzare lo stato di provisioning per un utente selezionato, vedere i [log di provisioning (anteprima)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) in Azure AD. Tutte le operazioni eseguite dal servizio di provisioning utenti vengono registrate nei log di provisioning di Azure AD. Sono incluse tutte le operazioni di lettura e scrittura eseguite nei sistemi di origine e di destinazione e i dati utente letti o scritti durante ogni operazione.

È possibile accedere ai log di provisioning nel portale di Azure selezionando I log di provisioning delle app aziendali di Azure Active Directory (anteprima) nella sezione **Attività.You** can access the provisioning logs in the Azure portal by selecting **Azure Active Directory** &gt; **Enterprise Apps** &gt; Provisioning logs **(preview)** in the Activity section. È possibile cercare i dati di provisioning in base al nome dell'utente o all'identificatore nel sistema di origine o nel sistema di destinazione. Per informazioni dettagliate, vedere [Provisioning dei registri (anteprima)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

I log di provisioning registrano tutte le operazioni eseguite dal servizio di provisioning, tra cui:The provisioning logs record all the operations performed by the provisioning service, including:

* Esecuzione in Azure AD di query sugli utenti assegnati che rientrano nell'ambito del provisioning
* Esecuzione nell'app di destinazione di query sull'esistenza di tali utenti
* Confronto degli oggetti utente tra i sistemi
* Aggiunta, aggiornamento o disabilitazione dell'account utente nel sistema di destinazione in base al confronto

Per altre informazioni su come leggere i log di provisioning nel portale di Azure, vedere la guida alla creazione di report di [provisioning.](check-status-user-account-provisioning.md)

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo sarà necessario per eseguire il provisioning degli utenti?
Quando si usa il provisioning automatico degli utenti con un'applicazione, Azure AD esegue automaticamente il provisioning e l'aggiornamento degli account utente in un'app in base a elementi come [l'assegnazione](../manage-apps/assign-user-or-group-access-portal.md) di utenti e gruppi a un intervallo di tempo pianificato regolarmente, in genere ogni 40 minuti.

Il tempo necessario per il provisioning di un determinato utente dipende principalmente dal fatto che il processo di provisioning esegua un ciclo iniziale o incrementale.

- Per il **ciclo iniziale**, il tempo di processo dipende da molti fattori, tra cui il numero di utenti e gruppi nell'ambito del provisioning e il numero totale di utenti e gruppi nel sistema di origine. La prima sincronizzazione tra Azure AD e un'app può richiedere da 20 minuti a diverse ore, a seconda delle dimensioni della directory di Azure AD e del numero di utenti inclusi nell'ambito del provisioning. Un elenco completo dei fattori che influiscono sulle prestazioni iniziali del ciclo sono riepilogati più avanti in questa sezione.

- Per **i cicli incrementali** successivi al ciclo iniziale, i tempi dei processi tendono a essere più veloci (ad esempio entro 10 minuti), poiché il servizio di provisioning archivia le filigrane che rappresentano lo stato di entrambi i sistemi dopo il ciclo iniziale, migliorando le prestazioni delle sincronizzazioni successive. Il tempo del processo dipende dal numero di modifiche rilevate nel ciclo di provisioning. Se sono presenti meno di 5.000 modifiche all'appartenenza a utenti o gruppi, il processo può terminare all'interno di un singolo ciclo di provisioning incrementale. 

La tabella seguente riepiloga i tempi di sincronizzazione per gli scenari di provisioning più comuni. In questi scenari, il sistema di origine è Azure AD e il sistema di destinazione è un'applicazione SaaS. I tempi di sincronizzazione derivano da un'analisi statistica dei processi di sincronizzazione per le applicazioni SaaS ServiceNow, Workplace, Salesforce e G Suite.


| Configurazione dell'ambito | Utenti, gruppi e membri nell'ambito | Tempo di ciclo iniziale | Tempo ciclo incrementale |
| -------- | -------- | -------- | -------- |
| Sincronizza solo utenti e gruppi assegnati |  < 1.000 |  < 30 minuti | < 30 minuti |
| Sincronizza solo utenti e gruppi assegnati |  1.000 - 10.000 | 142 - 708 minuti | < 30 minuti |
| Sincronizza solo utenti e gruppi assegnati |   10.000 - 100.000 | 1.170 - 2.340 minuti | < 30 minuti |
| Sincronizza tutti gli utenti e i gruppi in Azure AD |  < 1.000 | < 30 minuti  | < 30 minuti |
| Sincronizza tutti gli utenti e i gruppi in Azure AD |  1.000 - 10.000 | < 30 - 120 minuti | < 30 minuti |
| Sincronizza tutti gli utenti e i gruppi in Azure AD |  10.000 - 100.000  | 713 - 1.425 minuti | < 30 minuti |
| Sincronizza tutti gli utenti in Azure AD|  < 1.000  | < 30 minuti | < 30 minuti |
| Sincronizza tutti gli utenti in Azure AD | 1.000 - 10.000  | 43 - 86 minuti | < 30 minuti |


Per la configurazione Solo utenti e gruppi assegnati per la **sincronizzazione,** è possibile utilizzare le seguenti formule per determinare i tempi di **ciclo iniziali minimi** e massimi previsti approssimativi:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Riepilogo dei fattori che influenzano il tempo necessario per completare un **ciclo iniziale**:

- Numero totale di utenti e gruppi nell'ambito del provisioning.

- Numero totale di utenti, gruppi e membri del gruppo presenti nel sistema di origine (Azure AD).

- Se gli utenti nell'ambito del provisioning vengono associati agli utenti esistenti nell'applicazione di destinazione o devono essere creati per la prima volta. I processi di sincronizzazione per i quali tutti gli utenti vengono creati per la prima volta richiedono circa *il doppio* dei processi di sincronizzazione per i quali tutti gli utenti sono associati agli utenti esistenti.

- Numero di errori nei [registri di provisioning](check-status-user-account-provisioning.md). Le prestazioni risultano ridotte se sono presenti troppi errori e se il servizio di provisioning è in stato di quarantena. 

- Limitazioni relative al numero e alla frequenza delle richieste implementate dal sistema di destinazione. Alcuni sistemi di destinazione implementano i limiti di frequenza delle richieste e la limitazione delle richieste, che possono influire sulle prestazioni durante le operazioni di sincronizzazione di grandi dimensioni. In queste condizioni, un'applicazione che riceve un numero eccessivo di richieste potrebbe ridurre la propria velocità di risposta o interrompere la connessione. Per migliorare le prestazioni, il connettore deve essere regolato in modo da non inviare le richieste di app più velocemente di quanto l'app possa elaborarle. I connettori di provisioning creati da Microsoft sono in grado di eseguire questa regolazione. 

- Numero e dimensione dei gruppi assegnati. La sincronizzazione dei gruppi assegnati richiede più tempo rispetto alla sincronizzazione degli utenti. Il numero e la dimensione dei gruppi assegnati incidono sulle prestazioni. Se in un'applicazione è [abilitato il mapping per la sincronizzazione dell'oggetto del gruppo](customize-application-attributes.md#editing-group-attribute-mappings), in aggiunta agli utenti vengono sincronizzate le proprietà del gruppo, come i nomi e le appartenenze del gruppo. Queste sincronizzazioni aggiuntive richiederanno più tempo rispetto alla sincronizzazione dei soli oggetti utente.

- Se le prestazioni diventano un problema e si sta tentando di eseguire il provisioning della maggior parte degli utenti e dei gruppi nel tenant, usare i filtri di ambito. I filtri di ambito consentono di ottimizzare i dati che il servizio di provisioning estrae da Azure AD, filtrando gli utenti in base a valori di attributo specifici. Per altre informazioni sui filtri di ambito, vedere [Provisioning dell'applicazione basato su attributi con filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Passaggi successivi
[Automatizzare il provisioning e il deprovisioning degli utenti nelle applicazioni SaaS con Azure Active DirectoryAutomate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](user-provisioning.md)
