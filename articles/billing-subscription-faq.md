---
title: Domande frequenti su fatturazione e sottoscrizione di Azure | Documentazione Microsoft
description: "Fornisce le risposte alle domande più comuni su sottoscrizione e fatturazione di Azure"
services: 
documentationcenter: 
author: genlin
manager: mbaldwin
editor: 
tags: billing
ms.assetid: 614f90ee-b625-46cf-a91e-3bcd83190e62
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 7583a1c81802bdedabdb70c125aac536b879204d
ms.openlocfilehash: 38531c1b3cb7c7de9b832b4fbedd8991f40e3424


---
# <a name="azure-billing-and-subscription-faq"></a>Domande frequenti su fatturazione e sottoscrizione di Azure
Questo articolo risponde ad alcune delle domande più frequenti relative alla fatturazione e alla sottoscrizione di Azure.

> [!NOTE]
> Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
>
>

## <a name="account-administration"></a>Amministrazione account
* [Come si gestiscono gli account amministratore nel nuovo portale di Azure?](#how-do-i-manage-administrator-accounts-in-the-new-azure-portal)
* [È possibile inviare le notifiche a un indirizzo di posta elettronica da quello del proprietario dell'account associato all'account?](#can-notifications-be-sent-to-a-different-email-address-other-than-the-account-owner-email-address-associated-with-my-account)
* [Perché non è possibile modificare o aggiungere dettagli alla sottoscrizione?](#why-cant-i-edit-or-add-details-to-my-subscription)
* [Non è possibile accedere al portale di Azure](#i-am-unable-to-log-in-to-the-azure-portal)

## <a name="billing"></a>Fatturazione
* [Come si ottiene la copia di una fattura?](#how-do-i-get-a-copy-of-my-invoice)
* [Non viene visualizzata alcuna fattura per l'ultimo periodo di fatturazione. Perché?](#i-do-not-see-an-invoice-for-the-last-billing-period-why)
* [Come si effettua un pagamento?](#how-do-i-make-a-payment)

## <a name="free-trial"></a>Versione di valutazione gratuita
* [Si può provare gratuitamente Azure senza il rischio di incorrere in addebiti?](#can-i-try-azure-for-free-without-any-risk-of-being-charged)
* [Che cos'è una sottoscrizione della versione di valutazione e per quanto tempo è valida?](#what-is-a-free-trial-subscription-and-how-long-does-it-last)
* [Quali risorse sono disponibili quando si usa una versione di valutazione?](#what-resources-are-available-when-using-a-free-trial)
* [Cosa accade se si rimuove il limite di spesa dalla versione di valutazione gratuita?](#what-happens-if-i-remove-the-spending-limit-on-my-free-trial)
* [È possibile ottenere un aumento della quota in una sottoscrizione di valutazione gratuita?](#can-i-get-a-quota-increase-on-a-free-trial-subscription)
* [Dove è possibile ottenere altre informazioni sulla versione di valutazione gratuita?](#where-can-i-get-more-details-on-free-trial)

## <a name="how-to-buy-azure"></a>Come acquistare Azure
* [In quali paesi e aree geografiche Microsoft Azure è attualmente disponibile in commercio e quali valute possono essere usate per acquistarlo?](#in-which-countries-and-regions-is-microsoft-azure-commercially-available-and-what-currencies-can-be-used-to-purchase-azure)
* [La rivendita dei servizi basati su Azure è limitata nei paesi soggetti a embargo?](#do-we-restrict-resale-of-azure-based-service-into-countries-under-embargo)
* [Azure e il database SQL sono disponibili tramite contratto di licenza del provider di Servizi Microsoft?](#are-azure-and-sql-database-available-through-microsoft-services-provider-license-agreement)
* [È possibile usare licenze di Windows esistenti acquistate all'esterno di Azure?](#can-i-use-my-existing-windows-licenses-that-i-bought-outside-of-azure)
* [Chi può acquistare i servizi di Azure?](#who-can-purchase-azure-services)

## <a name="making-payments"></a>Eseguire pagamenti
* [Quali sono le opzioni di pagamento disponibili per l'acquisto di Azure?](#what-payment-options-do-i-have-in-purchasing-azure)
* [Come si può richiedere il metodo di pagamento con fattura?](#how-can-i-request-the-invoice-method-of-payment)
* [Come si controlla lo stato di un pagamento effettuato con carta di credito?](#how-do-i-check-the-status-of-a-payment-made-by-credit-card)
* [Come si rimuove una carta di credito che non si usa più come metodo di pagamento di Azure?](#how-do-i-remove-a-credit-card-that-i-no-longer-use-as-an-azure-payment-method)
* [Come si aggiornano o si modificano le informazioni relative alla carta di credito?](#how-can-i-update-or-change-my-credit-card-information)
* [Come si modificano le informazioni sul pagamento per le sottoscrizioni di Azure?](#how-can-i-edit-my-payment-information-for-my-azure-subscriptions)
* [Perché viene visualizzato un messaggio indicante che è necessario usare una carta di credito internazionale?](#why-am-i-seeing-a-message-that-i-need-to-use-an-international-credit-card)

## <a name="azure-pricing"></a>Prezzi di Azure
* [Dove si possono trovare informazioni su vantaggi e prezzi per i servizi di Azure?](#where-can-i-find-the-benefits-and-pricing-information-for-azure-services)
* [Come si modifica il piano tariffario?](#how-do-i-change-my-pricing-plan)

## <a name="service-level-agreements-sla"></a>Contratti di servizio
* [Come si possono conoscere in anticipo i tempi di inattività del servizio per manutenzione pianificata?](#how-do-we-know-in-advance-about-service-downtime-for-planned-maintenance)
* [Che cos'è il contratto di servizio di Azure per tempi di attività e connettività?](#what-is-the-azure-sla-agreement-for-uptime-and-connectivity)
* [Quali sono i crediti del contratto di servizio di Azure?](#what-are-the-azure-sla-credits)
* [Come si integrano i contratti di servizio di Azure con gli attuali contratti di licenza Microsoft per soluzioni locali?](#how-will-azure-service-level-agreements-work-with-current-on-premises-microsoft-licensing-agreements)

## <a name="spending-limits"></a>Limiti di spesa
* [Si può riattivare il limite di spesa dopo che è stato disattivato?](#can-i-turn-the-spending-limit-back-on-if-i-turn-off-it)
* [Si può modificare l'importo del limite di spesa?](#can-i-adjust-the-amount-of-the-spending-limit)

## <a name="support-plans"></a>Piani di supporto
* [Come si acquista il supporto tecnico di Azure?](#how-do-i-purchase-azure-support)
* [La tariffa mensile copre un singolo piano di Azure oppure l'intero account?](#does-the-monthly-rate-cover-a-single-azure-plan-or-the-entire-account)
* [Come è possibile eseguire l'aggiornamento a un piano di supporto di Azure di livello superiore?](#how-do-i-upgrade-to-a-higher-tier-azure-support-plan)
* [Quando viene emessa la fattura per il supporto di Azure?](#when-will-i-be-billed-for-azure-support)
* [Che cosa succede al termine del periodo di validità?](#what-happens-at-the-end-of-the-term)
* [Come è possibile annullare il piano di supporto di Azure?](#how-do-i-cancel-my-azure-support-plan)

## <a name="migrating-subscriptions-services-and-data"></a>Migrazione di sottoscrizioni, servizi e dati
* [Come si esegue la migrazione di dati e servizi per la sottoscrizione di Azure a una nuova sottoscrizione?](#how-do-i-migrate-data-and-services-for-my-azure-subscription-to-a-new-subscription)
* [Come si trasferisce la proprietà delle sottoscrizioni?](#how-do-i-transfer-ownership-of-my-subscriptions)

## <a name="subscription-service-limits-quotas"></a>Limiti del servizio di sottoscrizione (quote)
* [È possibile ottenere un aumento della quota in una sottoscrizione di valutazione gratuita?](#can-i-get-a-quota-increase-on-a-free-trial-subscription)

### <a name="what-payment-options-do-i-have-in-purchasing-azure"></a>Quali sono le opzioni di pagamento disponibili per l'acquisto di Azure?
È possibile acquistare Azure usando una carta di credito o di debito oppure scegliendo l'opzione di fatturazione.

> [!NOTE]
> * Una volta scelta questa opzione, non è passare all'opzione con carta di credito. Per iscriversi per la fatturazione, vedere [Fatturazione di Azure](https://azure.microsoft.com/pricing/invoicing/).
> * Non sono accettate carte prepagate e carte di credito virtuali.
> * L'utente è l'unico responsabile per eventuali interessi sulla carta di credito o altri addebiti correlati eventualmente risultanti.
>
>

### <a name="how-can-i-request-the-invoice-method-of-payment"></a>Come si può richiedere il metodo di pagamento con fattura?
Per inviare una richiesta per il pagamento con fattura, seguire la procedura descritta in [Fatturazione di Azure](https://azure.microsoft.com//pricing/invoicing/) . Dopo che la richiesta sarà stata approvata, si riceveranno istruzioni su come configurare la sottoscrizione per il metodo di pagamento con fattura.

### <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Come si controlla lo stato di un pagamento effettuato con carta di credito?
È necessario inviare un ticket di supporto per richiedere assistenza. Per creare un ticket di supporto per controllare lo stato di un pagamento eseguito con carta di credito, vedere [Come creare un ticket di supporto per problemi relativi alla sottoscrizione e alla fatturazione di Azure](billing-how-to-create-billing-support-ticket.md).

### <a name="how-do-i-get-a-copy-of-my-invoice"></a>Come si ottiene la copia di una fattura?
In quanto amministratore account Azure, è possibile visualizzare la fattura corrente nel centro Account Azure e scaricare i resoconti degli ultimi sei periodi di fatturazione. Per altre informazioni vedere [Come scaricare la fattura e i dati di utilizzo giornalieri di Azure](billing-download-azure-invoice-daily-usage-date.md).

### <a name="how-do-i-remove-a-credit-card-that-i-no-longer-use-as-an-azure-payment-method"></a>Come si rimuove una carta di credito che non si usa più come metodo di pagamento di Azure?
È necessario inviare un ticket di supporto per richiedere assistenza. Per creare un ticket di supporto per rimuovere una carta di credito, vedere [Come creare un ticket di supporto per problemi relativi alla sottoscrizione e alla fatturazione di Azure](billing-how-to-create-billing-support-ticket.md).

### <a name="how-can-i-update-or-change-my-credit-card-information"></a>Come si aggiornano o si modificano le informazioni relative alla carta di credito?
Se la carta di credito viene rinnovata e il numero rimane invariato, è possibile aggiornare semplicemente i dati della carta di credito esistente, ad esempio la data di scadenza. Se cambia il numero di carta di credito o si vuole usare un'altra carta, è necessario aggiungere la nuova carta. Per istruzioni su come aggiornare o aggiungere una carta di credito, vedere [Come modificare la carta di credito usata per pagare una sottoscrizione di Azure](billing-how-to-change-credit-card.md).

### <a name="how-do-we-know-in-advance-about-service-downtime-for-planned-maintenance"></a>Come si possono conoscere in anticipo i tempi di inattività del servizio per manutenzione pianificata?
Il contratto di servizio Microsoft relativo alla disponibilità si applica alle applicazioni dei clienti che usano almeno due istanze del ruolo.  Non è disponibile un contratto di servizio per singole istanze. Per informazioni complete, vedere la pagina [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/) .

#### <a name="planned-maintenance-classic-portal"></a>Manutenzione pianificata (portale classico)
Per le configurazioni a istanze singole e istanze multiple, Azure invia in anticipo una comunicazione tramite posta elettronica per avvisare della prossima manutenzione pianificata, con un anticipo di una settimana per istanze singole e di 48 ore per istanze multiple. Questo messaggio di posta elettronica viene inviato agli account di posta elettronica dell'amministratore account, dell'amministratore del servizio e del coamministratore forniti nella sottoscrizione.

> [!NOTE]
> Per altre informazioni specifiche delle macchine virtuali, vedere [Manutenzione pianificata per macchine virtuali Linux in Azure](virtual-machines/virtual-machines-linux-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
>
>

#### <a name="unplanned-maintenance"></a>Manutenzione non pianificata
In caso di interruzione imprevista di un servizio nella piattaforma Azure, gli aggiornamenti dello stato saranno visualizzati nel [dashboard del servizio](http://status.azure.com/) e i clienti riceveranno anche una notifica nel [portale di Azure classico](https://manage.windowsazure.com/).

### <a name="what-is-the-azure-sla-agreement-for-uptime-and-connectivity"></a>Che cos'è il contratto di servizio di Azure per tempi di attività e connettività?
Azure prevede contratti di servizio separati per tutti i servizi disponibili a livello generale e non in versione di anteprima. Il contratto di servizio definisce gli impegni di Microsoft in merito a tempi di attività e connettività. Per informazioni aggiornate, vedere la pagina [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/) .

### <a name="what-are-the-azure-sla-credits"></a>Quali sono i crediti del contratto di servizio di Azure?
I crediti del contratto di servizio di Azure vengono calcolati per ogni sottoscrizione come percentuale della fattura per il servizio in questione, nel mese di fatturazione in cui il contratto di servizio non è stato rispettato. I crediti del servizio vengono applicati alla fattura del mese successivo. In genere viene fornito un credito del 10% se si scende al di sotto della prima soglia (99,95% o 99,9%, a seconda del servizio) e del 25% se si scende al di sotto della soglia successiva (99%). Per informazioni complete, vedere la pagina [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/) .

### <a name="how-will-azure-service-level-agreements-work-with-current-on-premises-microsoft-licensing-agreements"></a>Come si integrano i contratti di servizio di Azure con gli attuali contratti di licenza Microsoft per soluzioni locali?
I contratti di servizio di Azure sono indipendenti dai contratti di licenza Microsoft per soluzioni locali. I contratti di servizio per Azure forniscono una garanzia del tempo di attività mensile per i servizi usati nel cloud, con crediti del contratto di servizio rispetto all'importo fatturato nel caso Microsoft non dovesse rispettare la garanzia.

### <a name="i-do-not-see-an-invoice-for-the-last-billing-period-why"></a>Non viene visualizzata alcuna fattura per l'ultimo periodo di fatturazione. Perché?
Le ragioni possono essere diverse:

* Sono passati meno di trenta giorni dalla data della sottoscrizione di Azure.
* Il tipo di sottoscrizione è una versione di valutazione gratuita oppure una delle offerte per i membri (credito mensile per un sottoscrittore di Visual Studio/Open/BizSpark/Imagine) a cui non sono associati una carta di credito né altri strumenti di pagamento.
* La data di emissione della fattura non è ancora arrivata.

### <a name="i-am-unable-to-log-in-to-the-azure-portal"></a>Non è possibile accedere al portale di Azure
Vedere [Non è possibile accedere per gestire la sottoscrizione di Azure](billing-cannot-login-subscription.md).

> [!NOTE]
> Assicurarsi di accedere con l'account amministratore corretto. Un amministratore account può accedere solo al [Centro account](https://account.windowsazure.com/), mentre gli amministratori del servizio e i coamministratori hanno accesso solo al [portale di Azure](https://portal.azure.com/) o al [portale di Azure classico](https://manage.windowsazure.com/).
>
>

### <a name="how-do-i-purchase-azure-support"></a>Come si acquista il supporto tecnico di Azure?
I piani di supporto di Azure possono essere acquistati online o tramite un contratto Enterprise. I piani di supporto Developer, Standard o Professional Direct sono disponibili online nella pagina [Supporto di Azure per i clienti](https://azure.microsoft.com/support/plans/) . Per acquistare un piano di supporto, è necessario essere proprietari dell'account Microsoft Azure.

Se si acquista Microsoft Azure tramite un contratto Enterprise, è possibile aggiungere un piano di supporto Standard o Professional Direct al contratto Enterprise contattando il LAR (Large Account Reseller). Il supporto tecnico Developer non è disponibile tramite un contratto Enterprise.

### <a name="does-the-monthly-rate-cover-a-single-azure-plan-or-the-entire-account"></a>La tariffa mensile copre un singolo piano di Azure oppure l'intero account?
Il costo fisso mensile per il supporto di Azure copre un account, indipendentemente dal numero di sottoscrizioni o utenti inclusi nell'account stesso.

Tutte le sottoscrizioni incluse in un account condivideranno lo stesso piano di supporto e tutti gli utenti con accesso di amministratore o proprietario per le sottoscrizioni incluse in un account coperto da un piano di supporto avranno diritto al supporto per le sottoscrizioni specifiche dell'account a cui hanno accesso.

È possibile accedere a un account Microsoft Azure dal [Centro account di Azure](https://account.windowsazure.com/Home/Index). Da questa posizione è possibile visualizzare l'elenco delle sottoscrizioni che appartengono all'account e per le quali si ha diritto al supporto.

### <a name="how-do-i-upgrade-to-a-higher-tier-azure-support-plan"></a>Come è possibile eseguire l'aggiornamento a un piano di supporto di Azure di livello superiore?
È possibile eseguire l'aggiornamento a un piano di livello superiore [inviando una richiesta di supporto](billing-how-to-create-billing-support-ticket.md) e scegliendo **Gestione della sottoscrizione**. Microsoft rinuncerà ai pagamenti rimanenti per il piano di supporto di Azure originale e per i mesi rimanenti dell'impegno iniziale addebiterà la tariffa per il livello superiore.

### <a name="when-will-i-be-billed-for-azure-support"></a>Quando viene emessa la fattura per il supporto di Azure?
Quando si acquista un piano di supporto di Azure online, viene immediatamente addebitata la tariffa per il primo mese. L'importo mensile è addebitato il primo giorno di ogni successivo ciclo di fatturazione. Gli acquisti con un contratto Enterprise seguiranno il ciclo di fatturazione del contratto.

### <a name="what-happens-at-the-end-of-the-term"></a>Che cosa succede al termine del periodo di validità?
Alla fine del periodo di validità di 6 mesi, i piani saranno rinnovati automaticamente per lo stesso piano di supporto di Azure per altri 6 mesi, con lo stesso metodo di pagamento. Il proprietario di un account può disabilitare la funzionalità di rinnovo automatico tramite il [Centro account di Azure](https://account.windowsazure.com/Home/Index).

Se non si vuole usare l'opzione di rinnovo automatico, accedere al [Centro account di Azure](https://account.windowsazure.com/Home/Index), selezionare il piano di supporto specifico, fare clic su "Modifica dettagli rinnovo" e deselezionare l'opzione predefinita di rinnovo automatico.

### <a name="how-do-i-cancel-my-azure-support-plan"></a>Come è possibile annullare il piano di supporto di Azure?
Il proprietario di un account può annullare un piano di supporto di Azure accedendo al [Centro account di Azure](https://account.windowsazure.com/Home/Index) e quindi selezionando **Sottoscrizioni**. Se si ha una sottoscrizione, verrà visualizzato l'elenco delle sottoscrizioni. Selezionare la sottoscrizione che si vuole annullare. I dettagli della sottoscrizione verranno quindi visualizzati con un elenco di opzioni a destra dello schermo. Selezionare **Annulla** e continuare. Se non si ha una sottoscrizione, verrà visualizzato il messaggio "L'utente non dispone di sottoscrizioni".

> [!NOTE]
> Un piano di supporto di Azure prevede un periodo di validità di 6 mesi e Microsoft richiede un impegno monetario continuo per tutta la durata di tale periodo. L'annullamento prima della scadenza del periodo di validità non esonera dal pagamento dell'impegno rimanente né dà diritto al rimborso di un eventuale importo prepagato.
> Per informazioni dettagliate, vedere [Come annullare la sottoscrizione di Azure](billing-how-to-cancel-azure-subscription.md).
>
>

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-a-new-subscription"></a>Come si esegue la migrazione di dati e servizi per la sottoscrizione di Azure a una nuova sottoscrizione?
Per altre informazioni, contattare il [Supporto tecnico di Azure](https://azure.microsoft.com/support/options/) . Per creare un ticket di supporto per la migrazione di dati e servizi da una sottoscrizione di Azure a una nuova sottoscrizione, vedere [Come creare un ticket di supporto per problemi relativi alla sottoscrizione e alla fatturazione di Azure](billing-how-to-create-billing-support-ticket.md).

### <a name="how-do-i-manage-administrator-accounts-in-the-new-azure-portal"></a>Come si gestiscono gli account amministratore nel nuovo portale di Azure?
Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. Usando il Controllo degli accessi in base al ruolo di Azure, è possibile separare compiti all'interno del team DevOps e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il proprio lavoro. Per altre informazioni, vedere [Controllo di accesso in base al ruolo Azure](active-directory/role-based-access-control-configure.md).

### <a name="how-do-i-transfer-ownership-of-my-subscriptions"></a>Come si trasferisce la proprietà delle sottoscrizioni?
È ora possibile eseguire facilmente questa operazione nel Centro account di Microsoft Azure per le offerte con pagamento in base al consumo o con credito mensile per i sottoscrittori di Visual Studio o per le sottoscrizioni di Microsoft Partner Network o di BizSpark. È stata aggiunta la possibilità di trasferire la sottoscrizione a un altro utente. In altre parole, è ora possibile modificare l'amministratore dell'account per qualsiasi offerta con pagamento in base al consumo o con credito mensile per i sottoscrittori di Visual Studio o per le sottoscrizioni di Microsoft Partner Network o di BizSpark. di cui si è proprietari. Si noti che l'account utente del destinatario deve essere nello stesso paese e che non è possibile trasferire le sottoscrizioni associate a un acquisto sul Marketplace.
Per informazioni dettagliate, vedere [Trasferimento di proprietà di una sottoscrizione di Azure](billing-subscription-transfer.md).

### <a name="can-i-use-my-existing-windows-licenses-that-i-bought-outside-of-azure"></a>È possibile usare licenze di Windows esistenti acquistate all'esterno di Azure?
Sì. Grazie alla mobilità delle licenze, se si ha una Software Assurance, è possibile sfruttare la funzionalità Bring Your Own License per tutti i prodotti server supportati dalle macchine virtuali, ad eccezione di Windows Server. Per altre informazioni, vedere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/).

### <a name="where-can-i-find-the-benefits-and-pricing-information-for-azure-services"></a>Dove si possono trovare informazioni su vantaggi e prezzi per i servizi di Azure?
Per informazioni sui vantaggi dei servizi di Azure, vedere [dettagli dell'offerta di Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). Per informazioni sui prezzi vedere la [Pagina dei prezzi di Azure](https://azure.microsoft.com/pricing/).

### <a name="how-do-i-change-my-pricing-plan"></a>Come si modifica il piano tariffario?
È possibile passare dalla sottoscrizione con pagamento in base al consumo all' [Offerta prepagata di 12 mesi](https://azure.microsoft.com/offers/ms-azr-0026p/). Con questa offerta si pagano in anticipo i servizi di Azure per un periodo di 12 mesi, ricevendo uno sconto del 5%. Nel [Centro account di Azure](https://account.windowsazure.com/Subscriptions)accedere alla propria sottoscrizione e fare clic su Cambia offerta. Questa è la sola offerta che si può cambiare nel Centro Account. In alternativa, contattare il [Supporto tecnico di Azure](https://azure.microsoft.com/support/options/) per passare a un'offerta diversa.

### <a name="can-notifications-be-sent-to-a-different-email-address-other-than-the-account-owner-email-address-associated-with-my-account"></a>È possibile inviare le notifiche a un indirizzo di posta elettronica da quello del proprietario dell'account associato all'account?
Sì. Per specificare un indirizzo di posta elettronica diverso a cui ricevere le notifiche, seguire questa procedura:

1. Accedere alla scheda [Profilo](https://account.windowsazure.com/Profile) nel portale degli account Azure.
2. Fare clic su **Modifica dettagli** per aggiornare l'indirizzo di posta elettronica per ricevere notifiche.

### <a name="how-can-i-edit-my-payment-information-for-my-azure-subscriptions"></a>Come si modificano le informazioni sul pagamento per le sottoscrizioni di Azure?
Per visualizzare e modificare le informazioni dell'account Azure, è necessario accedere al Centro account di Azure come amministratore account. Di seguito sono riportate le istruzioni per la gestione del metodo di pagamento per una o più sottoscrizioni di Azure.**

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/Subscriptions).
2. Nella pagina delle sottoscrizioni fare clic sulla sottoscrizione per cui si vuole aggiornare il metodo di pagamento.
3. Nella pagina di riepilogo della **sottoscrizione** fare clic su Modifica il metodo di pagamento. In una finestra separata viene visualizzato lo strumento **Modifica il metodo di pagamento** .

   **Nota:**si può accedere al Centro account anche dal portale di Microsoft Azure classico. A questo scopo, fare clic sul nome dell'account e quindi su Visualizza fattura.
4. Nella pagina **Scegli il metodo di pagamento** fare clic sull'elenco a discesa, selezionare il metodo di pagamento che si vuole aggiornare e quindi fare clic su **Modifica**.
5. Nella pagina dei dettagli verificare di aver selezionato il tipo e il numero di carta di credito corretti.
6. Apportare le modifiche necessarie ai dettagli della carta e quindi fare clic su **Avanti**.

Per altre informazioni, vedere l'articolo [Come modificare la carta di credito usata per pagare una sottoscrizione di Azure](billing-how-to-change-credit-card.md).

> [!NOTE]
> Attualmente, lo strumento Modifica il metodo di pagamento non consente di rimuovere una carta di credito esistente come metodo di pagamento. Per informazioni su come rimuovere una carta di credito, vedere la domanda [Come si rimuove una carta di credito che non si usa più come metodo di pagamento di Azure?](#how-do-i-remove-a-credit-card-that-i-no-longer-use-as-an-azure-payment-method) in questo articolo.
>
>

### <a name="why-am-i-seeing-a-message-that-i-need-to-use-an-international-credit-card"></a>Perché viene visualizzato un messaggio indicante che è necessario usare una carta di credito internazionale?
In alcuni casi, anche se si sta effettuando il pagamento per i servizi Azure nella valuta locale/preferita, l'acquisto avviene tramite un'entità Microsoft non locale e la carta di credito viene elaborata da una banca non locale. In questi casi, le carte rilasciate da banche locali potrebbero generare un errore e compromettere i servizi di Azure. Per verificare se la carta di credito è abilitata per le transazioni internazionali, contattare l'autorità emittente della carta o la banca. Vedere l' [elenco relativo ai paesi e alle valute supportati](billing-countries-and-currencies.md).

### <a name="why-cant-i-edit-or-add-details-to-my-subscription"></a>Perché non è possibile modificare o aggiungere dettagli alla sottoscrizione?
Per visualizzare e modificare le informazioni dell'account Azure, è necessario accedere al Centro account di Azure come amministratore account.  Se si è l'amministratore account, ma non è comunque possibile modificare la sottoscrizione, inviare un ticket di supporto per richiedere assistenza. Per creare un ticket di supporto, vedere [Come creare un ticket di supporto per problemi relativi alla sottoscrizione e alla fatturazione di Azure](billing-how-to-create-billing-support-ticket.md).

### <a name="who-can-purchase-azure-services"></a>Chi può acquistare i servizi di Azure?
Azure è una piattaforma destinata alle aziende, che possono usarla per creare applicazioni e servizi di livello cloud. Tuttavia, tutti gli utenti possono acquistare i servizi di Azure.

### <a name="can-i-try-azure-for-free-without-any-risk-of-being-charged"></a>Si può provare gratuitamente Azure senza il rischio di incorrere in addebiti?
Sì. La [funzionalità di limite di spesa](https://azure.microsoft.com/pricing/spending-limits/) consente ai clienti che si iscrivono per un'offerta per una versione di valutazione gratuita, con credito mensile per i sottoscrittori di Visual Studio, per Microsoft Partner Network o per BizSpark di usare Azure senza incorrere in addebiti fino a quando mantengono attivata la funzionalità di limite di spesa. Tutti i nuovi clienti che effettuano l'iscrizione a queste offerte avranno attivata la funzionalità Limite di spesa con un valore pari a $ 0.

### <a name="can-i-turn-the-spending-limit-back-on-if-i-turn-off-it"></a>Si può riattivare il limite di spesa dopo che è stato disattivato?
I clienti che hanno diritto alle offerte riservate ai membri, ad esempio il credito mensile per sottoscrittori di Visual Studio, possono richiedere la riabilitazione della funzionalità di limite di spesa all'inizio del successivo ciclo di fatturazione. Se il limite di spesa è stato disattivato, non può essere riabilitato per il periodo di fatturazione corrente.
Per altre informazioni, vedere [Modificare il limite di spesa di Azure](https://msdn.microsoft.com/library/azure/dn465781.aspx).

### <a name="can-i-adjust-the-amount-of-the-spending-limit"></a>Si può modificare l'importo del limite di spesa?
Per il rilascio iniziale di questa funzionalità, il limite di spesa è fissato a 0 USD e non può essere modificato. Tale limite è pensato per consentire ai clienti con versione di valutazione gratuita o con credito mensile per i sottoscrittori di Visual Studio, con offerte Microsoft Partner Network o BizSpark di usare Azure con la garanzia completa di non incorrere in addebiti.
Per altre informazioni, vedere [Limite di spesa di Azure](https://azure.microsoft.com/pricing/spending-limits/).

### <a name="in-which-countries-and-regions-is-microsoft-azure-commercially-available-and-what-currencies-can-be-used-to-purchase-azure"></a>In quali paesi e aree geografiche Microsoft Azure è attualmente disponibile in commercio e quali valute possono essere usate per acquistare Azure?
Azure è disponibile per l'acquisto in più di 140 paesi in tutto il mondo e supporta la fatturazione in molte valute. Fare clic [qui](billing-countries-and-currencies.md) per visualizzare un elenco di paesi e valute.

### <a name="do-we-restrict-resale-of-azure-based-service-into-countries-under-embargo"></a>La rivendita dei servizi basati su Azure è limitata nei paesi soggetti a embargo?
Sì.

### <a name="are-azure-and-sql-database-available-through-microsoft-services-provider-license-agreement"></a>Azure e il database SQL sono disponibili tramite contratto di licenza del provider di Servizi Microsoft?
Attualmente non si prevede di offrire Azure o il database SQL tramite SPLA.

### <a name="what-is-a-free-trial-subscription-and-how-long-does-it-last"></a>Che cos'è una sottoscrizione della versione di valutazione e per quanto tempo è valida?
Una sottoscrizione di valutazione gratuita è una versione valida un mese per tutti i nuovi clienti e include crediti di Azure pari a USD 200 senza costi aggiuntivi. Per visualizzare i servizi coperti o meno dalla versione di valutazione gratuita, vedere la pagina [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

### <a name="what-resources-are-available-when-using-a-free-trial"></a>Quali risorse sono disponibili quando si usa una versione di valutazione?
Con la versione di valutazione gratuita si ricevono USD 200 in crediti di Azure da usare in 30 giorni. La modalità d'uso dei crediti di Azure è a scelta dell'utente. Per altre informazioni, vedere la pagina [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) .

È possibile iscriversi [qui](http://azure.microsoft.com/pricing/free-trial/)per ottenere una sottoscrizione di valutazione.

Vedere anche [Domande frequenti sulla versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial-faq/).

### <a name="what-happens-if-i-remove-the-spending-limit-on-my-free-trial"></a>Cosa accade se si rimuove il limite di spesa dalla versione di valutazione gratuita?
Dopo aver rimosso il limite di spesa, viene addebitato l'utilizzo delle risorse che supera il credito della versione di valutazione gratuita. Se il limite di spesa viene rimosso, non è più possibile reimpostarlo.

### <a name="can-i-get-a-quota-increase-on-a-free-trial-subscription"></a>È possibile ottenere un aumento della quota in una sottoscrizione di valutazione gratuita?
Le sottoscrizioni di valutazione gratuita non sono idonee per aumenti della quota. Se è disponibile una versione di valutazione gratuita, è possibile eseguire l'aggiornamento a una sottoscrizione con [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) . Per informazioni dettagliate su come eseguire l'aggiornamento, vedere [Aggiornamento della versione di valutazione gratuita a Pagamento in base al consumo](billing-upgrade-azure-subscription.md).

> [!NOTE]
> Le sottoscrizioni Microsoft Imagine non sono idonee per aumenti di quota.
>
>

### <a name="where-can-i-get-more-details-on-free-trial"></a>Dove è possibile ottenere altre informazioni sulla versione di valutazione gratuita?
Visitare la pagina [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) e vedere le [Domande frequenti sulla versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial-faq/).

> [!NOTE]
> Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
>
>

### <a name="how-do-i-make-a-payment"></a>Come si effettua un pagamento?
Se il metodo di pagamento è impostato su carta di credito o carta di debito, il pagamento verrà effettuato automaticamente.

Se è stata configurata una [modalità di pagamento con fattura](https://azure.microsoft.com/pricing/invoicing/), inviare il pagamento al destinatario indicato nella parte inferiore della fattura. Per altre informazioni, [contattare il supporto tecnico](billing-how-to-create-billing-support-ticket.md).



<!--HONumber=Dec16_HO2-->


