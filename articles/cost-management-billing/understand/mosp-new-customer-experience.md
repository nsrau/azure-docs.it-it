---
title: Introduzione all'account di fatturazione di Azure aggiornato
description: Introduzione all'account di fatturazione di Azure aggiornato per illustrare le modifiche apportate alla nuova esperienza di fatturazione e gestione dei costi
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: banders
ms.openlocfilehash: 37b50e8761609f864d2cb16e7869614db64bac71
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87844916"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>Introduzione all'account di fatturazione di Azure aggiornato

La gestione dei costi e delle fatture è uno dei componenti principali dell'esperienza cloud. Consente di controllare e conoscere la spesa per il cloud. Per semplificare la gestione dei costi e delle fatture, Microsoft sta aggiornando l'account di fatturazione di Azure in modo da includere funzionalità migliorate di fatturazione e gestione dei costi. Questo articolo descrive gli aggiornamenti dell'account di fatturazione e illustra le nuove funzionalità.

> [!IMPORTANT]
> L'account verrà aggiornato al ricevimento di un messaggio di posta elettronica da Microsoft che notifica gli aggiornamenti apportati all'account. La notifica viene inviata 60 giorni prima dell'aggiornamento dell'account.

## <a name="more-flexibility-with-your-new-billing-account"></a>Maggiore flessibilità con il nuovo account di fatturazione

Il diagramma seguente confronta l'account di fatturazione precedente e quello nuovo:

![Diagramma che illustra il confronto tra la gerarchia di fatturazione nell'account precedente e in quello nuovo](./media/mosp-new-customer-experience/comparison-old-new-account.png)

Il nuovo account di fatturazione contiene uno o più profili di fatturazione che consentono di gestire le fatture e i metodi di pagamento. Ogni profilo di fatturazione contiene una o più sezioni della fattura che consentono di organizzare i costi per la fattura del profilo di fatturazione.

![Diagramma che illustra la nuova gerarchia di fatturazione](./media/mosp-new-customer-experience/new-billing-account-hierarchy.png)

I ruoli nell'account di fatturazione hanno il livello di autorizzazioni più elevato. Questi ruoli dovranno essere assegnati agli utenti che devono visualizzare le fatture e tenere traccia dei costi per l'intero account, ad esempio ai responsabili finanziari o IT di un'organizzazione o alla persona che ha effettuato l'iscrizione per un account. Per altre informazioni, vedere [Ruoli e attività dell'account di fatturazione](../manage/understand-mca-roles.md#billing-account-roles-and-tasks). Quando l'account viene aggiornato, all'utente con ruolo di amministratore account nell'account di fatturazione precedente viene assegnato un ruolo di proprietario per il nuovo account.

## <a name="billing-profiles"></a>Profili di fatturazione

Un profilo di fatturazione consente di gestire la fattura e i metodi di pagamento. Viene generata una fattura mensile all'inizio del mese per ogni profilo di fatturazione incluso nell'account. La fattura contiene i rispettivi addebiti relativi al mese precedente per tutte le sottoscrizioni associate al profilo di fatturazione.

Quando l'account viene aggiornato, viene creato automaticamente un profilo di fatturazione per ogni sottoscrizione. Gli addebiti della sottoscrizione vengono fatturati al rispettivo profilo di fatturazione e visualizzati nella relativa fattura.

Ai ruoli nei profili di fatturazione sono assegnate le autorizzazioni per visualizzare e gestire fatture e metodi di pagamento. Questi ruoli dovranno essere assegnati agli utenti che pagano le fatture, ad esempio ai membri del team di contabilità di un'organizzazione. Per altre informazioni, vedere [Ruoli e attività del profilo di fatturazione](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks). 

Quando l'account viene aggiornato, per ogni sottoscrizione per cui si è concessa ad altri l'autorizzazione per la [visualizzazione delle fatture](download-azure-invoice.md#allow-others-to-download-the-your-subscription-invoice) viene assegnato il ruolo di lettore per il rispettivo profilo di fatturazione agli utenti con ruolo Proprietario, Collaboratore, Lettore o Fatturazione per lettore del controllo degli accessi in base al ruolo di Azure.

## <a name="invoice-sections"></a>Sezioni della fattura

Una sezione della fattura consente di organizzare i costi nella fattura. Ad esempio, potrebbe essere necessaria una singola fattura pur volendo organizzare i costi per reparto, team o progetto. Questo scenario prevede un singolo profilo di fatturazione in cui viene creata una sezione della fattura per ogni reparto, team o progetto.

Quando l'account viene aggiornato, viene creata una sezione della fattura per ogni profilo di fatturazione e la sottoscrizione correlata viene assegnata alla sezione della fattura. Quando si aggiungono altre sottoscrizioni, è possibile creare sezioni aggiuntive e assegnare le sottoscrizioni alle sezioni della fattura. Le sezioni verranno visualizzate nella fattura del profilo di fatturazione e rispecchieranno l'utilizzo di ogni sottoscrizione assegnata.

I ruoli nella sezione della fattura dispongono delle autorizzazioni per controllare chi crea le sottoscrizioni di Azure. I ruoli dovranno essere assegnati agli utenti che configurano l'ambiente Azure per i team di un'organizzazione, ad esempio a responsabili e architetti tecnici. Per altre informazioni, vedere [Ruoli e attività della sezione della fattura](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="enhanced-features-in-your-new-experience"></a>Funzionalità migliorate nella nuova esperienza

La nuova esperienza consente di gestire facilmente i costi e le fatture grazie alle funzionalità di fatturazione e gestione dei costi seguenti.

#### <a name="invoice-management"></a>Gestione delle fatture

**Periodo di fatturazione mensile più prevedibile**: nel nuovo account, il periodo di fatturazione inizia il primo giorno del mese e termina l'ultimo giorno del mese, indipendentemente da quando viene effettuata l'iscrizione per Azure. All'inizio di ogni mese verrà generata una fattura che conterrà tutti gli addebiti del mese precedente.

**Ricevere una singola fattura mensile per più sottoscrizioni**: è possibile ricevere una fattura mensile per ogni sottoscrizione oppure una singola fattura per più sottoscrizioni.

**Ricevere una singola fattura mensile per le sottoscrizioni di Azure, i piani di supporto e i prodotti di Azure Marketplace**: si riceverà un'unica fattura mensile per tutti gli addebiti, che includerà gli addebiti per l'utilizzo delle sottoscrizioni di Azure, i piani di supporto e gli acquisti in Azure Marketplace.

**Raggruppare i costi nella fattura in base alle esigenze**: è possibile raggruppare i costi nella fattura in base alle esigenze, per reparti, progetti o team.

**Impostare un numero di ordine d'acquisto facoltativo per la fattura**: per associare la fattura ai sistemi finanziari interni, impostare un numero di ordine d'acquisto. Potrà essere gestito e aggiornato in qualsiasi momento nel portale di Azure.

#### <a name="payment-management"></a>Gestione dei pagamenti

**Pagare le fatture immediatamente con una carta di credito**: non è necessario attendere l'addebito del pagamento automatico sulla carta di credito. È possibile usare qualsiasi carta di credito per pagare una fattura in scadenza o scaduta nel portale di Azure.

**Tenere traccia di tutti i pagamenti applicati all'account**: visualizzare tutti i pagamenti applicati all'account, con il metodo di pagamento usato, l'importo pagato e la data di pagamento, nel portale di Azure.

#### <a name="cost-management"></a>Gestione dei costi

**Pianificare esportazioni mensili dei dati di utilizzo in un account di archiviazione**: è possibile pubblicare automaticamente i dati relativi ai costi e all'utilizzo in un account di archiviazione su base giornaliera, settimanale o mensile.

#### <a name="account-and-subscription-management"></a>Gestione dell'account e delle sottoscrizioni

**Assegnare più amministratori per le operazioni di fatturazione**: assegnare le autorizzazioni per la fatturazione a più utenti che gestiranno la fatturazione per l'account. Per una maggiore flessibilità, concedere ad altri autorizzazioni di lettura, di scrittura o sia di lettura che di scrittura.

**Creare sottoscrizioni aggiuntive direttamente nel portale di Azure**: creare tutte le sottoscrizioni con un solo clic nel portale di Azure.

#### <a name="api-support"></a>supporto API

**Eseguire operazioni di fatturazione e di gestione dei costi tramite API, SDK e PowerShell**: usare le API di gestione dei costi, fatturazione e utilizzo per eseguire il pull dei dati relativi ai costi e alla fatturazione negli strumenti di analisi dei dati preferiti.

**Eseguire tutte le operazioni relative alle sottoscrizioni tramite API, SDK e PowerShell**: usare le API per le sottoscrizioni di Azure per automatizzare la gestione delle sottoscrizioni di Azure, ad esempio la creazione, la ridenominazione e l'annullamento di una sottoscrizione.

## <a name="get-prepared-for-your-new-experience"></a>Preparazione per la nuova esperienza

Di seguito sono riportate le operazioni consigliate per prepararsi per la nuova esperienza.

**Periodo di fatturazione mensile e data di fatturazione diversa**

Nella nuova esperienza, la fattura verrà generata intorno al nono giorno del mese e conterrà tutti gli addebiti del mese precedente. Questa data potrebbe differire dalla data di generazione della fattura nell'account precedente. Se si condividono le fatture con altri, notificare il cambiamento della data.

**Nuove API di fatturazione e gestione dei costi**

Se si usano API di fatturazione o gestione dei costi per eseguire query e aggiornare i dati relativi alla fatturazione o ai costi, è necessario usare le nuove API. La tabella seguente riporta l'elenco delle API che non funzioneranno con il nuovo account di fatturazione e le modifiche da apportare nel nuovo account di fatturazione.

|API | Modifiche  |
|---------|---------|
|[Account di fatturazione - Elenco](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/billingaccounts/list) | Nell'API di elenco degli account di fatturazione il valore di agreementType per l'account di fatturazione precedente è **MicrosoftOnlineServiceProgram**, mentre per il nuovo account di fatturazione il valore di agreementType sarà **MicrosoftCustomerAgreement**. Se è presente una dipendenza da agreementType, aggiornarne il valore. |
|[Fatture - Elenco per sottoscrizione di fatturazione](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Questa API restituirà solo le fatture generate prima dell'aggiornamento dell'account. Per ottenere le fatture generate nel nuovo account di fatturazione, sarà necessario usare l'API illustrata in [Fatture - Elenco per account di fatturazione](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount). |

## <a name="additional-information"></a>Informazioni aggiuntive

Altre informazioni sulla nuova esperienza sono disponibili nelle sezioni seguenti.

**Nessun tempo di inattività dei servizi**. L'esecuzione dei servizi di Azure nella sottoscrizione continuerà senza interruzioni. Verrà aggiornata soltanto l'esperienza di fatturazione, senza alcun impatto sulle risorse, sui gruppi di risorse o sui gruppi di gestione esistenti.

**Nessuna modifica alle risorse di Azure**. L'aggiornamento non influisce sull'accesso alle risorse di Azure impostate con il controllo degli accessi in base al ruolo di Azure.

**Disponibilità delle fatture precedenti nella nuova esperienza**. Le fatture generate prima dell'aggiornamento dell'account saranno ancora disponibili nel portale di Azure.

**Fatture per un account aggiornato a metà mese**. Se l'account viene aggiornato a metà mese, si riceverà una fattura per gli addebiti accumulati fino al giorno dell'aggiornamento dell'account. Per il resto del mese verrà emessa un'altra fattura. Se un account con una sottoscrizione viene aggiornato il 15 settembre, ad esempio, si riceverà una fattura per gli addebiti accumulati fino al 15 settembre. Per il periodo compreso tra il 15 e il 30 settembre verrà emessa un'altra fattura. Dopo settembre, si riceverà un'unica fattura al mese.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'account di fatturazione, vedere gli articoli seguenti:

- [Informazioni sui ruoli amministrativi per il nuovo account di fatturazione](../manage/understand-mca-roles.md)
- [Creare una sottoscrizione di Azure aggiuntiva per il nuovo account di fatturazione](../manage/create-subscription.md)
- [Creare sezioni nella fattura per organizzare i costi](../manage/mca-section-invoice.md)
