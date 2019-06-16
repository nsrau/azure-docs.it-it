---
title: Gestire i costi AWS e utilizzo in Gestione costi di Azure
description: Questo articolo aiuta a comprendere come usare analisi dei costi e budget in Gestione costi di gestire i costi di AWS e utilizzo.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 007b6c409dde248a4dde7a15fd16b543add234bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870313"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Gestire i costi AWS e utilizzo in Azure

Dopo aver impostato e configurata l'integrazione di report di utilizzo e costi AWS per gestione costi di Azure, si è pronti per iniziare a gestire i costi di AWS e utilizzo. Questo articolo aiuta a comprendere come usare analisi dei costi e budget in Gestione costi di gestire i costi di AWS e utilizzo.

Se è già stato configurato l'integrazione, vedere [Set up e configurare l'integrazione di report sull'utilizzo di AWS](aws-integration-set-up-configure.md).

_Prima di iniziare_: Se non si ha familiarità con analisi dei costi, vedere la [esplorare e analizzare i costi con l'analisi del costo](quick-acm-cost-analysis.md) Guida introduttiva. E, se non si ha familiarità con i budget di Azure, vedere la [crea e Gestisci i budget di Azure](tutorial-acm-create-budgets.md) esercitazione.

## <a name="view-aws-costs-in-cost-analysis"></a>Visualizzare i costi AWS in analisi dei costi

I costi AWS sono disponibili nell'analisi dei costi negli ambiti seguenti:

- Account AWS collegato in un gruppo di gestione
- Costi di account AWS collegato
- Costi di account AWS consolidati

Le sezioni successive descrivono come usare gli ambiti che consente di visualizzare dati di utilizzo e costi per ognuno di essi.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Visualizzare gli account AWS collegato in un gruppo di gestione

Visualizzando i costi usando l'ambito del gruppo di gestione è l'unico modo per visualizzare i costi aggregati provenienti da diverse sottoscrizioni e account collegati. Usando un gruppo di gestione offre una visualizzazione tra cloud.

Nell'analisi dei costi, aprire il selettore di ambito e selezionare il gruppo di gestione che contiene gli account AWS collegato. Ecco un esempio di immagine nel portale di Azure:

![Esempio della visualizzazione selezionare ambito](./media/aws-integration-manage/select-scope01.png)



Ecco un esempio che mostra il costo di gruppo di gestione di analisi dei costi, raggruppati dal Provider (Azure e AWS).

![Esempio che mostra i costi di Azure e AWS per analisi dei costi di un trimestre in](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Visualizzare i costi di account AWS collegato

Per visualizzare i costi di account AWS collegamento, aprire il selettore di ambito e selezionare il AWS collegato l'account. Si noti che gli account collegati associati a un gruppo di gestione, come definito nel connettore di AWS.

Di seguito è riportato un esempio che mostra la selezione di AWS collegata ambito dell'account.

![Esempio della visualizzazione selezionare ambito](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Vista AWS consolidati i costi di account

Per visualizzare AWS account consolidato dell'account consolidati i costi, aprire il selettore di ambito e scegliere di AWS. Di seguito è riportato un esempio che mostra la selezione di AWS consolidati ambito dell'account.

![Esempio della visualizzazione selezionare ambito](./media/aws-integration-manage/select-scope03.png)



In questo ambito fornisce che una visualizzazione aggregata di tutte le AWS account associato all'account AWS consolidati collegati. Ecco un esempio che mostra i costi per AWS consolidati account, raggruppati per nome del servizio.

![Esempio che illustra AWS consolidati i costi di analisi dei costi](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Le dimensioni disponibili per il filtro e raggruppamento

La tabella seguente descrive le dimensioni disponibili per raggruppare e filtrare nell'analisi dei costi.

| Dimensione | Intestazione CUR Amazon | Ambiti | Commenti |
| --- | --- | --- | --- |
| Zona di disponibilità | lineitem/AvailabilityZone | Tutti |   |
| Località | prodotto/area geografica | Tutti |   |
| Contatore |   | Tutti |   |
| Categoria del contatore | lineItem/ProductCode | Tutti |   |
| Sottocategoria del contatore | lineitem/UsageType | Tutti |   |
| Operazione | lineItem/Operation | Tutti |   |
| Resource | lineItem/ResourceId | Tutti |   |
| Tipo di risorsa | product/instanceType | Tutti | Se product/instanceType è null, viene usato lineItem/UsageType. |
| ResourceGuid | N/D | Tutti | Contatori di Azure GUID. |
| Nome del servizio | product/ProductName | Tutti | Se product/ProductName è null, viene usato lineItem/ProductCode. |
| Livello di servizio |   |   |   |
| ID sottoscrizione | lineItem/UsageAccountId | Gruppo di gestione e account consolidato |   |
| Nome della sottoscrizione | N/D | Gruppo di gestione e account consolidato | I nomi degli account vengono raccolte usando l'API dell'organizzazione di AWS. |
| Tag | resourceTags/\* | Tutti | Il _utente:_ prefisso viene rimosso dai tag definiti dall'utente per consentire i tag tra cloud. Il _aws:_ prefisso rimanga invariato. |
| ID dell'account di fatturazione | bill/PayerAccountId | Gruppo di gestione |   |
| Nome dell'account di fatturazione | N/D | Gruppo di gestione | I nomi degli account vengono raccolte usando l'API dell'organizzazione di AWS. |
| Provider | N/D | Gruppo di gestione | AWS o Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Set di budget sugli ambiti di AWS

Usare i budget per gestire in modo proattivo i costi e ottimizzare responsabilità all'interno dell'organizzazione. Budget vengono impostati sull'account AWS consolidati e AWS account collegato ambiti. Di seguito è riportato un esempio di budget per un account AWS consolidati visualizzati in Gestione costi:

![Esempio che mostra i budget per AWS consolidati account](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Processo di raccolta dati AWS

Dopo aver configurato il connettore AWS, avviare la raccolta dei dati e processi di individuazione. Potrebbe richiedere alcune ore per raccogliere tutti i dati di utilizzo. La durata è dipende da:

- Il tempo necessario per elaborare i file CUR nel bucket di AWS S3.
- Il tempo necessario per creare l'account di AWS consolidati e ambiti dell'account AWS collegato.
- Il tempo e frequenza di AWS scrive i file di Report di utilizzo e costi nel bucket di S3

## <a name="aws-integration-pricing"></a>Prezzi di integrazione di AWS

Ogni connettore AWS Ottiene 90 giorni di valutazione gratuite. Durante l'anteprima pubblica, non sono previsti addebiti.

Il prezzo di listino è l'1% dei costi mensili di AWS. Ogni mese viene addebitato in base i costi fatturati dal basato sul mese precedente.

L'accesso alle API di AWS può comportare costi aggiuntivi.

## <a name="aws-integration-limitations"></a>Limitazioni di integrazione di AWS

- Gestione dei costi non supporta i report di costo che contengono più tipi di valuta. Viene visualizzato un messaggio di errore se si seleziona un ambito con più valute.
- I connettori di cloud non supportano AWS GovCloud (Stati Uniti), per enti pubblici AWS o Cina di AWS.
- Gestione dei costi Mostra AWS _i costi di utilizzo_ solo. Imposte, supporto rimborsi, un'istanza riservata, crediti o addebitati altri costi per tipi non sono ancora supportati.

## <a name="troubleshooting-aws-integration"></a>Risoluzione dei problemi di integrazione di AWS

Usare le informazioni sulla risoluzione dei problemi seguenti per risolvere i problemi comuni.

### <a name="no-permission-to-aws-linked-accounts"></a>Nessuna autorizzazione per gli account collegati AWS

Esistono due modi per ottenere le autorizzazioni per accedere ai costi AWS collegati gli account:

- Ottieni l'accesso al gruppo di gestione che contenga gli account AWS collegato.
- Fare in modo di concedere l'autorizzazione per l'account AWS collegato.

Per impostazione predefinita, l'autore di connettore AWS è il proprietario di tutti gli oggetti che ha creato il connettore. Tra cui, di AWS account consolidati e AWS collegato l'account.

### <a name="collection-failed-with-assumerole"></a>Raccolta non è riuscita con AssumeRole

Questo errore indica che Gestione costi è in grado di chiamare l'API di AssumeRole AWS. Questo problema può verificarsi a causa di un problema con la definizione di ruolo. Verificare che le condizioni seguenti sono vere:

- L'ID esterno è identico a quello nella definizione del ruolo e la definizione del connettore.
- Il tipo di ruolo è impostato su **AWS un altro account appartenente all'utente o a 3rd party.**
- Il **richiedere il MFA** scelta è deselezionata.
- È l'account AWS attendibile nel ruolo AWS _432263259397_.

### <a name="collection-failed-with-access-denied"></a>Raccolta non è riuscita con accesso negato

Questo messaggio di errore indica che Gestione costi è in grado di accedere ai file CUR archiviati nel bucket Amazon S3. Assicurarsi che i criteri di AWS JSON collegati al ruolo è simile all'esempio illustrato nella parte inferiore della [creare un ruolo e i criteri in AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) sezione.

### <a name="connector-error-with-failedtofindreport"></a>Errore del connettore con FailedToFindReport

Questo errore indica che Gestione costi di impossibilità di trovare il report di utilizzo e costi che è stato definito in connector. Assicurarsi che non viene eliminato e che i criteri di AWS JSON collegati al ruolo è simile all'esempio illustrato nella parte inferiore della [creare un ruolo e i criteri in AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) sezione.

## <a name="next-steps"></a>Passaggi successivi

- Se è già stato configurato l'ambiente di Azure con gruppi di gestione, vedere [configurazione dei gruppi di gestione iniziale](../governance/management-groups/index.md#initial-setup-of-management-groups).
