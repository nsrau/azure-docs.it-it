---
title: Introduzione ad Azure Enterprise Portal
description: Questo articolo illustra come i clienti con un Contratto Enterprise di Azure (Azure EA, Azure Enterprise Agreement) usano Azure Enterprise Portal.
author: bandersmsft
ms.reviewer: baolcsva
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: 0f4f4d934361f1e59eceaaccca680f0cf155bb04
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348960"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Introduzione ad Azure Enterprise Portal

Questo articolo assiste i clienti diretti e indiretti del Contratto Enterprise di Azure (Azure EA) per iniziare a usare [Azure Enterprise Portal](https://ea.azure.com). Sono disponibili informazioni di base sui seguenti argomenti:

- Struttura di Azure Enterprise Portal.
- Ruoli usati in Azure Enterprise Portal.
- Creazione della sottoscrizione.
- Analisi dei costi in Azure Enterprise Portal e nel portale di Azure.

## <a name="get-started-with-ea-onboarding"></a>Introduzione all'onboarding del contratto Enterprise

Per una guida all'onboarding nel Contratto Enterprise di Azure vedere [Guida all'onboarding per il Contratto Enterprise di Azure (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).

Questo video contiene una sessione completa di onboarding di Azure Enterprise Portal:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="understanding-ea-user-roles-and-introduction-to-user-hierarchy"></a>Informazioni sui ruoli utente del contratto Enterprise e introduzione alla gerarchia degli utenti

Per gestire l'utilizzo e la spesa per la propria organizzazione, i clienti di Azure con un Contratto Enterprise possono assegnare cinque distinti ruoli amministrativi:

- Amministratore dell'organizzazione
- Amministratore dell'organizzazione (sola lettura)
- Amministratore del reparto
- Amministratore del reparto (sola lettura)
- Proprietario dell'account

Ogni ruolo presenta vari gradi di limiti e autorizzazioni per gli utenti. Per altre informazioni, vedere [Struttura dell'organizzazione e autorizzazioni in base al ruolo](./understand-ea-roles.md#organization-structure-and-permissions-by-role).

## <a name="activate-your-enrollment-create-a-subscription-and-other-administrative-tasks"></a>Attivare la registrazione, quindi creare una sottoscrizione e altre attività amministrative

Per altre informazioni sull'attivazione della registrazione, sulla creazione di un reparto o una sottoscrizione, sull'aggiunta di amministratori e proprietari di account e su altre attività amministrative, vedere [Amministrazione di Azure EA Portal](./ea-portal-administration.md).

Per altre informazioni sul trasferimento di una sottoscrizione Enterprise in una sottoscrizione con pagamento in base al consumo, vedere [Trasferimenti di Azure Enterprise](./ea-transfers.md).

## <a name="view-usage-summary-and-download-reports"></a>Visualizzare il riepilogo sull'utilizzo e scaricare i report

È possibile gestire e agire sulla fattura di Azure EA. La fattura è una rappresentazione degli addebiti, quindi è opportuno esaminarla per verificarne la correttezza.

Per visualizzare il riepilogo dell'utilizzo, scaricare i report e gestire le fatture di registrazione, vedere [Fatture di iscrizione Enterprise di Azure](./ea-portal-enrollment-invoices.md).

## <a name="now-that-youre-familiar-with-the-basics-here-are-some-additional-links-to-help-you-get-onboarded"></a>Ora che si ha familiarità con le nozioni di base, ecco alcuni collegamenti aggiuntivi utili per eseguire l'onboarding

[Prezzi di Azure EA](./ea-pricing-overview.md): informazioni dettagliate su come viene calcolato l'utilizzo e sugli addebiti previsti per vari servizi di Azure nel contratto Enterprise per cui i calcoli sono più complessi.

Per informazioni su come sfruttare le prenotazioni di Azure di istanze riservate di VM per risparmiare sui costi con l'iscrizione Enterprise, vedere [Istanze riservate di VM per Azure EA](./ea-portal-vm-reservations.md).

Per informazioni sulle API REST da usare con l'iscrizione Enterprise di Azure e su come risolvere i problemi comuni con le API REST, vedere [API REST di Azure Enterprise](./ea-portal-rest-apis.md).

[Contratti Azure EA e modifiche](./ea-portal-agreements.md): informazioni sull'impatto che possono avere i contratti Azure EA e relative modifiche sull'accesso, l'uso e i pagamenti dei servizi di Azure.

[Azure Marketplace](./ea-azure-marketplace.md): informazioni su come clienti e partner di EA possono visualizzare gli addebiti del marketplace e abilitare gli acquisti in Azure Marketplace.

Per informazioni sulle attività comuni eseguite da un amministratore EA del partner in Azure EA Portal, vedere [Amministrazione di Azure EA Portal per i partner](./ea-partner-portal-administration.md).

## <a name="get-started-on-azure-ea---faq"></a>Domande frequenti per iniziare a usare il Contratto Enterprise di Azure

Questa sezione offre informazioni dettagliate sulle domande tipiche poste dai clienti durante il processo di onboarding.  

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

È anche possibile accedere ai dati di utilizzo tramite l'API di creazione report. Per informazioni dettagliate e codice di esempio, vedere [API REST di Azure Enterprise](./ea-portal-rest-apis.md).

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>È possibile impostare una quota di spesa e ricevere avvisi quando si avvicina il limite?

È possibile impostare una quota di spesa a livello di reparto: il sistema invierà automaticamente una notifica quando i limiti di spesa raggiungono il 50%, il 75%, il 90% e il 100% della quota definita.

Per definire la quota di spesa, selezionare un reparto e quindi selezionare l'icona di modifica. Dopo aver modificato i dettagli relativi al limite di spesa selezionare **Salva**.

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Sono stati usati gruppi di risorse per implementare il controllo degli accessi in base al ruolo e rilevare l'utilizzo. Come è possibile visualizzare i dettagli di utilizzo associati?

Se si usano i _gruppi di risorse_ e i _tag_, queste informazioni vengono rilevate a livello di servizio ed è possibile accedervi nel file di utilizzo dettagliato scaricabile (con estensione csv). Vedere il [report sull'utilizzo](https://ea.azure.com/report/downloadusage) scaricabile in Azure Enterprise Portal.

È anche possibile accedere all'utilizzo tramite API. Per informazioni dettagliate e codice di esempio, vedere [API REST di Azure Enterprise](./ea-portal-rest-apis.md).

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