---
title: Amministrazione di Azure EA Portal
description: Questo articolo illustra le attività comuni eseguite da un amministratore in Azure EA Portal.
author: bandersmsft
ms.author: banders
ms.date: 11/13/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperfq1
ms.openlocfilehash: f18f1184f921aca4f2c1e33ac00dcf14c63b24eb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95893359"
---
# <a name="azure-ea-portal-administration"></a>Amministrazione di Azure EA Portal

Questo articolo illustra le attività comuni eseguite da un amministratore in Azure EA Portal (https://ea.azure.com). Azure EA Portal è un portale di gestione online che consente ai clienti di gestire il costo dei servizi EA di Azure. Per informazioni introduttive su Azure EA Portal, vedere l'articolo [Introduzione ad Azure EA Portal](ea-portal-get-started.md).

## <a name="activate-your-enrollment"></a>Attivare la registrazione

Per attivare il servizio l'amministratore dell'organizzazione iniziale apre [Azure Enterprise Portal](https://ea.azure.com) e accede usando l'indirizzo di posta elettronica incluso nel messaggio di invito.

Se si ha il ruolo di amministratore dell'organizzazione, non è necessario ricevere il messaggio di posta elettronica di attivazione. Aprire [Azure Enterprise Portal](https://ea.azure.com) e accedere con il proprio indirizzo di posta elettronica (account aziendale, dell'istituto di istruzione o account Microsoft) e la password corrispondente.

Se si hanno più registrazioni, scegliere quale attivare. Per impostazione predefinita, vengono visualizzate solo le registrazioni attive. Per visualizzare la cronologia delle registrazioni, deselezionare l'opzione **Attivo** nell'angolo in alto a destra di Azure Enterprise Portal.

In **Registrazione** lo stato visualizzato è **Attivo**.

![Esempio di una registrazione attiva](./media/ea-portal-administration/ea-enrollment-status.png)

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

![Esempio con la finestra di dialogo per l'aggiunta dell'amministratore del reparto](./media/ea-portal-administration/ea-create-add-department-admin.png)

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

![Esempio che mostra l'elenco di account e l'opzione per aggiungere un account](./media/ea-portal-administration/create-ea-add-an-account.png)

Per aggiungere un altro account selezionare **Aggiungi un altro account** oppure selezionare **Aggiungi** nell'angolo in basso a destra della barra degli strumenti sinistra.

Per confermare la proprietà dell'account:

1. Accedere ad Azure Enterprise Portal.
1. Visualizzare lo stato.

   Lo stato dovrebbe cambiare da **In sospeso** a **Data di inizio/fine**. La data di inizio/fine corrisponde alla data in cui l'utente ha eseguito per la prima volta l'accesso a alla data di termine del contratto.
1. Quando viene visualizzato il **messaggio di avviso**, il proprietario di un account deve selezionare **Continua** per attivare l'account quando accede per la prima volta ad Azure Enterprise Portal.

## <a name="change-azure-subscription-or-account-ownership"></a>Cambiare la proprietà dell'account o della sottoscrizione di Azure

Gli amministratori dell'organizzazione possono usare Azure Enterprise Portal per trasferire la proprietà dell'account di alcune o di tutte le sottoscrizioni in una registrazione.

Al termine del trasferimento della proprietà di un account o di una sottoscrizione, Microsoft aggiorna il proprietario dell'account.

Prima di eseguire il trasferimento della proprietà, è importante conoscere i criteri di controllo degli accessi in base al ruolo di Azure:

- Quando si eseguono trasferimenti di proprietà di sottoscrizioni o account tra due ID organizzativi nello stesso tenant, i criteri di controllo degli accessi in base al ruolo di Azure e i ruoli di amministratore del servizio e coamministratore esistenti vengono mantenuti.
- I trasferimenti di proprietà di sottoscrizioni o account fra tenant diversi generano la perdita dei criteri di controllo degli accessi in base al ruolo di Azure e delle assegnazioni di ruoli.
- I criteri e i ruoli di amministratore non vengono trasferiti tra directory diverse. Gli amministratori del servizio vengono aggiornati nel proprietario dell'account di destinazione.
- Per evitare la perdita dei criteri di Controllo degli accessi in base al ruolo di Azure e delle assegnazioni di ruoli durante il trasferimento della sottoscrizione tra tenant, assicurarsi che la casella di controllo **Move the subscriptions to the recipient's Azure AD tenant** (Sposta le sottoscrizioni nel tenant di Azure AD del destinatario) rimanga **deselezionata**. In questo modo verranno mantenuti i servizi, i ruoli di Azure e i criteri nel tenant di Azure AD corrente e verrà trasferita solo la proprietà di fatturazione dell'account.  
    :::image type="content" source="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Immagine che mostra la casella di controllo per il trasferimento di sottoscrizioni al tenant Azure AD non selezionata" lightbox="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" :::


Prima di cambiare il proprietario di un account:

1. In Azure Enterprise Portal visualizzare la scheda **Account** e identificare l'account di origine. L'account di origine deve essere attivo.
1. Identificare l'account di destinazione e verificare che sia attivo.

Per trasferire la proprietà dell'account per tutte le sottoscrizioni:

1. Accedere ad Azure Enterprise Portal.
1. Nell'area di spostamento sinistra selezionare **Gestisci**.
1. Selezionare la scheda **Account** e passare il puntatore del mouse su un account.
1. Selezionare l'icona di modifica del proprietario dell'account sulla destra. L'icona raffigura una persona.  
    ![Immagine che mostra il simbolo di modifica del proprietario dell'account](./media/ea-portal-administration/create-ea-create-sub-transfer-account-ownership-of-sub.png)
1. Scegliere l'account di destinazione in cui eseguire il trasferimento e quindi selezionare **Avanti**.
1. Se si vuole trasferire la proprietà dell'account fra tenant di Azure AD diversi, selezionare la casella di controllo **Move the subscriptions to the recipient's Azure AD tenant** (Sposta le sottoscrizioni nel tenant di Azure AD del destinatario).  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Immagine che mostra la casella di controllo per il trasferimento di sottoscrizioni al tenant Azure AD selezionata" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Confermare il trasferimento e selezionare **Invia**.

Per trasferire la proprietà dell'account per una sottoscrizione singola:

1. Accedere ad Azure Enterprise Portal.
1. Nell'area di spostamento sinistra selezionare **Gestisci**.
1. Selezionare la scheda **Account** e passare il puntatore del mouse su un account.
1. Selezionare l'icona di trasferimento sottoscrizioni a destra. L'icona raffigura una pagina.  
    ![Immagine che mostra il simbolo del trasferimento di sottoscrizioni](./media/ea-portal-administration/ea-transfer-subscriptions.png)
1. Scegliere l'account di destinazione in cui trasferire la sottoscrizione e quindi selezionare **Avanti**.
1. Se si vuole trasferire la proprietà della sottoscrizione fra tenant di Azure AD diversi, selezionare la casella di controllo **Move the subscriptions to the recipient's Azure AD tenant** (Sposta le sottoscrizioni nel tenant di Azure AD del destinatario).  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Immagine che mostra la casella di controllo per il trasferimento di sottoscrizioni al tenant Azure AD selezionata" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Confermare il trasferimento e selezionare **Invia**.


## <a name="associate-an-account-to-a-department"></a>Associare un account a un reparto

Gli amministratori dell'organizzazione possono associare account esistenti ai reparti nell'ambito della registrazione.

### <a name="to-associate-an-account-to-a-department"></a>Per associare un account a un reparto

1. Accedere ad Azure EA Portal come amministratore dell'organizzazione.
1. Selezionare **Gestisci** nel riquadro di spostamento di sinistra.
1. Selezionare **Reparto**.
1. Passare il puntatore del mouse sulla riga relativa all'account e selezionare l'icona della matita a destra.
1. Selezionare il reparto nel menu a discesa.
1. Selezionare **Salva**.

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

## <a name="department-spending-quotas"></a>Quote di spesa del reparto

I clienti EA possono impostare o cambiare le quote di spesa per ogni reparto nell'ambito di una registrazione. L'importo delle quote di spesa è impostato per il periodo corrente di pagamento anticipato. Alla fine del periodo di corrente di pagamento anticipato, il sistema estenderà la quota di spesa esistente al periodo successivo, a meno che i valori non vengano aggiornati.

L'amministratore del reparto può visualizzare la quota di spesa, ma l'importo della quota può essere aggiornato solo dall'amministratore dell'organizzazione. L'amministratore dell'organizzazione e l'amministratore del reparto riceveranno notifiche quando la quota raggiunge il 50%, il 75%, il 90% e il 100%.

### <a name="enterprise-administrator-to-set-the-quota"></a>Per impostare la quota (amministratore dell'organizzazione):

 1. Aprire Azure EA Portal.
 1. Selezionare **Gestisci** nel riquadro di spostamento di sinistra.
 1. Selezionare la scheda **Reparto**.
 1. Selezionare il reparto.
 1. Selezionare il simbolo della matita nella sezione relativa ai dettagli del reparto oppure selezionare il simbolo **+ Aggiungi reparto** per aggiungere una quota di spesa insieme a un nuovo reparto.
 1. Nella sezione relativa ai dettagli del reparto immettere un importo per la quota di spesa nella valuta della registrazione nella casella corrispondente (il valore deve essere maggiore di 0).
    - In questa fase è anche possibile modificare il nome del reparto e il centro di costo.
 1. Selezionare **Salva**.

La quota di spesa del reparto sarà ora visibile nella visualizzazione dell'elenco dei reparti nella scheda Reparto. Alla fine del pagamento anticipato corrente, Azure EA Portal manterrà le quote di spesa per il periodo successivo.

L'importo della quota del reparto è indipendente dal pagamento anticipato corrente e gli avvisi e l'importo della quota sono validi solo per l'utilizzo da parte del produttore. La quota di spesa del reparto è solo a scopo informativo e non impone limiti di spesa.

### <a name="department-administrator-to-view-the-quota"></a>Per visualizzare la quota (amministratore del reparto):

1. Aprire Azure EA Portal.
1. Selezionare **Gestisci** nel riquadro di spostamento di sinistra.
1. Selezionare la scheda **Reparto** ed esaminare la visualizzazione elenco dei reparti con le quote di spesa.

Per i clienti indiretti le funzionalità relative ai costi devono essere abilitate dal partner di canale.

## <a name="enterprise-user-roles"></a>Ruoli utente dell'organizzazione

Azure EA Portal consente di amministrare i costi e l'utilizzo del contratto Enterprise Agreement di Azure. In Azure EA Portal sono disponibili tre ruoli principali:

- Amministratore EA
- Amministratore del reparto
- Proprietario dell'account

Ogni ruolo ha un livello di accesso e autorità diverso.

Per altre informazioni sui ruoli utente, vedere [Ruoli utente dell'organizzazione](/azure/cost-management-billing/manage/understand-ea-roles#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Aggiungere un account EA di Azure

L'account Azure EA è un'unità organizzativa in EA Portal di Azure. Viene usato per l'amministrazione delle sottoscrizioni e anche per la creazione di report. Per accedere e usare i servizi di Azure, è necessario creare un account o farsene creare uno.

Per altre informazioni sugli account Azure, vedere [Aggiungere un account](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-administration#add-an-account).

## <a name="enterprise-devtest-offer"></a>Offerta Sviluppo/test Enterprise

Un amministratore dell'organizzazione di Azure può abilitare i proprietari di account dell'organizzazione per la creazione di sottoscrizioni basate sull'offerta Sviluppo/test EA. A tale scopo, selezionare la casella Sviluppo/test per il proprietario dell'account in Azure EA Portal.

Dopo aver selezionato la casella Sviluppo/test, informare il proprietario dell'account in modo che possa configurare le sottoscrizioni di Sviluppo/test EA necessarie per i team di sottoscrittori di Sviluppo/test.

Questa offerta consente ai sottoscrittori attivi di Visual Studio di eseguire carichi di lavoro di sviluppo e test in Azure a tariffe speciali. Fornisce l'accesso alla raccolta completa di immagini di Sviluppo/test, tra cui Windows 8.1 e Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Per configurare l'offerta Sviluppo/test Enterprise:

1. Accedere come amministratore dell'organizzazione.
1. Selezionare **Gestisci** nel riquadro di spostamento di sinistra.
1. Fare clic sulla scheda **Account**.
1. Selezionare la riga relativa all'account in cui si intende abilitare l'accesso a Sviluppo/test.
1. Selezionare il simbolo della matita a destra della riga.
1. Selezionare la casella di controllo di sviluppo/test.
1. Selezionare **Salva**.

Quando un utente viene aggiunto come proprietario dell'account tramite Azure EA Portal, le sottoscrizioni di Azure associate al proprietario dell'account che si basano sull'offerta Sviluppo/test con pagamento in base al consumo o sulle offerte di credito mensile per i sottoscrittori di Visual Studio verranno convertite nell'offerta Sviluppo/test EA. Le sottoscrizioni associate al proprietario dell'account basate su altri tipi di offerta, ad esempio con pagamento in base al consumo, verranno convertite in offerte di Microsoft Azure Enterprise.

Al momento l'offerta Sviluppo/test non è applicabile ai clienti di Azure per enti pubblici.

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

## <a name="delete-subscription"></a>Eliminare una sottoscrizione

Per eliminare una sottoscrizione in cui si è proprietario dell'account:

1. Accedere al portale di Azure con le credenziali associate all'account.
1. Nel menu Hub selezionare **Sottoscrizioni**.
1. Nella scheda delle sottoscrizioni nell'angolo in alto a sinistra della pagina selezionare la sottoscrizione da annullare e quindi **Cancel Sub** (Annulla sottoscrizione) per aprire la scheda per l'annullamento.
1. Immettere il nome della sottoscrizione, scegliere un motivo per l'annullamento e selezionare il pulsante **Cancel Sub** (Annulla sottoscrizione).

Solo gli amministratori dell'account possono annullare le sottoscrizioni.

Per altre informazioni, vedere [Cosa accade quando si annulla la sottoscrizione](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription).

## <a name="delete-an-account"></a>Eliminare un account

La rimozione di un account può essere completata solo per gli account attivi senza sottoscrizioni attive.

1. In Enterprise Portal selezionare **Gestisci** nel riquadro di spostamento sinistro.
1. Fare clic sulla scheda **Account**.
1. Dalla tabella Account selezionare l'account che si intende eliminare.
1. Selezionare il simbolo X a destra della riga relativa all'account.
1. Nel momento in cui non sono presenti sottoscrizioni attive nell'account, selezionare **Sì** nella riga relativa all'account per confermarne la rimozione.

## <a name="update-notification-settings"></a>Aggiornare le impostazioni di notifica

Gli amministratori dell'organizzazione vengono registrati automaticamente per ricevere le notifiche di utilizzo associate alla relativa registrazione. Ogni amministratore dell'organizzazione può modificare l'intervallo delle singole notifiche oppure disattivarle completamente.

I contatti per le notifiche vengono visualizzati in Azure EA Portal nella sezione **Contatto per le notifiche**. La gestione dei contatti per le notifiche garantisce che le persone giuste dell'organizzazione ricevano le notifiche sulla registrazione EA di Azure.

Per visualizzare le impostazioni di notifica correnti:

1. In Azure EA Portal passare a **Gestisci** > **Contatto per le notifiche**.
2. Indirizzo di posta elettronica: indirizzo associato all'account Microsoft, aziendale o dell'Istituto di istruzione dell'amministratore dell'organizzazione che riceve le notifiche.
3. Unbilled Balance Notification Frequency (Frequenza delle notifiche di saldo non fatturato): frequenza impostata per l'invio delle notifiche a ogni singolo amministratore dell'organizzazione.

Per aggiungere un contatto:

1. Selezionare **+Aggiungi contatto**.
2. Immettere l'indirizzo di posta elettronica e confermarlo.
3. Selezionare **Salva**.

Il nuovo contatto per le notifiche viene visualizzato nella sezione **Contatto per le notifiche**. Per cambiare la frequenza di notifica, selezionare il contatto per la notifica e quindi il simbolo della matita a destra della riga selezionata. Impostare la frequenza su **giornaliera**, **settimanale**, **mensile** o **nessuna**.

È possibile eliminare le notifiche sul ciclo di vita che segnalano che si sta avvicinando la _data di fine del periodo di copertura_ e la _data di disabilitazione e deprovisioning_. Se si disabilitano le notifiche sul ciclo di vita, vengono eliminate le notifiche sulla data di fine del periodo di copertura e del contratto.

## <a name="azure-sponsorship-offer"></a>Offerta Azure Sponsorship

L'offerta Azure Sponsorship è un account Microsoft Azure sponsorizzato con limitazioni. È disponibile per invito tramite posta elettronica solo per un numero limitato di clienti selezionati da Microsoft. Gli utenti idonei per l'offerta Microsoft Azure Sponsorship riceveranno tramite posta elettronica un invito per il proprio ID account.

Per altre informazioni, creare una [richiesta di supporto per l'attivazione sponsorship](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Conversione all'autenticazione account aziendale o dell'istituto di istruzione

Gli utenti di Azure Enterprise possono convertire un account Microsoft (MSA o Live ID) in un'autenticazione di tipo account aziendale o dell'istituto di istruzione, che usa Azure Active Directory.

Per iniziare:

1. Aggiungere l'account aziendale o dell'istituto di istruzione ad Azure EA Portal nei ruoli necessari.
1. Se si verificano errori, è possibile che l'account non sia valido in Active Directory.  Azure usa il nome dell'entità utente (UPN), che non è sempre identico all'indirizzo di posta elettronica.
1. Eseguire l'autenticazione in Azure EA Portal usando l'account aziendale o dell'istituto di istruzione.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Per convertire le sottoscrizioni da account Microsoft in account aziendali o dell'istituto di istruzione:

1. Accedere al portale di gestione usando l'account Microsoft proprietario delle sottoscrizioni.
1. Usare il trasferimento della proprietà dell'account per passare al nuovo account.
1. A questo punto l'account Microsoft dovrebbe essere privo di sottoscrizioni attive e può essere eliminato.
1. Gli account eliminati rimarranno visibili nel portale con uno stato inattivo per motivi di fatturazione cronologica.  È possibile escluderli dalla visualizzazione selezionando una casella di controllo per la visualizzazione solo degli account attivi.

## <a name="account-subscription-ownership-faq"></a>Domande frequenti sulla proprietà delle sottoscrizioni degli account

Questo documento risponde alle domande frequenti relative alla proprietà delle sottoscrizioni degli account.

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>È possibile associare l'account Azure esistente all'iscrizione al Contratto Enterprise di Azure?

Sì. Tutte le sottoscrizioni di Azure per le quali si è proprietari dell'account verranno convertite nel Contratto Enterprise. Sono incluse le sottoscrizioni che usano credito mensile, ad esempio Visual Studio, AzurePass, MPN, BizSpark e così via. Quando si convertono queste sottoscrizioni, si perde il credito mensile.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Quanti proprietari di account Azure possono esistere per ogni sottoscrizione?

Per ogni sottoscrizione è consentito un solo proprietario di account.  È possibile aggiungere altri ruoli tramite l'accesso in base al ruolo o Controllo di accesso (IAM) nella scheda relativa alle sottoscrizioni nell'angolo superiore sinistro della pagina del [portale di Azure](https://portal.azure.com).

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>È possibile trasferire la proprietà di una sottoscrizione a un altro account?

Sì, la proprietà di una sottoscrizione può essere trasferita a un altro account. Se ad esempio un account A dispone di tre sottoscrizioni, l'amministratore dell'organizzazione può trasferirne una all'account B, una all'account C e una all'account D oppure può trasferirle tutte all'account E.

Per trasferire le sottoscrizioni:

1. In Azure Enterprise Portal selezionare **Gestisci** > **Account**.
1. Passare il puntatore del mouse su **Account** nell'angolo a destra per visualizzare le opzioni **Transfer Ownership** (Trasferisci proprietà, icona a forma di persona) e **Transfer Subscription** (Trasferisci sottoscrizione, icona a forma di elenco). Queste opzioni sono visibili solo per gli account attivi.

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Un proprietario di account di Azure può essere elencato in più reparti?

No, un proprietario di account può essere associato solo a un singolo reparto. Questo criterio garantisce un monitoraggio e una ripartizione accurati di costi e spese associati al reparto con cui è allineato nella registrazione EA in Azure EA Portal.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Un proprietario di account di Azure può essere elencato come gruppo di sicurezza?

No, un proprietario della sottoscrizione deve avere un account Microsoft (MSA) univoco o un'autenticazione di Azure Active Directory (Azure AD). Per tenere conto della successione all'interno dell'organizzazione, valutare l'opportunità di creare account generici e di usare Azure AD per gestire l'accesso alla sottoscrizione.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Un singolo utente può possedere più sottoscrizioni?

Un proprietario di account di Azure può creare e gestire un numero illimitato di sottoscrizioni.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Come è possibile accedere o visualizzare tutte le sottoscrizioni di un'organizzazione?

Attualmente questa operazione deve essere eseguita in base ai criteri, ovvero per ogni sottoscrizione creata è necessario richiedere che l'account venga aggiunto a un ruolo di sottoscrizione con l'accesso basato sui ruoli.

### <a name="where-do-i-go-to-create-a-subscription"></a>Da dove è possibile creare una sottoscrizione?

Per poter creare una sottoscrizione per l'offerta Azure Enterprise (EA), è necessario che l'account venga prima aggiunto al ruolo di proprietario dell'account dall'amministratore della registrazione EA in Azure EA Portal. Sarà quindi necessario accedere ad Azure EA Portal per ottenere l'entitlement per creare sottoscrizioni per il tipo di offerta EA. È consigliabile creare la prima sottoscrizione EA dal collegamento '+ Aggiungi sottoscrizione' nella scheda relativa alle sottoscrizioni in EA Portal.  Tuttavia, una volta che l'account è stato autorizzato, potrebbe essere più facile creare sottoscrizioni in portal.azure.com nella scheda relativa alla sottoscrizione nell'angolo superiore sinistro della pagina, in cui è possibile creare e rinominare una sottoscrizione in un unico passaggio.

### <a name="who-can-create-a-subscription"></a>Chi può creare una sottoscrizione?

Per creare una sottoscrizione per il tipo di offerta Azure Enterprise, è necessario essere autorizzati nel ruolo di proprietario dell'account in [EA Portal](https://ea.azure.com).

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
- **Account aziendale o dell'istituto di istruzione**: per le organizzazioni che hanno configurato Azure Active Directory con la federazione nel cloud e hanno tutti gli account in un singolo tenant.

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

## <a name="next-steps"></a>Passaggi successivi

- Leggere l'articolo sulle [istanze di macchina virtuale riservate di Azure](ea-portal-vm-reservations.md) per informazioni su come risparmiare denaro.
- Se serve assistenza per la risoluzione dei problemi relativi ad Azure EA Portal, vedere [Risolvere i problemi di accesso ad Azure EA Portal](ea-portal-troubleshoot.md).
