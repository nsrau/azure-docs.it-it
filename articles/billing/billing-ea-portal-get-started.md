---
title: Introduzione ad Azure EA Portal
description: Questo articolo illustra ai clienti con contratto Azure EA come usare Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 2f7c4f0d4d8bd7e5209f666dbfca34eaffc8ebc7
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841371"
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
1. Verificare la proprietà dell'account visualizzando lo stato. Lo stato dovrebbe cambiare da **In sospeso** a **Data di inizio/fine**. La data di inizio/fine corrisponde alla data in cui l'utente ha eseguito per la prima volta l'accesso a alla data di termine del contratto.
1. Quando viene visualizzato il messaggio di avviso, un proprietario di un account deve fare clic su **Continua** per attivare l'account la prima volta che accede ad Azure EA Portal.


## <a name="change-account-owner"></a>Cambiare il proprietario dell'account

Gli amministratori dell'organizzazione possono usare Azure EA Portal per trasferire la proprietà dell'account di sottoscrizione in una registrazione. L'azione sposta tutte le sottoscrizioni da un account utente di origine a uno di destinazione.

Note importanti sul trasferimento delle informazioni dell'account utente:

- I trasferimenti da un account aziendale o dell'istituto di istruzione a un altro account aziendale o dell'istituto di istruzione sono supportati.
- I trasferimenti da un account Microsoft a un account aziendale o dell'istituto di istruzione sono supportati.
- I trasferimenti da un account aziendale o dell'istituto di istruzione a un account Microsoft non sono supportati.
- I trasferimenti da un account Microsoft a un altro account Microsoft sono supportati. L'account di destinazione deve essere un account Azure commerciale valido per essere idoneo per il trasferimento. Per i nuovi account, viene chiesto di creare un account Azure commerciale durante l'accesso ad Azure EA Portal. Per gli account esistenti, è necessario creare prima una nuova sottoscrizione di Azure perché l'account sia idoneo.
- Al termine del trasferimento di una sottoscrizione, Microsoft aggiorna il proprietario dell'account.

Criteri di Controllo degli accessi in base al ruolo:

- Per conservare i criteri di Controllo degli accessi in base al ruolo di Azure e le assegnazioni di ruoli di amministratori e coamministratori esistenti, è necessario che le sottoscrizioni di Azure vengano trasferite tra due ID organizzativi dello stesso tenant. Altri trasferimenti di sottoscrizioni generano la perdita dei criteri di Controllo degli accessi in base al ruolo e delle assegnazioni di ruoli di amministratori e coamministratori. I criteri e i ruoli di amministratore non vengono trasferiti tra directory diverse. Gli amministratori del servizio vengono aggiornati nel proprietario dell'account di destinazione.
- Quando si eseguono trasferimenti di sottoscrizioni tra due ID organizzativi dello stesso tenant, i criteri di Controllo degli accessi in base al ruolo e i ruoli di amministratore del servizio e coamministratore esistenti vengono mantenuti.

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

## <a name="transfer-ea-subscription-to-pay-as-you-go-subscription"></a>Trasferire una sottoscrizione EA in una sottoscrizione con pagamento in base al consumo

Per trasferire una sottoscrizione EA in una singola sottoscrizione con pagamento in base al consumo, è necessario creare una nuova richiesta di supporto in Azure EA Portal. Per creare una richiesta di supporto, fare clic su **+ Nuova richiesta di supporto** nell'area Guida e supporto tecnico.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Associare un account esistente alla propria sottoscrizione con pagamento in base al consumo

Se si dispone già di un account Microsoft Azure esistente nel portale di Microsoft Azure, immettere l'account Microsoft oppure l'account aziendale o dell'istituto di istruzione associato per associarlo alla registrazione Enterprise Agreement.

### <a name="associate-an-existing-account"></a>Associare un account esistente

1. Da Enterprise Portal fare clic su **Gestisci**.
1. Fare clic sulla scheda **Account**.
1. Fare clic su **+Aggiungi account**.
1. Immettere l'account Microsoft oppure l'account aziendale o dell'istituto di istruzione associato all'account esistente.
1. Confermare l'account Microsoft oppure l'account aziendale o dell'istituto di istruzione associato all'account esistente.
1. Specificare un nome da usare per identificare l'account nei report.
1. Fare clic su **Aggiungi**.
1. È possibile aggiungere un altro account selezionando di nuovo l'opzione **+Aggiungi un account** oppure tornare alla home page selezionando il pulsante **Amministrazione**.
1. Se si fa clic per visualizzare la pagina **Account**, l'account appena aggiunto verrà visualizzato con stato **In sospeso**.

### <a name="confirm-account-ownership"></a>Confermare la proprietà dell'account

1. Accedere all'account di posta elettronica associato all'account Microsoft oppure all'account aziendale o dell'istituto di istruzione fornito.
1. Aprire la notifica di posta elettronica con oggetto "_Invitation to Activate your Account on the Microsoft Azure Service from Microsoft Volume Licensing_" (Invito ad attivare l'account nel servizio Microsoft Azure dai contratti multilicenza Microsoft).
1. Fare clic sul collegamento **Log into the Microsoft Azure Enterprise Portal** (Accedi a Microsoft Azure Enterprise Portal) nell'invito.
1. Fare clic su **Accedi**.
1. Immettere l'account Microsoft oppure l'account aziendale o dell'istituto di istruzione e la password per accedere e confermare la proprietà dell'account.

### <a name="azure-marketplace"></a>Azure Marketplace

Sebbene la maggior parte delle sottoscrizioni esegua la conversione dall'ambiente con pagamento in base al consumo a Enterprise Azure, i servizi di Azure Marketplace non procedono in tal modo. Per visualizzare tutte le sottoscrizioni e tutti gli addebiti con un solo colpo d'occhio, è consigliabile aggiungere i servizi di Azure Marketplace a Enterprise Portal:

1. Fare clic su **Gestisci** nel riquadro di spostamento sinistro.
1. Fare clic sulla scheda **Registrazione**.
1. Visualizzare la sezione relativa ai dettagli della registrazione.
1. A destra del campo Azure Marketplace fare clic sull'icona della matita per abilitarlo e scegliere **Salva**.

Il proprietario dell'account può ora acquistare le sottoscrizioni di Azure Marketplace precedentemente possedute con pagamento in base al consumo.

Una volta attivate le nuove sottoscrizioni di Azure Marketplace nella registrazione, annullare le sottoscrizioni del Marketplace create nell'ambiente con pagamento in base al consumo. Questo passaggio è fondamentale per evitare problemi con le sottoscrizioni del Marketplace quando scade lo strumento di pagamento in base al consumo.

### <a name="msdn"></a>MSDN

Gli abbonamenti MSDN vengono convertiti automaticamente in Sviluppo/test MSDN e l'offerta EA perderà il credito monetario esistente.

### <a name="azure-in-open"></a>Azure in Open

L'associazione di una sottoscrizione di Azure in Open a un Contratto Enterprise comporta la perdita di qualsiasi credito Azure in Open inutilizzato. Per evitare la possibile perdita di crediti, è opportuno che i clienti usino tutti i crediti associati a una sottoscrizione di Azure in Open prima di aggiungere l'account al proprio Contratto Enterprise.  

### <a name="accounts-with-support-subscriptions"></a>Account con sottoscrizioni al supporto

Quando si aggiungono a Enterprise Portal account esistenti che dispongono di una sottoscrizione al supporto (e non dispongono già di una sottoscrizione al supporto EA), tenere presente che la sottoscrizione al supporto del Contratto di Sottoscrizione Microsoft Online non viene trasferita automaticamente e il supporto dovrà essere riacquistato in EA. Verrà fornito un periodo di tolleranza per la copertura del supporto fino alla fine del mese successivo per consentire di riordinare il supporto.

## <a name="view-usage-summary-and-download-reports"></a>Visualizzare il riepilogo sull'utilizzo e scaricare i report

In Azure EA Portal gli amministratori dell'organizzazione possono visualizzare un riepilogo dei dati di utilizzo, l'impegno monetario utilizzato e gli addebiti associati all'utilizzo aggiuntivo. Gli addebiti vengono presentati a livello di riepilogo in tutti gli account e le sottoscrizioni.

Per visualizzare informazioni dettagliate sull'utilizzo per specifici account:

Scaricare il report Dettagli utilizzo. Fare clic su **Report** e quindi sulla scheda **Scarica utilizzo**. Nell'elenco dei report fare clic sull'opzione **Scarica** associata al report mensile da recuperare.

Il report non include imposte applicabili. Potrebbero verificarsi fino a otto ore di latenza tra l'ora dell'utilizzo e il momento in cui viene visualizzato nel report.

Per visualizzare grafici e report di riepilogo sull'utilizzo:

1. Nell'area di spostamento sinistra di Azure EA Portal fare clic su **Report** e visualizzare la scheda **Riepilogo utilizzo**.  
  ![Creare e visualizzare il riepilogo sull'utilizzo e scaricare i report](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports.png)
2. Selezionare un periodo di impegno.
3. Alternare tra **M** (mensile) e **C** (personalizzato) nella parte superiore della pagina per visualizzare il report **Riepilogo utilizzo** con date di inizio e di fine personalizzate.  
  ![Creare e visualizzare il riepilogo sull'utilizzo e scaricare i report in una visualizzazione personalizzata](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
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

### <a name="advanced-report-download"></a>Download dei report avanzati

Per la creazione di report relativi a intervalli di date o ad account specifici, è possibile usare la funzionalità di download dei report avanzati. A partire dal 30 agosto 2016, il formato del file di output è passato da xlsx a csv per contenere set di record più grandi.

1. Selezionare **Advanced Report Download** (Download report avanzati).
1. Selezionare **Appropriate Date Range** (Intervallo di date appropriato).
1. Selezionare **Appropriate Accounts** (Account appropriati).
1. Selezionare **Request Usage Data** (Richiedi dati utilizzo).
1. Fare clic sul pulsante **Aggiorna** finché lo stato del report non si imposta su **Download**.
1. Scaricare il report.

## <a name="ea-term-glossary"></a>Glossario dei termini EA

- **Account**: un'unità organizzativa in Azure EA Portal usata per amministrare le sottoscrizioni e per creare report.
- **Proprietario dell'account**: la persona identificata per gestire le sottoscrizioni e gli amministratori del servizio in Microsoft Azure. Questa persona può visualizzare i dati di utilizzo per l'account e le sottoscrizioni associate.
- **Sottoscrizione di rettifica**: una singola sottoscrizione di un anno o contestuale nell'ambito della rettifica della registrazione.
- **Impegno**: l'impegno di un importo monetario annuale per i servizi Microsoft Azure a una tariffa di impegno scontata per l'utilizzo a fronte di questo pagamento anticipato.
- **Amministratore del reparto**: la persona o le persone identificate per gestire i reparti, creare nuovi account e proprietari di account, visualizzare i dettagli di utilizzo per i reparti che gestiscono e visualizzare i costi se in possesso delle autorizzazioni necessarie.
- **Numero di registrazione**: un identificatore univoco fornito da Microsoft per identificare la registrazione specifica associata a un Contratto Enterprise.
- **Amministratore dell'organizzazione**: la persona o le persone identificate per gestire i reparti, i proprietari dei reparti, gli account e i proprietari degli account in Microsoft Azure. Questa persona ha la possibilità di gestire gli amministratori dell'organizzazione, nonché di visualizzare i dati di utilizzo, le quantità fatturate e gli addebiti non fatturati per tutti gli account e per tutte le sottoscrizioni associati all'iscrizione Enterprise.
- **Contratto Enterprise**: un contratto di licenza Microsoft per i clienti con acquisti centralizzati che intendono standardizzare l'intera organizzazione con la tecnologia Microsoft e mantenere un'infrastruttura IT su software Microsoft standard.
- **Registrazione Enterprise Agreement**: una registrazione nel programma Enterprise Agreement che fornisce prodotti Microsoft con contratti multilicenza a tariffe scontate.
- **Account Microsoft**: un servizio basato sul Web che consente ai siti di appartenenza di autenticare un utente con un unico set di credenziali.
- **Rettifica dell'iscrizione Microsoft Azure Enterprise (rettifica della registrazione)** : una rettifica firmata da un'azienda, che consente di accedere a Microsoft Azure come parte dell'iscrizione Enterprise.
- **Azure EA Portal**: il portale usato dai clienti aziendali per gestire i propri account Microsoft Azure e le sottoscrizioni correlate.
- **Quantità di risorse usata**: la quantità di un singolo servizio Microsoft Azure usata in un mese.
- **Amministratore dei servizi**: la persona identificata per accedere e gestire le sottoscrizioni e i progetti di sviluppo in Azure EA Portal.
- **Sottoscrizione** Rappresenta una sottoscrizione di Azure EA Portal ed è un contenitore di servizi Microsoft Azure gestiti dallo stesso amministratore dei servizi.
- **Account aziendale o dell'istituto di istruzione**: per le organizzazioni che hanno configurato Active Directory con la federazione nel cloud e hanno tutti gli account in un singolo tenant.

### <a name="enrollment-statuses"></a>Stati della registrazione:

- **Pending**: l'amministratore della registrazione deve accedere ad Azure EA Portal. Una volta effettuato l'accesso, la registrazione passerà a uno stato Attivo.
- **Attivo**: la registrazione è attiva ed è possibile creare account e sottoscrizioni in Azure EA Portal. La registrazione rimarrà attiva fino alla data di fine del Contratto Enterprise.
- **Periodo di validità esteso a tempo indeterminato**: si attiva dopo che è trascorsa la data di fine del Contratto Enterprise. Consente ai clienti EA inclusi nel periodo esteso di continuare a usare Azure a tempo indefinito alla fine del Contratto Enterprise. Prima che la registrazione EA raggiunga la data di fine del Contratto Enterprise, l'amministratore della registrazione deve decidere se rinnovare la registrazione aggiungendo ulteriore impegno monetario, passare a una nuova registrazione, eseguire la migrazione al programma Microsoft Online Subscription Program (MOSP) o confermare la disabilitazione di tutti i servizi associati alla registrazione.
- **Scaduta**: il cliente EA è escluso dal termine esteso e la registrazione EA ha raggiunto la data di fine del Contratto Enterprise. La registrazione scadrà e tutti i servizi associati verranno disabilitati.
- **Trasferita**: le registrazioni i cui account e servizi associati sono stati trasferiti a una nuova registrazione verranno visualizzati con questo stato. Si noti che le registrazioni non vengono trasferite automaticamente se viene generato un nuovo numero di registrazione durante il rinnovo. Per facilitare un trasferimento automatico, il numero di registrazione precedente deve essere incluso nel documento di rinnovo del cliente.

## <a name="get-started-on-azure-ea-faq"></a>Domande frequenti per iniziare a usare Azure EA

Questo documento fornisce informazioni dettagliate sulle domande tipiche poste dai clienti durante il processo di onboarding.  

### <a name="can-i-associate-my-existing-azure-account-to-enterprise-enrollment"></a>È possibile associare un account Azure esistente all'iscrizione Enterprise?

Sì, è importante notare tuttavia che tutte le sottoscrizioni di Azure di cui si è proprietari dell'account verranno convertite nel Contratto Enterprise. Sono incluse le sottoscrizioni che usano il credito mensile (ad esempio Visual Studio, AzurePass, MPN, BizSpark e così via), pertanto procedendo in questo modo si perderà credito mensile.

### <a name="i-accidentally-associated-my-existing-azure-account-with-enterprise-enrollment-as-a-result-i-lost-my-monthly-credit-is-it-possible-to-get-my-monthly-credit-back"></a>L'account Azure esistente è stato associato per errore all'iscrizione Enterprise e di conseguenza il credito mensile è andato perduto. È possibile riottenerlo?

Per ripristinare i vantaggi di Azure per le singole sottoscrizioni di Visual Studio dopo aver eseguito l'autenticazione come proprietario dell'account EA, usare per EA le stesse informazioni di accesso della sottoscrizione di Visual Studio e quindi effettuare una delle operazioni seguenti:
1. Eliminare questo proprietario dell'account da EA Portal dopo aver rimosso o spostato le sottoscrizioni di Azure di cui è proprietario e avergli fatto eseguire di nuovo l'iscrizione ai singoli vantaggi di Azure per Visual Studio.
 Oppure
1. Eliminare il sottoscrittore di Visual Studio dal sito di amministrazione in VLSC e riassegnare la sottoscrizione, facendo in modo che questa volta usi altre informazioni di accesso. Potrà quindi eseguire di nuovo l'iscrizione ai singoli vantaggi di Azure per Visual Studio.

### <a name="what-type-of-subscription-should-i-create"></a>Quale tipo di sottoscrizione è necessario creare?

EA Portal offre due tipi di sottoscrizioni per i clienti aziendali:

- Microsoft Azure Enterprise, ideale per:
  - Tutti gli scopi di produzione
  - Prezzi migliori in base all'uso dell'infrastruttura
  - Altri dettagli sono disponibili all'indirizzo https://azure.microsoft.com/pricing/enterprise-agreement/
- Sviluppo/test Enterprise, ideale per:
  - Tutti i carichi di lavoro di sviluppo/test per i team
  - Singoli carichi di lavoro di sviluppo/test da medi a pesanti
  - Accesso a speciali immagini MSDN e tariffe per servizi preferenziali
  - Altri dettagli sono disponibili all'indirizzo https://azure.microsoft.com/offers/ms-azr-0148p/

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>È possibile trasferire la proprietà di una sottoscrizione a un altro account?

Sì, la proprietà di una sottoscrizione può essere trasferita a un altro account. Se ad esempio un account A dispone di tre sottoscrizioni, l'amministratore dell'organizzazione può trasferirne una all'account B, una all'account C e una all'account D oppure può trasferirle tutte all'account E.

È possibile passare a EA e fare clic su Gestisci > Account, passare il puntatore su **Account** (estrema destra) e verranno visualizzate le opzioni di trasferimento della proprietà (icona foto ritratto) e trasferimento della sottoscrizione (icona elenco).

Questa opzione sarà visibile solo per gli account attivi.

### <a name="i-see-subscription-name-defaults-to-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Come nome della sottoscrizione viene visualizzato per impostazione predefinita il nome dell'offerta. È necessario sostituirlo con un nome significativo per la propria organizzazione?

Per le sottoscrizioni create verrà utilizzato per impostazione predefinita il tipo di offerta scelto. Si consiglia di cambiare il nome della sottoscrizione usando un nome significativo per facilitarne la ricerca.

**Per cambiare il nome:**
1. Accedere a [https://account.windowsazure.com](https://account.windowsazure.com).
1. Fare clic sull'elenco delle sottoscrizioni.
1. Selezionare la sottoscrizione.
1. Fare clic sull'icona **Gestisci sottoscrizione**.
1. Modificare i dettagli della sottoscrizione.

### <a name="how-can-i-track-cost-incurred-by-cost-center"></a>Come è possibile tenere traccia dei costi sostenuti in base al centro di costo?

Per tenere traccia dei costi in base al centro di costo, è necessario definire il centro di costo a uno dei livelli seguenti:
- department
- Account
- Subscription

A seconda delle esigenze, è possibile usare lo stesso centro di costo per tenere traccia dell'utilizzo e dei costi associati a un centro di costo specifico.

Ad esempio, per tenere traccia dei costi di un progetto speciale in cui sono coinvolti più reparti, è consigliabile usare il centro di costo a livello di sottoscrizione per tenere traccia dell'utilizzo e dei costi.

Non è possibile definire il centro di costo a livello di servizio. Se si intende tenere traccia dell'utilizzo a livello di servizio, è possibile usare la funzionalità "Tag" disponibile a livello di servizio.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Come è possibile tenere traccia dell'utilizzo e della spesa in base a diversi reparti in un'organizzazione?

È possibile creare tutti i reparti di cui si necessita nella registrazione EA. Per tenere traccia correttamente dell'utilizzo, è necessario assicurarsi che le sottoscrizioni non siano condivise tra i reparti.

Una volta completata la creazione del reparto e della sottoscrizione, è possibile visualizzare il flusso di informazioni nel report relativo all'utilizzo in modo da poter tenere traccia dell'utilizzo e gestire i costi e le spese a livello di reparto.

È anche possibile accedere all'utilizzo tramite API. Informazioni dettagliate e codice di esempio sono disponibili all'indirizzo [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

### <a name="can-i-set-the-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>È possibile impostare la quota di spesa e ricevere gli avvisi quando si avvicina il limite?

È possibile impostare la quota di spesa a livello di reparto e il sistema invierà automaticamente una notifica quando i limiti di spesa raggiungono il 50%, il 75%, il 90% e il 100% della quota definita.

Per definire la quota di spesa, fare clic sul reparto a cui si vuole aggiungere un limite di spesa e quindi sull'icona di modifica. Fare clic su **Salva** per salvare i dettagli.

### <a name="i-used-resource-groups-rgs-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Se si usano gruppi di risorse per implementare il controllo degli accessi in base al ruolo e tenere traccia dell'utilizzo, come è possibile visualizzare i dettagli sull'utilizzo associati?

Informazioni quali "Gruppi di risorse" e "Tag", se usate, vengono rilevate a livello di servizio e sono disponibili nel file di download dettagliato sull'utilizzo (CSV), che può essere scaricato da Azure EA Portal [https://ea.azure.com/report/downloadusage](https://ea.azure.com/report/downloadusage).

È anche possibile accedere all'utilizzo tramite API. Informazioni dettagliate e codice di esempio sono disponibili all'indirizzo [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

Si noti che i tag possono essere applicati solo alle risorse che supportano le operazioni di Resource Manager. Se è stata creata una macchina virtuale, una rete virtuale o una risorsa di archiviazione tramite il modello di distribuzione classica, ad esempio tramite il portale classico, non è possibile applicare un tag a tale risorsa. Per supportare l'assegnazione di tag, distribuire nuovamente tali risorse tramite Resource Manager. Tutte le altre risorse supportano l'assegnazione di tag.

### <a name="can-i-perform-analyses-using-power-bi"></a>È possibile eseguire analisi tramite Power BI?

Sì. Il pacchetto di contenuto di Microsoft Azure Enterprise per Power BI consente di importare e analizzare rapidamente il consumo di Azure per l'iscrizione Enterprise, di individuare il reparto, l'account o la sottoscrizione che ha consumato la maggiore quantità di quota di utilizzo e i servizi più usati dall'organizzazione o di tenere traccia della spesa e delle tendenze di utilizzo.

**Passare al sito Web di Power BI:**

 1. Accedere con un account aziendale o dell'istituto di istruzione valido.
    - L'account aziendale o dell'Istituto di istruzione può essere uguale o diverso da quello usato per accedere alla registrazione tramite Azure EA Portal.
 1. Nel dashboard dei servizi scegliere:
    - Il riquadro di Microsoft Azure Enterprise.
    - Fare clic su **Connetti**.
 1. Nella schermata di connessione ad Azure Enterprise scegliere:
    - Azure Environment URL (URL dell'ambiente di Azure): [https://ea.azure.com](https://ea.azure.com).
    - Numero di mesi: scegliere un valore compreso tra 1 e 36.
    - Numero di registrazione: immettere il numero di registrazione.
    - Fare clic su **Avanti**.
 1. Immettere la chiave API nella casella Chiave di autenticazione. È possibile ottenere la chiave API in Azure EA Portal nella scheda "Scarica utilizzo" facendo clic su **API Access Key** (Chiave di accesso API).
    - Copiare e incollare la chiave nella casella "Chiave account".
    - Il caricamento dei dati in Power BI richiederà circa 5-30 minuti, a seconda delle dimensioni del set di dati.

La creazione di report di Power BI è disponibile per i partner EA diretti e per i clienti EA indiretti che possono visualizzare le informazioni di fatturazione.

## <a name="next-steps"></a>Passaggi successivi

- È consigliabile che gli amministratori di Azure EA Portal leggano [Amministrazione di Azure EA Portal](billing-ea-portal-administration.md) per informazioni sulle comuni attività amministrative.
- Se serve assistenza per la risoluzione dei problemi relativi ad Azure EA Portal, vedere [Risolvere i problemi di accesso ad Azure EA Portal](billing-ea-portal-troubleshoot.md).
- Per una guida sull'onboarding di Azure EA, vedere [Guida all'onboarding di Azure EA](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).
