---
title: Informazioni sui ruoli di amministratore dell'organizzazione in Azure
description: Informazioni sui ruoli di amministratore per Enterprise in Azure. È possibile assegnare cinque ruoli amministrativi distinti.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: e712b44f22a8080b14a2cc2532cadf2dd4738b76
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409201"
---
# <a name="managing-azure-enterprise-roles"></a>Gestione dei ruoli Enterprise di Azure

Per gestire l'utilizzo e la spesa per la propria organizzazione, i clienti di Azure con un Contratto Enterprise possono assegnare cinque distinti ruoli amministrativi:

- Amministratore dell'organizzazione
- Amministratore dell'organizzazione (sola lettura)<sup>1</sup>
- Amministratore del reparto
- Amministratore del reparto (sola lettura)
- Proprietario dell'account<sup>2</sup>

<sup>1</sup> Il contatto per la fatturazione del contratto EA sarà incluso in questo ruolo.

<sup>2</sup> Il contatto per la fatturazione non può essere aggiunto o cambiato in Azure EA Portal e verrà aggiunto alla registrazione EA in base all'utente configurato come contatto per la fatturazione a livello di contratto. Per cambiare il contatto per la fatturazione, è necessario effettuare una richiesta al Centro operativo regionale tramite un partner/software advisor.

Il primo amministratore della registrazione configurato durante il provisioning della registrazione determina il tipo di autenticazione dell'account del contatto per la fatturazione. Quando il contatto per la fatturazione viene aggiunto a EA Portal come amministratore di sola lettura, ottiene l'autenticazione tramite account Microsoft. 

Se ad esempio il tipo di autenticazione iniziale viene impostato su Misto, il contratto Enterprise verrà aggiunto come account Microsoft e il contatto per la fatturazione avrà privilegio di amministratore EA di sola lettura. Se l'amministratore EA non approva l'autorizzazione tramite account Microsoft per un contatto per la fatturazione, l'amministratore EA potrà eliminare l'utente specifico e richiedere al cliente di aggiungere di nuovo l'utente come amministratore di sola lettura con un account aziendale o dell'istituto di istruzione configurato solo a livello di registrazione in EA Portal.

Questi ruoli sono specifici per la gestione del Contratto Enterprise di Azure e si aggiungono ai ruoli predefiniti disponibili in Azure per controllare l'accesso alle risorse. Per altre informazioni, vedere [Ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md).

## <a name="azure-enterprise-portal-hierarchy"></a>Gerarchia di Azure Enterprise Portal

La gerarchia di Azure Enterprise Portal è costituita da:

- **Azure Enterprise Portal** : portale di gestione online che consente di gestire i costi dei servizi del Contratto Enterprise di Azure. È possibile:

  - Creare una gerarchia del Contratto Enterprise di Azure con reparti, account e sottoscrizioni.
  - Riconciliare i costi dei servizi utilizzati, scaricare report di utilizzo e visualizzare i listini prezzi.
  - Creare chiavi API per la registrazione.

- **Reparti** : facilita la segmentazione dei costi in raggruppamenti logici. Tramite i reparti è possibile impostare un budget o una quota a livello di reparto.

- **Account** : sono le unità organizzative in Azure Enterprise Portal. È possibile usare gli account per gestire le sottoscrizioni e accedere ai report.

- **Sottoscrizioni** : sono le unità più piccole in Azure Enterprise Portal. Si tratta di contenitori per i servizi di Azure gestiti dall'amministratore del servizio.

Il diagramma seguente illustra le semplici gerarchie di Azure EA.

![Diagramma delle semplici gerarchie di Azure EA](./media/understand-ea-roles/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Ruoli utente dell'organizzazione

L'iscrizione Enterprise include i seguenti ruoli utente amministrativi:

- Amministratore dell'organizzazione
- Amministratore del reparto
- Proprietario dell'account
- Amministratore del servizio
- Contatto per le notifiche

I ruoli funzionano in due portali diversi per il completamento delle attività. [Azure Enterprise Portal](https://ea.azure.com) consente di gestire la fatturazione e i costi, mentre il [portale di Azure](https://portal.azure.com) consente di gestire i servizi di Azure.

I ruoli utente sono associati a un account utente. Per convalidarne l'autenticità, è necessario che ogni utente abbia un account Microsoft, dell'istituto di istruzione o aziendale valido. Assicurarsi che ogni account sia associato a un indirizzo di posta elettronica monitorato attivamente. Le notifiche dell'account vengono inviate all'indirizzo di posta elettronica.

Quando si configurano gli utenti, è possibile assegnare più account al ruolo di amministratore dell'organizzazione. Tuttavia il ruolo di proprietario dell'account può essere assegnato a un solo account. È anche possibile assegnare sia il ruolo di amministratore dell'organizzazione sia ruoli di proprietario dell'account a un singolo account.

### <a name="enterprise-administrator"></a>Amministratore dell'organizzazione

Gli utenti con questo ruolo hanno il livello di accesso più elevato. Possono eseguire le operazioni seguenti:

- Gestire account e proprietari di account.
- Gestire altri amministratori dell'organizzazione.
- Gestire amministratori di reparto.
- Gestire i contatti per le notifiche.
- Visualizzare l'utilizzo in tutti gli account.
- Visualizzare gli addebiti non fatturati in tutti gli account.

In una registrazione Enterprise è possibile designare più amministratori dell'organizzazione. Agli amministratori dell'organizzazione è possibile concedere l'accesso in sola lettura. Ereditano tutti il ruolo di amministratore del reparto.

### <a name="department-administrator"></a>Amministratore del reparto

Gli utenti con questo ruolo possono:

- Creare e gestire reparti.
- Creare nuovi proprietari di account.
- Visualizzare i dettagli d'uso per i reparti che gestiscono.
- Visualizzare i costi, se dispongono delle autorizzazioni necessarie.

In ogni registrazione Enterprise è possibile designare più amministratori del reparto.

È possibile concedere agli amministratori del reparto l'accesso in sola lettura quando si modifica o si crea un nuovo amministratore del reparto. Impostare l'opzione di sola lettura su **Sì**.

### <a name="account-owner"></a>Proprietario dell'account

Gli utenti con questo ruolo possono:

- Creare e gestire sottoscrizioni.
- Gestire gli amministratori del servizio.
- Visualizzare l'utilizzo delle sottoscrizioni.

Ogni account richiede un account Microsoft, dell'istituto di istruzione o aziendale univoco. Per altre informazioni sui ruoli amministrativi di Azure Enterprise Portal, vedere [Informazioni sui ruoli amministrativi per il Contratto Enterprise di Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Amministratore del servizio

Il ruolo di amministratore del servizio ha le autorizzazioni per gestire i servizi nel portale di Azure e per assegnare utenti al ruolo di coamministratore.

### <a name="notification-contact"></a>Contatto per le notifiche

Il contatto per le notifiche riceve le notifiche sull'utilizzo correlate alla registrazione.

Le sezioni seguenti descrivono i limiti e le funzionalità di ogni ruolo.

## <a name="user-limit-for-admin-roles"></a>Limite di utenti per i ruoli di amministratore

|Ruolo| Limite di utenti|
|---|---|
|Amministratore dell'organizzazione|Nessuna limitazione|
|Amministratore dell'organizzazione (sola lettura)|Nessuna limitazione|
|Amministratore del reparto|Nessuna limitazione|
|Amministratore del reparto (sola lettura)|Nessuna limitazione|
|Proprietario dell'account|1 per account<sup>3</sup>|

<sup>3</sup> Ogni account richiede un account Microsoft univoco oppure un account aziendale o dell'istituto di istruzione.

## <a name="organization-structure-and-permissions-by-role"></a>Struttura dell'organizzazione e autorizzazioni in base al ruolo

|Attività| Amministratore dell'organizzazione|Amministratore dell'organizzazione (sola lettura)|Amministratore del reparto|Amministratore del reparto (sola lettura)|Proprietario dell'account| Partner|
|---|---|---|---|---|---|---|
|Visualizzare gli amministratori dell'organizzazione|✔|✔|✘|✘|✘|✔|
|Aggiungere o rimuovere gli amministratori dell'organizzazione|✔|✘|✘|✘|✘|✘|
|Visualizzare i contatti per le notifiche<sup>4</sup> |✔|✔|✘|✘|✘|✔|
|Aggiungere o rimuovere i contatti per le notifiche<sup>4</sup> |✔|✘|✘|✘|✘|✘|
|Creare e gestire i reparti |✔|✘|✘|✘|✘|✘|
|Visualizzare gli amministratore di reparto|✔|✔|✔|✔|✘|✔|
|Aggiungere o rimuovere gli amministratori di reparto|✔|✘|✔|✘|✘|✘|
|Visualizzare gli account nella registrazione |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|Aggiungere gli account alla registrazione e cambiare il proprietario dell'account|✔|✘|✔<sup>5</sup>|✘|✘|✘|
|Creare e gestire le sottoscrizioni e le relative autorizzazioni|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> Ai contatti per le notifiche vengono inviate comunicazioni sul Contratto Enterprise di Azure tramite posta elettronica.
- <sup>5</sup> L'attività è limitata agli account nel reparto.

## <a name="add-a-new-enterprise-administrator"></a>Aggiungere un nuovo amministratore dell'organizzazione

Gli amministratori dell'organizzazione hanno il maggior numero di privilegi per la gestione di una registrazione EA di Azure. Il primo amministratore EA di Azure è stato creato al momento della configurazione del contratto Enterprise Agreement. Tuttavia, è possibile aggiungere o rimuovere nuovi amministratori in qualsiasi momento. I nuovi amministratori possono essere aggiunti solo dagli amministratori esistenti. Per altre informazioni sull'aggiunta di altri amministratori dell'organizzazione, vedere [Creare un altro amministratore dell'organizzazione](ea-portal-administration.md#create-another-enterprise-administrator). Per altre informazioni sui ruoli e le attività del profilo di fatturazione, vedere [Ruoli e attività del profilo di fatturazione](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-account-owner-state-from-pending-to-active"></a>Aggiornare lo stato del proprietario dell'account da in sospeso ad attivo

Quando vengono aggiunti nuovi proprietari di account a una registrazione EA di Azure per la prima volta, il relativo stato viene visualizzato come _in sospeso_. Quando un nuovo proprietario dell'account riceve il messaggio di posta elettronica di benvenuto per l'attivazione, può accedere per attivare il proprio account. Una volta attivato l'account, il relativo stato viene aggiornato e passa da _In sospeso_ ad _Attivo_. Il proprietario dell'account deve leggere il messaggio di avviso e selezionare **Continua**. Ai nuovi utenti potrebbe essere richiesto di immettere il nome e il cognome per creare un account commerciale. In tal caso, devono aggiungere le informazioni necessarie per continuare, quindi l'account viene attivato.

## <a name="add-a-department-admin"></a>Aggiungere un amministratore del reparto

Dopo che un amministratore EA di Azure ha creato un reparto, l'amministratore dell'organizzazione di Azure può aggiungere amministratori di reparto e associare ognuno a un reparto. Un amministratore del reparto può creare nuovi account. I nuovi account sono necessari per la creazione di sottoscrizioni EA di Azure.

Per altre informazioni sull'aggiunta di un amministratore di reparto, vedere [Creare un amministratore di reparto EA di Azure](ea-portal-administration.md#add-a-department-administrator).

## <a name="usage-and-costs-access-by-role"></a>Accesso all'utilizzo e ai costi per ruolo

|Attività| Amministratore dell'organizzazione|Amministratore dell'organizzazione (sola lettura)|Amministratore del reparto|Amministratore del reparto (sola lettura) |Proprietario dell'account| Partner|
|---|---|---|---|---|---|---|
|Visualizzare il saldo del credito che include il pagamento anticipato di Azure|✔|✔|✘|✘|✘|✔|
|Visualizzare le quote di spesa del reparto|✔|✔|✘|✘|✘|✔|
|Impostare le quote di spesa del reparto|✔|✘|✘|✘|✘|✘|
|Visualizzare l'elenco prezzi per il Contratto Enterprise dell'organizzazione|✔|✔|✘|✘|✘|✔|
|Visualizzare i dettagli relativi a utilizzo e costi|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Gestire le risorse nel portale di Azure|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> È necessario che l'amministratore dell'organizzazione abiliti il criterio **DA view charges** (Visualizzazione addebiti per amministratori di reparto) nell'Enterprise Portal. L'amministratore del reparto potrà quindi visualizzare i dettagli dei costi per il reparto.
- <sup>7</sup> È necessario che l'amministratore dell'organizzazione abiliti il criterio **AO view charges** (Visualizzazione addebiti per proprietari dell'account) nell'Enterprise Portal. Il proprietario dell'account può quindi visualizzare i dettagli dei costi per l'account.

## <a name="see-pricing-for-different-user-roles"></a>Vedere i prezzi per i diversi ruoli utente

È possibile riscontrare differenze di prezzi nel portale di Azure a seconda del ruolo amministrativo e della modalità in cui l'amministratore dell'organizzazione ha impostato i criteri di visualizzazione degli addebiti. Nell'Enterprise Portal i due criteri che determinano i prezzi visualizzati nel portale di Azure sono:

- DA view charges (Visualizzazione addebiti per amministratori di reparto)
- AO view charges (Visualizzazione addebiti per proprietari dell'account)

Per informazioni su come impostare questi criteri, vedere [Gestire l'accesso alle informazioni di fatturazione per Azure](manage-billing-access.md).

La tabella seguente illustra la relazione tra i ruoli di amministratore del Contratto Enterprise, i criteri per la visualizzazione degli addebiti, il ruolo di Azure nel portale di Azure e i prezzi visualizzati nel portale di Azure. L'amministratore dell'organizzazione esamina i dettagli di utilizzo in base ai prezzi per il Contratto Enterprise dell'organizzazione. Tuttavia, l'amministratore di reparto e il proprietario dell'account vedono visualizzazioni dei prezzi diverse in base ai criteri di visualizzazione degli addebiti e al ruolo di Azure. Il ruolo di amministratore di reparto elencato nella tabella seguente si riferisce sia al ruolo Amministratore di reparto sia al ruolo Amministratore di reparto (sola lettura).

|Ruolo di amministratore per il Contratto Enterprise|Criteri per la visualizzazione degli addebiti per ruolo|Ruolo di Azure|Visualizzazione dei prezzi|
|---|---|---|---|
|Proprietario dell'account OPPURE Amministratore del reparto|✔ Abilitato|Proprietario|Prezzi per il Contratto Enterprise dell'organizzazione|
|Proprietario dell'account OPPURE Amministratore del reparto|✘ Disabilitato|Proprietario|Prezzi al dettaglio|
|Proprietario dell'account OPPURE Amministratore del reparto|✔ Abilitato |none|Nessun prezzo|
|Proprietario dell'account OPPURE Amministratore del reparto|✘ Disabilitato |none|Nessun prezzo|
|nessuno|Non applicabile |Proprietario|Prezzi al dettaglio|

Si impostano il ruolo di amministratore dell'organizzazione e i criteri di visualizzazione degli addebiti nell'Enterprise Portal. Il ruolo di Azure può essere aggiornato nel portale di Azure. Per altre informazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../../role-based-access-control/role-assignments-portal.md).



## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso alle informazioni di fatturazione per Azure](manage-billing-access.md)
- [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../../role-based-access-control/role-assignments-portal.md)
- [Ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md)
