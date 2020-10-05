---
title: Introduzione ad Azure Enterprise Portal
description: Questo articolo illustra come i clienti con un Contratto Enterprise di Azure (Azure EA, Azure Enterprise Agreement) usano Azure Enterprise Portal.
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 0404e3b268f5797e34b3818df8eebeefbe68a1f3
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371950"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Introduzione ad Azure Enterprise Portal

Questo articolo assiste i clienti diretti e indiretti del Contratto Enterprise di Azure (Azure EA) per iniziare a usare [Azure Enterprise Portal](https://ea.azure.com). Sono disponibili informazioni di base sui seguenti argomenti:

- Struttura di Azure Enterprise Portal.
- Ruoli usati in Azure Enterprise Portal.
- Creazione della sottoscrizione.
- Analisi dei costi in Azure Enterprise Portal e nel portale di Azure.

Questo video contiene una sessione completa di onboarding di Azure Enterprise Portal:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-enterprise-portal-hierarchy"></a>Gerarchia di Azure Enterprise Portal

La gerarchia di Azure Enterprise Portal è costituita da:

- **Azure Enterprise Portal**: portale di gestione online che consente di gestire i costi dei servizi del Contratto Enterprise di Azure. È possibile:

  - Creare una gerarchia del Contratto Enterprise di Azure con reparti, account e sottoscrizioni.
  - Riconciliare i costi dei servizi utilizzati, scaricare report di utilizzo e visualizzare i listini prezzi.
  - Creare chiavi API per la registrazione.

- **Reparti**: facilita la segmentazione dei costi in raggruppamenti logici. Tramite i reparti è possibile impostare un budget o una quota a livello di reparto.

- **Account**: sono le unità organizzative in Azure Enterprise Portal. È possibile usare gli account per gestire le sottoscrizioni e accedere ai report.

- **Sottoscrizioni**: sono le unità più piccole in Azure Enterprise Portal. Si tratta di contenitori per i servizi di Azure gestiti dall'amministratore del servizio.

Il diagramma seguente illustra le semplici gerarchie di Azure EA.

![Diagramma delle semplici gerarchie di Azure EA](./media/ea-portal-get-started/ea-hierarchies.png)

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

## <a name="activate-your-enrollment"></a>Attivare la registrazione

Per attivare il servizio l'amministratore dell'organizzazione iniziale apre [Azure Enterprise Portal](https://ea.azure.com) e accede usando l'indirizzo di posta elettronica incluso nel messaggio di invito.

Se si ha il ruolo di amministratore dell'organizzazione, non è necessario ricevere il messaggio di posta elettronica di attivazione. Aprire [Azure Enterprise Portal](https://ea.azure.com) e accedere con il proprio indirizzo di posta elettronica (account aziendale, dell'istituto di istruzione o account Microsoft) e la password corrispondente.

Se si hanno più registrazioni, scegliere quale attivare. Per impostazione predefinita, vengono visualizzate solo le registrazioni attive. Per visualizzare la cronologia delle registrazioni, deselezionare l'opzione **Attivo** nell'angolo in alto a destra di Azure Enterprise Portal.

In **Registrazione** lo stato visualizzato è **Attivo**.

![Esempio di una registrazione attiva](./media/ea-portal-get-started/ea-enrollment-status.png)

Solo gli attuali amministratori dell'organizzazione di Azure possono creare altri amministratori dell'organizzazione.

### <a name="create-another-enterprise-administrator"></a>Creare un altro amministratore dell'organizzazione

Per aggiungere un altro amministratore dell'organizzazione:

1. Accedere ad [Azure Enterprise Portal](https://ea.azure.com).
1. Passare a **Gestisci** > **Dettagli della registrazione**.
1. In alto a destra selezionare **+ Aggiungi amministratore**.

Assicurarsi di avere l'indirizzo di posta elettronica dell'utente e il metodo di autenticazione preferito, ad esempio un account aziendale o dell'istituto di istruzione oppure un account Microsoft.

Se non si è l'amministratore dell'organizzazione, contattare un amministratore dell’organizzazione per chiedere di essere aggiunti a una registrazione. Dopo l'aggiunta in una registrazione, si riceverà un messaggio di posta elettronica di attivazione.

Se l'amministratore dell'organizzazione non è in grado di offrire assistenza, creare una [richiesta di supporto per Azure Enterprise Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Specificare le informazioni seguenti:

- Numero di registrazione
- Indirizzo di posta elettronica da aggiungere e tipo di autenticazione (account aziendale, dell'istituto di istruzione o Microsoft)
- Approvazione tramite posta elettronica di un amministratore dell'organizzazione esistente
  - Se l'amministratore dell'organizzazione esistente non è disponibile, contattare il partner o il software advisor per chiedere di modificare le informazioni di contatto tramite lo strumento Volume Licensing Service Center (VLSC).

Per altre informazioni, vedere [Informazioni sui ruoli amministrativi per il contratto Azure Enterprise Agreement](understand-ea-roles.md).

## <a name="create-an-azure-enterprise-department"></a>Creare un reparto di Azure Enterprise

Gli amministratori dell'organizzazione e gli amministratori del reparto usano i reparti per organizzare e creare report sui servizi di Azure dell'organizzazione e sull'utilizzo in base a reparto e a centro di costo. L'amministratore dell'organizzazione può:

- Aggiungere o rimuovere reparti.
- Associare un account a un reparto.
- Creare amministratori del reparto.
- Consentire agli amministratori del reparto di visualizzare il prezzo e i costi.

Un amministratore del reparto può aggiungere nuovi account ai propri reparti. Può rimuovere account dai reparti, ma non dalla registrazione.

Per aggiungere un reparto:

1. Accedere ad Azure Enterprise Portal.
1. Nel riquadro sinistro selezionare **Gestisci**.
1. Selezionare la scheda **Reparto** e quindi selezionare **+ Aggiungi reparto**.
1. Immettere le informazioni.
   Il nome del reparto è l'unico campo obbligatorio. Deve essere composto da almeno tre caratteri.
1. Al termine selezionare **Aggiungi**.

## <a name="add-a-department-administrator"></a>Aggiungere un amministratore del reparto

Dopo aver creato il reparto, l'amministratore dell'organizzazione di Azure può aggiungere amministratori del reparto e associare ogni amministratore a un reparto. Gli amministratori del reparto possono eseguire le azioni seguenti per i reparti:

- Creare altri amministratori del reparto
- Visualizzare e modificare le proprietà del reparto, ad esempio il nome o il centro di costo
- Aggiungere account
- Rimuovere account
- Scaricare dettagli dell'utilizzo
- Visualizzare l'utilizzo mensile e gli addebiti <sup>1</sup>

> <sup>1</sup> Queste autorizzazioni devono essere concesse da un amministratore dell'organizzazione. Se è presente l'autorizzazione per visualizzare i dati sull'utilizzo e sugli addebiti mensili ma questi non sono visibili, contattare il partner.

### <a name="to-add-a-department-administrator"></a>Per aggiungere un amministratore del reparto

Un amministratore dell'organizzazione dovrà eseguire queste operazioni:

1. Accedere ad Azure Enterprise Portal.
1. Nel riquadro sinistro selezionare **Gestisci**.
1. Selezionare la scheda **Reparto** e quindi selezionare il reparto.
1. Selezionare **+ Aggiungi amministratore** e immettere le informazioni necessarie.
1. Per l'accesso in sola lettura impostare l'opzione **Sola lettura** su **Sì** e quindi selezionare **Aggiungi**.

![Esempio con la finestra di dialogo per l'aggiunta dell'amministratore del reparto](./media/ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Per impostare l'accesso in sola lettura

Agli amministratori del reparto è possibile concedere l'accesso in sola lettura.

- Quando si crea un nuovo amministratore del reparto, impostare l'opzione per la sola lettura su **Sì**.

- Per modificare un amministratore del reparto esistente:
   1. Selezionare un reparto, quindi selezionare il simbolo della matita accanto all'opzione **Amministratore del reparto** da modificare.
   1. Impostare l'apertura di sola lettura su **Sì** e quindi selezionare **Salva**.

Gli amministratori dell'organizzazione ottengono automaticamente le autorizzazioni di amministratore del reparto.

## <a name="add-an-account"></a>Aggiungi un account

La struttura di account e sottoscrizioni influisce sul modo in cui questi elementi vengono amministrati e visualizzati nelle fatture e nei report. Sono esempi tipici di strutture di organizzazione le divisioni aziendali, i team funzionali e le posizioni geografiche.

Per aggiungere un account:

1. In Azure Enterprise Portal selezionare **Gestisci** nel riquadro di spostamento sinistro.
1. Fare clic sulla scheda **Account**. Nella pagina **Account** fare clic su **+Aggiungi account**.
1. Selezionare un reparto oppure lasciarlo come non assegnato, quindi selezionare il tipo di autenticazione desiderato.
1. Digitare un nome descrittivo per identificare l'account nella creazione di report.
1. Immettere **l'indirizzo di posta elettronica del proprietario dell'account** da associare al nuovo account.
1. Confermare l'indirizzo di posta elettronica e quindi selezionare **Aggiungi**.

![Esempio che mostra l'elenco di account e l'opzione per aggiungere un account](./media/ea-portal-get-started/create-ea-add-an-account.png)

Per aggiungere un altro account selezionare **Aggiungi un altro account** oppure selezionare **Aggiungi** nell'angolo in basso a destra della barra degli strumenti sinistra.

Per confermare la proprietà dell'account:

1. Accedere ad Azure Enterprise Portal.
1. Visualizzare lo stato.

   Lo stato dovrebbe cambiare da **In sospeso** a **Data di inizio/fine**. La data di inizio/fine corrisponde alla data in cui l'utente ha eseguito per la prima volta l'accesso a alla data di termine del contratto.
1. Quando viene visualizzato il **messaggio di avviso**, il proprietario di un account deve selezionare **Continua** per attivare l'account quando accede per la prima volta ad Azure Enterprise Portal.

## <a name="change-account-owner"></a>Cambiare il proprietario dell'account

Gli amministratori dell'organizzazione possono usare Azure Enterprise Portal per trasferire la proprietà dell'account di sottoscrizione in una registrazione. L'azione sposta tutte le sottoscrizioni da un account utente di origine a un account utente di destinazione.

Per il trasferimento degli account tenere a mente queste importanti informazioni:

- È possibile eseguire questi trasferimenti:
  - Da un account aziendale o dell'istituto di istruzione a un altro account aziendale o dell'istituto di istruzione.
  - Da un account Microsoft a un account aziendale o dell'istituto di istruzione.
  - Da un account Microsoft a un altro account Microsoft.

    L'account di destinazione deve essere un account Azure commerciale valido per essere idoneo per il trasferimento. Per i nuovi account viene chiesto di creare un account Azure commerciale durante l'accesso ad Azure Enterprise Portal. Per gli account esistenti, è necessario creare prima una nuova sottoscrizione di Azure perché l'account sia idoneo.

- Non è possibile eseguire un trasferimento da un account aziendale o dell'istituto di istruzione a un account Microsoft.

- Al termine del trasferimento di una sottoscrizione, Microsoft aggiorna il proprietario dell'account.

Informazioni sui criteri di Controllo degli accessi in base al ruolo di Azure:

- Quando si eseguono trasferimenti di sottoscrizioni tra due ID organizzativi dello stesso tenant, i criteri di Controllo degli accessi in base al ruolo di Azure e i ruoli di amministratore del servizio e coamministratore esistenti vengono mantenuti.
- Altri trasferimenti di sottoscrizioni generano la perdita dei criteri di Controllo degli accessi in base al ruolo di Azure e delle assegnazioni di ruoli.
- I criteri e i ruoli di amministratore non vengono trasferiti tra directory diverse. Gli amministratori del servizio vengono aggiornati nel proprietario dell'account di destinazione.

Prima di cambiare il proprietario di un account:

1. In Azure Enterprise Portal visualizzare la scheda **Account** e identificare l'account di origine. L'account di origine deve essere attivo.
1. Identificare l'account di destinazione e verificare che sia attivo.

Per trasferire la proprietà dell'account per tutte le sottoscrizioni:

1. Accedere ad Azure Enterprise Portal.
1. Nell'area di spostamento sinistra selezionare **Gestisci**.
1. Selezionare la scheda **Account** e passare il puntatore del mouse su un account.
1. Selezionare l'icona di modifica del proprietario dell'account sulla destra. L'icona raffigura una persona.
1. Scegliere un account idoneo e selezionare **Avanti**.
1. Confermare il trasferimento e selezionare **Invia**.

![Immagine che mostra il simbolo di modifica del proprietario dell'account](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Per trasferire la proprietà dell'account per una sottoscrizione singola:

1. Accedere ad Azure Enterprise Portal.
1. Nell'area di spostamento sinistra selezionare **Gestisci**.
1. Selezionare la scheda **Account** e passare il puntatore del mouse su un account.
1. Selezionare l'icona di trasferimento sottoscrizioni a destra. L'icona raffigura una pagina.
1. Scegliere una sottoscrizione idonea e selezionare **Avanti**.
1. Confermare il trasferimento e selezionare **Invia**.

![Immagine che mostra il simbolo del trasferimento di sottoscrizioni](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

Visualizzare in questo video la gestione utenti di Azure Enterprise Portal:

> [!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Creare una sottoscrizione

I proprietari di account possono visualizzare e gestire le sottoscrizioni. È possibile usare le sottoscrizioni per consentire ai team dell'organizzazione di accedere ad ambienti di sviluppo e progetti, ad esempio test, produzione, sviluppo e staging.

Se si creano sottoscrizioni diverse per ogni ambiente applicativo, si proteggono tutti gli ambienti.

- È anche possibile assegnare un account di amministratore del servizio diverso per ogni sottoscrizione.
- È possibile associare le sottoscrizioni a un numero qualsiasi di servizi.
- Il proprietario dell'account crea le sottoscrizioni e assegna un account amministratore del servizio a ogni sottoscrizione nel proprio account.

### <a name="add-a-subscription"></a>Aggiungere una sottoscrizione

Usare le informazioni seguenti per aggiungere una sottoscrizione.

Quando si aggiunge per la prima volta una sottoscrizione all'account viene chiesto di accettare il Contratto di Sottoscrizione Microsoft Online e un piano tariffario. Anche se non sono applicabili ai clienti del Contratto Enterprise, il Contratto di Sottoscrizione Microsoft Online e il piano tariffario sono necessari per creare la sottoscrizione. L'emendamento della registrazione del contratto Microsoft Azure Enterprise prevale sui suddetti elementi e la relazione contrattuale non cambia. Quando richiesto, selezionare la casella che indica che si accettano le condizioni.

Quando si crea una sottoscrizione il nome predefinito è _Microsoft Azure Enterprise_. È possibile modificare il nome della sottoscrizione per distinguerla dalle altre sottoscrizioni della registrazione e per assicurarsi che sia riconoscibile nei report a livello aziendale.

Per aggiungere una sottoscrizione:

1. In Azure Enterprise Portal accedere all'account.
1. Selezionare la scheda **Amministrazione** e quindi selezionare **Sottoscrizione** nella parte superiore della pagina.
1. Verificare di aver eseguito l'accesso come proprietario dell'account.
1. Selezionare **+Aggiungi sottoscrizione** e quindi selezionare **Aggiorna**.

   La prima volta che si aggiunge una sottoscrizione a un account, è necessario fornire le informazioni di contatto. Quando si aggiungono altre sottoscrizioni, le informazioni di contatto dell'utente vengono aggiunte automaticamente.

1. Selezionare **Sottoscrizioni** e quindi selezionare la sottoscrizione creata.
1. Selezionare **Modifica i dettagli della sottoscrizione**.
1. Modificare i valori di **Nome sottoscrizione** e **Amministratore del servizio**, quindi selezionare il segno di spunta.

   Il nome della sottoscrizione viene visualizzato nei report. È il nome del progetto associato alla sottoscrizione nel portale di sviluppo.

Per visualizzare le nuove sottoscrizioni nell'elenco, possono essere necessarie fino a 24 ore. Dopo aver creato una sottoscrizione, è possibile:

- [Modificare i dettagli della sottoscrizione](https://account.azure.com/Subscriptions)
- [Gestire i servizi della sottoscrizione](https://portal.azure.com/#home)

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Trasferimento di una sottoscrizione Enterprise a una sottoscrizione con pagamento in base al consumo

Per trasferire una sottoscrizione Enterprise a una sottoscrizione individuale con pagamento in base al consumo è necessario creare una nuova richiesta di supporto in Azure Enterprise Portal. Per creare una richiesta di supporto selezionare **+ Nuova richiesta di supporto** nell'area **Guida e supporto tecnico**.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Associare un account esistente alla propria sottoscrizione con pagamento in base al consumo

Se è già presente un account Microsoft Azure nel portale di Azure, immettere l'account aziendale, dell'istituto di istruzione o l'account Microsoft associato per collegarlo alla registrazione del Contratto Enterprise.

### <a name="associate-an-existing-account"></a>Associare un account esistente

1. In Azure Enterprise Portal selezionare **Gestisci**.
1. Fare clic sulla scheda **Account**.
1. Selezionare **+Aggiungi account**.
1. Immettere l'account aziendale, dell'istituto di istruzione o l'account Microsoft associato all'account Azure esistente.
1. Confermare l'account associato all'account Azure esistente.
1. Specificare un nome da usare per identificare l'account nei report.
1. Selezionare **Aggiungi**.
1. Per aggiungere un altro account selezionare di nuovo l'opzione **+Aggiungi account** oppure tornare alla home page selezionando il pulsante **Amministrazione**.
1. Se si visualizza la scheda **Account**, l'account appena aggiunto viene visualizzato con lo stato **In sospeso**.

### <a name="confirm-account-ownership"></a>Confermare la proprietà dell'account

1. Accedere all'account di posta elettronica associato all'account aziendale, dell'istituto di istruzione o all'account Microsoft specificato.
1. Aprire la notifica di posta elettronica con oggetto "_Invitation to Activate your Account on the Microsoft Azure Service from Microsoft Volume Licensing_" (Invito ad attivare l'account nel servizio Microsoft Azure dai contratti multilicenza Microsoft).
1. Fare clic sul collegamento **Log into the Microsoft Azure Enterprise Portal** (Accedi a Microsoft Azure Enterprise Portal) nell'invito.
1. Fare clic su **Accedi**.
1. Immettere l'account aziendale, dell'istituto di istruzione o l'account Microsoft e la password corrispondente per accedere e confermare la proprietà dell'account.

### <a name="azure-marketplace"></a>Azure Marketplace

È possibile convertire la maggior parte delle sottoscrizioni dall'ambiente con pagamento in base al consumo al Contratto Enterprise di Azure, ma questo non vale per i servizi Azure Marketplace. Per visualizzare tutte le sottoscrizioni e tutti gli addebiti in un'unica vista è consigliabile aggiungere i servizi Azure Marketplace ad Azure Enterprise Portal.

1. Accedere ad Azure Enterprise Portal.
1. Nel riquadro di spostamento sinistro selezionare **Gestisci**.
1. Selezionare la scheda **Registrazione**.
1. Visualizzare la sezione **Dettagli della registrazione**.
1. Sulla destra del campo Azure Marketplace selezionare l'icona della matita per abilitare il campo. Selezionare **Salva**.

Ora il proprietario dell'account può acquistare qualsiasi servizio Azure Marketplace che in precedenza usava con pagamento in base al consumo.

Dopo che le nuove sottoscrizioni di Azure Marketplace sono attivate nella registrazione, annullare i servizi Azure Marketplace creati nell'ambiente con pagamento in base al consumo. Questo passaggio è fondamentale per evitare problemi con le sottoscrizioni di Azure Marketplace quando scade lo strumento per il pagamento in base al consumo.

### <a name="msdn"></a>MSDN

Le sottoscrizioni MSDN vengono convertite automaticamente in Sviluppo/test MSDN e l'offerta del Contratto Enterprise di Azure perde il credito monetario esistente.

### <a name="azure-in-open"></a>Azure in Open

Se si associa una sottoscrizione Azure in Open a un Contratto Enterprise, qualsiasi credito Azure in Open non usato va perduto. Pertanto è consigliabile usare tutto il credito della sottoscrizione Azure in Open prima di aggiungere l'account al Contratto Enterprise.  

### <a name="accounts-with-support-subscriptions"></a>Account con sottoscrizioni al supporto

Se il Contratto Enterprise non dispone di una sottoscrizione al supporto e si aggiunge ad Azure Enterprise Portal un account esistente con una sottoscrizione al supporto, la sottoscrizione al supporto del Contratto di Sottoscrizione Microsoft Online (MOSA) non viene trasferita automaticamente. È necessario tornare ad acquistare una sottoscrizione al supporto nel Contratto Enterprise di Azure durante il periodo di tolleranza, ovvero entro la fine del mese successivo.

## <a name="view-usage-summary-and-download-reports"></a>Visualizzare il riepilogo sull'utilizzo e scaricare i report

Gli amministratori aziendali possono visualizzare un riepilogo dei dati di utilizzo, la quota utilizzata di Pagamento anticipato di Azure e gli addebiti associati all'utilizzo aggiuntivo in Azure Enterprise Portal. Gli addebiti vengono presentati a livello di riepilogo in tutti gli account e le sottoscrizioni.

Per visualizzare informazioni dettagliate sull'utilizzo per account specifici, scaricare il report relativo ai dettagli dell'utilizzo:

1. Accedere ad Azure Enterprise Portal.
1. Selezionare **Report**.
1. Selezionare la scheda **Scarica utilizzo**.
1. Nell'elenco dei report selezionare l'opzione **Scarica** associata al report mensile da recuperare.

   > [!NOTE]
   > Il report relativo ai dettagli dell'utilizzo non include le imposte applicabili.
   >
   > Potrebbero verificarsi fino a otto ore di latenza tra l'ora dell'utilizzo e il momento in cui questo viene visualizzato nel report.

Per visualizzare grafici e report di riepilogo sull'utilizzo:

1. Accedere ad Azure Enterprise Portal.

1. Selezionare un pagamento anticipato.

   Per modificare l'intervallo di date per **Riepilogo utilizzo** è possibile spostare l'interruttore da **M** (Mensile) a **C** (Custom, Personalizzato) nell'angolo superiore destro della pagina, quindi immettere date di inizio e fine personalizzate.

   ![Creare e visualizzare il riepilogo dell'utilizzo e scaricare i report in una visualizzazione personalizzata](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Per visualizzare altri dettagli, selezionare un periodo o un mese sul grafico.

   - Il grafico visualizza l'utilizzo mese per mese, con una suddivisione tra utilizzo effettivo, costi aggiuntivi del servizio, addebiti fatturati separatamente e addebiti di Azure Marketplace.
   - Per il mese selezionato è possibile usare i campi sotto il grafico per applicare filtri in base a reparti, account e sottoscrizioni.
   - È possibile alternare tra **Charge by Services** (Addebito per servizi ) e **Charge by Hierarchy** (Addebito per gerarchia).
   - Per visualizzare i dettagli di **Servizio di Azure**, **Addebiti fatturati separatamente** e **Azure Marketplace** espandere le sezioni corrispondenti.

Per informazioni su come visualizzare l'utilizzo, guardare questo video:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Scaricare i report CSV

Gli amministratori dell'organizzazione usano la pagina di download dei report mensili per scaricare i seguenti report come file con estensione csv:

- Saldo e addebito
- Dettagli utilizzo
- Addebiti per Azure Marketplace
- Elenco prezzi

Per scaricare i report:

1. In Azure Enterprise Portal selezionare **Report**.
2. Selezionare **Scarica utilizzo** nella parte superiore della pagina.
3. Selezionare **Download** accanto al report del mese.

   > [!NOTE]
   > Potrebbero verificarsi fino a cinque giorni di latenza tra la data di utilizzo e la data in cui viene visualizzato nei report.
   >
   > Gli utenti che scaricano i file CSV con Safari in Excel potrebbero riscontrare errori di formattazione. Per evitare errori, aprire il file in un editor di testo.

![Esempio che mostra la pagina di download dei dati di utilizzo](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

Guardare questo video per informazioni su come scaricare le informazioni sull'utilizzo:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Download dei report avanzati

È possibile usare il download dei report avanzati per ottenere report relativi a intervalli di date o account specifici. Il file di output è in formato CSV per il supporto di set di record di grandi dimensioni.

1. In Azure Enterprise Portal selezionare **Advanced Report Download** (Download dei report avanzati).
1. Selezionare un intervallo di date appropriato e gli account appropriati.
1. Selezionare **Request Usage Data** (Richiedi dati utilizzo).
1. Fare clic sul pulsante **Aggiorna** fino a quando lo stato del report non diventa **Download**.
1. Scaricare il report.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Scaricare i report sull'utilizzo e le informazioni di fatturazione per una registrazione precedente

È possibile scaricare i report sull'utilizzo e le informazioni di fatturazione per una registrazione precedente dopo che è stato eseguito un trasferimento della registrazione. La creazione di report cronologici è disponibile sia in Azure Enterprise Portal sia nella gestione dei costi.

Azure Enterprise Portal esclude con un filtro la visualizzazione delle registrazioni inattive. Per visualizzare le registrazioni trasferite inattive è necessario deselezionare la casella **Attivo**.  

![Deselezione della casella Attivo per visualizzare le registrazioni inattive](./media/ea-portal-get-started/unchecked-active-box.png)

## <a name="azure-ea-term-glossary"></a>Glossario dei termini del Contratto Enterprise di Azure

- **Account**: unità organizzativa in Azure Enterprise Portal. Viene usato per l'amministrazione delle sottoscrizioni e la creazione di report.
- **Proprietario dell'account**: persona che gestisce le sottoscrizioni e gli amministratori del servizio in Azure. Questa persona può visualizzare i dati di utilizzo per l'account e le sottoscrizioni associate.
- **Sottoscrizione di rettifica**: sottoscrizione di un anno o contestuale nell'ambito della rettifica della registrazione.
- **Pagamento anticipato**: pagamento anticipato di un importo monetario annuale per i servizi Azure a una tariffa di pagamento anticipato scontata per l'utilizzo a fronte di questo pagamento anticipato.
- **Amministratore del reparto**: persona che gestisce i reparti, crea nuovi account e proprietari di account, visualizza i dettagli di utilizzo per i reparti che gestisce e visualizza i costi (se dispone delle autorizzazioni necessarie).
- **Numero di registrazione**: identificatore univoco reso disponibile da Microsoft per identificare la registrazione specifica associata a un Contratto Enterprise.
- **Amministratore dell'organizzazione**: persona che gestisce i reparti, i proprietari di reparti, gli account e i proprietari di account in Azure. Questa persona ha la possibilità di gestire gli amministratori dell'organizzazione, nonché di visualizzare i dati di utilizzo, le quantità fatturate e gli addebiti non fatturati per tutti gli account e per tutte le sottoscrizioni associati all'iscrizione Enterprise.
- **Contratto Enterprise**: un contratto di licenza Microsoft per i clienti con acquisti centralizzati che intendono standardizzare l'intera organizzazione con la tecnologia Microsoft e mantenere un'infrastruttura IT su software Microsoft standard.
- **Registrazione Enterprise Agreement**: registrazione nel programma Contratto Enterprise, che offre prodotti Microsoft con contratti multilicenza a tariffe scontate.
- **Account Microsoft**: servizio basato sul Web che consente ai siti di appartenenza di autenticare un utente con un unico set di credenziali.
- **Rettifica dell'iscrizione Microsoft Azure Enterprise (rettifica della registrazione)** : rettifica firmata da un'azienda, che consente di accedere a Microsoft Azure come parte dell'iscrizione Enterprise.
- **Azure Enterprise Portal**: portale usato dai clienti aziendali per gestire i propri account Azure e le sottoscrizioni correlate.
- **Quantità di risorse usata**: quantità di un singolo servizio Azure usata in un mese.
- **Amministratore dei servizi**: persona che accede e gestisce le sottoscrizioni e i progetti di sviluppo in Azure Enterprise Portal.
- **Sottoscrizione** Rappresenta una sottoscrizione di Azure Enterprise Portal ed è un contenitore dei servizi Azure gestiti dallo stesso amministratore dei servizi.
- **Account aziendale o dell'istituto di istruzione**: per le organizzazioni che hanno configurato Active Directory con la federazione nel cloud e hanno tutti gli account in un singolo tenant.

### <a name="enrollment-statuses"></a>Stati della registrazione

- **Nuovo**: questo stato viene assegnato a una registrazione che è stata creata nel corso di 24 ore e viene aggiornato allo stato In sospeso entro 24 ore.
- **Pending**: l'amministratore della registrazione deve accedere ad Azure Enterprise Portal. Dopo l'accesso, la registrazione passa allo stato Attivo.
- **Attivo**: la registrazione è attiva ed è possibile creare account e sottoscrizioni in Azure Enterprise Portal. La registrazione rimane attiva fino alla data di fine del Contratto Enterprise.
- **Periodo di validità esteso a tempo indeterminato**: si attiva dopo la data di fine del Contratto Enterprise. Consente ai clienti del Contratto Enterprise di Azure inclusi nel periodo esteso di continuare a usare i servizi Azure a tempo indefinito dopo la scadenza del Contratto Enterprise.

   Prima che la registrazione del Contratto Enterprise di Azure raggiunga la data di fine del Contratto Enterprise, l'amministratore della registrazione deve scegliere tra le opzioni seguenti:

  - Rinnovare la registrazione aggiungendo un ulteriore pagamento anticipato di Azure.
  - Eseguire il trasferimento a una nuova registrazione.
  - Eseguire la migrazione al programma Microsoft Online Subscription Program (MOSP).
  - Confermare la disabilitazione di tutti i servizi associati alla registrazione.
- **Scaduta**: il cliente del Contratto Enterprise di Azure è escluso dal periodo di validità esteso e la registrazione del Contratto Enterprise di Azure ha raggiunto la data di fine. La registrazione scade e tutti i servizi associati vengono disabilitati.
- **Trasferita**: vengono visualizzate con questo stato le registrazioni i cui account e servizi associati sono stati trasferiti a una nuova registrazione.
  >[!NOTE]
  > Le registrazioni non vengono trasferite automaticamente se al momento del rinnovo viene generato un nuovo numero di registrazione. Per facilitare il trasferimento automatico, includere il numero di registrazione precedente nella documentazione per il rinnovo.

## <a name="get-started-on-azure-ea---faq"></a>Domande frequenti per iniziare a usare il Contratto Enterprise di Azure

Questa sezione offre informazioni dettagliate sulle domande tipiche poste dai clienti durante il processo di onboarding.  

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>È possibile associare l'account Azure esistente all'iscrizione al Contratto Enterprise di Azure?

Sì. Tutte le sottoscrizioni di Azure per le quali si è proprietari dell'account verranno convertite nel Contratto Enterprise. Sono incluse le sottoscrizioni che usano credito mensile, ad esempio Visual Studio, AzurePass, MPN, BizSpark e così via. Quando si convertono queste sottoscrizioni, si perde il credito mensile.

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>L'account Azure esistente è stato associato per errore alla registrazione del Contratto Enterprise di Azure e di conseguenza il credito mensile è andato perduto. È possibile recuperare il credito mensile?

Se è stato eseguito l'accesso come proprietario di un account Contratto Enterprise di Azure con le stesse credenziali usate per la sottoscrizione di Visual Studio, è possibile ripristinare i singoli vantaggi di Azure per la sottoscrizione di Visual Studio con una delle azioni seguenti:

- Eliminare il proprietario dell'account da Azure Enterprise Portal dopo aver rimosso o spostato tutte le sottoscrizioni di Azure associate. Quindi registrarsi di nuovo per i singoli vantaggi Azure di Visual Studio.
- Eliminare il sottoscrittore di Visual Studio dal sito di amministrazione in VLSC e riassegnare la sottoscrizione a un account con credenziali diverse. Quindi registrarsi di nuovo per i singoli vantaggi Azure di Visual Studio.

### <a name="what-type-of-subscription-should-i-create"></a>Quale tipo di sottoscrizione è necessario creare?

Azure Enterprise Portal offre due tipi di sottoscrizioni per i clienti aziendali:

- Microsoft Azure Enterprise, ideale per:
  - Tutti gli scopi di produzione
  - Prezzi migliori in base all'uso dell'infrastruttura

  Per altre informazioni, [contattare un rappresentante di Azure](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Sviluppo/test Enterprise, ideale per:
  - Tutti i carichi di lavoro di sviluppo/test per i team
  - Singoli carichi di lavoro di sviluppo/test da medi a pesanti
  - Accesso a speciali immagini MSDN e tariffe per servizi preferenziali

  Per altre informazioni, vedere l'[offerta Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>È possibile trasferire la proprietà di una sottoscrizione a un altro account?

Sì, la proprietà di una sottoscrizione può essere trasferita a un altro account. Se ad esempio un account A dispone di tre sottoscrizioni, l'amministratore dell'organizzazione può trasferirne una all'account B, una all'account C e una all'account D oppure può trasferirle tutte all'account E.

Per trasferire le sottoscrizioni:

1. In Azure Enterprise Portal selezionare **Gestisci** > **Account**.
1. Passare il puntatore del mouse su **Account** nell'angolo a destra per visualizzare le opzioni **Transfer Ownership** (Trasferisci proprietà, icona a forma di persona) e **Transfer Subscription** (Trasferisci sottoscrizione, icona a forma di elenco). Queste opzioni sono visibili solo per gli account attivi.

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Il nome della sottoscrizione è uguale al nome dell'offerta. È necessario sostituirlo con un nome significativo per la propria organizzazione?

Quando si crea una sottoscrizione, il nome predefinito usato per la sottoscrizione è identico a quello del tipo di offerta scelto. Si consiglia di cambiare il nome della sottoscrizione usando un nome significativo per facilitarne la ricerca.

Per modificare il nome:

1. Accedere a [https://account.windowsazure.com](https://account.windowsazure.com).
1. Selezionare il nome della sottoscrizione.
1. Selezionare la sottoscrizione da modificare.
1. Selezionare l'icona **Gestisci sottoscrizione**.
1. Modificare i dettagli della sottoscrizione.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Come è possibile tenere traccia dei costi sostenuti per centro di costo?

Per tenere traccia dei costi in base al centro di costo, è necessario definire il centro di costo a uno dei livelli seguenti:

- department
- Account
- Subscription

A seconda delle esigenze, è possibile usare lo stesso centro di costo per tenere traccia dell'utilizzo e dei costi associati a un centro di costo specifico.

Ad esempio, per tenere traccia dei costi di un progetto speciale che coinvolge più reparti, può essere utile definire il centro di costo a livello di sottoscrizione per tenere traccia dell'utilizzo e dei costi.

Non è possibile definire un centro di costo a livello di servizio. Se si vuole tenere traccia dell'utilizzo a livello di servizio è possibile usare la funzionalità _Tag_, disponibile a livello di servizio.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Come è possibile tenere traccia dell'utilizzo e della spesa in base a diversi reparti in un'organizzazione?

È possibile creare tutti i reparti necessari nella registrazione del Contratto Enterprise di Azure. Per tenere traccia correttamente dell'utilizzo, assicurarsi che le sottoscrizioni non siano condivise tra i reparti.

Dopo aver creato reparti e sottoscrizioni, è possibile visualizzare i dati nel report sull'utilizzo. Queste informazioni consentono di tenere traccia dell'utilizzo e di gestire i costi e le spese a livello di reparto.

È anche possibile accedere ai dati di utilizzo tramite l'API di creazione report. Per informazioni dettagliate e codice di esempio, vedere [API REST di Azure Enterprise](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis).

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>È possibile impostare una quota di spesa e ricevere avvisi quando si avvicina il limite?

È possibile impostare una quota di spesa a livello di reparto: il sistema invierà automaticamente una notifica quando i limiti di spesa raggiungono il 50%, il 75%, il 90% e il 100% della quota definita.

Per definire la quota di spesa, selezionare un reparto e quindi selezionare l'icona di modifica. Dopo aver modificato i dettagli relativi al limite di spesa selezionare **Salva**.

### <a name="i-used-resource-groups-to-implement-azure-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Sono stati usati gruppi di risorse per implementare Controllo degli accessi in base al ruolo di Azure e rilevare l'utilizzo. Come è possibile visualizzare i dettagli di utilizzo associati?

Se si usano i _gruppi di risorse_ e i _tag_, queste informazioni vengono rilevate a livello di servizio ed è possibile accedervi nel file di utilizzo dettagliato scaricabile (con estensione csv). Vedere il [report sull'utilizzo](https://ea.azure.com/report/downloadusage) scaricabile in Azure Enterprise Portal.

È anche possibile accedere all'utilizzo tramite API. Per informazioni dettagliate e codice di esempio, vedere [API REST di Azure Enterprise](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis).

> [!NOTE]
> È possibile applicare tag solo alle risorse che supportano le operazioni di Azure Resource Manager. Se è stata creata una macchina virtuale, una rete virtuale o una risorsa di archiviazione tramite il modello di distribuzione classica, ad esempio tramite il portale classico, non è possibile applicare un tag a tale risorsa. Per supportare l'assegnazione di tag, distribuire nuovamente tali risorse tramite Resource Manager. Tutte le altre risorse supportano l'assegnazione di tag.

### <a name="can-i-perform-analyses-using-power-bi"></a>È possibile eseguire analisi tramite Power BI?

Sì. Con il pacchetto di contenuto Microsoft Azure Enterprise per Power BI è possibile:

- Importare e analizzare rapidamente il consumo di Azure per l'iscrizione Enterprise.
- Trovare il reparto, l'account o la sottoscrizione che ha registrato il consumo più elevato.
- Trovare il servizio più usato dall'organizzazione.
- Tenere traccia delle tendenze di spesa e utilizzo.

Per usare Power BI:

1. Passare al sito Web Power BI.
1. Accedere con un account aziendale o dell'istituto di istruzione valido.

   L'account aziendale o dell'istituto di istruzione può essere uguale o diverso da quello usato per accedere alla registrazione tramite Azure Enterprise Portal.
1. Nel dashboard dei servizi scegliere il riquadro Microsoft Azure Enterprise e selezionare **Connetti**.
1. Nella **schermata di connessione ad Azure Enterprise** immettere quanto segue:
    - Azure Environment URL (URL dell'ambiente di Azure): [https://ea.azure.com](https://ea.azure.com)
    - Numero di mesi: scegliere un valore compreso tra 1 e 36
    - Numero di registrazione: immettere il numero di registrazione
1. Selezionare **Avanti**.
1. Immettere la chiave API nella casella **Chiave account**.

   La chiave API è disponibile in Azure Enterprise Portal. Sotto la scheda **Scarica utilizzo** selezionare **API Access Key** (Chiave di accesso API). Copiare la chiave e quindi incollarla nella casella **Chiave account** in Power BI.

A seconda delle dimensioni del set di dati, il caricamento dei dati in Power BI può richiedere da cinque a 30 minuti.

La creazione di report di Power BI è disponibile per i clienti diretti, indiretti e i partner del Contratto Enterprise che possono visualizzare le informazioni di fatturazione.

## <a name="next-steps"></a>Passaggi successivi

- Si consiglia agli amministratori di Azure Enterprise Portal di leggere [Amministrazione di Azure Enterprise Portal](ea-portal-administration.md) per informazioni sulle attività amministrative comuni.
- Per assistenza nella risoluzione dei problemi relativi ad Azure Enterprise Portal, vedere [Risolvere i problemi di accesso ad Azure Enterprise Portal](ea-portal-troubleshoot.md).
- Per una guida all'onboarding nel Contratto Enterprise di Azure vedere [Guida all'onboarding per il Contratto Enterprise di Azure (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).
