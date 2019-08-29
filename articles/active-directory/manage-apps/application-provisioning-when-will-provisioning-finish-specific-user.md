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
ms.openlocfilehash: 3c6ad7e305958131c4f544dfa2022e7471e9adac
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147101"
---
# <a name="check-the-status-of-user-provisioning"></a>Verificare lo stato del provisioning utenti

Il servizio di provisioning Azure AD esegue un ciclo di provisioning iniziale sul sistema di origine e di destinazione, seguito da cicli incrementali periodici. Quando si configura il provisioning per un'app, è possibile controllare lo stato corrente del servizio di provisioning e vedere quando un utente sarà in grado di accedere a un'app.

## <a name="view-the-provisioning-progress-bar"></a>Visualizzare l'indicatore di stato del provisioning

 Nella pagina di provisioning di un'app è possibile visualizzare lo stato del servizio di provisioning di Azure ad. La sezione **stato corrente** nella parte inferiore della pagina indica se un ciclo di provisioning ha avviato il provisioning degli account utente. È possibile controllare lo stato di avanzamento del ciclo, vedere il numero di utenti e gruppi di cui è stato effettuato il provisioning e verificare il numero di ruoli creati.

Quando si configura per la prima volta il provisioning automatico, la sezione **stato corrente** nella parte inferiore della pagina mostra lo stato del ciclo di provisioning iniziale. Questa sezione viene aggiornata ogni volta che viene eseguito un ciclo incrementale. Vengono visualizzati i dettagli seguenti:
- Il tipo di ciclo di provisioning (iniziale o incrementale) attualmente in esecuzione o è stato completato per ultimo.
- **Indicatore di stato** che mostra la percentuale del ciclo di provisioning completato. La percentuale riflette il numero di pagine di cui è stato effettuato il provisioning. Si noti che ogni pagina potrebbe contenere più utenti o gruppi, quindi la percentuale non è direttamente correlata al numero di utenti, gruppi o ruoli di cui è stato effettuato il provisioning.
- Pulsante **Aggiorna** che è possibile utilizzare per aggiornare la visualizzazione.
- Il numero di **utenti** e **gruppi** di cui è stato effettuato il provisioning e il numero di ruoli creati. Durante il ciclo iniziale, il numero di **utenti** viene conteggiato per 1 quando un utente viene creato o aggiornato e viene conteggiato di 1 quando viene eliminato un utente. Durante un ciclo incrementale, gli aggiornamenti utente non influiscono sul numero di **utenti** . il numero viene modificato solo quando gli utenti vengono creati o eliminati.
- Un collegamento **Visualizza log di controllo** , che consente di aprire i log di controllo Azure ad per informazioni dettagliate su tutte le operazioni eseguite dal servizio di provisioning utenti, incluso lo stato del provisioning per i singoli utenti (vedere la sezione [usare i log di controllo](#use-audit-logs-to-check-a-users-provisioning-status) più avanti).

Al termine di un ciclo di provisioning, nella sezione **statistiche per data** vengono visualizzati i numeri cumulativi di utenti e gruppi di cui è stato effettuato il provisioning in data, insieme alla data di completamento e alla durata dell'ultimo ciclo. L' **ID attività** identifica in modo univoco il ciclo di provisioning più recente. L' **ID del processo** è un identificatore univoco per il processo di provisioning ed è specifico dell'app nel tenant.

Lo stato di avanzamento del provisioning può essere visualizzato nella portale di Azure, nella scheda provisioning del **nome &gt; &gt; \] dell'applicazione &gt; Azure Active Directory app \[aziendali** .

![Indicatore di stato della pagina di provisioning](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>Usare i log di controllo per verificare lo stato di provisioning di un utente

Per visualizzare lo stato di provisioning per un utente selezionato, vedere i log di controllo in Azure AD. Tutte le operazioni eseguite dal servizio di provisioning utenti vengono registrate nei log di controllo Azure AD. Sono incluse tutte le operazioni di lettura e scrittura effettuate nei sistemi di origine e di destinazione e i dati utente letti o scritti durante ogni operazione.

È possibile accedere ai log di controllo del provisioning nella portale di Azure nella scheda **log &gt; di controllo &gt; del nome\] &gt; dell'applicazione Azure Active Directory app \[aziendali** . Filtrare i log nella categoria **Provisioning account** per visualizzare solo gli eventi di provisioning per l'app specifica. È possibile cercare gli utenti in base all'ID di abbinamento configurato nel mapping degli attributi. 

Ad esempio, se è stato configurato il nome dell'entità utente o l'indirizzo di posta elettronica come attributo di abbinamento sul lato Azure AD e l'utente non sottoposto a provisioning presenta un valore "audrey@contoso.com", cercare "audrey@contoso.com" nei log di controllo ed esaminare le voci restituite.

I log di controllo di provisioning registrano tutte le operazioni eseguite dal servizio di provisioning, ad esempio:

* Esecuzione in Azure AD di query sugli utenti assegnati che rientrano nell'ambito del provisioning
* Esecuzione nell'app di destinazione di query sull'esistenza di tali utenti
* Confronto degli oggetti utente tra i sistemi
* Aggiunta, aggiornamento o disabilitazione dell'account utente nel sistema di destinazione in base al confronto

Per ulteriori informazioni su come leggere i log di controllo nell'portale di Azure, vedere la [Guida alla creazione di report](check-status-user-account-provisioning.md)sul provisioning.

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo sarà necessario per eseguire il provisioning degli utenti?
Quando si usa il provisioning utenti automatico con un'applicazione, Azure AD esegue automaticamente il provisioning e aggiorna gli account utente in un'app in base a elementi come l' [assegnazione di utenti e gruppi](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) a un intervallo di tempo pianificato regolarmente, in genere ogni 40 minuti.

Il tempo necessario per eseguire il provisioning di un determinato utente dipende principalmente dal fatto che il processo di provisioning esegua una sincronizzazione iniziale o una sincronizzazione incrementale.

- Per le **sincronizzazioni iniziali**, il tempo di esecuzione del processo dipende da molti fattori, tra cui il numero di utenti e gruppi nell'ambito del provisioning e il numero totale di utenti e gruppi nel sistema di origine. La prima sincronizzazione tra Azure AD e un'app può richiedere da 20 minuti a diverse ore, a seconda delle dimensioni della directory di Azure AD e del numero di utenti inclusi nell'ambito del provisioning. Più avanti in questa sezione è presente un elenco completo dei fattori che influiscono sulle prestazioni della sincronizzazione iniziale.

- Per le sincronizzazioni incrementali dopo la sincronizzazione iniziale, i tempi dei processi tendono a essere più veloci (ad esempio entro 10 minuti), perché il servizio di provisioning archivia le filigrane che rappresentano lo stato di entrambi i sistemi dopo la sincronizzazione iniziale, migliorando le prestazioni delle sincronizzazioni successive. Il tempo di esecuzione del processo dipende dal numero di modifiche rilevate nel ciclo di provisioning. Se sono presenti meno di 5.000 modifiche dell'appartenenza a un utente o a un gruppo, il processo può terminare con un singolo ciclo di provisioning incrementale. 

La tabella seguente riepiloga i tempi di sincronizzazione per gli scenari di provisioning più comuni. In questi scenari, il sistema di origine è Azure AD e il sistema di destinazione è un'applicazione SaaS. I tempi di sincronizzazione sono derivati da un'analisi statistica dei processi di sincronizzazione per le applicazioni SaaS ServiceNow, area di lavoro, Salesforce e G Suite.


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

- Indica se gli utenti nell'ambito per il provisioning vengono associati agli utenti esistenti nell'applicazione di destinazione o devono essere creati per la prima volta. I processi di sincronizzazione per cui tutti gli utenti vengono creati per la prima volta possono richiedere circa *due volte* i processi di sincronizzazione per cui tutti gli utenti vengono associati agli utenti esistenti.

- Numero di errori nei [log di controllo](check-status-user-account-provisioning.md). Le prestazioni risultano ridotte se sono presenti troppi errori e se il servizio di provisioning è in stato di quarantena.    

- Limitazioni relative al numero e alla frequenza delle richieste implementate dal sistema di destinazione. Alcuni sistemi di destinazione implementano limiti di frequenza e limitazione delle richieste, che possono influito sulle prestazioni durante le operazioni di sincronizzazione di grandi dimensioni. In queste condizioni, un'applicazione che riceve un numero eccessivo di richieste potrebbe ridurre la propria velocità di risposta o interrompere la connessione. Per migliorare le prestazioni, il connettore deve essere regolato in modo da non inviare le richieste di app più velocemente di quanto l'app possa elaborarle. I connettori di provisioning creati da Microsoft sono in grado di eseguire questa regolazione. 

- Numero e dimensione dei gruppi assegnati. La sincronizzazione dei gruppi assegnati richiede più tempo rispetto alla sincronizzazione degli utenti. Il numero e la dimensione dei gruppi assegnati incidono sulle prestazioni. Se in un'applicazione è [abilitato il mapping per la sincronizzazione dell'oggetto del gruppo](customize-application-attributes.md#editing-group-attribute-mappings), in aggiunta agli utenti vengono sincronizzate le proprietà del gruppo, come i nomi e le appartenenze del gruppo. Queste sincronizzazioni aggiuntive richiederanno più tempo rispetto alla sincronizzazione dei soli oggetti utente.

## <a name="next-steps"></a>Passaggi successivi
[Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
