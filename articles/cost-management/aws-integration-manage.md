---
title: Gestire i costi e l'utilizzo di AWS in gestione costi di Azure
description: Questo articolo illustra come usare l'analisi dei costi e i budget in gestione costi per gestire i costi e l'utilizzo di AWS.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 772f6cdde575a9ac669c73ecca039914357ffe2f
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338889"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Gestire i costi e l'utilizzo di AWS in Azure

Dopo aver configurato e configurato l'integrazione dei report di utilizzo e costi di AWS per gestione costi di Azure, si è pronti per iniziare a gestire i costi e l'utilizzo di AWS. Questo articolo illustra come usare l'analisi dei costi e i budget in gestione costi per gestire i costi e l'utilizzo di AWS.

Se l'integrazione non è già stata configurata, vedere [configurare e configurare l'integrazione dei report sull'utilizzo di AWS](aws-integration-set-up-configure.md).

_Prima di iniziare_: Se non si ha familiarità con l'analisi dei costi, vedere la Guida introduttiva [esplorare e analizzare i costi con l'analisi dei](quick-acm-cost-analysis.md) costi. Se non si ha familiarità con i budget in Azure, vedere l'esercitazione [creare e gestire i budget di Azure](tutorial-acm-create-budgets.md) .

## <a name="view-aws-costs-in-cost-analysis"></a>Visualizzare i costi AWS nell'analisi dei costi

I costi di AWS sono disponibili nell'analisi dei costi negli ambiti seguenti:

- Account collegati AWS in un gruppo di gestione
- Costi dell'account collegato AWS
- Costi account consolidato AWS

Nelle sezioni successive viene descritto come utilizzare gli ambiti in modo da visualizzare i dati relativi a costi e utilizzo per ciascuno di essi.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Visualizzare gli account collegati AWS in un gruppo di gestione

La visualizzazione dei costi tramite l'ambito del gruppo di gestione è l'unico modo per visualizzare i costi aggregati provenienti da sottoscrizioni e account collegati diversi. L'uso di un gruppo di gestione fornisce una visualizzazione tra cloud.

In analisi dei costi aprire Selezione ambito e selezionare il gruppo di gestione che include gli account collegati AWS. Ecco un'immagine di esempio nell'portale di Azure:

![Esempio della visualizzazione seleziona ambito](./media/aws-integration-manage/select-scope01.png)



Di seguito è riportato un esempio in cui viene illustrato il costo dell'analisi dei costi del gruppo di gestione, raggruppato per provider (Azure e AWS).

![Esempio che mostra i costi di Azure e AWS per un trimestre nell'analisi dei costi](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Visualizza i costi dell'account collegato AWS

Per visualizzare i costi dell'account di collegamento AWS, aprire Selezione ambito e selezionare l'account collegato AWS. Si noti che gli account collegati sono associati a un gruppo di gestione, come definito nel connettore AWS.

Di seguito è riportato un esempio che illustra la selezione di un ambito dell'account collegato AWS.

![Esempio della visualizzazione seleziona ambito](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Visualizza i costi dell'account consolidato AWS

Per visualizzare i costi dell'account consolidato di AWS, aprire Selezione ambito e selezionare l'account consolidato AWS. Di seguito è riportato un esempio che illustra la selezione di un ambito dell'account consolidato AWS.

![Esempio della visualizzazione seleziona ambito](./media/aws-integration-manage/select-scope03.png)



Questo ambito fornisce una visualizzazione aggregata di tutti gli account collegati AWS associati all'account consolidato AWS. Ecco un esempio che mostra i costi per un account consolidato AWS, raggruppati in base al nome del servizio.

![Esempio che mostra i costi consolidati di AWS nell'analisi dei costi](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimensioni disponibili per il filtro e il raggruppamento

Nella tabella seguente vengono descritte le dimensioni disponibili per il raggruppamento e il filtro in base all'analisi dei costi.

| Dimensione | Intestazione di Amazon CUR | Ambiti | Commenti |
| --- | --- | --- | --- |
| Zona di disponibilità | LineItem/AvailabilityZone | Tutti |   |
| Location | prodotto/area geografica | Tutti |   |
| Misuratore |   | Tutti |   |
| Categoria del contatore | lineItem/ProductCode | Tutti |   |
| Sottocategoria di contatore | lineitem/UsageType | Tutti |   |
| Operazione | lineItem/operazione | Tutti |   |
| Resource | lineItem/ResourceId | Tutti |   |
| Tipo di risorsa | prodotto/instanceType | Tutti | Se Product/instanceType è null, viene utilizzato lineItem/UsageType. |
| ResourceGuid | N/D | Tutti | GUID del contatore di Azure. |
| Nome servizio | prodotto/ProductName | Tutti | Se Product/ProductName è null, viene usato lineItem/ProductCode. |
| Livello di servizio |   |   |   |
| ID sottoscrizione | lineItem/UsageAccountId | Account consolidato e gruppo di gestione |   |
| Nome sottoscrizione | N/D | Account consolidato e gruppo di gestione | I nomi degli account vengono raccolti usando l'API dell'organizzazione AWS. |
| Tag | resourceTags/\* | Tutti | Il prefisso _User:_ viene rimosso dai tag definiti dall'utente per consentire i tag tra cloud. Il prefisso _AWS:_ è rimasto intatto. |
| ID account di fatturazione | fattura/PayerAccountId | Gruppo di gestione |   |
| Nome dell'account di fatturazione | N/D | Gruppo di gestione | I nomi degli account vengono raccolti usando l'API dell'organizzazione AWS. |
| Provider | N/D | Gruppo di gestione | AWS o Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Impostare i budget negli ambiti di AWS

Usare i budget per gestire in modo proattivo i costi e gestire le responsabilità dell'organizzazione. I budget vengono impostati nell'account consolidato AWS e negli ambiti dell'account collegato AWS. Ecco un esempio di budget per un account consolidato AWS illustrato in gestione costi:

![Esempio che mostra i budget per un account consolidato AWS](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Processo di raccolta dati AWS

Dopo aver configurato il connettore AWS, vengono avviati i processi di raccolta e individuazione dei dati. La raccolta di tutti i dati di utilizzo potrebbe richiedere alcune ore. La durata dipende da:

- Tempo necessario per elaborare i file CUR presenti nel bucket di AWS s3.
- Tempo necessario per creare l'account consolidato AWS e gli ambiti degli account collegati AWS.
- Il tempo e la frequenza di AWS scrivono i file di report relativi ai costi e all'utilizzo nel bucket S3

## <a name="aws-integration-pricing"></a>Prezzi di integrazione di AWS

Ogni connettore AWS ottiene 90 giorni di valutazione gratuita. Durante l'anteprima pubblica non è previsto alcun addebito.

Il prezzo di listino è l'1% dei costi mensili di AWS. Ogni mese viene addebitato in base ai costi fatturati del mese precedente.

L'accesso alle API AWS può comportare costi aggiuntivi.

## <a name="aws-integration-limitations"></a>Limitazioni dell'integrazione di AWS

- Gestione costi non supporta i report sui costi che contengono più tipi di valuta. Un messaggio di errore viene visualizzato se si seleziona un ambito con più valute.
- I connettori cloud non supportano AWS GovCloud (US), AWS gov o AWS Cina.
- Gestione costi indica solo i _costi di utilizzo_ di AWS. Le tasse, il supporto, i rimborsi, il RI, i crediti o qualsiasi altro tipo di addebito non sono ancora supportati.

## <a name="troubleshooting-aws-integration"></a>Risoluzione dei problemi di integrazione di AWS

Per risolvere i problemi comuni, utilizzare le seguenti informazioni sulla risoluzione dei problemi.

### <a name="no-permission-to-aws-linked-accounts"></a>Nessuna autorizzazione per gli account collegati AWS

**Codice errore:** _Autorizzato_

Esistono due modi per ottenere le autorizzazioni per accedere ai costi degli account collegati AWS:

- Ottenere l'accesso al gruppo di gestione con gli account collegati AWS.
- Chiedere a un utente di concedere l'autorizzazione all'account collegato di AWS.

Per impostazione predefinita, AWS Connector Creator è il proprietario di tutti gli oggetti creati dal connettore. Inclusi l'account consolidato AWS e l'account collegato AWS.

Per essere in grado di verificare le impostazioni del connettore è necessario almeno un ruolo Collaboratore, il lettore non è in grado di verificare le impostazioni del connettore

### <a name="collection-failed-with-assumerole"></a>Raccolta non riuscita con AssumeRole

**Codice errore:** _FailedToAssumeRole_

Questo errore indica che gestione costi non è in grado di chiamare l'API AssumeRole di AWS. Questo problema può verificarsi a causa di un problema con la definizione del ruolo. Verificare che siano soddisfatte le condizioni seguenti:

- L'ID esterno corrisponde a quello della definizione del ruolo e della definizione del connettore.
- Il tipo di ruolo è impostato su **un altro account AWS appartenente all'utente o a terze parti.**
- L'opzione **Richiedi** autenticazione a più fattori è deselezionata.
- L'account AWS attendibile nel ruolo AWS è _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>Raccolta non riuscita con accesso negato-definizioni del report CUR

**Codice errore:** _AccessDeniedReportDefinitions_ 

Questo errore indica che gestione costi non è in grado di visualizzare le definizioni dei report relativi ai costi e all'utilizzo. Questa autorizzazione viene usata per verificare che il CUR sia definito come previsto da gestione costi di Azure. Vedere [creare un report sui costi e sull'utilizzo in AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>Raccolta non riuscita con accesso negato-elencare i report

**Codice errore:** _AccessDeniedListReports_ 

Questo errore indica che gestione costi non è in grado di elencare l'oggetto nel bucket S3 in cui si trova il CUR. Il criterio IAM di AWS richiede un'autorizzazione sul bucket e sugli oggetti nel bucket. Vedere [creare un ruolo e un criterio in AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>Raccolta non riuscita con accesso negato-download report 

**Codice errore:** _AccessDeniedDownloadReport_ 

Questo errore indica che gestione costi non è in grado di accedere e scaricare i file CUR archiviati nel bucket Amazon S3. Verificare che il criterio JSON di AWS associato al ruolo sia simile all'esempio illustrato nella parte inferiore della sezione [creare un ruolo e un criterio in AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) .

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>La raccolta non è riuscita perché non sono stati trovati report relativi a costi e utilizzo

**Codice errore:** _FailedToFindReport_

Questo errore indica che gestione costi non è in grado di trovare il report di costo e utilizzo definito nel connettore. Assicurarsi che non sia stato eliminato e che il criterio JSON di AWS associato al ruolo sia simile all'esempio illustrato nella parte inferiore della sezione [creare un ruolo e un criterio in AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) .

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Non è possibile creare o verificare il connettore a causa di mancata corrispondenza delle definizioni dei report relativi a costi e utilizzo

**Codice errore:** _ReportIsNotValid_

Questo errore si riferisce alla definizione del report sul costo e sull'utilizzo di AWS. per questo report sono necessarie impostazioni specifiche. vedere i requisiti in [creare un report sui costi e sull'utilizzo in AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws) .

## <a name="next-steps"></a>Passaggi successivi

- Se l'ambiente Azure non è già stato configurato con i gruppi di gestione, vedere [configurazione iniziale dei gruppi di gestione](../governance/management-groups/overview.md#initial-setup-of-management-groups).
