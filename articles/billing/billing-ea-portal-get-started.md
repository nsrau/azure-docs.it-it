---
title: Introduzione ad Azure EA Portal
description: Questo articolo illustra ai clienti con contratto Azure EA come usare Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 1882b283f376a1bb8706132263c83e1a24ec0705
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900931"
---
# <a name="get-started-with-the-azure-ea-portal"></a>Introduzione ad Azure EA Portal

Questo articolo aiuta i clienti diretti e indiretti di Azure EA a iniziare a usare [Azure EA Portal](https://ea.azure.com) con queste informazioni di base:

- Come è strutturato Azure EA Portal.
- Ruoli usati in Azure EA Portal.
- Come iniziare a creare sottoscrizioni.
- Analisi dei costi in Azure EA Portal e nel portale di Azure.

Ecco un video che mostra una sessione completa di onboarding di Azure EA Portal:

[Video sull'onboarding di Azure EA Portal ](https://www.youtube.com/watch?v=OiZ1GdBpo-I)

>[!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-ea-portal-hierarchy"></a>Gerarchia di Azure EA Portal

La gerarchia di Azure EA Portal è costituita da:

**Microsoft Azure EA Portal**: Azure EA Portal è un portale online che consente di gestire i costi per i servizi di Azure EA. Viene usato per creare una gerarchia di Azure EA, inclusi reparti, account e sottoscrizioni, oltre che per riconciliare i costi dei servizi utilizzati, scaricare i report sull'utilizzo e visualizzare i listini prezzi. Inoltre, consente di creare le chiavi API per la registrazione.

**Reparti**: è possibile creare reparti per segmentare i costi in raggruppamenti logici e quindi impostare un budget o una quota a livello di reparto.

**Account**: gli account sono unità organizzative in Azure EA Portal e vengono usati per gestire le sottoscrizioni, oltre che per la creazione di report.

**Sottoscrizioni**: le sottoscrizioni sono le unità più piccole in Azure EA Portal. Si tratta di contenitori per i servizi di Azure gestiti dall'amministratore del servizio.

Il diagramma seguente illustra le semplici gerarchie di Azure EA.

![Diagramma delle semplici gerarchie di Azure EA](./media/billing-ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Ruoli utente dell'organizzazione

Per amministrare i servizi di Azure nella registrazione, sono disponibili quattro ruoli utente amministrativi distinti per l'organizzazione:

- Amministratore dell'organizzazione
- Amministratore del reparto
- Proprietario dell'account
- Amministratore del servizio

I ruoli vengono usati per completare le attività in due diversi portali di Microsoft Azure. Azure EA Portal (https://ea.azure.com) ) si usa per gestire fatturazione e costi. Il portale di Azure (https://portal.azure.com) ) si usa per gestire i servizi di Azure.

I ruoli utente sono associati a un account utente. Per convalidarne l'autenticità, è necessario che ogni utente abbia un account aziendale, dell'istituto di istruzione o Microsoft valido. Assicurarsi che ogni account sia associato a un indirizzo di posta elettronica monitorato attivamente. Le notifiche dell'account vengono inviate all'indirizzo di posta elettronica.

Quando si configurano gli utenti, è possibile assegnare più account aziendali, dell'istituto di istruzione o Microsoft al ruolo di amministratore dell'organizzazione. Al ruolo di proprietario dell'account è invece possibile assegnare un solo account aziendale, dell'istituto di istruzione o Microsoft. Inoltre, a un singolo account aziendale, dell'istituto di istruzione o Microsoft è possibile applicare sia il ruolo di amministratore dell'organizzazione che quello di proprietario dell'account.

### <a name="enterprise-administrator"></a>Amministratore dell'organizzazione

Il ruolo di amministratore dell'organizzazione ha il livello massimo di accesso. Gli utenti con questo ruolo possono:

- Gestire account e proprietari di account
- Gestire altri amministratori dell'organizzazione
- Gestire gli amministratori del reparto
- Gestire i contatti per le notifiche
- Visualizzare l'utilizzo in tutti gli account
- Visualizzare gli addebiti non fatturati in tutti gli account

In una registrazione Enterprise è possibile designare più amministratori dell'organizzazione. Agli amministratori dell'organizzazione è possibile concedere l'accesso in sola lettura. Ereditano tutti il ruolo di amministratore del reparto.

### <a name="department-administrator"></a>Amministratore del reparto

Gli utenti con questo ruolo possono:

- Creare e gestire i reparti
- Creare nuovi proprietari di account
- Visualizzare i dettagli di utilizzo per i reparti che gestiscono
- Visualizzare i costi, se vengono concesse le autorizzazioni necessarie

In ogni registrazione Enterprise è possibile designare più amministratori del reparto.

Agli amministratori del reparto è possibile concedere l'accesso in sola lettura. A questo scopo, modificare o creare un nuovo amministratore del reparto e impostare l'opzione per la sola lettura su **Sì**.

### <a name="account-owner"></a>Proprietario dell'account

Gli utenti con questo ruolo possono:

- Creare e gestire sottoscrizioni
- Gestire gli amministratori del servizio
- Visualizzare l'utilizzo delle sottoscrizioni

Ogni account richiede un account aziendale, dell'istituto di istruzione o Microsoft univoco. Per altre informazioni, vedere [Informazioni sui ruoli amministrativi per il contratto Azure Enterprise Agreement](billing-understand-ea-roles.md).

### <a name="service-administrator"></a>Amministratore del servizio

L'amministratore del servizio ha le autorizzazioni per gestire i servizi nel portale di Azure e per assegnare utenti al ruolo di coamministratore.

## <a name="activate-your-enrollment"></a>Attivare la registrazione

Per attivare il servizio, l'amministratore dell'organizzazione iniziale apre Azure EA Portal all'indirizzo [https://ea.azure.com](https://ea.azure.com) e accede usando l'indirizzo di posta elettronica incluso nel messaggio di invito.

Se si hanno più registrazioni, scegliere quale attivare. Per impostazione predefinita, vengono visualizzate solo le registrazioni attive. Per visualizzare la cronologia delle iscrizioni, deselezionare l'opzione **Attivo** nell'angolo in alto a destra di Azure EA Portal.

In Registrazione lo stato visualizzato è **Attivo**.

![Esempio di una registrazione attiva](./media/billing-ea-portal-get-started/ea-enrollment-status.png)

Solo gli attuali amministratori dell'organizzazione di Azure possono creare altri amministratori dell'organizzazione.

### <a name="create-another-enterprise-admin"></a>Creare un altro amministratore dell'organizzazione

- Accedere a [Azure EA Portal](https://ea.azure.com) e passare a **Gestisci** > **Dettagli registrazione**, quindi fare clic su **+ Aggiungi amministratore** nell'angolo in alto a destra della pagina.

Assicurarsi di avere gli indirizzi di posta elettronica dell'utente e il metodo di autenticazione preferito, ad esempio tramite autenticazione aziendale o dell'istituto di istruzione oppure account Microsoft. Queste informazioni sono necessarie per aggiungere un utente.

Se non si è l'amministratore EA, contattare un amministratore EA per chiedere di essere aggiunti a una registrazione. Dopo l'aggiunta in una registrazione, si riceverà un messaggio di posta elettronica di attivazione.

Se l'amministratore EA non è in grado di fornire assistenza, creare una [richiesta di supporto per Azure EA Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Specificare le informazioni seguenti:

- Numero di registrazione
- Indirizzo di posta elettronica da aggiungere e tipo di autenticazione (aziendale, dell'istituto di istruzione o account Microsoft)
- Approvazione tramite posta elettronica di un amministratore EA esistente
  - Se l'amministratore EA esistente non è disponibile, contattare il partner o il software advisor per chiedergli di modificare le informazioni di contatto tramite lo strumento VLSC.

Per altre informazioni, vedere [Informazioni sui ruoli amministrativi per il contratto Azure Enterprise Agreement](billing-understand-ea-roles.md).

## <a name="create-an-azure-ea-department"></a>Creare un reparto di Azure EA

Gli amministratori dell'organizzazione e gli amministratori del reparto usano i reparti per organizzare e creare report sui servizi di Azure dell'organizzazione e sull'utilizzo in base a reparto e a centro di costo. L'amministratore dell'organizzazione può:

- Aggiungere o rimuovere reparti
- Associare un account a un reparto
- Creare amministratori del reparto
- Consentire agli amministratori del reparto di visualizzare il prezzo e i costi

Un amministratore del reparto può aggiungere nuovi account ai propri reparti. Può rimuovere account dai reparti, ma non dalla registrazione.

Per aggiungere un reparto:

1. Nell'area di spostamento sinistra fare clic su **Gestisci**.
2. Fare clic sulla scheda **Reparto**, quindi su **+ Aggiungi un nuovo reparto** e immettere le informazioni necessarie.
3. Il nome del reparto è l'unico campo obbligatorio. Deve essere composto da almeno tre caratteri.
4. Al termine fare clic su **Aggiungi**.

## <a name="add-a-department-admin"></a>Aggiungere un amministratore del reparto

Dopo aver creato il reparto, l'amministratore dell'organizzazione di Azure può aggiungere amministratori del reparto e associare ognuno a un reparto. L'amministratore del reparto può:

- Creare altri amministratori del reparto
- Visualizzare e modificare le proprietà del reparto, come il nome o il centro di costo
- Aggiungere un account per i propri reparti
- Rimuovere account dai propri reparti
- Scaricare i dettagli sull'utilizzo per i propri reparti
- Visualizzare i dati di utilizzo e addebiti mensili per il reparto, se un amministratore dell'organizzazione gli ha concesso l'autorizzazione <sup>1</sup>

### <a name="to-add-a-department-admin"></a>Per aggiungere un amministratore del reparto

Un amministratore dell'organizzazione dovrà eseguire queste operazioni:

1. Nell'area di spostamento sinistra fare clic su **Gestisci**.
2. Fare clic sulla scheda **Reparto** e quindi sul reparto.
3. Fare clic su **+ Aggiungi amministratore** e immettere le informazioni necessarie.
4. Per l'accesso in sola lettura, impostare l'opzione **Sola lettura** su **Sì** e quindi fare clic su **Aggiungi**.

![Esempio che mostra la finestra di dialogo per l'aggiunta dell'amministratore del reparto](./media/billing-ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Per impostare l'accesso in sola lettura

Agli amministratori del reparto è possibile concedere l'accesso in sola lettura. Quando si crea un nuovo amministratore del reparto:

- Impostare l'opzione di sola lettura su **Sì**.

Per modificare un amministratore del reparto esistente:

1. Selezionare un reparto, quindi fare clic sul simbolo della matita accanto all'opzione **Amministratore del reparto** da modificare.
2. Impostare l'opzione di sola lettura su **Sì**. Quindi fare clic su **Salva**.

Gli utenti con il ruolo di amministratore dell'organizzazione ottengono automaticamente le autorizzazioni di amministratore del reparto.

<sup>1</sup> Se è stata concessa l'autorizzazione per visualizzare i dati sull'utilizzo e sugli addebiti mensili, ma non sono visibili, contattare il partner.

## <a name="add-an-account"></a>Aggiungi un account

La struttura di account e sottoscrizioni influisce sul modo in cui vengono amministrati e visualizzati nelle fatture e nei report. Esempi di organizzazioni tipiche includono la strutturazione in base a divisioni aziendali, team funzionali e posizioni geografiche.

Per aggiungere un account:

1. In Azure EA Portal fare clic su **Gestisci** nell'area di spostamento sinistra.
2. Fare clic sulla scheda **Account**, quindi nella pagina **Account** fare clic su **+Aggiungi account**.
3. Selezionare un reparto oppure lasciarlo come non assegnato, quindi selezionare il tipo di autenticazione desiderato.
4. Digitare un nome descrittivo da usare per identificare l'account nella creazione di report.
5. Digitare **l'indirizzo di posta elettronica del proprietario dell'account** da associare al nuovo account.
6. Confermare l'indirizzo di posta elettronica e quindi fare clic su **Aggiungi**.

![Esempio che mostra l'elenco di account e l'opzione per aggiungere un account](./media/billing-ea-portal-get-started/create-ea-add-an-account.png)

È possibile aggiungere un altro account facendo clic su **Aggiungi un altro account** oppure su **Aggiungi** nell'angolo in basso a destra della barra degli strumenti sinistra.

Per confermare la proprietà dell'account:

1. Accedere ad Azure EA Portal.
2. Verificare la proprietà dell'account visualizzando lo stato. Lo stato dovrebbe cambiare da **In sospeso** a **Data di inizio/fine**. La data di inizio/fine corrisponde alla data in cui l'utente ha eseguito per la prima volta l'accesso a alla data di termine del contratto.


## <a name="change-account-owner"></a>Cambiare il proprietario dell'account

Gli amministratori dell'organizzazione possono usare Azure EA Portal per trasferire la proprietà dell'account di sottoscrizione in una registrazione. L'azione sposta tutte le sottoscrizioni da un account utente di origine a uno di destinazione.

Note importanti sul trasferimento delle informazioni dell'account utente:

- I trasferimenti da un account aziendale o dell'istituto di istruzione a un altro account aziendale o dell'istituto di istruzione sono supportati.
- I trasferimenti da un account Microsoft a un account aziendale o dell'istituto di istruzione sono supportati.
- I trasferimenti da un account aziendale o dell'istituto di istruzione a un account Microsoft non sono supportati.
- I trasferimenti da un account Microsoft a un altro account Microsoft sono supportati. L'account di destinazione deve essere un account Azure commerciale valido per essere idoneo per il trasferimento. Per i nuovi account, viene chiesto di creare un account Azure commerciale durante l'accesso ad Azure EA Portal. Per gli account esistenti, è necessario creare prima una nuova sottoscrizione di Azure perché l'account sia idoneo.
- Al termine del trasferimento di una sottoscrizione, Microsoft aggiorna il proprietario dell'account.

Criteri di controllo degli accessi in base al ruolo:

- Per conservare i criteri di Controllo degli accessi in base al ruolo di Azure e le assegnazioni di ruoli di amministratori e coamministratori esistenti, è necessario che le sottoscrizioni di Azure vengano trasferite tra due ID organizzativi dello stesso tenant. Altri trasferimenti di sottoscrizioni generano la perdita dei criteri di Controllo degli accessi in base al ruolo e delle assegnazioni di ruoli di amministratori e coamministratori. I criteri e i ruoli di amministratore non vengono trasferiti tra directory diverse. Gli amministratori del servizio vengono aggiornati nel proprietario dell'account di destinazione.
- Quando si eseguono trasferimenti di sottoscrizioni tra due ID organizzativi dello stesso tenant, i criteri di Controllo degli accessi in base al ruolo e i ruoli di amministratori e coamministratori esistenti vengono mantenuti.

Prima di cambiare il proprietario di un account:

1. Visualizzare la scheda **Account** e identificare l'account di origine. L'account di origine deve essere attivo.
2. Identificare l'account di destinazione. Deve essere attivo.

Per trasferire la proprietà dell'account per tutte le sottoscrizioni:

1. Nell'area di spostamento sinistra fare clic su **Gestisci**.
2. Fare clic sulla scheda **Account** e passare il puntatore del mouse su un account.
3. Fare clic sul simbolo modifica del proprietario dell'account a destra. Il simbolo raffigura una persona.
4. Selezionare un account idoneo e quindi fare clic su **Avanti**.
5. Confermare il trasferimento e fare clic su **Invia**.

![Immagine che mostra il simbolo di modifica del proprietario dell'account](./media/billing-ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Per trasferire la proprietà dell'account per una sottoscrizione singola:

1. Nell'area di spostamento sinistra fare clic su **Gestisci**.
2. Fare clic sulla scheda **Account** e passare il puntatore del mouse su un account.
3. Fare clic sul simbolo dei trasferimenti di sottoscrizioni a destra. Il simbolo raffigura una pagina.
4. Selezionare una sottoscrizione idonea e quindi fare clic su **Avanti**.
5. Confermare il trasferimento e quindi fare clic su **Invia**.

![Immagine che mostra il simbolo del trasferimento di sottoscrizioni](./media/billing-ea-portal-get-started/ea-transfer-subscriptions.png)

Ecco un video che illustra la gestione degli utenti in Azure EA Portal:

[Video sulla gestione degli utenti in Azure EA Portal](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Creare una sottoscrizione

I proprietari di account possono visualizzare e gestire le sottoscrizioni. È possibile usare le sottoscrizioni per consentire ai team dell'organizzazione di accedere ad ambienti di sviluppo e progetti, ad esempio test, produzione, sviluppo e staging. Se si creano sottoscrizioni diverse per ogni ambiente applicativo, si proteggono tutti gli ambienti. È anche possibile assegnare un account di amministratore del servizio diverso per ogni sottoscrizione. È possibile associare le sottoscrizioni a un numero qualsiasi di servizi. Il proprietario dell'account crea le sottoscrizioni e assegna un account amministratore del servizio a ogni sottoscrizione nel proprio account.

### <a name="add-a-subscription"></a>Aggiungere una sottoscrizione

Usare le informazioni seguenti per aggiungere una sottoscrizione.

La prima volta che si aggiunge una sottoscrizione all'account, viene chiesto di accettare il Contratto di Sottoscrizione Microsoft Online e un piano tariffario. Anche se non sono applicabili ai clienti del contratto Enterprise Agreement, questi elementi sono necessari per creare la sottoscrizione. L'emendamento della registrazione del contratto Microsoft Azure Enterprise prevale sui suddetti elementi e la relazione contrattuale non cambia. Quando richiesto, selezionare la casella per indicare di accettare le condizioni.

Tutte le nuove sottoscrizioni vengono create con il nome di sottoscrizione predefinito, _Microsoft Azure Enterprise_. È possibile aggiornare questo nome per differenziarlo dalle altre sottoscrizioni del tenant e per assicurarsi che sia riconoscibile nei report a livello aziendale.

Per aggiungere una sottoscrizione:

1. In Azure EA Portal accedere all'account.
2. Fare clic sulla scheda **Amministrazione** e quindi su **Sottoscrizione** nella parte superiore della pagina.
2. Verificare di aver eseguito l'accesso come proprietario dell'account.
3. Fare clic su **+Aggiungi sottoscrizione** e quindi su **Acquista**.
  La prima volta che si aggiunge una sottoscrizione a un account, è necessario fornire le informazioni di contatto. Quando si aggiungono altre sottoscrizioni, le informazioni di contatto vengono aggiunte automaticamente.
4. Fare clic su **Sottoscrizioni**, quindi selezionare la sottoscrizione creata e fare clic su **Modifica i dettagli della sottoscrizione**.
5. Aggiornare i valori per **Nome sottoscrizione** e **Amministratore del servizio**, quindi selezionare il segno di spunta.
  Il nome della sottoscrizione viene visualizzato nei report e come nome del progetto associato alla sottoscrizione nel portale di sviluppo.

Per visualizzare le nuove sottoscrizioni nell'elenco, possono essere necessarie fino a 24 ore. Dopo aver creato una sottoscrizione, è possibile:

- [Modificare i dettagli della sottoscrizione](https://account.azure.com/Subscriptions)
- [Gestire i servizi della sottoscrizione](https://portal.azure.com/#home)

## <a name="transfer-pay-as-you-go-subscription-to-ea-subscription"></a>Trasferire una sottoscrizione con pagamento in base al consumo in una sottoscrizione EA

Per trasferire una singola sottoscrizione con pagamento in base al consumo in una sottoscrizione EA, è necessario creare una nuova richiesta di supporto nel portale di Azure. Per creare una richiesta di supporto, fare clic su **+ Nuova richiesta di supporto** nell'area Guida e supporto tecnico.


## <a name="view-usage-summary-and-download-reports"></a>Visualizzare il riepilogo sull'utilizzo e scaricare i report

In Azure EA Portal gli amministratori dell'organizzazione possono visualizzare un riepilogo dei dati di utilizzo, l'impegno monetario utilizzato e gli addebiti associati all'utilizzo aggiuntivo. Gli addebiti vengono presentati a livello di riepilogo in tutti gli account e le sottoscrizioni.

Per visualizzare informazioni dettagliate sull'utilizzo per specifici account

Scaricare il report Dettagli utilizzo. Fare clic su **Report** e quindi sulla scheda **Scarica utilizzo**. Nell'elenco dei report fare clic sull'opzione **Scarica** associata al report mensile da recuperare.

Il report non include imposte applicabili. Potrebbero verificarsi fino a otto ore di latenza tra l'ora dell'utilizzo e il momento in cui viene visualizzato nel report.

Per visualizzare i grafici e i report di riepilogo sull'utilizzo:

1. Nell'area di spostamento sinistra di Azure EA Portal fare clic su **Report** e visualizzare la scheda **Riepilogo utilizzo**.  
  ![](./media/billing-ea-portal-get-started/create-ea-view usage-summary-and-download-reports.png)
2. Selezionare un periodo di impegno.
3. Alternare tra **M** (mensile) e **C** (personalizzato) nella parte superiore della pagina per visualizzare il report **Riepilogo utilizzo** con date di inizio e di fine personalizzate.  
  ![](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. Per visualizzare ulteriori dettagli, selezionare un periodo o un mese sul grafico.
5. Il grafico mostra l'utilizzo mese dopo mese con una suddivisione tra utilizzo effettivo, sovraddebito del servizio, addebiti fatturati separatamente e addebiti del Marketplace.
6. Per il mese selezionato, filtrare in base a reparti, account e sottoscrizioni al di sotto del grafico.
7. Alternare tra **Charge by Services** (Addebito per servizi ) e **Charge by Hierarchy** (Addebito per gerarchia).
8. Espandere e comprimere tra **Servizio di Azure**, **Addebiti fatturati separatamente** e **Azure Marketplace** per visualizzare i dettagli.

Ecco un video che illustra come visualizzare i dati sull'utilizzo:

[Video sull'utilizzo di Azure EA Portal](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Scaricare i report CSV

Gli amministratori dell'organizzazione usano la pagina di download dei report mensili per scaricare diversi report come file CSV, tra cui:

- Saldo e addebito
- Dettagli utilizzo
- Addebiti per Marketplace
- Elenco prezzi

Per scaricare i report:


1. In Azure EA Portal fare clic su **Report**.
2. Fare clic su **Scarica utilizzo** nella parte superiore della pagina.
3. Selezionare **Download** accanto al report del mese.

Potrebbero verificarsi fino a cinque giorni di latenza tra la data di utilizzo e la data in cui viene visualizzato nei report.

Gli utenti che scaricano i file CSV con Safari in Excel potrebbero riscontrare errori di formattazione. Per evitare errori, aprire il file in un editor di testo.

![Esempio che mostra la pagina di download dei dati di utilizzo](./media/billing-ea-portal-get-started/create-ea-download-csv-reports.png)

Ecco un video che illustra come scaricare le informazioni sull'utilizzo:

[Video sull'utilizzo di Azure EA Portal](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

## <a name="schedule-an-onboarding-call"></a>Pianificare un intervento di onboarding

Se si vuole pianificare una sessione di onboarding cliente di persona, creare una richiesta di supporto nella pagina di [supporto di Azure EA Portal](https://support.microsoft.com/supportrequestform/e114582c-4e51-af46-10b1-1f0cc141e133). Selezionare **Onboarding** come **Categoria problema**.

## <a name="next-steps"></a>Passaggi successivi
- È consigliabile che gli amministratori di Azure EA Portal leggano [Amministrazione di Azure EA Portal](billing-ea-portal-administration.md) per informazioni sulle comuni attività amministrative.
- Se serve assistenza per la risoluzione dei problemi relativi ad Azure EA Portal, vedere [Risolvere i problemi di accesso ad Azure EA Portal](billing-ea-portal-troubleshoot.md).
