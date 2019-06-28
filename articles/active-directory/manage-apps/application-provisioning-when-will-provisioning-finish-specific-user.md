---
title: Identificare se un utente specifico è in grado di accedere a un'applicazione | Microsoft Docs
description: Come identificare se un utente fondamentale è in grado di accedere a un'applicazione configurata per il provisioning utenti con Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd6b70e7a4542a4cad2ee95fa280ddf8fbe6553
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310046"
---
# <a name="check-the-status-of-user-provisioning"></a>Controllare lo stato del provisioning degli utenti

Il servizio di provisioning di Azure AD viene eseguito un ciclo di provisioning iniziale rispetto al sistema di origine e il sistema di destinazione, seguito da cicli incrementali periodici. Quando si configura il provisioning per un'app, è possibile controllare lo stato corrente del servizio di provisioning e vedere quando un utente sarà in grado di accedere a un'app.

## <a name="view-the-provisioning-progress-bar-preview"></a>Visualizzare il provisioning di un indicatore di stato (anteprima)

 Nel **Provisioning** pagina per un'app, è possibile visualizzare lo stato del provisioning del servizio di Azure AD. Il **lo stato corrente** sezione nella parte inferiore della pagina Mostra se un ciclo di provisioning è stato avviato il provisioning degli account utente. È possibile controllare lo stato di avanzamento del ciclo, vedere quanti utenti e gruppi sono stato effettuato il provisioning e vedere quanti ruoli vengono creati.

Quando si configura prima il provisioning automatico, il **lo stato corrente** nella parte inferiore della pagina Mostra lo stato del ciclo di provisioning iniziale. In questa sezione Aggiorna ogni volta che viene eseguito un ciclo incrementale. Vengono visualizzati i dettagli seguenti:
- Il tipo di provisioning del ciclo (iniziale o incrementale) che è attualmente in esecuzione o è stato completato.
- Oggetto **indicatore di stato** che mostra la percentuale del ciclo di provisioning che è stata completata. La percentuale corrisponde al conteggio delle pagine eseguito il provisioning. Si noti che ogni pagina può contenere più utenti o gruppi, in modo che la percentuale non direttamente correlati al numero di utenti, gruppi o ruoli effettuato il provisioning.
- Oggetto **Aggiorna** pulsante è possibile usare per mantenere la vista aggiornata.
- Il numero di **gli utenti** e **gruppi** effettuato il provisioning e il numero di ruoli creati. Durante il ciclo iniziale, il **utenti** numero conta da 1 quando un utente viene creato o aggiornato e conta verso il basso di 1 quando un utente viene eliminato. Durante un ciclo incrementale, non influiscono sull'utente aggiorna il **utenti** conteggio; il numero cambia solo quando gli utenti vengono creati o eliminati.
- Oggetto **Visualizza log di controllo** collegare, che viene aperto il controllo di Azure AD consente di registrare per informazioni dettagliate su tutte le operazioni eseguite dal servizio di provisioning utente, tra cui provisioning lo stato per i singoli utenti (vedere il [logdicontrollodiuso](#use-audit-logs-to-check-a-users-provisioning-status)sezione riportata di seguito).

Dopo un ciclo di provisioning è stato completato, il **statistiche data** sezione illustra i numeri di utenti e gruppi sottoposti a provisioning per data, insieme alla data di completamento e durata dell'ultimo ciclo di aggiornamento cumulativi. Il **ID attività** identifica in modo univoco il ciclo di provisioning più recente. Il **ID processo** è un identificatore univoco per il processo di provisioning ed è specifico per l'app nel tenant.

L'avanzamento del provisioning visualizzabili nel portale di Azure, nelle **Azure Active Directory &gt; App aziendali &gt; \[nome applicazione\] &gt; Provisioning** scheda.

![Barra di avanzamento pagina di provisioning](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>Usare i log di controllo per controllare lo stato di provisioning dell'utente

Per visualizzare lo stato di provisioning per un utente selezionato, vedere i log di controllo in Azure AD. Tutte le operazioni eseguite dall'utente del servizio di provisioning vengono registrate in Azure Active Directory log di controllo. Questo include tutte le operazioni effettuate da sistemi di origine e destinazione e dati utente che è stato letto o scritti durante ogni operazione di lettura e scrittura.

I log di controllo di provisioning sono accessibili nel portale di Azure, nelle **Azure Active Directory &gt; App aziendali &gt; \[nome applicazione\] &gt; log di controllo** scheda. Filtrare i log nella categoria **Provisioning account** per visualizzare solo gli eventi di provisioning per l'app specifica. È possibile cercare gli utenti in base all'ID di abbinamento configurato nel mapping degli attributi. 

Ad esempio, se è stato configurato il nome dell'entità utente o l'indirizzo di posta elettronica come attributo di abbinamento sul lato Azure AD e l'utente non sottoposto a provisioning presenta un valore "audrey@contoso.com", cercare "audrey@contoso.com" nei log di controllo ed esaminare le voci restituite.

I log di controllo di provisioning registrano tutte le operazioni eseguite dal servizio di provisioning, ad esempio:

* Esecuzione in Azure AD di query sugli utenti assegnati che rientrano nell'ambito del provisioning
* Esecuzione nell'app di destinazione di query sull'esistenza di tali utenti
* Confronto degli oggetti utente tra i sistemi
* Aggiunta, aggiornamento o disabilitazione dell'account utente nel sistema di destinazione in base al confronto

Per altre informazioni su come leggere i log di controllo nel portale di Azure, vedere la [Guida alla creazione di report sul provisioning](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo sarà necessario per eseguire il provisioning degli utenti?
Quando si usa il provisioning utenti automatico con un'applicazione, Azure AD viene eseguito il provisioning e aggiorna automaticamente gli account utente in un'app basata su elementi quali [assegnazione di utenti e gruppi](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) a una pianificazione regolare intervallo di tempo, in genere ogni 10 minuti.

Il tempo che necessario per un determinato utente di eseguire il provisioning dipende principalmente dal fatto che il processo di provisioning è in esecuzione una sincronizzazione iniziale o incrementale.

- Per la **iniziale viene sincronizzata**, ora del processo dipende da molti fattori, inclusi il numero di utenti e gruppi nell'ambito del provisioning e il numero totale di utenti e i gruppi nel sistema di origine. La prima sincronizzazione tra Azure AD e un'app può richiedere da 20 minuti a diverse ore, a seconda delle dimensioni della directory di Azure AD e del numero di utenti inclusi nell'ambito del provisioning. Più avanti in questa sezione è presente un elenco completo dei fattori che influiscono sulle prestazioni della sincronizzazione iniziale.

- Per la **sincronizzazioni incrementali** dopo la sincronizzazione iniziale, tempistiche del processo tendono a essere più veloci (entro 10 minuti), poiché il servizio di provisioning archivia i limiti che rappresentano lo stato di entrambi i sistemi dopo la sincronizzazione iniziale, migliorando le prestazioni delle sincronizzazioni successive. Ora del processo dipende dal numero di modifiche rilevate in tale ciclo di provisioning. Se sono presenti meno di 5.000 utenti o modifiche all'appartenenza a gruppo, è possibile completare il processo all'interno di un singolo ciclo di provisioning incrementale. 

La tabella seguente riepiloga i tempi di sincronizzazione per gli scenari di provisioning più comuni. In questi scenari, il sistema di origine è Azure AD e il sistema di destinazione è un'applicazione SaaS. I tempi di sincronizzazione sono derivati da un'analisi statistica dei processi di sincronizzazione per le applicazioni SaaS ServiceNow, all'area di lavoro, Salesforce e G Suite.


| Configurazione dell'ambito | Utenti, gruppi e membri nell'ambito | Ora della sincronizzazione iniziale | Ora della sincronizzazione incrementale |
| -------- | -------- | -------- | -------- |
| Sincronizza solo utenti e gruppi assegnati |  < 1.000 |  < 30 minuti | < 30 minuti |
| Sincronizza solo utenti e gruppi assegnati |  1\.000 - 10.000 | 142 - 708 minuti | < 30 minuti |
| Sincronizza solo utenti e gruppi assegnati |   10.000 - 100.000 | 1\.170 - 2.340 minuti | < 30 minuti |
| Sincronizza tutti gli utenti e i gruppi in Azure AD |  < 1.000 | < 30 minuti  | < 30 minuti |
| Sincronizza tutti gli utenti e i gruppi in Azure AD |  1\.000 - 10.000 | < 30 - 120 minuti | < 30 minuti |
| Sincronizza tutti gli utenti e i gruppi in Azure AD |  10.000 - 100.000  | 713 - 1.425 minuti | < 30 minuti |
| Sincronizza tutti gli utenti in Azure AD|  < 1.000  | < 30 minuti | < 30 minuti |
| Sincronizza tutti gli utenti in Azure AD | 1\.000 - 10.000  | 43 - 86 minuti | < 30 minuti |


Nella configurazione **Sincronizza solo utenti e gruppi assegnati**, è possibile applicare le formule seguenti per determinare la durata approssimativa prevista, minima e massima, per la **sincronizzazione iniziale**:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Riepilogo dei fattori che influenzano il tempo necessario per completare una **sincronizzazione iniziale**:

- Numero totale di utenti e gruppi nell'ambito del provisioning.

- Numero totale di utenti, gruppi e membri del gruppo presenti nel sistema di origine (Azure AD).

- Indica se gli utenti nell'ambito del provisioning corrispondono agli utenti esistenti nell'applicazione di destinazione o devono essere creati per la prima volta. I processi di sincronizzazione per il quale vengono creati tutti gli utenti per la prima volta richiedere circa *due volte più a lungo* come sincronizzare i processi per il quale vengono confrontati tutti gli utenti per gli utenti esistenti.

- Numero di errori nei [log di controllo](check-status-user-account-provisioning.md). Le prestazioni risultano ridotte se sono presenti troppi errori e se il servizio di provisioning è in stato di quarantena.    

- Limitazioni relative al numero e alla frequenza delle richieste implementate dal sistema di destinazione. Alcuni sistemi di destinazione implementano limiti di velocità di richiesta e la limitazione delle richieste, che può influire sulle prestazioni durante le operazioni di sincronizzazione di grandi dimensioni. In queste condizioni, un'applicazione che riceve un numero eccessivo di richieste potrebbe ridurre la propria velocità di risposta o interrompere la connessione. Per migliorare le prestazioni, il connettore deve essere regolato in modo da non inviare le richieste di app più velocemente di quanto l'app possa elaborarle. I connettori di provisioning creati da Microsoft sono in grado di eseguire questa regolazione. 

- Numero e dimensione dei gruppi assegnati. La sincronizzazione dei gruppi assegnati richiede più tempo rispetto alla sincronizzazione degli utenti. Il numero e la dimensione dei gruppi assegnati incidono sulle prestazioni. Se in un'applicazione è [abilitato il mapping per la sincronizzazione dell'oggetto del gruppo](customize-application-attributes.md#editing-group-attribute-mappings), in aggiunta agli utenti vengono sincronizzate le proprietà del gruppo, come i nomi e le appartenenze del gruppo. Queste sincronizzazioni aggiuntive richiederanno più tempo rispetto alla sincronizzazione dei soli oggetti utente.

## <a name="next-steps"></a>Passaggi successivi
[Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
